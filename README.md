
-- [[ BRUTAL HUB V311 - OMNI ULTIMATE [PREMIUM] ]]
-- [2026-01-10] UI ANIMADA | NOTIFICAÇÕES | SLIDERS PREMIUM
-- UI: v43 REMASTER | LOGIC: v57 | FEATURES: V113 FULL | ESP: JAN 10

local CoreGui = game:GetService("CoreGui")
local RS = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local Players = game:GetService("Players")
local Lighting = game:GetService("Lighting")
local TS = game:GetService("TeleportService")
local StarterGui = game:GetService("StarterGui")
local TweenService = game:GetService("TweenService")
local VirtualUser = game:GetService("VirtualUser")
local UserService = game:GetService("UserService")


local lp = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local Mouse = lp:GetMouse()
local TargetPlayer = nil

-- [[ CONFIGURAÇÃO GLOBAL (PRESETS OTIMIZADOS) ]]
_G.Speed = false; _G.WalkSpeedValue = 100
_G.JumpPower = false; _G.JumpPowerValue = 100
_G.Fly = false; _G.FlySpeedValue = 100
_G.NoClip = false; _G.InfJump = false
_G.AntiSit = false; _G.AntiFling = false; _G.AntiAFK = true
_G.GravityValue = 196.2
_G.Swim = false; _G.SwimSpeed = 50
_G.Float = false; _G.FloatHeight = 0
_G.AutoClicker = false
_G.ClickSpeed = 10 -- Cliques por segundo
_G.ShowWatermark = true
_G.AntiRecording = false
_G.ProAim = false
_G.AimPart = "Head"
_G.AimSmoothness = 0.2 -- Valor de suavização humana
_G.PredictionAmount = 0.165 -- Para compensar o ping/movimento
_G.TeamCheck = false
_G.GodMode = false
_G.Telekinesis = false
_G.TK_Power = 50



-- [[ CONFIGURAÇÕES GLOBAIS ]]
_G.AimbotEnabled = false
_G.AimbotPart = "Head"
_G.AimbotFov = 100
_G.AimbotSmooth = 1
_G.ShowFOV = false
_G.WallCheck = false
_G.ShowFOV = false
_G.FovSize = 100

-- Variáveis de controle interno
local IsAiming = false
local AimKey = Enum.UserInputType.MouseButton2 -- Botão Direito
_G.HitboxEnabled = false; _G.HitboxSize = 20
_G.SpinBot = false; _G.SpinSpeed = 20
_G.TouchFling = false

_G.ESP_Box = false; _G.ESP_Name = false; _G.ESP_Tracer = false; _G.ESP_Distance = 5000
_G.FullBright = false; _G.NoFog = false; _G.XRay = false



-- [[ MOTOR RGB & TEMA ]]
local RGB_Color = Color3.fromRGB(255, 0, 0)
local Theme = {
    Bg = Color3.fromRGB(15, 15, 17),
    Item = Color3.fromRGB(25, 25, 28),
    Hover = Color3.fromRGB(35, 35, 38),
    Text = Color3.fromRGB(240, 240, 240),
    TextDark = Color3.fromRGB(150, 150, 150)
}

task.spawn(function()
    local c = 0
    while true do
        c = (c + 0.002) % 1
        RGB_Color = Color3.fromHSV(c, 0.85, 1)
        task.wait()
    end
end)
-- [[ FOV CIRCLE SINCRONIZADO ]]
local FovCircle = Drawing.new("Circle")
FovCircle.Thickness = 1
FovCircle.NumSides = 90
FovCircle.Filled = false
FovCircle.Transparency = 1

RS.RenderStepped:Connect(function()
    local mousePos = UIS:GetMouseLocation()
    
    -- Pega os valores das variáveis que os seus botões controlam
    FovCircle.Position = Vector2.new(mousePos.X, mousePos.Y)
    FovCircle.Radius = _G.FovSize or 100  -- Se não existir, usa 100
    FovCircle.Visible = _G.ShowFOV or false -- Se não existir, fica invisível
    FovCircle.Color = RGB_Color -- Deixa o FOV em RGB igual ao seu tema!
    
    -- Atualiza também a tabela do Aimbot para a lógica de alvo saber o novo tamanho
    if _G.Aimbot then
        _G.Aimbot.Fov = _G.FovSize or 100
    end
end)
-- [[ SISTEMA DE NOTIFICAÇÃO CUSTOMIZADO ]]
local NotifyGui = Instance.new("ScreenGui", CoreGui); NotifyGui.Name = "BrutalNotify"
local NotifyList = Instance.new("UIListLayout", NotifyGui)
NotifyList.HorizontalAlignment = Enum.HorizontalAlignment.Right
NotifyList.VerticalAlignment = Enum.VerticalAlignment.Bottom
NotifyList.Padding = UDim.new(0, 5)

