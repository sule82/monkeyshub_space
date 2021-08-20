---
title: Mikrotik auto backup & update
date: 2020-07-20 02:11:20
updated: 2020-07-20 02:11:20
categories:
  - Research
tags:
  - mikrotik
  - update
  - backup
---

```routeros
/system script add name="bananaUP" source="[...]"
/system script run bananaUP
/system sheduler add name="Sedmicni Backup" start-time=00:01:00 interval=7d on-event="/system script run bananaUP"
```

<!--more-->

# Info

Skripta je namjenjena za dnevni, ili po izboru sedmični, mjesečni itd, backup rutera na e-mail. Također možete postaviti redovnu provjeru i automatski update ili dojavu o dostupnoj novoj verziji mailom.

- mod: samo backup, provjera verzije, update & backup
- update kanal po izboru
- instalacija samo patch na postojeću verziju (ako ograničite update na xx.xx.patch)
- osnovne informacije o ruteru
- sigurnosni triger: update neće biti pokrenut ako nije uspjelo slanje backup-a na mail
- firmware upgrade

# Opcije skripte

**Samo Backup** - Kreira sistem i backup konfiguracije te šalje na mail u atachmentu. Koristi mail za skladištenje backup fajlova.

**Backup & notifikacija o novim RouterOS izdanjima** - Uključuje gore navedeno, također provjerava nova izdanja RouterOS firmware i šalje informacije na mail.

**Backup & automatski RouterOS upgrade** - Gore dvoje navedeno, i ako je novi firmware dostupan, sskripta pokreće upgrade proces. Na kraju dobijate dva mail-a. Prvi sa sistem backup i prethodnom RouterOS verzijom, drugi šalje po završetku upgrade (prikačen backup sa update sistemom).

## Upotreba

### Konfiguracija skripte

Skriptu kopirajte i po želji podesite parametre. U slučaju da naletite na poteškoće, kontaktirajte me, slobodno :monkey:.

