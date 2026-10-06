--[[
    ═══════════════════════════════════════════════════════
    🎯 AIMBOT HUB v9  •  PURPLE EDITION  •  CLEAN
    - Aimbot normal (BindToRenderStep, câmera blindada) [E]
    - ESP Box / Line / Name / Health
    - ESP Customizável (cor, espessura, texto, estilo, distância)
    - FOV Circle Roxo
    - Panic key [F]
    - SEM silent aim, SEM hook de metatable, SEM nada suspeito
    ═══════════════════════════════════════════════════════
]]

-- ==================== SERVIÇOS ====================
local Players          = game:GetService("Players")
local RunService       = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService     = game:GetService("TweenService")
local Workspace        = game:GetService("Workspace")

local LP     = Players.LocalPlayer
local Camera = Workspace.CurrentCamera

-- ==================== CONFIG ====================
local CFG = {
    Aimbot=false, ShowFOV=true, TeamCheck=false, WallCheck=false,
    ESPBox=false, ESPLine=false, ESPName=false, ESPHealth=false,
    FOV=200, Smooth=0.25, MaxDist=1500, AimPart="Head",
    Key=Enum.KeyCode.E, PanicKey=Enum.KeyCode.F,
    Color=Color3.fromRGB(170,90,255),
}

local ESPCfg = {
    ColorMode="Roxo", LineThickness=1.5, TextSize=13,
    BoxStyle="Caixa", CornerSize=6, ShowDistance=false, TeamColor=false,
}

local ColorPresets = {
    Roxo=Color3.fromRGB(170,90,255), Vermelho=Color3.fromRGB(255,80,80),
    Verde=Color3.fromRGB(100,255,130), Azul=Color3.fromRGB(90,160,255),
    Branco=Color3.fromRGB(255,255,255), Amarelo=Color3.fromRGB(255,220,90),
}
local RainbowHue = 0

local T = {
    Bg=Color3.fromRGB(18,12,28), Panel=Color3.fromRGB(28,18,45),
    Btn=Color3.fromRGB(45,25,70), BtnHov=Color3.fromRGB(70,40,110),
    Acc=Color3.fromRGB(170,90,255), AccB=Color3.fromRGB(210,140,255),
    Text=Color3.fromRGB(235,220,255), Green=Color3.fromRGB(140,255,180),
    Red=Color3.fromRGB(255,90,120),
}

-- ==================== ESTADO ====================
local aiming = false

-- ==================== GUI ====================
local SG=Instance.new("ScreenGui")
SG.Name="AimbotHubV9"; SG.ResetOnSpawn=false; SG.IgnoreGuiInset=true
SG.ZIndexBehavior=Enum.ZIndexBehavior.Sibling
SG.Parent=LP:WaitForChild("PlayerGui")

local OpenBtn=Instance.new("TextButton")
OpenBtn.Size=UDim2.new(0,55,0,55); OpenBtn.Position=UDim2.new(0,20,0,200)
OpenBtn.BackgroundColor3=T.Panel; OpenBtn.Text="🎯"; OpenBtn.TextSize=26
OpenBtn.TextColor3=T.AccB; OpenBtn.BorderSizePixel=0; OpenBtn.ZIndex=100
OpenBtn.Parent=SG
Instance.new("UICorner",OpenBtn).CornerRadius=UDim.new(1,0)
local OS=Instance.new("UIStroke",OpenBtn); OS.Color=T.Acc; OS.Thickness=2

local Frame=Instance.new("Frame")
Frame.Size=UDim2.new(0,430,0,480); Frame.Position=UDim2.new(0.5,-215,0.5,-240)
Frame.BackgroundColor3=T.Bg; Frame.BorderSizePixel=0; Frame.Visible=false
Frame.ZIndex=100; Frame.Parent=SG
Instance.new("UICorner",Frame).CornerRadius=UDim.new(0,12)
local FS=Instance.new("UIStroke",Frame); FS.Color=T.Acc; FS.Thickness=2

