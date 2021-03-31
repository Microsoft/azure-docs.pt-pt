---
title: Use o Gestor de Dados blockchain para atualizar a Azure Cosmos DB - Azure Blockchain Service
description: Use o Gestor de Dados blockchain para o Serviço Azure Blockchain para enviar dados blockchain para Azure Cosmos DB
ms.date: 03/08/2020
ms.topic: tutorial
ms.reviewer: chroyal
ms.openlocfilehash: 69790787bc888448f2f40178bd12ee7058cc5892
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91283463"
---
# <a name="tutorial-use-blockchain-data-manager-to-send-data-to-azure-cosmos-db"></a>Tutorial: Use o Gestor de Dados blockchain para enviar dados para a Azure Cosmos DB

Neste tutorial, você usa Blockchain Data Manager para O Serviço Azure Blockchain para gravar dados de transações blockchain em Azure Cosmos DB. O Blockchain Data Manager captura, transforma e entrega dados de contabilidade blockchain aos tópicos da grelha de eventos do Azure. A partir da Azure Event Grid, você usa um conector Azure Logic App para criar documentos numa base de dados DB Azure Cosmos. Quando terminar com tutorial, pode explorar dados de transações blockchain no Azure Cosmos DB Data Explorer.

[![A screenshot mostra detalhes da transação blockchain.](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

Neste tutorial:

> [!div class="checklist"]
> * Criar uma instância de Gestor de Dados blockchain
> * Adicione uma aplicação blockchain para descodificar propriedades e eventos de transações
> * Criar uma conta e base de dados DB da Azure Cosmos para armazenar dados de transações
> * Crie uma App Azure Logic para ligar um tópico de grelha de eventos Azure ao Azure Cosmos DB
> * Enviar uma transação para um livro de contabilidade blockchain
> * Ver os dados de transações descodificados no Azure Cosmos DB

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Quickstart Completo: Criar um membro blockchain utilizando o portal Azure](create-member.md) ou [Quickstart: Criar um membro blockchain do Azure Blockchain Service utilizando o Azure CLI](create-member-cli.md)
* [Quickstart completo: Utilize o Código do Estúdio Visual para ligar a uma rede de consórcios do Azure Blockchain Service](connect-vscode.md). O quickstart guia-o embora tenha instalado [o Azure Blockchain Development Kit para o Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) e configurando o seu ambiente de desenvolvimento blockchain.
* [Tutorial Completo: Use o Código do Estúdio Visual para criar, construir e implementar contratos inteligentes](send-transaction.md). O tutorial passa pela criação de um contrato inteligente de amostra.
* Criar um [tópico de grelha de evento](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Saiba mais sobre [os manipuladores de eventos em Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Criar exemplo

Uma instância do Gestor de Dados blockchain conecta e monitoriza um nó de transação do Serviço Azure Blockchain. Um caso captura todos os dados brutos de blocos brutos e transações brutas do nó de transação. Uma ligação de saída envia dados blockchain para Azure Event Grid. Configura uma única ligação de saída quando cria o caso.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Vá ao membro do Serviço Azure Blockchain que criou no [Quickstart pré-requisito: Crie um membro blockchain utilizando o portal Azure](create-member.md). Selecione **Blockchain Data Manager**.
1. Selecione **Adicionar**.

    ![Adicionar Gestor de Dados blockchain](./media/data-manager-cosmosdb/add-instance.png)

    Introduza os seguintes detalhes:

    Definição | Exemplo | Descrição
    --------|---------|------------
    Nome | mywatcher | Insira um nome único para um Gestor de Dados blockchain conectado.
    Nó de transação | myblockchainmember | Escolha o nó de transação padrão do membro do Serviço Azure Blockchain que criou no pré-requisito.
    Nome da ligação | cosmosdb | Introduza um nome único da ligação de saída onde os dados de transação blockchain são enviados.
    Ponto final da grelha de eventos | myTopic | Escolha um tópico de grelha de eventos que criou no pré-requisito. Nota: A instância do Gestor de Dados blockchain e o tópico da grelha de eventos devem estar na mesma subscrição.

1. Selecione **OK**.

    Demora menos de um minuto a criar uma instância do Gestor de Dados blockchain. Após a implementação do caso, é automaticamente iniciado. Uma instância de Gestor de Dados blockchain em execução captura eventos blockchain a partir do nó de transação e envia dados para a grelha de eventos.

## <a name="add-application"></a>Adicionar aplicação

Adicione a aplicação **helloblockchain** blockchain para que o Blockchain Data Manager descodifica o evento e o estado da propriedade. O Blockchain Data Manager requer o contrato inteligente ABI e o ficheiro bytecode para adicionar a aplicação.

### <a name="get-contract-abi-and-bytecode"></a>Obtenha contrato ABI e bytecode

O contrato ABI define as interfaces de contrato inteligente. Descreve como interagir com o contrato inteligente. Pode utilizar o [Kit de Desenvolvimento Azure Blockchain para a extensão Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) para copiar o contrato ABI para a área de transferência.

1. No painel de exploradores visual Studio Code, expanda a pasta de **construção/contratos** do projeto **Helloblockchain** Solidity que criou no tutorial pré-requisito: [Use o Código do Estúdio Visual para criar, construir e implementar contratos inteligentes.](send-transaction.md)
1. Clique com o botão direito no ficheiro JSON de metadados contratuais. O nome do ficheiro é o nome do contrato inteligente seguido da extensão **.json.**
1. Selecione **Copy Contract ABI**.

    ![Painel visual studio code com a seleção de ABI do contrato de cópia](./media/data-manager-cosmosdb/abi-devkit.png)

    O contrato ABI é copiado para a área de transferência.

1. Guarde a matriz **abi** como um ficheiro JSON. Por exemplo, *abi.js.* Usa o ficheiro num passo posterior.

O Gestor de Dados blockchain requer o código de acesso implementado para o contrato inteligente. O código porte código implementado é diferente do código de contrato inteligente. Utilize a extensão do kit de desenvolvimento da blockchain Azure para copiar o código de acesso à área de transferência.

1. No painel de exploradores visual Studio Code, expanda a pasta **de construção/contratos** do seu projeto Solidity.
1. Clique com o botão direito no ficheiro JSON de metadados contratuais. O nome do ficheiro é o nome do contrato inteligente seguido da extensão **.json.**
1. Selecione **Copy Transaction Bytecode**.

    ![Painel visual studio code com a seleção de código de transação de cópia](./media/data-manager-cosmosdb/bytecode-devkit.png)

    O código bytecode é copiado para a área de transferência.

1. Guarde o valor **do código de acesso** como ficheiro JSON. Por exemplo, *bytecode.js.* Usa o ficheiro num passo posterior.

O exemplo que se segue mostra *abi.jse* *bytecode.jsem* ficheiros abertos no editor do Código VS. Os seus ficheiros devem ser semelhantes.

![Exemplo de abi.jsem e bytecode.jsem ficheiros](./media/data-manager-cosmosdb/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Criar contrato ABI e BYTECODE URL

O Blockchain Data Manager exige que os ficheiros ABI e bytecode do contrato sejam acessíveis por um URL ao adicionar uma aplicação. Pode utilizar uma conta de Armazenamento Azure para fornecer um URL acessível a privados.

#### <a name="create-storage-account"></a>Criar conta de armazenamento

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Carregar ficheiros de contratos

1. Criar um novo recipiente para a conta de armazenamento. Selecione **recipientes > Recipiente**.

    ![Criar um recipiente de conta de armazenamento](./media/data-manager-cosmosdb/create-container.png)

    | Definições | Descrição |
    |---------|-------------|
    | Nome  | Dê um nome ao contentor. Por exemplo, *smartcontract* |
    | Nível de acesso público | Escolha *Privado (sem acesso anónimo)* |

1. Selecione **OK** para criar o contentor.
1. Selecione o recipiente e, em seguida, **selecione Upload**.
1. Escolha ambos os ficheiros JSON que criou na secção [Get Contract ABI e bytecode.](#get-contract-abi-and-bytecode)

    ![Bolha de upload](./media/data-manager-cosmosdb/upload-blobs.png)

    Selecione **Carregar**.

#### <a name="generate-url"></a>Gerar URL

Para cada bolha, gere uma assinatura de acesso partilhado.

1. Selecione a bolha ABI JSON.
1. **Selecione gerar SAS**
1. Descreva a expiração da assinatura de acesso desejada e, em seguida, **selecione Gerar o símbolo e o URL blob SAS**.

    ![Gerar ficha SAS](./media/data-manager-cosmosdb/generate-sas.png)

1. Copie o **URL Blob SAS** e guarde-o para a secção seguinte.
1. Repita os passos [de URL de Geração](#generate-url) para a bolha de código JSON.

### <a name="add-helloblockchain-application-to-instance"></a>Adicione a aplicação helloblockchain à instância

1. Selecione a sua instância do Gestor de Dados blockchain na lista de casos.
1. Selecione **aplicações Blockchain**.
1. Selecione **Adicionar**.

    ![Adicionar uma aplicação blockchain](./media/data-manager-cosmosdb/add-application.png)

    Introduza o nome da aplicação blockchain e o contrato inteligente ABI e BYtecode URLs.

    Definições | Descrição
    --------|------------
    Nome | Introduza um nome único para a aplicação blockchain para rastrear.
    Contrato ABI | Caminho URL para o ficheiro ABI do contrato. Para obter mais informações, consulte [Criar contrato ABI e BYTECODE URL](#create-contract-abi-and-bytecode-url).
    Bytecode do Contrato | Caminho URL para o ficheiro bytecode. Para obter mais informações, consulte [Criar contrato ABI e BYTECODE URL](#create-contract-abi-and-bytecode-url).

1. Selecione **OK**.

    Uma vez criada a aplicação, a aplicação aparece na lista de aplicações blockchain.

    ![Lista de aplicações blockchain](./media/data-manager-cosmosdb/artifact-list.png)

Pode eliminar a conta de Armazenamento Azure ou usá-la para configurar mais aplicações blockchain. Se desejar eliminar a conta de Armazenamento Azure, pode eliminar o grupo de recursos. Ao eliminar o grupo de recursos também elimina a conta de armazenamento associada e quaisquer outros recursos associados ao grupo de recursos.

## <a name="create-azure-cosmos-db"></a>Criar uma Base de Dados do Azure Cosmos DB

[!INCLUDE [cosmos-db-create-storage-account](../../../includes/cosmos-db-create-dbaccount.md)]

### <a name="add-a-database-and-container"></a>Adicione uma base de dados e um recipiente

Pode utilizar o Data Explorer no portal Azure para criar uma base de dados e um recipiente.

1. Selecione Data **Explorer** a partir da navegação à esquerda na sua página de conta DB Azure Cosmos e, em seguida, selecione **Novo Recipiente**.
1. No painel de **recipiente Adicionar,** introduza as definições para o novo recipiente.

    ![Adicione as definições do recipiente](./media/data-manager-cosmosdb/add-container.png)

    | Definições | Descrição
    |---------|-------------|
    | ID da base de dados | Introduza **os dados blockchain** como o nome da nova base de dados. |
    | Débito | Deixe a produção a **400** unidades de pedido por segundo (RU/s). Se pretender reduzir a latência, pode aumentar verticalmente o débito mais tarde.|
    | ID do Contentor | Introduza **mensagens** como o nome do seu novo recipiente. |
    | Chave de partição | Utilize **/MessageType** como chave de partição. |

1. Selecione **OK**. O Data Explorer apresenta a nova base de dados e o recipiente que criou.

## <a name="create-logic-app"></a>Criar Aplicação Lógica

O Azure Logic Apps ajuda-o a agendar e automatizar processos de negócio e fluxos de trabalho quando precisa de integrar sistemas e serviços. Você pode usar uma aplicação lógica para ligar a Grade de Eventos a Azure Cosmos DB.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Integração** > **Aplicação Lógica**.
1. Forneça detalhes sobre onde criar a sua aplicação lógica. Depois de terminar, **selecione Criar**.

    Para obter mais informações sobre a criação de aplicações [lógicas, consulte Criar fluxos de trabalho automatizados com apps Azure Logic.](../../logic-apps/quickstart-create-first-logic-app-workflow.md)

1. Depois de o Azure implementar a sua aplicação, selecione o seu recurso de aplicação lógica.
1. No Design de Aplicações **Lógicas, em Modelos,** selecione **Blank Logic App**.

### <a name="add-event-grid-trigger"></a>Adicionar gatilho de grelha de evento

Todas as aplicações lógicas têm de iniciar com um acionador, que é desencadeado quando um evento específico acontece ou quando uma condição específica é cumprida. Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica que inicia e executa o fluxo de trabalho. Utilize um gatilho de grelha de eventos Azure para enviar dados de transação blockchain da Grade de Eventos para a Cosmos DB.

1. No Logic Apps Designer, procure e selecione o conector **Azure Event Grid.**
1. A partir do **separador Gatilhos,** selecione **Quando ocorrer um evento de recurso**.
1. Crie uma ligação API ao tópico da grelha de eventos.

    ![Definições de gatilho da grelha de evento](./media/data-manager-cosmosdb/event-grid-trigger.png)

    | Definições | Descrição
    |---------|-------------|
    | Subscrição | Escolha a subscrição que contém o Tópico da Grelha de Eventos. |
    | Tipo de Recurso | Escolha **Microsoft.EventGrid.Topics**. |
    | Nome do Recurso | Escolha o nome do Tópico de Grelha de Eventos onde o Blockchain Data Manager está a enviar mensagens de dados de transação. |

### <a name="add-cosmos-db-action"></a>Adicione ação de Cosmos DB

Adicione uma ação para criar um documento em Cosmos DB para cada transação. Utilize o tipo de mensagem de transação como chave de partição para categorizar as mensagens.

1. Selecione **Novo passo**.
1. Em **Escolha uma ação**, procure **por Azure Cosmos DB**.
1. Escolha **Azure Cosmos DB > Ações > Criar ou atualizar documento**.
1. Crie uma ligação API à sua base de dados Cosmos DB.

    ![Definições de conexão Cosmos DB](./media/data-manager-cosmosdb/cosmosdb-connection.png)

    | Definições | Descrição
    |---------|-------------|
    | Nome da Ligação | Escolha a subscrição que contém o Tópico da Grelha de Eventos. |
    | Conta DocumentDB | Escolha a conta DocumentDB que criou na secção [Descosus cosmos.](#create-azure-cosmos-db) |

1. Introduza o **ID da base de dados** e **o ID de recolha** para o seu DB Azure Cosmos que criou anteriormente na secção Adicionar uma base de [dados e contentores.](#add-a-database-and-container)

1. Selecione a definição **de Documento.** No *pop-out de conteúdo dinâmico Add,* selecione **Expressão** e copie e cole a seguinte expressão:

    ```
    addProperty(triggerBody()?['data'], 'id', utcNow())
    ```

    A expressão obtém a parte de dados da mensagem e define o ID para um valor de marca de tempo.

1. **Selecione Adicione novo parâmetro** e escolha o valor da chave de **partição.**
1. Desajei o valor da **chave partição** para `"@{triggerBody()['data']['MessageType']}"` . O valor deve ser rodeado de citações duplas.

    ![Designer de apps lógicas com definições de DB cosmos](./media/data-manager-cosmosdb/create-action.png)

    O valor define a chave de partição para o tipo de mensagem de transação.

1. Selecione **Guardar**.

A aplicação lógica monitoriza o Tópico da Grelha de Eventos. Quando uma nova mensagem de transação é enviada do Blockchain Data Manager, a aplicação lógica cria um documento em Cosmos DB.

## <a name="send-a-transaction"></a>Enviar uma transação

Em seguida, envie uma transação para o livro de contabilidade blockchain para testar o que criou. Utilize a função **SendRequest** do contrato **HelloBlockchain** que criou no Tutorial pré-requisito: Use o [Código do Estúdio Visual para criar, construir e implementar contratos inteligentes](send-transaction.md).

1. Utilize a página de interação inteligente do Kit de Desenvolvimento blockchain Azure para chamar a função **SendRequest.** Clique com o botão direito **HelloBlockchain.sol** e escolha Mostrar Página de **Interação Com Contrato Inteligente** no menu.

    ![Escolha mostrar página de interação de contrato inteligente do menu](./media/data-manager-cosmosdb/contract-interaction.png)

1. Escolha ação contratual **SendRequest** e insira **Hello, Blockchain!** para o **parâmetro de mesagens de pedido.** Selecione **Executar** para ligar para a função **SendRequest** através de uma transação.

    ![Execute a ação SendRequest](./media/data-manager-cosmosdb/sendrequest-action.png)

A função SendRequest define os campos **RequestMessage** e **State.** O estado atual **de RequestMessage** é o argumento que passou **Hello, Blockchain.** O valor do campo **do Estado** continua a ser **Pedido.**

## <a name="view-transaction-data"></a>Ver dados de transações

Agora que ligou o seu Gestor de Dados blockchain ao Azure Cosmos DB, pode ver as mensagens de transação blockchain no Cosmos DB Data Explorer.

1. Vá à vista cosmos DB Data Explorer. Por exemplo, **o cosmosdb-blockchain > Data Explorer > > de dados de blockchain mensagens > Itens**.

    ![Cosmos DB Data Explorer](./media/data-manager-cosmosdb/data-explorer.png)

    O Data Explorer lista as mensagens de dados blockchain que foram criadas na base de dados do Cosmos DB.

1. Navegue pelas mensagens selecionando o ID do item e encontre a mensagem com o haxixe de transação correspondente.

    [![A screenshot mostra os detalhes da transação blockchain de um item selecionado.](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

    A mensagem de transação bruta contém detalhes sobre a transação. No entanto, a informação da propriedade está encriptada.

    Uma vez que adicionou o contrato inteligente HelloBlockchain à instância do Gestor de Dados blockchain, também é enviado um tipo de mensagem **ContractProperties** que contém informações de propriedade descodificados.

1. Encontre a mensagem **ContractProperties** para a transação. Deve ser a próxima mensagem da lista.

    [![Detalhe de transação blockchain](./media/data-manager-cosmosdb/properties-msg.png)](./media/data-manager-cosmosdb/properties-msg.png#lightbox)

    A matriz **de DecodedProperties** contém as propriedades da transação.

Parabéns! Criou com sucesso um explorador de mensagens de transação utilizando o Blockchain Data Manager e o Azure Cosmos DB.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode eliminar os recursos e grupos de recursos utilizados para este tutorial. Para eliminar um grupo de recursos:

1. No portal Azure, navegue para o **grupo De recursos** no painel de navegação esquerdo e selecione o grupo de recursos que pretende eliminar.
1. Selecione **Eliminar grupo de recursos**. Verifique a eliminação introduzindo o nome do grupo de recursos e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a integração com os livros blockchain.

> [!div class="nextstepaction"]
> [Utilizando o conector Ethereum Blockchain com apps Azure Logic](ethereum-logic-app.md)
