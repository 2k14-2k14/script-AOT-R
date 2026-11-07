-- ======================================================
-- MENU TĂNG HITBOX GÁY - DÀNH CHO AOT REVOLUTION
-- ======================================================

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Workspace = game:GetService("Workspace")

local LocalPlayer = Players.LocalPlayer

-- Biến cấu hình
local Config = {
    Enabled = false,
    SizeMultiplier = 3, -- Hệ số phóng to (1 = bình thường, 3 = lớn gấp 3 lần)
    ShowVisuals = true, -- Hiển thị hitbox màu xanh để dễ nhìn
}

-- Hàm tìm và phóng to hitbox gáy của Titan
local function expandTitanNape(titan)
    -- Tìm thư mục chứa hitbox của Titan
    local hitboxesFolder = titan:FindFirstChild("Hitboxes")
    if not hitboxesFolder then return end
    
    local hitFolder = hitboxesFolder:FindFirstChild("Hit")
    if not hitFolder then return end

    local nape = hitFolder:FindFirstChild("Nape")
    if nape and nape:IsA("BasePart") then
        -- Lưu kích thước gốc nếu chưa có
        if not nape:GetAttribute("OriginalSize") then
            nape:SetAttribute("OriginalSize", nape.Size)
        end

        if Config.Enabled then
            -- Phóng to hitbox
            local originalSize = nape:GetAttribute("OriginalSize")
            nape.Size = originalSize * Config.SizeMultiplier
            
            -- Tùy chọn hiển thị
            if Config.ShowVisuals then
                nape.Transparency = 0.5
                nape.Color = Color3.new(0, 1, 0) -- Màu xanh lá
                nape.Material = Enum.Material.Neon
            else
                nape.Transparency = 1 -- Ẩn đi
            end
        else
            -- Khôi phục lại kích thước và hình dáng ban đầu
            if nape:GetAttribute("OriginalSize") then
                nape.Size = nape:GetAttribute("OriginalSize")
            end
            nape.Transparency = 1 -- Thường hitbox gốc là vô hình
        end
    end
end

-- Vòng lặp quét qua các Titan trong bản đồ
local function scanForTitans()
    -- Tên thư mục chứa Titan trong game có thể là "Titans" hoặc tương tự
    local titansFolder = Workspace:FindFirstChild("Titans") 
    if not titansFolder then return end

    for _, titan in ipairs(titansFolder:GetChildren()) do
        expandTitanNape(titan)
    end
end

