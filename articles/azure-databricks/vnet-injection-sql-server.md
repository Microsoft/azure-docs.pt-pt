---
title: Consultar um contêiner SQL Server do Docker do Linux com Azure Databricks
description: Este artigo descreve como implantar Azure Databricks em sua rede virtual, também conhecida como injeção de VNet.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 773ffe264446e6a4d9ef2e88634e4f2c9b8aeb45
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273977"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Tutorial: consultar um contêiner SQL Server do Docker do Linux em uma rede virtual de um notebook Azure Databricks

Este tutorial ensina a integrar o Azure Databricks com um contêiner do Docker do Linux SQL Server em uma rede virtual. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Implantar um espaço de trabalho Azure Databricks em uma rede virtual
> * Instalar uma máquina virtual do Linux em uma rede pública
> * Instalar o Docker
> * Instalar Microsoft SQL Server em Linux contêiner do Docker
> * Consultar o SQL Server usando JDBC em um notebook do databricks

## <a name="prerequisites"></a>Pré-requisitos

* Crie um [espaço de trabalho do databricks em uma rede virtual](quickstart-create-databricks-workspace-vnet-injection.md).

* Instale o [Ubuntu para Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Transfira o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="create-a-linux-virtual-machine"></a>Criar uma Máquina Virtual do Linux

1. No portal do Azure, selecione o ícone para **máquinas virtuais**. Em seguida, selecione **+ Adicionar**.

    ![Adicionar nova máquina virtual do Azure](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. Na guia **noções básicas** , escolha Ubuntu Server 16, 4 LTS. Altere o tamanho da VM para B1ms, que tem um VCPUS e 2 GB de RAM. O requisito mínimo para um contêiner do Docker para Linux SQL Server é 2 GB. Escolha um nome de usuário e senha de administrador.

    ![Guia básico da nova configuração de máquina virtual](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Navegue até a guia **rede** . escolha a rede virtual e a sub-rede pública que inclui o cluster Azure Databricks. Selecione **revisar + criar**e **criar** para implantar a máquina virtual.

    ![Guia rede da nova configuração de máquina virtual](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. Quando a implantação for concluída, navegue até a máquina virtual. Observe o endereço IP público e a rede/sub-rede virtual na **visão geral**. Selecione o **endereço IP público**

    ![Visão geral da máquina virtual](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Altere a **atribuição** para **estático** e insira um **rótulo de nome DNS**. Selecione **salvar**e reinicie a máquina virtual.

    ![Configuração de endereço IP público](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Selecione a guia **rede** em **configurações**. Observe que o grupo de segurança de rede que foi criado durante a implantação de Azure Databricks está associado à máquina virtual. Selecione **Adicionar regra de porta de entrada**.

7. Adicione uma regra para abrir a porta 22 para SSH. Use as seguintes configurações:
    
    |Definição|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Origem|Endereços IP|Endereços IP especifica que o tráfego de entrada de um endereço IP de origem específico será permitido ou negado por essa regra.|
    |Endereços IP de origem|< seu IP público @ no__t-0|Insira o endereço IP público. Você pode encontrar seu endereço IP público visitando [Bing.com](https://www.bing.com/) e procurando por **"meu IP"** .|
    |Intervalo de portas de origem|*|Permitir o tráfego de qualquer porta.|
    |Destino|Endereços IP|Endereços IP especifica que o tráfego de saída para um endereço IP de origem específico será permitido ou negado por essa regra.|
    |Endereços IP de destino|< seu IP público de VM @ no__t-0|Insira o endereço IP público da máquina virtual. Você pode encontrá-lo na página **visão geral** de sua máquina virtual.|
    |Intervalos de portas de destino|22|Abra a porta 22 para SSH.|
    |Prioridade|290|Dê prioridade à regra.|
    |Nome|SSH-databricks-tutorial-VM|Dê um nome à regra.|


    ![Adicionar regra de segurança de entrada para a porta 22](./media/vnet-injection-sql-server/open-port.png)

8. Adicione uma regra para abrir a porta 1433 para SQL com as seguintes configurações:

    |Definição|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Origem|Endereços IP|Endereços IP especifica que o tráfego de entrada de um endereço IP de origem específico será permitido ou negado por essa regra.|
    |Endereços IP de origem|10.179.0.0/16|Insira o intervalo de endereços para sua rede virtual.|
    |Intervalo de portas de origem|*|Permitir o tráfego de qualquer porta.|
    |Destino|Endereços IP|Endereços IP especifica que o tráfego de saída para um endereço IP de origem específico será permitido ou negado por essa regra.|
    |Endereços IP de destino|< seu IP público de VM @ no__t-0|Insira o endereço IP público da máquina virtual. Você pode encontrá-lo na página **visão geral** de sua máquina virtual.|
    |Intervalos de portas de destino|1433|Abra a porta 22 para SQL Server.|
    |Prioridade|300|Dê prioridade à regra.|
    |Nome|SQL-databricks-tutorial-VM|Dê um nome à regra.|

    ![Adicionar regra de segurança de entrada para a porta 1433](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Executar SQL Server em um contêiner do Docker

1. Abra o [Ubuntu para Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)ou qualquer outra ferramenta que permita que você use ssh na máquina virtual. Navegue até sua máquina virtual no portal do Azure e selecione **conectar** para obter o comando ssh que você precisa para se conectar.

    ![Conectar à máquina virtual](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Insira o comando em seu terminal do Ubuntu e insira a senha de administrador que você criou quando configurou a máquina virtual.

    ![Entrada SSH do terminal do Ubuntu](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. Use o comando a seguir para instalar o Docker na máquina virtual.

    ```bash
    sudo apt-get install docker.io
    ```

    Verifique a instalação do Docker com o seguinte comando:

    ```bash
    sudo docker --version
    ```

4. Instale a imagem.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    Verifique as imagens.

    ```bash
    sudo docker images
    ```

5. Execute o contêiner a partir da imagem.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Verifique se o contêiner está em execução.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>Criar uma base de dados SQL

1. Abra SQL Server Management Studio e conecte-se ao servidor usando o nome do servidor e a autenticação do SQL. O nome de usuário de logon é **SA** e a senha é a senha definida no comando Docker. A senha no comando de exemplo é `Password1234`.

    ![Conectar-se a SQL Server usando SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. Depois de se conectar com êxito, selecione **nova consulta** e insira o trecho de código a seguir para criar um banco de dados, uma tabela e inserir alguns registros na tabela.

    ```SQL
    CREATE DATABASE MYDB;
    GO
    USE MYDB;
    CREATE TABLE states(Name VARCHAR(20), Capitol VARCHAR(20));
    INSERT INTO states VALUES ('Delaware','Dover');
    INSERT INTO states VALUES ('South Carolina','Columbia');
    INSERT INTO states VALUES ('Texas','Austin');
    SELECT * FROM states
    GO
    ```

    ![Consulta para criar um banco de dados SQL Server](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>SQL Server de consulta de Azure Databricks

1. Navegue até o espaço de trabalho Azure Databricks e verifique se você criou um cluster como parte dos pré-requisitos. Em seguida, selecione **criar um bloco de anotações**. Dê um nome ao bloco de anotações, selecione *Python* como idioma e selecione o cluster que você criou.

    ![Novas configurações de notebook do databricks](./media/vnet-injection-sql-server/create-notebook.png)

2. Use o comando a seguir para executar ping no endereço IP interno da máquina virtual SQL Server. Esse ping deve ser bem-sucedido. Caso contrário, verifique se o contêiner está em execução e examine a configuração do NSG (grupo de segurança de rede).

    ```python
    %sh
    ping 10.179.64.4
    ```

    Você também pode usar o comando nslookup para examinar.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. Depois de executar o ping com êxito no SQL Server, você poderá consultar o banco de dados e as tabelas. Execute o seguinte código Python:

    ```python
    jdbcHostname = "10.179.64.4"
    jdbcDatabase = "MYDB"
    userName = 'SA'
    password = 'Password1234'
    jdbcPort = 1433
    jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, userName, password)

    df = spark.read.jdbc(url=jdbcUrl, table='states')
    display(df)
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos, o espaço de trabalho Azure Databricks e todos os recursos relacionados. A exclusão do trabalho evita a cobrança desnecessária. Se estiver planejando usar o espaço de trabalho Azure Databricks no futuro, você poderá interromper o cluster e reiniciá-lo mais tarde. Se você não pretende usar este Azure Databricks espaço de trabalho, exclua todos os recursos criados neste tutorial usando as seguintes etapas:

1. No menu à esquerda na portal do Azure, clique em grupos de **recursos** e, em seguida, clique no nome do grupo de recursos que você criou.

2. Na página do grupo de recursos, selecione **excluir**, digite o nome do recurso a ser excluído na caixa de texto e, em seguida, selecione **excluir** novamente.

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para saber como extrair, transformar e carregar dados usando Azure Databricks.
> [!div class="nextstepaction"]
> [Tutorial: extrair, transformar e carregar dados usando Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