local function SendNotify(title, text, duration)
    local Frame = Instance.new("Frame", NotifyGui)
    Frame.Size = UDim2.new(0, 250, 0, 50)
    Frame.Position = UDim2.new(1, 260, 1, -60) -- Start off screen
    Frame.BackgroundColor3 = Theme.Bg
    Frame.BorderSizePixel = 0
    Instance.new("UICorner", Frame).CornerRadius = UDim.new(0, 6)
    
    local Stroke = Instance.new("UIStroke", Frame)
    Stroke.Color = RGB_Color; Stroke.Thickness = 1.5; Stroke.Transparency = 0.5

    local T = Instance.new("TextLabel", Frame)
    T.Text = title; T.Font = Enum.Font.GothamBold; T.TextSize = 12
    T.TextColor3 = RGB_Color; T.Size = UDim2.new(1, -10, 0, 20); T.Position = UDim2.new(0, 10, 0, 5)
    T.BackgroundTransparency = 1; T.TextXAlignment = Enum.TextXAlignment.Left

    local D = Instance.new("TextLabel", Frame)
    D.Text = text; D.Font = Enum.Font.Gotham; D.TextSize = 11
    D.TextColor3 = Theme.Text; D.Size = UDim2.new(1, -10, 0, 20); D.Position = UDim2.new(0, 10, 0, 25)
    D.BackgroundTransparency = 1; D.TextXAlignment = Enum.TextXAlignment.Left

    -- Animação Entrada
    Frame.Position = UDim2.new(1, -10, 1, -((#NotifyGui:GetChildren()) * 55))
    TweenService:Create(Frame, TweenInfo.new(0.5, Enum.EasingStyle.Exponential), {BackgroundTransparency = 0.1}):Play()
    
    task.delay(duration or 2, function()
        TweenService:Create(Frame, TweenInfo.new(0.5), {BackgroundTransparency = 1}):Play()
        TweenService:Create(T, TweenInfo.new(0.5), {TextTransparency = 1}):Play()
        TweenService:Create(D, TweenInfo.new(0.5), {TextTransparency = 1}):Play()
        TweenService:Create(Stroke, TweenInfo.new(0.5), {Transparency = 1}):Play()
        task.wait(0.5)
        Frame:Destroy()
    end)
end

-- [[ ESP ORIGINAL 10/01/2026 (INTOCADO) ]]
local function CreateESP(p)
    local Box = Drawing.new("Square"); Box.Thickness = 1; Box.Transparency = 1
    local Tracer = Drawing.new("Line"); Tracer.Thickness = 1; Tracer.Transparency = 1
    local Name = Drawing.new("Text"); Name.Size = 13; Name.Center = true; Name.Outline = true; Name.Transparency = 1
    task.spawn(function()
        while p and p.Parent do
            if p.Character and p.Character:FindFirstChild("HumanoidRootPart") and p.Character.Humanoid.Health > 0 then
                local hrp = p.Character.HumanoidRootPart
                local pos, vis = Camera:WorldToViewportPoint(hrp.Position)
                if vis and (Camera.CFrame.Position - hrp.Position).Magnitude <= _G.ESP_Distance then
                    Box.Visible = _G.ESP_Box; Box.Size = Vector2.new(2000/pos.Z, 2500/pos.Z); Box.Position = Vector2.new(pos.X - Box.Size.X/2, pos.Y - Box.Size.Y/2); Box.Color = RGB_Color
                    Tracer.Visible = _G.ESP_Tracer; Tracer.From = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y); Tracer.To = Vector2.new(pos.X, pos.Y); Tracer.Color = RGB_Color
                    Name.Visible = _G.ESP_Name; Name.Position = Vector2.new(pos.X, pos.Y - (2500/pos.Z)/2 - 15); Name.Text = p.DisplayName; Name.Color = Color3.new(1,1,1)
                else Box.Visible = false; Tracer.Visible = false; Name.Visible = false end
            else Box.Visible = false; Tracer.Visible = false; Name.Visible = false end
            task.wait()
        end
        Box:Destroy(); Tracer:Destroy(); Name:Destroy()
    end)
end
for _, v in pairs(Players:GetPlayers()) do if v ~= lp then CreateESP(v) end end
Players.PlayerAdded:Connect(function(v) if v ~= lp then CreateESP(v) end end)

-- [[ INTERFACE v43 REMASTERIZADA ]]
local function BuildUI()
    if CoreGui:FindFirstChild("Brutal Hub") then CoreGui.BrutalHub:Destroy() end
    local Gui = Instance.new("ScreenGui", CoreGui); Gui.Name = "Brutal Hub"

    -- Botão de Abrir (Animado)
    local OpenBtn = Instance.new("TextButton", Gui)
    OpenBtn.Size = UDim2.new(0, 50, 0, 50); OpenBtn.Position = UDim2.new(0, 20, 0.5, -25)
    OpenBtn.BackgroundColor3 = Theme.Bg; OpenBtn.Text = "☠"; OpenBtn.TextColor3 = Color3.new(1,1,1)
    OpenBtn.TextSize = 25; OpenBtn.Font = Enum.Font.GothamBold; OpenBtn.Visible = false
    Instance.new("UICorner", OpenBtn).CornerRadius = UDim.new(1,0)
    local OS = Instance.new("UIStroke", OpenBtn); OS.Thickness = 2; OS.Color = RGB_Color
    
    -- Main Window
    local Main = Instance.new("Frame", Gui)
    Main.Size = UDim2.new(0, 700, 0, 500); Main.Position = UDim2.new(0.5, -350, 0.5, -250)
    Main.BackgroundColor3 = Theme.Bg; Main.ClipsDescendants = true
    Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 8)
    local MS = Instance.new("UIStroke", Main); MS.Thickness = 1; MS.Color = Color3.fromRGB(40,40,40)

   -- [[ PROFILE SYSTEM v43 ]]
    local ProfileFrame = Instance.new("Frame", Main)
    ProfileFrame.Size = UDim2.new(0, 160, 0, 60)
    ProfileFrame.Position = UDim2.new(0, 5, 1, -65) -- Fica no rodapé da sidebar
    ProfileFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 23)
    ProfileFrame.BorderSizePixel = 0
    Instance.new("UICorner", ProfileFrame).CornerRadius = UDim.new(0, 6)
    
    local ProfileStroke = Instance.new("UIStroke", ProfileFrame)
    ProfileStroke.Thickness = 1
    ProfileStroke.Color = Color3.fromRGB(40, 40, 43)

    local AvatarImg = Instance.new("ImageLabel", ProfileFrame)
    AvatarImg.Size = UDim2.new(0, 45, 0, 45)
    AvatarImg.Position = UDim2.new(0, 7, 0, 7)
    AvatarImg.BackgroundColor3 = Theme.Item
    AvatarImg.Image = "rbxthumb://type=AvatarHeadShot&id=" .. lp.UserId .. "&w=150&h=150"
    Instance.new("UICorner", AvatarImg).CornerRadius = UDim.new(1, 0) -- Avatar Redondo

    local UserName = Instance.new("TextLabel", ProfileFrame)
    UserName.Text = lp.DisplayName
    UserName.Size = UDim2.new(1, -60, 0, 20)
    UserName.Position = UDim2.new(0, 57, 0, 10)
    UserName.TextColor3 = Theme.Text
    UserName.Font = Enum.Font.GothamBold
    UserName.TextSize = 11
    UserName.TextXAlignment = Enum.TextXAlignment.Left
    UserName.BackgroundTransparency = 1

    local UserID = Instance.new("TextLabel", ProfileFrame)
    UserID.Text = "@" .. lp.Name
    UserID.Size = UDim2.new(1, -60, 0, 20)
    UserID.Position = UDim2.new(0, 57, 0, 25)
    UserID.TextColor3 = Theme.TextDark
    UserID.Font = Enum.Font.Gotham
    UserID.TextSize = 10
    UserID.TextXAlignment = Enum.TextXAlignment.Left
    UserID.BackgroundTransparency = 1

    -- Efeito de Pulsação RGB no contorno do perfil
    task.spawn(function()
        while task.wait() do
            ProfileStroke.Color = RGB_Color
        end
    end)

    -- Top Bar
    local Top = Instance.new("Frame", Main)
    Top.Size = UDim2.new(1,0,0,45); Top.BackgroundColor3 = Color3.fromRGB(20,20,22)
    Instance.new("UICorner", Top).CornerRadius = UDim.new(0, 8)
    local Title = Instance.new("TextLabel", Top)
    Title.Text = "  BRUTAL HUB "; Title.Size = UDim2.new(1,0,1,0)
    Title.TextColor3 = Theme.Text; Title.Font = Enum.Font.GothamBold; Title.TextSize = 14
    Title.TextXAlignment = Enum.TextXAlignment.Left; Title.BackgroundTransparency = 1
    
    local Mini = Instance.new("TextButton", Top); Mini.Text = "-"; Mini.Size = UDim2.new(0,35,0,35)
    Mini.Position = UDim2.new(1,-40,0,5); Mini.BackgroundColor3 = Theme.Item
    Mini.TextColor3 = Theme.Text; Instance.new("UICorner", Mini).CornerRadius = UDim.new(0,6)

    -- Efeitos de Abrir/Fechar
    Mini.MouseButton1Click:Connect(function()
        TweenService:Create(Main, TweenInfo.new(0.3), {Size = UDim2.new(0,700,0,0)}):Play()
        task.wait(0.3); Main.Visible = false; OpenBtn.Visible = true
        TweenService:Create(OpenBtn, TweenInfo.new(0.3), {Rotation = 360}):Play()
    end)
    OpenBtn.MouseButton1Click:Connect(function()
        OpenBtn.Visible = false; Main.Visible = true; Main.Size = UDim2.new(0,700,0,0)
        TweenService:Create(Main, TweenInfo.new(0.4, Enum.EasingStyle.Back), {Size = UDim2.new(0,700,0,500)}):Play()
    end)

    -- Sidebar (Tabs)
    local Sidebar = Instance.new("ScrollingFrame", Main)
    Sidebar.Size = UDim2.new(0, 170, 1, -50); Sidebar.Position = UDim2.new(0, 5, 0, 50)
    Sidebar.BackgroundTransparency = 1; Sidebar.ScrollBarThickness = 0
    local SideList = Instance.new("UIListLayout", Sidebar); SideList.Padding = UDim.new(0, 6)
    
    local PageHolder = Instance.new("Frame", Main)
    PageHolder.Size = UDim2.new(1, -185, 1, -60); PageHolder.Position = UDim2.new(0, 180, 0, 50)
    PageHolder.BackgroundTransparency = 1

    local Pages = {}

    local function CreatePage(name)
        local f = Instance.new("ScrollingFrame", PageHolder); f.Name = name; f.Size = UDim2.new(1,0,1,0)
        f.Visible = false; f.BackgroundTransparency = 1; f.CanvasSize = UDim2.new(0,0,0,0)
        f.ScrollBarThickness = 3; f.ScrollBarImageColor3 = RGB_Color
        local list = Instance.new("UIListLayout", f); list.Padding = UDim.new(0, 8)
        list:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function() f.CanvasSize = UDim2.new(0,0,0,list.AbsoluteContentSize.Y+20) end)
        return f
    end

    Pages.Locomocao = CreatePage("Locomocao")
    Pages.Combate = CreatePage("Combate")
    Pages.Visuais = CreatePage("Visuais")
    Pages.Exploits = CreatePage("Exploits")
    Pages.Util = CreatePage("Util")
    Pages.Sistema = CreatePage("Sistema")
    Pages.Locomocao.Visible = true

    -- Função de Tab Animada
    local function AddTab(text, page)
        local btn = Instance.new("TextButton", Sidebar)
        btn.Size = UDim2.new(1, -5, 0, 40); btn.Text = text
        btn.BackgroundColor3 = Theme.Item; btn.TextColor3 = Theme.TextDark
        btn.Font = Enum.Font.GothamSemibold; btn.TextSize = 12
        Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)
        
        -- Efeito de Seleção
        btn.MouseButton1Click:Connect(function()
            for _, p in pairs(Pages) do p.Visible = false end
            page.Visible = true
            for _, b in pairs(Sidebar:GetChildren()) do 
                if b:IsA("TextButton") then 
                    TweenService:Create(b, TweenInfo.new(0.2), {BackgroundColor3 = Theme.Item, TextColor3 = Theme.TextDark}):Play() 
                end 
            end
            TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundColor3 = Theme.Hover, TextColor3 = RGB_Color}):Play()
        end)
    end

    AddTab("LOCOMOÇÃO", Pages.Locomocao)
    AddTab("COMBATE", Pages.Combate)
    AddTab("VISUAIS", Pages.Visuais)
    AddTab("EXPLOITS", Pages.Exploits)
    AddTab("UTILITÁRIOS", Pages.Util)
    AddTab("SISTEMA", Pages.Sistema)

    -- [[ COMPONENTES UI APRIMORADOS ]]

    local function AddToggle(text, var, page)
        local btn = Instance.new("TextButton", page)
        btn.Size = UDim2.new(0.98, 0, 0, 42); btn.Text = "  " .. text
        btn.BackgroundColor3 = Theme.Item; btn.TextColor3 = Theme.Text
        btn.Font = Enum.Font.Gotham; btn.TextSize = 12; btn.TextXAlignment = Enum.TextXAlignment.Left
        btn.AutoButtonColor = false
        Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)

        local indicator = Instance.new("Frame", btn)
        indicator.Size = UDim2.new(0, 10, 0, 10); indicator.Position = UDim2.new(1, -25, 0.5, -5)
        indicator.BackgroundColor3 = _G[var] and RGB_Color or Color3.fromRGB(60,60,60)
        Instance.new("UICorner", indicator).CornerRadius = UDim.new(1,0)
        
        btn.MouseButton1Click:Connect(function()
            _G[var] = not _G[var]
            local state = _G[var] and "ATIVADO" or "DESATIVADO"
            SendNotify(text, state, 1.5)
            
            TweenService:Create(indicator, TweenInfo.new(0.3), {BackgroundColor3 = _G[var] and RGB_Color or Color3.fromRGB(60,60,60)}):Play()
            -- Efeito de Clique
            TweenService:Create(btn, TweenInfo.new(0.1), {Size = UDim2.new(0.95,0,0,42)}):Play()
            task.wait(0.1)
            TweenService:Create(btn, TweenInfo.new(0.1), {Size = UDim2.new(0.98,0,0,42)}):Play()
        end)
    end

    local function AddSlider(text, min, max, default, page, var)
    local f = Instance.new("Frame", page)
    f.Size = UDim2.new(0.98, 0, 0, 55)
    f.BackgroundTransparency = 1
    
    local l = Instance.new("TextLabel", f)
    l.Text = "  " .. text
    l.Size = UDim2.new(1, 0, 0, 20)
    l.TextColor3 = Color3.fromRGB(220, 220, 200) -- Tom bege/dourado
    l.Font = "GothamBold"
    l.TextSize = 11
    l.BackgroundTransparency = 1
    l.TextXAlignment = "Left"

    local vL = Instance.new("TextLabel", f)
    vL.Text = tostring(default)
    vL.Size = UDim2.new(1, -10, 0, 20)
    vL.TextColor3 = Color3.fromRGB(255, 230, 150) -- Valor em destaque
    vL.Font = "GothamBold"
    vL.TextSize = 14
    vL.BackgroundTransparency = 1
    vL.TextXAlignment = "Right"

    -- Fundo do Slider (Mais escuro e fino, como na imagem)
    local sB = Instance.new("Frame", f)
    sB.Size = UDim2.new(1, -15, 0, 4) -- Fino
    sB.Position = UDim2.new(0, 7, 0, 35)
    sB.BackgroundColor3 = Color3.fromRGB(35, 32, 30) -- Marrom bem escuro
    Instance.new("UICorner", sB).CornerRadius = UDim.new(1, 0)
    local sStroke = Instance.new("UIStroke", sB)
    sStroke.Color = Color3.fromRGB(60, 55, 50)
    sStroke.Thickness = 1

    -- Preenchimento (Cor amarelada/creme da imagem)
    local sF = Instance.new("Frame", sB)
    sF.Size = UDim2.new((default-min)/(max-min), 0, 1, 0)
    sF.BackgroundColor3 = Color3.fromRGB(255, 220, 130) -- Cor idêntica à imagem
    Instance.new("UICorner", sF).CornerRadius = UDim.new(1, 0)

    -- O Círculo (Knob)
    local Knob = Instance.new("Frame", sF)
    Knob.AnchorPoint = Vector2.new(0.5, 0.5)
    Knob.Size = UDim2.new(0, 14, 0, 14)
    Knob.Position = UDim2.new(1, 0, 0.5, 0)
    Knob.BackgroundColor3 = Color3.fromRGB(230, 210, 170)
    Instance.new("UICorner", Knob).CornerRadius = UDim.new(1, 0)
    local kStroke = Instance.new("UIStroke", Knob)
    kStroke.Thickness = 1.5
    kStroke.Color = Color3.fromRGB(40, 35, 30)

    local b = Instance.new("TextButton", sB)
    b.Size = UDim2.new(1, 0, 1, 0)
    b.BackgroundTransparency = 1
    b.Text = ""

    local drag = false
    local function update()
        local p = math.clamp((UIS:GetMouseLocation().X - sB.AbsolutePosition.X) / sB.AbsoluteSize.X, 0, 1)
        _G[var] = math.floor(min + ((max - min) * p))
        vL.Text = tostring(_G[var])
        sF.Size = UDim2.new(p, 0, 1, 0)
    end

    b.InputBegan:Connect(function(i) 
        if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then 
            drag = true 
            update()
        end 
    end)
    UIS.InputEnded:Connect(function(i) 
        if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then 
            drag = false 
        end 
    end)
    UIS.InputChanged:Connect(function(i) 
        if drag and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
            update()
        end 
    end)
