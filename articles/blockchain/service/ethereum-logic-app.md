---
title: Use o conector Ethereum Blockchain com aplicações lógicas azure - Serviço Azure Blockchain
description: Utilize o conector Ethereum Blockchain com aplicações da Azure Logic para desencadear funções inteligentes de contrato e responder a eventos de contrato inteligentes.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: chrisseg
ms.openlocfilehash: 4a9acfd6098ed45fd92c7e3047b5d1446eeddbd6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74325227"
---
# <a name="use-the-ethereum-blockchain-connector-with-azure-logic-apps"></a>Utilize o conector Ethereum Blockchain com aplicações lógicas azure

Utilize o [conector Ethereum Blockchain](https://docs.microsoft.com/connectors/blockchainethereum/) com [apps azure logic](https://docs.microsoft.com/azure/logic-apps/) para realizar ações de contrato inteligente e responder a eventos de contrato inteligentes. Por exemplo, digamos que quer criar um microserviço baseado em REST que devolve informações a partir de um livro-razão blockchain. Ao utilizar uma aplicação lógica, pode aceitar pedidos HTTP que consultam informações armazenadas num livro-razão blockchain.

## <a name="prerequisites"></a>Pré-requisitos

Complete o pré-requisito opcional [Quickstart: Use visual studio code para ligar a uma rede de consórcio seleções Azure Blockchain](connect-vscode.md)Service . O quickstart guia-o apesar de instalar o Kit de Desenvolvimento azure blockchain para o [Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) e configurar o seu ambiente de desenvolvimento blockchain.

## <a name="create-a-logic-app"></a>Criar uma aplicação lógica

As Aplicações Lógicas Azure ajudam-no a agendar e automatizar processos de negócio e fluxos de trabalho quando precisa de integrar sistemas e serviços. Primeiro, cria-se uma lógica que usa o conector Ethereum Blockchain.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Integração** > **Aplicação Lógica**.
1. No âmbito da **aplicação Create logic,** forneça detalhes sobre onde criar a sua aplicação lógica. Depois de terminar, selecione **Criar**.

    Para obter mais informações sobre a criação de apps lógicas, consulte [Criar fluxos de trabalho automatizados com aplicações lógicas azure](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Depois do Azure implementar a sua aplicação, selecione o seu recurso de aplicação lógica.
1. No Designer de Aplicações Lógicas, em **Modelos,** selecione **App lógica em branco**.

Todas as aplicações lógicas têm de iniciar com um acionador, que é desencadeado quando um evento específico acontece ou quando uma condição específica é cumprida. Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica que inicia e executa o fluxo de trabalho.

O conector Ethereum Blockchain tem um gatilho e várias ações. O gatilho ou ação que usas depende do teu cenário.

Se o seu fluxo de trabalho:

* Ativa-se quando ocorre um evento na blockchain, [utilize o gatilho do evento](#use-the-event-trigger).
* Consultas ou implementa um contrato inteligente, [Use ações.](#use-actions)
* Segue um cenário comum, [Gera um fluxo de trabalho utilizando o kit de desenvolvimento](#generate-a-workflow).

## <a name="use-the-event-trigger"></a>Use o gatilho do evento

Use o evento Ethereum Blockchain dispara quando quiser que uma aplicação lógica seja executada depois de ocorrer um evento de contrato inteligente. Por exemplo, você quer enviar um e-mail quando uma função de contrato inteligente é chamada.

1. No Logic Apps Designer, selecione o conector Ethereum Blockchain.
1. A partir do separador **Triggers,** selecione **Quando ocorrer um evento de contrato inteligente**.
1. Altere ou [crie uma ligação API](#create-an-api-connection) ao Serviço Blockchain Azure.
1. Insira os detalhes sobre o contrato inteligente que pretende verificar para os eventos.

    ![Logic Apps Designer com propriedades de gatilho de evento](./media/ethereum-logic-app/event-properties.png)

    | Propriedade | Descrição |
    |----------|-------------|
    | **Contrato ABI** | A interface binária de aplicação contratual (ABI) define as interfaces de contrato inteligentes. Para mais informações, consulte [O contrato ABI](#get-the-contract-abi). |
    | **Endereço de contrato inteligente** | O endereço do contrato é o endereço de destino de contrato inteligente na blockchain Ethereum. Para mais informações, consulte [O endereço do contrato](#get-the-contract-address). |
    | **Nome do evento** | Selecione um evento de contrato inteligente para verificar. O evento despoleta a aplicação lógica. |
    | **Intervalo** e **Frequência** | Selecione quantas vezes pretende verificar o evento. |

1. Selecione **Guardar**.

Para completar a sua aplicação lógica, pode adicionar um novo passo que executa uma ação baseada no gatilho do evento Ethereum Blockchain. Por exemplo, envie um e-mail.

## <a name="use-actions"></a>Utilizar ações

Use as ações do Ethereum Blockchain quando quiser uma aplicação lógica para realizar uma ação no livro de blockchain. Por exemplo, pretende criar um microserviço baseado em REST que chame uma função de contrato inteligente quando um pedido http é feito para uma aplicação lógica.

As ações do conector requerem um gatilho. Pode utilizar uma ação de conector Ethereum Blockchain como o próximo passo após um gatilho, como um gatilho de pedido HTTP para um microserviço.

1. No Logic Apps Designer, selecione **Novo passo** seguindo um gatilho.
1. Selecione o conector Ethereum Blockchain.
1. A partir do separador **Ações,** selecione uma das ações disponíveis.

    ![Logic Apps Designer com propriedades de ações](./media/ethereum-logic-app/action-properties.png)

1. Altere ou [crie uma ligação API](#create-an-api-connection) ao Serviço Blockchain Azure.
1. Dependendo da ação que escolheu, forneça os seguintes detalhes sobre a sua função de contrato inteligente.

    | Propriedade | Descrição |
    |----------|-------------|
    | **Contrato ABI** | O contrato ABI define as interfaces inteligentes do contrato. Para mais informações, consulte [O contrato ABI](#get-the-contract-abi). |
    | **Código de contrato** | O código de contrato inteligente compilado. Para mais informações, consulte [Obter o código de identificação](#get-the-contract-bytecode). |
    | **Endereço de contrato inteligente** | O endereço do contrato é o endereço de destino de contrato inteligente na blockchain Ethereum. Para mais informações, consulte [O endereço do contrato](#get-the-contract-address). |
    | **Nome de função de contrato inteligente** | Selecione o nome da função de contrato inteligente para a ação. A lista é preenchida a partir dos detalhes do contrato ABI. |

    Depois de selecionar um nome de função de contrato inteligente, pode ver os campos necessários para os parâmetros de função. Introduza os valores ou conteúdo dinâmico necessários para o seu cenário.

Agora pode usar a sua aplicação lógica. Quando o evento de aplicações lógicas é desencadeado, a ação ethereum Blockchain funciona. Por exemplo, um gatilho de pedido HTTP executa uma ação blockchain Ethereum para consultar um valor de estado de contrato inteligente. Esta consulta resulta numa resposta HTTP que devolve o valor.

## <a name="generate-a-workflow"></a>Gerar um fluxo de trabalho

O Kit de Desenvolvimento azure blockchain para extensão do Código do Estúdio Visual Ethereum pode gerar fluxos de trabalho de aplicações lógicas para cenários comuns. Estão disponíveis quatro cenários:

* Publicação de dados numa instância de Base de Dados Azure SQL
* Publicação de eventos em uma instância de Azure Event Grid ou Azure Service Bus
* Publicação de relatórios
* Microserviço baseado em REPOUSO

 O Kit de Desenvolvimento azure blockchain usa a Truffle para simplificar o desenvolvimento da blockchain. Para gerar uma aplicação lógica baseada num contrato inteligente, precisa de uma solução Truffle para o contrato inteligente. Também precisa de uma ligação com a sua rede de consórcio seletiva Azure Blockchain Service. Para mais informações, consulte [o Use Visual Studio Code para se ligar a uma rede de consórcio seleções do Azure Blockchain Service.](connect-vscode.md)

Por exemplo, os seguintes passos geram uma aplicação lógica de microserviço baseada em REST baseada no contrato inteligente quickstart **HelloBlockchain:**

1. Na barra lateral do explorador do Visual Studio Code, expanda a pasta de **contratos** na sua solução.
1. Clique no **HelloBlockchain.sol** e selecione **Generate Microservices for Smart Contracts** a partir do menu.

    ![Painel de Código de Estúdio Visual com a Geração de Microserviços para a seleção de Contratos Inteligentes](./media/ethereum-logic-app/generate-logic-app.png)

1. Na paleta de comando, selecione **Logic App**.
1. Insira o endereço do **contrato**. Para mais informações, consulte [O endereço do contrato](#get-the-contract-address).
1. Selecione o grupo de subscrição e recursos Azure para a aplicação lógica.

    A configuração lógica da aplicação e os ficheiros de código são gerados no diretório **gerado LogicApp.**

1. Ver o diretório **generatedLogicApp/HelloBlockchain.** Há um ficheiro JSON de aplicação lógica para cada função de contrato inteligente, evento e propriedade.
1. Abra o **GeneratedLogicApp/HelloBlockchain/Service/property. RequestMessage.logicapp.json** ficheiro e copie o conteúdo.

    ![Ficheiro JSON com código para copiar](./media/ethereum-logic-app/requestmessage.png)

1. Na sua aplicação lógica, selecione **Logic app code view**. Substitua o JSON existente pela aplicação lógica gerada JSON.

    ![Visão de código de aplicativo lógica com novo código de aplicação substituído](./media/ethereum-logic-app/code-view.png)

1. Selecione **Designer** para mudar para a vista do designer.
1. A aplicação lógica inclui os passos básicos para o cenário. No entanto, é necessário atualizar os detalhes de configuração do conector Ethereum Blockchain.
1. Selecione o passo de **Ligações** e altere ou [crie uma ligação API](#create-an-api-connection) ao Serviço Blockchain Azure.

    ![Vista do designer com a seleção de Ligações](./media/ethereum-logic-app/microservice-logic-app.png)

1. Agora pode usar a sua aplicação lógica. Para testar o microserviço baseado em REST, emita um pedido HTTP POST para o URL de pedido de aplicação lógica. Copie o conteúdo do **URL HTTP POST** do passo quando um pedido HTTP é **recebido.**

    ![Logic Apps Designer painel e com o URL HTTP POST](./media/ethereum-logic-app/post-url.png)

1. Utilize cURL para criar um pedido HTTP POST. Substitua o * \<\> * texto do espaço reservado HTTP POST URL pelo URL do passo anterior.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    O comando cURL devolve uma resposta da aplicação lógica. Neste caso, a resposta é a saída da função de contrato inteligente **RequestMessage.**

    ![Saída de código da função de contrato inteligente RequestMessage](./media/ethereum-logic-app/curl.png)

Para obter mais informações sobre a utilização do kit de desenvolvimento, consulte o Kit de Desenvolvimento azure blockchain para a [página wiki da Ethereum](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki).

## <a name="create-an-api-connection"></a>Criar uma ligação API

É necessária uma ligação API a uma blockchain para o conector Ethereum Blockchain. Pode utilizar o conector API para aplicações lógicas múltiplas. Algumas propriedades são necessárias e outras dependem do seu cenário.

> [!IMPORTANT]
> Uma chave privada ou endereço de conta e senha são necessárias para criar transações em um blockchain. Só é necessária uma forma de autenticação. Não precisa fornecer a chave privada e os detalhes da conta. A consulta de contratos não requer uma transação. Se estiver a utilizar ações que se obriguem a declarar, não são necessárias as chaves privadas ou o endereço de conta e a palavra-passe.

Para ajudá-lo a configurar uma ligação a um membro do Serviço Azure Blockchain, a seguinte lista tem possíveis propriedades que poderá necessitar dependendo do seu cenário.

| Propriedade | Descrição |
|----------|-------------|
|**Nome da ligação** | Nome da ligação API. Necessário. |
|**Ponto final do Ethereum RPC** | Endereço HTTP do nó de transações do Serviço Azure Blockchain. Necessário. Para mais informações, consulte [o ponto final do RPC](#get-the-rpc-endpoint). |
|**Chave privada** | Chave privada da conta Ethereum. A chave privada ou o endereço de conta e a palavra-passe são necessários para transações. Para mais informações, consulte [A chave privada](#get-the-private-key). |
|**Endereço de conta** | Endereço de conta membro do Serviço Azure Blockchain. A chave privada ou o endereço de conta e a palavra-passe são necessários para transações. Para mais informações, consulte [O endereço da conta](#get-the-account-address). |
|**Senha de conta** | A palavra-passe da conta é definida quando cria o membro. Para obter informações sobre a reposição da palavra-passe, consulte a [conta Ethereum](consortium.md#ethereum-account).|

## <a name="get-the-rpc-endpoint"></a>Obtenha o ponto final do RPC

O endereço final do Serviço Azure Blockchain RPC é necessário para ligar a uma rede blockchain. Pode obter o endereço final utilizando o Kit de Desenvolvimento azure blockchain para o Ethereum ou o portal Azure.

**Para utilizar o kit de desenvolvimento:**

1. Sob o **Serviço Azure Blockchain** em Código de Estúdio Visual, clique no consórcio.
1. Selecione **Copy RPC Endpoint Address**.

    ![Painel de código de estúdio visual mostrando o consórcio com a seleção copy RPC Endpoint Address](./media/ethereum-logic-app/devkit-rpc.png)

    O ponto final do RPC é copiado para a sua prancheta.

**Para utilizar o portal Azure:**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Vá ao seu membro do Serviço Azure Blockchain. Selecione **os nódosos de transação** e o link de nó de transação predefinido.

    ![Página de nós de transação com a seleção (nó padrão)](./media/ethereum-logic-app/transaction-nodes.png)

1. Selecione **As teclas** > de**acesso**.
1. Copie o endereço de ponto final de **HTTPS (Tecla** de acesso 1) ou HTTPS (Tecla de **acesso 2)**.

    ![Portal Azure com as teclas de acesso de cordas de ligação](./media/ethereum-logic-app/connection-string.png)

    O ponto final do RPC é o URL HTTPS, que inclui o endereço e a chave de acesso do seu nó de transações do Membro do Serviço Blockchain Azure.

## <a name="get-the-private-key"></a>Pegue a chave privada

Pode utilizar a chave privada da conta Ethereum para autenticar ao enviar uma transação para a blockchain. As chaves públicas e privadas da sua conta Ethereum são geradas a partir de um mnemónico de 12 palavras. O Kit de Desenvolvimento Azure Blockchain para o Ethereum gera uma mnemónica quando se conecta a um membro do consórcio Azure Blockchain Service. Pode obter o endereço final utilizando a extensão do kit de desenvolvimento.

1. No Código do Estúdio Visual, abra a paleta de comando (F1).
1. Selecione **Azure Blockchain: Recupere a chave privada**.
1. Selecione o mnemónico que guardou ao ligar-se ao membro do consórcio.

    ![Paleta de comando com opção de selecionar o mnemônico](./media/ethereum-logic-app/private-key.png)

    A chave privada é copiada para a sua prancheta.

## <a name="get-the-account-address"></a>Obtenha o endereço da conta

Pode utilizar a conta membro e a palavra-passe para autenticar quando envia uma transação para a blockchain. A palavra-passe é definida quando cria o membro.

1. No portal Azure, vá à sua página geral do Serviço Blockchain Azure.
1. Copiar o endereço da **conta do membro.**

    ![Página de resumo com o endereço da conta do membro](./media/ethereum-logic-app/member-account.png)

Para obter mais informações sobre o endereço da conta e a palavra-passe, consulte a [conta Ethereum](consortium.md#ethereum-account).

## <a name="get-the-contract-abi"></a>Obtenha o contrato ABI

O contrato ABI define as interfaces inteligentes do contrato. Descreve como interagir com o contrato inteligente. Você pode obter o contrato ABI usando o Kit de Desenvolvimento Azure Blockchain para ethereum. Também pode obtê-lo a partir do ficheiro de metadados do contrato criado pelo compilador solidity.

**Para utilizar o kit de desenvolvimento:**

Se usou o kit de desenvolvimento ou a Truffle para construir o seu contrato inteligente, pode usar a extensão para copiar o contrato ABI para a área de receita.

1. No painel de explorador esguia do Código do Estúdio Visual, expanda a pasta **build/contracts** do seu projeto Solidity.
1. Clique no ficheiro JSON dos metadados do contrato. O nome do ficheiro é o nome do contrato inteligente seguido da extensão **.json.**
1. Selecione **Copy Contract ABI**.

    ![Painel de Código de Estúdio Visual com a seleção ABI do contrato de cópia](./media/ethereum-logic-app/abi-devkit.png)

    O contrato ABI é copiado para a prancheta.

**Para utilizar o ficheiro de metadados do contrato:**

1. Abra o ficheiro de metadados do contrato contido na pasta **build/contract** do seu projeto Solidity. O nome do ficheiro é o nome do contrato inteligente seguido da extensão **.json.**
1. Encontre a secção **abi** no ficheiro JSON.
1. Copie a matriz **abi** JSON.

    ![Código ABI no ficheiro de metadados do contrato](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-the-contract-bytecode"></a>Obtenha o código de contrato

O código de contrato é o contrato inteligente compilado executado pela máquina virtual Ethereum. Você pode obter o código de contrato usando o Kit de Desenvolvimento Azure Blockchain para ethereum. Também pode obtê-lo do compilador solidity.

**Para utilizar o kit de desenvolvimento:**

Se usou o kit de desenvolvimento ou a Truffle para construir o seu contrato inteligente, pode utilizar a extensão para copiar o código de encaixe do contrato para a pasta.

1. No painel de explorador esguia do Código do Estúdio Visual, expanda a pasta **build/contracts** do seu projeto Solidity.
1. Clique no ficheiro JSON dos metadados do contrato. O nome do ficheiro é o nome do contrato inteligente seguido da extensão **.json.**
1. Selecione **Copy Contract Bytecode**.

    ![Painel de código de estúdio visual com a seleção de Código de Contrato de Cópia](./media/ethereum-logic-app/bytecode-devkit.png)

    O código de identificação do contrato é copiado para a pasta.

**Para utilizar o ficheiro de metadados do contrato:**

1. Abra o ficheiro de metadados do contrato contido na pasta **build/contract** do seu projeto Solidity. O nome do ficheiro é o nome do contrato inteligente seguido da extensão **.json.**
1. Encontre o elemento **bytecode** no ficheiro JSON.
1. Copie o valor do **código de byte.**

    ![Painel de código de estúdio visual com bytecode nos metadados](./media/ethereum-logic-app/bytecode-metadata.png)

**Para utilizar o compilador solidez:**

Use o `solc --bin <smart contract>.sol` comando para gerar o código de código do contrato.

## <a name="get-the-contract-address"></a>Obtenha o endereço do contrato

O endereço do contrato é o endereço de destino de contrato inteligente na blockchain Ethereum. Usa este endereço para enviar uma transação ou um estado de consulta de um contrato inteligente. Pode obter o endereço do contrato a partir da saída de migração truffle ou do ficheiro de metadados do contrato.

**Para utilizar a saída de migração truffle:**

A Trufa exibe o endereço do contrato após a implementação do contrato inteligente. Copie o endereço do **contrato** da saída.

![Produção de migração de trufas com o endereço do contrato no Código do Estúdio Visual](./media/ethereum-logic-app/contract-address-truffle.png)

**Para utilizar o ficheiro de metadados do contrato:**

1. Abra o ficheiro de metadados do contrato contido na pasta **build/contract** do seu projeto Solidity. O nome do ficheiro é o nome do contrato inteligente seguido da extensão **.json.**
1. Encontre a secção **de redes** no ficheiro JSON.
1. As redes privadas são identificadas por um ID de rede inteiro. Encontre o valor de endereço dentro da secção de rede.
1. Copie o valor do **endereço.**

![Metadados com o valor de endereço no Código do Estúdio Visual](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>Passos seguintes

Assista a cenários comuns no vídeo [Fazer mais com Apps Lógicas](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true).
