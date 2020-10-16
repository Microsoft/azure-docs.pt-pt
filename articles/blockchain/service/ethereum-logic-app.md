---
title: Use o conector Ethereum Blockchain com apps Azure Logic - Azure Blockchain Service
description: Utilize o conector Ethereum Blockchain com Azure Logic Apps para desencadear funções de contrato inteligentes e responder a eventos de contrato inteligentes.
ms.date: 08/31/2020
ms.topic: how-to
ms.reviewer: caleteet
ms.openlocfilehash: 4364d2f616c8eaadedf12baf4bf77810eec69fdb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89230539"
---
# <a name="use-the-ethereum-blockchain-connector-with-azure-logic-apps"></a>Utilize o conector Ethereum Blockchain com apps Azure Logic

Utilize o [conector Ethereum Blockchain](/connectors/blockchainethereum/) com [a Azure Logic Apps](../../logic-apps/index.yml) para realizar ações de contrato inteligentes e responder a eventos de contrato inteligentes. Este artigo explica como pode usar o conector Ethereum Blockchain para enviar informações blockchain para outro serviço ou chamar uma função blockchain. Por exemplo, digamos que pretende criar um microserviço baseado em REST que retorna a informação de um livro de contabilidade blockchain. Ao utilizar uma aplicação lógica, pode aceitar pedidos HTTP que questionem as informações armazenadas num livro-razão blockchain.

## <a name="prerequisites"></a>Pré-requisitos

- Preencha o pré-requisito opcional [Quickstart: Use o Código do Estúdio Visual para ligar a uma rede de consórcios do Azure Blockchain Service](connect-vscode.md). O quickstart guia-o embora tenha instalado [o Azure Blockchain Development Kit para o Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) e configurando o seu ambiente de desenvolvimento blockchain.
- Se é novo em Azure Logic Apps, considere rever os módulos Microsoft Learn [Introdução a Aplicações Lógicas Azure](/learn/modules/intro-to-logic-apps/) e [Ligue uma API a partir de um fluxo de trabalho de Apps Lógicas utilizando um conector personalizado](/learn/modules/logic-apps-and-custom-connectors/).

## <a name="create-a-logic-app"></a>Criar uma aplicação lógica

O Azure Logic Apps ajuda-o a agendar e automatizar processos de negócio e fluxos de trabalho quando precisa de integrar sistemas e serviços. Primeiro, cria-se uma lógica que utiliza o conector Ethereum Blockchain.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Integração** > **Aplicação Lógica**.
1. No **âmbito da aplicação lógica Create,** forneça detalhes sobre onde criar a sua aplicação lógica. Depois de terminar, **selecione Criar**.

    Para obter mais informações sobre a criação de aplicações [lógicas, consulte Criar fluxos de trabalho automatizados com apps Azure Logic.](../../logic-apps/quickstart-create-first-logic-app-workflow.md)

1. Depois de o Azure implementar a sua aplicação, selecione o seu recurso de aplicação lógica.
1. No Design de Aplicações **Lógicas, em Modelos,** selecione **Blank Logic App**.

Todas as aplicações lógicas têm de iniciar com um acionador, que é desencadeado quando um evento específico acontece ou quando uma condição específica é cumprida. Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica que inicia e executa o fluxo de trabalho.

O conector Ethereum Blockchain tem um gatilho e várias ações. O gatilho ou ação que usas depende do teu cenário. Siga a secção deste artigo que melhor corresponda ao seu cenário.

Se o seu fluxo de trabalho:

