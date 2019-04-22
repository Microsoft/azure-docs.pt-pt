---
title: Consultar um contentor de Docker do SQL Server Linux numa rede virtual a partir de um bloco de notas do Azure Databricks
description: Este artigo descreve como implementar o Azure Databricks para sua rede virtual, também conhecida como injeção de VNet.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 345e07fac30f4ad0c8e9918cb8a1ff0fb8aeb811
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59288955"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Tutorial: Consultar um contentor de Docker do SQL Server Linux numa rede virtual a partir de um bloco de notas do Azure Databricks

Esse tutorial ensina-lhe como integrar o Azure Databricks com um contentor de Docker do SQL Server Linux numa rede virtual. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Implementar uma área de trabalho do Azure Databricks para uma rede virtual
> * Instalar uma máquina virtual do Linux numa rede pública
> * Instalar o Docker
> * Instalar o Microsoft SQL Server no contentor de docker do Linux
> * Consultar o SQL Server usando o JDBC a partir de um bloco de notas do Databricks

## <a name="prerequisites"></a>Pré-requisitos

* Criar uma [área de trabalho do Databricks numa rede virtual](quickstart-create-databricks-workspace-vnet-injection.md).

* Instale [Ubuntu para Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Transfira o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="create-a-linux-virtual-machine"></a>Criar uma máquina virtual do Linux

1. No portal do Azure, selecione o ícone para **máquinas virtuais**. Em seguida, selecione **+ adicionar**.

    ![Adicionar nova máquina virtual do Azure](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. Sobre o **Noções básicas** separador, escolha o Ubuntu Server 16.04 LTS. Altere o tamanho da VM para B1ms, que tem um VCPUS e 2 GB de RAM. Os requisitos mínimos para um contentor de Docker do Linux SQL Server é de 2 GB. Escolha um administrador de nome de utilizador e palavra-passe.

    ![Separador de noções básicas da nova configuração de máquina virtual](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Navegue para o **redes** separador. Escolha a rede virtual e a sub-rede pública que inclui o seu cluster do Azure Databricks. Selecione **rever + criar**, em seguida, **criar** para implementar a máquina virtual.

    ![Separador de nova configuração de máquina virtual de rede](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. Quando a implementação estiver concluída, navegue para a máquina virtual. Observe que o endereço IP público e Virtual rede/sub-rede na **descrição geral**. Selecione o **endereço IP público**

    ![Descrição geral de máquina virtual](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Alteração da **atribuição** para **estático** e introduza um **etiqueta de nome DNS**. Selecione **guardar**e reinicie a máquina virtual.

    ![Configuração do endereço IP público](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Selecione o **Networking** separador sob **definições**. Tenha em atenção que o grupo de segurança de rede que foi criado durante a implementação do Azure Databricks está associado com a máquina virtual. Selecione **Adicionar regra de porta de entrada**.

7. Adicione uma regra para abrir a porta 22 para SSH. Utilize as seguintes definições:
    
    |Definição|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Origem|Endereços IP|Endereços IP Especifica que o tráfego de entrada de uma origem específica, endereço IP será permitido ou negado por esta regra.|
    |Endereços IP de origem|< o seu ip público\>|Introduza o seu endereço IP público. Pode encontrar o seu endereço IP público ao visitar [bing.com](https://www.bing.com/) e ao procurar por **"meu IP"**.|
    |Intervalo de portas de origem|*|Permitir tráfego a partir de qualquer porta.|
    |Destino|Endereços IP|Endereços IP especifica esse tráfego de saída para uma origem específica de endereço IP será permitido ou negado por esta regra.|
    |Endereços IP de destino|< o seu ip público da vm\>|Introduza o endereço IP público da sua máquina virtual. Pode encontrá-lo sobre a **descrição geral** página da sua máquina virtual.|
    |Intervalos de portas de destino|22|Abra a porta 22 para SSH.|
    |Prioridade|290|Dê a regra com uma prioridade.|
    |Name|ssh-databricks-tutorial-vm|Dê um nome de regra.|


    ![Adicionar regra de segurança de entrada para a porta 22](./media/vnet-injection-sql-server/open-port.png)

8. Adicione uma regra para abrir a porta 1433 para SQL com as seguintes definições:

    |Definição|Valor sugerido|Descrição|
    |-------|---------------|-----------|
    |Origem|Endereços IP|Endereços IP Especifica que o tráfego de entrada de uma origem específica, endereço IP será permitido ou negado por esta regra.|
    |Endereços IP de origem|10.179.0.0/16|Introduza o intervalo de endereços da rede virtual.|
    |Intervalo de portas de origem|*|Permitir tráfego a partir de qualquer porta.|
    |Destino|Endereços IP|Endereços IP especifica esse tráfego de saída para uma origem específica de endereço IP será permitido ou negado por esta regra.|
    |Endereços IP de destino|< o seu ip público da vm\>|Introduza o endereço IP público da sua máquina virtual. Pode encontrá-lo sobre a **descrição geral** página da sua máquina virtual.|
    |Intervalos de portas de destino|1433|Abra a porta 22 para o SQL Server.|
    |Prioridade|300|Dê a regra com uma prioridade.|
    |Name|sql-databricks-tutorial-vm|Dê um nome de regra.|

    ![Adicionar regra de segurança de entrada para a porta 1433](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Execute o SQL Server num contentor do Docker

1. Open [Ubuntu para Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab), ou qualquer outra ferramenta que lhe permite para encaminhar o SSH na máquina virtual. Navegue para a máquina virtual no portal do Azure e selecione **Connect** para obter o comando SSH tem de se ligar.

    ![Conectar à máquina virtual](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Introduza o comando no seu terminal Ubuntu e introduza a palavra-passe de administrador que criou quando configurou a máquina virtual.

    ![Ubuntu terminal SSH início de sessão](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. Utilize o seguinte comando para instalar o Docker na máquina virtual.

    ```bash
    sudo apt-get install docker.io
    ```

    Certifique-se a instalação do Docker com o seguinte comando:

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

5. Execute o contentor a partir da imagem.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Certifique-se de que o contentor está em execução.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>Criar uma base de dados SQL

1. Abra o SQL Server Management Studio e ligue ao servidor com o nome do servidor e autenticação do SQL. É o nome de utilizador de início de sessão **SA** e a palavra-passe é a palavra-passe definida no comando Docker. A palavra-passe no comando de exemplo é `Password1234`.

    ![Ligar ao SQL Server com o SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. Depois de se ligar com êxito, selecione **nova consulta** e introduza o fragmento de código seguinte para criar uma base de dados, uma tabela e inserir alguns registros na tabela.

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

    ![Consulta para criar uma base de dados do SQL Server](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Consulta SQL Server do Azure Databricks

1. Navegue até à sua área de trabalho do Azure Databricks e certifique-se de que criou um cluster como parte dos pré-requisitos. Em seguida, selecione **criar um bloco de notas**. Dê um nome, selecione ao bloco de notas *Python* como a linguagem e selecione o cluster que criou.

    ![Novas definições de bloco de notas do Databricks](./media/vnet-injection-sql-server/create-notebook.png)

2. Utilize o seguinte comando para efetuar o ping do endereço IP da máquina virtual do SQL Server. Este ping deve ser concluída com êxito. Caso contrário, certifique-se de que o contentor está em execução e reveja a configuração de grupo (NSG) de segurança de rede.

    ```python
    %sh
    ping 10.179.64.4
    ```

    Também pode utilizar o comando nslookup para rever.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. Assim que tiver com êxito alcançados pelo ping do SQL Server, pode consultar a base de dados e tabelas. Execute o python seguinte código:

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

Quando já não for necessário, elimine o grupo de recursos, a área de trabalho do Azure Databricks e todos os recursos relacionados. A eliminação da tarefa evita a faturação desnecessária. Se estiver a planear utilizar a área de trabalho do Azure Databricks no futuro, pode parar o cluster e reiniciá-lo mais tarde. Se não pretender continuar a utilizar esta área de trabalho do Azure Databricks, elimine todos os recursos criados neste tutorial, utilizando os seguintes passos:

1. No menu esquerdo no portal do Azure, clique em **grupos de recursos** e, em seguida, clique no nome do grupo de recursos que criou.

2. Na página do grupo de recursos, selecione **elimine**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, selecione **eliminar** novamente.

## <a name="next-steps"></a>Passos Seguintes

Avance para o artigo seguinte para saber como extrair, transformar e carregar dados com o Azure Databricks.
> [!div class="nextstepaction"]
> [Tutorial: Extrair, transformar e carregar dados com o Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
