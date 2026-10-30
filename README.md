
-- DONO ADMIN V2
-- Recursos locais de demonstração
-- Roblox LuaU

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer

local gui = Instance.new("ScreenGui")
gui.Name = "DonoAdminV2"
gui.ResetOnSpawn = false
gui.Parent = game:GetService("CoreGui")

local function make(class, props, parent)
    local obj = Instance.new(class)
    for k,v in pairs(props) do obj[k] = v end
    obj.Parent = parent
    return obj
end

local login = make("Frame", {
    Size = UDim2.fromOffset(300,190),
    Position = UDim2.new(.5,-150,.5,-95),
    BackgroundColor3 = Color3.fromRGB(18,20,32)
},gui)

make("UICorner",{CornerRadius=UDim.new(0,12)},login)

make("TextLabel",{
    Size=UDim2.new(1,0,0,45),
    BackgroundTransparency=1,
    Text="DONO ADMIN V2",
    TextColor3=Color3.fromRGB(0,220,255),
    TextSize=20,
    Font=Enum.Font.GothamBold
},login)

local user = make("TextBox",{
    Size=UDim2.new(.8,0,0,35),
    Position=UDim2.new(.1,0,0,55),
    PlaceholderText="Usuário",
    Text="",
    TextSize=15
},login)

local pass = make("TextBox",{
    Size=UDim2.new(.8,0,0,35),
    Position=UDim2.new(.1,0,0,100),
    PlaceholderText="Senha",
    Text="",
    TextSize=15
},login)

local enter = make("TextButton",{
    Size=UDim2.new(.8,0,0,35),
    Position=UDim2.new(.1,0,0,145),
    Text="ENTRAR",
    BackgroundColor3=Color3.fromRGB(0,130,190),
    TextColor3=Color3.new(1,1,1)
},login)

local function getHumanoid()
    local char=player.Character
    return char and char:FindFirstChildOfClass("Humanoid")
end

local function getRoot()
    local char=player.Character
    return char and char:FindFirstChild("HumanoidRootPart")
end

local function openPanel()
    login:Destroy()

    local panel=make("Frame",{
        Size=UDim2.fromOffset(300,390),
        Position=UDim2.new(0,25,.2,0),
        BackgroundColor3=Color3.fromRGB(18,20,32)
    },gui)

    make("UICorner",{CornerRadius=UDim.new(0,12)},panel)

    make("TextLabel",{
        Size=UDim2.new(1,0,0,45),
        Text="DONO ADMIN",
        TextColor3=Color3.fromRGB(0,220,255),
        BackgroundTransparency=1,
        TextSize=21,
        Font=Enum.Font.GothamBold
    },panel)

    local scroll=make("ScrollingFrame",{
        Size=UDim2.new(1,-15,1,-55),
        Position=UDim2.fromOffset(7,48),
        BackgroundTransparency=1,
        BorderSizePixel=0,
        CanvasSize=UDim2.new(0,0,0,650),
        ScrollBarThickness=5
    },panel)

    local function button(label,y,callback)
        local b=make("TextButton",{
            Size=UDim2.new(1,-10,0,36),
            Position=UDim2.fromOffset(5,y),
            Text=label,
            TextSize=14,
            TextColor3=Color3.new(1,1,1),
            BackgroundColor3=Color3.fromRGB(40,55,80),
            Font=Enum.Font.GothamBold
        },scroll)
        make("UICorner",{CornerRadius=UDim.new(0,7)},b)
        b.MouseButton1Click:Connect(callback)
    end

    local speed=false
    button("SPEED ON/OFF",5,function()
        local h=getHumanoid()
        if h then
            speed=not speed
            h.WalkSpeed=speed and 50 or 16
        end
    end)

    local jump=false
    button("INFINITE JUMP ON/OFF",47,function()
        jump=not jump
    end)

    UIS.JumpRequest:Connect(function()
        if jump then
            local h=getHumanoid()
            if h then h:ChangeState(Enum.HumanoidStateType.Jumping) end
        end
    end)

    local noclip=false
    local noclipConnection

    button("NOCLIP ON/OFF",89,function()
        noclip=not noclip
        if noclip and not noclipConnection then
            noclipConnection=RunService.Stepped:Connect(function()
                if not noclip then return end
                local char=player.Character
                if char then
                    for _,part in ipairs(char:GetDescendants()) do
                        if part:IsA("BasePart") then
                            part.CanCollide=false
                        end
                    end
                end
            end)
        elseif not noclip and noclipConnection then
            noclipConnection:Disconnect()
            noclipConnection=nil
            local char=player.Character
            if char then
                for _,part in ipairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide=true
                    end
                end
            end
        end
    end)

    local antiFling=false
    button("ANTI-FLING ON/OFF",131,function()
        antiFling=not antiFling
        local h=getHumanoid()
        if h then
            h:SetStateEnabled(
                Enum.HumanoidStateType.FallingDown,
                not antiFling
            )
            h:SetStateEnabled(
                Enum.HumanoidStateType.Ragdoll,
                not antiFling
            )
        end
    end)

    local esp=false
    local highlights={}

    button("ESP ON/OFF",173,function()
        esp=not esp
        for _,p in ipairs(Players:GetPlayers()) do
            if p~=player then
                if esp then
                    local char=p.Character
                    if char then
                        local hl=Instance.new("Highlight")
                        hl.FillColor=Color3.fromRGB(255,50,50)
                        hl.Adornee=char
                        hl.Parent=gui
                        highlights[p]=hl
                    end
                elseif highlights[p] then
                    highlights[p]:Destroy()
                    highlights[p]=nil
                end
            end
        end
    end)

    button("ANTI-AFK ON/OFF (STATUS)",215,function()
        local status=make("TextLabel",{
            Size=UDim2.new(1,-10,0,30),
            Position=UDim2.fromOffset(5,260),
            Text="Anti-AFK requer teste no jogo.",
            TextColor3=Color3.new(1,1,1),
            BackgroundTransparency=1
        },scroll)
    end)

    button("AIMBOT",257,function()
        warn("Mira automática não incluída nesta versão.")
    end)

    button("TP TOOL",299,function()
        local mouse=player:GetMouse()
        local tool=Instance.new("Tool")
        tool.Name="TP Tool"
        tool.RequiresHandle=false
        tool.Parent=player.Backpack

        tool.Activated:Connect(function()
            local root=getRoot()
            if root and mouse.Hit then
                root.CFrame=mouse.Hit+Vector3.new(0,4,0)
            end
        end)
    end)

    button("JOGADORES / SELEÇÃO",341,function()
        for _,p in ipairs(Players:GetPlayers()) do
            if p~=player then
                print("Jogador disponível:",p.Name)
            end
        end
    end)
end

enter.MouseButton1Click:Connect(function()
    if user.Text=="dono" and pass.Text=="244466666" then
        openPanel()
    else
        pass.Text=""
        pass.PlaceholderText="Login incorreto"
    end
end)
