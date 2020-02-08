# prime

# DNSMASQ
- `apt-get install dnsmasq`
- Add `conf-dir=/etc/dnsmasq.d/,*.conf` at the end of the file `conf-dir=/etc/dnsmasq.d/,*.conf`
- Create a file `/etc/dnsmasq.d/maas.conf`
```
port=0

log-dhcp

dhcp-no-override

dhcp-boot=pxelinux.0,pxeserver,<ip-pxe-server>

pxe-prompt="Booting FOG Client", 1

dhcp-range=<subnet>,proxy
```
Example :
```
port=0

log-dhcp

dhcp-no-override

dhcp-boot=pxelinux.0,pxeserver,192.168.1.34

pxe-prompt="Booting FOG Client", 1

dhcp-range=192.168.1.0,proxy
```

# LOGIN TO MAAS
```
maasadminuser="<user>"
maas login $maasadminuser http://localhost:5240/MAAS $(maas apikey --username=$maasadminuser)
```
# ADD DEBIAN IMG TO MAAS
- Download raw  `http://cdimage.debian.org/cdimage/openstack/current/`
- Push the image to maas after login `maas <user> boot-resources create name=custom/debian title="debian-10.2.1" architecture=amd64/generic content@=debian-10.2.1-20200204-openstack-amd64.raw
`
# WINDOWS IMG TO MAAS

```
Source: https://www.cryingcloud.com/blog/2018/09/18/creating-a-custom-windows-image-for-maas?rq=maas


Continuing on the Maas theme from yesterday, I thought I'd put up a post about my experience with Windows imaging for MaaS (https://github.com/cloudbase/windows-openstack-imaging-tools). The Windows Openstack Imaging Tools are great for creating Windows images for Maas. They provide support for creating gold images, custom drivers, and pretty much anything else you'd want for custom image creation.

Included is support for UEFI or BIOS boot options. This is where I ran into issues. I found that Hyper-V wasn't having secureboot turned off for the VM, causing BIOS builds to fail. I found the problem and fixed it relatively easily. After successfully creating a BIOS image, and finding it wouldn't install on most of our equipment due to it being configured for UEFI, I decided to create a UEFI image.

Creating a UEFI image errored out, and so the hunt for the issue began. I won't bore you with the tedium of digging through code to figure out where the problem lay, but there are two changes that need to be made to the PS module that does most of the heavy lifting in the solution.

In the install directory, there is a file called WinImageBuilder.psm1. The first fix is to insert the following code right after line 902.


if ($DiskLayout -eq "UEFI")         {         $Drive = $Drive[1]         }

The solution doesn't treat the disk layout as an array if UEFI is selected, so naturally the disk optomization phase fails.

Following that, the next change is after line 1006. Insert the following code:


if ($DiskLayout -ne "UEFI")         {         Set-VMFirmware -VMName (Get-VM).Name -EnableSecureBoot Off         }

Turning off secureboot enables BIOS builds to succeed.

Once these fixes are in place, you can set your variables and have a successful build .
```
