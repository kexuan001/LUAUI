-- [[ Neko UI Library | Version 2.1x FULL ]] --
-- 這是開源庫主體，請將其託管於 GitHub 後使用 loadstring 調用

local library = {}

local services = setmetatable({}, { __index = function(_, k) return game:GetService(k) end })
local lplr = services.Players.LocalPlayer
local ts = services.TweenService
local uis = services.UserInputService

-- 確保遊戲加載
if not game:IsLoaded() then game.Loaded:Wait() end

-- 自動尋找父級 (優先 CoreGui)
local targetParent = nil
local s, e = pcall(function() targetParent = services.CoreGui end)
if not s then targetParent = lplr:WaitForChild("PlayerGui") end

-- 全局鎖定狀態 (防止 UI 拖拽與 Slider 衝突)
local _G_DraggingUI = false
local _G_Interacting = false 

-- 配色與主題定義
local C = {
    Accent     = Color3.fromRGB(255, 105, 180),
    MainBG     = Color3.fromRGB(12, 8, 12),
    SideBG     = Color3.fromRGB(18, 12, 18),
    BtnBG      = Color3.fromRGB(45, 30, 45),
    Text       = Color3.fromRGB(240, 200, 220), 
    TextDim    = Color3.fromRGB(130, 90, 110),
    TextHi     = Color3.fromRGB(255, 255, 255),
    Stroke     = Color3.fromRGB(255, 130, 190),
    Success    = Color3.fromRGB(0, 255, 150),
    Error      = Color3.fromRGB(255, 80, 80),
}

-- [ 視覺工具函數 ]
local function Tween(obj, info, prop)
    local t = ts:Create(obj, TweenInfo.new(unpack(info)), prop)
    t:Play()
    return t
end

local function CreateParticle(parent, config)
    local p = Instance.new("Frame")
    p.BackgroundColor3 = config.Color
    p.BorderSizePixel = 0
    p.Size = UDim2.new(0, config.Size, 0, config.Size)
    p.Position = config.StartPos
    p.Parent = parent
    p.ZIndex = parent.ZIndex
    Instance.new("UICorner", p).CornerRadius = UDim.new(1, 0)
    if config.Glow then
        local st = Instance.new("UIStroke", p)
        st.Color = config.Color
        st.Thickness = 1.2
        st.Transparency = 0.7
    end
    local t = ts:Create(p, TweenInfo.new(config.Time, Enum.EasingStyle.Linear), {
        Position = config.EndPos,
        BackgroundTransparency = 1
    })
    t:Play()
    t.Completed:Connect(function() p:Destroy() end)
end