end

    local function AddBtn(text, page, callback)
        local btn = Instance.new("TextButton", page)
        btn.Size = UDim2.new(0.98, 0, 0, 42); btn.Text = text
        btn.BackgroundColor3 = Theme.Item; btn.TextColor3 = Theme.Text
        btn.Font = Enum.Font.GothamBold; btn.TextSize = 12
        Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)
        
        btn.MouseEnter:Connect(function() TweenService:Create(btn, TweenInfo.new(0.3), {BackgroundColor3 = Theme.Hover}):Play() end)
        btn.MouseLeave:Connect(function() TweenService:Create(btn, TweenInfo.new(0.3), {BackgroundColor3 = Theme.Item}):Play() end)

        btn.MouseButton1Click:Connect(function()
            TweenService:Create(btn, TweenInfo.new(0.1), {Size = UDim2.new(0.95,0,0,42)}):Play()
            task.wait(0.1)
            TweenService:Create(btn, TweenInfo.new(0.1), {Size = UDim2.new(0.98,0,0,42)}):Play()
            callback()
        end)
    end

    local function AddInput(placeholder, page, callback)
        local box = Instance.new("TextBox", page)
        box.Size = UDim2.new(0.98, 0, 0, 42); box.PlaceholderText = placeholder; box.Text = ""
        box.BackgroundColor3 = Theme.Item; box.TextColor3 = Theme.Text
        box.Font = Enum.Font.Gotham; box.TextSize = 12
        Instance.new("UICorner", box).CornerRadius = UDim.new(0, 6)
        box.FocusLost:Connect(function() callback(box.Text) end)
    end

    -- [[ POPULANDO A UI ]]

    -- LOCOMOÇÃO
    AddToggle("Speed Hack", "Speed", Pages.Locomocao); AddSlider("Força", 16, 500, 100, Pages.Locomocao, "WalkSpeedValue")
    AddToggle("Super pulo", "JumpPower", Pages.Locomocao); AddSlider("Altura", 50, 500, 100, Pages.Locomocao, "JumpPowerValue")
    AddToggle("Fly Mode", "Fly", Pages.Locomocao); AddSlider("Velocidade Voo", 10, 500, 100, Pages.Locomocao, "FlySpeedValue")
    AddToggle("No Clip", "NoClip", Pages.Locomocao)
    AddToggle("Infinite Jump", "InfJump", Pages.Locomocao)
    AddSlider("Gravidade", 0, 196, 196, Pages.Locomocao, "GravityValue")
    AddToggle("Swim", "Swim", Pages.Locomocao)
    AddSlider("Velocidade Swin", 10, 300, 50, Pages.Locomocao, "SwimSpeed")
    AddToggle("Float", "Float", Pages.Locomocao)
    AddSlider("Ajustar Float", -100, 100, 0, Pages.Locomocao, "FloatHeight")
  

  -- COMBATE 
    AddToggle("God Mode", "GodMode", Pages.Combate)
    AddToggle("Aimbot Pro", "ProAim", Pages.Combate)
    AddToggle("team", "TeamCheck", Pages.Combate)
    AddSlider("Suavização (Smooth)", 1, 10, 2, Pages.Combate, "AimSmoothness")
    AddSlider("Prediction", 0, 100, 16, Pages.Combate, "PredictionAmount")
    AddToggle("Hitbox Expander", "HitboxEnabled", Pages.Combate); AddSlider("Tamanho Hitbox", 2, 50, 20, Pages.Combate, "HitboxSize")
    AddToggle("SpinBot ", "SpinBot", Pages.Combate); AddSlider("Velocidade Giro", 1, 100, 20, Pages.Combate, "SpinSpeed")
    AddToggle("Touch Fling ", "TouchFling", Pages.Combate)
    AddToggle("Ativar Aimbot", "AimbotEnabled", Pages.Combate)
    AddToggle("Mostrar FO", "ShowFOV", Pages.Combate)
    AddToggle("Wall Check (Paredes)", "WallCheck", Pages.Combate)
  
    AddSlider("Suavização Mira", 1, 20, 1, Pages.Combate, "AimbotSmooth")
