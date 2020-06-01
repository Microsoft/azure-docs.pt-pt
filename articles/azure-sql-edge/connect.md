---
title: Ligar e consultar a borda Azure SQL (pré-visualização)
description: Saiba como ligar e consultar a Borda Azure SQL (Pré-visualização)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e7034c3f664eeba802341510b109ba9cc57845a8
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235140"
---
# <a name="connect-and-query-azure-sql-edge-preview"></a>Ligar e Consultar a borda SQL (pré-visualização)

Depois de implantar o recipiente Azure SQL Edge, pode ligar-se ao motor de base de dados SQL a partir de qualquer um dos seguintes locais.

- Dentro do recipiente
- De outro contentor de estivador correndo no mesmo hospedeiro.
- No computador anfitrião
- De qualquer outra máquina de clientes na rede.

## <a name="tools-to-connect-to-azure-sql-edge"></a>Ferramentas para ligar ao Azure SQL Edge

As ligações a uma instância Azure SQL Edge podem ser feitas a partir de qualquer uma das ferramentas comuns mencionadas abaixo.

* [sqlcmd](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) - as ferramentas de cliente sqlcmd já estão incluídas na imagem do recipiente Azure SQL Edge. Se se ligar a um recipiente de corrida com uma casca de pancada interativa, pode executar as ferramentas localmente.
* [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode)

Para ligar a um motor de base de dados Azure SQL Edge a partir de uma máquina de rede, você precisará do seguinte

- *Endereço IP ou nome de rede da máquina hospedeira* - Esta é a máquina hospedeira onde o recipiente Azure SQL Edge está em funcionamento.
- *Mapeamento do porto de anfitrião do recipiente Azure SQL Edge* - Este é o mapeamento da porta de porta para uma porta no hospedeiro. Dentro do recipiente SQL Edge é sempre mapeado para a porta 1433. Isto pode ser alterado como parte da implantação do Azure SQL Edge. Para alterar o número da porta, atualize as "Opções de Criação de Recipientes" para o módulo SQL Edge em Azure IoT Edge. No exemplo a seguir indicado, a porta 1433 do contentor está mapeada para o porto 1600 do hospedeiro.

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

- *Palavra-passe SA para a instância SQL Edge* - Este é o valor especificado para a variável **ambiente SA_PASSWORD** durante a implementação do SQL Edge.

## <a name="connecting-to-the-database-engine-from-within-the-container"></a>Ligação ao motor de base de dados a partir do contentor

As [ferramentas de linha de comando SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) estão incluídas na imagem do recipiente Azure SQL Edge. Se ligar ao recipiente com uma solicitação de comando interativa, pode executar as ferramentas localmente.

1. Utilize o `docker exec -it` comando para iniciar uma casca de pancada interativa dentro do seu recipiente de funcionamento. No exemplo a seguir `e69e056c702d` está a identificação do contentor.

    ```bash
    docker exec -it <Azure SQL Edge container id or name> /bin/bash
    ```

    > [!TIP]
    > Nem sempre é preciso especificar a identificação do contentor inteiro. Só tens de especificar caracteres suficientes para identificá-lo de forma única. Assim, neste exemplo, pode ser o suficiente para usar `e6` ou em vez de o `e69` id completo.

2. Uma vez dentro do recipiente, conecte-se localmente com sqlcmd. Sqlcmd não está no caminho por defeito, por isso tem que especificar o caminho completo.

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. Quando terminar com sqlcmd, `exit` escreva.

4. Quando terminar com o pedido de comando interativo, escreva `exit` . O seu contentor continua a funcionar depois de sair da casca de choque interativa.

## <a name="connect-to-sql-edge-from-another-container-on-the-same-host"></a>Ligue ao SQL Edge a partir de outro recipiente no mesmo hospedeiro

Uma vez que dois contentores que funcionam no mesmo hospedeiro estão na mesma rede de estivadores, podem ser facilmente acedidos usando o nome do contentor e o endereço de porta para o serviço. Por exemplo, se estiver a ligar-se à instância SQL Edge a partir de outro módulo python (recipiente) no mesmo hospedeiro, pode utilizar uma cadeia de ligação semelhante à seguinte. O exemplo abaixo pressupõe que o SQL Edge esteja configurado para ouvir na porta predefinida.

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-sql-edge-from-another-network-machine"></a>Ligue ao SQL Edge a partir de outra máquina de rede

Para ligar à instância SQL Edge de outra máquina da rede, terá de utilizar o endereço IP do anfitrião e da porta hospedeira para a qual o recipiente SQL Edge está mapeado. Por exemplo, se o endereço IP do anfitrião do estivador for *xxx.xxx.xxx.xxx" e o recipiente SQL Edge estiver mapeado para a porta de acolhimento *1600,* então o endereço do servidor para a instância SQL Edge seria **xxx.xxx.xxx.xxx.1600**. O script de python atualizado seria

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

Para ligar a um exemplo de SQL Edge utilizando o SQL Server Management Studio em execução numa máquina Windows, consulte [o SQL Server Management Studio](https://docs.microsoft.com/sql/linux/sql-server-linux-manage-ssms).

Para ligar a uma instância de SQL Edge utilizando código de estúdio visual numa máquina Windows, Mac ou Linux, consulte [o Código do Estúdio Visual](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode).

Para ligar a um exemplo de SQL Edge utilizando o Azure Data Studio no Windows, Mac ou máquina Linux, consulte [o Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-server).

## <a name="see-also"></a>Ver também

[Ligar e Consultar](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[Instale ferramentas sql server no Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)
