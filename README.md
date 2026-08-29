# 🔐 Hardening de MySQL com Kali Linux e Nmap

## 📌 Sobre o projeto

Neste laboratório, analisei a exposição de um serviço MySQL executado em uma máquina Windows utilizando Kali Linux e Nmap.

Durante o reconhecimento, identifiquei a porta `3306/tcp` acessível pela rede. Após investigar a configuração do serviço, apliquei uma medida de hardening para restringir o MySQL somente ao acesso local e validei a alteração com um novo scan.

> Laboratório realizado em ambiente próprio e controlado.


## 🧪 Ambiente

- Kali Linux (VirtualBox)
- Windows
- MySQL Server 8.0
- Nmap
- Netstat


## 🌐 Identificação da rede

Antes de iniciar o reconhecimento, verifiquei o endereço IP da máquina Kali:

```bash
ip a
```

![IP da máquina Kali](images/01-kali-ip.png)


Em seguida, consultei a tabela de roteamento para identificar a rota padrão e o gateway utilizado pela máquina virtual:

```bash
ip route
```

![Tabela de roteamento do Kali](images/02-kali-route.png)


Para validar a comunicação entre o Kali Linux e a máquina Windows, realizei um teste de conectividade utilizando o `ping`:

```bash
ping -c 4 192.168.1.8
```

![Ping do Kali para o Windows](images/03-ping-windows.png)


## 🔎 Reconhecimento

Com a conectividade validada, realizei um scan no host Windows para identificar as portas acessíveis:

```bash
nmap 192.168.1.8
```

O scan identificou algumas portas abertas, entre elas a porta `3306/tcp`, normalmente associada ao MySQL.

![Scan inicial com Nmap](images/04-nmap-inicial.png)


Em seguida, utilizei a detecção de serviço especificamente na porta 3306:

```bash
nmap -sV -p 3306 192.168.1.8
```

Resultado:

```text
3306/tcp open mysql MySQL (unauthorized)
```

![Identificação do MySQL com Nmap](images/05-nmap-mysql-antes.png)


## 🔍 Validação no Windows

No Windows, verifiquei localmente o estado da porta 3306:

```cmd
netstat -ano | findstr :3306
```

O resultado mostrou o MySQL escutando em:

```text
0.0.0.0:3306
```

Isso indicava que o serviço estava escutando nas interfaces IPv4 disponíveis da máquina.

![Netstat antes do hardening](images/06-netstat-antes.png)


## 🛡️ Hardening

Como o MySQL era utilizado apenas localmente, alterei o arquivo `my.ini` para restringir o serviço ao endereço de loopback.

Na seção `[mysqld]`, adicionei:

```ini
bind-address=127.0.0.1
```

![Configuração bind-address](images/07-bind-address.png)


Após salvar a configuração, reiniciei o serviço MySQL para aplicar a alteração.


## ✅ Validação após o hardening

Após a alteração, executei novamente:

```cmd
netstat -ano | findstr :3306
```

Dessa vez, o MySQL passou a escutar em:

```text
127.0.0.1:3306
```

![Netstat depois do hardening](images/08-netstat-depois.png)


Por fim, repeti o scan a partir do Kali:

```bash
nmap -sV -p 3306 192.168.1.8
```

Resultado:

```text
3306/tcp filtered mysql
```

![Nmap depois do hardening](images/09-nmap-depois.png)


## 📊 Antes e depois

| Antes | Depois |
|---|---|
| `0.0.0.0:3306` | `127.0.0.1:3306` |
| Nmap: `open` | Nmap: `filtered` |
| Serviço acessível pela rede | Serviço restrito ao acesso local |


## 📚 Aprendizados

O laboratório permitiu praticar conceitos de redes, conectividade entre máquinas, reconhecimento com Nmap, análise de portas e serviços, validação com Netstat e aplicação de hardening para redução da superfície de exposição.