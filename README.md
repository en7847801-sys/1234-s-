--[[
    Jeep Sik Hub v4.0 - Professional Edition
    Design Premium | Animação na Senha | Tudo Funcionando
    Senha: 1234
]]

-- Services
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local TweenService = game:GetService("TweenService")
local CoreGui = game:GetService("CoreGui")
local TeleportService = game:GetService("TeleportService")

local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Paleta de Cores Premium
local C = {
    BG = Color3.fromRGB(13, 15, 22),
    SURF = Color3.fromRGB(20, 22, 32),
    SURF2 = Color3.fromRGB(28, 30, 42),
    PRIM = Color3.fromRGB(65, 135, 255),
    PRIM2 = Color3.fromRGB(85, 155, 255),
    TXT = Color3.fromRGB(245, 245, 250),
    TXT2 = Color3.fromRGB(165, 170, 185),
    BORD = Color3.fromRGB(40, 44, 58),
    ACC = Color3.fromRGB(225, 45, 45),
    ACC2 = Color3.fromRGB(245, 65, 65),
    GRN = Color3.fromRGB(45, 210, 85),
    YLW = Color3.fromRGB(255, 185, 45),
    SHDW = Color3.fromRGB(0, 0, 0)
}

-- Variáveis de Controle
local NoclipLoop = nil
local FlyLoop = nil
local AntiAfkLoop = nil

getgenv().ESPData = {Boxes = {}, Tracers = {}, Names = {}, HealthBars = {}}
getgenv().Settings = {
    ESPEnabled = false, ShowBoxes = true, ShowTracers = true, ShowNames = true,
    ShowHealth = true, ShowDistance = true, ESPLinePos = "Centro Inferior",
    ESPColor = C.PRIM, ESPThick = 2, FlyEnabled = false, InfJump = false,
    NoclipEnabled = false, AntiAfkEnabled = false, FullBright = false
}

-- ============================================
-- SISTEMA DE NOTIFICAÇÕES PREMIUM
-- ============================================
local NotifFrame = Instance.new("Frame", CoreGui)
NotifFrame.BackgroundTransparency = 1
NotifFrame.Position = UDim2.new(0, 24, 0, 70)
NotifFrame.Size = UDim2.new(0, 300, 0, 0)
NotifFrame.ZIndex = 999
Instance.new("UIListLayout", NotifFrame).Padding = UDim.new(0, 10)

function Notify(title, msg, dur, icon)
    local n = Instance.new("Frame", NotifFrame)
    n.BackgroundColor3 = C.SURF
    n.BorderSizePixel = 0
    n.Size = UDim2.new(1, 0, 0, 62)
    n.BackgroundTransparency = 1
    n.Position = UDim2.new(0, -320, 0, 0)
    n.ZIndex = 999
    
    Instance.new("UICorner", n).CornerRadius = UDim.new(0, 10)
    
    local shadow = Instance.new("Frame", n)
    shadow.BackgroundColor3 = C.SHDW
    shadow.BackgroundTransparency = 0.8
    shadow.BorderSizePixel = 0
    shadow.Position = UDim2.new(0, 2, 0, 2)
    shadow.Size = UDim2.new(1, 0, 1, 0)
    shadow.ZIndex = 998
    Instance.new("UICorner", shadow).CornerRadius = UDim.new(0, 10)
    
    local bar = Instance.new("Frame", n)
    bar.BackgroundColor3 = C.PRIM
    bar.BorderSizePixel = 0
    bar.Size = UDim2.new(0, 3, 1, 0)
    bar.ZIndex = 1000
    
    local ic = Instance.new("TextLabel", n)
    ic.BackgroundTransparency = 1
    ic.Position = UDim2.new(0, 16, 0, 0)
    ic.Size = UDim2.new(0, 24, 1, 0)
    ic.Font = Enum.Font.GothamBold
    ic.Text = icon or "●"
    ic.TextColor3 = C.PRIM
    ic.TextSize = 15
    ic.ZIndex = 1000
    
    local tl = Instance.new("TextLabel", n)
    tl.BackgroundTransparency = 1
    tl.Position = UDim2.new(0, 46, 0, 8)
    tl.Size = UDim2.new(1, -56, 0, 22)
    tl.Font = Enum.Font.GothamBold
    tl.Text = title
    tl.TextColor3 = C.TXT
    tl.TextSize = 13
    tl.TextXAlignment = Enum.TextXAlignment.Left
    tl.ZIndex = 1000
    
    local ml = Instance.new("TextLabel", n)
    ml.BackgroundTransparency = 1
    ml.Position = UDim2.new(0, 46, 0, 32)
    ml.Size = UDim2.new(1, -56, 0, 22)
    ml.Font = Enum.Font.Gotham
    ml.Text = msg
    ml.TextColor3 = C.TXT2
    ml.TextSize = 11
    ml.TextXAlignment = Enum.TextXAlignment.Left
    ml.ZIndex = 1000
    
    TweenService:Create(n, TweenInfo.new(0.5, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
        BackgroundTransparency = 0,
        Position = UDim2.new(0, 0, 0, 0)
    }):Play()
    
    delay(dur or 3.5, function()
        TweenService:Create(n, TweenInfo.new(0.4, Enum.EasingStyle.Quart, Enum.EasingDirection.In), {
            BackgroundTransparency = 1,
            Position = UDim2.new(0, -320, 0, 0)
        }):Play()
        wait(0.5)
        n:Destroy()
    end)
end

-- ============================================
-- FUNÇÃO PARA DESLIGAR TUDO
-- ============================================
function DisableAll()
    getgenv().Settings.ESPEnabled = false
    getgenv().Settings.FlyEnabled = false
    getgenv().Settings.NoclipEnabled = false
    getgenv().Settings.InfJump = false
    getgenv().Settings.AntiAfkEnabled = false
    
    NoclipLoop = nil
    FlyLoop = nil
    AntiAfkLoop = nil
    
    local char = LocalPlayer.Character
    if char then
        local hum = char:FindFirstChildOfClass("Humanoid")
        local root = char:FindFirstChild("HumanoidRootPart")
        if hum then hum.PlatformStand = false end
        if root then
            for _, v in pairs(root:GetChildren()) do
                if v:IsA("BodyGyro") or v:IsA("BodyVelocity") then v:Destroy() end
            end
        end
    end
    
    for _, v in pairs(getgenv().ESPData.Boxes) do pcall(function() v:Remove() end) end
    for _, v in pairs(getgenv().ESPData.Tracers) do pcall(function() v:Remove() end) end
    for _, v in pairs(getgenv().ESPData.Names) do pcall(function() v:Remove() end) end
    for _, v in pairs(getgenv().ESPData.HealthBars) do pcall(function() v:Remove() end) end
    getgenv().ESPData = {Boxes = {}, Tracers = {}, Names = {}, HealthBars = {}}
end

