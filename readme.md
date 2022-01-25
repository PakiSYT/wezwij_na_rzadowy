# Aby wszystko poprawnie działało, postępuj zgodnie z poradnikiem #
# Skrypt jest prawdopodobnie wykonany przez MlodyR#2137, ja go tylko repastuje #
# Miłego korzystania :) #

Otwierasz folder z EasyAdmin'em, szukasz gui_c.lua i wklejasz to :

        if permissions["spectate"] then
                          local thisItem = NativeUI.CreateItem("~b~Wezwij na rzadowy","")
                          thisPlayer:AddItem(thisItem)
                      thisItem.Activated = function(ParentMenu,SelectedItem)
                        TriggerServerEvent("wezwijrzad", thePlayer.id)
                        what = "ZAPROSIŁ NA POCZEKALNIE"
                        TriggerServerEvent("wezwijpoczek", what)
                      end
                    end
Następnie przechodzisz do server.lua i wklejasz to:

    RegisterServerEvent("wezwijrzad")
    AddEventHandler("wezwijrzad", function(pedal)
    local _source = pedal
    local xPlayer = ESX.GetPlayerFromId(_source)
    local xd = ExtractIdentifiers(_source)
    local name = GetPlayerName(source)
    local discord ="<@" ..xd.discord:gsub("discord:", "")..">" 

        TriggerClientEvent('rzadowy:call', pedal, GetPlayerName(_source))

    end)


    RegisterServerEvent("wezwijpoczek")
    AddEventHandler("wezwijpoczek", function(what)
    local name = GetPlayerName(source)
    wiadomosc = ""..what.."\nNAZWA STEAM ZAPRASZAJĄCEGO: "..name.."\nID ZAPRASZAJĄCEGO: "..source.."  " 
    EasyAdmin('EasyAdmin', wiadomosc, 11750815)
    end)



    function EasyAdmin(hook,message,color)
      local embeds = {
                {
            ["title"] = message,
            ["type"] = "rich",
            ["color"] = color,
            ["footer"] = {
                ["text"] = 'RozganiaczPack'
                    },
                }
            }
    if message == nil or message == '' then return FALSE end
    PerformHttpRequest('', function(err, text, headers) end, 'POST', json.encode({ username = hook,embeds = embeds}), { ['Content-Type'] = 'application/json' })
    end

 Następnie przechodzisz do client.lua i wklejasz te funkcje:

    function ExtractIdentifiers(src)
       local identifiers = {
        steam = "",
        discord = "",
        license = "",
        xbl = "",
        live = ""
    }

    for i = 0, GetNumPlayerIdentifiers(source) - 1 do
        local id = GetPlayerIdentifier(source, i)

        if string.find(id, "steam") then
            identifiers.steam = id
        elseif string.find(id, "discord") then
            identifiers.discord = id
        elseif string.find(id, "license") then
            identifiers.license = id
        elseif string.find(id, "xbl") then
            identifiers.xbl = id
        elseif string.find(id, "live") then
            identifiers.live = id
        end
    end

    return identifiers
    end


    local announcestring = false
 
    RegisterNetEvent("rzadowy:call")
    AddEventHandler("rzadowy:call", function(admin)
     announcestring = 'Administrator: ~g~'..admin..'~s~ zaprasza Cię na kanał pomocy'
     Citizen.Wait(30*1000)
     announcestring = false
    end)
 
    function Initialize(scaleform)
     local scaleform = RequestScaleformMovie(scaleform)
     while not HasScaleformMovieLoaded(scaleform) do
         Citizen.Wait(0)
     end
     PushScaleformMovieFunction(scaleform, "SHOW_SHARD_WASTED_MP_MESSAGE")
     PushScaleformMovieFunctionParameterString("~r~UWAGA")
     PushScaleformMovieFunctionParameterString(announcestring)
     PopScaleformMovieFunctionVoid()
     return scaleform
    end
