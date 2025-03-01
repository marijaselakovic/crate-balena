# Crate database running on balena

This project deploys [CrateDB](https://crate.io/) on [balena.io](https://balena.io). 

It runs on an ARM device such as Raspberry Pi 4 or x86 generic 64 bits device.

## Requirements

### Hardware

* Raspberry Pi 4
* SD card 
* Power supply and (optionally) ethernet cable

### Software

* a balenaCloud account ([sign up here](https://dashboard.balena-cloud.com/))
* [Etcher](https://balena.io/etcher)

## Deploy the code

### Via [Balena Deploy](https://www.balena.io/docs/learn/deploy/deploy-with-balena-button/)

Running this project is as simple as deploying it to a balenaCloud application. You can do it in just one click by using the button below:

[![](https://www.balena.io/deploy.png)](https://dashboard.balena-cloud.com/deploy?repoUrl=https://github.com/mpous/crate-balena)

Follow instructions, click Add a Device and flash an SD card with that OS image dowloaded from balenaCloud. Enjoy the magic 🌟Over-The-Air🌟!


### Via [Balena-Cli](https://www.balena.io/docs/reference/balena-cli/)

If you are a balena CLI expert, feel free to use balena CLI.

- Sign up on [balena.io](https://dashboard.balena.io/signup)
- Create a new application on balenaCloud.
- Clone this repository to your local workspace.
- Using [Balena CLI](https://www.balena.io/docs/reference/cli/), push the code with `balena push <application-name>`
- See the magic happening, your device is getting updated 🌟Over-The-Air🌟!

## Run crateDB on your Raspberry Pi

### Increment max_map_count

To run crate database on your Raspberry Pi, first of all you will need to open `HostOS Terminal` and type:

`sysctl -w vm.max_map_count=262144`

This will increment the `max_map_count`, which is the maximum number of memory map areas a process may have of your container. It's by default  65536 as most of the applications need less than a thousand maps.

At this point, on the balenaCloud `Logs` crate database should start correctly.

### Install CrateDB Shell Crash

Now it's time to install CrateDB Shell CLI (also known as [Crash](https://crate.io/docs/crate/crash/en/0.27/getting-started.html#standalone)).

Open on the `cratedb` services shh Terminal on balenaCloud and type:

`curl -o crash https://cdn.crate.io/downloads/releases/crash_standalone_latest`

then

`chmod +x crash`

and now to launch the shell from Crash, we will need to get the internal http address of the CrateDB service. You will be able to find it on the logs:

```

25.03.22 09:39:21 (+0000)  cratedb  [2022-03-25T09:39:21,660][INFO ][o.e.c.s.MasterService    ] [cratedb] elected-as-master ([1] nodes joined)[{cratedb}{yQDvRiYaSJqliB00mI0AmQ}{uh7fdJlSTMKuX0co00pXoA}{172.18.0.2}{172.18.0.2:4300}{http_address=172.18.0.2:4200} elect leader, _BECOME_MASTER_TASK_, _FINISH_ELECTION_], term: 1, version: 1, reason: master node changed {previous [], current [{cratedb}{yQDvRiYaSJqliB00mI0AmQ}{uh7fdJlSTMKuX0co00pXoA}{172.18.0.2}{172.18.0.2:4300}{http_address=172.18.0.2:4200}]}

```

The address we are going to use will be `http_address=172.18.0.2:4200`.


![crash-cratedb-balena](https://user-images.githubusercontent.com/173156/160127035-a582065e-b518-4fdd-9d35-f2def5aaf48a.png)


Type again on the CrateDB Terminal and paste your `http_adress` where it says `<your_http_addres>`. In my case it was `172.18.0.2`.

`./crash --host "http://<your_http_address>:4200" `

Add `--verbose` in case you want to know more, or you get a connection error.

### Access to the CrateDB UI

Copy your local IP address and paste it into your browser with the port 4201 and that might open the CrateDB UI.

![crate-ui-balena](https://user-images.githubusercontent.com/173156/160127077-a720e83f-4c16-4251-b220-26782072ec6e.png)


