# Diablo4VM

This is a quick and dirty project to get Diablo 4 running in a HyperV Mirtual machine on windows 10 or 11 with a Nvidia GPU.

# Prerequisites:

* Windows 10 20H1+ Pro, Enterprise or Education OR Windows 11 Pro, Enterprise or Education. Windows 11 on host and VM is preferred due to better compatibility.
* Matched Windows versions between the host and VM. Mismatches may cause compatibility issues, blue-screens, or other issues. (Win10 21H1 + Win10 21H1, or Win11 21H2 + Win11 21H2, for example) (I personally haven't had an issue win 11 -> win 10 VM but your milage may vary)
* Desktop Computer with dedicated NVIDIA GPU. GPU must support hardware video encoding (NVIDIA NVENC).
* Latest GPU driver from I NVIDIA.com, don't rely on Device manager or Windows update.
* Latest Windows 10 ISO [downloaded from here](https://www.microsoft.com/en-gb/software-download/windows10ISO "Downloaded from here") / Windows 11 ISO [downloaded from here](https://www.microsoft.com/en-us/software-download/windows11 "downloaded from here"). - Do not use Media Creation Tool, if no direct ISO link is available, follow [this guide](https://www.nextofwindows.com/downloading-windows-10-iso-images-using-rufus).
* Virtualisation enabled in the motherboard and [Hyper-V fully enabled](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) on the Windows 10/ 11 OS (requires reboot).
* Allow Powershell scripts to run on your system - typically by running "Set-ExecutionPolicy unrestricted" in Powershell running as Administrator.

# Instructions
1. Make sure your system meets the prerequisites.
2. Download the Repo and extract.
3. Search your system for Powershell ISE and run as Administrator.
4. In the extracted folder you downloaded, open PreChecks.ps1 in Powershell ISE. Run the files from within the extracted folder. Do not move them.
5. Open and Run PreChecks.ps1 in Powershell ISE using the green play button and copy the GPU Listed (or the warnings that you need to fix).
6. Open CopyFilesToVM.ps1 Powershell ISE and edit the params section at the top of the file, you need to be careful about how much ram, storage and hard drive you give it as your system needs to have that available.
7. On Windows 10 the GPUName must be left as "AUTO", In Windows 11 it can be either "AUTO" or the specific name of the GPU you want to partition exactly how it appears in PreChecks.ps1. Additionally, you need to provide the path to the Windows 10/11 ISO file you downloaded.
8. Run CopyFilesToVM.ps1 with your changes to the params section - this may take 5-10 minutes.
9. Open and sign into Parsec on the VM. You can use Parsec to connect to the VM up to 4K60FPS. (optional)
10. Turn off the VM and go to the VM settings on host.
11. Hit security and uncheck the enable secure boot checkbox.
12. On the host PC, Open RegEdit.
13. Navigate to Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\
14. Export the nvlddmkm key to a file, you can call it whatever but I'll call it nvlddmkm.reg
15. copy the resulting registery file nvlddmkm.reg to the VM's disk somehow (either mount the disk while it's offline, or use file sharing)
16. On the VM, Install the nvlddmkm.reg
17. Install Battle.net and Diablo
18. Run Diablo

# Upgrading GPU Drivers when you update the host GPU Drivers
It's important to update the VM GPU Drivers after you have updated the Host GPUs drivers. You can do this by...

1. Reboot the host after updating GPU Drivers.
2. Open Powershell as administrator and change directory (CD) to the path that CopyFilesToVM.ps1 and Update-VMGpuPartitionDriver.ps1 are located.
3. Run Update-VMGpuPartitionDriver.ps1 -VMName "Name of your VM" -GPUName "Name of your GPU" (Windows 10 GPU name must be "AUTO")

# Variables

Variable  | Description
------------- | -------------
VMName = "Diablo"  | Name of VM in Hyper-V and the computername / hostname
SourcePath = "C:\Users\xxxx\Downloads\Win11_English_x64.iso"  | path to Windows 10/ 11 ISO on your host
Edition    = 6  | Leave as 6, this means Windows 10/11 Pro
VhdFormat  = "VHDX"  | Leave this value alone
DiskLayout = "UEFI"  | Leave this value alone
SizeBytes  = 100gb  | Disk size, in this case 100GB, the minimum is 20GB
MemoryAmount = 8GB   | Memory size, in this case 8GB
CPUCores = 8  | CPU Cores you want to give VM, in this case 8
NetworkSwitch = "Default Switch"  | Leave this alone unless you're not using the default Hyper-V Switch
VHDPath = "C:\Users\Public\Documents\Hyper-V\Virtual Hard Disks\"  | Path to the folder you want the VM Disk to be stored in, it must already exist
UnattendPath = "$PSScriptRoot"+"\autounattend.xml"  | Leave this value alone
PUName = "AUTO"  | AUTO selects the first available GPU. On Windows 11 you may also use the exact name of the GPU you want to share with the VM in multi GPU situations (GPU selection is not available in Windows 10 and must be set to AUTO)
GPUResourceAllocationPercentage = 50  | Percentage of the GPU you want to share with the VM
Team_ID = ""  | The Parsec for Teams ID if you are a Parsec for Teams Subscriber
Key = ""  | The Parsec for Teams Secret Key if you are a Parsec for Teams Subscriber
Username = "Username"  | The VM Windows Username, do not include special characters, and must be different from the "VMName" value you set
Password = "CoolestPassword!"  | The VM Windows Password, cannot be blank
Autologon = "true"  | If you want the VM to automatically login to the Windows Desktop

# Notes

* After you have signed into Parsec on the VM, always use Parsec to connect to the VM. Keep the Microsft Hyper-V Video adapter disabled. Using RDP and Hyper-V Enhanced Session mode will result in broken behaviour and black screens in Parsec. RDP and the Hyper-V video adapter only offer a maximum of 30FPS. Using Parsec will allow you to use up to 4k60 FPS.
* If you get "ERROR : Cannot bind argument to parameter 'Path' because it is null." this probably means you used Media Creation Tool to download the ISO. You unfortunately cannot use that, if you don't see a direct ISO download link at the Microsoft page, follow [this guide.](https://www.nextofwindows.com/downloading-windows-10-iso-images-using-rufus)
* Your GPU on the host will have a Microsoft driver in device manager, rather than an nvidia/intel/amd driver. As long as it doesn't have a yellow triangle over top of the device in device manager, it's working correctly.
* A powered on display / HDMI dummy dongle must be plugged into the GPU to allow Parsec to capture the screen. You only need one of these per host machine regardless of number of VM's.
* If your computer is super fast it may get to the login screen before the audio driver (VB Cable) and Parsec display driver are installed, but fear not! They should soon install.
* The screen may go black for times up to 10 seconds in situations when UAC prompts appear, applications go in and out of fullscreen and when you switch between video codecs in Parsec - not really sure why this happens, it's unique to GPU-P machines and seems to recover faster at 1280x720.
* Vulkan renderer is unavailable and GL games may or may not work. [This](https://www.microsoft.com/en-us/p/opencl-and-opengl-compatibility-pack/9nqpsl29bfff?SilentAuth=1&wa=wsignin1.0#activetab=pivot:overviewtab) may help with some OpenGL apps.
* If you do not have administrator permissions on the machine it means you set the username and vmname to the same thing, these needs to be different.
* To download Windows ISOs with Rufus, it must have "Check for updates" enabled.

# Diablo4VM (German - I used Chat GPT don't know if its right.)

Dies ist ein schnelles und einfaches Projekt, um Diablo 4 in einer Hyper-V-Virtual Machine unter Windows 10 oder 11 mit einer Nvidia-GPU zum Laufen zu bringen.

## Voraussetzungen:

- Windows 10 20H1+ Pro, Enterprise oder Education ODER Windows 11 Pro, Enterprise oder Education. Windows 11 auf Host und VM wird aufgrund besserer Kompatibilität empfohlen.
- Übereinstimmende Windows-Versionen zwischen Host und VM. Abweichungen können zu Kompatibilitätsproblemen, Blue Screens oder anderen Fehlern führen. (z.B. Win10 21H1 + Win10 21H1 oder Win11 21H2 + Win11 21H2) (Ich persönlich hatte kein Problem mit Win 11 -> Win 10 VM, aber es kann variieren.)
- Desktop-Computer mit dedizierter NVIDIA-GPU. Die GPU muss Hardware-Videocodierung unterstützen (NVIDIA NVENC).
- Neuester GPU-Treiber von NVIDIA.com, verlassen Sie sich nicht auf den Geräte-Manager oder Windows Update.
- Neueste Windows 10 ISO [von hier heruntergeladen](https://www.microsoft.com/de-de/software-download/windows10ISO) / Windows 11 ISO [von hier heruntergeladen](https://www.microsoft.com/de-us/software-download/windows11). - Verwenden Sie nicht das Media Creation Tool. Falls kein direkter ISO-Link verfügbar ist, folgen Sie [dieser Anleitung](https://www.nextofwindows.com/downloading-windows-10-iso-images-using-rufus).
- Virtualisierung aktiviert im Motherboard und [Hyper-V vollständig aktiviert](https://docs.microsoft.com/de-de/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) auf Windows 10/11 OS (erfordert Neustart).
- Erlauben Sie PowerShell-Skripts auf Ihrem System auszuführen - normalerweise indem Sie "Set-ExecutionPolicy Unrestricted" in PowerShell als Administrator ausführen.

## Anleitung
1. Stellen Sie sicher, dass Ihr System die Voraussetzungen erfüllt.
2. Laden Sie das Repository herunter und extrahieren Sie es.
3. Suchen Sie auf Ihrem System nach PowerShell ISE und führen Sie es als Administrator aus.
4. Öffnen Sie im extrahierten Ordner, den Sie heruntergeladen haben, die Datei PreChecks.ps1 in PowerShell ISE. Führen Sie die Dateien innerhalb des extrahierten Ordners aus. Verschieben Sie sie nicht.
5. Öffnen und führen Sie PreChecks.ps1 in PowerShell ISE aus, indem Sie auf den grünen Abspielen-Button klicken, und kopieren Sie die aufgelistete GPU (oder die Warnungen, die Sie beheben müssen).
6. Öffnen Sie CopyFilesToVM.ps1 in PowerShell ISE und bearbeiten Sie den Abschnitt "params" oben in der Datei. Sie müssen darauf achten, wie viel RAM, Speicherplatz und Festplattenspeicher Sie der VM zuweisen, da Ihr System das zur Verfügung stellen muss.
7. Unter Windows 10 muss der Wert von GPUName als "AUTO" belassen werden. Unter Windows 11 kann er entweder "AUTO" oder der genaue Name der GPU sein, die Sie partitionieren möchten, genau wie es in PreChecks.ps1 angezeigt wird. Geben Sie außerdem den Pfad zur Windows 10/11 ISO-Datei an, die Sie heruntergeladen haben.
8. Führen Sie CopyFilesToVM.ps1 mit Ihren Änderungen im Abschnitt "params" aus - das kann 5-10 Minuten dauern.
9. Öffnen Sie Parsec auf der VM und melden Sie sich an. Sie können Parsec verwenden, um sich mit der VM zu verbinden, mit bis zu 4K60FPS. (optional)
10. Schalten Sie die virtuelle Maschine aus und gehen Sie zu den VM-Einstellungen auf dem Host.
11. Klicken Sie auf "Sicherheit" und deaktivieren Sie das Kontrollkästchen für "Sicheres Starten aktivieren".
12. Auf dem Host-PC, öffnen Sie die Registrierung (RegEdit).
13. Navigieren Sie zu Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\
14. Exportieren Sie den Schlüssel "nvlddmkm" in eine Datei. Sie können sie beliebig benennen, aber ich werde sie "nvlddmkm.reg" nennen.
15. Kopieren Sie die resultierende Registrierungsdatei "nvlddmkm.reg" auf die Festplatte der VM, entweder indem Sie die Festplatte offline einbinden oder Dateifreigabe verwenden.
16. Installieren Sie "nvlddmkm.reg" auf der VM.
17. Installieren Sie Battle.net und Diablo.
18. Starten Sie Diablo.

## Aktualisieren der GPU-Treiber beim Aktualisieren der Host-GPU-Treiber
Es ist wichtig, die VM-GPU-Treiber zu aktualisieren, nachdem Sie die Treiber der Host-GPUs aktualisiert haben. Dies können Sie folgendermaßen tun:

1. Starten Sie den Host nach dem Aktualisieren der GPU-Treiber neu.
2. Öffnen Sie PowerShell als Administrator und ändern Sie das Verzeichnis (CD) zum Pfad, in dem sich CopyFilesToVM.ps1 und Update-VMGpuPartitionDriver.ps1 befinden.
3. Führen Sie Update-VMGpuPartitionDriver.ps1 aus -VMName "Name Ihrer VM" -GPUName "Name Ihrer GPU" (Der Name der GPU unter Windows 10 muss "AUTO" sein.)

## Variablen

Variable  | Beschreibung
------------- | -------------
VMName = "Diablo"  | Name der VM in Hyper-V und der Computername / Hostname
SourcePath = "C:\Users\xxxx\Downloads\Win11_English_x64.iso"  | Pfad zur Windows 10/ 11 ISO auf Ihrem Host
Edition    = 6  | Lassen Sie es bei 6, das bedeutet Windows 10/11 Pro
VhdFormat  = "VHDX"  | Lassen Sie diesen Wert unverändert
DiskLayout = "UEFI"  | Lassen Sie diesen Wert unverändert
SizeBytes  = 100gb  | Größe der Festplatte, in diesem Fall 100 GB, das Minimum sind 20 GB
MemoryAmount = 8GB   | Größe des Arbeitsspeichers, in diesem Fall 8 GB
CPUCores = 8  | Anzahl der CPU-Kerne, die Sie der VM zuweisen möchten, in diesem Fall 8
NetworkSwitch = "Default Switch"  | Lassen Sie das unverändert, es sei denn, Sie verwenden nicht den Standard-Hyper-V-Switch
VHDPath = "C:\Users\Public\Documents\Hyper-V\Virtual Hard Disks\"  | Pfad zum Ordner, in dem die VM-Festplatte gespeichert werden soll. Der Ordner muss bereits existieren
UnattendPath = "$PSScriptRoot"+"\autounattend.xml"  | Lassen Sie diesen Wert unverändert
PUName = "AUTO"  | AUTO wählt die erste verfügbare GPU aus. Unter Windows 11 können Sie auch den genauen Namen der GPU verwenden, die Sie in Multi-GPU-Situationen mit der VM teilen möchten (Die Auswahl der GPU ist unter Windows 10 nicht verfügbar und muss auf AUTO gesetzt sein)
GPUResourceAllocationPercentage = 50  | Prozentsatz der GPU, den Sie mit der VM teilen möchten
Team_ID = ""  | Die Parsec for Teams ID, wenn Sie ein Abonnent von Parsec for Teams sind
Key = ""  | Der geheime Schlüssel von Parsec for Teams, wenn Sie ein Abonnent von Parsec for Teams sind
Username = "Benutzername"  | Der Windows-Benutzername der VM, verwenden Sie keine Sonderzeichen und er muss sich vom Wert "VMName" unterscheiden, den Sie festgelegt haben
Password = "CoolstesPasswort!"  | Das Windows-Passwort der VM, es darf nicht leer sein
Autologon = "true"  | Wenn Sie möchten, dass die VM automatisch in den Windows-Desktop anmeldet

## Hinweise

* Nachdem Sie sich bei Parsec auf der VM angemeldet haben, verwenden Sie immer Parsec, um sich mit der VM zu verbinden. Deaktivieren Sie den Microsoft Hyper-V Video-Adapter. Die Verwendung von RDP und dem Hyper-V Enhanced Session-Modus führt zu fehlerhaftem Verhalten und schwarzen Bildschirmen in Parsec. RDP und der Hyper-V-Video-Adapter bieten nur maximal 30 FPS. Mit Parsec können Sie bis zu 4K60FPS nutzen.
* Wenn Sie die Meldung "FEHLER: Argument kann dem Parameter 'Path' nicht zugewiesen werden, da es null ist." erhalten, bedeutet dies wahrscheinlich, dass Sie das Media Creation Tool verwendet haben, um das ISO herunterzuladen. Leider können Sie das nicht verwenden. Wenn Sie keinen direkten ISO-Downloadlink auf der Microsoft-Seite sehen, folgen Sie [dieser Anleitung](https://www.nextofwindows.com/downloading-windows-10-iso-images-using-rufus).
* Ihre GPU auf dem Host wird einen Microsoft-Treiber im Geräte-Manager haben, anstelle eines NVIDIA-/Intel-/AMD-Treibers. Solange kein gelbes Dreieck über dem Gerät im Geräte-Manager angezeigt wird, funktioniert sie korrekt.
* Ein aktiver Monitor / HDMI-Dummy-Dongle muss in die GPU eingesteckt werden, um Parsec zu ermöglichen, den Bildschirm zu erfassen. Sie benötigen nur einen solchen Dongle pro Host-Maschine, unabhängig von der Anzahl der VMs.
* Wenn Ihr Computer sehr schnell ist, kann der Anmeldebildschirm erscheinen, bevor der Audio-Treiber (VB Cable) und der Parsec-Display-Treiber installiert sind, aber keine Sorge! Sie sollten bald installiert werden.
* Der Bildschirm kann für Zeiträume von bis zu 10 Sekunden schwarz werden, wenn UAC-Prompts angezeigt werden, Anwendungen in den Vollbildmodus wechseln und zwischen Video-Codecs in Parsec wechseln - es ist nicht ganz klar, warum dies passiert, es tritt nur bei GPU-P-Maschinen auf und erholt sich schneller bei 1280x720.
* Der Vulkan-Renderer ist nicht verfügbar und GL-Spiele funktionieren möglicherweise nicht. [Dies](https://www.microsoft.com/de-us/p/opencl-and-opengl-compatibility-pack/9nqpsl29bfff?SilentAuth=1&wa=wsignin1.0#activetab=pivot:overviewtab) könnte bei einigen OpenGL-Anwendungen helfen.
* Wenn Sie keine Administratorberechtigungen auf dem Computer haben, bedeutet dies, dass Sie den Benutzernamen und den VM-Namen auf dasselbe gesetzt haben. Diese müssen unterschiedlich sein.
* Um Windows-ISOs mit Rufus herunterzuladen, muss "Nach Updates suchen" aktiviert sein.
