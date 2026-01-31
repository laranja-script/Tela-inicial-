--// SERVICES
local Players = game:GetService("Players")
local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local HttpService = game:GetService("HttpService")

--// ANTI DUPLICAÇÃO
if playerGui:FindFirstChild("LaranjaScriptGUI") then
    playerGui.LaranjaScriptGUI:Destroy()
end

--// SCREEN GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "LaranjaScriptGUI"
screenGui.Parent = playerGui
screenGui.ResetOnSpawn = false

--// MAIN FRAME
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 520, 0, 380)
mainFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
mainFrame.AnchorPoint = Vector2.new(0.5, 0.5)
mainFrame.BackgroundColor3 = Color3.fromRGB(20,20,20)
mainFrame.BackgroundTransparency = 0.2
mainFrame.BorderSizePixel = 0
mainFrame.Visible = true
mainFrame.Parent = screenGui
mainFrame.ClipsDescendants = true

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 15)
corner.Parent = mainFrame

--// LED NEON
local borderLED = Instance.new("UIStroke")
borderLED.Parent = mainFrame
borderLED.Color = Color3.fromRGB(255,140,0)
borderLED.Thickness = 4
borderLED.LineJoinMode = Enum.LineJoinMode.Round
borderLED.ApplyStrokeMode = Enum.ApplyStrokeMode.Border

--// TITLE
local title = Instance.new("TextLabel")
title.Name = "Title"
title.Size = UDim2.new(1, 0, 0, 40)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundTransparency = 1
title.Text = "LARANJA SCRIPT" -- Título editável
title.Font = Enum.Font.GothamBold
title.TextSize = 28
title.TextColor3 = borderLED.Color
title.Parent = mainFrame

--// SUBTITLE
local subtitle = Instance.new("TextLabel")
subtitle.Name = "Subtitle"
subtitle.Size = UDim2.new(1,0,0,20)
subtitle.Position = UDim2.new(0,0,0,40)
subtitle.BackgroundTransparency = 1
subtitle.Text = "CATEGORIA" -- Subtítulo editável
subtitle.TextColor3 = Color3.fromRGB(255,255,255)
subtitle.Font = Enum.Font.Gotham
subtitle.TextSize = 16
subtitle.Parent = mainFrame

--// CLOSE BUTTON
local closeButton = Instance.new("TextButton")
closeButton.Name = "CloseButton"
closeButton.Size = UDim2.new(0,35,0,35)
closeButton.Position = UDim2.new(1,-40,0,5)
closeButton.BackgroundColor3 = Color3.fromRGB(220,20,60)
closeButton.Text = "X"
closeButton.TextColor3 = Color3.fromRGB(255,255,255)
closeButton.Font = Enum.Font.GothamBold
closeButton.TextSize = 22
closeButton.AutoButtonColor = true
closeButton.Parent = mainFrame
local closeCorner = Instance.new("UICorner")
closeCorner.CornerRadius = UDim.new(0,8)
closeCorner.Parent = closeButton

closeButton.MouseButton1Click:Connect(function()
    TweenService:Create(mainFrame, TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Position=UDim2.new(0.5,0,0.5,-600)}):Play()
end)

--// CONTENT FRAME
local contentFrame = Instance.new("Frame")
contentFrame.Name = "ContentFrame"
contentFrame.Size = UDim2.new(1, -20, 1, -110)
contentFrame.Position = UDim2.new(0, 10, 0, 70)
contentFrame.BackgroundTransparency = 1
contentFrame.Parent = mainFrame

--// ABAS
local tabs = {"SCRIPT","INFORMAÇÕES"}
local activeTab = nil

--// DADOS DOS SCRIPTS (30 scripts separados)
local scriptData = {}
for i = 1,30 do
    table.insert(scriptData,{
        Name = "Script "..i, -- Nome editável
        Description = "Descrição do Script "..i, -- Descrição editável
        Image = "rbxassetid://12345678", -- Imagem editável
        Link = "https://linkdosescript.com/script"..i, -- Link editável
        Executions = 0
    })
end

