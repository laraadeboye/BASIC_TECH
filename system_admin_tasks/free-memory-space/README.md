# Swap memory
When memory is a constraint in a server where memory is used up fast, the following these steps will create and activate a swap file which helps to manage memory more effectively on your system. This can prevent issues if your physical memory is exhausted especially when running memory-intensive applications and you are low on memory. Swap can act as a safety net to handle memory spikes. 
If a system is running low on RAM and doessnt have sufficient swap space, `kswapd`, a virtual management kernel thread is enabled which indicates that the system memory is under memory pressure and is relying heavily on swap space. To limit this, a swapfile can be created on the server.  `htop` or `top` command can help to detect the kswapd activity. (Make sure to also reduce the swappiness of the kernel after creating the swapfile. This is show below.)

## Steps 
*  Check current swap status (No output means that there is no swapfile on the server)

```sh
sudo swapon --show 
```
* Create swapfile:

```sh
sudo fallocate -l 2G /swapfile  # creates a 2G swapfile
```

or 

```sh
sudo dd if=/dev/zero of=/swapfile bs=1M count=2048
```


* Create root permissions for the swap file:

```sh
sudo chmod 600 /swapfile
```


* Set up the swap space:

```sh
sudo mkswap /swapfile
```

* Enable the swap

```sh
sudo swapon /swapfile
```

* Verify the swap status

```sh
sudo swapon --show
free -h
```
* Make the swap permanent and persist after a reboot.

```sh
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

* Optionally, you can adjust the swappiness i.e how often the system uses swap space by modifying the swappiness value. Default is 6o. Lowering it makes the system less likely to use swap.

```sh
sudo sysctl vm.swappiness=10
```
Make it permanent by adding the following line to `/etc/sysctl.conf`:

```sh
vm.swappiness=10
```