local TB=Instance.new("Frame")
TB.Size=UDim2.new(1,0,0,42); TB.BackgroundColor3=T.Panel; TB.BorderSizePixel=0
TB.ZIndex=101; TB.Parent=Frame
Instance.new("UICorner",TB).CornerRadius=UDim.new(0,12)
local Mask=Instance.new("Frame")
Mask.Size=UDim2.new(1,0,0,12); Mask.Position=UDim2.new(0,0,1,-12)
Mask.BackgroundColor3=T.Panel; Mask.BorderSizePixel=0; Mask.ZIndex=102; Mask.Parent=TB
local TL=Instance.new("TextLabel")
TL.Size=UDim2.new(1,-90,1,0); TL.Position=UDim2.new(0,15,0,0); TL.BackgroundTransparency=1
TL.Text="🎯 AIMBOT HUB v9"; TL.TextColor3=T.AccB; TL.TextSize=17
TL.Font=Enum.Font.GothamBold; TL.TextXAlignment=Enum.TextXAlignment.Left
TL.ZIndex=103; TL.Parent=TB
local CB=Instance.new("TextButton")
CB.Size=UDim2.new(0,28,0,28); CB.Position=UDim2.new(1,-36,0,7)
CB.BackgroundColor3=T.Red; CB.Text="✕"; CB.TextColor3=Color3.new(1,1,1)
CB.TextSize=16; CB.Font=Enum.Font.GothamBold; CB.BorderSizePixel=0
CB.ZIndex=103; CB.Parent=TB
Instance.new("UICorner",CB).CornerRadius=UDim.new(0,6)

local Sc=Instance.new("ScrollingFrame")
Sc.Size=UDim2.new(1,-20,1,-60); Sc.Position=UDim2.new(0,10,0,52)
Sc.BackgroundTransparency=1; Sc.BorderSizePixel=0; Sc.ScrollBarThickness=4
Sc.ScrollBarImageColor3=T.Acc; Sc.CanvasSize=UDim2.new(0,0,0,0)
Sc.ZIndex=101; Sc.Parent=Frame
local LL=Instance.new("UIListLayout",Sc)
LL.Padding=UDim.new(0,6); LL.SortOrder=Enum.SortOrder.LayoutOrder

-- ==================== HELPERS ====================
local function MakeBtn(txt,cor,fn)
    local b=Instance.new("TextButton")
    b.Size=UDim2.new(1,-10,0,34); b.BackgroundColor3=cor or T.Btn
    b.Text="  "..txt; b.TextColor3=T.Text; b.TextSize=14
    b.Font=Enum.Font.GothamMedium; b.TextXAlignment=Enum.TextXAlignment.Left
    b.BorderSizePixel=0; b.ZIndex=102; b.Parent=Sc
    Instance.new("UICorner",b).CornerRadius=UDim.new(0,7)
    local s=Instance.new("UIStroke",b); s.Color=T.Acc; s.Thickness=1; s.Transparency=0.6
    b.MouseEnter:Connect(function()
        TweenService:Create(b,TweenInfo.new(0.15),{BackgroundColor3=T.BtnHov}):Play()
    end)
    b.MouseLeave:Connect(function()
        TweenService:Create(b,TweenInfo.new(0.15),{BackgroundColor3=cor or T.Btn}):Play()
    end)
    b.MouseButton1Click:Connect(function() fn(b) end)
    return b
end

local function MakeToggle(txt,tbl,key)
    local b=MakeBtn("🔴 "..txt,nil,function(btn)
        tbl[key]=not tbl[key]
        local on=tbl[key]
        btn.Text=(on and "  🟢 " or "  🔴 ")..txt
        btn.TextColor3=on and T.Green or T.Text
    end)
    if tbl[key] then
        b.Text="  🟢 "..txt; b.TextColor3=T.Green
    end
    return b
end

local function Section(txt)
    local l=Instance.new("TextLabel")
    l.Size=UDim2.new(1,-10,0,26); l.BackgroundTransparency=1
    l.Text="  ── "..txt.." ──"; l.TextColor3=T.Acc; l.TextSize=13
    l.Font=Enum.Font.GothamBold; l.TextXAlignment=Enum.TextXAlignment.Left
    l.ZIndex=102; l.Parent=Sc
end

