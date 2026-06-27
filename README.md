# Arquitetura de Alta Disponibilidade na Oracle Cloud Infrastructure (OCI)

Este repositório contém os scripts de automação e a documentação para o provisionamento de uma infraestrutura web de alta disponibilidade na Oracle Cloud Infrastructure (OCI). O projeto simula um ambiente de produção real utilizando instâncias Linux automatizadas, balanceamento de carga e configuração de rede segura.
<img width="1310" height="519" alt="VMs" src="https://github.com/user-attachments/assets/15d51d3a-f3df-4a9b-8369-45201f48fba6" />


## 🚀 Tecnologias Utilizadas

* **Nuvem:** Oracle Cloud Infrastructure (OCI)
* **Sistema Operacional:** Ubuntu Linux
* **Automação:** Shell Scripting / Cloud-Init
* **Servidor Web & Banco de Dados (Stack LAMP):** Apache, MySQL, PHP
* **Segurança:** Netfilter / UFW (Uncomplicated Firewall) & OCI Security Lists

---

## 🏗️ Arquitetura do Projeto

A arquitetura foi desenhada para garantir que a aplicação continue online mesmo se uma das instâncias sofrer alguma instabilidade:

1. **OCI Load Balancer:** Atua como a porta de entrada única para o tráfego público (HTTP/Porta 80), distribuindo as requisições de forma inteligente entre os servidores.
2. **Instâncias Compute (Web Servers):** Múltiplas VMs rodando Ubuntu Linux distribuídas na rede.
3. **Automação com Cloud-Init:** Cada máquina virtual é provisionada de forma 100% automatizada através de um Shell Script executado na inicialização.

---

## 🔧 Scripts de Automação

O script de inicialização (`cloud-init.sh`) executa as seguintes tarefas assim que a VM é criada:

* Atualização completa dos pacotes do sistema operativo.
* Instalação e configuração do servidor web Apache, PHP e dependências.
* Configuração do firewall nativo da instância (UFW) para permitir apenas o tráfego necessário.
* Criação de uma página web customizada com o hostname da máquina para validar o correto funcionamento do Load Balancer.

### Exemplo do Script Utilizado:

```bash
#!/bin/bash
# Atualiza o sistema
apt-get update && apt-get upgrade -y

# Instala Apache e PHP
apt-get install apache2 php libapache2-mod-php -y

# Configura o Firewall local da VM
ufw allow 80/tcp
ufw allow 22/tcp
ufw --force enable

# Cria página de teste identificando a VM
echo "<h1>Servidor Web Ativo - Hostname: $(hostname)</h1>" > /var/www/html/index.php


🔒 Configuração de Rede e Segurança
Para garantir a segurança do ambiente, o acesso foi estruturado em camadas:

Security Lists (OCI): Configuradas para permitir tráfego público na porta 80 apenas direcionado ao Load Balancer, e porta 22 restrita para gerenciamento via SSH.

Firewall Host-Based (UFW): Alinhado com as regras da nuvem para garantir que a pilha de rede interna da VM rejeite conexões não autorizadas.

🛠️ Como Replicar este Projeto
Acesse o console da OCI e crie uma nova instância Compute com imagem Ubuntu.

Na seção de Advanced Options (Opções Avançadas), cole o conteúdo do script cloud-init.sh no campo User Data.

Repita o processo para criar a segunda VM.

Crie um OCI Load Balancer, configure o Backend Set apontando para as duas VMs criadas e configure o Listener na porta 80.

Acesse o IP público do Load Balancer no navegador e atualize a página para ver o tráfego alternando entre as instâncias.