```routeros bananaUP.rsc
:local emailAdresa "adnan@monkeyshub.space";

## mod: backup, osupdate, osnotifikacija.
:local modSkripte "backup";

## za `osupdate` ili `osnotifikacija`
:local forceBackup false;

:local backupPassword ""

:local sensetiveDataInConfig false;

## update kanal: stable, long-term, testing, development
:local updateKanal "stable";

## samo za modSkripte "osupdate"
:local instalirajSamoPatchUpdates false;

#Script messages prefix
:local SMP "B+Up"

:log info "\r\n$SMP Starta \"Mikrotik RouterOS automatski backup i update\" skripta.";
:log info "$SMP Script Mod: $modSkripte, forceBackup: $forceBackup";

:if ([:len $emailAdresa] = 0 or [:len [/tool e-mail get address]] = 0 or [:len [/tool e-mail get from]] = 0) do={
	:log error ("$SMP Email konfiguracija nije valjana.");   
	:error "$SMP vozdrica!";
}

if ([:len [/system identity get name]] = 0 or [/system identity get name] = "MikroTik") do={
	:log warning ("$SMP Promjenite identitet rutera.");  
};

:global buGlobalFuncGetOsVerNum do={
	:local osVer $paramOsVer;
	:local osVerNum;
	:local osVerMicroPart;
	:local zro 0;
	:local tmp;

	:local isBetaPos [:tonum [:find $osVer "beta" 0]];
	:if ($isBetaPos > 1) do={
		:set osVer ([:pick $osVer 0 $isBetaPos] . "." . [:pick $osVer ($isBetaPos + 4) [:len $osVer]]);
	}

	:local dotPos1 [:find $osVer "." 0];

	:if ($dotPos1 > 0) do={

		:set osVerNum  [:pick $osVer 0 $dotPos1];

		:local dotPos2 [:find $osVer "." $dotPos1];

		:if ([:len $dotPos2] = 0) 	do={:set tmp [:pick $osVer ($dotPos1+1) [:len $osVer]];}

		:if ($dotPos2 > 0) 			do={:set tmp [:pick $osVer ($dotPos1+1) $dotPos2];}

		:if ([:len $tmp] = 1) 	do={:set osVerNum "$osVerNum$zro$tmp";}

		:if ([:len $tmp] = 2) 	do={:set osVerNum "$osVerNum$tmp";}

		:if ($dotPos2 > 0) do={
			:set tmp [:pick $osVer ($dotPos2+1) [:len $osVer]];

			:if ([:len $tmp] = 1) do={:set osVerNum "$osVerNum$zro$tmp";}

			:if ([:len $tmp] = 2) do={:set osVerNum "$osVerNum$tmp";}
		} else={

			:set osVerNum "$osVerNum$zro$zro";
		}
	} else={

		:set osVerNum "$osVer$zro$zro$zro$zro";
	}

	:return $osVerNum;
}

:global buGlobalFuncCreateBackups do={
	:log info ("$SMP Pokrenuto kreiranje backup fajlova.");  

	:local backupFileSys "$backupName.backup";
	:local backupFileConfig "$backupName.rsc";
	:local backupNames {$backupFileSys;$backupFileConfig};

	:if ([:len $backupPassword] = 0) do={
		/system backup save dont-encrypt=yes name=$backupName;
	} else={
		/system backup save password=$backupPassword name=$backupName;
	}
	:log info ("$SMP Kreiran sistem backup. $backupFileSys");   

	:if ($sensetiveDataInConfig = true) do={
		/export compact file=$backupName;
	} else={
		/export compact hide-sensitive file=$backupName;
	}
	:log info ("$SMP Konfiguracijski fajl je eksportovan. $backupFileConfig");   

	:delay 5s;
	:return $backupNames;
}

:global buGlobalVarUpdateStep;

:local dateTime ([:pick [/system clock get date] 7 11] . [:pick [/system clock get date] 0 3] . [:pick [/system clock get date] 4 6] . "-" . [:pick [/system clock get time] 0 2] . [:pick [/system clock get time] 3 5] . [:pick [/system clock get time] 6 8]);

:local deviceOsVerInst 			[/system package update get installed-version];
:local deviceOsVerInstNum 		[$buGlobalFuncGetOsVerNum paramOsVer=$deviceOsVerInst];
:local deviceOsVerAvail 		"";
:local deviceOsVerAvailNum 		0;
:local deviceRbModel			[/system routerboard get model];
:local deviceRbSerialNumber 	[/system routerboard get serial-number];
:local deviceRbCurrentFw 		[/system routerboard get current-firmware];
:local deviceRbUpgradeFw 		[/system routerboard get upgrade-firmware];
:local deviceIdentityName 		[/system identity get name];
:local deviceIdentityNameShort 	[:pick $deviceIdentityName 0 18]
:local deviceupdateKanal 		[/system package update get channel];

:local isOsUpdateAvailable 	false;
:local potrebnoUpdateovati	false;

:local isSendEmailRequired	true;

:local mailSubjekt   		"$SMP Uredjaj - $deviceIdentityNameShort.";
:local mailPoruka 	 		"";

:local mailPorukaDeviceInfo	"\r\n\r\nInformacije o uredjaju: \r\nIdentitet: $deviceIdentityName \r\nModel: $deviceRbModel \r\nSerijski broj: $deviceRbSerialNumber \r\nYrenutni RouterOS: $deviceOsVerInst ($[/system package update get channel]) $[/system resource get build-time] \r\nTrenutni routerboard FW: $deviceRbCurrentFw \r\nUptime uredjaja: $[/system resource get uptime]";
:local mailPorukaCopyright 	"\r\n\r\nMikrotik RouterOS Banana UP \r\nhttps://monkeyshub.space";
:local changelogUrl			("RouterOS changelog: https://mikrotik.com/download/changelogs/" . $updateKanal . "-release-tree");

:local backupName 			"$deviceIdentityName.$deviceRbModel.$deviceRbSerialNumber.v$deviceOsVerInst.$deviceupdateKanal.$dateTime";
:local backupNameBeforeUpd	"backup_before_update_$backupName";
:local backupNameAfterUpd	"backup_after_update_$backupName";

:local backupNameFinal		$backupName;
:local mailPrilog		[:toarray ""];

:local updateStep $buGlobalVarUpdateStep;
:do {/system script environment remove buGlobalVarUpdateStep;} on-error={}
:if ([:len $updateStep] = 0) do={
	:set updateStep 1;
}


:if ($updateStep = 1) do={
	:log info ("$SMP Prvi korak.");   

	if ($modSkripte = "osupdate" or $modSkripte = "osnotifikacija") do={
		log info ("$SMP Trazim novu verziju RouterOS-a. Trenutna je: $deviceOsVerInst");
		/system package update set channel=$updateKanal;
		/system package update check-for-updates;
		:delay 5s;
		:set deviceOsVerAvail [/system package update get latest-version];

		:if ([:len $deviceOsVerAvail] = 0) do={
			:log warning ("$SMP Ne mogu dobiti informacije o novoj RouterOS verziji sa servera.");
			:set mailSubjekt	($mailSubjekt . " Ooops, Nema podataka o novoj verziji!")
			:set mailPoruka 		($mailPoruka . "Doslo je do greske! \r\nMikrotik nije uspio doci do informacija o novom RouterOS sa servera! \r\nPogledaj log uredjaja za dodatne informacije.")
		} else={
			:set deviceOsVerAvailNum [$buGlobalFuncGetOsVerNum paramOsVer=$deviceOsVerAvail];

			:if ($deviceOsVerAvailNum > $deviceOsVerInstNum) do={
				:set isOsUpdateAvailable true;
				:log info ("$SMP Novi RouterOS je dostupan! $deviceOsVerAvail");
			} else={
				:set isSendEmailRequired false;
				:log info ("$SMP Sistem je Up 2 Date.");
				:set mailSubjekt ($mailSubjekt . " Nema novih OS update-a.");
				:set mailPoruka 	 ($mailPoruka . "Sistem je Up 2 Date.");
			}
		};
	} else={
		:set modSkripte "backup";
	};

	if ($forceBackup = true) do={
		:set isSendEmailRequired true;
	}

	if ($isOsUpdateAvailable = true and $isSendEmailRequired = true) do={
		if ($modSkripte = "osnotifikacija") do={
			:set mailSubjekt 	($mailSubjekt . " Novi RouterOS je dostupan! v.$deviceOsVerAvail.")
			:set mailPoruka 		($mailPoruka . "Nova RouterOS verzija je dostupna za instalaciju: v.$deviceOsVerAvail ($updateKanal) \r\n$changelogUrl")
		}

		if ($modSkripte = "osupdate") do={
			:set potrebnoUpdateovati true;
			:if ($instalirajSamoPatchUpdates = true) do={
				:if ([:pick $deviceOsVerInstNum 0 ([:len $deviceOsVerInstNum]-2)] = [:pick $deviceOsVerAvailNum 0 ([:len $deviceOsVerAvailNum]-2)]) do={
					:log info ("$SMP Nova patch verzija RouterOS firmware-a je dostupna.");   
				} else={
					:log info ("$SMP Nova verzija RouterOS firmware-a je dostupna. Potrebno rucno instalirati.");
					:set mailSubjekt 	($mailSubjekt . " Novi RouterOS: v.$deviceOsVerAvail je potrebno rucno instalirati.");
					:set mailPoruka 		($mailPoruka . "Nova verzija RouterOS firmware-a je dostupna za rucno instaliranje.: v.$deviceOsVerAvail ($updateKanal). \r\nIzabrao si automatsko instaliranje samo za patch verzije.  \r\n$changelogUrl");
					:set potrebnoUpdateovati false;
				}
			}


			if ($potrebnoUpdateovati = true) do={
				:log info ("$SMP Novi RouterOS ce biti instalisan! v.$deviceOsVerInst -> v.$deviceOsVerAvail");
				:set mailSubjekt	($mailSubjekt . " Novi RouterOS ce biti instalisan! v.$deviceOsVerInst -> v.$deviceOsVerAvail.");
				:set mailPoruka 		($mailPoruka . "Tvoj Mikrotik ce biti update-ovan na novu RouterOS verziju sa v.$deviceOsVerInst na v.$deviceOsVerAvail (Update kanal: $updateKanal) \r\nFinalni izvjestaj sa detaljima se salje nakon zavrsenog procesa. \r\nAko ne primis drugu poruku u narednih 5 minuta onda je vjerovatno nesto krenulo po zlu. (Provjeri log uredjaja)");
			}

		}
	}

	:log info ("$SMP Provjeravam da li je potrebno uraditi backup.");
	if ($forceBackup = true or $modSkripte = "backup" or $potrebnoUpdateovati = true) do={
		:log info ("$SMP Kreiram sistemski backup.");
		if ($potrebnoUpdateovati = true) do={
			:set backupNameFinal $backupNameBeforeUpd;
		};
		if ($modSkripte != "backup") do={
			:set mailPoruka ($mailPoruka . "\r\n\r\n");
		};

		:set mailSubjekt	($mailSubjekt . " Kreiran backup.");
		:set mailPoruka		($mailPoruka . "Sistemski backup fajlovi su kreirani i prikaceni za email poruku.");

		:set mailPrilog [$buGlobalFuncCreateBackups backupName=$backupNameFinal backupPassword=$backupPassword sensetiveDataInConfig=$sensetiveDataInConfig];
	} else={
		:log info ("$SMP Nema potrebe za kreiranjem backup fajla.");
	}

	:set mailPoruka ($mailPoruka . $mailPorukaDeviceInfo . $mailPorukaCopyright);
}

:if ($updateStep = 2) do={
	:log info ("$SMP Drugi korak.");   

	if ($deviceRbCurrentFw != $deviceRbUpgradeFw) do={
		:set isSendEmailRequired false;
		:delay 10s;
		:log info "$SMP Nadogradjujem routerboard firmware sa v.$deviceRbCurrentFw na v.$deviceRbUpgradeFw";
		/system routerboard upgrade;
		:delay 5s;
		:log info "$SMP routerboard upgrade proces je zavrsio, reboot-ujem za minut!";

		/system schedule add name=BANANAUP-IZJESTAJ-BOOT on-event=":delay 5s; /system scheduler remove BANANAUP-IZJESTAJ-BOOT; :global buGlobalVarUpdateStep 3; :delay 10s; /system script run bananaUP;" start-time=startup interval=0;
		/system reboot;
	} else={
		:log info "$SMP Routerboard je vec update-ovan, preskacem ovaj korak.";
		:set updateStep 3;
	};
}

:if ($updateStep = 3) do={
	:log info ("$SMP Performing the third step.");   
	:log info "B+Up RouterOS i routerboard upgrade proces je zavrsio. Nova RouterOS verzija: v.$deviceOsVerInst, routerboard firmware: v.$deviceRbCurrentFw.";

	:log info "$SMP Email sa izvjestajem i backup fajlovima se salju za minut.";
	:delay 1m;
	:set mailSubjekt	($mailSubjekt . " RouterOS Upgrade is completed, new version: v.$deviceOsVerInst!");
	:set mailPoruka 	  	"RouterOS i routerboard upgrade proces je zavrsio. \r\nNoca RouterOS verzija: v.$deviceOsVerInst, routerboard firmware: v.$deviceRbCurrentFw. \r\n$changelogUrl \r\n\r\nBackup fajlovisistema se nalaze u prilogu email-a.  $mailPorukaDeviceInfo $mailPorukaCopyright";
	:set mailPrilog [$buGlobalFuncCreateBackups backupName=$backupNameAfterUpd backupPassword=$backupPassword sensetiveDataInConfig=$sensetiveDataInConfig];
}

:do {/system script environment remove buGlobalFuncGetOsVerNum;} on-error={}
:do {/system script environment remove buGlobalFuncCreateBackups;} on-error={}

:if ($isSendEmailRequired = true) do={
	:log info "$SMP Saljem email, trajat ce otprilike pola minute...";
	:do {/tool e-mail send to=$emailAdresa subject=$mailSubjekt body=$mailPoruka file=$mailPrilog;} on-error={
		:delay 5s;
		:log error "$SMP nije moguce poslati email ($[/tool e-mail get last-status]). Pokusat cu ponovo za par sekundi."

		:delay 5m;

		:do {/tool e-mail send to=$emailAdresa subject=$mailSubjekt body=$mailPoruka file=$mailPrilog;} on-error={
			:delay 5s;
			:log error "$SMP nije moguce poslati email ($[/tool e-mail get last-status]) po drugi put."

			if ($potrebnoUpdateovati = true) do={
				:set potrebnoUpdateovati false;
				:log warning "$SMP skripta se nece izvrsiti jer nije u mogucnosti poslati email."
			}
		}
	}

	:delay 30s;

	:if ([:len $mailPrilog] > 0 and [/tool e-mail get last-status] = "succeeded") do={
		:log info "$SMP ciscenje fajl sistema."
		/file remove $mailPrilog;
		:delay 2s;
	}

}

if ($potrebnoUpdateovati = true) do={

	/system schedule add name=BANANAUP-NAREDNI-BOOT on-event=":delay 5s; /system scheduler remove BANANAUP-NAREDNI-BOOT; :global buGlobalVarUpdateStep 2; :delay 10s; /system script run bananaUP;" start-time=startup interval=0;

   :log info "$SMP sve spremno za instalaciju novog RouterOS-a, reboot-ujem uskoro!"
	/system package update install;
}

:log info "$SMP skripta \"Mikrotik RouterOS automatski backup i update\" je zavrsila posao. \r\n";
```

