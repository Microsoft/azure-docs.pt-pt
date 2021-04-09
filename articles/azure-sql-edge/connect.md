---
title: Ligar e consultar Azure SQL Edge
description: Aprenda a ligar-se e consultar a Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/25/2020
ms.openlocfilehash: b56b65261950e9cf534a3755d214229ef7d5bb1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395211"
---
# <a name="connect-and-query-azure-sql-edge"></a>Ligar e consultar Azure SQL Edge

No Azure SQL Edge, depois de colocar um contentor, pode ligar-se ao motor da base de dados a partir de qualquer um dos seguintes locais:

- Dentro do recipiente
- De outro recipiente Docker correndo no mesmo anfitrião
- No computador anfitrião
- De qualquer outra máquina cliente na rede

## <a name="tools-to-connect-to-azure-sql-edge"></a>Ferramentas para ligar ao Azure SQL Edge

Pode ligar-se a uma instância de Azure SQL Edge a partir de qualquer uma destas ferramentas comuns:

* [sqlcmd:](/sql/linux/sql-server-linux-setup-tools)as ferramentas de cliente sqlcmd já estão incluídas na imagem de contentor de Azure SQL Edge. Se se ligar a um recipiente de corrida com uma casca de pancada interativa, pode executar as ferramentas localmente. As ferramentas de cliente SQL não estão disponíveis na plataforma ARM64, uma vez que não estão incluídas na versão ARM64 dos contentores SQL Edge. 
* [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](/sql/visual-studio-code/sql-server-develop-use-vscode)

Para ligar a um motor de base de dados Azure SQL Edge a partir de uma máquina de rede, precisa do seguinte:

- **Endereço IP ou nome de rede da máquina hospedeira**: Esta é a máquina hospedeira onde o recipiente Azure SQL Edge está em funcionamento.
- **Mapeamento do porto de anfitrião do recipiente Azure SQL Edge**: Este é o mapeamento da porta de contentores Docker para uma porta no hospedeiro. Dentro do recipiente, a Borda Azure SQL é sempre mapeada para a porta 1433. Pode mudar isto se quiser. Para alterar o número da porta, atualize as **opções de criação** de recipientes para o módulo Azure SQL Edge em Azure IoT Edge. No exemplo seguinte, a porta 1433 do contentor está mapeada para o porto 1600 no hospedeiro.

    ```JSON
    {
        "PortBindings": {
          "1433/tcp": [
            {
              "HostPort": "1600"
            }
          ]
        }
    }
    ```

- **Palavra-passe SA para o exemplo Azure SQL Edge**: Este é o valor especificado para a variável ambiental durante a `SA_PASSWORD` implementação do Azure SQL Edge.

## <a name="connect-to-the-database-engine-from-within-the-container"></a>Ligue ao motor da base de dados a partir do recipiente

As [ferramentas de linha de comando SQL Server](/sql/linux/sql-server-linux-setup-tools) estão incluídas na imagem de contentor do Azure SQL Edge. Se ligar ao recipiente com uma indicação de comando interativo, pode executar as ferramentas localmente. As ferramentas de cliente SQL não estão disponíveis na plataforma ARM64, uma vez que não estão incluídas na versão ARM64 dos contentores SQL Edge. 

1. Utilize o `docker exec -it` comando para iniciar uma casca de pancada interativa dentro do seu recipiente de funcionamento. No exemplo seguinte, `e69e056c702d` encontra-se a identificação do contentor.

    ```bash
    docker exec -it <Azure SQL Edge container ID or name> /bin/bash
    ```

    > [!TIP]
    > Nem sempre é preciso especificar a identificação do contentor inteiro. Só tens de especificar caracteres suficientes para identificá-lo de forma única. Assim, neste exemplo, pode ser o suficiente para usar `e6` ou , em vez de o `e69` ID completo.

2. Quando estiver dentro do contentor, conecte-se localmente com sqlcmd. Sqlcmd não está no caminho por defeito, por isso tem que especificar o caminho completo.

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. Quando terminar com sqlcmd, `exit` escreva.

4. Quando terminar com o pedido de comando interativo, escreva `exit` . O seu contentor continua a funcionar depois de sair da casca de choque interativa.

## <a name="connect-to-azure-sql-edge-from-another-container-on-the-same-host"></a>Ligue-se ao Azure SQL Edge a partir de outro recipiente no mesmo hospedeiro

Como dois contentores que estão a funcionar no mesmo hospedeiro estão na mesma rede Docker, pode facilmente acessá-los utilizando o nome do recipiente e o endereço de porta para o serviço. Por exemplo, se estiver a ligar-se ao exemplo de Azure SQL Edge a partir de outro módulo python (recipiente) no mesmo hospedeiro, pode utilizar uma cadeia de ligação semelhante à seguinte. (Este exemplo pressupõe que a Borda Azure SQL está configurada para ouvir na porta padrão.)

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-azure-sql-edge-from-another-network-machine"></a>Ligue-se ao Azure SQL Edge a partir de outra máquina de rede

É melhor ligar-se à instância de Azure SQL Edge a partir de outra máquina da rede. Para tal, utilize o endereço IP do anfitrião Docker e a porta de acolhimento para a qual está mapeado o recipiente Azure SQL Edge. Por exemplo, se o endereço IP do anfitrião Docker for *xxx.xxx.xxx.xxx*, e o contentor Azure SQL Edge estiver mapeado para a porta de acolhimento *1600*, então o endereço do servidor, por exemplo, Azure SQL Edge seria *xxx.xxx.xxx.xxx.1600*. O script de python atualizado é:

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

Para ligar a um exemplo de Azure SQL Edge utilizando o SQL Server Management Studio em execução numa máquina Windows, consulte [o SQL Server Management Studio](/sql/linux/sql-server-linux-manage-ssms).

Para ligar a um exemplo de Azure SQL Edge utilizando o Código de Estúdio Visual numa máquina Windows, Mac ou Linux, consulte [o Código do Estúdio Visual](/sql/visual-studio-code/sql-server-develop-use-vscode).

Para ligar a um exemplo de Azure SQL Edge utilizando o Azure Data Studio numa máquina Windows, Mac ou Linux, consulte [o Azure Data Studio](/sql/azure-data-studio/quickstart-sql-server).

## <a name="next-steps"></a>Passos seguintes

[Ligar e consultar](/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[Instale ferramentas sql server no Linux](/sql/linux/sql-server-linux-setup-tools)