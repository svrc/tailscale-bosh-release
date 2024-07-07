# Tailscale bosh addon


## What does this do?

Allows BOSH VMs in Tanzu Platform for Cloud Foundry (tPCF) to enroll and participate in a Tailnet, and/or act as a subnet router.

## Prerequisties

On your Tailscale admin console,

1. Create an Auth Key via Settings -> Personal Settings -> Keys , and then "Generate auth key...".   Mark it as Ephemeral and Reusable.   This can also be done in an automated fashion via REST API, the details of which are left as an exercise for the reader.

2. Register tags you want to us with your BOSH VMs in the Access Control tab.   If you want to auto approve a subnet route, you'll also need to configure that.

Example of registering tags that allow auth keys created by admin users
```
	"tagOwners": {
		"tag:aws-tpcf": ["autogroup:admin"],
		"tag:bosh-router":   ["autogroup:admin"],
	},
```
Example of auto approving subnets
```
"autoApprovers": {
		"routes": {
			"10.0.4.0/24": ["tag:bosh-router"],
		},
	},
```

3.  You can also use tags to control east-west firewalls within the Tailnet, via the "acls" section, see the Tailscale docs for more info.  

As BOSH VMs are created with the Tailscale addon & an appropriate auth key, they will appear by their BOSH agent UUID in the *Machines* tab on your Admin console.  Old machines (after stemcell upgrades or VM recreates) will disappear after a few minutes if your Auth Key is marked ephemeral.

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

5. Update your tPCF foundation via the TKGI CLI and/or Ops Manager "Apply Pending Changes" button with the various upgrade errand enabled.  


Tailscale is a registered trademark of Tailscale Inc.  WireGuard is a registered trademark of Jason A. Donenfeld.

The BOSH addon is BSD licensed from Broadcom, Inc.  Tailscale itself is BSD Licensed from Tailscale, Inc. 