-- Botão de Alternar Parte (Head -> Torso -> Root)
local parts = {"Head", "HumanoidRootPart", "UpperTorso"}
local pIndex = 1

AddBtn("Parte: " .. _G.AimbotPart, Pages.Combate, function()
    pIndex = pIndex + 1
    if pIndex > #parts then pIndex = 1 end
    _G.AimbotPart = parts[pIndex]
    SendNotify("Aimbot", "Mirando em: " .. _G.AimbotPart, 2)
end)
-- Na aba COMBATE

    -- VISUAIS
    AddToggle("ESP Box (RGB)", "ESP_Box", Pages.Visuais)
    AddToggle("ESP Nome", "ESP_Name", Pages.Visuais)
    AddToggle("ESP Linhas", "ESP_Tracer", Pages.Visuais)
    AddSlider("Distância ESP", 100, 10000, 5000, Pages.Visuais, "ESP_Distance")
    AddToggle("Full Bright (Luz)", "FullBright", Pages.Visuais)
    AddToggle("Remover Neblina", "NoFog", Pages.Visuais)
    AddToggle("X-Ray (Paredes)", "XRay", Pages.Visuais)

    -- EXPLOITS


    AddBtn("Teleportar para Alvo", Pages.Util, function() if TargetPlayer and TargetPlayer.Character then lp.Character:MoveTo(TargetPlayer.Character.HumanoidRootPart.Position) end end)
    AddBtn("Assistir Alvo (Spectate)", Pages.Util, function() if TargetPlayer then Camera.CameraSubject = TargetPlayer.Character.Humanoid end end)
    AddBtn("Parar de Assistir", Pages.Util, function() Camera.CameraSubject = lp.Character.Humanoid end)
    AddBtn("Pegar TP Tool", Pages.Util, function() local t=Instance.new("Tool", lp.Backpack); t.Name="TP Tool"; t.RequiresHandle=false; t.Activated:Connect(function() lp.Character:MoveTo(Mouse.Hit.p) end) SendNotify("Tool", "Adicionada", 2) end)
    AddToggle("Auto Clicker ", "AutoClicker", Pages.Util)
    AddSlider("Velocidade (CPS)", 1, 50, 10, Pages.Util, "ClickSpeed")
    local PlayerListFrame = Instance.new("ScrollingFrame", Pages.Util)
    PlayerListFrame.Size = UDim2.new(0.98, 0, 0, 300) -- Altura da lista
    PlayerListFrame.BackgroundTransparency = 1
    PlayerListFrame.ScrollBarThickness = 2
    PlayerListFrame.ScrollBarImageColor3 = RGB_Color