-- ==================== FOV CIRCLE ====================
local FovC=Instance.new("Frame")
FovC.AnchorPoint=Vector2.new(0.5,0.5)
FovC.Position=UDim2.new(0.5,0,0.5,0)
FovC.Size=UDim2.new(0,CFG.FOV*2,0,CFG.FOV*2)
FovC.BackgroundTransparency=1; FovC.BorderSizePixel=0
FovC.ZIndex=5; FovC.Visible=false; FovC.Parent=SG
Instance.new("UICorner",FovC).CornerRadius=UDim.new(1,0)
local FovS=Instance.new("UIStroke",FovC)
FovS.Color=T.Acc; FovS.Thickness=2; FovS.Transparency=0.3

-- ==================== UTIL ====================
local function ValidTarget(plr)
    if plr==LP then return false end
    if CFG.TeamCheck and plr.Team==LP.Team then return false end
    local c=plr.Character
    if not c then return false end
    local h=c:FindFirstChildOfClass("Humanoid")
    if not h or h.Health<=0 then return false end
    return c:FindFirstChild(CFG.AimPart) ~= nil
end

local function InFov(pos, radius)
    local sp,on=Camera:WorldToViewportPoint(pos)
    if not on then return false,math.huge end
    local ctr=Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)
    local d=(Vector2.new(sp.X,sp.Y)-ctr).Magnitude
    return d<=radius, d
end

local function IsVisible(targetPart)
    if not CFG.WallCheck then return true end
    local o=Camera.CFrame.Position
    local params=RaycastParams.new()
    params.FilterType=Enum.RaycastFilterType.Exclude
    params.FilterDescendantsInstances={LP.Character, Camera}
    local r=Workspace:Raycast(o, targetPart.Position-o, params)
    if r then return r.Instance:IsDescendantOf(targetPart.Parent) end
    return true
end

-- ==================== SEÇÕES ====================
Section("🎯 AIMBOT")
MakeToggle("Aimbot (E)", CFG, "Aimbot")
MakeToggle("Mostrar FOV", CFG, "ShowFOV")
MakeToggle("Team Check", CFG, "TeamCheck")
MakeToggle("Wall Check", CFG, "WallCheck")
MakeBtn("🎯 FOV: 200",nil,function(b)
    CFG.FOV=CFG.FOV+50; if CFG.FOV>600 then CFG.FOV=100 end
    b.Text="  🎯 FOV: "..CFG.FOV
end)
MakeBtn("💧 Smooth: 0.25",nil,function(b)
    CFG.Smooth=CFG.Smooth+0.05
    if CFG.Smooth>1.0 then CFG.Smooth=0.05 end
    b.Text="  💧 Smooth: "..string.format("%.2f",CFG.Smooth)
end)
MakeBtn("📏 Dist: 1500",nil,function(b)
    CFG.MaxDist=CFG.MaxDist+500; if CFG.MaxDist>5000 then CFG.MaxDist=500 end
    b.Text="  📏 Dist: "..CFG.MaxDist
end)
MakeBtn("🎯 Mira: Head",nil,function(b)
    local list={"Head","HumanoidRootPart","UpperTorso"}
    local i=table.find(list,CFG.AimPart) or 1
    i=i%#list+1
    CFG.AimPart=list[i]
    b.Text="  🎯 Mira: "..CFG.AimPart
end)

Section("👁️ ESP")
MakeToggle("ESP Box", CFG, "ESPBox")
MakeToggle("ESP Line", CFG, "ESPLine")
MakeToggle("ESP Nome", CFG, "ESPName")
MakeToggle("ESP Vida", CFG, "ESPHealth")

