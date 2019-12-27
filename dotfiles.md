Create a dotfile repo

`$ git init --bare ~/.dotfiles`

Add alias

`$ alias dotconf='/usr/bin/git --git-dir=$HOME/.dotfiles/ --work-tree=$HOME'`

Dont show untracked files

`$ config config status.showUntrackedFiles no`

Add remote

`$ dotconf remote add origin git@github.com:ivanvig/dotfiles.git`
