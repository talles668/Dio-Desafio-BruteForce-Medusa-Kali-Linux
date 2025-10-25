# 🧑‍💻 Simulação de Ataque de Força Bruta com Medusa e Kali Linux

## 🛡️ Introdução 🛡️

Este repositório documenta a simulação de um ataque de **Força Bruta (*Brute Force*)** contra um formulário de login utilizando a ferramenta **Medusa** a partir de um ambiente **Kali Linux**. O alvo da simulação é o **Metasploitable 2**, uma aplicação intencionalmente vulnerável, utilizada para fins de aprendizado e teste de segurança.

O objetivo principal é demonstrar o funcionamento de um ataque de dicionário/força bruta, identificar as vulnerabilidades em aplicações web e, consequentemente, entender as medidas de mitigação necessárias para proteger sistemas de autenticação.

## ⚒️ Ferramentas e Ambiente ⚒️

### - Kali Linux
O Kali Linux é uma distribuição baseada em Debian, projetada para testes de penetração e auditoria de segurança. Ele já vem pré-instalado com centenas de ferramentas, incluindo o Medusa.

### - Medusa
O Medusa é um **auditor de login de rede paralelo e modular**, conhecido por sua velocidade e capacidade de testar múltiplos protocolos e serviços simultaneamente. Para este desafio, o Medusa foi escolhido por sua eficiência em ataques de força bruta baseados em dicionário.

### - Metasploitable 2
Metasploitable 2 é uma máquina virtual deliberadamente vulnerável projetada para fins de teste e treino de segurança. Ela permite que os profissionais de segurança explorem vulnerabilidades em sistemas de forma segura, sem comprometer a segurança real.

## 🕵️‍♂️ Configuração do Ambiente 🕵️‍♂️

Para replicar este ataque, é necessário:

- ☑️  **Máquina Atacante:** Uma máquina virtual com **Kali Linux** instalado.
- ☑️  **Máquina Alvo:** Uma máquina virtual separada **Metasploitable 2** instalado e configurado.
- ☑️  **Conectividade:** As duas máquinas devem estar na mesma rede e ser capazes de se comunicar (ex: rede NAT ou Host-Only no VirtualBox/VMware).
- 
##
## 🔐 1- Ataque ao serviço FTP na Prática 🔐
##

## 1. As etapas do ataque foram executadas de forma sequencial no terminal Zsh, dentro do ambiente Kali Linux. O primeiro passo consistiu na utilização do Nmap para detectar os hosts ativos na rede.

``` bash
nmap -sV -p 21,22,80,445,139 192.168.56.0/24
```
### 2.1 Depois de localizar o alvo, confirmamos sua acessibilidade com um ping. Em seguida, realizamos um escaneamento com nmap -sV para identificar portas abertas e versões dos serviços. 

``` bash
nmap -sV -p 21,22,80,445,139 192.168.56.101
```
<img width="1250" height="444" alt="image" src="https://github.com/user-attachments/assets/54391d0c-41ca-4ef5-aa74-b1c6f11560b4" />

### 2.3 Após o ping foi encontrada uma vulnerabilidade no serviço FTP, que estava aberta.

``` bash
ftp 192.168.56.101
connected to 192.168.56.101
```

## 2. Ataque de Força Bruta ao Serviço FTP, tentando encontrar credênciais vulneráveis através de listas de usernames e passwords:

``` bash
medusa -h 192.168.56.101 -U files/user.txt -P files/passwd.txt -M ftp -t 6
```
<img width="1280" height="372" alt="image" src="https://github.com/user-attachments/assets/adaa7cfb-5610-4e72-8b7f-3b2028c9237a" />

### 2.1 Descrição do comando de ataque:

-  h 192.168.56.101 -	Define o Host alvo do ataque (IP do servidor).
- -U files/user.txt	- lista de Usuários.
- -P files/passwd.txt - lista de Senhas.
- -M ftp - Define o sistema a ser atacado (FTP).
- -t 6 - Define a quantidade de theads a ser usadas

### 2.2 Credenciais encontradas e login no serviço FTP realizado com sucesso.
- User: msfadmin
- Senha: msfadmin

<img width="596" height="206" alt="image" src="https://github.com/user-attachments/assets/b848a6cb-8e19-42f6-8877-6ac2e055c1a7" />

##
## ⛔ 2- Ataque ao formulário DVWA na Prática ⛔
##

### 1. Ataque de força bruta em formulários da WEB - DVWA

- Primeiro passo é aprender como o formulário funciona e seus possíveis erros e vulnerabilidades
  
<img width="1773" height="825" alt="image" src="https://github.com/user-attachments/assets/914746ff-93be-42ad-bcfa-ce896ff40c8b" />

### 1.1 Realizando ataque com Medusa, testando senhas de wordlists:

``` bash
medusa -h 192.168.56.102 -U users.txt -P pass.txt -M http \
-m PAGE:'/dvwa/login.php'
-m FORM: 'username=^USER &password="PASS &Login=Login'\
-m 'FAIL-Login failed' -t 6
```
### 1.2 Senhas vazadas e login no formulário:
  
<img width="1275" height="812" alt="image" src="https://github.com/user-attachments/assets/0b35ba69-e433-4016-a40a-8f40afeb0fbc" />

##
### Credênciais encontradas
- User: admin
- Senha: password
##

<img width="732" height="564" alt="image" src="https://github.com/user-attachments/assets/251a3edc-632f-4394-9259-57873ab677cb" />

##
## ⚔️ 3- Ataque ao serviço SMB na Prática ⚔️
##

### 1 Exploração do serviço SMB

### 1.1 A partir da saída do enum4linux, montamos as wordlists e as utilizamos com o medusa:

``` bash
medusa -h 192.168.56.101 -U files/smb_users.txt -P files/senhas_spray.txt -M smbnt -t 2 -T 50
```

<img width="1299" height="533" alt="image" src="https://github.com/user-attachments/assets/07ce4b6a-a686-4f6e-9e4c-21818544a7a7" />

### 1.2 O teste identificou o usuário válido msfadmin com a senha msfadmin, que foi usada para conectar ao cliente SMB.

- User: msfadmin
- Senha: msfadmin

##

# ⚠️ Este documento tem caráter exclusivamente educacional e destina-se a testes de segurança conduzidos em ambientes controlados e com autorização prévia. A realização de ataques de força bruta ou de qualquer outra ação de intrusão sem a devida permissão é ilegal e antiética. Recomendamos a utilização responsável e legal deste conhecimento. ⚠️
