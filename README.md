# Homework2
1. Vagrant при загрузке создает автоматически RAID1 и выводит информацию.

			    otuslinux: Personalities : [raid1] 
			    otuslinux: md0 : active raid1 sde[3] sdd[2] sdc[1] sdb[0]
			    otuslinux:       254976 blocks super 1.2 [4/4] [UUUU]
			    otuslinux:       [>....................]  resync =  1.4% (3584/254976) finish=1.1min speed=3584K/sec
			    otuslinux:       
			    otuslinux: unused devices: <none>


2. Работа с RAID
  			
			1. Фейлим один диск 
			
				[root@otuslinux vagrant]# mdadm /dev/md0 --fail /dev/sde
				mdadm: set /dev/sde faulty in /dev/md0
				[root@otuslinux vagrant]# cat /proc/mdstat
				Personalities : [raid1] 
				md0 : active raid1 sde[3](F) sdd[2] sdc[1] sdb[0]
    			254976 blocks super 1.2 [4/3] [UUU_]
     			unused devices: <none>

    		2. Удаляем, монтируем обратно
			
				[root@otuslinux vagrant]# mdadm /dev/md0 --remove /dev/sde
				mdadm: hot removed /dev/sde from /dev/md0
				[root@otuslinux vagrant]#  mdadm /dev/md0 --add /dev/sde
				mdadm: added /dev/sde
				[root@otuslinux vagrant]# cat /proc/mdstat
				Personalities : [raid1] 
				md0 : active raid1 sde[4] sdd[2] sdc[1] sdb[0]
				254976 blocks super 1.2 [4/4] [UUUU]
     			unused devices: <none>
				
3.Создаем GPT раздел, пять партиций и монтируем их на диск
				
				
				[root@otuslinux ~]# parted -s /dev/md0 mklabel gpt
				[root@otuslinux ~]# parted /dev/md0 mkpart primary ext4 0% 20%
				[root@otuslinux ~]# parted /dev/md0 mkpart primary ext4 20% 40% 
				[root@otuslinux ~]# parted /dev/md0 mkpart primary ext4 40% 60%    
				[root@otuslinux ~]# parted /dev/md0 mkpart primary ext4 60% 80%   
				[root@otuslinux ~]# parted /dev/md0 mkpart primary ext4 80% 100% 
				[root@otuslinux ~]# for i in $(seq 1 5); do sudo mkfs.ext4 /dev/md0p$i; done
				mke2fs 1.42.9 (28-Dec-2013)
				Filesystem label=
				OS type: Linux
				Block size=1024 (log=0)
				Fragment size=1024 (log=0)
				Stride=0 blocks, Stripe width=0 blocks
				12544 inodes, 50176 blocks
				2508 blocks (5.00%) reserved for the super user
				First data block=1
				Maximum filesystem blocks=33685504
				7 block groups
				8192 blocks per group, 8192 fragments per group
				1792 inodes per group
				Superblock backups stored on blocks: 
					8193, 24577, 40961

				Allocating group tables: done                            
				Writing inode tables: done                            
				Creating journal (4096 blocks): done
				Writing superblocks and filesystem accounting information: done

				mke2fs 1.42.9 (28-Dec-2013)
				Filesystem label=
				OS type: Linux
				Block size=1024 (log=0)
				Fragment size=1024 (log=0)
				Stride=0 blocks, Stripe width=0 blocks
				12824 inodes, 51200 blocks
				2560 blocks (5.00%) reserved for the super user
				First data block=1
				Maximum filesystem blocks=33685504
				7 block groups
				8192 blocks per group, 8192 fragments per group
				1832 inodes per group
				Superblock backups stored on blocks: 
					8193, 24577, 40961

				Allocating group tables: done                            
			Writing inode tables: done                            
			Creating journal (4096 blocks): done
			Writing superblocks and filesystem accounting information: done

			mke2fs 1.42.9 (28-Dec-2013)
			Filesystem label=
			OS type: Linux
			Block size=1024 (log=0)
			Fragment size=1024 (log=0)
			Stride=0 blocks, Stripe width=0 blocks
			12544 inodes, 50176 blocks
			2508 blocks (5.00%) reserved for the super user
			First data block=1
			Maximum filesystem blocks=33685504
			7 block groups
			8192 blocks per group, 8192 fragments per group
			1792 inodes per group
			Superblock backups stored on blocks: 
				8193, 24577, 40961

			Allocating group tables: done                            
			Writing inode tables: done                            
			Creating journal (4096 blocks): done
			Writing superblocks and filesystem accounting information: done

			mke2fs 1.42.9 (28-Dec-2013)
			Filesystem label=
			OS type: Linux
			Block size=1024 (log=0)
			Fragment size=1024 (log=0)
			Stride=0 blocks, Stripe width=0 blocks
			12824 inodes, 51200 blocks
			2560 blocks (5.00%) reserved for the super user
			First data block=1
			Maximum filesystem blocks=33685504
			7 block groups
			8192 blocks per group, 8192 fragments per group
			1832 inodes per group
			Superblock backups stored on blocks: 
				8193, 24577, 40961

			Allocating group tables: done                            
			Writing inode tables: done                            
			Creating journal (4096 blocks): done
			Writing superblocks and filesystem accounting information: done

			mke2fs 1.42.9 (28-Dec-2013)
			Filesystem label=
			OS type: Linux
			Block size=1024 (log=0)
			Fragment size=1024 (log=0)
			Stride=0 blocks, Stripe width=0 blocks
			12544 inodes, 50176 blocks
			2508 blocks (5.00%) reserved for the super user
			First data block=1
			Maximum filesystem blocks=33685504
			7 block groups
			8192 blocks per group, 8192 fragments per group
			1792 inodes per group
			Superblock backups stored on blocks: 
				8193, 24577, 40961

			Allocating group tables: done                            
			Writing inode tables: done                            
			Creating journal (4096 blocks): done
			Writing superblocks and filesystem accounting information: done

			[root@otuslinux ~]# mkdir -p /raid/part{1,2,3,4,5}
			[root@otuslinux ~]# for i in $(seq 1 5); do mount /dev/md0p$i /raid/part$i; done
			
			
