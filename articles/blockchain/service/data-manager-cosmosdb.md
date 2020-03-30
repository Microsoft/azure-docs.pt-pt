---
title: Use o Gestor de Dados blockchain para atualizar o Azure Cosmos DB - Serviço Azure Blockchain
description: Use o Gestor de Dados blockchain para o Serviço Azure Blockchain para enviar dados blockchain para o Azure Cosmos DB
ms.date: 03/08/2020
ms.topic: tutorial
ms.reviewer: chroyal
ms.openlocfilehash: 483a5246274f63549dfb2914361ede6aa001e02e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79533186"
---
# <a name="tutorial-use-blockchain-data-manager-to-send-data-to-azure-cosmos-db"></a>Tutorial: Use blockchain Data Manager para enviar dados para O Azure Cosmos DB

Neste tutorial, você usa O Gestor de Dados blockchain para o Serviço Azure Blockchain para gravar dados de transação blockchain em Azure Cosmos DB. O Blockchain Data Manager captura, transforma e entrega dados do livro de blockchain aos Tópicos da Grelha de Eventos Do Evento Azure. A partir da Azure Event Grid, você usa um conector azure logic app para criar documentos numa base de dados Azure Cosmos DB. Quando terminar com tutorial, pode explorar dados de transações blockchain no Azure Cosmos DB Data Explorer.

