---
title: Tutorial - configurar bases de dados na base de dados do Azure para MySQL com o Ansible | Documentos da Microsoft
description: Saiba como utilizar o Ansible para criar e configurar um servidor de Base de Dados do Azure para MySQL
keywords: ansible, azure, devops, bash, manual de procedimentos, mysql, base de dados
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 7238e993ebd812734b3b08f57b7a4c2f080a7384
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727071"
---
# <a name="tutorial-configure-databases-in-azure-database-for-mysql-using-ansible"></a>Tutorial: Configurar bases de dados na base de dados do Azure para MySQL com o Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Base de dados do Azure para MySQL](/azure/mysql/overview) é um serviço de base de dados relacional com base na edição de Comunidade do MySQL. Base de dados do Azure para MySQL permite-lhe gerir bases de dados MySQL nas suas aplicações web.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar um servidor MySql
> * Criar uma base de dados MySql
> * Configurar uma regra de filewall para que uma aplicação externa pode ligar ao seu servidor
> * Ligar ao seu servidor MySql a partir do Azure cloud shell
> * Consultar os seus servidores MySQL disponíveis
> * Liste todas as bases de dados em seus servidores ligados

## <a name="prerequisites"></a>Pré-requisitos

* [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
* [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

O código do playbook nesta secção cria um grupo de recursos do Azure. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.  

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

Antes de executar o playbook, consulte as seguintes notas de:

* Um grupo de recursos com o nome `myResourceGroup` é criado.
* O grupo de recursos é criado no `eastus` localização:

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>Criar um servidor e base de dados MySQL

O código do playbook nesta secção cria um servidor MySQL e uma base de dados do Azure para a instância do MySQL. O novo servidor do MySQL é um servidor de finalidade básica de 5 de fins com um vCore e o nome `mysqlserveransible`. A instância de base de dados com o nome `mysqldbansible`.

Para obter mais informações sobre escalões de preço, consulte [base de dados do Azure para MySQL escalões de preço](/azure/mysql/concepts-pricing-tiers). 

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

Antes de executar o playbook, consulte as seguintes notas de:

* Na `vars` secção, o valor de `mysqlserver_name` tem de ser exclusivo.
* Na `vars` secção, substitua `<server_admin_password>` com uma palavra-passe.

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Configurar uma regra de firewall

Uma regra de firewall ao nível do servidor permite que uma aplicação externa para ligar ao seu servidor através da firewall do serviço MySQL do Azure. São exemplos de aplicações externas a `mysql` ferramenta da linha de comandos e o MySQL Workbench.

O código de playbook nesta secção cria uma regra de firewall com o nome `extenalaccess` que permite ligações a partir de qualquer endereço IP externo. 

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

Antes de executar o playbook, consulte as seguintes notas de:

* Na secção de variáveis, substitua `startIpAddress` e `endIpAddress`. Utilize o intervalo de endereços IP que correspondem para o intervalo a partir do qual irá ser ligar.
* As ligações à base de dados do Azure para MySQL comunicam através da porta 3306. Se tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 3306 poderá não ser permitido. Nesse caso, não pode ligar ao servidor, a menos que o seu departamento de TI abra a porta 3306.
* O playbook utiliza o `azure_rm_resource` módulo, que permite a utilização direta da REST API.

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server"></a>Ligar ao servidor

Nesta secção, vai utilizar o Azure cloud shell para ligar ao servidor que criou anteriormente.

1. Selecione o **experimentar** botão no código a seguir:

    ```azurecli-interactive
    mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    ```

1. Na linha de comandos, introduza o seguinte comando para consultar o estado do servidor:

    ```sql
    mysql> status
    ```
    
    Se tudo correr bem, ver um resultado semelhante para os seguintes resultados:
    
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
    
## <a name="query-mysql-servers"></a>Consultar servidores MySQL

O código de playbook nesta secção consulta servidores MySQL no `myResourceGroup` e apresenta uma lista de bases de dados nos servidores encontrados.

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

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook mysql_query.yml
```

Depois de executar o playbook, ver um resultado semelhante para os seguintes resultados:

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

Consulte também o seguinte resultado para a base de dados MySQL:

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

Quando já não for necessário, elimine os recursos criados neste artigo. 

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

Executar o playbook com o `ansible-playbook` comando:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"] 
> [Ansible no Azure](/azure/ansible/)