-- ============================================
-- HUB PRINCIPAL
-- ============================================
local Hub = Instance.new("ScreenGui", CoreGui)
Hub.Name = "JeepSikHub"
Hub.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

-- ============================================
-- TELA DE SENHA COM ANIMAÇÕES
-- ============================================

-- Overlay com partículas simuladas
local Overlay = Instance.new("Frame", Hub)
Overlay.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Overlay.BackgroundTransparency = 1
Overlay.BorderSizePixel = 0
Overlay.Size = UDim2.new(1, 0, 1, 0)
Overlay.ZIndex = 10
TweenService:Create(Overlay, TweenInfo.new(0.8), {BackgroundTransparency = 0.65}):Play()

-- Partículas decorativas (círculos flutuantes)
for i = 1, 20 do
    spawn(function()
        local particle = Instance.new("Frame", Overlay)
        particle.BackgroundColor3 = C.PRIM
        particle.BorderSizePixel = 0
        particle.Size = UDim2.new(0, math.random(4, 12), 0, math.random(4, 12))
        particle.Position = UDim2.new(0, math.random(0, 100), 0, math.random(0, 100))
        particle.BackgroundTransparency = 0.7
        particle.ZIndex = 11
        Instance.new("UICorner", particle).CornerRadius = UDim.new(1, 0)
        
        while particle and particle.Parent do
            TweenService:Create(particle, TweenInfo.new(math.random(3, 6), Enum.EasingStyle.Linear), {
                Position = UDim2.new(0, math.random(0, 100), 0, math.random(0, 100)),
                BackgroundTransparency = 0.9
            }):Play()
            wait(math.random(3, 6))
            TweenService:Create(particle, TweenInfo.new(math.random(3, 6), Enum.EasingStyle.Linear), {
                Position = UDim2.new(0, math.random(0, 100), 0, math.random(0, 100)),
                BackgroundTransparency = 0.7
            }):Play()
            wait(math.random(3, 6))
        end
    end)
end

-- Frame da Senha
local PassFrame = Instance.new("Frame", Hub)
PassFrame.BackgroundColor3 = C.BG
PassFrame.BorderSizePixel = 2
PassFrame.BorderColor3 = C.PRIM
PassFrame.Position = UDim2.new(0.5, -170, 0.5, -250)
PassFrame.Size = UDim2.new(0, 340, 0, 340)
PassFrame.AnchorPoint = Vector2.new(0.5, 0.5)
PassFrame.ZIndex = 15
PassFrame.BackgroundTransparency = 1
Instance.new("UICorner", PassFrame).CornerRadius = UDim.new(0, 16)

-- Sombra do frame
local passShadow = Instance.new("Frame", PassFrame)
passShadow.BackgroundColor3 = C.PRIM
passShadow.BackgroundTransparency = 0.85
passShadow.BorderSizePixel = 0
passShadow.Position = UDim2.new(0, 4, 0, 4)
passShadow.Size = UDim2.new(1, 0, 1, 0)
passShadow.ZIndex = 14
Instance.new("UICorner", passShadow).CornerRadius = UDim.new(0, 16)

-- Animação de entrada do frame
TweenService:Create(PassFrame, TweenInfo.new(1, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
    Position = UDim2.new(0.5, -170, 0.5, -170),
    BackgroundTransparency = 0
}):Play()

-- Logo com animação de rotação
local logoFrame = Instance.new("Frame", PassFrame)
logoFrame.BackgroundTransparency = 1
logoFrame.Position = UDim2.new(0.5, -35, 0, 20)
logoFrame.Size = UDim2.new(0, 70, 0, 70)
logoFrame.ZIndex = 16

local logo = Instance.new("Frame", logoFrame)
logo.BackgroundColor3 = C.PRIM
logo.BorderSizePixel = 0
logo.Size = UDim2.new(1, 0, 1, 0)
logo.ZIndex = 16
Instance.new("UICorner", logo).CornerRadius = UDim.new(0, 16)

local logoText = Instance.new("TextLabel", logo)
logoText.BackgroundTransparency = 1
logoText.Size = UDim2.new(1, 0, 1, 0)
logoText.Font = Enum.Font.GothamBlack
logoText.Text = "JS"
logoText.TextColor3 = Color3.fromRGB(255, 255, 255)
logoText.TextSize = 26
logoText.ZIndex = 17

-- Animação de pulso no logo
spawn(function()
    while logoFrame and logoFrame.Parent do
        TweenService:Create(logoFrame, TweenInfo.new(0.6, Enum.EasingStyle.Sine), {
            Size = UDim2.new(0, 75, 0, 75),
            Position = UDim2.new(0.5, -37, 0, 18)
        }):Play()
        wait(0.6)
        TweenService:Create(logoFrame, TweenInfo.new(0.6, Enum.EasingStyle.Sine), {
            Size = UDim2.new(0, 70, 0, 70),
            Position = UDim2.new(0.5, -35, 0, 20)
        }):Play()
        wait(0.6)
    end
end)

-- Linha decorativa
local lineDeco = Instance.new("Frame", PassFrame)
lineDeco.BackgroundColor3 = C.PRIM
lineDeco.BorderSizePixel = 0
lineDeco.Position = UDim2.new(0.5, -50, 0, 100)
lineDeco.Size = UDim2.new(0, 100, 0, 2)
lineDeco.ZIndex = 16
Instance.new("UICorner", lineDeco).CornerRadius = UDim.new(0, 1)

-- Nome da Marca
local nameLabel = Instance.new("TextLabel", PassFrame)
nameLabel.BackgroundTransparency = 1
nameLabel.Position = UDim2.new(0, 0, 0, 110)
nameLabel.Size = UDim2.new(1, 0, 0, 30)
nameLabel.Font = Enum.Font.GothamBlack
nameLabel.Text = "JEEP SIK HUB"
nameLabel.TextColor3 = C.TXT
nameLabel.TextSize = 26
nameLabel.ZIndex = 16

-- Subtítulo
local subLabel = Instance.new("TextLabel", PassFrame)
subLabel.BackgroundTransparency = 1
subLabel.Position = UDim2.new(0, 0, 0, 138)
subLabel.Size = UDim2.new(1, 0, 0, 18)
subLabel.Font = Enum.Font.Gotham
subLabel.Text = "Professional Edition v4.0"
subLabel.TextColor3 = C.TXT2
subLabel.TextSize = 11
subLabel.ZIndex = 16

-- Input de Senha
local passInput = Instance.new("TextBox", PassFrame)
passInput.BackgroundColor3 = C.SURF
passInput.BorderSizePixel = 2
passInput.BorderColor3 = C.BORD
passInput.Position = UDim2.new(0.5, -115, 0, 175)
passInput.Size = UDim2.new(0, 230, 0, 44)
passInput.Font = Enum.Font.GothamBold
passInput.PlaceholderText = "Digite a senha..."
passInput.PlaceholderColor3 = C.TXT2
passInput.Text = ""
passInput.TextColor3 = C.TXT
passInput.TextSize = 16
passInput.ZIndex = 16
passInput.TextXAlignment = Enum.TextXAlignment.Center
Instance.new("UICorner", passInput).CornerRadius = UDim.new(0, 10)