local PL_List = Instance.new("UIListLayout", PlayerListFrame)
PL_List.Padding = UDim.new(0, 5)

-- Função para atualizar a lista de players
local function UpdatePlayerList()
    for _, child in pairs(PlayerListFrame:GetChildren()) do
        if child:IsA("Frame") then child:Destroy() end
    end

    for _, p in pairs(Players:GetPlayers()) do
        if p == lp then continue end -- Não mostra você mesmo
        
        local pFrame = Instance.new("Frame", PlayerListFrame)
        pFrame.Size = UDim2.new(1, -10, 0, 45)
        pFrame.BackgroundColor3 = Theme.Item
        Instance.new("UICorner", pFrame).CornerRadius = UDim.new(0, 6)
        
        local pName = Instance.new("TextLabel", pFrame)
        pName.Text = " " .. p.DisplayName .. " (@" .. p.Name .. ")"
        pName.Size = UDim2.new(0.6, 0, 1, 0)
        pName.TextColor3 = Theme.Text
        pName.Font = Enum.Font.GothamSemibold
        pName.TextSize = 11
        pName.BackgroundTransparency = 1
        pName.TextXAlignment = Enum.TextXAlignment.Left

        -- Botão de Selecionar (Target)
        local selBtn = Instance.new("TextButton", pFrame)
        selBtn.Text = "🎯"
        selBtn.Size = UDim2.new(0, 30, 0, 30); selBtn.Position = UDim2.new(1, -100, 0.5, -15)
        selBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 45)
        selBtn.TextColor3 = Color3.new(1,1,1)
        Instance.new("UICorner", selBtn).CornerRadius = UDim.new(0, 4)
        
        -- Botão de Teleport (TP)
        local tpBtn = Instance.new("TextButton", pFrame)
        tpBtn.Text = "📍"
        tpBtn.Size = UDim2.new(0, 30, 0, 30); tpBtn.Position = UDim2.new(1, -65, 0.5, -15)
        tpBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 45)
        Instance.new("UICorner", tpBtn).CornerRadius = UDim.new(0, 4)

        -- Botão de Spectate (Olho)
        local spBtn = Instance.new("TextButton", pFrame)
        spBtn.Text = "👁️"
        spBtn.Size = UDim2.new(0, 30, 0, 30); spBtn.Position = UDim2.new(1, -30, 0.5, -15)
        spBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 45)
        Instance.new("UICorner", spBtn).CornerRadius = UDim.new(0, 4)

        -- Lógica dos botões
        selBtn.MouseButton1Click:Connect(function()
            TargetPlayer = p
            SendNotify("Target", "Alvo definido: " .. p.DisplayName, 2)
        end)

        tpBtn.MouseButton1Click:Connect(function()
            if p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                lp.Character.HumanoidRootPart.CFrame = p.Character.HumanoidRootPart.CFrame
            end
        end)

        spBtn.MouseButton1Click:Connect(function()
            if p.Character and p.Character:FindFirstChild("Humanoid") then
                Camera.CameraSubject = p.Character.Humanoid
                SendNotify("Spectate", "Assistindo: " .. p.DisplayName, 2)
            end
        end)
    end
    PlayerListFrame.CanvasSize = UDim2.new(0, 0, 0, PL_List.AbsoluteContentSize.Y)
