---
title: Usar Gerenciador de Dados Blockchain para atualizar Azure Cosmos DB
description: Usar Gerenciador de Dados Blockchain para enviar dados do Blockchain para Azure Cosmos DB
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chroyal
ms.openlocfilehash: 3f2d0df2c094d8455aa29e79ad3c6acc0aa52dd4
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73586482"
---
# <a name="tutorial-use-blockchain-data-manager-to-send-data-to-azure-cosmos-db"></a>Tutorial: usar o Blockchain Gerenciador de Dados para enviar dados para Azure Cosmos DB

Neste tutorial, você usará o Blockchain Gerenciador de Dados para o serviço Blockchain do Azure para registrar dados de transação Blockchain no Azure Cosmos DB. Blockchain Gerenciador de Dados captura, transforma e entrega dados de contabilidade de Blockchain para tópicos da grade de eventos do Azure. Na grade de eventos do Azure, você usa um conector de aplicativo lógico do Azure para criar documentos em um banco de dados Azure Cosmos DB. Quando terminar com o tutorial, você poderá explorar os dados da transação blockchain no Data Explorer Azure Cosmos DB.

[![detalhes da transação Blockchain](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

Neste tutorial:

> [!div class="checklist"]
> * Criar uma instância de Gerenciador de Dados do Blockchain
> * Adicionar um aplicativo blockchain para decodificar Propriedades e eventos de transação
> * Criar uma conta de Azure Cosmos DB e um banco de dados para armazenar o dado da transação
> * Crie um aplicativo lógico do Azure para conectar um tópico da grade de eventos do Azure para Azure Cosmos DB
> * Enviar uma transação para uma contabilidade do blockchain
> * Exibir os dados de transação decodificados em Azure Cosmos DB

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Conclua [o início rápido: criar um membro do blockchain usando o portal do Azure](create-member.md) ou [início rápido: criar um membro Blockchain do serviço blockchain do Azure usando o CLI do Azure](create-member-cli.md)
* Conclua o [início rápido: Use Visual Studio Code para se conectar a uma rede do Azure Blockchain Service Consortium](connect-vscode.md). O guia de início rápido orienta você durante a instalação do [Kit de desenvolvimento do Azure Blockchain para Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) e a configuração do ambiente de desenvolvimento de Blockchain.
* [Tutorial completo: Use Visual Studio Code para criar, compilar e implantar contratos inteligentes](send-transaction.md). O tutorial orienta a criação de um contrato inteligente de exemplo.
* Criar um [tópico de grade de eventos](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Saiba mais sobre [manipuladores de eventos na grade de eventos do Azure](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Criar instância

Uma instância de Gerenciador de Dados do Blockchain conecta e monitora um nó de transação do serviço Blockchain do Azure. Uma instância captura todos os dados brutos de bloqueio e de transação do nó de transação. Uma conexão de saída envia dados blockchain para a grade de eventos do Azure. Você configura uma conexão de saída única quando cria a instância.

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
1. Vá para o membro do serviço Blockchain do Azure que você criou no guia de [início rápido de pré-requisito: criar um membro do Blockchain usando o portal do Azure](create-member.md). Selecione **Blockchain Gerenciador de dados**.
1. Selecione **Adicionar**.

    ![Adicionar Gerenciador de Dados Blockchain](./media/data-manager-cosmosdb/add-instance.png)

    Introduza os seguintes detalhes:

    Definição | Exemplo | Descrição
    --------|---------|------------
    Nome | myassister | Insira um nome exclusivo para um Gerenciador de Dados Blockchain conectado.
    Nó de transação | myblockchainmember | Escolha o nó de transação padrão do membro do serviço Blockchain do Azure que você criou no pré-requisito.
    Nome da ligação | Cosmosdb | Insira um nome exclusivo da conexão de saída onde os dados da transação blockchain são enviados.
    Ponto de extremidade da grade de eventos | myTopic | Escolha um tópico da grade de eventos criado no pré-requisito. Observação: a instância de Gerenciador de Dados do Blockchain e o tópico da grade de eventos devem estar na mesma assinatura.

1. Selecione **OK**.

    Leva menos de um minuto para criar uma instância de Gerenciador de Dados Blockchain. Depois que a instância for implantada, ela será iniciada automaticamente. Uma instância de Gerenciador de Dados em execução Blockchain captura eventos de Blockchain do nó de transação e envia dados para a grade de eventos.

## <a name="add-application"></a>Adicionar aplicação

Adicione o aplicativo **helloblockchain** blockchain para que blockchain Gerenciador de dados decodifique o evento e o estado da propriedade. Blockchain Gerenciador de Dados requer a ABI do contrato inteligente e o arquivo de código de bytes para adicionar o aplicativo.

### <a name="get-contract-abi-and-bytecode"></a>Obter ABI do contrato e código de bytes

A ABI do contrato define as interfaces do contrato inteligente. Ele descreve como interagir com o contrato inteligente. Você pode usar a [extensão do kit de desenvolvimento do Azure Blockchain para Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) para copiar a Abi do contrato para a área de transferência.

1. No painel do Visual Studio Code Explorer, expanda a pasta **Build/Contracts** do projeto de solidez **Helloblockchain** criado no tutorial de pré-requisito [: Use Visual Studio Code para criar, compilar e implantar contratos inteligentes](send-transaction.md).
1. Clique com o botão direito do mouse no arquivo JSON de metadados do contrato. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **. JSON** .
1. Selecione **copiar o Abi do contrato**.

    ![Painel de Visual Studio Code com a seleção de ABI do contrato de cópia](./media/data-manager-cosmosdb/abi-devkit.png)

    A ABI do contrato é copiada para a área de transferência.

1. Salve a matriz **Abi** como um arquivo JSON. Por exemplo, *Abi. JSON*. Você usará o arquivo em uma etapa posterior.

Blockchain Gerenciador de Dados requer o código de bytes implantado para o contrato inteligente. O código de bytes implantado é diferente do código de bytes do contrato inteligente. Você pode obter o código de bytes implantado do arquivo de metadados do contrato compilado.

1. Abra o arquivo de metadados de contrato contido na pasta **Compilar/contratos** de seu projeto de solidez. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **. JSON** .
1. Localize o elemento **deployedBytecode** no arquivo JSON.
1. Copie o valor hexadecimal sem as aspas.

    ![Painel de Visual Studio Code com código de bytes nos metadados](./media/data-manager-portal/bytecode-metadata.png)

1. Salve o valor do **código de bytes** como um arquivo JSON. Por exemplo, *código de bytes. JSON*. Você usará o arquivo em uma etapa posterior.

O exemplo a seguir mostra o *Abi. JSON* e os arquivos de *código de bytes. JSON* abertos no editor de vs Code. Os arquivos devem ser semelhantes.

![Exemplo de arquivos Abi. JSON e código de bytes. JSON](./media/data-manager-cosmosdb/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Criar o contrato ABI e URL do código de bytes

Blockchain Gerenciador de Dados requer que os arquivos de código de bytes ABI e do contrato sejam acessíveis por uma URL ao adicionar um aplicativo. Você pode usar uma conta de armazenamento do Azure para fornecer uma URL acessível de forma privada.

#### <a name="create-storage-account"></a>Criar conta de armazenamento

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Carregar arquivos de contrato

1. Crie um novo contêiner para a conta de armazenamento. Selecione **contêineres > contêiner**.

    ![Criar um contêiner de conta de armazenamento](./media/data-manager-cosmosdb/create-container.png)

    | Definição | Descrição |
    |---------|-------------|
    | Nome  | Nomeie o contêiner. Por exemplo, *smartcontract* |
    | Nível de acesso público | Escolha *privado (sem acesso anônimo)* |

1. Selecione **OK** para criar o contentor.
1. Selecione o contêiner e, em seguida, selecione **carregar**.
1. Escolha os arquivos JSON que você criou na seção [obter o Abi do contrato e o código de bytes](#get-contract-abi-and-bytecode) .

    ![Carregar blob](./media/data-manager-cosmosdb/upload-blobs.png)

    Selecione **Upload**.

#### <a name="generate-url"></a>Gerar URL

Para cada blob, gere uma assinatura de acesso compartilhado.

1. Selecione o blob do ABI JSON.
1. Selecione **gerar SAS**
1. Defina expiração de assinatura de acesso desejado e, em seguida, selecione **gerar token SAS de BLOB e URL**.

    ![Gerar token SAS](./media/data-manager-cosmosdb/generate-sas.png)

1. Copie a **URL de SAS do blob** e salve-a para a próxima seção.
1. Repita as etapas de [geração de URL](#generate-url) para o blob JSON de código de bytes.

### <a name="add-helloblockchain-application-to-instance"></a>Adicionar aplicativo helloblockchain à instância

1. Selecione sua instância de Gerenciador de Dados do Blockchain na lista de instâncias.
1. Selecione **aplicativos Blockchain**.
1. Selecione **Adicionar**.

    ![Adicionar um aplicativo blockchain](./media/data-manager-cosmosdb/add-application.png)

    Insira o nome do aplicativo blockchain e as URLs ABI do Smart Contract e código de bytes.

    Definição | Descrição
    --------|------------
    Nome | Insira um nome exclusivo para o aplicativo blockchain rastrear.
    ABI do contrato | Caminho da URL para o arquivo da ABI do contrato. Para obter mais informações, consulte [criar contrato Abi e URL de código de bytes](#create-contract-abi-and-bytecode-url).
    Código de bytes do contrato | Caminho da URL para o arquivo de código de bytes. Para obter mais informações, consulte [criar contrato Abi e URL de código de bytes](#create-contract-abi-and-bytecode-url).

1. Selecione **OK**.

    Depois que o aplicativo é criado, o aplicativo aparece na lista de aplicativos blockchain.

    ![Lista de aplicativos do Blockchain](./media/data-manager-cosmosdb/artifact-list.png)

Você pode excluir a conta de armazenamento do Azure ou usá-la para configurar aplicativos mais blockchain. Se você quiser excluir a conta de armazenamento do Azure, poderá excluir o grupo de recursos. Ao eliminar o grupo de recursos também elimina a conta de armazenamento associada e quaisquer outros recursos associados ao grupo de recursos.

## <a name="create-azure-cosmos-db"></a>Criar Azure Cosmos DB

[!INCLUDE [cosmos-db-create-storage-account](../../../includes/cosmos-db-create-dbaccount.md)]

### <a name="add-a-database-and-container"></a>Adicionar um banco de dados e um contêiner

Você pode usar o Data Explorer no portal do Azure para criar um banco de dados e um contêiner.

1. Selecione **Data Explorer** na barra de navegação à esquerda na página da sua conta do Azure Cosmos DB e, em seguida, selecione **novo contêiner**.
1. No painel **Adicionar contêiner** , insira as configurações para o novo contêiner.

    ![Adicionar configurações de contêiner](./media/data-manager-cosmosdb/add-container.png)

    | Definição | Descrição
    |---------|-------------|
    | ID da base de dados | Insira **blockchain-data** como o nome do novo banco de dados. |
    | Débito | Deixe a taxa de transferência em **400** unidades de solicitação por segundo (ru/s). Se pretender reduzir a latência, pode aumentar o débito mais tarde.|
    | ID do contêiner | Insira **mensagens** como o nome do novo contêiner. |
    | Chave de partição | Use **/MessageType** como a chave de partição. |

1. Selecione **OK**. O Data Explorer exibe o novo banco de dados e o contêiner que você criou.

## <a name="create-logic-app"></a>Criar Aplicação Lógica

Os aplicativos lógicos do Azure ajudam a agendar e automatizar processos de negócios e fluxos de trabalho quando você precisa integrar sistemas e serviços. Você pode usar um aplicativo lógico para conectar a grade de eventos ao Azure Cosmos DB.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Integração** > **Aplicação Lógica**.
1. Forneça detalhes sobre onde criar seu aplicativo lógico. Depois de terminar, selecione **criar**.

    Para obter mais informações sobre como criar aplicativos lógicos, consulte [criar fluxos de trabalho automatizados com aplicativos lógicos do Azure](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Depois que o Azure implantar seu aplicativo, selecione o recurso do aplicativo lógico.
1. No designer de aplicativos lógicos, em **modelos**, selecione **aplicativo lógico em branco**.

### <a name="add-event-grid-trigger"></a>Adicionar gatilho de grade de eventos

Todas as aplicações lógicas têm de iniciar com um acionador, que é desencadeado quando um evento específico acontece ou quando uma condição específica é cumprida. Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica que inicia e executa o fluxo de trabalho. Use um gatilho de grade de eventos do Azure para enviar dados de transação blockchain da grade de eventos para Cosmos DB.

1. No designer de aplicativos lógicos, procure e selecione o conector de **grade de eventos do Azure** .
1. Na guia **gatilhos** , selecione **quando ocorrer um evento de recurso**.
1. Crie uma conexão de API com o tópico da grade de eventos.

    ![Configurações do gatilho da grade de eventos](./media/data-manager-cosmosdb/event-grid-trigger.png)

    | Definição | Descrição
    |---------|-------------|
    | Subscrição | Escolha a assinatura que contém o tópico da grade de eventos. |
    | Tipo de Recurso | Escolha **Microsoft. EventGrid. topics**. |
    | Nome do Recurso | Escolha o nome do tópico da grade de eventos em que Blockchain Gerenciador de Dados está enviando mensagens de dados de transação. |

### <a name="add-cosmos-db-action"></a>Adicionar ação de Cosmos DB

Adicione uma ação para criar um documento no Cosmos DB para cada transação. Use o tipo de mensagem de transação como a chave de partição para categorizar as mensagens.

1. Selecione **Novo passo**.
1. Em **escolher uma ação**, procure **Azure Cosmos DB**.
1. Escolha **Azure Cosmos DB ações de > > criar ou atualizar documento**.
1. Crie uma conexão de API para seu banco de dados Cosmos DB.

    ![Cosmos DB configurações de conexão](./media/data-manager-cosmosdb/cosmosdb-connection.png)

    | Definição | Descrição
    |---------|-------------|
    | Nome da Ligação | Escolha a assinatura que contém o tópico da grade de eventos. |
    | Conta do DocumentDB | Escolha a conta do DocumentDB criada na seção [criar Azure Cosmos DB conta](#create-azure-cosmos-db) . |

1. Insira a **ID do banco de dados** e a **ID da coleção** para seu Azure Cosmos DB que você criou anteriormente na seção [Adicionar um banco de dados e contêiner](#add-a-database-and-container) .

1. Selecione a configuração do **documento** . No pop-out *adicionar conteúdo dinâmico* , selecione **expressão** e copie e cole a seguinte expressão:

    ```
    addProperty(triggerBody()?['data'], 'id', utcNow())
    ```

    A expressão Obtém a parte de dados da mensagem e define a ID como um valor de carimbo de data/hora.

1. Selecione **Adicionar novo parâmetro** e escolha **valor de chave de partição**.
1. Defina o **valor da chave de partição** como `"@{triggerBody()['data']['MessageType']}"`. O valor deve estar entre aspas duplas.

    ![Designer de aplicativos lógicos com configurações de Cosmos DB](./media/data-manager-cosmosdb/create-action.png)

    O valor define a chave de partição para o tipo de mensagem de transação.

1. Selecione **Guardar**.

O aplicativo lógico monitora o tópico da grade de eventos. Quando uma nova mensagem de transação é enviada do Blockchain Gerenciador de Dados, o aplicativo lógico cria um documento no Cosmos DB.

## <a name="send-a-transaction"></a>Enviar uma transação

Em seguida, envie uma transação para o razão blockchain para testar o que você criou. Use o script **SendRequest. js** criado no tutorial de pré-requisito [: Use Visual Studio Code para criar, compilar e implantar contratos inteligentes](send-transaction.md).

No painel de terminal do VS Code, use Truffle para executar o script em sua rede do consórcio blockchain. Na barra de menus do painel terminal, selecione a guia **terminal** e o **PowerShell** na lista suspensa.

``` PowerShell
truffle exec sendrequest.js --network <blockchain network>
```

Substitua \<rede blockchain\> pelo nome da rede blockchain definida no **Truffle-config. js**.

![Enviar transação](./media/data-manager-cosmosdb/send-request.png)

## <a name="view-transaction-data"></a>Exibir dados da transação

Agora que você conectou seu Gerenciador de Dados Blockchain ao Azure Cosmos DB, você pode exibir as mensagens de transação Blockchain no Cosmos DB Data Explorer.

1. Vá para a exibição de Data Explorer de Cosmos DB. Por exemplo, **cosmosdb-blockchain > Data Explorer > blockchain-Data > messages > itens**.

    ![Cosmos DB Data Explorer](./media/data-manager-cosmosdb/data-explorer.png)

    Data Explorer lista as mensagens de dados do blockchain que foram criadas no banco de Cosmos DB do.

1. Navegue pelas mensagens selecionando ID do item e localize a mensagem com o hash de transação correspondente.

    [![detalhes da transação Blockchain](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

    A mensagem de transação bruta contém detalhes sobre a transação. No entanto, as informações de propriedade são criptografadas.

    Como você adicionou o contrato inteligente HelloBlockchain à instância de Gerenciador de Dados do Blockchain, um tipo de mensagem **contractproperties** também é enviado que contém informações de propriedade decodificadas.

1. Localize a mensagem **contractproperties** para a transação. Ela deve ser a próxima mensagem na lista.

    [![detalhes da transação Blockchain](./media/data-manager-cosmosdb/properties-msg.png)](./media/data-manager-cosmosdb/properties-msg.png#lightbox)

    A matriz **DecodedProperties** contém as propriedades da transação.

Parabéns! Você criou com êxito um Gerenciador de mensagens de transação usando Blockchain Gerenciador de Dados e Azure Cosmos DB.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você poderá excluir os recursos e grupos de recursos usados neste tutorial. Para excluir um grupo de recursos:

1. No portal do Azure, navegue até o **grupo de recursos** no painel de navegação esquerdo e selecione o grupo de recursos que deseja excluir.
1. Selecione **Eliminar grupo de recursos**. Verifique a exclusão inserindo o nome do grupo de recursos e selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a integração com os razões blockchain.

> [!div class="nextstepaction"]
> [Usando o conector Ethereum Blockchain com aplicativos lógicos do Azure](ethereum-logic-app.md)
