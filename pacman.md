
# Pacman

Command | Meaning
--- | ---
pacman -S | Install package
pacman -Syy | Sync package database
pacman -Syu | Upgrade whole system
pacman -Ss | search package
pacman -R | remove package
pacman -Rs | remove package with dependencies
pacman -Rnscd | remove package and all that belongs to it
pacman -Q | all installed packages
pacman -Qe | all explicitly installed packages
pacman -Qq | all installed packages without versions
pacman -Qn | packges from main repositories
pacman -Qm | all AUR packages
pacman -Qdtq | list unneeded dependencies 
pacman -Qs | search for local packages
pacman -Sc | remove old packages from the cache
yay -Yc | remove unneded packages (alternatively, use `sudo pacman -Rns $(pacman -Qtdq)`)
yay -Syu --devel | upgrade all AUR packages


Uncomment the line `Color` in `/etc/pacman.conf` to add colors to the pacman output.