Section("🎨 ESP CUSTOM")
MakeBtn("🎨 Cor ESP: Roxo",nil,function(b)
    local list={"Roxo","Vermelho","Verde","Azul","Branco","Amarelo","Rainbow"}
    local i=table.find(list,ESPCfg.ColorMode) or 1
    i=i%#list+1
    ESPCfg.ColorMode=list[i]
    b.Text="  🎨 Cor ESP: "..ESPCfg.ColorMode
end)
MakeBtn("📏 Espessura: 1.5",nil,function(b)
    ESPCfg.LineThickness=ESPCfg.LineThickness+0.5
    if ESPCfg.LineThickness>5 then ESPCfg.LineThickness=0.5 end
    b.Text="  📏 Espessura: "..string.format("%.1f",ESPCfg.LineThickness)
end)
MakeBtn("🔤 Texto: 13",nil,function(b)
    ESPCfg.TextSize=ESPCfg.TextSize+2
    if ESPCfg.TextSize>24 then ESPCfg.TextSize=9 end
    b.Text="  🔤 Texto: "..ESPCfg.TextSize
end)
MakeBtn("⬜ Estilo: Caixa",nil,function(b)
    ESPCfg.BoxStyle=(ESPCfg.BoxStyle=="Caixa") and "Cantos" or "Caixa"
    b.Text="  ⬜ Estilo: "..ESPCfg.BoxStyle
end)
MakeBtn("📐 Canto: 6px",nil,function(b)
    ESPCfg.CornerSize=ESPCfg.CornerSize+2
    if ESPCfg.CornerSize>20 then ESPCfg.CornerSize=4 end
    b.Text="  📐 Canto: "..ESPCfg.CornerSize.."px"
end)
MakeToggle("Mostrar Distância", ESPCfg, "ShowDistance")
MakeToggle("Cor do Time", ESPCfg, "TeamColor")

Section("⚙️ SISTEMA")
MakeBtn("🛑 Destravar Câmera (F)",T.Red,function(b)
    CFG.Aimbot=false
    aiming=false
    b.Text="  ✅ Destravado"
    task.wait(1)
    b.Text="  🛑 Destravar Câmera (F)"
end)

-- ==================== ESP RENDERER ====================
local ESP={}
local function BuildESP(plr)
    if plr==LP or ESP[plr] then return end
    local d={}
    d.box=Instance.new("Frame",SG)
    d.box.BackgroundTransparency=1; d.box.BorderSizePixel=0
    d.box.Visible=false; d.box.ZIndex=3
    Instance.new("UICorner",d.box).CornerRadius=UDim.new(0,3)
    d.stroke=Instance.new("UIStroke",d.box)
    d.stroke.Color=CFG.Color; d.stroke.Thickness=1.5

    d.corners={}
    for i=1,4 do
        local c=Instance.new("Frame",SG)
        c.BackgroundColor3=CFG.Color; c.BorderSizePixel=0
        c.Visible=false; c.ZIndex=3
        d.corners[i]=c
    end

    d.line=Instance.new("Frame",SG)
    d.line.BackgroundColor3=CFG.Color; d.line.BorderSizePixel=0
    d.line.Visible=false; d.line.AnchorPoint=Vector2.new(0.5,0); d.line.ZIndex=3

    d.name=Instance.new("TextLabel",SG)
    d.name.BackgroundTransparency=1; d.name.TextColor3=CFG.Color
    d.name.TextSize=13; d.name.Font=Enum.Font.GothamBold
    d.name.TextStrokeTransparency=0.4; d.name.Visible=false; d.name.ZIndex=4
    d.name.Text=plr.Name

    d.hpBg=Instance.new("Frame",SG)
    d.hpBg.BackgroundColor3=Color3.fromRGB(30,30,30)
    d.hpBg.BorderSizePixel=0; d.hpBg.Visible=false; d.hpBg.ZIndex=3

    d.hpFill=Instance.new("Frame",d.hpBg)
    d.hpFill.BackgroundColor3=Color3.fromRGB(120,255,120)
    d.hpFill.BorderSizePixel=0; d.hpFill.Size=UDim2.new(1,0,1,0); d.hpFill.ZIndex=4

    ESP[plr]=d
end

local function KillESP(plr)
    if not ESP[plr] then return end
    for _,o in pairs(ESP[plr]) do
        if typeof(o)=="Instance" and o.Destroy then o:Destroy()
        elseif typeof(o)=="table" then
            for _,c in pairs(o) do
                if c.Destroy then c:Destroy() end
            end
        end
    end
    ESP[plr]=nil
end

Players.PlayerAdded:Connect(BuildESP)
Players.PlayerRemoving:Connect(KillESP)
for _,p in pairs(Players:GetPlayers()) do BuildESP(p) end

