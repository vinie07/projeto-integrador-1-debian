#  1
https://askubuntu.com/questions/308937/cannot-install-ubuntu-in-virtualbox-due-to-this-kernel-requires-an-x86-64-cpu

o que houve? Debian nao aceita i686 CPU , para resolver isto é (considerando que a vm é x86_64) , vai em configs -> general -> version -> debianx64 

<div align="center">
<img width="300" height="300" alt="image" src="https://i.sstatic.net/2HLZ9.png"/>
</div>


#  2
https://cursos.alura.com.br/forum/topico-sugestao-resolvido-user-is-not-in-the-sudoers-file-this-incident-will-be-reported-259842

o que houve? usuario criado sem acesso ao root
1.  `su root`
2.  `suno nano /etc/sudoers`
3.  Na linha `a%sudo ALL=(ALL:ALL) ALL`
4.  escreva em baixo dela: `nomeUsuario ALL=(ALL) ALL`
5. ctrl + o + ctrl + x + `exit`
