#!name=𝐀𝐥𝐥 𝐈𝐧 𝐎𝐧𝐞
#!desc= NgDanhThanhTrung

[Rule]
AND,((DOMAIN-SUFFIX,googlevideo.com), (PROTOCOL,UDP)),REJECT
AND,((DOMAIN,youtubei.googleapis.com), (PROTOCOL,UDP)),REJECT

[Header Rewrite]
^https?://api.revenuecat.com/.+/(receipts$|subscribers/?(.*?)*$) header-del x-revenuecat-etag
^https?://api.revenuecat.com/.+/(receipts$|subscribers/?(.*?)*$) header-del X-RevenueCat-ETag

[Script]

#Sub-Store
#hostname = sub.store
Sub-Store Core=type=http-request,pattern=^https?:\/\/sub\.store\/((download)|api\/(preview|sync|(utils\/node-info))),script-path=https://github.com/sub-store-org/Sub-Store/releases/latest/download/sub-store-1.min.js,requires-body=true,timeout=120
Sub-Store Simple=type=http-request,pattern=^https?:\/\/sub\.store,script-path=https://github.com/sub-store-org/Sub-Store/releases/latest/download/sub-store-0.min.js,requires-body=true

Sub-Store Sync=type=cron,cronexp=0 0 * * *,wake-system=1,timeout=120,script-path=https://github.com/sub-store-org/Sub-Store/releases/latest/download/cron-sync-artifacts.min.js
#************************************#

#Youtube
#hostname = *.googlevideo.com, youtubei.googleapis.com
youtube.request = type=http-request,pattern=^https:\/\/youtubei\.googleapis\.com\/youtubei\/v1\/(browse|next|player|reel\/reel_watch_sequence|get_watch),requires-body=1,max-size=-1,binary-body-mode=1,engine=jsc,script-path=https://github.com/lonely0811/Surge/raw/main/js/youtube.request.preview.js

youtube.response = type=http-response,pattern=^https:\/\/youtubei\.googleapis\.com\/youtubei\/v1\/(browse|next|player|search|reel\/reel_watch_sequence|guide|account\/get_setting|get_watch),requires-body=1,max-size=-1,binary-body-mode=1,engine=jsc,script-path=https://github.com/lonely0811/Surge/raw/main/js/youtube.response.preview.js,argument="{"lyricLang":"vi","captionLang":"vi","blockUpload":true,"blockImmersive":true,"debug":false}"
#************************************#

#SpotifyPremium
#hostname = spclient.wg.spotify.com
spotify-json = type=http-request,pattern=^https:\/\/spclient\.wg\.spotify\.com\/(artistview\/v1\/artist|album-entity-view\/v2\/album)\/,requires-body=0,script-path=https://raw.githubusercontent.com/app2smile/rules/master/js/spotify-json.js

spotify-proto = type=http-response,pattern=^https:\/\/spclient\.wg\.spotify\.com\/(bootstrap\/v1\/bootstrap|user-customization-service\/v1\/customize)$,requires-body=1,binary-body-mode=1,max-size=0,script-path=https://raw.githubusercontent.com/app2smile/rules/master/js/spotify-proto.js,script-update-interval=0
#************************************#

#SoundCloudGo+
#hostname= api-mobile.soundcloud.com
SoundCloudGo+=type=http-response,pattern=https://api-mobile.soundcloud.com/configuration/ios,requires-body=1,script-path=https://raw.githubusercontent.com/DungHoang120401/Nobita/refs/heads/Module/SoundCloud.js
#************************************#

#Revenuecat
#hostname = api.revenuecat.com
revenuecat = type=http-response, pattern=^https:\/\/api\.revenuecat\.com\/.+\/(receipts$|subscribers\/[^/]+$), script-path=https://raw.githubusercontent.com/DungHoang120401/Nobita/Module/Locket_Gold.js, requires-body=true, max-size=-1, timeout=60

deleteHeader = type=http-request, pattern=^https:\/\/api\.revenuecat\.com\/.+\/(receipts|subscribers), script-path=https://raw.githubusercontent.com/DungHoang120401/Nobita/Module/LKG_delete_header.js, timeout=60
#************************************#

#Truecaller
#hostname = premium*.truecaller.com
Truecaller = type=http-response,pattern=^https://premium-(.+)\.truecaller\.com/v\d/(subscriptions|products\/apple),requires-body=1,max-size=0,script-path=https://raw.githubusercontent.com/DungHoang120401/Nobita/refs/heads/Module/TrueCaller.js

[Map Local]
^https?:\/\/[\w-]+\.googlevideo\.com\/initplayback.+&oad data-type=text data=""

[MITM]
hostname = %APPEND% *.googlevideo.com, youtubei.googleapis.com, premium*.truecaller.com, sub.store, api.revenuecat.com, spclient.wg.spotify.com, api-mobile.soundcloud.com