local function GetESPColor(plr)
    if ESPCfg.TeamColor and plr and plr.Team then
        return plr.Team.TeamColor.Color
    end
    if ESPCfg.ColorMode=="Rainbow" then
        return Color3.fromHSV(RainbowHue, 0.8, 1)
    end
    return ColorPresets[ESPCfg.ColorMode] or Color3.fromRGB(170,90,255)
end

RunService.RenderStepped:Connect(function()
    if ESPCfg.ColorMode=="Rainbow" then
        RainbowHue=(RainbowHue+0.005)%1
    end

    local show=CFG.ESPBox or CFG.ESPLine or CFG.ESPName or CFG.ESPHealth

    for plr,d in pairs(ESP) do
        local c=plr.Character
        local hrp=c and c:FindFirstChild("HumanoidRootPart")
        local head=c and c:FindFirstChild("Head")
        local hum=c and c:FindFirstChildOfClass("Humanoid")
        local color=GetESPColor(plr)

        if show and c and hrp and head and hum and hum.Health>0 then
            local rp,on=Camera:WorldToViewportPoint(hrp.Position)
            local hp=Camera:WorldToViewportPoint(head.Position+Vector3.new(0,0.5,0))
            if on then
                local bh=math.abs(hp.Y-rp.Y)*2
                local bw=bh*0.6
                local bx=rp.X-bw/2
                local by=rp.Y-bh/2

                if ESPCfg.BoxStyle=="Caixa" then
                    d.box.Visible=CFG.ESPBox
                    d.box.Size=UDim2.new(0,bw,0,bh)
                    d.box.Position=UDim2.new(0,bx,0,by)
                    d.stroke.Color=color
                    d.stroke.Thickness=ESPCfg.LineThickness
                    for i=1,4 do d.corners[i].Visible=false end
                else
                    d.box.Visible=false
                    local cs=ESPCfg.CornerSize
                    local cth=math.max(1,math.floor(ESPCfg.LineThickness))
                    local pos={
                        {bx,by},{bx+bw-cs,by},
                        {bx,by+bh-cs},{bx+bw-cs,by+bh-cs},
                    }
                    for i=1,4 do
                        local cc=d.corners[i]
                        cc.Visible=CFG.ESPBox
                        cc.Position=UDim2.new(0,pos[i][1],0,pos[i][2])
                        cc.Size=UDim2.new(0,cs,0,cth)
                        cc.BackgroundColor3=color
                    end
                end

                d.name.Visible=CFG.ESPName
                d.name.Size=UDim2.new(0,200,0,16)
                d.name.Position=UDim2.new(0,rp.X-100,0,by-20)
                d.name.TextColor3=color
                d.name.TextSize=ESPCfg.TextSize
                if ESPCfg.ShowDistance then
                    local dist=math.floor((hrp.Position-Camera.CFrame.Position).Magnitude)
                    d.name.Text=plr.Name.." ["..dist.."m]"
                else
                    d.name.Text=plr.Name
                end

                d.hpBg.Visible=CFG.ESPHealth
                d.hpBg.Size=UDim2.new(0,4,0,bh)
                d.hpBg.Position=UDim2.new(0,bx-8,0,by)
                d.hpFill.Size=UDim2.new(1,0,hum.Health/hum.MaxHealth,0)
                d.hpFill.Position=UDim2.new(0,0,1-hum.Health/hum.MaxHealth,0)

                d.line.Visible=CFG.ESPLine
                local ctr=Vector2.new(Camera.ViewportSize.X/2,Camera.ViewportSize.Y)
                local tgt=Vector2.new(rp.X,rp.Y)
                local dir=tgt-ctr
                d.line.Size=UDim2.new(0,dir.Magnitude,0,ESPCfg.LineThickness)
                d.line.Position=UDim2.new(0,ctr.X,0,ctr.Y)
                d.line.Rotation=math.deg(math.atan2(dir.Y,dir.X))
                d.line.BackgroundColor3=color
            else
                d.box.Visible=false; d.line.Visible=false
                d.name.Visible=false; d.hpBg.Visible=false
                for i=1,4 do d.corners[i].Visible=false end
            end
        else
            d.box.Visible=false; d.line.Visible=false
            d.name.Visible=false; d.hpBg.Visible=false
            for i=1,4 do d.corners[i].Visible=false end
        end
    end
end)