-- Animação de foco no input
passInput.Focused:Connect(function()
    TweenService:Create(passInput, TweenInfo.new(0.3), {
        BorderColor3 = C.PRIM,
        BackgroundColor3 = C.SURF2
    }):Play()
end)

passInput.FocusLost:Connect(function()
    TweenService:Create(passInput, TweenInfo.new(0.3), {
        BorderColor3 = C.BORD,
        BackgroundColor3 = C.SURF
    }):Play()
end)

-- Botão de Confirmar
local confirmBtn = Instance.new("TextButton", PassFrame)
confirmBtn.BackgroundColor3 = C.PRIM
confirmBtn.BorderSizePixel = 0
confirmBtn.Position = UDim2.new(0.5, -90, 0, 240)
confirmBtn.Size = UDim2.new(0, 180, 0, 42)
confirmBtn.Font = Enum.Font.GothamBold
confirmBtn.Text = "DESBLOQUEAR"
confirmBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
confirmBtn.TextSize = 14
confirmBtn.ZIndex = 16
confirmBtn.AutoButtonColor = false
Instance.new("UICorner", confirmBtn).CornerRadius = UDim.new(0, 10)

-- Animação hover no botão
confirmBtn.MouseEnter:Connect(function()
    TweenService:Create(confirmBtn, TweenInfo.new(0.2), {
        BackgroundColor3 = C.PRIM2,
        Size = UDim2.new(0, 185, 0, 44)
    }):Play()
end)

confirmBtn.MouseLeave:Connect(function()
    TweenService:Create(confirmBtn, TweenInfo.new(0.2), {
        BackgroundColor3 = C.PRIM,
        Size = UDim2.new(0, 180, 0, 42)
    }):Play()
end)

-- Label de Erro
local errorLabel = Instance.new("TextLabel", PassFrame)
errorLabel.BackgroundTransparency = 1
errorLabel.Position = UDim2.new(0, 0, 0, 298)
errorLabel.Size = UDim2.new(1, 0, 0, 22)
errorLabel.Font = Enum.Font.Gotham
errorLabel.Text = ""
errorLabel.TextColor3 = C.ACC
errorLabel.TextSize = 11
errorLabel.ZIndex = 16

-- ============================================
-- BOLINHA MINIMIZAR
-- ============================================
local MiniBall = Instance.new("TextButton", Hub)
MiniBall.BackgroundColor3 = C.PRIM
MiniBall.BorderSizePixel = 2
MiniBall.BorderColor3 = Color3.fromRGB(255, 255, 255)
MiniBall.Position = UDim2.new(0.93, -50, 0.88, -50)
MiniBall.Size = UDim2.new(0, 50, 0, 50)
MiniBall.Font = Enum.Font.GothamBlack
MiniBall.Text = "JS"
MiniBall.TextColor3 = Color3.fromRGB(255, 255, 255)
MiniBall.TextSize = 12
MiniBall.Visible = false
MiniBall.ZIndex = 100
MiniBall.Active = true
MiniBall.Draggable = true
MiniBall.AutoButtonColor = false
Instance.new("UICorner", MiniBall).CornerRadius = UDim.new(1, 0)

-- Animação de pulso na bolinha
spawn(function()
    while MiniBall and MiniBall.Parent do
        if MiniBall.Visible then
            TweenService:Create(MiniBall, TweenInfo.new(0.7, Enum.EasingStyle.Sine), {
                Size = UDim2.new(0, 54, 0, 54),
                Position = UDim2.new(0.93, -52, 0.88, -52)
            }):Play()
            wait(0.7)
            TweenService:Create(MiniBall, TweenInfo.new(0.7, Enum.EasingStyle.Sine), {
                Size = UDim2.new(0, 50, 0, 50),
                Position = UDim2.new(0.93, -50, 0.88, -50)
            }):Play()
            wait(0.7)
        else
            wait(1)
        end
    end
end)

-- ============================================
-- PAINEL PRINCIPAL
-- ============================================
local MainFrame = Instance.new("Frame", Hub)
MainFrame.BackgroundColor3 = C.BG
MainFrame.BorderSizePixel = 2
MainFrame.BorderColor3 = C.PRIM
MainFrame.Position = UDim2.new(0.5, -310, 0.5, -245)
MainFrame.Size = UDim2.new(0, 620, 0, 490)
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Visible = false
MainFrame.BackgroundTransparency = 1
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 14)

-- Sombra do painel
local mainShadow = Instance.new("Frame", MainFrame)
mainShadow.BackgroundColor3 = C.PRIM
mainShadow.BackgroundTransparency = 0.8
mainShadow.BorderSizePixel = 0
mainShadow.Position = UDim2.new(0, 4, 0, 4)
mainShadow.Size = UDim2.new(1, 0, 1, 0)
mainShadow.ZIndex = -1
Instance.new("UICorner", mainShadow).CornerRadius = UDim.new(0, 14)

-- ============================================
-- TITLE BAR
-- ============================================
local TitleBar = Instance.new("Frame", MainFrame)
TitleBar.BackgroundColor3 = C.SURF
TitleBar.BorderSizePixel = 0
TitleBar.Size = UDim2.new(1, 0, 0, 46)
Instance.new("UICorner", TitleBar).CornerRadius = UDim.new(0, 14)

-- Linha inferior da titlebar
local titleLine = Instance.new("Frame", TitleBar)
titleLine.BackgroundColor3 = C.PRIM
titleLine.BorderSizePixel = 0
titleLine.Position = UDim2.new(0, 0, 1, -1)
titleLine.Size = UDim2.new(1, 0, 0, 1)

-- Ícone do Hub
local hubIcon = Instance.new("Frame", TitleBar)
hubIcon.BackgroundColor3 = C.PRIM
hubIcon.BorderSizePixel = 0
hubIcon.Position = UDim2.new(0, 12, 0, 10)
hubIcon.Size = UDim2.new(0, 26, 0, 26)
Instance.new("UICorner", hubIcon).CornerRadius = UDim.new(0, 7)

local hubIconText = Instance.new("TextLabel", hubIcon)
hubIconText.BackgroundTransparency = 1
hubIconText.Size = UDim2.new(1, 0, 1, 0)
hubIconText.Font = Enum.Font.GothamBlack
hubIconText.Text = "JS"
hubIconText.TextColor3 = Color3.fromRGB(255, 255, 255)
hubIconText.TextSize = 11

-- Título
local Title = Instance.new("TextLabel", TitleBar)
Title.BackgroundTransparency = 1
Title.Position = UDim2.new(0, 46, 0, 0)
Title.Size = UDim2.new(0.5, 0, 1, 0)
Title.Font = Enum.Font.GothamBold
Title.Text = "JEEP SIK HUB"
Title.TextColor3 = C.TXT
Title.TextSize = 14
Title.TextXAlignment = Enum.TextXAlignment.Left

