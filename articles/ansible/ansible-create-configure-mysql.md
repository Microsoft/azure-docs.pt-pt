---
title: Criar e configurar uma base de dados do Azure para o servidor MySQL com o Ansible
description: Saiba como utilizar o Ansible para criar e configurar um servidor de Base de Dados do Azure para MySQL
ms.service: azure
keywords: ansible, azure, devops, bash, manual de procedimentos, mysql, base de dados
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/23/2018
ms.openlocfilehash: 63472cf9c4b6b16f74ececfb6c6e61cf5f89ff9d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58095403"
---
# <a name="create-and-configure-an-azure-database-for-mysql-server-by-using-ansible"></a>Criar e configurar uma base de dados do Azure para o servidor MySQL com o Ansible
A [Base de Dados do Azure para MySQL](https://docs.microsoft.com/azure/mysql/) é um serviço gerido que utiliza para executar, gerir e dimensionar bases de dados MySQL de elevada disponibilidade na cloud. O Ansible permite-lhe automatizar a implementação e a configuração de recursos no seu ambiente. 

Este inicio rápido mostra como utilizar o Ansible para criar um servidor de Base de Dados do Azure para MySQL e configurar a respetiva regra de firewall. Pode concluir essas tarefas em cerca de cinco minutos com o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
- **Subscrição do Azure** - se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> O Ansible 2.7 é necessário para executar os manuais de procedimentos de exemplo neste tutorial. 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.  

O exemplo seguinte cria um grupo de recursos com o nome **myResourceGroup** na localização **eastus**:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

Guarde o manual de procedimentos anterior como **rg.yml**. Para executar o manual de procedimentos, utilize o comando **ansible-playbook** da seguinte forma:
```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>Criar um servidor e base de dados MySQL
O exemplo seguinte cria um servidor MySQL com o nome **mysqlserveransible** e uma instância da Base de Dados do Azure para MySQL com o nome **mysqldbansible**. Este é um servidor de Ger 5 para Fins Básicos com um vCore. 

O valor de **mysqlserver_name** tem de ser exclusivo. Para compreender os valores válidos por região e escalão, leia a [documentação de escalões de preços](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers). Substitua `<server_admin_password>` por uma palavra-passe.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus
    mysqlserver_name: mysqlserveransible
    mysqldb_name: mysqldbansible
    admin_username: mysqladmin
    admin_password: <server_admin_password> 
  tasks:
    - name: Create MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        sku:
          name: B_Gen5_1
          tier: Basic
        location: "{{ location }}"
        version: 5.6
        enforce_ssl: True
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        storage_mb: 51200
    - name: Create instance of MySQL Database
      azure_rm_mysqldatabase:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
        name: "{{ mysqldb_name }}"
```

Guarde o manual de procedimentos anterior como **mysql_create.yml**. Para executar o manual de procedimentos, utilize o comando **ansible-playbook** da seguinte forma:
```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Configurar uma regra de firewall
A regra de firewall ao nível do servidor permite que uma aplicação externa se ligue ao seu servidor através da firewall do serviço Azure MySQL. Um exemplo de uma aplicação externa é a ferramenta de linha de comandos **mysql** ou o MySQL Workbench.
O exemplo seguinte cria uma regra de firewall com o nome **externalaccess** que permite ligações de qualquer endereço IP externo. 

Introduza os seus próprios valores para **startIpAddress** e **endIpAddress**. Utilize o intervalo de endereços IP que correspondem ao local a partir do qual estabelecerá a ligação. 

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
  - name: Open firewall to access MySQL Server from outside
    azure_rm_resource:
      api_version: '2017-12-01'
      resource_group: "{{ resource_group }}"
      provider: dbformysql
      resource_type: servers
      resource_name: "{{ mysqlserver_name }}"
      subresource:
        - type: firewallrules
          name: externalaccess
      body:
        properties:
          startIpAddress: "0.0.0.0"
          endIpAddress: "255.255.255.255"
```

> [!NOTE]
> As ligações à base de dados do Azure para MySQL comunicam através da porta 3306. Se tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 3306 poderá não ser permitido. Nesse caso, não pode ligar ao servidor, a menos que o seu departamento de TI abra a porta 3306.
> 

Aqui, é utilizado o modulo **azure_rm_resource** para efetuar esta tarefa. Permite a utilização direta da API REST.

Guarde o manual de procedimentos anterior como **mysql_firewall.yml**. Para executar o manual de procedimentos, utilize o comando **ansible-playbook** da seguinte forma:
```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server-by-using-the-command-line-tool"></a>Ligar ao servidor com a ferramenta de linha de comandos
Pode [transferir o MySQL](https://dev.mysql.com/downloads/) e instalá-lo no seu computador. Em vez disso, pode selecionar o botão **Experimentar** nos exemplos de código ou no botão **>_** na barra de ferramentas superior direita no portal do Azure e abrir o **Azure Cloud Shell**.

Introduza os seguintes comandos: 

1. Ligar ao servidor com a ferramenta de linha de comandos **mysql**:
   ```azurecli-interactive
   mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
   ```

2. Ver o estado do servidor:
   ```sql
   mysql> status
   ```

Se tudo correr bem, a ferramenta de linha de comandos deve produzir o seguinte texto:

```
demo@Azure:~$ mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65233
Server version: 5.6.39.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64) using  EditLine wrapper

