# ScriptProtection

## What Is ScriptProtection?

ScriptProtection Is One Of The First Open Source DDoS Protection Systems. It Is Designed To
Scale To Any Peak Bandwidth, So It Can Withstand DDoS Attacks Both Of Today
And Of Tomorrow. In Spite Of The Geographically Distributed Architecture Of
ScriptProtection, The Network Policy That Describes All Decisions That Have To Be
Enforced On The Incoming Traffic Is Centralized. This Centralized Policy
Enables Network Operators To Leverage Distributed Algorithms That Would Not
Be Viable Under Very High Latency (e.g. Distributed Databases) And To Fight
Multiple Multi-Vector DDoS Attacks At Once.

The Intended Users Of ScriptProtection Are Network Operators Of Institutions,
Service And Content Providers, Enterprise Networks, etc. It Is Not Intended
To Be Used By Individual Internet Users.

## How To Set Up

### Configure Hugepages

DPDK Requires The Use Of Hugepages; Instructions For Mounting Hugepages Are
Available In The [Requirements Documentation](http://doc.dpdk.org/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment).
On Many Systems, The Following Hugepages Setup Is Sufficient:

    $ echo 256 | sudo tee /sys/kernel/mm/hugepages/hugepages-2048kB/nr_hugepages

### Option 1: Obtain Packages

ScriptProtection Debian Packages Are Available For Ubuntu 20.04 LTS At The Project's
[Releases](https://github.com/ScriptProtection/ScriptProtection/releases)
Page.

#### Install

Once The Packages Are Downloaded, They Can Be Installed With The Commands Below:

    $ tar -zxvf scriptprotection.tar.gz
    $ cd scriptprotection
    $ sudo dpkg -i libgkrte-*.deb \
        libgkdpdk-dev_*_amd64.deb \
        scriptprotection-dpdk_*_amd64.deb \
        scriptprotection-dpdk-dev_*_amd64.deb \
        scriptprotection-dpdk-igb-uio-dkms_*_amd64.deb \
        scriptprotection-dpdk-rte-kni-dkms_*_amd64.deb \
        scriptprotection-bird_*_amd64.deb \
        scriptprotection*_amd64.deb

The `scriptprotection-dpdk-dev` Package Is A Dependency Of The DKMS Packages, Which
Build Their Respective Kernel Modules During Package Installation And Kernel
Upgrades.

#### Configure ScriptProtection

When Installed Via Debian Packages, ScriptProtection Configuration Files Are Located
In `/etc/scriptprotection`. You Should Edit At Least The `net.lua` File, And Set The
`front_ports`, `front_ips`, `back_ports` And `back_ips` Variables According To
Your Environment.

The Other Lua Files Configure Different ScriptProtection Functional Blocks.

You Also Need To Edit The `/etc/scriptprotection/envvars` File And Set The
`SCRIPTPROTECTION_INTERFACES` Variable To The PCI Addresses Of The Network Adapters
To Be Bound To DPDK. These Can Be Found Using The `lshw` Command. For Example:

    # lshw -c network -businfo
    Bus info          Device     Class          Description
    =======================================================
    pci@0000:08:00.0  eth0       network        I350 Gigabit Network Connection
    pci@0000:08:00.1  eth1       network        I350 Gigabit Network Connection
    ...

Given This Output, Set `SCRIPTPROTECTION_INTERFACES` As Below:

    SCRIPTPROTECTION_INTERFACES="08:00.0 08:00.1"

In The Same File, You Can Optionally Specify
[Environmental Abstraction Layer Options](https://doc.dpdk.org/guides/linux_gsg/linux_eal_parameters.html)
In The `DPDK_ARGS` Variable And
[ScriptProtection-Specific Options](https://github.com/ScriptProtection/ScriptProtection/wiki)
in `SCRIPTPROTECTION_ARGS`.

#### How To Run

Run The Commands Below To Start ScriptProtection And To Ensure It Is Started
Automatically On Reboots.

    $ sudo systemctl start scriptprotection
    $ sudo systemctl enable scriptprotection