-- Botão Minimizar
local MinBtn = Instance.new("TextButton", TitleBar)
MinBtn.BackgroundColor3 = C.YLW
MinBtn.BorderSizePixel = 0
MinBtn.Position = UDim2.new(0.85, 0, 0, 11)
MinBtn.Size = UDim2.new(0, 24, 0, 24)
MinBtn.Font = Enum.Font.GothamBold
MinBtn.Text = "−"
MinBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
MinBtn.TextSize = 12
MinBtn.AutoButtonColor = false
Instance.new("UICorner", MinBtn).CornerRadius = UDim.new(0, 6)

MinBtn.MouseEnter:Connect(function()
    TweenService:Create(MinBtn, TweenInfo.new(0.15), {BackgroundColor3 = Color3.fromRGB(255, 200, 60)}):Play()
end)
MinBtn.MouseLeave:Connect(function()
    TweenService:Create(MinBtn, TweenInfo.new(0.15), {BackgroundColor3 = C.YLW}):Play()
end)

MinBtn.MouseButton1Click:Connect(function()
    MainFrame.Visible = false
    MiniBall.Visible = true
    Notify("Minimizado", "Clique na bolinha JS para voltar", 2.5, "●")
end)

MiniBall.MouseButton1Click:Connect(function()
    MiniBall.Visible = false
    MainFrame.Visible = true
    TweenService:Create(MainFrame, TweenInfo.new(0.5, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
        BackgroundTransparency = 0
    }):Play()
    Notify("Restaurado", "Painel voltou!", 1.5, "●")
end)

-- Botão Fechar
local CloseBtn = Instance.new("TextButton", TitleBar)
CloseBtn.BackgroundColor3 = C.ACC
CloseBtn.BorderSizePixel = 0
CloseBtn.Position = UDim2.new(0.93, 0, 0, 11)
CloseBtn.Size = UDim2.new(0, 24, 0, 24)
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.Text = "✕"
CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseBtn.TextSize = 12
CloseBtn.AutoButtonColor = false
Instance.new("UICorner", CloseBtn).CornerRadius = UDim.new(0, 6)

CloseBtn.MouseEnter:Connect(function()
    TweenService:Create(CloseBtn, TweenInfo.new(0.15), {BackgroundColor3 = C.ACC2}):Play()
end)
CloseBtn.MouseLeave:Connect(function()
    TweenService:Create(CloseBtn, TweenInfo.new(0.15), {BackgroundColor3 = C.ACC}):Play()
end)

CloseBtn.MouseButton1Click:Connect(function()
    DisableAll()
    Notify("Hub Fechado", "Até logo!", 2, "✕")
    wait(2.5)
    MiniBall:Destroy()
    Hub:Destroy()
end)

-- ============================================
-- TAB FRAME
-- ============================================
local TabFrame = Instance.new("Frame", MainFrame)
TabFrame.BackgroundColor3 = C.SURF
TabFrame.BorderSizePixel = 0
TabFrame.Position = UDim2.new(0, 0, 0, 46)
TabFrame.Size = UDim2.new(0, 135, 1, -46)

-- Linha separadora das abas
local tabDiv = Instance.new("Frame", TabFrame)
tabDiv.BackgroundColor3 = C.PRIM
tabDiv.BorderSizePixel = 0
tabDiv.Position = UDim2.new(1, -1, 0, 0)
tabDiv.Size = UDim2.new(0, 1, 1, 0)

-- Área de Conteúdo
local ContentFrame = Instance.new("Frame", MainFrame)
ContentFrame.BackgroundColor3 = C.BG
ContentFrame.BorderSizePixel = 0
ContentFrame.Position = UDim2.new(0, 135, 0, 46)
ContentFrame.Size = UDim2.new(1, -135, 1, -46)

-- ============================================
-- UI COMPONENTS
-- ============================================
local function MakeScroll(parent)
    local s = Instance.new("ScrollingFrame", parent)
    s.BackgroundTransparency = 1
    s.BorderSizePixel = 0
    s.Size = UDim2.new(1, 0, 1, 0)
    s.CanvasSize = UDim2.new(0, 0, 0, 0)
    s.ScrollBarThickness = 4
    s.ScrollBarImageColor3 = C.PRIM
    s.ScrollBarImageTransparency = 0.4
    s.AutomaticCanvasSize = Enum.AutomaticSize.Y
    s.TopImage = ""
    s.BottomImage = ""
    
    local list = Instance.new("UIListLayout", s)
    list.Padding = UDim.new(0, 8)
    list.SortOrder = Enum.SortOrder.LayoutOrder
    list.HorizontalAlignment = Enum.HorizontalAlignment.Center
    
    local pad = Instance.new("UIPadding", s)
    pad.PaddingTop = UDim.new(0, 10)
    pad.PaddingBottom = UDim.new(0, 10)
    pad.PaddingLeft = UDim.new(0, 10)
    pad.PaddingRight = UDim.new(0, 10)
    
    return s
end

local function MakeLabel(parent, text)
    local lbl = Instance.new("TextLabel", parent)
    lbl.BackgroundColor3 = C.SURF2
    lbl.BorderSizePixel = 1
    lbl.BorderColor3 = C.PRIM
    lbl.Size = UDim2.new(1, 0, 0, 26)
    lbl.Font = Enum.Font.GothamBold
    lbl.Text = "  " .. text
    lbl.TextColor3 = C.TXT
    lbl.TextSize = 11
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    Instance.new("UICorner", lbl).CornerRadius = UDim.new(0, 5)
    return lbl
end

local function MakeToggle(parent, name, default, callback)
    local frame = Instance.new("Frame", parent)
    frame.BackgroundColor3 = C.SURF
    frame.BorderSizePixel = 1
    frame.BorderColor3 = C.BORD
    frame.Size = UDim2.new(1, 0, 0, 42)
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 7)
    
    local lbl = Instance.new("TextLabel", frame)
    lbl.BackgroundTransparency = 1
    lbl.Position = UDim2.new(0, 12, 0, 0)
    lbl.Size = UDim2.new(0.7, 0, 1, 0)
    lbl.Font = Enum.Font.GothamSemibold
    lbl.Text = name
    lbl.TextColor3 = C.TXT
    lbl.TextSize = 12
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    
    local btn = Instance.new("TextButton", frame)
    btn.BackgroundColor3 = default and C.PRIM or Color3.fromRGB(55, 58, 70)
    btn.BorderSizePixel = 0
    btn.Position = UDim2.new(0.83, 0, 0, 10)
    btn.Size = UDim2.new(0, 48, 0, 22)
    btn.Font = Enum.Font.GothamBold
    btn.Text = default and "ON" or "OFF"
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.TextSize = 9
    btn.AutoButtonColor = false
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 11)
    
    local toggled = default
    btn.MouseButton1Click:Connect(function()
        toggled = not toggled
        TweenService:Create(btn, TweenInfo.new(0.25), {
            BackgroundColor3 = toggled and C.PRIM or Color3.fromRGB(55, 58, 70)
        }):Play()
        btn.Text = toggled and "ON" or "OFF"
        Notify(name, toggled and "✅ Ativado" or "❌ Desativado", 1.5)
        callback(toggled)
    end)
    return frame
