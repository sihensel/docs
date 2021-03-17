
# Pacman

Command | Meaning
--- | ---
pacman -S | Install package
pacman -Syy | Sync package database
pacman -Syu | Upgrade whole system
pacman -Ss | search package
pacman -R | remove package
pacman -Rs | remove package with dependencies
pacman -Rns | remove system config files as well
pacman -Q | all installed packages
pacman -Qe | all explicitly installed packages
pacman -Qq | all installed packages without versions
pacman -Qn | packges from main repositories
pacman -Qm | all AUR packages
pacman -Qdt | unneeded dependencies 
pacman -Qs | search for local packages
pacman -Sc | remove old packages from the cache


Uncomment the line `Color` in `/etc/pacman.conf` to add colors to the pacman output.