-- Tạo GUI Menu đơn giản
local function createMenu()
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "NapeHitboxMenu"
    ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
    ScreenGui.ResetOnSpawn = false

    local Frame = Instance.new("Frame")
    Frame.Size = UDim2.new(0, 250, 0, 180)
    Frame.Position = UDim2.new(0, 20, 0, 100)
    Frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    Frame.BorderSizePixel = 0
    Frame.Active = true
    Frame.Draggable = true -- Cho phép kéo thả menu
    Frame.Parent = ScreenGui

    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 8)
    UICorner.Parent = Frame

    -- Tiêu đề
    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(1, 0, 0, 30)
    Title.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    Title.Text = "  MENU HITBOX GÁY"
    Title.TextColor3 = Color3.fromRGB(255, 255, 255)
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Font = Enum.Font.SourceSansBold
    Title.TextSize = 16
    Title.Parent = Frame
    
    local TitleCorner = Instance.new("UICorner")
    TitleCorner.CornerRadius = UDim.new(0, 8)
    TitleCorner.Parent = Title

    -- Nút Bật/Tắt
    local ToggleBtn = Instance.new("TextButton")
    ToggleBtn.Size = UDim2.new(0.9, 0, 0, 35)
    ToggleBtn.Position = UDim2.new(0.05, 0, 0, 45)
    ToggleBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50) -- Màu đỏ (Tắt)
    ToggleBtn.Text = "TRẠNG THÁI: TẮT"
    ToggleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    ToggleBtn.Font = Enum.Font.SourceSansBold
    ToggleBtn.TextSize = 14
    ToggleBtn.Parent = Frame
    
    local ToggleCorner = Instance.new("UICorner")
    ToggleCorner.CornerRadius = UDim.new(0, 6)
    ToggleCorner.Parent = ToggleBtn

    -- Thanh trượt kích thước (Slider)
    local SizeLabel = Instance.new("TextLabel")
    SizeLabel.Size = UDim2.new(0.9, 0, 0, 20)
    SizeLabel.Position = UDim2.new(0.05, 0, 0, 90)
    SizeLabel.BackgroundTransparency = 1
    SizeLabel.Text = "Hệ số Hitbox: x" .. Config.SizeMultiplier
    SizeLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    SizeLabel.TextXAlignment = Enum.TextXAlignment.Left
    SizeLabel.Font = Enum.Font.SourceSans
    SizeLabel.TextSize = 14
    SizeLabel.Parent = Frame

    local SizeSlider = Instance.new("TextButton") -- Dùng TextButton để dễ bấm
    SizeSlider.Size = UDim2.new(0.9, 0, 0, 10)
    SizeSlider.Position = UDim2.new(0.05, 0, 0, 115)
    SizeSlider.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
    SizeSlider.Text = ""
    SizeSlider.Parent = Frame
    
    local SliderCorner = Instance.new("UICorner")
    SliderCorner.CornerRadius = UDim.new(0, 5)
    SliderCorner.Parent = SizeSlider

    -- Nút tăng/giảm kích thước
    local MinusBtn = Instance.new("TextButton")
    MinusBtn.Size = UDim2.new(0.4, 0, 0, 25)
    MinusBtn.Position = UDim2.new(0.05, 0, 0, 135)
    MinusBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    MinusBtn.Text = "-"
    MinusBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    MinusBtn.Parent = Frame
    local MinusCorner = Instance.new("UICorner")
    MinusCorner.CornerRadius = UDim.new(0, 6)
    MinusCorner.Parent = MinusBtn

    local PlusBtn = Instance.new("TextButton")
    PlusBtn.Size = UDim2.new(0.4, 0, 0, 25)
    PlusBtn.Position = UDim2.new(0.55, 0, 0, 135)
    PlusBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    PlusBtn.Text = "+"
    PlusBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    PlusBtn.Parent = Frame
    local PlusCorner = Instance.new("UICorner")
    PlusCorner.CornerRadius = UDim.new(0, 6)
    PlusCorner.Parent = PlusBtn

    -- Xử lý sự kiện Bật/Tắt
    ToggleBtn.MouseButton1Click:Connect(function()
        Config.Enabled = not Config.Enabled
        if Config.Enabled then
            ToggleBtn.BackgroundColor3 = Color3.fromRGB(50, 200, 50) -- Màu xanh (Bật)
            ToggleBtn.Text = "TRẠNG THÁI: BẬT"
        else
            ToggleBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50) -- Màu đỏ (Tắt)
            ToggleBtn.Text = "TRẠNG THÁI: TẮT"
        end
        scanForTitans() -- Áp dụng ngay lập tức
    end)

    -- Xử lý sự kiện thay đổi kích thước
    local function updateSize(multiplier)
        Config.SizeMultiplier = math.clamp(multiplier, 1, 10) -- Giới hạn từ 1x đến 10x
        SizeLabel.Text = "Hệ số Hitbox: x" .. Config.SizeMultiplier
        scanForTitans()
    end

    MinusBtn.MouseButton1Click:Connect(function()
        updateSize(Config.SizeMultiplier - 1)
    end)

    PlusBtn.MouseButton1Click:Connect(function()
        updateSize(Config.SizeMultiplier + 1)
    end)
end

-- Khởi tạo
createMenu()
print("Menu Hitbox Gáy đã được tải!")

-- Vòng lặp chính: Cứ mỗi 1 giây sẽ quét lại các Titan một lần
-- để đảm bảo hitbox của các Titan mới xuất hiện cũng được phóng to.
task.spawn(function()
    while task.wait(1) do
        if Config.Enabled then
            scanForTitans()
        end
    end
end)