4. Вывод lsblk


			NAME      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
			sda         8:0    0   40G  0 disk  
			└─sda1      8:1    0   40G  0 part  /
			sdb         8:16   0  250M  0 disk  
			└─md0       9:0    0  249M  0 raid1 
			  ├─md0p1 259:0    0   49M  0 md    /raid/part1
			  ├─md0p2 259:1    0   50M  0 md    /raid/part2
			  ├─md0p3 259:2    0   49M  0 md    /raid/part3
			  ├─md0p4 259:3    0   50M  0 md    /raid/part4
			  └─md0p5 259:4    0   49M  0 md    /raid/part5
			sdc         8:32   0  250M  0 disk  
			└─md0       9:0    0  249M  0 raid1 
			  ├─md0p1 259:0    0   49M  0 md    /raid/part1
			  ├─md0p2 259:1    0   50M  0 md    /raid/part2
			  ├─md0p3 259:2    0   49M  0 md    /raid/part3
			  ├─md0p4 259:3    0   50M  0 md    /raid/part4
			  └─md0p5 259:4    0   49M  0 md    /raid/part5
			sdd         8:48   0  250M  0 disk  
			└─md0       9:0    0  249M  0 raid1 
			  ├─md0p1 259:0    0   49M  0 md    /raid/part1
			  ├─md0p2 259:1    0   50M  0 md    /raid/part2
			  ├─md0p3 259:2    0   49M  0 md    /raid/part3
			  ├─md0p4 259:3    0   50M  0 md    /raid/part4
			  └─md0p5 259:4    0   49M  0 md    /raid/part5
			sde         8:64   0  250M  0 disk  
			└─md0       9:0    0  249M  0 raid1 
			  ├─md0p1 259:0    0   49M  0 md    /raid/part1
			  ├─md0p2 259:1    0   50M  0 md    /raid/part2
			  ├─md0p3 259:2    0   49M  0 md    /raid/part3
			  ├─md0p4 259:3    0   50M  0 md    /raid/part4
			  └─md0p5 259:4    0   49M  0 md    /raid/part5
			sdf         8:80   0  250M  0 disk  
			sdg         8:96   0  250M  0 disk  


