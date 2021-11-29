# MikroTik Scripts

## Auto Backup

```
:log info "STARTING BACKUP";

:global filename;

:global date [/system clock get date];
:global time [/system clock get time];
:global name [/system identity get name];

:global months ("jan","feb","mar","apr","may","jun","jul","aug","sep","oct","nov","dec");
:global hour [:pick $time 0 2];
:global min [:pick $time 3 5];
:global sec [:pick $time 6 8];
:global month [:pick $date 0 3];
:set month ([ :find $months $month -1 ] + 1);

:if ($month < 10) do={ :set month ("0" . $month); }

:global day [:pick $date 4 6];
:global year [:pick $date 7 11];
:set filename ($name. "-" .$year."-".$month."-".$day."-".$hour.$min.$sec);

/system backup save name="pub/$filename";

:log info "DELAY 3S"
:delay 3s;

:log info "GENERATING RSC";
:global rsc $filename;

/export file="pub/$rsc";

:log info "BACKUP FINISHED"
```

## Send Text to Telegram

```
:log info "Send to Telegram";

/tool fetch url="https://api.telegram.org/bot[YourBotID]/sendMessage?chat_id=[ChatGroupID]&text=Hello Telegram" keep-result=no;

```

## Send Text to Telegram when loggin

```
:local whouser $user;
:local whoip $address;
:local macaddr [/ip hotspot active get [find address=$whoip] mac-address];
:log info "user logged in: $whouser IP: $whoip Mac: $macaddr";

{
:local date [/system clock get date ];
:local time [/system clock get time ];

:if ( [/ip hotspot user get $user comment ] = "" ) do={[/ip hotspot user set $user comment="$date $time"];

:log info "New Hotspot user logged in: $whouser";}
}

:log info "Send user to Telegram";

/tool fetch url="https://api.telegram.org/bot[YourBotID]/sendMessage?chat_id=[ChatGroupID]&text=*$whouser* is logged&parse_mode=markdown" keep-result=no;

:local USERACTIVE [/ip hotspot active print count-only];

/tool fetch url="https://api.telegram.org/[YourBotID]/sendMessage?chat_id=[ChatGroupID]&text=$USERACTIVE users online now." keep-result=no;
```
