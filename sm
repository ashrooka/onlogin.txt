:local mac $"mac-address"
:log info ("LOGIN start user=" . $user . " mac=" . $mac)

:local hn "unknown"
:do {
  :local lid [/ip dhcp-server lease find where mac-address=$mac]
  :if ([:len $lid] > 0) do={
    :local h [/ip dhcp-server lease get [:pick $lid 0] host-name]
    :if ([:len $h] > 0) do={ :set hn $h }
  }
} on-error={ :log info "LOGIN lease error" }

:local ok "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789_.-"
:local hexd "0123456789ABCDEF"
:local out ""
:for i from=0 to=([:len $hn] - 1) do={
  :local c [:pick $hn $i ($i + 1)]
  :if ($c = " ") do={
    :set out ($out . "-")
  } else={
    :if ([:typeof [:find $ok $c]] = "num") do={
      :set out ($out . $c)
    } else={
      :set out ($out . "?")
    }
  }
}
:log info ("LOGIN name=" . $out)

:local d [/system clock get date]
:local t [/system clock get time]
:local dd (([:pick $d 0 3]) . "-" . ([:pick $d 4 6]) . "-" . ([:pick $d 7 11]))
:local tt (([:pick $t 0 2]) . "-" . ([:pick $t 3 5]) . "-" . ([:pick $t 6 8]))
:local mail ($out . "_" . $dd . "_" . $tt . "@sm.cafe")
:log info ("LOGIN email=" . $mail)

:do {
  :local uid [/ip hotspot user find where name=$user]
  :if ([:len $uid] > 0) do={
    /ip hotspot user set [:pick $uid 0] email=$mail
    :local curMac [/ip hotspot user get [:pick $uid 0] mac-address]
    :if (([:len $curMac] = 0) || ($curMac = "00:00:00:00:00:00")) do={
      /ip hotspot user set [:pick $uid 0] mac-address=$mac
    }
  } else={ :log info "LOGIN user not found" }
} on-error={ :log info "LOGIN set error" }
