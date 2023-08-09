# Diablo4VM

This is a quick and dirty project to get Diablo 4 running in a HyperV Mirtual machine on windows 10 or 11.

# Prerequisites:

* Windows 10 20H1+ Pro, Enterprise or Education OR Windows 11 Pro, Enterprise or Education. Windows 11 on host and VM is preferred due to better compatibility.
* Matched Windows versions between the host and VM. Mismatches may cause compatibility issues, blue-screens, or other issues. (Win10 21H1 + Win10 21H1, or Win11 21H2 + Win11 21H2, for example)
* Desktop Computer with dedicated NVIDIA GPU. GPU must support hardware video encoding (NVIDIA NVENC).
* Latest GPU driver from I NVIDIA.com, don't rely on Device manager or Windows update.
* Latest Windows 10 ISO [downloaded from here](https://www.microsoft.com/en-gb/software-download/windows10ISO "Downloaded from here") / Windows 11 ISO [downloaded from here](https://www.microsoft.com/en-us/software-download/windows11 "downloaded from here"). - Do not use Media Creation Tool, if no direct ISO link is available, follow [this guide](https://www.nextofwindows.com/downloading-windows-10-iso-images-using-rufus).
* Virtualisation enabled in the motherboard and Hyper-V fully enabled on the Windows 10/ 11 OS (requires reboot).
* Allow Powershell scripts to run on your system - typically by running "Set-ExecutionPolicy unrestricted" in Powershell running as Administrator.

# Instructions
1. Make sure your system meets the prerequisites.
2. Download the Repo and extract.
3. Search your system for Powershell ISE and run as Administrator.
4. In the extracted folder you downloaded, open PreChecks.ps1 in Powershell ISE. Run the files from within the extracted folder. Do not move them.
5. Open and Run PreChecks.ps1 in Powershell ISE using the green play button and copy the GPU Listed (or the warnings that you need to fix).
6. Open CopyFilesToVM.ps1 Powershell ISE and edit the params section at the top of the file, you need to be careful about how much ram, storage and hard drive you give it as your system needs to have that available. On
7. Windows 10 the GPUName must be left as "AUTO", In Windows 11 it can be either "AUTO" or the specific name of the GPU you want to partition exactly how it appears in PreChecks.ps1. Additionally, you need to provide the path to the Windows 10/11 ISO file you downloaded.
8. Run CopyFilesToVM.ps1 with your changes to the params section - this may take 5-10 minutes.
9. Open and sign into Parsec on the VM. You can use Parsec to connect to the VM up to 4K60FPS.
10. On the host PC, Open RegEdit
11. Navigate to Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\
12. Export the nvlddmkm key to a file, you can call it whatever but I'll call it nvlddmkm.reg
13. copy the resulting registery file nvlddmkm.reg to the VM's disk somehow (either mount the disk while it's offline, or use file sharing)
14. On the VM, Install the nvlddmkm.reg
15. Install Battle.net and Diablo
16. Run Diablo
