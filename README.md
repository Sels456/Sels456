lblInfo= UI.Label("[[XxSauLxX]]")
lblInfo= UI.Label("-- [[Combos]] --")
cpvp = macro(200, "Combo",  function()
if g_game.isAttacking() then
say(storage.ComboText)
say(storage.Combo1Text)
say(storage.Combo2Text)
say(storage.Combo3Text)
say(storage.Combo4Text)
say(storage.Combo5Text)
end

end)
addTextEdit("ComboText", storage.ComboText or "magia 1", function(widget, text) 
storage.ComboText = text
end)
addTextEdit("Combo1Text", storage.Combo1Text or "magia 1", function(widget, text) storage.Combo1Text = text
end)
addTextEdit("Combo2Text", storage.Combo2Text or "magia 2", function(widget, text) storage.Combo2Text = text
end)
addTextEdit("Combo3Text", storage.Combo3Text or "magia 3", function(widget, text) storage.Combo3Text = text
end)
addTextEdit("Combo4Text", storage.Combo4Text or "magia 4", function(widget, text) storage.Combo4Text = text
end)
addTextEdit("Combo5Text", storage.Combo5Text or "magia 5", function(widget, text) storage.Combo5Text = text
end)
test1 = addIcon("2", {item =2137, text = "C.UP", hotkey = ""}, cpvp)
test1:breakAnchors()
test1:move(200, 75)

local combo
macro(100, "Hibrido", function()
if g_game.isAttacking() then
say('')
say('Hibrido super slash')
say('Hibrido devastacion')
end
end)

local combo
macro(100, "Fire", function()
if g_game.isAttacking() then
say('')
say('fire devion')
say('fire devastacion')
end
end)

local combo
macro(100, "Magnus", function()
if g_game.isAttacking() then
say('')
say('Magnus penetracion')
say('magnus destruction')
end
end)

lblInfo= UI.Label("-- [[PvP]] --")
UI.Button("Friends", function(newText)
    UI.MultilineEditorWindow(storage.FriendText or "", {title="Amigos", description="Coloque o nome dos amigos"}, function(text)
        storage.FriendText = text
        reload()
    end)
end)

isAmigo = function(name)
    if type(name) ~= 'string' then
        name = name:getName()
    end
    local tabela = storage.FriendText:split('\n')
    tabela = #tabela > 0 and tabela or false
    if tabela then
        return table.find(tabela, name:trim(), true)
    end
end
macro(1, "Enemy", function()
for _,pla in ipairs(getSpectators(posz())) do
attacked = g_game.getAttackingCreature()
  if (not attacked or (attacked and (not attacked:isPlayer() or (pla:getHealthPercent() < attacked:getHealthPercent())))) and pla:isPlayer() and pla:getEmblem() ~= 1 then 
   g_game.attack(pla)
  end
end
end)

lblInfo= UI.Label("-- [[Auras]] --")

macro(8000, "Power Up", function()
  say('Power Up')
end)

macro(8000, "Mega Power ", function()
  say('Mega Power Up')
end)

macro(8000, "Giga Power ", function()
  say('Giga Power Up')
end)

macro(8000, " Plation ", function()
  say('Plation Power Up')
end)

macro(8000, " Hibrido ", function()
  say('Hibrido Power Up')
end)


macro(8000, " Fire ", function()
  say('fire Power Up')
end)

macro(8000, " Magnus", function()
  say('Magnus Power Up')
end)

-- AUTO REVIDE / FIGHT BACK / ATTACK PLAYER PK (REVIDAR)








setDefaultTab("Main")

-- vBot scripting services: F.Almeida#8019
-- if you like it, consider making a donation:
-- ATTENTION:
-- Don't edit below this line unless you know what you're doing.
-- ATENÇÃO:
-- Não mexa em nada daqui para baixo, a não ser que saiba o que está fazendo.
-- ATENCIÓN:
-- No cambies nada desde aquí, solamente si sabes lo que estás haciendo.

local ignoreEmblems = {1,4} -- Guild Emblems (Allies)

local ui = setupUI([[
Panel
  height: 19

  BotSwitch
    id: title
    anchors.top: parent.top
    anchors.left: parent.left
    text-align: center
    width: 130
    !text: tr('Fight Back (Revide)')
    font: verdana-11px-rounded

  Button
    id: edit
    anchors.top: prev.top
    anchors.left: prev.right
    anchors.right: parent.right
    margin-left: 3
    height: 17
    text: Setup
    font: verdana-11px-rounded
]])

local edit = setupUI([[
RevideBox < CheckBox
  font: verdana-11px-rounded
  margin-top: 5
  margin-left: 5
  anchors.top: prev.bottom
  anchors.left: parent.left
  anchors.right: parent.right
  color: lightGray

Panel
  height: 123

  RevideBox
    id: pauseTarget
    anchors.top: parent.top
    text: Pause TargetBot 
    !tooltip: tr('Pause TargetBot While fighting back.')

  RevideBox
    id: pauseCave
    text: Pause CaveBot 
    !tooltip: tr('Pause CaveBot While fighting back.')

  RevideBox
    id: followTarget
    text: Follow Target 
    !tooltip: tr('Set Chase Mode to Follow While fighting back.')

  RevideBox
    id: ignoreParty
    text: Ignore Party Members

  RevideBox
    id: ignoreGuild
    text: Ignore Guild Members

  RevideBox
    id: attackAll
    text: Attack All Skulled
    !tooltip: tr("Attack every skulled player, even if he didn't attacked you.")

  BotTextEdit
    id: esc
    width: 83
    anchors.bottom: parent.bottom
    anchors.right: parent.right
    text: Escape
    color: red
    font: verdana-11px-rounded

  Label
    text: Cancel Attack:
    font: verdana-11px-rounded
    anchors.left: parent.left
    margin-left: 5
    anchors.verticalCenter: esc.verticalCenter
]])
edit:hide()