* Aciona quando ocorre um evento na blockchain, [utilize o gatilho do evento](#use-the-event-trigger).
* Consultas ou implementa um contrato inteligente, [Use ações](#use-actions).
* Segue um cenário comum, [Gere um fluxo de trabalho utilizando o kit de desenvolvedores.](#generate-a-workflow)

## <a name="use-the-event-trigger"></a>Use o gatilho do evento

Use o evento Ethereum Blockchain dispara quando pretende que uma aplicação lógica seja executada após ocorrê-lo um evento de contrato inteligente. Por exemplo, pretende enviar um e-mail quando uma função de contrato inteligente é chamada.

1. No Logic Apps Designer, selecione o conector Ethereum Blockchain.
1. A partir do **separador Gatilhos,** selecione **Quando ocorrer um evento de contrato inteligente**.
1. Alterar ou [criar uma ligação API](#create-an-api-connection) ao Serviço Azure Blockchain.
1. Insira os detalhes sobre o contrato inteligente que pretende verificar para eventos.

    ![Designer de apps lógicas com propriedades de gatilho de eventos](./media/ethereum-logic-app/event-properties.png)

    | Propriedade | Descrição |
    |----------|-------------|
    | **Contrato ABI** | A interface binária de aplicação contratual (ABI) define as interfaces de contrato inteligente. Para mais informações, consulte [obter o contrato ABI.](#get-the-contract-abi) |
    | **Endereço de contrato inteligente** | O endereço do contrato é o endereço de destino de contrato inteligente na blockchain Ethereum. Para mais informações, consulte [obter o endereço do contrato.](#get-the-contract-address) |
    | **Nome do evento** | Selecione um evento de contrato inteligente para verificar. O evento desencadeia a aplicação lógica. |
    | **Intervalo** e **Frequência** | Selecione a frequência com que pretende verificar o evento. |

1. Selecione **Guardar**.

Para completar a sua aplicação lógica, pode adicionar um novo passo que executa uma ação baseada no gatilho do evento Ethereum Blockchain. Por exemplo, envie um e-mail.

## <a name="use-actions"></a>Utilizar ações

Utilize as ações do Ethereum Blockchain quando quiser que uma aplicação lógica realize uma ação no livro de contabilidade blockchain. Por exemplo, pretende criar um microserviço baseado em REST que chame uma função de contrato inteligente quando um pedido HTTP é feito para uma aplicação lógica.

As ações de conector requerem um gatilho. Pode utilizar uma ação de conector Ethereum Blockchain como o próximo passo após um gatilho, como um gatilho de pedido HTTP para um microserviço.

1. No Logic Apps Designer, selecione **Novo passo** seguindo um gatilho.
1. Selecione o conector Ethereum Blockchain.
1. A partir do separador **Ações,** selecione uma das ações disponíveis.

    ![Designer de Aplicativos Lógicos com propriedades de Ações](./media/ethereum-logic-app/action-properties.png)

1. Alterar ou [criar uma ligação API](#create-an-api-connection) ao Serviço Azure Blockchain.
1. Dependendo da ação que escolheu, forneça os seguintes detalhes sobre a sua função de contrato inteligente.

    | Propriedade | Descrição |
    |----------|-------------|
    | **Contrato ABI** | O contrato ABI define as interfaces de contrato inteligente. Para mais informações, consulte [obter o contrato ABI.](#get-the-contract-abi) |
    | **Bytecode contrato** | O código de contrato inteligente compilado. Para mais informações, consulte [obter o código de acesso do contrato.](#get-the-contract-bytecode) |
    | **Endereço de contrato inteligente** | O endereço do contrato é o endereço de destino de contrato inteligente na blockchain Ethereum. Para mais informações, consulte [obter o endereço do contrato.](#get-the-contract-address) |
    | **Nome de função de contrato inteligente** | Selecione o nome da função de contrato inteligente para a ação. A lista é povoada dos detalhes do contrato ABI. |

    Depois de selecionar um nome de função de contrato inteligente, poderá ver os campos necessários para os parâmetros de função. Insira os valores ou conteúdo dinâmico necessário para o seu cenário.

Agora pode usar a sua aplicação lógica. Quando o evento de aplicação lógica é desencadeado, a ação Ethereum Blockchain é executado. Por exemplo, um gatilho de pedido HTTP executa uma ação blockchain Ethereum para consultar um valor estatal de contrato inteligente. Esta consulta resulta numa resposta HTTP que devolve o valor.

## <a name="generate-a-workflow"></a>Gerar um fluxo de trabalho

O Kit de Desenvolvimento de Blockchain Azure para a extensão do Código do Estúdio Visual Ethereum pode gerar fluxos de trabalho de aplicações lógicas para cenários comuns. Quatro cenários estão disponíveis:

* Publicação de dados para uma instância da Base de Dados Azure SQL
* Evento publicando para uma instância de Azure Event Grid ou Azure Service Bus
* Publicação de relatório
* Microserviço baseado em REST

 O Azure Blockchain Development Kit usa Trufas para simplificar o desenvolvimento da blockchain. Para gerar uma aplicação lógica baseada num contrato inteligente, você precisa de uma solução Truffle para o contrato inteligente. Também precisa de uma ligação à sua rede de consórcios Azure Blockchain Service. Para obter mais informações, consulte [o Código do Estúdio Visual para ligar-se a uma rede de consórcios Azure Blockchain Service .](connect-vscode.md)

Por exemplo, os seguintes passos geram uma aplicação lógica de microserviço baseada em REST baseada no contrato inteligente **HelloBlockchain** quickstart:

1. Na barra lateral do explorador do Código do Estúdio Visual, expanda a pasta **de contratos** na sua solução.
1. Clique com o botão direito **HelloBlockchain.sol** e **selecione Gerar Microserviços para Contratos Inteligentes** a partir do menu.

    ![Painel visual studio code com os Microservices Generate para seleção de contratos inteligentes](./media/ethereum-logic-app/generate-logic-app.png)

1. Na paleta de comando, selecione **Logic App**.
1. Introduza o **endereço do contrato.** Para mais informações, consulte [obter o endereço do contrato.](#get-the-contract-address)
1. Selecione a subscrição Azure e o grupo de recursos para a aplicação lógica.

    A configuração lógica de aplicativos e ficheiros de código são gerados no diretório **gerado doLogicApp.**

1. Ver o **diretório gerado daLogicApp/HelloBlockchain.** Há uma aplicação lógica JSON arquivo para cada função de contrato inteligente, evento e propriedade.
1. Abra **oLogicApp/HelloBlockchain/Service/property.RequestMessage.logicapp.jsgerado no** ficheiro e copie o conteúdo.

    ![Ficheiro JSON com código para copiar](./media/ethereum-logic-app/requestmessage.png)

1. Na sua aplicação lógica, selecione **Logic app code view**. Substitua o JSON existente pela aplicação lógica gerada JSON.

    ![Visão lógica do código da aplicação com novo código de aplicação substituído](./media/ethereum-logic-app/code-view.png)

1. Selecione **Designer** para mudar para a vista do designer.
1. A aplicação lógica inclui os passos básicos para o cenário. No entanto, é necessário atualizar os detalhes de configuração do conector Ethereum Blockchain.
1. Selecione o passo **de Conexões** e altere ou [crie uma ligação API](#create-an-api-connection) ao Serviço Azure Blockchain.

    ![Vista de designer com a seleção de Conexões](./media/ethereum-logic-app/microservice-logic-app.png)

1. Agora pode usar a sua aplicação lógica. Para testar o microserviço baseado em REST, emita um pedido HTTP POST para o URL de pedido de aplicação lógica. Copie o conteúdo **do URL HTTP POST** do Quando um pedido HTTP é **recebido** passo.

    ![Painel de designer de aplicações lógicas com o URL HTTP POST](./media/ethereum-logic-app/post-url.png)

1. Utilize o cURL para criar um pedido HTTP POST. Substitua o texto do espaço reservado *\<HTTP POST URL\>* pelo URL do passo anterior.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    O comando cURL devolve uma resposta da aplicação lógica. Neste caso, a resposta é a saída da função de contrato inteligente **RequestMessage.**

    ![Saída de código da função de contrato inteligente RequestMessage](./media/ethereum-logic-app/curl.png)

Para obter mais informações sobre a utilização do kit de desenvolvimento, consulte o [Kit de Desenvolvimento Azure Blockchain para a página wiki do Ethereum](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki).

## <a name="create-an-api-connection"></a>Criar uma ligação API

É necessária uma ligação API a uma blockchain para o conector Ethereum Blockchain. Pode utilizar o conector API para várias aplicações lógicas. Algumas propriedades são necessárias e outras dependem do seu cenário.

> [!IMPORTANT]
> Uma chave privada ou endereço de conta e senha são necessárias para criar transações em uma blockchain. Só é necessária uma forma de autenticação. Não precisa de fornecer tanto a chave privada como os detalhes da conta. Os contratos de consulta não requerem uma transação. Se estiver a utilizar ações que questionem o estado do contrato, não são necessárias as chaves privadas ou o endereço de conta e a palavra-passe.

Para ajudá-lo a configurar uma ligação com um membro do Serviço Azure Blockchain, a lista a seguir tem possíveis propriedades que poderá necessitar dependendo do seu cenário.

| Propriedade | Descrição |
|----------|-------------|
|**Nome da ligação** | Nome da ligação API. Obrigatório. |
|**Ponto final do Ethereum RPC** | Endereço HTTP do nó de transação do Serviço Azure Blockchain. Obrigatório. Para obter mais informações, [consulte o ponto de terminação do RPC.](#get-the-rpc-endpoint) |
|**Chave privada** | Chave privada da conta Ethereum. São necessárias chaves privadas ou endereço de conta e senha para transações. Para mais informações, consulte [a chave privada.](#get-the-private-key) |
|**Endereço da conta** | Endereço da conta do membro do Serviço Azure Blockchain. São necessárias chaves privadas ou endereço de conta e senha para transações. Para mais informações, consulte [obter o endereço da conta.](#get-the-account-address) |
|**Senha de conta** | A palavra-passe da conta é definida quando cria o membro. Para obter informações sobre a reposição da palavra-passe, consulte [a conta Ethereum](consortium.md#ethereum-account).|

## <a name="get-the-rpc-endpoint"></a>Obtenha o ponto final do RPC

O endereço de ponto final do Serviço Azure Blockchain RPC é necessário para se ligar a uma rede blockchain. Pode obter o endereço de ponto final utilizando o Kit de Desenvolvimento Azure Blockchain para ethereum ou o portal Azure.

**Para utilizar o kit de desenvolvimento:**

1. De acordo com **o Serviço Azure Blockchain** em Visual Studio Code, clique com o botão direito no consórcio.
1. Selecione **copy RPC Endpoint Address**.

    ![Painel visual studio code mostrando o consórcio com a seleção copy RPC Endpoint Address](./media/ethereum-logic-app/devkit-rpc.png)

    O ponto final do RPC é copiado para a sua área de transferência.

**Para utilizar o portal Azure:**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Vá ao seu membro do Serviço Azure Blockchain. Selecione **os nós de transação** e o link de nó de transação padrão.

    ![Página de nós de transação com a seleção (nó padrão)](./media/ethereum-logic-app/transaction-nodes.png)

1. **Selecione as teclas**de acesso de ligação  >  **Access keys**.
1. Copie o endereço de ponto final a partir de **HTTPS (Tecla de acesso 1)** ou **HTTPS (tecla de acesso 2)**.

    ![Portal Azure com as teclas de acesso a cadeia de ligação](./media/ethereum-logic-app/connection-string.png)

    O ponto final do RPC é o URL HTTPS, que inclui o endereço e a chave de acesso do seu nó de transação membro do Azure Blockchain Service.

## <a name="get-the-private-key"></a>Pegue a chave privada

Pode utilizar a chave privada da conta Ethereum para autenticar ao enviar uma transação para a blockchain. As chaves públicas e privadas da sua conta Ethereum são geradas a partir de um mnemónico de 12 palavras. O Kit de Desenvolvimento Azure Blockchain para ethereum gera um mnemónico quando se liga a um membro do consórcio do Azure Blockchain Service. Pode obter o endereço do ponto final utilizando a extensão do kit de desenvolvimento.

1. No Código do Estúdio Visual, abra a paleta de comando (F1).
1. Selecione **Azure Blockchain: Recupere a chave privada**.
1. Selecione o mnemónico que guardou ao ligar-se ao membro do consórcio.

    ![Paleta de comando com opção de selecionar o mnemónico](./media/ethereum-logic-app/private-key.png)

    A chave privada é copiada para a sua área de transferência.

## <a name="get-the-account-address"></a>Obtenha o endereço da conta

Pode utilizar a conta e a palavra-passe dos membros para autenticar quando envia uma transação para a blockchain. A palavra-passe é definida quando cria o membro.

1. No portal Azure, aceda à sua página de visão geral do Serviço Azure Blockchain.
1. Copie o endereço da **conta do membro.**

    ![Página geral com o endereço da conta do membro](./media/ethereum-logic-app/member-account.png)

Para obter mais informações sobre o endereço da conta e a palavra-passe, consulte [a conta Ethereum](consortium.md#ethereum-account).

## <a name="get-the-contract-abi"></a>Obtenha o contrato ABI

O contrato ABI define as interfaces de contrato inteligente. Descreve como interagir com o contrato inteligente. Você pode obter o contrato ABI usando o Azure Blockchain Development Kit para Ethereum. Também pode obtê-lo a partir do ficheiro de metadados do contrato criado pelo compilador Solidity.

**Para utilizar o kit de desenvolvimento:**

Se usou o kit de desenvolvimento ou a Truffle para construir o seu contrato inteligente, pode usar a extensão para copiar o contrato ABI para a área de transferência.

1. No painel de exploradores visual Studio Code, expanda a pasta **de construção/contratos** do seu projeto Solidity.
1. Clique com o botão direito no ficheiro JSON de metadados contratuais. O nome do ficheiro é o nome do contrato inteligente seguido da extensão **.json.**
1. Selecione **Copy Contract ABI**.

    ![Painel visual studio code com a seleção de ABI do contrato de cópia](./media/ethereum-logic-app/abi-devkit.png)

    O contrato ABI é copiado para a área de transferência.

**Para utilizar o ficheiro de metadados contratuais:**

1. Abra o ficheiro de metadados contratuais contido na pasta **de construção/contrato do** seu projeto Solidity. O nome do ficheiro é o nome do contrato inteligente seguido da extensão **.json.**
1. Encontre a secção **abi** no ficheiro JSON.
1. Copie a matriz **abi** JSON.

    ![Código ABI no ficheiro de metadados do contrato](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-the-contract-bytecode"></a>Obtenha o contrato por código

O contrato bytecode é o contrato inteligente compilado executado pela máquina virtual Ethereum. Você pode obter o contrato bytecode usando o Azure Blockchain Development Kit para Ethereum. Também pode obtê-lo a partir do compilador solidity.

**Para utilizar o kit de desenvolvimento:**

Se usou o kit de desenvolvimento ou a Truffle para construir o seu contrato inteligente, pode usar a extensão para copiar o código de contrato para a área de transferência.

1. No painel de exploradores visual Studio Code, expanda a pasta **de construção/contratos** do seu projeto Solidity.
1. Clique com o botão direito no ficheiro JSON de metadados contratuais. O nome do ficheiro é o nome do contrato inteligente seguido da extensão **.json.**
1. Selecione **copy contract bytecode**.

    ![Painel visual studio code com a seleção de bytecode do contrato de cópia](./media/ethereum-logic-app/bytecode-devkit.png)

    O código de contrato é copiado para a área de transferência.

**Para utilizar o ficheiro de metadados contratuais:**

1. Abra o ficheiro de metadados contratuais contido na pasta **de construção/contrato do** seu projeto Solidity. O nome do ficheiro é o nome do contrato inteligente seguido da extensão **.json.**
1. Encontre o elemento **bytecode** no ficheiro JSON.
1. Copie o valor **do código bytecode.**

    ![Painel visual studio code com bytecode nos metadados](./media/ethereum-logic-app/bytecode-metadata.png)

**Para utilizar o compilador solidity:**

Utilize o comando `solc --bin <smart contract>.sol` para gerar o código de acesso do contrato.

## <a name="get-the-contract-address"></a>Obtenha o endereço do contrato

O endereço do contrato é o endereço de destino de contrato inteligente na blockchain Ethereum. Utilize este endereço para enviar uma transação ou estado de consulta de um contrato inteligente. Pode obter o endereço do contrato a partir da saída de migração da Truffle ou do ficheiro de metadados do contrato.

**Para utilizar a saída de migração de Trufas:**

A trufa exibe o endereço do contrato após a implementação do contrato inteligente. Copie o endereço do **contrato** a partir da saída.

![Saída de migração de trufas com o endereço do contrato no Código do Estúdio Visual](./media/ethereum-logic-app/contract-address-truffle.png)

**Para utilizar o ficheiro de metadados contratuais:**

1. Abra o ficheiro de metadados contratuais contido na pasta **de construção/contrato do** seu projeto Solidity. O nome do ficheiro é o nome do contrato inteligente seguido da extensão **.json.**
1. Encontre a secção **de redes** no ficheiro JSON.
1. As redes privadas são identificadas por um ID de rede inteiro. Encontre o valor do endereço na secção de rede.
1. Copie o valor do **endereço.**

![Metadados com o valor do endereço no Código do Estúdio Visual](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>Passos seguintes

Veja cenários comuns no vídeo [Fazendo mais com As Aplicações Lógicas](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true).