end

-- Botão de Refresh no topo da aba
AddBtn("Atualizar Lista de Jogadores", Pages.Util, function()
    UpdatePlayerList()
end)

-- Atualiza automaticamente quando alguém entra ou sai
Players.PlayerAdded:Connect(UpdatePlayerList)
Players.PlayerRemoving:Connect(UpdatePlayerList)

    -- SISTEMA
    AddBtn("Rejoin", Pages.Sistema, function() TS:TeleportToPlaceInstance(game.PlaceId, game.JobId, lp) end)
    AddBtn("server Hop", Pages.Sistema, function() TS:Teleport(game.PlaceId) end)
    AddToggle("Anti AFK", "AntiAFK", Pages.Sistema)
    AddToggle("Anti Fling", "AntiFling", Pages.Sistema)
    AddToggle("Mostrar Fps", "ShowWatermark", Pages.Sistema)
    AddToggle("Anti-Gravação (OBS/dc alguns executor)", "AntiRecording", Pages.Sistema)
    AddBtn("Salvar Configurações (refazendo)", Pages.Sistema, function()
    SaveSettings()
end)

AddBtn("Carregar Configurações(refazendo)", Pages.Sistema, function()
    LoadSettings()
end)

AddBtn("Resetar Configs(refazendo)", Pages.Sistema, function()
    if isfile(ConfigFile) then
        delfile(ConfigFile)
        SendNotify("SISTEMA", "Configs deletadas! Reinicie o script.", 3)
    end
end)
    -- Draggable
    local dragging, dragInput, dragStart, startPos
    local function update(input)
        local delta = input.Position - dragStart
        Main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
    Top.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true; dragStart = input.Position; startPos = Main.Position
            input.Changed:Connect(function() if input.UserInputState == Enum.UserInputState.End then dragging = false end end)
        end
    end)
    Top.InputChanged:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseMovement and dragging then update(input) end end)
end

local function GetClosestTarget()
    local target = nil
    local shortestDist = _G.AimbotFov or 100
    local mousePos = UIS:GetMouseLocation()

    for _, p in pairs(Players:GetPlayers()) do
        if p ~= lp and p.Character and p.Character:FindFirstChild("Humanoid") and p.Character.Humanoid.Health > 0 then
            -- Verifica se a parte do corpo selecionada existe no boneco
            local bodyPart = p.Character:FindFirstChild(_G.AimbotPart)
            if bodyPart then
                -- Wall Check (Opcional)
                if _G.WallCheck then
                    local ray = Camera:GetPartsObscuringTarget({bodyPart.Position}, {lp.Character, p.Character})
                    if #ray > 0 then continue end
                end

                local pos, vis = Camera:WorldToViewportPoint(bodyPart.Position)
                if vis then
                    local dist = (Vector2.new(pos.X, pos.Y) - mousePos).Magnitude
                    if dist < shortestDist then
                        shortestDist = dist
                        target = p
                    end
                end
            end
        end
    end
    return target
end

-- [[ LÓGICA PRINCIPAL (v57 ENGINE) ]]

RS.Stepped:Connect(function()
    if not lp.Character then return end
    local hum = lp.Character:FindFirstChild("Humanoid")
    local hrp = lp.Character:FindFirstChild("HumanoidRootPart")

    if hum and hrp then
        workspace.Gravity = _G.GravityValue
-- Detectar Tecla de Mira
UIS.InputBegan:Connect(function(i, g)
    if not g and i.UserInputType == AimKey then IsAiming = true end
end)
UIS.InputEnded:Connect(function(i)
    if i.UserInputType == AimKey then IsAiming = false end
end)


    if _G.AimbotEnabled and IsAiming then
        local target = GetClosestTarget()
        if target and target.Character and target.Character:FindFirstChild(_G.AimbotPart) then
            local goal = CFrame.new(Camera.CFrame.Position, target.Character[_G.AimbotPart].Position)
            Camera.CFrame = Camera.CFrame:Lerp(goal, 1 / (_G.AimbotSmooth or 1))
        end
    end
    
        -- Locomoção
        if _G.Speed then hum.WalkSpeed = _G.WalkSpeedValue end
        if _G.JumpPower then hum.JumpPower = _G.JumpPowerValue end
        if _G.Fly then hrp.Velocity = Camera.CFrame.LookVector * _G.FlySpeedValue + Vector3.new(0, 0, 0) end
        if _G.NoClip then for _, v in pairs(lp.Character:GetDescendants()) do if v:IsA("BasePart") then v.CanCollide = false end end end
        if _G.AntiSit then hum.Sit = false end
        if _G.InfJump and UIS:IsKeyDown(Enum.KeyCode.Space) then hum:ChangeState(Enum.HumanoidStateType.Jumping) end

        -- Combate: Hitbox
        if _G.HitboxEnabled then
            for _, p in pairs(Players:GetPlayers()) do
                if p ~= lp and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                    p.Character.HumanoidRootPart.Size = Vector3.new(_G.HitboxSize, _G.HitboxSize, _G.HitboxSize)
                    p.Character.HumanoidRootPart.Transparency = 0.6
                    p.Character.HumanoidRootPart.CanCollide = false
                end
            end
        end

        -- Combate: Spinbot
        if _G.SpinBot then
            hrp.CFrame = hrp.CFrame * CFrame.Angles(0, math.rad(_G.SpinSpeed), 0)
        end

        -- Visuais
        if _G.FullBright then Lighting.Brightness = 2; Lighting.ClockTime = 14; Lighting.GlobalShadows = false end
        if _G.NoFog then Lighting.FogEnd = 100000 end
        if _G.XRay then for _, v in pairs(workspace:GetDescendants()) do if v:IsA("BasePart") then v.Transparency = 0.5 end end end
    end
end)


-- Loop Anti-AFK
lp.Idled:Connect(function()
    if _G.AntiAFK then VirtualUser:CaptureController(); VirtualUser:ClickButton2(Vector2.new()) end
end)