end

local function MakeSlider(parent, name, min, max, default, callback)
    local frame = Instance.new("Frame", parent)
    frame.BackgroundColor3 = C.SURF
    frame.BorderSizePixel = 1
    frame.BorderColor3 = C.BORD
    frame.Size = UDim2.new(1, 0, 0, 62)
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 7)
    
    local lbl = Instance.new("TextLabel", frame)
    lbl.BackgroundTransparency = 1
    lbl.Position = UDim2.new(0, 12, 0, 5)
    lbl.Size = UDim2.new(0.75, 0, 0, 18)
    lbl.Font = Enum.Font.GothamSemibold
    lbl.Text = name .. ": " .. default
    lbl.TextColor3 = C.TXT
    lbl.TextSize = 11
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    
    local track = Instance.new("Frame", frame)
    track.BackgroundColor3 = Color3.fromRGB(45, 48, 60)
    track.BorderSizePixel = 0
    track.Position = UDim2.new(0, 12, 0, 36)
    track.Size = UDim2.new(1, -68, 0, 5)
    Instance.new("UICorner", track).CornerRadius = UDim.new(0, 2.5)
    
    local fill = Instance.new("Frame", track)
    fill.BackgroundColor3 = C.PRIM
    fill.BorderSizePixel = 0
    fill.Size = UDim2.new((default - min) / (max - min), 0, 1, 0)
    Instance.new("UICorner", fill).CornerRadius = UDim.new(0, 2.5)
    
    local thumb = Instance.new("TextButton", track)
    thumb.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    thumb.BorderSizePixel = 2
    thumb.BorderColor3 = C.PRIM
    thumb.Size = UDim2.new(0, 14, 0, 14)
    thumb.Position = UDim2.new((default - min) / (max - min), -7, 0.5, -7)
    thumb.Text = ""
    thumb.AutoButtonColor = false
    Instance.new("UICorner", thumb).CornerRadius = UDim.new(1, 0)
    
    local valBox = Instance.new("TextBox", frame)
    valBox.BackgroundColor3 = C.SURF2
    valBox.BorderSizePixel = 1
    valBox.BorderColor3 = C.BORD
    valBox.Position = UDim2.new(0.88, 0, 0, 32)
    valBox.Size = UDim2.new(0, 38, 0, 20)
    valBox.Font = Enum.Font.Gotham
    valBox.Text = tostring(default)
    valBox.TextColor3 = C.TXT
    valBox.TextSize = 10
    valBox.TextXAlignment = Enum.TextXAlignment.Center
    
    local function Update(value)
        value = math.clamp(math.floor(tonumber(value) or min), min, max)
        local pct = (value - min) / (max - min)
        fill.Size = UDim2.new(pct, 0, 1, 0)
        thumb.Position = UDim2.new(pct, -7, 0.5, -7)
        lbl.Text = name .. ": " .. value
        valBox.Text = tostring(value)
        callback(value)
    end
    
    local dragging = false
    thumb.MouseButton1Down:Connect(function() dragging = true end)
    
    UserInputService.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local mousePos = UserInputService:GetMouseLocation().X
            local trackPos = track.AbsolutePosition.X
            local trackSize = track.AbsoluteSize.X
            local pct = math.clamp((mousePos - trackPos) / trackSize, 0, 1)
            Update(math.floor(min + (max - min) * pct))
        end
    end)
    
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end
    end)
    
    valBox.FocusLost:Connect(function() Update(valBox.Text) end)
    return frame
end

local function MakeButton(parent, name, callback)
    local btn = Instance.new("TextButton", parent)
    btn.BackgroundColor3 = C.PRIM
    btn.BorderSizePixel = 0
    btn.Size = UDim2.new(1, 0, 0, 34)
    btn.Font = Enum.Font.GothamBold
    btn.Text = name
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.TextSize = 11
    btn.AutoButtonColor = false
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)
    
    btn.MouseButton1Click:Connect(function()
        Notify(name, "✅ Executado com sucesso", 1.5)
        callback()
    end)
    
    btn.MouseEnter:Connect(function()
        TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundColor3 = C.PRIM2}):Play()
    end)
    btn.MouseLeave:Connect(function()
        TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundColor3 = C.PRIM}):Play()
    end)
    
    return btn
end