[![Detalhe de transação blockchain](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

Neste tutorial:

> [!div class="checklist"]
> * Criar uma instância de Gestor de Dados blockchain
> * Adicione uma aplicação blockchain para descodificar propriedades e eventos de transação
> * Criar uma conta e base de dados Azure Cosmos DB para armazenar dados de transações
> * Crie uma App Lógica Azure para ligar um tópico de grelha de eventos Azure ao Azure Cosmos DB
> * Envie uma transação para um livro-razão blockchain
> * Ver os dados de transações descodificados no Azure Cosmos DB

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Complete [Quickstart: Criar um membro blockchain usando o portal Azure](create-member.md) ou [Quickstart: Criar um membro blockchain Azure Blockchain Service usando o Azure CLI](create-member-cli.md)
* Complete [Quickstart: Use visual studio code para ligar a uma rede](connect-vscode.md)de consórcio azure Blockchain Service . O quickstart guia-o apesar de instalar o Kit de Desenvolvimento azure blockchain para o [Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) e configurar o seu ambiente de desenvolvimento blockchain.
* Tutorial completo: Use o [Código do Estúdio Visual para criar, construir e implementar contratos inteligentes.](send-transaction.md) O tutorial passa pela criação de um contrato inteligente de amostra.
* Criar um tópico de grelha de [eventos](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Saiba mais sobre [os manipuladores de eventos na Grelha de Eventos Azure](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Criar instância

Uma instância do Gestor de Dados blockchain conecta e monitoriza um nó de transações do Serviço Azure Blockchain. Um caso captura todos os dados de transações brutas e blocos brutos do nó de transações. Uma ligação de saída envia dados blockchain para a Rede de Eventos Azure. Configura uma única ligação de saída quando cria a instância.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Vá ao membro do Serviço Azure Blockchain que criou no [pré-requisito Quickstart: Crie um membro blockchain utilizando o portal Azure](create-member.md). Selecione **Blockchain Data Manager**.
1. Selecione **Adicionar**.

    ![Adicionar Gestor de Dados blockchain](./media/data-manager-cosmosdb/add-instance.png)

    Introduza os seguintes detalhes:

    Definição | Exemplo | Descrição
    --------|---------|------------
    Nome | mywatcher | Introduza um nome único para um Gestor de Dados blockchain conectado.
    Nó de transação | myblockchainmember | Escolha o nó de transações padrão do membro do Serviço Azure Blockchain que criou no pré-requisito.
    Nome da ligação | cosmosdb | Introduza um nome único da ligação de saída onde os dados de transação blockchain são enviados.
    Ponto final da grelha do evento | myTopic | Escolha um tópico de grelha de eventos que criou no pré-requisito. Nota: A instância do Gestor de Dados blockchain e o tópico da grelha de eventos devem estar na mesma subscrição.

1. Selecione **OK**.

    Demora menos de um minuto a criar uma instância do Gestor de Dados blockchain. Após a ocorrência ser implantada, é automaticamente iniciada. Uma instância de Blockchain Data Manager captura eventos blockchain a partir do nó de transações e envia dados para a grelha de eventos.

## <a name="add-application"></a>Adicionar aplicação

Adicione a aplicação **blockchain helloblockchain** para que o Blockchain Data Manager descodifique o evento e o estado de propriedade. O Blockchain Data Manager requer o contrato inteligente ABI e ficheiro bytecode para adicionar a aplicação.

### <a name="get-contract-abi-and-bytecode"></a>Obtenha contrato ABI e bytecode

O contrato ABI define as interfaces inteligentes do contrato. Descreve como interagir com o contrato inteligente. Pode utilizar o Kit de [Desenvolvimento azure blockchain para extensão Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) para copiar o contrato ABI para a área de receita.

1. No painel de explorador esguia do Código do Estúdio Visual, expanda a pasta **build/contracts** do projeto **Helloblockchain** Solidity que criou no pré-requisito [Tutorial: Use visual studio code para criar, construir e implementar contratos inteligentes.](send-transaction.md)
1. Clique no ficheiro JSON dos metadados do contrato. O nome do ficheiro é o nome do contrato inteligente seguido da extensão **.json.**
1. Selecione **Copy Contract ABI**.

    ![Painel de Código de Estúdio Visual com a seleção ABI do contrato de cópia](./media/data-manager-cosmosdb/abi-devkit.png)

    O contrato ABI é copiado para a prancheta.

1. Guarde a matriz **de abi** como um ficheiro JSON. Por exemplo, *abi.json*. Usa o ficheiro num passo posterior.

O Blockchain Data Manager requer o código de envio implementado para o contrato inteligente. O código de envio é diferente do código de contrato inteligente. Utilize a extensão do kit de desenvolvimento da blockchain Azure para copiar o código de acesso à pasta.

1. No painel de explorador esguia do Código do Estúdio Visual, expanda a pasta **build/contracts** do seu projeto Solidity.
1. Clique no ficheiro JSON dos metadados do contrato. O nome do ficheiro é o nome do contrato inteligente seguido da extensão **.json.**
1. Selecione **Copy Transaction Bytecode**.

    ![Painel de código de estúdio visual com a seleção de Bytecode de Transação de Cópia](./media/data-manager-cosmosdb/bytecode-devkit.png)

    O código de acesso é copiado para a pasta.

1. Guarde o valor do **código de byte** como ficheiro JSON. Por exemplo, *bytecode.json*. Usa o ficheiro num passo posterior.

O exemplo que se segue mostra *ficheiros abi.json* e *bytecode.json abertos* no editor do Código VS. Os seus ficheiros devem parecer semelhantes.

![Exemplo de ficheiros abi.json e bytecode.json](./media/data-manager-cosmosdb/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Criar contrato ABI e bytecode URL

O Blockchain Data Manager exige que os ficheiros ABI e bytecode sejam acessíveis por um URL ao adicionar uma aplicação. Pode utilizar uma conta de Armazenamento Azure para fornecer um URL acessível a privados.

#### <a name="create-storage-account"></a>Criar conta de armazenamento

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Carregar ficheiros de contratos

1. Crie um novo recipiente para a conta de armazenamento. Selecione **recipientes > recipiente**.

    ![Criar um recipiente de conta de armazenamento](./media/data-manager-cosmosdb/create-container.png)

    | Definição | Descrição |
    |---------|-------------|
    | Nome  | Dê um nome ao contentor. Por exemplo, *contrato inteligente* |
    | Nível de acesso público | Escolha *O Privado (sem acesso anónimo)* |

1. Selecione **OK** para criar o contentor.
1. Selecione o recipiente e selecione **Upload**.
1. Escolha os dois ficheiros JSON que criou na secção [Get Contract ABI e bytecode.](#get-contract-abi-and-bytecode)

    ![Carregar blob](./media/data-manager-cosmosdb/upload-blobs.png)

    Selecione **Upload**.

#### <a name="generate-url"></a>Gerar URL

Para cada bolha, gere uma assinatura de acesso partilhado.

1. Selecione a bolha ABI JSON.
1. Selecione **Generate SAS**
1. Detete a expiração da assinatura de acesso desejada e, em seguida, **selecione Generate blob SAS token e URL**.

    ![Gerar ficha SAS](./media/data-manager-cosmosdb/generate-sas.png)

1. Copie o **URL Blob SAS** e guarde-o para a próxima secção.
1. Repita os passos [de URL de geração](#generate-url) para a bolha JSON bytecode.

### <a name="add-helloblockchain-application-to-instance"></a>Adicione a aplicação helloblockchain à instância

1. Selecione a sua instância de Gestor de Dados blockchain na lista de instâncias.
1. Selecione **aplicações Blockchain**.
1. Selecione **Adicionar**.

    ![Adicione uma aplicação blockchain](./media/data-manager-cosmosdb/add-application.png)

    Introduza o nome da aplicação blockchain e do contrato inteligente ABI e bytecode URLs.

    Definição | Descrição
    --------|------------
    Nome | Introduza um nome único para a aplicação blockchain rastrear.
    Contrato ABI | URL caminho para o ficheiro CONTRACT ABI. Para mais informações, consulte [Criar contrato ABI e bytecode URL](#create-contract-abi-and-bytecode-url).
    Código de contrato | URL caminho para ficheiro bytecode. Para mais informações, consulte [Criar contrato ABI e bytecode URL](#create-contract-abi-and-bytecode-url).

1. Selecione **OK**.

    Uma vez criada a aplicação, a aplicação aparece na lista de aplicações blockchain.

    ![Lista de aplicações blockchain](./media/data-manager-cosmosdb/artifact-list.png)

Pode eliminar a conta De armazenamento do Azure ou usá-la para configurar mais aplicações blockchain. Se pretender eliminar a conta De armazenamento Azure, pode eliminar o grupo de recursos. Ao eliminar o grupo de recursos também elimina a conta de armazenamento associada e quaisquer outros recursos associados ao grupo de recursos.

## <a name="create-azure-cosmos-db"></a>Criar uma Base de Dados do Azure Cosmos DB

[!INCLUDE [cosmos-db-create-storage-account](../../../includes/cosmos-db-create-dbaccount.md)]

### <a name="add-a-database-and-container"></a>Adicione uma base de dados e um recipiente

Pode utilizar o Data Explorer no portal Azure para criar uma base de dados e um contentor.

1. Selecione **Data Explorer** a partir da navegação esquerda na página da conta Azure Cosmos DB e, em seguida, selecione **Novo Recipiente**.
1. No painel do **recipiente Adicionar,** introduza as definições para o novo recipiente.

    ![Adicionar definições de recipiente](./media/data-manager-cosmosdb/add-container.png)

    | Definição | Descrição
    |---------|-------------|
    | ID da base de dados | Introduza os **dados blockchain** como o nome da nova base de dados. |
    | Débito | Deixe a entrada em **400** unidades de pedido por segundo (RU/s). Se pretender reduzir a latência, pode aumentar o débito mais tarde.|
    | ID do Contentor | Introduza **mensagens** como nome para o seu novo recipiente. |
    | Chave de partição | Utilize **/MessageType** como chave de partição. |

1. Selecione **OK**. O Data Explorer exibe a nova base de dados e o recipiente que criou.

## <a name="create-logic-app"></a>Criar Aplicação Lógica

As Aplicações Lógicas Azure ajudam-no a agendar e automatizar processos de negócio e fluxos de trabalho quando precisa de integrar sistemas e serviços. Você pode usar uma aplicação lógica para ligar Event Grid a Azure Cosmos DB.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Integração** > **Aplicação Lógica**.
1. Forneça detalhes sobre onde criar a sua aplicação lógica. Depois de terminar, selecione **Criar**.

    Para obter mais informações sobre a criação de apps lógicas, consulte [Criar fluxos de trabalho automatizados com aplicações lógicas azure](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Depois do Azure implementar a sua aplicação, selecione o seu recurso de aplicação lógica.
1. No Designer de Aplicações Lógicas, em **Modelos,** selecione **App lógica em branco**.

### <a name="add-event-grid-trigger"></a>Adicionar gatilho da grelha de eventos

Todas as aplicações lógicas têm de iniciar com um acionador, que é desencadeado quando um evento específico acontece ou quando uma condição específica é cumprida. Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica que inicia e executa o fluxo de trabalho. Utilize um gatilho da Rede de Eventos Azure para enviar dados de transação blockchain da Rede de Eventos para o Cosmos DB.

1. No Logic Apps Designer, procure e selecione o conector **Azure Event Grid.**
1. A partir do separador **Triggers,** selecione **Quando ocorrer um evento**de recurso .
1. Crie uma ligação API ao seu Tópico de Grelha de Eventos.

    ![Definições do gatilho da grelha do evento](./media/data-manager-cosmosdb/event-grid-trigger.png)

    | Definição | Descrição
    |---------|-------------|
    | Subscrição | Escolha a subscrição que contém o Tópico da Grelha de Eventos. |
    | Tipo de Recurso | Escolha **Microsoft.EventGrid.Tópicos**. |
    | Nome do Recurso | Escolha o nome do Tópico da Grelha de Eventos onde o Gestor de Dados blockchain está a enviar mensagens de dados de transação. |

### <a name="add-cosmos-db-action"></a>Adicionar ação Cosmos DB

Adicione uma ação para criar um documento em Cosmos DB para cada transação. Utilize o tipo de mensagem de transação como chave de partição para categorizar as mensagens.

1. Selecione **Novo passo**.
1. Em **Escolha uma ação**, procure **Azure Cosmos DB**.
1. Escolha **as ações > da Azure Cosmos DB > criar ou atualizar documentos.**
1. Crie uma ligação API à sua base de dados Cosmos DB.

    ![Definições de conexão Cosmos DB](./media/data-manager-cosmosdb/cosmosdb-connection.png)

    | Definição | Descrição
    |---------|-------------|
    | Nome da Ligação | Escolha a subscrição que contém o Tópico da Grelha de Eventos. |
    | Conta DocumentDB | Escolha a conta DocumentDB que criou na secção de [conta Create Azure Cosmos DB.](#create-azure-cosmos-db) |

1. Introduza o **ID** de base de **dados** e o ID de recolha para o seu Azure Cosmos DB que criou anteriormente na base de dados e secção [de contentores.](#add-a-database-and-container)

1. Selecione a definição de **Documento.** No pop-out de *conteúdo dinâmico Adicionar,* selecione **Expressão** e copie e cole a seguinte expressão:

    ```
    addProperty(triggerBody()?['data'], 'id', utcNow())
    ```

    A expressão obtém a parte de dados da mensagem e define o ID para um valor de carimbo temporal.

1. **Selecione Adicionar novo parâmetro** e escolha o valor da chave da **partilha**.
1. Desdefinir o `"@{triggerBody()['data']['MessageType']}"`valor da chave da **partilha** para . O valor deve ser rodeado de orçamentos duplos.

    ![Logic Apps Designer com configurações cosmos DB](./media/data-manager-cosmosdb/create-action.png)

    O valor define a chave de partição para o tipo de mensagem de transação.

1. Selecione **Guardar**.

A aplicação lógica monitoriza o Tópico da Grelha de Eventos. Quando uma nova mensagem de transação é enviada do Blockchain Data Manager, a aplicação lógica cria um documento em Cosmos DB.

## <a name="send-a-transaction"></a>Enviar uma transação

Em seguida, envie uma transação para o livro de blockchain para testar o que criou. Utilize a função **SendRequest** do contrato **HelloBlockchain** que criou no tutorial pré-requisito: Use o Código do [Estúdio Visual para criar, construir e implementar contratos inteligentes](send-transaction.md).

1. Utilize a página de interação inteligente do Kit de Desenvolvimento de Blockchain Azure para ligar para a função **SendRequest.** Clique no **HelloBlockchain.sol** e escolha página de **interação de contrato inteligente** do menu.

    ![Escolha mostrar página de interação de contrato inteligente a partir do menu](./media/data-manager-cosmosdb/contract-interaction.png)

1. Escolha a ação do contrato **SendRequest** e insira **Hello, Blockchain!** para o **parâmetro de mensagemMensagem.** Selecione **Executar** para ligar para a função **Enviar Solicitar** através de uma transação.

    ![Executar ação SendRequest](./media/data-manager-cosmosdb/sendrequest-action.png)

A função SendRequest define os campos **RequestMessage** e **State.** O estado atual do **RequestMessage** é o argumento que passou **olá, Blockchain.** O valor do campo **do Estado** continua a ser **pedido.**

## <a name="view-transaction-data"></a>Ver dados de transações

Agora que ligou o seu Gestor de Dados blockchain ao Azure Cosmos DB, pode ver as mensagens de transação blockchain no Cosmos DB Data Explorer.

1. Vá à vista cosmos DB Data Explorer. Por exemplo, **o cosmosdb-blockchain > Data Explorer > blockchain-data > Mensagens > Itens**.

    ![Cosmos DB Data Explorer](./media/data-manager-cosmosdb/data-explorer.png)

    O Data Explorer lista as mensagens de dados blockchain que foram criadas na base de dados cosmos DB.

1. Navegue através das mensagens selecionando id do item e encontre a mensagem com o hash de transação correspondente.

    [![Detalhe de transação blockchain](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

    A mensagem de transação bruta contém detalhes sobre a transação. No entanto, a informação da propriedade é encriptada.

    Uma vez que adicionou o contrato inteligente HelloBlockchain à instância do Blockchain Data Manager, é também enviado um tipo de mensagem **ContractProperties** que contém informações de propriedade descodificadas.

1. Encontre a mensagem **ContractProperties** para a transação. Deve ser a próxima mensagem da lista.

    [![Detalhe de transação blockchain](./media/data-manager-cosmosdb/properties-msg.png)](./media/data-manager-cosmosdb/properties-msg.png#lightbox)

    A matriz **DecodedProperties** contém as propriedades da transação.

Parabéns! Criou com sucesso um explorador de mensagens de transação usando o Blockchain Data Manager e o Azure Cosmos DB.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode eliminar os recursos e grupos de recursos utilizados para este tutorial. Para eliminar um grupo de recursos:

1. No portal Azure, navegue para o **grupo Recurso** no painel de navegação à esquerda e selecione o grupo de recursos que pretende eliminar.
1. Selecione **Eliminar grupo de recursos**. Verifique a eliminação inserindo o nome do grupo de recursos e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a integração com os livros blockchain.

> [!div class="nextstepaction"]
> [Utilização do conector Ethereum Blockchain com aplicações lógicas azure](ethereum-logic-app.md)
