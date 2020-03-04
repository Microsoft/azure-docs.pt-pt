---
title: Tutorial - Configure bases de dados em Base de Dados Azure para MySQL usando Ansible
description: Saiba como utilizar o Ansible para criar e configurar um servidor de Base de Dados do Azure para MySQL
keywords: ansible, azure, devops, bash, manual de procedimentos, mysql, base de dados
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 9cd574417733518b993bb242c2c168aba338e34a
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78247874"
---
# <a name="tutorial-configure-databases-in-azure-database-for-mysql-using-ansible"></a>Tutorial: Configure bases de dados em Base de Dados Azure para MySQL usando Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure Database for MySQL](/azure/mysql/overview) é um serviço de base de dados relacional baseado na Edição Comunitária MySQL. A Base de Dados Azure para MySQL permite-lhe gerir as bases de dados MySQL nas suas aplicações web.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar um servidor MySql
> * Criar uma base de dados MySql
> * Configure uma regra de firewall para que uma aplicação externa possa ligar-se ao seu servidor
> * Ligue-se ao seu servidor MySql a partir da Casca de Nuvem Azure
> * Consulta dos seus servidores MySQL disponíveis
> * Listar todas as bases de dados nos seus servidores conectados

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos:

O código de jogadas nesta secção cria um grupo de recursos Azure. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.  

Guarde o manual de procedimentos seguinte como `rg.yml`:

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

Antes de executar o livro de jogadas, consulte as seguintes notas:

* É criado um grupo de recursos chamado `myResourceGroup`.
* O grupo de recursos é criado no local `eastus`:

Executar o manual usando o comando `ansible-playbook`:

```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>Criar um servidor e base de dados MySQL

O código de jogadas nesta secção cria um servidor MySQL e uma Base de Dados Azure para a instância MySQL. O novo servidor MySQL é um servidor De Propósito Básico Gen 5 com um vCore e é nomeado `mysqlserveransible`. A instância da base de dados chama-se `mysqldbansible`.

Para obter mais informações sobre os níveis de preços, consulte a Base de Dados Azure para os níveis de [preços MySQL](/azure/mysql/concepts-pricing-tiers). 

Guarde o manual de procedimentos seguinte como `mysql_create.yml`:

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

Antes de executar o livro de jogadas, consulte as seguintes notas:

* Na secção `vars`, o valor da `mysqlserver_name` deve ser único.
* Na secção `vars`, substitua `<server_admin_password>` por uma palavra-passe.

Executar o manual usando o comando `ansible-playbook`:

```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Configurar uma regra de firewall

Uma regra de firewall ao nível do servidor permite que uma aplicação externa se conectem ao seu servidor através da firewall de serviço Azure MySQL. Exemplos de aplicações externas são a ferramenta `mysql` linha de comando e a bancada de trabalho MySQL.

O código de jogadas nesta secção cria uma regra de firewall chamada `extenalaccess` que permite ligações a partir de qualquer endereço IP externo. 

Guarde o manual de procedimentos seguinte como `mysql_firewall.yml`:

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

Antes de executar o livro de jogadas, consulte as seguintes notas:

* Na secção vars, substitua `startIpAddress` e `endIpAddress`. Utilize o leque de endereços IP que correspondam ao intervalo a partir do qual irá ligar.
* As ligações à base de dados do Azure para MySQL comunicam através da porta 3306. Se tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 3306 poderá não ser permitido. Nesse caso, não pode ligar ao servidor, a menos que o seu departamento de TI abra a porta 3306.
* O manual utiliza o módulo `azure_rm_resource`, que permite a utilização direta da API REST.

Executar o manual usando o comando `ansible-playbook`:

```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server"></a>Ligar ao servidor

Nesta secção, utiliza a Cloud Shell Azure para se ligar ao servidor que criou anteriormente.

1. Abra shell.azure.com selecionando abaixo.

   [![Lançamento emcamado](https://shell.azure.com/images/launchcloudshell.png "Iniciar o Azure Cloud Shell")](https://shell.azure.com)

1. Introduza o seguinte código:

    ```sql
    mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    ```

1. Na solicitação, introduza o seguinte comando para consultar o estado do servidor:

    ```sql
    mysql> status
    ```
    
    Se tudo correr bem, vê-se uma saída semelhante aos seguintes resultados:
    
    ```output
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
    
## <a name="query-mysql-servers"></a>Consulta servidores MySQL

O código de jogadas nesta secção consulta os servidores MySQL em `myResourceGroup` e lista as bases de dados nos servidores encontrados.

Guarde o manual de procedimentos seguinte como `mysql_query.yml`:

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

Executar o manual usando o comando `ansible-playbook`:

```bash
ansible-playbook mysql_query.yml
```

Depois de executar o livro de jogadas, vê a saída semelhante aos seguintes resultados:

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

Também vê a seguinte saída para a base de dados MySQL:

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

Quando já não for necessário, apague os recursos criados neste artigo. 

Guarde o manual de procedimentos seguinte como `cleanup.yml`:

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

Executar o manual usando o comando `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Ansible no Azure](/azure/ansible/)