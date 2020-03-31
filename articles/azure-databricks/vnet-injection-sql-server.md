---
title: Consulta a um recipiente SQL Server Linux Docker com tijolos de dados Azure
description: Este artigo descreve como implantar os Azure Databricks na sua rede virtual, também conhecida como injeção VNet.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 460079248e6cbd939c36b84f94cac41dce4dda2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73747660"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Tutorial: Consulta um recipiente SQL Server Linux Docker numa rede virtual a partir de um caderno Azure Databricks

Este tutorial ensina-o a integrar os Azure Databricks com um recipiente SQL Server Linux Docker numa rede virtual. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Implementar um espaço de trabalho Azure Databricks para uma rede virtual
> * Instale uma máquina virtual Linux numa rede pública
> * Instalar o Docker
> * Instale o Microsoft SQL Server no recipiente de estivador Linux
> * Consulta do Servidor SQL usando JDBC a partir de um caderno databricks

## <a name="prerequisites"></a>Pré-requisitos

* Criar um espaço de [trabalho databricks numa rede virtual.](quickstart-create-databricks-workspace-vnet-injection.md)

* Instale [Ubuntu para Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Transfira o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="create-a-linux-virtual-machine"></a>Criar uma máquina virtual do Linux

1. No portal Azure, selecione o ícone para **Máquinas Virtuais**. Em seguida, selecione **+ Adicionar**.

    ![Adicione nova máquina virtual Azure](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. No separador **Basics,** escolha o Ubuntu Server 18.04 LTS e mude o tamanho VM para B2s. Escolha um nome de utilizador e senha de administrador.

    ![Separador básico da nova configuração da máquina virtual](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Navegue para o separador **Networking.** Escolha a rede virtual e a subnet pública que inclui o seu cluster De Tijolos De Dados Azure. Selecione **Review + criar**e, em seguida, **Criar** para implementar a máquina virtual.

    ![Separador de rede da nova configuração da máquina virtual](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. Quando a implantação estiver concluída, navegue para a máquina virtual. Note o endereço IP público e a rede/sub-rede virtual na **visão geral**. Selecione o **endereço IP público**

    ![Visão geral da máquina virtual](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Alterar a **Atribuição** para **Estática** e introduzir uma etiqueta de **nome DNS**. Selecione **Guardar**e reiniciar a máquina virtual.

    ![Configuração do endereço IP público](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Selecione o separador **de rede** em **Definições**. Note que o grupo de segurança da rede que foi criado durante a implementação de Azure Databricks está associado à máquina virtual. **Selecione Adicionar regra**da porta de entrada .

7. Adicione uma regra para abrir a porta 22 para SSH. Utilize as seguintes definições:
    
    |Definição|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Origem|Endereços IP|O IP Addresss especifica que o tráfego de entrada de um endereço IP de origem específica será permitido ou negado por esta regra.|
    |Endereços IP de origem|<seu IP público\>|Insira o seu endereço IP público. Pode encontrar o seu endereço IP público visitando [bing.com](https://www.bing.com/) e procurando **"o meu IP".**|
    |Intervalo de portas de origem|*|Permita o tráfego de qualquer porto.|
    |Destino|Endereços IP|O IP Addresss especifica que o tráfego de saída para um endereço IP de origem específica será permitido ou negado por esta regra.|
    |Endereços IP de destino|<o seu vm público ip\>|Insira o endereço IP público da sua máquina virtual. Pode encontrá-lo na página **de visão geral** da sua máquina virtual.|
    |Intervalos de portas de destino|22|Porta aberta 22 para SSH.|
    |Prioridade|290|Dê prioridade à regra.|
    |Nome|ssh-databricks-tutorial-vm|Dê um nome à regra.|


    ![Adicione a regra de segurança de entrada para a porta 22](./media/vnet-injection-sql-server/open-port.png)

8. Adicione uma regra para abrir a porta 1433 para SQL com as seguintes definições:

    |Definição|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Origem|Qualquer|Fonte especifica que o tráfego de entrada de um endereço IP de origem específica será permitido ou negado por esta regra.|
    |Intervalo de portas de origem|*|Permita o tráfego de qualquer porto.|
    |Destino|Endereços IP|O IP Addresss especifica que o tráfego de saída para um endereço IP de origem específica será permitido ou negado por esta regra.|
    |Endereços IP de destino|<o seu vm público ip\>|Insira o endereço IP público da sua máquina virtual. Pode encontrá-lo na página **de visão geral** da sua máquina virtual.|
    |Intervalos de portas de destino|1433|Porta aberta 22 para SQL Server.|
    |Prioridade|300|Dê prioridade à regra.|
    |Nome|sql-databricks-tutorial-vm|Dê um nome à regra.|

    ![Adicione a regra de segurança de entrada para a porta 1433](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Executar o Servidor SQL em um recipiente Docker

1. Abra [ubuntu para Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab), ou qualquer outra ferramenta que lhe permita entrar na máquina virtual. Navegue para a sua máquina virtual no portal Azure e selecione **Connect** para obter o comando SSH que precisa para ligar.

    ![Conectar à máquina virtual](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Introduza o comando no seu terminal Ubuntu e introduza a palavra-passe de administrador que criou quando configuraa a máquina virtual.

    ![Ubuntu terminal SSH assinar](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. Utilize o seguinte comando para instalar o Docker na máquina virtual.

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

5. Executar o recipiente a partir da imagem.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Verifique se o recipiente está em funcionamento.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>Criar uma base de dados SQL

1. Abra o Estúdio de Gestão de Servidores SQL e ligue-se ao servidor utilizando o nome do servidor e a autenticação SQL. O sinal em nome de utilizador é **SA** e a palavra-passe é a palavra-passe definida no comando Docker. A palavra-passe no `Password1234`comando de exemplo é .

    ![Ligue-se ao Servidor SQL utilizando o Estúdio de Gestão de Servidores SQL](./media/vnet-injection-sql-server/ssms-login.png)

2. Depois de conectado com sucesso, selecione **New Query** e introduza o seguinte código para criar uma base de dados, uma tabela e insira alguns registos na tabela.

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

    ![Consulta para criar uma base de dados do Servidor SQL](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Consulta SQL Server de Tijolos de Dados Azure

1. Navegue para o seu espaço de trabalho Azure Databricks e verifique se criou um cluster como parte dos pré-requisitos. Em seguida, selecione **Criar um Caderno**. Dê ao caderno um nome, selecione *Python* como idioma e selecione o cluster que criou.

    ![Novas definições de caderno databricks](./media/vnet-injection-sql-server/create-notebook.png)

2. Utilize o seguinte comando para pingar o endereço IP interno da máquina virtual Do Servidor SQL. Este ping deve ser bem sucedido. Caso contrário, verifique se o recipiente está em funcionamento e reveja a configuração do grupo de segurança da rede (NSG).

    ```python
    %sh
    ping 10.179.64.4
    ```

    Também pode usar o comando nslookup para rever.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. Depois de ter saqueado com sucesso o Servidor SQL, pode consultar a base de dados e as tabelas. Executar o seguinte código python:

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

Quando já não for necessário, elimine o grupo de recursos, o espaço de trabalho azure Databricks e todos os recursos relacionados. Apagar o trabalho evita faturação desnecessária. Se planeia utilizar o espaço de trabalho azure Databricks no futuro, pode parar o cluster e reiniciá-lo mais tarde. Se não vai continuar a utilizar este espaço de trabalho Azure Databricks, elimine todos os recursos que criou neste tutorial utilizando os seguintes passos:

1. A partir do menu à esquerda no portal Azure, clique em **grupos de Recursos** e, em seguida, clique no nome do grupo de recursos que criou.

2. Na página do grupo de recursos, selecione **Eliminar**, digite o nome do recurso para apagar na caixa de texto e, em seguida, selecione **Apagar** novamente.

## <a name="next-steps"></a>Passos seguintes

Avançar para o próximo artigo para aprender a extrair, transformar e carregar dados usando os Databricks Azure.
> [!div class="nextstepaction"]
> [Tutorial: Extrair, transformar e carregar dados utilizando os Databricks Azure](databricks-extract-load-sql-data-warehouse.md)
