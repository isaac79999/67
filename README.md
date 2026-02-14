-- [[ SS HUB - TREATMENT TURBO-TRANSFER (GOD SPEED) ]] --

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local lp = Players.LocalPlayer

-- Localização rápida (Cache local para velocidade micro)
local mercadinmote = ReplicatedStorage:WaitForChild("RemoteNovos"):WaitForChild("mercadinmote")
local invRequest = ReplicatedStorage:WaitForChild("Modules"):WaitForChild("InvRemotes"):WaitForChild("InvRequest")

-- [[ UI GETHUI ]] --
local ScreenGui = Instance.new("ScreenGui", (gethui and gethui()) or game:GetService("CoreGui"))
local Main = Instance.new("Frame", ScreenGui)
Main.Size = UDim2.new(0, 200, 0, 160)
Main.Position = UDim2.new(0.5, -100, 0.4, 0)
Main.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
Main.BorderSizePixel = 0
Main.Active = true
Main.Draggable = true
Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 10)

local ActionBtn = Instance.new("TextButton", Main)
ActionBtn.Size = UDim2.new(0.85, 0, 0, 45)
ActionBtn.Position = UDim2.new(0.075, 0, 0.6, 0)
ActionBtn.BackgroundColor3 = Color3.fromRGB(255, 0, 100)
ActionBtn.Text = "FORCE START"
ActionBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
ActionBtn.Font = Enum.Font.GothamBold
Instance.new("UICorner", ActionBtn)

local Input = Instance.new("TextBox", Main)
Input.Size = UDim2.new(0.85, 0, 0, 35)
Input.Position = UDim2.new(0.075, 0, 0.25, 0)
Input.PlaceholderText = "QUANTIDADE"
Input.Text = "100"
Input.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Input.TextColor3 = Color3.fromRGB(255, 255, 255)
Instance.new("UICorner", Input)

-- [[ ENGINE DE ALTA VELOCIDADE ]] --
local function NitroTransfer(amount)
    local count = tonumber(amount) or 0
    if count <= 0 then return end

    ActionBtn.Text = "INJETANDO..."
    ActionBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)

    -- ANTI-CRASH: Divide a carga em blocos para o processador não travar
    for i = 1, count do
        -- Disparo imediato em paralelo (Zero Delay)
        task.spawn(function()
            -- Puxa o item
            mercadinmote:FireServer("vaidemed", {"Tratamento"})
            
            -- Transfere (InvokeServer dentro de spawn vira FireServer na prática, não espera resposta)
            task.spawn(function()
                invRequest:InvokeServer("trasnferebau", "Entro", "Tratamento", 5, 1)
            end)
        end)

        -- Se a quantidade for muito grande (ex: 500+), evita que o Roblox congele
        if i % 40 == 0 then 
            task.wait() -- Pequeno respiro pro processador a cada 40 itens
        end
    end

    ActionBtn.Text = "FINALIZADO"
    ActionBtn.BackgroundColor3 = Color3.fromRGB(0, 200, 100)
    task.wait(1)
    ActionBtn.Text = "FORCE START"
end

ActionBtn.MouseButton1Click:Connect(function()
    NitroTransfer(Input.Text)
end)

-- Anti-AFK
local VirtualUser = game:GetService('VirtualUser')
lp.Idled:Connect(function()
    VirtualUser:CaptureController()
    VirtualUser:ClickButton2(Vector2.new())
end)

print("⚡ Modo Nitro Ativado. Puxando itens em tempo recorde.")
