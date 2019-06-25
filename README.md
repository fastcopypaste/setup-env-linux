# setup-env-linux

### 1. Create swap space

The first we need to check if `swap` is enabled or not
```
swapon --show
```
If the output is blank then it is good to following the instruction.

a. Create a swap file:

```
sudo fallocate -l {MemoryNumber}G /swapfile
```

Where {MemoryNumber} is about double the amound of your RAM.

You can check the swap file was created by type: ```ls -lh /swapfile```

b. Configure the swap file:

```
sudo chmod 600 /swapfile
sudo mkswap /swapfile
```

The we can enable the swap file:

```
sudo swapon /swapfile
```

Verify it by:

```
sudo swapon --show
```
or
```
free -m
```