-- [[ LÓGICA TOUCH FLING (SKIDDOS ADAPTADO) ]]
task.spawn(function()
    local movel = 0.1
    while true do
        task.wait() -- RunService.Heartbeat:Wait()
        if _G.TouchFling then
            local char = lp.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            
            if hrp then
                local oldVel = hrp.Velocity
                -- A mágica do Fling: Velocidade extrema por um milissegundo
                hrp.Velocity = oldVel * 10000 + Vector3.new(0, 10000, 0)
                RS.RenderStepped:Wait()
                
                if hrp and hrp.Parent then
                    hrp.Velocity = oldVel
                end
                
                RS.Stepped:Wait()
                if hrp and hrp.Parent then
                    hrp.Velocity = oldVel + Vector3.new(0, movel, 0)
                    movel = movel * -1
                end
            end
        end
    end
end)

-- [[ SISTEMA DE SWIM / WATER FLY ]]
task.spawn(function()
    while true do
        task.wait()
        if _G.Swim then
            local char = lp.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            local hum = char and char:FindFirstChild("Humanoid")
            
            if hrp and hum then
                -- Faz o player entrar no estado de nado
                hum:ChangeState(Enum.HumanoidStateType.Swimming)
                
                -- Calcula a direção baseada no movimento (W,A,S,D) e Câmera
                local moveDir = hum.MoveDirection
                local cameraCF = Camera.CFrame
                
                if moveDir.Magnitude > 0 then
                    -- Se estiver se movendo, aplica velocidade na direção da câmera
                    hrp.Velocity = cameraCF.LookVector * _G.SwimSpeed
                else
                    -- Se estiver parado, anula a gravidade para boiar
                    hrp.Velocity = Vector3.new(0, 0, 0)
                end
                
                -- Se segurar Espaço, sobe. Se segurar Ctrl, desce.
                if UIS:IsKeyDown(Enum.KeyCode.Space) then
                    hrp.Velocity = hrp.Velocity + Vector3.new(0, _G.SwimSpeed, 0)
                elseif UIS:IsKeyDown(Enum.KeyCode.LeftControl) then
                    hrp.Velocity = hrp.Velocity + Vector3.new(0, -_G.SwimSpeed, 0)
                end
            end
        end
    end
end)

-- [[ SISTEMA DE FLOAT PREMIUM ]]
task.spawn(function()
    while true do
        task.wait()
        local char = lp.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        local hum = char and char:FindFirstChild("Humanoid")

        if _G.Float and hrp and hum then
            -- Anula a gravidade apenas no eixo vertical
            local currentVel = hrp.Velocity
            hrp.Velocity = Vector3.new(currentVel.X, 0, currentVel.Z)
            
            -- Permite ajuste fino de altura via Slider ou Teclado
            if UIS:IsKeyDown(Enum.KeyCode.E) then
                hrp.CFrame = hrp.CFrame * CFrame.new(0, 0.5, 0)
            elseif UIS:IsKeyDown(Enum.KeyCode.Q) then
                hrp.CFrame = hrp.CFrame * CFrame.new(0, -0.5, 0)
            end
            
            -- Aplica o offset do Slider
            if _G.FloatHeight ~= 0 then
                hrp.CFrame = hrp.CFrame * CFrame.new(0, _G.FloatHeight/10, 0)
                _G.FloatHeight = 0 -- Reseta para não subir infinitamente
            end
        end
    end
end)

-- [[ SISTEMA DE AUTO CLICKER ]]
task.spawn(function()
    while true do
        if _G.AutoClicker then
            -- Verifica se o jogo está em foco para não clicar fora do Roblox
            if UIS:GetFocusedTextBox() == nil then
                VirtualUser:CaptureController()
                VirtualUser:ClickButton1(Vector2.new(Mouse.X, Mouse.Y))
            end
            -- Calcula o delay baseado no CPS (Cliques por Segundo)
            task.wait(1 / (_G.ClickSpeed or 10))
        else
            task.wait(0.5) -- Idle quando desligado
        end
    end
end)
-- [[ SISTEMA DE WATERMARK PREMIUM ]]
local WatermarkGui = Instance.new("ScreenGui", CoreGui)
WatermarkGui.Name = "BrutalWatermark"

local WMain = Instance.new("Frame", WatermarkGui)
WMain.Size = UDim2.new(0, 220, 0, 35)
WMain.Position = UDim2.new(1, -230, 0, 10) -- Canto superior direito
WMain.BackgroundColor3 = Theme.Bg
WMain.BorderSizePixel = 0
Instance.new("UICorner", WMain).CornerRadius = UDim.new(0, 6)

local WStroke = Instance.new("UIStroke", WMain)
WStroke.Thickness = 1.5
WStroke.Color = RGB_Color

local WText = Instance.new("TextLabel", WMain)
WText.Size = UDim2.new(1, 0, 1, 0)
WText.BackgroundTransparency = 1
WText.Font = Enum.Font.GothamBold
WText.TextColor3 = Color3.fromRGB(255, 255, 255)
WText.TextSize = 12
WText.Text = "BRUTAL HUB | FPS: 00 | 00:00:00"

-- Lógica de Atualização
task.spawn(function()
    local startTime = os.time()
    while true do
        if _G.ShowWatermark then
            WMain.Visible = true
            WStroke.Color = RGB_Color -- Sincroniza com o seu motor RGB
            
            -- Cálculo de FPS
            local fps = math.floor(1 / task.wait())
            
            -- Cálculo de Uptime
            local uptime = os.time() - startTime
            local hours = math.floor(uptime / 3600)
            local mins = math.floor((uptime % 3600) / 60)
            local secs = uptime % 60
            
            WText.Text = string.format("BRUTAL HUB | FPS: %02d | %02d:%02d:%02d", fps, hours, mins, secs)
        else
            WMain.Visible = false
            task.wait(0.5)
        end
    end
end)