-- Função criar abas
local function createTabButton(name,index,totalTabs)
    local btn = Instance.new("TextButton")
    btn.Name = name:gsub(" ","")
    local width = (mainFrame.AbsoluteSize.X - 30) / totalTabs
    btn.Size = UDim2.new(0,width,0,35)
    btn.Position = UDim2.new(0,10 + (index-1)*(width+10),0,65)
    btn.BackgroundColor3 = Color3.fromRGB(100,100,100)
    btn.Text = name
    btn.TextColor3 = Color3.fromRGB(255,255,255)
    btn.Font = Enum.Font.Gotham
    btn.TextSize = 16
    btn.Parent = mainFrame
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0,8)
    corner.Parent = btn

    btn.MouseEnter:Connect(function()
        TweenService:Create(btn,TweenInfo.new(0.2),{BackgroundColor3=Color3.fromRGB(120,120,120)}):Play()
    end)
    btn.MouseLeave:Connect(function()
        if activeTab ~= btn then
            TweenService:Create(btn,TweenInfo.new(0.2),{BackgroundColor3=Color3.fromRGB(100,100,100)}):Play()
        end
    end)

    btn.MouseButton1Click:Connect(function()
        if activeTab then
            TweenService:Create(activeTab,TweenInfo.new(0.2),{BackgroundColor3=Color3.fromRGB(100,100,100)}):Play()
        end
        activeTab = btn
        TweenService:Create(btn,TweenInfo.new(0.2),{BackgroundColor3=Color3.fromRGB(0,120,255)}):Play() -- Azul ativo

        for _, frame in pairs(contentFrame:GetChildren()) do
            frame.Visible = false
        end

        local label = contentFrame:FindFirstChild(name)
        if not label then
            label = Instance.new("Frame")
            label.Name = name
            label.Size = UDim2.new(1,0,1,0)
            label.Position = UDim2.new(0,0,0,0)
            label.BackgroundTransparency = 1
            label.Parent = contentFrame

            if name == "SCRIPT" then
                local scrollFrame = Instance.new("ScrollingFrame")
                scrollFrame.Size = UDim2.new(1,-20,1,-70)
                scrollFrame.Position = UDim2.new(0,10,0,50)
                scrollFrame.BackgroundTransparency = 1
                scrollFrame.BorderSizePixel = 0
                scrollFrame.ScrollBarThickness = 6
                scrollFrame.ScrollBarImageColor3 = Color3.fromRGB(255,255,255)
                scrollFrame.HorizontalScrollBarInset = Enum.ScrollBarInset.None
                scrollFrame.CanvasSize = UDim2.new(0,0,0,0)
                scrollFrame.Parent = label

                local totalHeight = 10
                for _, data in ipairs(scriptData) do
                    -- Bloco separado
                    local block = Instance.new("Frame")
                    block.Size = UDim2.new(1,0,0,100)
                    block.Position = UDim2.new(0,0,0,totalHeight)
                    block.BackgroundColor3 = Color3.fromRGB(50,50,50)
                    block.BorderSizePixel = 0
                    block.Parent = scrollFrame
                    local blockCorner = Instance.new("UICorner")
                    blockCorner.CornerRadius = UDim.new(0,10)
                    blockCorner.Parent = block

                    -- Imagem
                    local img = Instance.new("ImageLabel")
                    img.Size = UDim2.new(0,80,0,80)
                    img.Position = UDim2.new(0,10,0,10)
                    img.Image = data.Image
                    img.BackgroundTransparency = 0
                    local imgCorner = Instance.new("UICorner")
                    imgCorner.CornerRadius = UDim.new(0,40)
                    imgCorner.Parent = img
                    img.Parent = block

                    -- Nome
                    local nameLabel = Instance.new("TextLabel")
                    nameLabel.Size = UDim2.new(0.5,0,0,25)
                    nameLabel.Position = UDim2.new(0,100,0,10)
                    nameLabel.Text = data.Name
                    nameLabel.TextColor3 = Color3.fromRGB(255,255,255)
                    nameLabel.Font = Enum.Font.GothamBold
                    nameLabel.TextSize = 18
                    nameLabel.BackgroundTransparency = 1
                    nameLabel.TextXAlignment = Enum.TextXAlignment.Left
                    nameLabel.Parent = block

                    -- Descrição
                    local descLabel = Instance.new("TextLabel")
                    descLabel.Size = UDim2.new(0.7,0,0,50)
                    descLabel.Position = UDim2.new(0,100,0,35)
                    descLabel.Text = data.Description
                    descLabel.TextColor3 = Color3.fromRGB(200,200,200)
                    descLabel.Font = Enum.Font.Gotham
                    descLabel.TextSize = 14
                    descLabel.TextWrapped = true
                    descLabel.BackgroundTransparency = 1
                    descLabel.TextXAlignment = Enum.TextXAlignment.Left
                    descLabel.Parent = block

                    -- Botão Executar
                    local executeBtn = Instance.new("TextButton")
                    executeBtn.Size = UDim2.new(0,80,0,30)
                    executeBtn.Position = UDim2.new(1,-90,0,35)
                    executeBtn.Text = "Executar"
                    executeBtn.TextColor3 = Color3.fromRGB(255,255,255)
                    executeBtn.BackgroundColor3 = Color3.fromRGB(0,120,255)
                    executeBtn.Font = Enum.Font.GothamBold
                    executeBtn.TextSize = 16
                    local btnCorner = Instance.new("UICorner")
                    btnCorner.CornerRadius = UDim.new(0,6)
                    btnCorner.Parent = executeBtn
                    executeBtn.Parent = block

                    executeBtn.MouseButton1Click:Connect(function()
                        data.Executions = data.Executions + 1
                        print("Executando "..data.Name.." | Total execuções: "..data.Executions)
                        -- Executa script online:
                        -- loadstring(game:HttpGet(data.Link))()
                        TweenService:Create(mainFrame, TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Position=UDim2.new(0.5,0,0.5,-600)}):Play()
                    end)

                    totalHeight = totalHeight + 110 -- Espaço entre os blocos
                end

                scrollFrame.CanvasSize = UDim2.new(0,0,0,totalHeight)
            end

            if name == "INFORMAÇÕES" then
                local infoLabel = Instance.new("TextLabel")
                infoLabel.Size = UDim2.new(1,-20,1,0)
                infoLabel.Position = UDim2.new(0,10,0,10)
                infoLabel.Text = "Informações gerais do script"
                infoLabel.TextColor3 = Color3.fromRGB(255,255,255)
                infoLabel.Font = Enum.Font.Gotham
                infoLabel.TextSize = 16
                infoLabel.TextWrapped = true
                infoLabel.BackgroundTransparency = 1
                infoLabel.Parent = label
            end
        end
        label.Visible = true
    end)
end

for i,name in ipairs(tabs) do
    createTabButton(name,i,#tabs)
end

-- NEON PULSANTE
spawn(function()
    local increasing = true
    local intensity = 0.7
    while mainFrame.Parent do
        if increasing then
            intensity = intensity + 0.01
            if intensity >= 1 then
                intensity = 1
                increasing = false
            end
        else
            intensity = intensity - 0.01
            if intensity <= 0.6 then
                intensity = 0.6
                increasing = true
            end
        end
        local ledColor = Color3.fromRGB(255, math.floor(140*intensity), 0)
        borderLED.Color = ledColor
        title.TextColor3 = ledColor
        RunService.RenderStepped:Wait()
    end
end)