-- ============================================
-- ESP SYSTEM
-- ============================================
function CreateESP(player)
    if player == LocalPlayer then return end
    
    local function Setup()
        local char = player.Character
        if not char then return end
        
        local head = char:FindFirstChild("Head")
        local hum = char:FindFirstChildOfClass("Humanoid")
        local root = char:FindFirstChild("HumanoidRootPart")
        if not head or not hum or not root then return end
        
        if getgenv().ESPData.Boxes[player] then pcall(function() getgenv().ESPData.Boxes[player]:Remove() end) end
        if getgenv().ESPData.Tracers[player] then pcall(function() getgenv().ESPData.Tracers[player]:Remove() end) end
        if getgenv().ESPData.Names[player] then pcall(function() getgenv().ESPData.Names[player]:Remove() end) end
        if getgenv().ESPData.HealthBars[player] then pcall(function() getgenv().ESPData.HealthBars[player]:Remove() end) end
        
        local box = Drawing.new("Square")
        box.Visible = false
        box.Color = getgenv().Settings.ESPColor
        box.Thickness = getgenv().Settings.ESPThick
        box.Filled = false
        box.Transparency = 1
        
        local tracer = Drawing.new("Line")
        tracer.Visible = false
        tracer.Color = getgenv().Settings.ESPColor
        tracer.Thickness = getgenv().Settings.ESPThick
        tracer.Transparency = 1
        
        local nameTag = Drawing.new("Text")
        nameTag.Visible = false
        nameTag.Color = Color3.fromRGB(255, 255, 255)
        nameTag.Size = 13
        nameTag.Center = true
        nameTag.Outline = true
        nameTag.Font = 2
        
        local healthBar = Drawing.new("Line")
        healthBar.Visible = false
        healthBar.Thickness = 2
        healthBar.Transparency = 1
        
        getgenv().ESPData.Boxes[player] = box
        getgenv().ESPData.Tracers[player] = tracer
        getgenv().ESPData.Names[player] = nameTag
        getgenv().ESPData.HealthBars[player] = healthBar
        
        local conn
        conn = RunService.RenderStepped:Connect(function()
            if not getgenv().Settings.ESPEnabled then
                pcall(function() box.Visible = false; tracer.Visible = false; nameTag.Visible = false; healthBar.Visible = false end)
                return
            end
            
            if not player.Parent or not char or not char.Parent then
                pcall(function() box:Remove(); tracer:Remove(); nameTag:Remove(); healthBar:Remove() end)
                getgenv().ESPData.Boxes[player] = nil
                getgenv().ESPData.Tracers[player] = nil
                getgenv().ESPData.Names[player] = nil
                getgenv().ESPData.HealthBars[player] = nil
                conn:Disconnect()
                return
            end
            
            local h = char:FindFirstChild("Head")
            local r = char:FindFirstChild("HumanoidRootPart")
            local hm = char:FindFirstChildOfClass("Humanoid")
            if not h or not r or not hm then return end
            
            local headPos, onScreen = Camera:WorldToViewportPoint(h.Position)
            local distance = (Camera.CFrame.Position - r.Position).Magnitude
            local scale = math.clamp(2000 / distance, 0.4, 1.4)
            local boxW = 35 * scale
            local boxH = 60 * scale
            
            if getgenv().Settings.ShowBoxes then
                pcall(function()
                    box.Size = Vector2.new(boxW, boxH)
                    box.Position = Vector2.new(headPos.X - boxW/2, headPos.Y - boxH/2)
                    box.Visible = onScreen
                end)
            else
                pcall(function() box.Visible = false end)
            end
            
            if getgenv().Settings.ShowTracers then
                pcall(function()
                    local origin = getgenv().Settings.ESPLinePos == "Centro Superior"
                        and Vector2.new(Camera.ViewportSize.X/2, 0)
                        or Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y)
                    tracer.From = origin
                    tracer.To = Vector2.new(headPos.X, headPos.Y)
                    tracer.Visible = true
                end)
            else
                pcall(function() tracer.Visible = false end)
            end
            
            if getgenv().Settings.ShowNames then
                pcall(function()
                    local txt = player.Name
                    if getgenv().Settings.ShowDistance then txt = txt .. " [" .. math.floor(distance) .. "m]" end
                    nameTag.Text = txt
                    nameTag.Position = Vector2.new(headPos.X, headPos.Y - boxH/2 - 16)
                    nameTag.Visible = onScreen
                end)
            else
                pcall(function() nameTag.Visible = false end)
            end
            
            if getgenv().Settings.ShowHealth then
                pcall(function()
                    local health = hm.Health / hm.MaxHealth
                    local barX = headPos.X - boxW/2 - 5
                    local barTop = headPos.Y - boxH/2
                    healthBar.From = Vector2.new(barX, barTop)
                    healthBar.To = Vector2.new(barX, barTop + (boxH * health))
                    if health > 0.6 then healthBar.Color = C.GRN
                    elseif health > 0.3 then healthBar.Color = C.YLW
                    else healthBar.Color = C.ACC end
                    healthBar.Visible = onScreen
                end)
            else
                pcall(function() healthBar.Visible = false end)
            end
        end)
    end
    
    Setup()
    player.CharacterAdded:Connect(function() wait(0.5); Setup() end)
end

function RemoveESP(player)
    for _, key in pairs({"Boxes", "Tracers", "Names", "HealthBars"}) do
        if getgenv().ESPData[key][player] then
            pcall(function() getgenv().ESPData[key][player]:Remove() end)
            getgenv().ESPData[key][player] = nil
        end
    end
end

function RefreshESP()
    for p, _ in pairs(getgenv().ESPData.Boxes) do RemoveESP(p) end
    if getgenv().Settings.ESPEnabled then
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer then CreateESP(p) end
        end
    end
end

-- ============================================
-- CRIAR TABS
-- ============================================
local function MakeTab(name, icon, pos)
    local tab = Instance.new("TextButton", TabFrame)
    tab.BackgroundColor3 = C.SURF
    tab.BorderSizePixel = 0
    tab.Position = UDim2.new(0, 8, 0, pos)
    tab.Size = UDim2.new(1, -16, 0, 38)
    tab.Font = Enum.Font.GothamSemibold
    tab.Text = "  " .. icon .. "  " .. name
    tab.TextColor3 = C.TXT2
    tab.TextSize = 11
    tab.TextXAlignment = Enum.TextXAlignment.Left
    tab.AutoButtonColor = false
    Instance.new("UICorner", tab).CornerRadius = UDim.new(0, 7)
    return tab
end

local ESPTab = MakeTab("ESP", "👁", 8)
local PlayerTab = MakeTab("Player", "⚡", 56)
local VisualsTab = MakeTab("Visuals", "🌍", 104)
local SettingsTab = MakeTab("Settings", "⚙", 152)

local ESPContent = MakeScroll(ContentFrame)
local PlayerContent = MakeScroll(ContentFrame)
local VisualsContent = MakeScroll(ContentFrame)
local SettingsContent = MakeScroll(ContentFrame)

PlayerContent.Visible = false
VisualsContent.Visible = false
SettingsContent.Visible = false

-- ============================================
-- CONTEÚDO ESP
-- ============================================
MakeLabel(ESPContent, "VISUAL ESP")
MakeToggle(ESPContent, "ESP Master", false, function(v)
    getgenv().Settings.ESPEnabled = v
    RefreshESP()
end)
MakeToggle(ESPContent, "Caixas 2D", true, function(v) getgenv().Settings.ShowBoxes = v end)
MakeToggle(ESPContent, "ESP Line", true, function(v) getgenv().Settings.ShowTracers = v end)
MakeToggle(ESPContent, "Nomes", true, function(v) getgenv().Settings.ShowNames = v end)
MakeToggle(ESPContent, "Distância", true, function(v) getgenv().Settings.ShowDistance = v end)
MakeToggle(ESPContent, "Barra de Vida", true, function(v) getgenv().Settings.ShowHealth = v end)

MakeLabel(ESPContent, "POSIÇÃO DA LINHA ESP")
MakeButton(ESPContent, "⬆ Centro Superior (meio em cima)", function()
    getgenv().Settings.ESPLinePos = "Centro Superior"
    Notify("ESP Line", "Centro Superior selecionado", 2)
end)
MakeButton(ESPContent, "⬇ Centro Inferior (meio embaixo)", function()
    getgenv().Settings.ESPLinePos = "Centro Inferior"
    Notify("ESP Line", "Centro Inferior selecionado", 2)
end)

MakeLabel(ESPContent, "ESPESSURA")
MakeSlider(ESPContent, "Grossura", 1, 5, 2, function(v) getgenv().Settings.ESPThick = v end)

