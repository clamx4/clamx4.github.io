# .zshrc
```zsh
export PATH=$PATH:~/bin

alias ll='ls -l'
alias ls='ls -G'

autoload -Uz compinit && compinit

parse_git_branch() {
  git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/^\* \(.*\)/(\1)/'
}
setopt PROMPT_SUBST
export PROMPT='z@%1~%F{yellow}$(parse_git_branch)%f %(!.#.$) '
```