-- [[ SISTEMA ANTI-GRAVAÇÃO / ANTI-SCREENSHOT ]]
task.spawn(function()
    while true do
        task.wait(0.5)
        -- Lista de todas as GUIs do seu script
        local HubGuis = {
            CoreGui:FindFirstChild("Brutal"),
            CoreGui:FindFirstChild("BrutalNotify"),
            CoreGui:FindFirstChild("BrutalWatermark")
        }

        for _, gui in pairs(HubGuis) do
            if gui and gui:IsA("ScreenGui") then
                if _G.AntiRecording then
                    -- Tenta usar a propriedade de proteção de conteúdo (se disponível no executor)
                    -- E define um DisplayOrder extremamente alto
                    gui.DisplayOrder = 999999
                    
                    -- Nota: Alguns executores possuem a função 'set_gui_protection'
                    -- Se o seu executor suportar, ele esconderá a GUI de capturas de tela.
                    if pcall(function() return set_gui_protection end) then
                        set_gui_protection(gui, true)
                    end
                else
                    if pcall(function() return set_gui_protection end) then
                        set_gui_protection(gui, false)
                    end
                    gui.DisplayOrder = 0
                end
            end
        end
    end
end)
-- [[ MOTOR PRO AIM V311 ]]
local function GetProTarget()
    local closest = nil
    local shortestDist = _G.AimbotFov or 150
    local mousePos = UIS:GetMouseLocation()

    for _, p in pairs(Players:GetPlayers()) do
        if p ~= lp and p.Character then
            -- Team Check
            if _G.TeamCheck and p.Team == lp.Team then continue end
            
            local targetPart = p.Character:FindFirstChild(_G.AimPart)
            local hum = p.Character:FindFirstChild("Humanoid")
            
            if targetPart and hum and hum.Health > 0 then
                local pos, vis = Camera:WorldToViewportPoint(targetPart.Position)
                
                if vis then
                    -- Wall Check Avançado
                    if _G.WallCheck then
                        local castPoints = {targetPart.Position}
                        local ignoreList = {lp.Character, p.Character}
                        if #Camera:GetPartsObscuringTarget(castPoints, ignoreList) > 0 then continue end
                    end

                    local dist = (mousePos - Vector2.new(pos.X, pos.Y)).Magnitude
                    if dist < shortestDist then
                        closest = p
                        shortestDist = dist
                    end
                end
            end
        end
    end
    return closest
end

task.spawn(function()
    RS.RenderStepped:Connect(function()
        if _G.ProAim and UIS:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) then
            local target = GetProTarget()
            
            if target and target.Character and target.Character:FindFirstChild(_G.AimPart) then
                local part = target.Character[_G.AimPart]
                local velocity = part.Velocity
                
                -- Cálculo de Predição (Compensa movimento do alvo)
                local predictedPos = part.Position + (velocity * (_G.PredictionAmount / 100))
                
                -- Suavização Humana (Interpolation)
                local targetCF = CFrame.new(Camera.CFrame.Position, predictedPos)
                local smoothValue = (_G.AimSmoothness / 10)
                
                Camera.CFrame = Camera.CFrame:Lerp(targetCF, smoothValue)
            end
        end
    end)
end)

-- [[ SISTEMA DE GOD MODE PREMIUM ]]
task.spawn(function()
    while true do
        task.wait(0.1)
        if _G.GodMode then
            local char = lp.Character
            local hum = char and char:FindFirstChildOfClass("Humanoid")
            
            if char and hum and hum.Health > 0 then
                -- Método v113: Remove a conexão de morte
                hum:SetStateEnabled(Enum.HumanoidStateType.Dead, false)
                
                -- Se a vida baixar de 20%, ele tenta forçar o God Mode
                if hum.Health < 20 then
                    local oldPos = char.HumanoidRootPart.CFrame
                    -- Deleta o script de dano local se existir
                    local healthScript = char:FindFirstChild("Health")
                    if healthScript then healthScript:Destroy() end
                    
                    SendNotify("God Mode", "Proteção de vida ativada!", 2)
                end
            end
        end
    end
end)

-- Sistema de Re-Aplicação ao Morrer/Resetar
lp.CharacterAdded:Connect(function(char)
    if _G.GodMode then
        task.wait(1)
        SendNotify("God Mode", "Reaplicando imortalidade...", 2)
    end
end)

-- [[ SISTEMA DE TELECINESE PC - OMNI ULTIMATE ]]
local TK_Target = nil
local TK_BP = nil
local TK_BG = nil

local function StopTK()
    if TK_BP then TK_BP:Destroy(); TK_BP = nil end
    if TK_BG then TK_BG:Destroy(); TK_BG = nil end
    TK_Target = nil
end

task.spawn(function()
    while true do
        task.wait()
        if _G.Telekinesis then
            -- Inicia ao apertar F
            if UIS:IsKeyDown(Enum.KeyCode.F) and not TK_Target then
                local target = Mouse.Target
                if target and not target.Anchored and (target:IsA("BasePart") or target.Parent:FindFirstChild("Humanoid")) then
                    TK_Target = target
                    
                    -- Cria as forças físicas
                    TK_BP = Instance.new("BodyPosition", TK_Target)
                    TK_BP.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                    TK_BP.P = 10000
                    
                    TK_BG = Instance.new("BodyGyro", TK_Target)
                    TK_BG.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
                    TK_BG.P = 10000
                    
                    SendNotify("Telecinese", "Objeto Capturado!", 1.5)
                end
            end
            
            -- Move o objeto enquanto segura F
            if UIS:IsKeyDown(Enum.KeyCode.F) and TK_Target then
                if TK_BP then
                    -- Mantém o objeto a uma distância de 15 studs do mouse
                    TK_BP.Position = Mouse.Hit.p + Vector3.new(0, 5, 0)
                    TK_BG.CFrame = Camera.CFrame
                end
            elseif not UIS:IsKeyDown(Enum.KeyCode.F) and TK_Target then
                -- Ao soltar F, arremessa
                if TK_Target then
                    TK_Target.Velocity = Camera.CFrame.LookVector * _G.TK_Power
                    StopTK()
                    SendNotify("Telecinese", "Objeto Lançado!", 1)
                end
            end
        else
            if TK_Target then StopTK() end
        end
    end
end)

-- [[ SISTEMA DE CONFIGURAÇÃO OMNI ]]
local HttpService = game:GetService("HttpService")
local ConfigFile = "BrutalHub_Configs.json"

local function SaveSettings()
    local ConfigData = {}
    -- Adicione aqui todas as variáveis que você quer salvar
    for i, v in pairs(_G) do
        if type(v) ~= "function" and type(v) ~= "userdata" then
            ConfigData[i] = v
        end
    end
    
    local json = HttpService:JSONEncode(ConfigData)
    writefile(ConfigFile, json)
    SendNotify("SISTEMA", "Configurações Salvas com Sucesso!", 2)
end

local function LoadSettings()
    if isfile(ConfigFile) then
        local json = readfile(ConfigFile)
        local ConfigData = HttpService:JSONDecode(json)
        
        for i, v in pairs(ConfigData) do
            _G[i] = v
        end
        SendNotify("SISTEMA", "Configurações Carregadas!", 2)
    end
end

BuildUI()
SendNotify("by kachan e kethy", "Brutal Hub: Beta", 3)
-- Carrega automaticamente ao iniciar
pcall(LoadSettings)
