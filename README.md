-- [[ SS HUB - TREATMENT AUTO-TRANSFER (ELITE) ]] --

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local lp = Players.LocalPlayer

-- Cache de Remotes
local mercadinmote = ReplicatedStorage:WaitForChild("RemoteNovos"):WaitForChild("mercadinmote")
local invRequest = ReplicatedStorage:WaitForChild("Modules"):WaitForChild("InvRemotes"):WaitForChild("InvRequest")

-- [[ CRIACAO DA UI (GETHUI) ]] --
local ScreenGui = Instance.new("ScreenGui", (gethui and gethui()) or game:GetService("CoreGui"))
ScreenGui.Name = "SS_Tratamento_Transfer"

local Main = Instance.new("Frame", ScreenGui)
Main.Size = UDim2.new(0, 200, 0, 160) -- UI Menor e compacta
Main.Position = UDim2.new(0.5, -100, 0.4, 0)
Main.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
Main.BorderSizePixel = 0
Main.Active = true
Main.Draggable = true

local Corner = Instance.new("UICorner", Main)
Corner.CornerRadius = UDim.new(0, 10)

local Title = Instance.new("TextLabel", Main)
Title.Size = UDim2.new(1, 0, 0, 35)
Title.Text = "⚡ SS HUB - FARM"
Title.TextColor3 = Color3.fromRGB(255, 255, 0)
Title.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 14
Instance.new("UICorner", Title)

local Input = Instance.new("TextBox", Main)
Input.Size = UDim2.new(0.85, 0, 0, 35)
Input.Position = UDim2.new(0.075, 0, 0.3, 0)
Input.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
Input.PlaceholderText = "Quantid. (Ex: 100)"
Input.Text = ""
Input.TextColor3 = Color3.fromRGB(255, 255, 255)
Input.Font = Enum.Font.Gotham
Instance.new("UICorner", Input)

local ActionBtn = Instance.new("TextButton", Main)
ActionBtn.Size = UDim2.new(0.85, 0, 0, 40)
ActionBtn.Position = UDim2.new(0.075, 0, 0.65, 0)
ActionBtn.BackgroundColor3 = Color3.fromRGB(0, 180, 100)
ActionBtn.Text = "INICIAR CICLO"
ActionBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
ActionBtn.Font = Enum.Font.GothamBold
Instance.new("UICorner", ActionBtn)

-- [[ LOGICA DE TRANSFERENCIA ]] --
local function StartTransfer(amount)
    local total = tonumber(amount) or 0
    if total <= 0 then return end
    
    ActionBtn.Text = "PROCESSANDO..."
    ActionBtn.BackgroundColor3 = Color3.fromRGB(150, 100, 0)
    
    for i = 1, total do
        -- Puxa 1 Tratamento
        task.spawn(function()
            mercadinmote:FireServer("vaidemed", {"Tratamento"})
        end)
        
        -- Transfere para o Bau IMEDIATAMENTE
        -- Usando task.spawn para não esperar a resposta do servidor e ser ultra-rápido
        task.spawn(function()
            invRequest:InvokeServer("trasnferebau", "Entro", "Tratamento", 5, 1)
        end)
        
        -- Pequeno delay para o servidor não engasgar (0.01 é imperceptível)
        if i % 10 == 0 then task.wait() end 
    end
    
    ActionBtn.Text = "CONCLUÍDO!"
    ActionBtn.BackgroundColor3 = Color3.fromRGB(0, 180, 100)
    task.wait(1)
    ActionBtn.Text = "INICIAR CICLO"
end

ActionBtn.MouseButton1Click:Connect(function()
    StartTransfer(Input.Text)
end)

-- Anti-AFK integrado
local VirtualUser = game:GetService('VirtualUser')
lp.Idled:Connect(function()
    VirtualUser:CaptureController()
    VirtualUser:ClickButton2(Vector2.new())
end)

print("✅ UI Reduzida e Otimizada carregada no GETHUI.")