-- ==================== INPUTS ====================
UserInputService.InputBegan:Connect(function(input,gpe)
    if gpe then return end
    if input.KeyCode==CFG.Key then aiming=true end
    if input.KeyCode==CFG.PanicKey then
        aiming=false
        CFG.Aimbot=false
        print("[PANIC] Aimbot desligado")
    end
end)
UserInputService.InputEnded:Connect(function(input)
    if input.KeyCode==CFG.Key then aiming=false end
end)
LP.CharacterAdded:Connect(function()
    aiming=false
end)

-- ==================== AIMBOT ====================
RunService.RenderStepped:Connect(function()
    FovC.Visible=CFG.ShowFOV and CFG.Aimbot
    FovC.Size=UDim2.new(0,CFG.FOV*2,0,CFG.FOV*2)
    FovS.Color=CFG.Aimbot and T.Acc or T.Red
end)

RunService:BindToRenderStep("PurpleAimbot", Enum.RenderPriority.Camera.Value+1, function()
    if not CFG.Aimbot or not aiming then return end

    local best,bestD=nil,math.huge
    for _,plr in pairs(Players:GetPlayers()) do
        if ValidTarget(plr) then
            local p=plr.Character[CFG.AimPart]
            local d3=(p.Position-Camera.CFrame.Position).Magnitude
            if d3<=CFG.MaxDist then
                local ok,fd=InFov(p.Position, CFG.FOV)
                if ok and fd<bestD and IsVisible(p) then
                    bestD=fd; best=p
                end
            end
        end
    end

    if not best then return end
    local tcf=CFrame.new(Camera.CFrame.Position, best.Position)
    local alpha=math.clamp(1-CFG.Smooth, 0.05, 1)
    Camera.CFrame=Camera.CFrame:Lerp(tcf, alpha)
end)

LP.AncestryChanged:Connect(function()
    if not LP:IsDescendantOf(game) then
        pcall(function() RunService:UnbindFromRenderStep("PurpleAimbot") end)
    end
end)

-- ==================== DRAG / ABRIR / FECHAR ====================
local drag,dStart,fStart
TB.InputBegan:Connect(function(i)
    if i.UserInputType==Enum.UserInputType.MouseButton1 then
        drag=true; dStart=i.Position; fStart=Frame.Position
    end
end)
TB.InputChanged:Connect(function(i)
    if drag and i.UserInputType==Enum.UserInputType.MouseMovement then
        local d=i.Position-dStart
        Frame.Position=UDim2.new(fStart.X.Scale,fStart.X.Offset+d.X,fStart.Y.Scale,fStart.Y.Offset+d.Y)
    end
end)
UserInputService.InputEnded:Connect(function(i)
    if i.UserInputType==Enum.UserInputType.MouseButton1 then drag=false end
end)

OpenBtn.MouseButton1Click:Connect(function() Frame.Visible=not Frame.Visible end)
CB.MouseButton1Click:Connect(function() Frame.Visible=false end)

LL:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    Sc.CanvasSize=UDim2.new(0,0,0,LL.AbsoluteContentSize.Y+10)
end)

-- ==================== NOTIFY ====================
local N=Instance.new("TextLabel",SG)
N.Size=UDim2.new(0,440,0,40); N.Position=UDim2.new(0.5,-220,0,20)
N.BackgroundColor3=T.Panel; N.TextColor3=T.AccB
N.Text="🎯 v9 CLEAN | [E] Aimbot • [F] Panic"
N.TextSize=15; N.Font=Enum.Font.GothamBold; N.BorderSizePixel=0; N.ZIndex=200
Instance.new("UICorner",N).CornerRadius=UDim.new(0,8)
task.wait(3.5)
TweenService:Create(N,TweenInfo.new(0.5),{BackgroundTransparency=1,TextTransparency=1}):Play()
task.wait(0.6); N:Destroy()

print("🎯 AIMBOT v9 CLEAN carregado. [E] Aimbot | [F] Panic")
