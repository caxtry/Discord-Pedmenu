# DISCORD-PEDMENU
Simple pedmenu script linked with Discord.

### What does it do?

This simple script will open the pedmenu if you have a discord role and your an admin on the server.

## REQUIRED
- You will need illenium-appearance installed: https://github.com/iLLeniumStudios/illenium-appearance

## SETUP MANUALLY
### server.lua
After having iillenium-appearance and setting up everything you may edit the following:
- Go inside the folder `illenium-appearance/server/server.lua`
- Search for `if Config.EnablePedMenu` select all from line **304** to **334** and paste the following code:

```lua

local function HasDiscordRole(playerId, requiredRole, callback)
    local identifiers = GetPlayerIdentifiers(playerId)
    for _, identifier in ipairs(identifiers) do
        if string.find(identifier, "discord:") then
            local discordId = string.gsub(identifier, "discord:", "")
            local url = ("https://discord.com/api/v9/guilds/%s/members/%s"):format(Config.DiscordGuildId, discordId)
            PerformHttpRequest(url, function(errorCode, resultData, resultHeaders)
                if errorCode == 200 then
                    local data = json.decode(resultData)
                    for _, role in ipairs(data.roles) do
                        if role == requiredRole then
                            callback(true)
                            return
                        end
                    end
                end
                callback(false)
            end, "GET", "", {
                ["Content-Type"] = "application/json",
                ["Authorization"] = "Bot " .. Config.BotToken
            })
        end
    end
    callback(false)
end

if Config.EnablePedMenu then
    lib.addCommand("pedmenu", {
        help = _L("commands.pedmenu.title"),
        params = {
            {
                name = "playerID",
                type = "number",
                help = "Target player's server id",
                optional = true
            },
        },
    }, function(source, args)
        
        local target = source
        if args.playerID then
            local citizenID = Framework.GetPlayerID(args.playerID)
            if citizenID then
                target = args.playerID
            else
                lib.notify(source, {
                    title = _L("commands.pedmenu.failure.title"),
                    description = _L("commands.pedmenu.failure.description"),
                    type = "error",
                    position = Config.NotifyOptions.position
                })
                return
            end
        end
        
        HasDiscordRole(target, Config.RequiredDiscordRole, function(hasRole)

            if not hasRole then
                lib.notify(source, {
                    title = _L("commands.pedmenu.failure.title"),
                    description = _L("commands.pedmenu.failure.role_missing"),
                    type = "error",
                    position = Config.NotifyOptions.position
                })
                return
            end

            TriggerClientEvent("illenium-appearance:client:openClothingShopMenu", target, true)
        end, "You don't have enough permissions to use pedmenu.")
    end)
end
```

### config.lua
Last step!
- Go inside the folder `illenium-appearance/shared/config.lua`
- Search for `Config.EnablePedMenu` it will be in line **36**
- Select all from line **36** to **37** and paste the following code:

```lua
Config.EnablePedMenu = true -- Enable or Disable the pedmenu true/false
Config.PedMenuGroup = "group.admin" -- pedmenu group permission
Config.BotToken = "" -- Bot Token
Config.RequiredDiscordRole = "" -- Role ID
Config.DiscordGuildId = "" -- Guild ID (Server ID)
```


## NEED HELP?
- You can join my [Discord](https://discord.gg/3HuUY988hV) if you need any help! 