local showEdit = false
ui.edit.onClick = function(widget)
  showEdit = not showEdit
  if showEdit then
    edit:show()
  else
    edit:hide()
  end
end
-- End Basic UI

-- Storage
local st = "RevideFight"
storage[st] = storage[st] or {
  enabled = false,
  pauseTarget = true,
  pauseCave = true,
  followTarget = true,
  ignoreParty = false,
  ignoreGuild = false,
  attackAll = false,
  esc = "Escape",
}
local config = storage[st]

-- UI Functions
-- Main Button
ui.title:setOn(config.enabled)
ui.title.onClick = function(widget)
  config.enabled = not config.enabled
  widget:setOn(config.enabled)
end

-- Checkboxes
do
  edit.pauseTarget:setChecked(config.pauseTarget)
  edit.pauseTarget.onClick = function(widget)
    config.pauseTarget = not config.pauseTarget
    widget:setChecked(config.pauseTarget)
    widget:setImageColor(config.pauseTarget and "green" or "red")
  end
  edit.pauseTarget:setImageColor(config.pauseTarget and "green" or "red")
  
  edit.pauseCave:setChecked(config.pauseCave)
  edit.pauseCave.onClick = function(widget)
    config.pauseCave = not config.pauseCave
    widget:setChecked(config.pauseCave)
    widget:setImageColor(config.pauseCave and "green" or "red")
  end
  edit.pauseCave:setImageColor(config.pauseCave and "green" or "red")

  edit.followTarget:setChecked(config.followTarget)
  edit.followTarget.onClick = function(widget)
    config.followTarget = not config.followTarget
    widget:setChecked(config.followTarget)
    widget:setImageColor(config.followTarget and "green" or "red")
  end
  edit.followTarget:setImageColor(config.followTarget and "green" or "red")

  edit.ignoreParty:setChecked(config.ignoreParty)
  edit.ignoreParty.onClick = function(widget)
    config.ignoreParty = not config.ignoreParty
    widget:setChecked(config.ignoreParty)
    widget:setImageColor(config.ignoreParty and "green" or "red")
  end
  edit.ignoreParty:setImageColor(config.ignoreParty and "green" or "red")

  edit.ignoreGuild:setChecked(config.ignoreGuild)
  edit.ignoreGuild.onClick = function(widget)
    config.ignoreGuild = not config.ignoreGuild
    widget:setChecked(config.ignoreGuild)
    widget:setImageColor(config.ignoreGuild and "green" or "red")
  end
  edit.ignoreGuild:setImageColor(config.ignoreGuild and "green" or "red")

  edit.attackAll:setChecked(config.attackAll)
  edit.attackAll.onClick = function(widget)
    config.attackAll = not config.attackAll
    widget:setChecked(config.attackAll)
    widget:setImageColor(config.attackAll and "green" or "red")
  end
  edit.attackAll:setImageColor(config.attackAll and "green" or "red")
end

-- TextEdit
edit.esc:setText(config.esc)
edit.esc.onTextChange = function(widget, text)
  config.esc = text
end
edit.esc:setTooltip("Hotkey to cancel attack.")

-- End of setup.

local target = nil
local c = config

-- Main Loop
macro(100, function()
  if not c.enabled then return end
  if not target then
    if c.pausedTarget then
      c.pausedTarget = false
      TargetBot.setOn()
    end
    if c.pausedCave then
      c.pausedCave = false
      CaveBot.setOn()
    end
    -- Search for attackers
    local creatures = getSpectators(false)
    for s, spec in ipairs(creatures) do
      if spec ~= player and spec:isPlayer() then
        if (c.attackAll and spec:getSkull() > 2) or spec:isTimedSquareVisible() then
          if c.ignoreParty or spec:getShield() < 3 then
            if c.ignoreGuild or not table.find(ignoreEmblems, spec:getEmblem()) then
              target = spec:getName()
              break
            end
          end
        end
      end
    end
    return
  end

  local creature = getPlayerByName(target)
  if not creature then target = nil return end
  if c.pauseTargetBot then
    c.pausedTarget = true
    TargetBot.setOff()
  end
  if c.pauseTarget then
    c.pausedTarget = true
    TargetBot.setOff()
  end
  if c.pauseCave then
    c.pausedCave = true
    CaveBot.setOff()
  end

  if c.followTarget then
    g_game.setChaseMode(2)
  end

  if g_game.isAttacking() then
    if g_game.getAttackingCreature():getName() == target then
      return
    end
  end
  g_game.attack(creature)
end)

onKeyDown(function(keys)
  if not c.enabled then return end
  if keys == config.esc then
    target = nil
  end
end)

UI.Separator()

Panels.AttackLeaderTarget(batTab)  UI.Separator()