-- [ 核心窗口邏輯 ]
function library.new(name)
    local oldUI = targetParent:FindFirstChild("Aether_Xeno_V2")
    if oldUI then oldUI:Destroy() end

    local sg = Instance.new("ScreenGui")
    sg.Name = "Aether_Xeno_V2"
    sg.Parent = targetParent
    sg.ResetOnSpawn = false
    sg.DisplayOrder = 100
    sg.IgnoreGuiInset = true

    local Main = Instance.new("Frame", sg)
    Main.Size = UDim2.new(0, 660, 0, 385)
    Main.Position = UDim2.new(0.5, -330, 0.5, -192)
    Main.BackgroundColor3 = C.MainBG
    Main.BackgroundTransparency = 0.1
    Main.ClipsDescendants = true
    Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 14)

    local MainStroke = Instance.new("UIStroke", Main)
    MainStroke.Thickness = 2
    MainStroke.Color = C.Stroke
    MainStroke.Transparency = 0.5

    -- 🌌 背景垂直粒子系統
    local BgParticles = Instance.new("Frame", Main)
    BgParticles.Size = UDim2.new(1, 0, 1, 0)
    BgParticles.BackgroundTransparency = 1
    BgParticles.ZIndex = 1

    task.spawn(function()
        local r = Random.new()
        while task.wait(0.08) do
            if not Main or not Main.Parent then break end
            local posX = r:NextNumber(0, 1)
            CreateParticle(BgParticles, {
                Color = Color3.new(1, 1, 1),
                Size = r:NextNumber(1.2, 2.2),
                StartPos = UDim2.new(posX, 0, -0.05, 0),
                EndPos = UDim2.new(posX, 0, 1.05, 0),
                Time = r:NextNumber(3, 5),
                Glow = true
            })
        end
    end)

    local Content = Instance.new("Frame", Main)
    Content.Size = UDim2.new(1, 0, 1, 0)
    Content.BackgroundTransparency = 1
    Content.ZIndex = 2

    local Side = Instance.new("Frame", Content)
    Side.Size = UDim2.new(0, 176, 1, 0)
    Side.BackgroundColor3 = C.SideBG
    Instance.new("UICorner", Side).CornerRadius = UDim.new(0, 14)

    local Title = Instance.new("TextLabel", Side)
    Title.Size = UDim2.new(1, 0, 0, 40)
    Title.Position = UDim2.new(0, 0, 0, 18)
    Title.Text = "🌸 " .. name:upper()
    Title.TextColor3 = C.Accent
    Title.Font = Enum.Font.GothamBlack
    Title.TextSize = 20
    Title.BackgroundTransparency = 1

    local SubTitle = Instance.new("TextLabel", Side)
    SubTitle.Size = UDim2.new(1, 0, 0, 20)
    SubTitle.Position = UDim2.new(0, 0, 0, 52)
    SubTitle.Text = "by kexuan&neko"
    SubTitle.TextColor3 = C.TextDim
    SubTitle.Font = Enum.Font.GothamBold
    SubTitle.TextSize = 11
    SubTitle.BackgroundTransparency = 1

    local TabContainer = Instance.new("ScrollingFrame", Side)
    TabContainer.Size = UDim2.new(1, -20, 1, -100)
    TabContainer.Position = UDim2.new(0, 10, 0, 90)
    TabContainer.BackgroundTransparency = 1
    TabContainer.ScrollBarThickness = 0
    local tl = Instance.new("UIListLayout", TabContainer)
    tl.Padding = UDim.new(0, 7)
    tl:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
        TabContainer.CanvasSize = UDim2.new(0, 0, 0, tl.AbsoluteContentSize.Y)
    end)

    local Pages = Instance.new("Frame", Content)
    Pages.Size = UDim2.new(1, -200, 1, -30)
    Pages.Position = UDim2.new(0, 190, 0, 15)
    Pages.BackgroundTransparency = 1

    local window = {current = nil}

    -- [ 創建分頁 ]
    function window:Tab(name)
        local Page = Instance.new("ScrollingFrame", Pages)
        Page.Size = UDim2.new(1, 0, 1, 0)
        Page.ScrollBarThickness = 2
        Page.ScrollBarImageColor3 = C.Accent
        Page.BackgroundTransparency = 1
        Page.Visible = false
        local pl = Instance.new("UIListLayout", Page)
        pl.Padding = UDim.new(0, 12)
        pl:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
            Page.CanvasSize = UDim2.new(0, 0, 0, pl.AbsoluteContentSize.Y + 10)
        end)

        local Btn = Instance.new("TextButton", TabContainer)
        Btn.Size = UDim2.new(1, 0, 0, 40)
        Btn.BackgroundColor3 = C.BtnBG
        Btn.BackgroundTransparency = 0.7
        Btn.Text = "  " .. name
        Btn.TextColor3 = C.TextDim
        Btn.Font = Enum.Font.GothamBold
        Btn.TextSize = 14
        Btn.TextXAlignment = "Left"
        Instance.new("UICorner", Btn).CornerRadius = UDim.new(0, 8)

        Btn.MouseButton1Click:Connect(function()
            if window.current then
                window.current.Page.Visible = false
                Tween(window.current.Btn, {0.3}, {TextColor3 = C.TextDim, BackgroundTransparency = 0.7})
            end
            Page.Visible = true
            Tween(Btn, {0.3}, {TextColor3 = C.Accent, BackgroundTransparency = 0.3})
            window.current = {Page = Page, Btn = Btn}
        end)

        if not window.current then
            Page.Visible = true
            Btn.TextColor3 = C.Accent
            Btn.BackgroundTransparency = 0.3
            window.current = {Page = Page, Btn = Btn}
        end

        local tab = {}

        -- [ 創建區段 ]
        function tab:Section(txt)
            local Sec = Instance.new("Frame", Page)
            Sec.Size = UDim2.new(1, -10, 0, 40)
            Sec.BackgroundColor3 = C.BtnBG
            Sec.BackgroundTransparency = 0.9
            Instance.new("UICorner", Sec).CornerRadius = UDim.new(0, 10)
            
            local T = Instance.new("TextLabel", Sec)
            T.Size = UDim2.new(1, -20, 0, 30)
            T.Position = UDim2.new(0, 10, 0, 5)
            T.Text = txt:upper()
            T.TextColor3 = C.Accent
            T.Font = Enum.Font.GothamBlack
            T.TextSize = 13
            T.BackgroundTransparency = 1
            T.TextXAlignment = "Left"

            local Box = Instance.new("Frame", Sec)
            Box.Size = UDim2.new(1, -20, 0, 0)
            Box.Position = UDim2.new(0, 10, 0, 35)
            Box.BackgroundTransparency = 1
            local bl = Instance.new("UIListLayout", Box)
            bl.Padding = UDim.new(0, 8)
            bl:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
                Sec.Size = UDim2.new(1, -10, 0, bl.AbsoluteContentSize.Y + 45)
            end)

            local func = {}
            
            function func:Toggle(txt, default, callback)
                local enabled = default or false
                local TFrame = Instance.new("TextButton", Box)
                TFrame.Size = UDim2.new(1, 0, 0, 38)
                TFrame.BackgroundColor3 = C.BtnBG
                TFrame.BackgroundTransparency = 0.5
                TFrame.Text = ""
                Instance.new("UICorner", TFrame).CornerRadius = UDim.new(0, 6)
                
                local Lbl = Instance.new("TextLabel", TFrame)
                Lbl.Size = UDim2.new(1, -50, 1, 0)
                Lbl.Position = UDim2.new(0, 12, 0, 0)
                Lbl.Text = txt
                Lbl.TextColor3 = C.Text
                Lbl.Font = Enum.Font.GothamMedium
                Lbl.TextSize = 13
                Lbl.BackgroundTransparency = 1
                Lbl.TextXAlignment = "Left"

                local Indicator = Instance.new("Frame", TFrame)
                Indicator.Size = UDim2.new(0, 32, 0, 18)
                Indicator.Position = UDim2.new(1, -44, 0.5, -9)
                Indicator.BackgroundColor3 = enabled and C.Accent or Color3.fromRGB(80, 60, 80)
                Instance.new("UICorner", Indicator).CornerRadius = UDim.new(1, 0)

                local Dot = Instance.new("Frame", Indicator)
                Dot.Size = UDim2.new(0, 12, 0, 12)
                Dot.Position = enabled and UDim2.new(1, -15, 0.5, -6) or UDim2.new(0, 3, 0.5, -6)
                Dot.BackgroundColor3 = Color3.new(1, 1, 1)
                Instance.new("UICorner", Dot).CornerRadius = UDim.new(1, 0)

                TFrame.MouseButton1Click:Connect(function()
                    enabled = not enabled
                    Tween(Indicator, {0.2}, {BackgroundColor3 = enabled and C.Accent or Color3.fromRGB(80, 60, 80)})
                    Tween(Dot, {0.2, Enum.EasingStyle.Back}, {Position = enabled and UDim2.new(1, -15, 0.5, -6) or UDim2.new(0, 3, 0.5, -6)})
                    pcall(callback, enabled)
                end)
            end

            function func:Slider(txt, min, max, default, callback)
                local SFrame = Instance.new("Frame", Box)
                SFrame.Size = UDim2.new(1, 0, 0, 45)
                SFrame.BackgroundColor3 = C.BtnBG
                SFrame.BackgroundTransparency = 0.5
                Instance.new("UICorner", SFrame).CornerRadius = UDim.new(0, 6)

                local Lbl = Instance.new("TextLabel", SFrame)
                Lbl.Size = UDim2.new(1, -20, 0, 25)
                Lbl.Position = UDim2.new(0, 12, 0, 2)
                Lbl.Text = txt
                Lbl.TextColor3 = C.Text
                Lbl.Font = Enum.Font.GothamMedium
                Lbl.TextSize = 13
                Lbl.BackgroundTransparency = 1
                Lbl.TextXAlignment = "Left"

                local ValLbl = Instance.new("TextLabel", SFrame)
                ValLbl.Size = UDim2.new(0, 40, 0, 25)
                ValLbl.Position = UDim2.new(1, -52, 0, 2)
                ValLbl.Text = tostring(default)
                ValLbl.TextColor3 = C.Accent
                ValLbl.Font = Enum.Font.GothamBold
                ValLbl.TextSize = 13
                ValLbl.BackgroundTransparency = 1
                ValLbl.TextXAlignment = "Right"

                local BarBG = Instance.new("Frame", SFrame)
                BarBG.Size = UDim2.new(1, -24, 0, 4)
                BarBG.Position = UDim2.new(0, 12, 0, 32)
                BarBG.BackgroundColor3 = Color3.fromRGB(60, 45, 60)
                Instance.new("UICorner", BarBG).CornerRadius = UDim.new(1, 0)

                local Bar = Instance.new("Frame", BarBG)
                Bar.Size = UDim2.new((default - min)/(max - min), 0, 1, 0)
                Bar.BackgroundColor3 = C.Accent
                Instance.new("UICorner", Bar).CornerRadius = UDim.new(1, 0)

                local sliding = false
                local function move(input)
                    local pos = math.clamp((input.Position.X - BarBG.AbsolutePosition.X) / BarBG.AbsoluteSize.X, 0, 1)
                    local val = math.floor(min + (max - min) * pos)
                    Bar.Size = UDim2.new(pos, 0, 1, 0)
                    ValLbl.Text = tostring(val)
                    pcall(callback, val)
                end

                SFrame.InputBegan:Connect(function(input)
                    if input.UserInputType == Enum.UserInputType.MouseButton1 then 
                        sliding = true 
                        _G_Interacting = true 
                        move(input) 
                    end
                end)
                uis.InputChanged:Connect(function(input)
                    if sliding and input.UserInputType == Enum.UserInputType.MouseMovement then move(input) end
                end)
                uis.InputEnded:Connect(function(input)
                    if input.UserInputType == Enum.UserInputType.MouseButton1 then 
                        sliding = false 
                        _G_Interacting = false 
                    end
                end)
            end

            function func:Button(txt, callback)
                local B = Instance.new("TextButton", Box)
                B.Size = UDim2.new(1, 0, 0, 38)
                B.BackgroundColor3 = C.BtnBG
                B.BackgroundTransparency = 0.5
                B.Text = txt
                B.TextColor3 = C.TextHi
                B.Font = Enum.Font.GothamMedium
                B.TextSize = 13
                B.ClipsDescendants = true
                Instance.new("UICorner", B).CornerRadius = UDim.new(0, 6)
                
                -- 按鈕粒子特效
                task.spawn(function()
                    local br = Random.new()
                    while task.wait(0.04) do 
                        if not B or not B.Parent then break end
                        CreateParticle(B, {
                            Color = Color3.fromRGB(255, 180, 220),
                            Size = br:NextNumber(1.3, 2.8),
                            StartPos = UDim2.new(-0.1, 0, br:NextNumber(), 0),
                            EndPos = UDim2.new(1.1, 0, br:NextNumber(), 0),
                            Time = br:NextNumber(0.8, 1.5),
                            Glow = false
                        })
                    end
                end)
                B.MouseButton1Click:Connect(function() pcall(callback) end)
            end

            function func:Status(label, default)
                local SFrame = Instance.new("Frame", Box)
                SFrame.Size = UDim2.new(1, 0, 0, 38)
                SFrame.BackgroundColor3 = C.BtnBG
                SFrame.BackgroundTransparency = 0.5
                Instance.new("UICorner", SFrame).CornerRadius = UDim.new(0, 6)
                local SText = Instance.new("TextLabel", SFrame)
                SText.Size = UDim2.new(1, -20, 1, 0)
                SText.Position = UDim2.new(0, 10, 0, 0)
                SText.BackgroundTransparency = 1
                SText.Font = Enum.Font.GothamMedium
                SText.TextSize = 13
                SText.TextColor3 = default and C.Success or C.Error
                SText.Text = label .. (default and ": 🟢 開啟中" or ": 🔴 已關閉")
                SText.TextXAlignment = "Left"
                return { Set = function(v) 
                    SText.Text = label .. (v and ": 🟢 開啟中" or ": 🔴 已關閉")
                    SText.TextColor3 = v and C.Success or C.Error
                end }
            end

            return func
        end
        return tab
    end

    -- [ 拖拽系統 ]
    local dragStart, startPos
    Main.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 and not _G_Interacting then
            _G_DraggingUI = true
            dragStart = input.Position
            startPos = Main.Position
        end
    end)
    uis.InputChanged:Connect(function(input)
        if _G_DraggingUI and input.UserInputType == Enum.UserInputType.MouseMovement then
            local delta = input.Position - dragStart
            Main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
    uis.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then _G_DraggingUI = false end
    end)

    return window
end

return library
