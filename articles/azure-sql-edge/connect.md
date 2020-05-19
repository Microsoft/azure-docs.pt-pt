---
title: Conecte e consulta Azure SQL Edge (Pré-visualização)
description: Saiba mais sobre a ligação e consulta do Azure SQL Edge (Pré-visualização)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 17bb5afebe0335bcdcf40298490f94999c04a621
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596919"
---
# <a name="connect-and-query-azure-sql-edge-preview"></a>Connect and Query Azure SQL Edge (Pré-visualização)

Depois de implantar o recipiente Azure SQL Edge, pode ligar-se ao motor de base de dados SQL a partir de qualquer um dos seguintes locais.

- Dentro do recipiente
- De outro contentor de estivador correndo no mesmo hospedeiro.
- No computador anfitrião
- De qualquer outra máquina cliente da rede.

## <a name="tools-to-connect-to-azure-sql-edge"></a>Ferramentas para ligar ao Azure SQL Edge

As ligações a uma instância Azure SQL Edge podem ser feitas a partir de qualquer uma das ferramentas comuns mencionadas abaixo.

* [sqlcmd](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) - ferramentas de cliente sqlcmd já estão incluídas na imagem do recipiente Azure SQL Edge. Se se prender a um recipiente de corrida com uma concha de festa interativa, pode executar as ferramentas localmente.
* [Estúdio de Gestão de Servidores SQL](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode)

Para ligar a um motor de base de dados Azure SQL Edge a partir de uma máquina de rede, você precisará do seguinte

- *Endereço IP ou nome de rede da máquina hospedeira* - Esta é a máquina de acolhimento onde o contentor Azure SQL Edge está em funcionamento.
- Mapeamento do porto de *contentores Azure SQL Edge* - Este é o mapeamento da porta do contentor de estivadores para uma porta no hospedeiro. Dentro do recipiente, o SQL Edge é sempre mapeado para a porta 1433. Isto pode ser alterado como parte da implantação Azure SQL Edge. Para alterar o número de porta, atualize as opções "Container Create Options" para o módulo SQL Edge em Azure IoT Edge. No exemplo abaixo indicado, a porta 1433 do contentor é mapeada para a porta 1600 no hospedeiro.

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

## <a name="connecting-to-the-database-engine-from-within-the-container"></a>Ligação ao motor de base de dados a partir do interior do recipiente

As [ferramentas de linha de comando SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) estão incluídas na imagem do recipiente Azure SQL Edge. Se se prender ao recipiente com um pedido de comando interativo, pode executar as ferramentas localmente.

1. Utilize o `docker exec -it` comando para iniciar uma concha interativa dentro do seu recipiente de corrida. No exemplo seguinte `e69e056c702d` está o ID do recipiente.

    ```bash
    docker exec -it <Azure SQL Edge container id or name> /bin/bash
    ```

    > [!TIP]
    > Nem sempre é preciso especificar toda a identificação do contentor. Só tens de especificar caracteres suficientes para o identificares de forma única. Assim, neste exemplo, pode ser suficiente para usar `e6` ou em vez de `e69` toda a identificação.

2. Uma vez dentro do recipiente, ligue-se localmente com sqlcmd. Sqlcmd não está no caminho por defeito, por isso tem que especificar o caminho completo.

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. Quando terminar com sqlcmd, digite `exit` .

4. Quando terminar com o comando interativo, escreva `exit` . O seu contentor continua a funcionar depois de sair da concha interativa.

## <a name="connect-to-sql-edge-from-another-container-on-the-same-host"></a>Ligue-se ao SQL Edge a partir de outro recipiente no mesmo hospedeiro

Uma vez que dois contentores que estão no mesmo hospedeiro estão na mesma rede de estivadores, podem ser facilmente acedidos utilizando o nome do contentor e o endereço da porta para o serviço. Por exemplo, se estiver a ligar-se à instância SQL Edge a partir de outro módulo python (recipiente) no mesmo hospedeiro, pode utilizar uma cadeia de ligação semelhante à seguinte. O exemplo abaixo pressupõe que o SQL Edge está configurado para ouvir na porta predefinida.

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-sql-edge-from-another-network-machine"></a>Ligue-se ao SQL Edge a partir de outra máquina de rede

Para ligar à instância SQL Edge a partir de outra máquina da rede, terá de utilizar o endereço IP do hospedeiro e a porta hospedeira para a qual o recipiente SQL Edge está mapeado. Por exemplo, se o endereço IP do hospedeiro de estivador for *xxx.xxx.xxx.xxx" e o recipiente SQL Edge for mapeado para alojar a porta *1600,* então o endereço do servidor para a instância SQL Edge seria **xxx.xxx.xxx.xxx.xxx,1600**. O roteiro de python atualizado seria

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

Para se ligar a uma instância de SQL Edge utilizando o Estúdio de Gestão de Servidores SQL a funcionar numa máquina Windows, consulte o Estúdio de [Gestão de Servidores SQL](https://docs.microsoft.com/sql/linux/sql-server-linux-manage-ssms).

Para se ligar a uma instância de SQL Edge utilizando o Código do Estúdio Visual num Windows, Mac ou Linux, consulte o [Código do Estúdio Visual](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode).

Para se ligar a uma instância de SQL Edge utilizando o Azure Data Studio no Windows, Mac ou Linux, consulte [o Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-server).

## <a name="see-also"></a>Ver também

[Ligar e Consultar](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[Instale ferramentas do Servidor SQL no Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)
