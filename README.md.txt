**# 🔐 Hardening de MySQL com Kali Linux e Nmap**



**## 📌 Sobre o projeto**



**Neste laboratório, analisei a exposição de um serviço MySQL executado em uma máquina Windows utilizando Kali Linux e Nmap.**



**Durante o reconhecimento, identifiquei a porta `3306/tcp` acessível pela rede. Após investigar a configuração do serviço, apliquei uma medida de hardening para restringir o MySQL somente ao acesso local e validei a alteração com um novo scan.**



**> Laboratório realizado em ambiente próprio e controlado.**



**## 🧪 Ambiente**



**- Kali Linux (VirtualBox)**

**- Windows**

**- MySQL Server 8.0**

**- Nmap**

**- Netstat**



**## 🔎 Identificação**



**O scan realizado pelo Kali identificou a porta 3306 como aberta:**



**```bash**

**nmap -sV -p 3306 192.168.1.8**



**Resultado:**



**3306/tcp open mysql MySQL (unauthorized)**



**No Windows, validei a exposição utilizando:**



**netstat -ano | findstr :3306**



**O MySQL estava escutando em:**



**0.0.0.0:3306**



**🛡️ Hardening**



**Como o banco era utilizado apenas localmente, alterei o arquivo my.ini para restringir o serviço ao endereço de loopback:**



**\[mysqld]**

**bind-address=127.0.0.1**



**Após a alteração, reiniciei o serviço MySQL.**



**✅ Resultado**



**O netstat passou a mostrar:**



**127.0.0.1:3306**



**E um novo scan a partir do Kali retornou:**



**3306/tcp filtered mysql**



**Antes	Depois**

**0.0.0.0:3306	127.0.0.1:3306**

**Nmap: open	Nmap: filtered**











**📚 Aprendizados**



**O laboratório permitiu praticar reconhecimento com Nmap, análise de portas e serviços, validação com Netstat e aplicação de hardening para redução da superfície de exposição.**