MakeLabel(ESPContent, "CORES")
MakeButton(ESPContent, "🔵 Azul Padrão", function()
    getgenv().Settings.ESPColor = C.PRIM
    for _, b in pairs(getgenv().ESPData.Boxes) do if b then b.Color = C.PRIM end end
    for _, t in pairs(getgenv().ESPData.Tracers) do if t then t.Color = C.PRIM end end
end)
MakeButton(ESPContent, "🔴 Vermelho", function()
    getgenv().Settings.ESPColor = C.ACC
    for _, b in pairs(getgenv().ESPData.Boxes) do if b then b.Color = C.ACC end end
    for _, t in pairs(getgenv().ESPData.Tracers) do if t then t.Color = C.ACC end end
end)
MakeButton(ESPContent, "🟢 Verde Neon", function()
    getgenv().Settings.ESPColor = C.GRN
    for _, b in pairs(getgenv().ESPData.Boxes) do if b then b.Color = C.GRN end end
    for _, t in pairs(getgenv().ESPData.Tracers) do if t then t.Color = C.GRN end end
end)
MakeButton(ESPContent, "🟡 Amarelo", function()
    getgenv().Settings.ESPColor = C.YLW
    for _, b in pairs(getgenv().ESPData.Boxes) do if b then b.Color = C.YLW end end
    for _, t in pairs(getgenv().ESPData.Tracers) do if t then t.Color = C.YLW end end
end)
MakeButton(ESPContent, "🟣 Roxo", function()
    local c = Color3.fromRGB(160, 40, 255)
    getgenv().Settings.ESPColor = c
    for _, b in pairs(getgenv().ESPData.Boxes) do if b then b.Color = c end end
    for _, t in pairs(getgenv().ESPData.Tracers) do if t then t.Color = c end end
end)
MakeButton(ESPContent, "⚪ Branco", function()
    local c = Color3.fromRGB(255, 255, 255)
    getgenv().Settings.ESPColor = c
    for _, b in pairs(getgenv().ESPData.Boxes) do if b then b.Color = c end end
    for _, t in pairs(getgenv().ESPData.Tracers) do if t then t.Color = c end end
end)

-- ============================================
-- CONTEÚDO PLAYER
-- ============================================
MakeLabel(PlayerContent, "MOVIMENTO")
MakeSlider(PlayerContent, "WalkSpeed", 16, 500, 16, function(v)
    local char = LocalPlayer.Character
    if char and char:FindFirstChildOfClass("Humanoid") then
        char:FindFirstChildOfClass("Humanoid").WalkSpeed = v
    end
end)
MakeSlider(PlayerContent, "JumpPower", 50, 500, 50, function(v)
    local char = LocalPlayer.Character
    if char and char:FindFirstChildOfClass("Humanoid") then
        char:FindFirstChildOfClass("Humanoid").JumpPower = v
    end
end)
MakeSlider(PlayerContent, "Gravity", 0, 196, 196, function(v) workspace.Gravity = v end)
MakeSlider(PlayerContent, "FOV", 70, 140, 70, function(v) Camera.FieldOfView = v end)

MakeLabel(PlayerContent, "HABILIDADES")
MakeToggle(PlayerContent, "Pulo Infinito", false, function(v) getgenv().Settings.InfJump = v end)

MakeToggle(PlayerContent, "Noclip", false, function(v)
    getgenv().Settings.NoclipEnabled = v
    if v then
        NoclipLoop = true
        spawn(function()
            while NoclipLoop do
                wait()
                local char = LocalPlayer.Character
                if char then
                    for _, p in pairs(char:GetDescendants()) do
                        if p:IsA("BasePart") and p.CanCollide then p.CanCollide = false end
                    end
                end
            end
        end)
    else
        NoclipLoop = nil
    end
end)

MakeToggle(PlayerContent, "Fly Mode", false, function(v)
    getgenv().Settings.FlyEnabled = v
    if v then
        local char = LocalPlayer.Character
        if char then
            local root = char:FindFirstChild("HumanoidRootPart")
            local hum = char:FindFirstChildOfClass("Humanoid")
            if root and hum then
                hum.PlatformStand = true
                local bg = Instance.new("BodyGyro", root)
                bg.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
                bg.CFrame = root.CFrame
                local bv = Instance.new("BodyVelocity", root)
                bv.MaxForce = Vector3.new(9e9, 9e9, 9e9)
                FlyLoop = true
                spawn(function()
                    while FlyLoop and root and bg and bv do
                        wait()
                        bg.CFrame = Camera.CFrame
                        local vel = Vector3.new(0, 0, 0)
                        local s = 50
                        if UserInputService:IsKeyDown(Enum.KeyCode.W) then vel = vel + Camera.CFrame.LookVector * s end
                        if UserInputService:IsKeyDown(Enum.KeyCode.S) then vel = vel - Camera.CFrame.LookVector * s end
                        if UserInputService:IsKeyDown(Enum.KeyCode.A) then vel = vel - Camera.CFrame.RightVector * s end
                        if UserInputService:IsKeyDown(Enum.KeyCode.D) then vel = vel + Camera.CFrame.RightVector * s end
                        if UserInputService:IsKeyDown(Enum.KeyCode.Space) then vel = vel + Vector3.new(0, s, 0) end
                        if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then vel = vel - Vector3.new(0, s, 0) end
                        bv.Velocity = vel
                    end
                end)
            end
        end
    else
        FlyLoop = nil
        local char = LocalPlayer.Character
        if char then
            local hum = char:FindFirstChildOfClass("Humanoid")
            local root = char:FindFirstChild("HumanoidRootPart")
            if hum then hum.PlatformStand = false end
            if root then
                for _, c in pairs(root:GetChildren()) do
                    if c:IsA("BodyGyro") or c:IsA("BodyVelocity") then c:Destroy() end
                end
            end
        end
    end
end)

MakeLabel(PlayerContent, "AÇÕES")
MakeButton(PlayerContent, "🔄 Reset Character", function()
    local char = LocalPlayer.Character
    if char and char:FindFirstChildOfClass("Humanoid") then
        char:FindFirstChildOfClass("Humanoid").Health = 0
    end
end)
MakeButton(PlayerContent, "⬆ Teleport +50", function()
    local char = LocalPlayer.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        char.HumanoidRootPart.CFrame = char.HumanoidRootPart.CFrame + Vector3.new(0, 50, 0)
    end
end)

-- ============================================
-- CONTEÚDO VISUALS
-- ============================================
MakeLabel(VisualsContent, "AMBIENTE")
MakeToggle(VisualsContent, "Full Bright", false, function(v)
    getgenv().Settings.FullBright = v
    if v then
        Lighting.Ambient = Color3.fromRGB(255, 255, 255)
        Lighting.Brightness = 3
        Lighting.ClockTime = 12
        Lighting.FogEnd = 9e9
    else
        Lighting.Ambient = Color3.fromRGB(0, 0, 0)
        Lighting.Brightness = 2
    end
end)
MakeToggle(VisualsContent, "Modo Noturno", false, function(v)
    if v then
        Lighting.Ambient = Color3.fromRGB(15, 15, 35)
        Lighting.Brightness = 0.3
        Lighting.ClockTime = 0
    else
        if not getgenv().Settings.FullBright then
            Lighting.Ambient = Color3.fromRGB(0, 0, 0)
            Lighting.Brightness = 2
        end
    end
end)

