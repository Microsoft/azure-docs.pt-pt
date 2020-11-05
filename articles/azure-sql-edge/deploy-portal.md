---
title: Implementar arestas Azure SQL utilizando o portal Azure
description: Saiba como implementar a Azure SQL Edge utilizando o portal Azure
keywords: implementar SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 5d7d232ada814d5d3c30e7b012c6289f847d641f
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395093"
---
# <a name="deploy-azure-sql-edge"></a>Implementar borda Azure SQL 

Azure SQL Edge é um motor de base de dados relacional otimizado para implementações IoT e Azure IoT Edge. Fornece capacidades para criar uma camada de armazenamento e processamento de dados de alto desempenho para aplicações e soluções IoT. Este quickstart mostra-lhe como começar a criar um módulo Azure SQL Edge através do Azure IoT Edge utilizando o portal Azure.

## <a name="before-you-begin"></a>Antes de começar

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).
* Inicie sessão no [portal do Azure](https://portal.azure.com/).
* Criar um [Hub Azure IoT](../iot-hub/iot-hub-create-through-portal.md).
* Criar um [dispositivo Azure IoT Edge](../iot-edge/how-to-install-iot-edge.md).

> [!NOTE]   
> Para implementar um Azure Linux VM como um dispositivo IoT Edge, consulte este [guia de arranque rápido](../iot-edge/quickstart-linux.md).

## <a name="deploy-sql-edge-module-from-azure-marketplace"></a>Implementar módulo sql edge a partir do Azure Marketplace

O Azure Marketplace é um mercado de aplicações e serviços online onde pode navegar através de uma vasta gama de aplicações e soluções empresariais que são certificadas e otimizadas para funcionar em Azure, incluindo [módulos IoT Edge.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) O Azure SQL Edge pode ser implantado num dispositivo de borda através do mercado.

1. Encontre o módulo Azure SQL Edge no Mercado Azure.<br><br>

   ![SqL Edge no MarketPlace](media/deploy-portal/find-offer-marketplace.png)

2. Escolha o plano de software que melhor corresponda aos seus requisitos e clique em **Criar**. <br><br>

   ![Escolha o plano de software correto](media/deploy-portal/pick-correct-plan.png)

3. Nos dispositivos-alvo para a página do módulo IoT Edge, especifique os seguintes detalhes e, em seguida, clique em **Criar**

   |**Campo**  |**Descrição**  |
   |---------|---------|
   |Subscrição  |  A subscrição Azure sob a qual o IoT Hub foi criado |
   |IoT Hub   |  Nome do Hub IoT onde o dispositivo IoT Edge está registado e, em seguida, selecione "Implementar para um dispositivo"|
   |Nome do dispositivo de borda IoT  |  Nome do dispositivo IoT Edge onde o SQL Edge seria implantado |

4. Nos **Módulos De Conjunto no dispositivo:** página, clique no módulo Azure SQL Edge em **módulos IoT Edge**. O nome do módulo predefinido é definido para *AzureSQLEdge*. 

5. Na secção *de Definições* do Módulo da lâmina do **módulo de borda IoT,** especifique os valores pretendidos para o nome do *módulo de borda IoT,* *política de reinício* e *estado desejado*. 

   > [!IMPORTANT]    
   > Não altere nem atualize as definições **de Image URI** no módulo.

6. Na secção *De Variáveis Ambientais* da lâmina do **Módulo de Borda IoT de atualização,** especifique os valores desejados para as variáveis ambientais. Para obter uma lista completa de variáveis ambientais Azure SQL Edge consulte [Configure usando variáveis ambientais](configure.md#configure-by-using-environment-variables). As seguintes variáveis de ambiente predefinido são definidas para o módulo. 

   |**Parâmetro**  |**Descrição**|
   |---------|---------|
   | MSSQL_SA_PASSWORD  | Altere o valor predefinido para especificar uma palavra-passe forte para a conta de administração SQL Edge. |
   | MSSQL_LCID   | Altere o valor predefinido para definir o ID do idioma pretendido para utilizar para SQL Edge. Por exemplo, 1036 é francês. |
   | MSSQL_COLLATION | Altere o valor predefinido para definir a colagem predefinida para SQL Edge. Esta definição substitui o mapeamento padrão do ID da linguagem (LCID) à colagem. |

   > [!IMPORTANT]    
   > Não altere nem atualize a variável ambiente **ACCEPT_EULA** para o módulo.

7. Na secção *De Opções de Criação* de Recipientes da lâmina do **Módulo IoT Edge de atualização,** atualize as seguintes opções de acordo com o requisito. 
   - **Porto anfitrião:** Mapear a porta de anfitrião especificada para a porta 1433 (porta SQL padrão) no contentor.
   - **Ligações** e **suportes :** Se precisar de implantar mais de um módulo SQL Edge, certifique-se de que atualiza a opção de montagem para criar uma nova fonte & par de alvos para o volume persistente. Para obter mais informações sobre suportes e volume, consulte [utilize volumes](https://docs.docker.com/storage/volumes/) na documentação do estivador. 

   ```json
   {
    "HostConfig": {
        "CapAdd": [
            "SYS_PTRACE"
        ],
        "Binds": [
            "sqlvolume:/sqlvolume"
        ],
        "PortBindings": {
            "1433/tcp": [
                {
                    "HostPort": "1433"
                }
            ]
        },
        "Mounts": [
            {
                "Type": "volume",
                "Source": "sqlvolume",
                "Target": "/var/opt/mssql"
            }
        ]
    },
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "PlanId=asde-developer-on-iot-edge"
    ]
   }
   ```
   > [!IMPORTANT]    
   > Não altere a `PlanId` variável de ambiente definida na definição de configuração de criação. Se este valor for alterado, o recipiente Azure SQL Edge não irá arrancar. 
   
8. No painel do **módulo IoT Edge de atualização,** clique em **Update**.
9. Nos **módulos set na** página do dispositivo clique em **seguinte: Rotas >** se precisar de definir rotas para a sua implantação. Caso contrário, clique em **Rever + Criar**. Para obter mais informações sobre as rotas de configuração, consulte [os módulos de implantação e estabeleça rotas em IoT Edge.](../iot-edge/module-composition.md)
11. Nos **módulos set na** página do dispositivo, clique em **Criar**.

## <a name="connect-to-azure-sql-edge"></a>Ligue-se à Borda Azure SQL

Os passos seguintes utilizam a ferramenta de linha de comando Azure SQL Edge, **sqlcmd,** dentro do recipiente para ligar ao Aresta SQL Azure.

> [!NOTE]      
> As ferramentas da linha sql command (sqlcmd) não estão disponíveis dentro da versão ARM64 dos recipientes Azure SQL Edge.

1. Utilize o `docker exec -it` comando para iniciar uma casca de pancada interativa dentro do seu recipiente de funcionamento. No exemplo a seguir `azuresqledge` encontra-se o nome especificado pelo `Name` parâmetro do seu Módulo de Borda IoT.

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. Uma vez dentro do recipiente, conecte-se localmente com sqlcmd. Sqlcmd não está no caminho por defeito, por isso tem que especificar o caminho completo.

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]    
   > Pode omitir a palavra-passe na linha de comando a ser solicitada para a introduzir.

3. Se for bem sucedido, deverá chegar a um pedido de comando **sqlcmd:** `1>` .

## <a name="create-and-query-data"></a>Criar e consultar dados

As secções seguintes **acompanham-no utilizando sqlcmd** e Transact-SQL para criar uma nova base de dados, adicionar dados e executar uma consulta.

### <a name="create-a-new-database"></a>Criar uma nova base de dados

Os passos seguintes criam uma nova base de dados chamada `TestDB` .

1. A partir da solicitação de comando **sqlcmd,** cole o seguinte comando Transact-SQL para criar uma base de dados de teste:

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. Na linha seguinte, escreva uma consulta para devolver o nome de todas as bases de dados no seu servidor:

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>Inserir dados

Em seguida, crie uma nova `Inventory` mesa, e insira duas novas linhas.

1. A partir do pedido de comando **sqlcmd,** altere o contexto para a nova `TestDB` base de dados:

   ```sql
   USE TestDB
   ```

2. Criar uma nova tabela chamada `Inventory` :

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. Inserir dados na nova tabela:

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. Digite `GO` para executar os comandos anteriores:

   ```sql
   GO
   ```

### <a name="select-data"></a>Selecionar dados

Agora, fazer uma consulta para devolver os dados da `Inventory` tabela.

1. A partir do pedido de comando **sqlcmd,** insira uma consulta que retorna as linhas da `Inventory` tabela onde a quantidade é superior a 152:

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. Execute o comando:

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>Saia da solicitação de comando sqlcmd

1. Para terminar a sua sessão **de sqlcmd,** escreva: `QUIT`

   ```sql
   QUIT
   ```

2. Para sair do comando interativo no seu recipiente, escreva `exit` . O seu contentor continua a funcionar depois de sair da casca de choque interativa.

## <a name="connect-from-outside-the-container"></a>Conecte-se de fora do recipiente

Pode ligar e executar consultas SQL contra a sua instância Azure SQL Edge a partir de qualquer ferramenta externa de Linux, Windows ou macOS que suporte ligações SQL. Para obter mais informações sobre a ligação a um recipiente SQL Edge a partir do exterior, consulte [Connect e Query Azure SQL Edge](./connect.md).

Neste arranque rápido, lançou um Módulo SQL Edge num dispositivo IoT Edge. 

## <a name="next-steps"></a>Passos Seguintes

- [Machine Learning e Inteligência Artificial com ONNX em SQL Edge](onnx-overview.md)
- [Construção de uma solução de fim para o fim de IoT com borda SQL usando IoT Edge](tutorial-deploy-azure-resources.md)
- [Streaming de dados em Azure SQL Edge](stream-data.md)
- [Resolver erros de implementação](troubleshoot.md)