{% note danger no-icon %}
**Napomena!**
- Postavite ispravnu email adresu `emailAdresa` za backup fajlove i update poruke.
- Obratite paznju i izaberite Mod Skripte `modSkripte` koji preferirate.
{% endnote %}

### Kreiranje nove skripte na ruteru

```routeros
system script add name="bananaUP" source="[zaljepi skriptu ovdje]"
```
{% note danger no-icon %}
**Napomena!**
- Sa konfiguracijom gore, skriptu trebate nazvati **bananaUP** kako bi ispravno radila.
{% endnote %}

### Konfiguracija email servera

```routeros
/tool e-mail \
set address=mail.smtp2go.com port=2525 start-tls=yes \
from=ruter@domena.space user=user2go password=password2go
```

Postavite email server parametre. Ako nemate mail server, koristite [smtp2go.com](https://smtp2go.com/) servis i postavite e-mail kao u primjeru gore. Preko smtp2go dopušteno slanje 1000 besplatnih mailova mjesečno, isprobao - dobro funkcioniše.

Provjerite postavke, pošaljite test mail iz terminala sljedećom komandom; izmjenite `to`, `subject` & `body` po želji:

```routeros
tool e-mail \
send to="sxt@banana.ba" subject="Test banana!" body="Vozdrica! ...samo testiram!";
```

Ako je ispravno konfigurisan `e-mail` stići će vam poruka na unešenu `to=`
email adresu:

<img src="https://static.monkeyshub.space/fragments/sky/backup-5.png" title="email" label="email" style="width:100vw;" />

### Kreiranje Shedulera

```routeros sedmični sheduler u 16:10h
/system scheduler \
add name="Periodicni updater" on-event="/system script run bananaUP;" start-time=16:10:00 interval=7d comment="" disabled=no
```

## Testiranje skripte

Nakon što sve odradite pokrenite skriptu terminal komandom:

```routeros
/system script run bananaUP
```

Proces skripte možete pratiti u `log`-u. Ako skripta prođe bez greške provjerite email inbox i pronaći ćete novu poruku sa prikačenim backup fajlovima koju ste dobili od svog Mikrotik rutera.