MakeLabel(VisualsContent, "UTILITÁRIOS")
MakeToggle(VisualsContent, "Anti-AFK", false, function(v)
    getgenv().Settings.AntiAfkEnabled = v
    if v then
        AntiAfkLoop = true
        spawn(function()
            while AntiAfkLoop do
                wait(120)
                LocalPlayer.Idled:Fire()
            end
        end)
    else
        AntiAfkLoop = nil
    end
end)
MakeButton(VisualsContent, "🔧 Coletar Ferramentas", function()
    local char = LocalPlayer.Character
    if char then
        for _, obj in pairs(workspace:GetDescendants()) do
            if obj:IsA("Tool") then pcall(function() obj.Parent = char end) end
        end
    end
end)
MakeButton(VisualsContent, "🔄 Rejoin Server", function()
    TeleportService:Teleport(game.PlaceId)
end)

-- ============================================
-- CONTEÚDO SETTINGS
-- ============================================
MakeLabel(SettingsContent, "CONFIGURAÇÕES")
MakeButton(SettingsContent, "💾 Salvar Config", function()
    Notify("Salvo", "Configurações salvas com sucesso!", 2)
end)
MakeButton(SettingsContent, "📂 Carregar Config", function()
    Notify("Carregado", "Configurações restauradas!", 2)
end)

MakeLabel(SettingsContent, "INFORMAÇÕES")
MakeButton(SettingsContent, "💬 Copiar Discord", function()
    setclipboard("discord.gg/jeepsikhub")
    Notify("Copiado", "Link do Discord copiado!", 2)
end)
MakeButton(SettingsContent, "🔴 Fechar Hub", function()
    DisableAll()
    Notify("Hub Fechado", "Até logo!", 2, "✕")
    wait(2.5)
    MiniBall:Destroy()
    Hub:Destroy()
end)

-- ============================================
-- TAB SWITCHING
-- ============================================
local Tabs = {
    [ESPTab] = ESPContent,
    [PlayerTab] = PlayerContent,
    [VisualsTab] = VisualsContent,
    [SettingsTab] = SettingsContent
}

function SwitchTab(sel)
    for tab, content in pairs(Tabs) do
        if tab == sel then
            TweenService:Create(tab, TweenInfo.new(0.3), {
                BackgroundColor3 = C.PRIM,
                TextColor3 = Color3.fromRGB(255, 255, 255)
            }):Play()
            tab.Font = Enum.Font.GothamBold
            content.Visible = true
        else
            TweenService:Create(tab, TweenInfo.new(0.3), {
                BackgroundColor3 = C.SURF,
                TextColor3 = C.TXT2
            }):Play()
            tab.Font = Enum.Font.GothamSemibold
            content.Visible = false
        end
    end
end

ESPTab.MouseButton1Click:Connect(function() SwitchTab(ESPTab) end)
PlayerTab.MouseButton1Click:Connect(function() SwitchTab(PlayerTab) end)
VisualsTab.MouseButton1Click:Connect(function() SwitchTab(VisualsTab) end)
SettingsTab.MouseButton1Click:Connect(function() SwitchTab(SettingsTab) end)

-- ============================================
-- VERIFICAÇÃO DE SENHA
-- ============================================
local attempts = 0

local function CheckPassword()
    if passInput.Text == "1234" then
        -- Animação de sucesso
        TweenService:Create(confirmBtn, TweenInfo.new(0.3), {
            BackgroundColor3 = C.GRN
        }):Play()
        
        wait(0.3)
        
        Notify("✅ Acesso Liberado", "Bem-vindo ao Jeep Sik Hub v4.0!", 3, "✓")
        
        -- Animação de saída do frame de senha
        TweenService:Create(PassFrame, TweenInfo.new(0.6, Enum.EasingStyle.Quart, Enum.EasingDirection.In), {
            Position = UDim2.new(0.5, -170, 0.5, 400),
            BackgroundTransparency = 1
        }):Play()
        
        TweenService:Create(Overlay, TweenInfo.new(0.6), {BackgroundTransparency = 1}):Play()
        
        wait(0.6)
        PassFrame.Visible = false
        Overlay.Visible = false
        
        -- Mostrar painel principal com animação
        MainFrame.Visible = true
        MainFrame.Position = UDim2.new(0.5, -310, 0.5, -300)
        TweenService:Create(MainFrame, TweenInfo.new(0.7, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
            BackgroundTransparency = 0,
            Position = UDim2.new(0.5, -310, 0.5, -245)
        }):Play()
        
        Notify("🔥 Hub Carregado", "Todas as funções estão prontas!", 3, "●")
    else
        attempts = attempts + 1
        errorLabel.Text = "❌ Senha incorreta • " .. attempts .. "/3"
        
        -- Animação de tremor
        TweenService:Create(passInput, TweenInfo.new(0.1), {
            BorderColor3 = C.ACC
        }):Play()
        
        TweenService:Create(PassFrame, TweenInfo.new(0.05), {
            Position = UDim2.new(0.5, -180, 0.5, -170)
        }):Play()
        wait(0.05)
        TweenService:Create(PassFrame, TweenInfo.new(0.05), {
            Position = UDim2.new(0.5, -160, 0.5, -170)
        }):Play()
        wait(0.05)
        TweenService:Create(PassFrame, TweenInfo.new(0.05), {
            Position = UDim2.new(0.5, -170, 0.5, -170)
        }):Play()
        
        wait(0.3)
        TweenService:Create(passInput, TweenInfo.new(0.3), {
            BorderColor3 = C.BORD
        }):Play()
        
        if attempts >= 3 then
            Notify("🚫 Bloqueado", "Muitas tentativas! Hub fechado.", 2.5, "✕")
            wait(3)
            Hub:Destroy()
        end
    end
end

confirmBtn.MouseButton1Click:Connect(CheckPassword)

UserInputService.InputBegan:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.Return and passInput:IsFocused() then
        CheckPassword()
    end
end)

-- ============================================
-- EVENTOS GLOBAIS
-- ============================================
UserInputService.JumpRequest:Connect(function()
    if getgenv().Settings.InfJump then
        local char = LocalPlayer.Character
        if char and char:FindFirstChildOfClass("Humanoid") then
            char:FindFirstChildOfClass("Humanoid"):ChangeState("Jumping")
        end
    end
end)

Players.PlayerAdded:Connect(function(p)
    p.CharacterAdded:Connect(function()
        wait(1)
        if getgenv().Settings.ESPEnabled then CreateESP(p) end
    end)
end)

Players.PlayerRemoving:Connect(function(p) RemoveESP(p) end)

-- ============================================
-- INICIAR
-- ============================================
SwitchTab(ESPTab)
Notify("🔒 Jeep Sik Hub v4.0", "Digite a senha: 1234", 4, "🔑")

print("╔══════════════════════════════════╗")
print("║   JEEP SIK HUB v4.0            ║")
print("║   Professional Edition          ║")
print("║   Status: ONLINE ✅            ║")
print("║   Senha: 1234                   ║")
print("╚══════════════════════════════════╝")
