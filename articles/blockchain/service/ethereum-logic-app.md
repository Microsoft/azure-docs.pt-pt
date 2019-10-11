---
title: Usar o conector Ethereum Blockchain com aplicativos lógicos do Azure
description: Use o conector Ethereum Blockchain com aplicativos lógicos do Azure para disparar funções de contrato inteligente e responder a eventos de contrato inteligente.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/26/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: 26652a956f9f50f82f462c564d6daf0dc37b0722
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254959"
---
# <a name="use-the-ethereum-blockchain-connector-with-azure-logic-apps"></a>Usar o conector Ethereum Blockchain com aplicativos lógicos do Azure

Use o [conector Ethereum Blockchain](https://docs.microsoft.com/connectors/blockchainethereum/) com [aplicativos lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/) para executar ações de contrato inteligente e responder a eventos de contrato inteligente. Por exemplo, digamos que você deseja criar um microserviço baseado em REST que retorna informações de uma razão blockchain. Usando um aplicativo lógico, você pode aceitar solicitações HTTP que consultam informações armazenadas em uma contabilidade blockchain.

## <a name="prerequisites"></a>Pré-requisitos

Conclua o início rápido de pré-requisito opcional [: Use Visual Studio Code para se conectar a uma rede do Azure Blockchain Service Consortium](connect-vscode.md). O guia de início rápido orienta você durante a instalação do [Kit de desenvolvimento do Azure Blockchain para Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) e a configuração do ambiente de desenvolvimento de Blockchain.

## <a name="create-a-logic-app"></a>Criar uma aplicação lógica

Os aplicativos lógicos do Azure ajudam a agendar e automatizar processos de negócios e fluxos de trabalho quando você precisa integrar sistemas e serviços. Primeiro, você cria uma lógica que usa o conector Ethereum Blockchain.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Integração** > **Aplicação Lógica**.
1. Em **criar aplicativo lógico**, forneça detalhes sobre onde criar seu aplicativo lógico. Depois de terminar, selecione **criar**.

    Para obter mais informações sobre como criar aplicativos lógicos, consulte [criar fluxos de trabalho automatizados com aplicativos lógicos do Azure](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Depois que o Azure implantar seu aplicativo, selecione o recurso do aplicativo lógico.
1. No designer de aplicativos lógicos, em **modelos**, selecione **aplicativo lógico em branco**.

Todas as aplicações lógicas têm de iniciar com um acionador, que é desencadeado quando um evento específico acontece ou quando uma condição específica é cumprida. Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica que inicia e executa o fluxo de trabalho.

O conector Ethereum Blockchain tem um gatilho e várias ações. O gatilho ou a ação que você usa depende do seu cenário.

Se seu fluxo de trabalho:

* Dispara quando um evento ocorre no blockchain, [use o gatilho de evento](#use-the-event-trigger).
* Consultas ou implanta um contrato inteligente, [use ações](#use-actions).
* Segue um cenário comum, [gere um fluxo de trabalho usando o kit do desenvolvedor](#generate-a-workflow).

## <a name="use-the-event-trigger"></a>Usar o gatilho de evento

Use disparadores de eventos Ethereum Blockchain quando desejar que um aplicativo lógico seja executado depois que um evento de contrato inteligente ocorrer. Por exemplo, você deseja enviar um email quando uma função de contrato inteligente é chamada.

1. No designer de aplicativos lógicos, selecione o conector Ethereum Blockchain.
1. Na guia **gatilhos** , selecione **quando ocorrer um evento de contrato inteligente**.
1. Altere ou [crie uma conexão de API com o](#create-an-api-connection) serviço Blockchain do Azure.
1. Insira os detalhes sobre o contrato inteligente que você deseja verificar em busca de eventos.

    ![Designer de aplicativos lógicos com propriedades de gatilho de evento](./media/ethereum-logic-app/event-properties.png)

    | Propriedade | Descrição |
    |----------|-------------|
    | **ABI do contrato** | A ABI (interface binária de aplicativos de contrato) define as interfaces de contrato inteligente. Para obter mais informações, consulte [obter a Abi do contrato](#get-the-contract-abi). |
    | **Endereço do contrato inteligente** | O endereço do contrato é o endereço de destino do contrato inteligente no blockchain Ethereum. Para obter mais informações, consulte [obter o endereço do contrato](#get-the-contract-address). |
    | **Nome do evento** | Selecione um evento de contrato inteligente para verificar. O evento dispara o aplicativo lógico. |
    | **Intervalo** e **frequência** | Selecione com que frequência você deseja verificar o evento. |

1. Selecione **Guardar**.

Para concluir seu aplicativo lógico, você pode adicionar uma nova etapa que executa uma ação com base no gatilho de evento Ethereum Blockchain. Por exemplo, envie um email.

## <a name="use-actions"></a>Ações de uso

Use as ações Ethereum Blockchain quando desejar que um aplicativo lógico execute uma ação na contabilidade Blockchain. Por exemplo, você deseja criar um microserviço baseado em REST que chama uma função de contrato inteligente quando uma solicitação HTTP é feita a um aplicativo lógico.

As ações do conector exigem um gatilho. Você pode usar uma ação de conector Ethereum Blockchain como a próxima etapa após um gatilho, como um gatilho de solicitação HTTP para um microserviço.

1. No designer de aplicativos lógicos, selecione **nova etapa** após um gatilho.
1. Selecione o conector Ethereum Blockchain.
1. Na guia **ações** , selecione uma das ações disponíveis.

    ![Designer de aplicativos lógicos com propriedades de ações](./media/ethereum-logic-app/action-properties.png)

1. Altere ou [crie uma conexão de API com o](#create-an-api-connection) serviço Blockchain do Azure.
1. Dependendo da ação escolhida, forneça os seguintes detalhes sobre a função de contrato inteligente.

    | Propriedade | Descrição |
    |----------|-------------|
    | **ABI do contrato** | A ABI do contrato define as interfaces do contrato inteligente. Para obter mais informações, consulte [obter a Abi do contrato](#get-the-contract-abi). |
    | **Código de bytes do contrato** | O código de bytes do contrato inteligente compilado. Para obter mais informações, consulte [obter o código de bytes do contrato](#get-the-contract-bytecode). |
    | **Endereço do contrato inteligente** | O endereço do contrato é o endereço de destino do contrato inteligente no blockchain Ethereum. Para obter mais informações, consulte [obter o endereço do contrato](#get-the-contract-address). |
    | **Nome da função de contrato inteligente** | Selecione o nome da função de contrato inteligente para a ação. A lista é preenchida com os detalhes na ABI do contrato. |

    Depois de selecionar um nome de função de contrato inteligente, você poderá ver campos obrigatórios para parâmetros de função. Insira os valores ou o conteúdo dinâmico necessário para seu cenário.

Agora você pode usar seu aplicativo lógico. Quando o evento do aplicativo lógico é disparado, a ação Ethereum Blockchain é executada. Por exemplo, um gatilho de solicitação HTTP executa uma ação Ethereum blockchain para consultar um valor de estado do contrato inteligente. Essa consulta resulta em uma resposta HTTP que retorna o valor.

## <a name="generate-a-workflow"></a>Gerar um fluxo de trabalho

A extensão do kit de desenvolvimento Blockchain do Azure para Ethereum Visual Studio Code pode gerar fluxos de trabalho de aplicativo lógico para cenários comuns. Quatro cenários estão disponíveis:

* Publicação de dados em uma instância do banco de dado SQL do Azure
* Publicação de eventos em uma instância da grade de eventos do Azure ou barramento de serviço do Azure
* Publicação de relatório
* Microserviço baseado em REST

 O kit de desenvolvimento do Azure Blockchain usa o Truffle para simplificar o desenvolvimento de Blockchain. Para gerar um aplicativo lógico com base em um contrato inteligente, você precisa de uma solução Truffle para o contrato inteligente. Você também precisa de uma conexão com sua rede do Azure Blockchain Service Consortium. Para obter mais informações, consulte [usar Visual Studio Code para se conectar a um início rápido de rede do Azure Blockchain Service Consortium](connect-vscode.md).

Por exemplo, as etapas a seguir geram um aplicativo lógico de microserviço baseado em REST com base no contrato inteligente **HelloBlockchain** de início rápido:

1. Na barra lateral do Visual Studio Code Explorer, expanda a pasta **contratos** em sua solução.
1. Clique com o botão direito do mouse em **HelloBlockchain. sol** e selecione **gerar microserviços para contratos inteligentes** no menu.

    ![Painel de Visual Studio Code com a seleção gerar microserviços para contratos inteligentes](./media/ethereum-logic-app/generate-logic-app.png)

1. Na paleta de comandos, selecione **aplicativo lógico**.
1. Insira o **endereço do contrato**. Para obter mais informações, consulte [obter o endereço do contrato](#get-the-contract-address).
1. Selecione a assinatura do Azure e o grupo de recursos para o aplicativo lógico.

    Os arquivos de código e de configuração do aplicativo lógico são gerados no diretório **generatedLogicApp**

1. Exiba o diretório **generatedLogicApp/HelloBlockchain** . Há um arquivo JSON de aplicativo lógico para cada função, evento e propriedade de contrato inteligente.
1. Abra a **Propriedade generatedLogicApp/HelloBlockchain/Service/. Arquivo RequestMessage. logicapp. JSON** e copie o conteúdo.

    ![Arquivo JSON com código a ser copiado](./media/ethereum-logic-app/requestmessage.png)

1. Em seu aplicativo lógico, selecione **exibição de código do aplicativo lógico**. Substitua o JSON existente pelo JSON do aplicativo lógico gerado.

    ![Exibição de código do aplicativo lógico com o novo código de aplicativo substituído](./media/ethereum-logic-app/code-view.png)

1. Selecione **Designer** para alternar para o modo de exibição de designer.
1. O aplicativo lógico inclui as etapas básicas para o cenário. No entanto, você precisa atualizar os detalhes de configuração para o conector Ethereum Blockchain.
1. Selecione a etapa **conexões** e altere ou [crie uma conexão de API com o](#create-an-api-connection) serviço Blockchain do Azure.

    ![Exibição de designer com a seleção de conexões](./media/ethereum-logic-app/microservice-logic-app.png)

1. Agora você pode usar seu aplicativo lógico. Para testar o microserviço baseado em REST, emita uma solicitação HTTP POST para a URL de solicitação do aplicativo lógico. Copie o conteúdo da **URL http post** da etapa **quando uma solicitação HTTP é recebida** .

    ![Painel designer de aplicativos lógicos com a URL HTTP POST](./media/ethereum-logic-app/post-url.png)

1. Use a rotação para criar uma solicitação HTTP POST. Substitua o texto do espaço reservado *\<http URL de post @ no__t-2* pela URL da etapa anterior.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    O comando de ondulação retorna uma resposta do aplicativo lógico. Nesse caso, a resposta é a saída da função de contrato inteligente **RequestMessage** .

    ![Saída de código da função de contrato inteligente RequestMessage](./media/ethereum-logic-app/curl.png)

Para obter mais informações sobre como usar o kit de desenvolvimento, consulte a [página do Azure Blockchain Development Kit para Ethereum wiki](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki).

## <a name="create-an-api-connection"></a>Criar uma conexão de API

Uma conexão de API com um blockchain é necessária para o conector Ethereum Blockchain. Você pode usar o conector de API para vários aplicativos lógicos. Algumas propriedades são necessárias e outras dependem de seu cenário.

> [!IMPORTANT]
> Uma chave privada ou um endereço de conta e uma senha são necessários para a criação de transações em um blockchain. Apenas uma forma de autenticação é necessária. Você não precisa fornecer os detalhes da chave privada e da conta. A consulta de contratos não requer uma transação. Se você estiver usando ações que consultam o estado do contrato, a chave privada ou o endereço da conta e a senha não são necessários.

Para ajudá-lo a configurar uma conexão com um membro do serviço Blockchain do Azure, a lista a seguir tem as propriedades possíveis que podem ser necessárias dependendo do seu cenário.

| Propriedade | Descrição |
|----------|-------------|
|**Connection name** (Nome da ligação) | Nome da conexão de API. Necessário. |
|**Ponto de extremidade RPC Ethereum** | Endereço HTTP do nó de transação do serviço Blockchain do Azure. Necessário. Para obter mais informações, consulte [obter o ponto de extremidade RPC](#get-the-rpc-endpoint). |
|**Chave privada** | Chave privada da conta do Ethereum. A chave privada ou o endereço da conta e a senha são necessários para as transações. Para obter mais informações, consulte [obter a chave privada](#get-the-private-key). |
|**Endereço da conta** | Endereço da conta do membro do serviço Blockchain do Azure. A chave privada ou o endereço da conta e a senha são necessários para as transações. Para obter mais informações, consulte [obter o endereço da conta](#get-the-account-address). |
|**Senha da conta** | A senha da conta é definida quando você cria o membro. Para obter informações sobre como redefinir a senha, consulte [conta do Ethereum](consortium.md#ethereum-account).|

## <a name="get-the-rpc-endpoint"></a>Obter o ponto de extremidade RPC

O endereço do ponto de extremidade RPC do serviço Blockchain do Azure é necessário para se conectar a uma rede Blockchain. Você pode obter o endereço do ponto de extremidade usando o kit de desenvolvimento Blockchain do Azure para Ethereum ou o portal do Azure.

**Para usar o kit de desenvolvimento:**

1. Em **serviço Blockchain do Azure** no Visual Studio Code, clique com o botão direito do mouse no consórcio.
1. Selecione **copiar endereço do ponto de extremidade RPC**.

    ![Painel de Visual Studio Code mostrando o consórcio com a seleção copiar endereço de ponto de extremidade RPC](./media/ethereum-logic-app/devkit-rpc.png)

    O ponto de extremidade RPC é copiado para a área de transferência.

**Para usar o portal do Azure:**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Vá para o membro do serviço Blockchain do Azure. Selecione **nós de transação** e o link do nó de transação padrão.

    ![Página nós de transação com a seleção (nó padrão)](./media/ethereum-logic-app/transaction-nodes.png)

1. Selecione **cadeias de conexão** > **chaves de acesso**.
1. Copie o endereço do ponto de extremidade do **https (chave de acesso 1)** ou **https (chave de acesso 2)** .

    ![portal do Azure com as chaves de acesso da cadeia de conexão](./media/ethereum-logic-app/connection-string.png)

    O ponto de extremidade RPC é a URL HTTPS, que inclui o endereço e a chave de acesso do seu nó de transação de membro do serviço Blockchain do Azure.

## <a name="get-the-private-key"></a>Obter a chave privada

Você pode usar a chave privada da conta do Ethereum para autenticar ao enviar uma transação para o blockchain. As chaves públicas e privadas da sua conta do Ethereum são geradas de um mnemônico de 12 palavras. O kit de desenvolvimento do Azure Blockchain para Ethereum gera um mnemônico quando você se conecta a um membro do Azure Blockchain Service Consortium. Você pode obter o endereço do ponto de extremidade usando a extensão do kit de desenvolvimento.

1. Em Visual Studio Code, abra a paleta de comandos (F1).
1. Selecione **Azure Blockchain: recuperar chave privada**.
1. Selecione o mnemônico que você salvou ao se conectar ao membro do consórcio.

    ![Paleta de comandos com uma opção para selecionar o mnemônico](./media/ethereum-logic-app/private-key.png)

    A chave privada é copiada para a área de transferência.

## <a name="get-the-account-address"></a>Obter o endereço da conta

Você pode usar a conta de membro e a senha para autenticar ao enviar uma transação para o blockchain. A senha é definida quando você cria o membro.

1. Na portal do Azure, vá para a página Visão geral do serviço Blockchain do Azure.
1. Copie o endereço da **conta do membro** .

    ![Página de visão geral com o endereço de conta do membro](./media/ethereum-logic-app/member-account.png)

Para obter mais informações sobre o endereço da conta e a senha, consulte [conta do Ethereum](consortium.md#ethereum-account).

## <a name="get-the-contract-abi"></a>Obtenha a ABI do contrato

A ABI do contrato define as interfaces do contrato inteligente. Ele descreve como interagir com o contrato inteligente. Você pode obter a ABI do contrato usando o kit de desenvolvimento Blockchain do Azure para Ethereum. Você também pode obtê-lo do arquivo de metadados do contrato no compilador de solidez.

**Para usar o kit de desenvolvimento:**

Se você usou o kit de desenvolvimento ou o Truffle para criar seu contrato inteligente, poderá usar a extensão para copiar a ABI do contrato para a área de transferência.

1. No painel Visual Studio Code Explorer, expanda a pasta **Build/Contracts** do projeto de sólidaidade.
1. Clique com o botão direito do mouse no arquivo JSON de metadados do contrato. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **. JSON** .
1. Selecione **copiar o Abi do contrato**.

    ![Painel de Visual Studio Code com a seleção de ABI do contrato de cópia](./media/ethereum-logic-app/abi-devkit.png)

    A ABI do contrato é copiada para a área de transferência.

**Para usar o arquivo de metadados do contrato:**

1. Abra o arquivo de metadados de contrato contido na pasta **Compilar/contratos** de seu projeto de solidez. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **. JSON** .
1. Localize a seção **Abi** no arquivo JSON.
1. Copie a matriz do **Abi** JSON.

    ![Código da ABI no arquivo de metadados do contrato](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-the-contract-bytecode"></a>Obter o código de bytes do contrato

O código de bytes do contrato é o contrato inteligente compilado executado pela máquina virtual Ethereum. Você pode obter o código de bytes do contrato usando o kit de desenvolvimento Blockchain do Azure para Ethereum. Você também pode obtê-lo do compilador de solidez.

**Para usar o kit de desenvolvimento:**

Se você usou o kit de desenvolvimento ou o Truffle para criar seu contrato inteligente, poderá usar a extensão para copiar o código de bytes do contrato para a área de transferência.

1. No painel Visual Studio Code Explorer, expanda a pasta **Build/Contracts** do projeto de sólidaidade.
1. Clique com o botão direito do mouse no arquivo JSON de metadados do contrato. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **. JSON** .
1. Selecione **copiar código de bytes do contrato**.

    ![Painel de Visual Studio Code com a seleção copiar código de bytes de contrato](./media/ethereum-logic-app/bytecode-devkit.png)

    O código de bytes do contrato é copiado para a área de transferência.

**Para usar o arquivo de metadados do contrato:**

1. Abra o arquivo de metadados de contrato contido na pasta **Compilar/contratos** de seu projeto de solidez. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **. JSON** .
1. Localize o elemento de **código de bytes** no arquivo JSON.
1. Copie o valor do **código de bytes** .

    ![Painel de Visual Studio Code com código de bytes nos metadados](./media/ethereum-logic-app/bytecode-metadata.png)

**Para usar o compilador de solidez:**

Use o comando `solc --bin <smart contract>.sol` para gerar o código de bytes do contrato.

## <a name="get-the-contract-address"></a>Obter o endereço do contrato

O endereço do contrato é o endereço de destino do contrato inteligente no blockchain Ethereum. Você usa esse endereço para enviar uma transação ou estado de consulta de um contrato inteligente. Você pode obter o endereço do contrato da saída de migração do Truffle ou do arquivo de metadados do contrato.

**Para usar a saída de migração do Truffle:**

Truffle exibe o endereço do contrato após a implantação do contrato inteligente. Copie o **endereço do contrato** da saída.

![Saída de migração do Truffle com o endereço do contrato no Visual Studio Code](./media/ethereum-logic-app/contract-address-truffle.png)

**Para usar o arquivo de metadados do contrato:**

1. Abra o arquivo de metadados de contrato contido na pasta **Compilar/contratos** de seu projeto de solidez. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **. JSON** .
1. Localize a seção **redes** no arquivo JSON.
1. Redes privadas são identificadas por uma ID de rede de número inteiro. Localize o valor do endereço na seção rede.
1. Copie o valor do **endereço** .

![Metadados com o valor de endereço em Visual Studio Code](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>Passos seguintes

Assista a cenários comuns no vídeo [fazendo mais com aplicativos lógicos](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true).
