docker-cookbooks
================

simple recipes for docker containers and commands

alias rdc='docker rmi $(docker images --filter dangling=true -q 2>/dev/null) 2>/dev/null'
alias rdi='docker rm -v $(docker ps --filter status=exited -q 2>/dev/null) 2>/dev/null'

