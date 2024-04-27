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
<a href="https://discord.gg/3HuUY988hV"><img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABgAAAAYCAYAAADgdz34AAAAAXNSR0IArs4c6QAAAiZJREFUSEuVVut54zAMA7TIpZM0mSTJJJdOknSSOpPUkxhn6mFRttzm9COfrMgECYKkCVsEoPgTH5ej5Wm1aa8WA93bdjUD7FkmCC3Q3nbd510HOAF4lM2lvTC8X/svOYBfuPE0bijtALigdnmXdAB0BvgkOfhYJB0BvAN4kmGINO7gLLEWxzTp72zsFomn8c+RwhDFUJcBHPLjOO8fDPxoUQiuQSUzrptAsx0BkgcvrSvJh8/rJskzQNRqNesBfgETRga+Vc2YAt2SdAdwWY72IujguKMbyY9SW44iQpq+E6/7nlbMmJvFQ/fGSOYoPLnSdBF437KtEWBJZpMIT6PPmcBTCBzMz8We0SPgssrpg+R1kg4ELDofW+IaOEfFtbwmmiyCoiJpugPM/CffZtmZvKMXk/QF4Ji9jsARMNYKvlfRDCHwZJL1EWT+k5+Z31MpMGWA6Kgw0AzkJU3JjYqS1LQCqK203rQCMk+tYh0N0fJDwOds1orSis6TNIaY6FJoBj4l/S9eOQX8V6nlPAVHb4o6F9gPCm0U1PWkUAbkCFoVpf5Tl9FjDe6ZlWL/GBV2bqv8b3sTx6H0LoC5ZZQkZylFRQjnOV8JaFP6bRBZQaauUifm0DWQY+G7qeQyMu1FAWfmVuxH6ZonTbqA+gPwk6QV5WYCdrl9ZbBtZlDHUtNNN7P3R5T+dF4wehOtL5P901cn505guUfEalx9VWw+ItpQ/dM/4qwMMRnIEGwAAAAASUVORK5CYII="/></a>&nbsp;&nbsp;&nbsp;&nbsp;