Connection id:          65233
Current database:
Current user:           mysqladmin@13.76.42.93
SSL:                    Cipher in use is AES256-SHA
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         5.6.39.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mysqlserveransible.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    utf8
Conn.  characterset:    utf8
TCP port:               3306
Uptime:                 36 min 21 sec

Threads: 5  Questions: 559  Slow queries: 0  Opens: 96  Flush tables: 3  Open tables: 10  Queries per second avg: 0.256
--------------
```

## <a name="using-facts-to-query-mysql-servers"></a>Utilizar factos para consultar os servidores MySQL
O exemplo seguinte consulta os servidores MySQL em **myResourceGroup** e, posteriormente, todas as bases de dados nos servidores:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Query MySQL Servers in current resource group
      azure_rm_mysqlserver_facts:
        resource_group: "{{ resource_group }}"
      register: mysqlserverfacts

    - name: Dump MySQL Server facts
      debug:
        var: mysqlserverfacts

    - name: Query MySQL Databases
      azure_rm_mysqldatabase_facts:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
      register: mysqldatabasefacts

    - name: Dump MySQL Database Facts
      debug:
        var: mysqldatabasefacts
```

Guarde o manual de procedimentos anterior como **mysql_query.yml**. Para executar o manual de procedimentos, utilize o comando **ansible-playbook** da seguinte forma:

```bash
ansible-playbook mysql_query.yml
```

Em seguida, verá o resultado seguinte do servidor MySQL: 
```json
"servers": [
    {
        "admin_username": "mysqladmin",
        "enforce_ssl": false,
        "fully_qualified_domain_name": "mysqlserveransible.mysql.database.azure.com",
        "id": "/subscriptions/685ba005-af8d-4b04-8f16-a7bf38b2eb5a/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysqlserveransible",
        "location": "eastus",
        "name": "mysqlserveransible",
        "resource_group": "myResourceGroup",
        "sku": {
            "capacity": 1,
            "family": "Gen5",
            "name": "B_Gen5_1",
            "tier": "Basic"
        },
        "storage_mb": 5120,
        "user_visible_state": "Ready",
        "version": "5.6"
    }
]
```

Também verá o resultado seguinte da base de dados MySQL:
```json
"databases": [
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "information_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysql",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransibler"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysqldbansible",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "performance_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    }
]
```

## <a name="clean-up-resources"></a>Limpar recursos

Se não precisar destes recursos, poderá eliminá-los ao executar o exemplo seguinte. Elimina um grupo de recursos com o nome **myResourceGroup**. 

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Guarde o manual de procedimentos anterior como **rg_delete.yml**. Para executar o manual de procedimentos, utilize o comando **ansible-playbook** da seguinte forma:
```bash
ansible-playbook rg_delete.yml
```

Se quiser eliminar apenas o servidor MySQL recentemente criado, execute o exemplo seguinte:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Delete MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        state: absent
```

Guarde o manual de procedimentos anterior como **mysql_delete.yml**. Para executar o manual de procedimentos, utilize o comando **ansible-playbook** da seguinte forma:
```bash
ansible-playbook mysql_delete.yml
```

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"] 
> [Ansible no Azure](https://docs.microsoft.com/azure/ansible/)
