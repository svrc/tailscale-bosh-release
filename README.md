# Tailscale bosh addon


## What does this do?

Allows BOSH VMs in Tanzu Platform for Cloud Foundry (tPCF) to enroll and participate in a Tailnet, and/or act as a subnet router.

## How do I install it?

1. Open a shell prompt on a BOSH CLI workstation with network access to your PKS bosh director.  Eg. Ops Manager .
2. Export your BOSH credentials to the enviornment.  These can be accessed via the Ops Manager GUI -> BOSH Director Tile -> Credentials Tab -> Bosh Commandline Credentials.    
e.g.
```
export BOSH_CLIENT=ops_manager BOSH_CLIENT_SECRET=fakesecret BOSH_CA_CERT=/var/tempest/workspaces/default/root_ca_certificate  BOSH_ENVIRONMENT=10.0.0.10
```
3. Copy or clone this repository onto this BOSH CLI workstation and create+upload the BOSH release to the director

```
git clone https://github.com/svrc/tailscale-bosh-release && cd tailscale-bosh-release
bosh create-release
bosh upload-release ./dev_releases/tailscale/tailscale-0+dev.1.yml 

```
4. Configure the addon from this repo.  You'll want to edit `tailscale-addon.yml` to add your auth key, tags, and subnet routes you want to advertise.
```
bosh -n update-config --name=tailscale --type=runtime ./tailscale-addon.yml
```
5. Update your tPCF foundation via the TKGI CLI and/or Ops Manager "Apply Pending Changes" button with the various upgrade errand enabled.  This addon will automatically be installed on all nodes in your foundation with the default addon config `tailscale-addon.yml`.


Tailscale is a registered trademark of Tailscale Inc.  WireGuard is a registered trademark of Jason A. Donenfeld.

The BOSH addon is BSD licensed from Broadcom, Inc.  Tailscale itself is BSD Licensed from Tailscale, Inc. 
