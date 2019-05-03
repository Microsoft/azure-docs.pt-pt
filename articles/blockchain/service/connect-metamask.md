---
title: Ligar MetaMask a uma rede de serviço do Azure Blockchain
description: Ligar a uma rede de serviço do Azure Blockchain com MetaMask e implementar um contrato inteligente.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: db029cee6edcd14d29c83964e5bf75aa45077e7e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026901"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Início rápido: Utilizar MetaMask para ligar e implementar um contrato inteligente

Neste início rápido, irá utilizar MetaMask para ligar a uma rede de serviço do Azure Blockchain e utilizar Remix para implementar um contrato inteligente. Metamask é uma extensão de browser para gerir uma carteira de Ether e executar ações de contrato inteligente.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Criar um membro do Azure Blockchain](create-member.md)
* Instalar [MetaMask a extensão do browser](https://metamask.io)
* Gerar um MetaMask [carteira](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time)

## <a name="get-endpoint-address"></a>Obter o endereço de ponto final

Terá do endereço de ponto final de serviço do Azure Blockchain para ligar à rede de blockchain. Pode encontrar o ponto final de chaves de acesso e de endereço no portal do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para o membro de serviço do Azure Blockchain. Selecione **nós de transação** e o link de nó de transação de predefinição.

    ![Selecione o nó de transação de predefinição](./media/connect-metamask/transaction-nodes.png)

1. Selecione **cadeias de ligação > chaves de acesso**.
1. Copie o endereço de ponto final da **HTTPS (chave de acesso 1)**. Precisa do endereço para a próxima seção.

    ![Cadeia de ligação](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>Ligar MetaMask

1. Abra MetaMask a extensão do browser e inicie sessão.
1. Na lista pendente de rede, selecione **personalizado RPC**.

    ![Custom RPC](./media/connect-metamask/custom-rpc.png)

1. Na **nova rede > novo URL de RPC**, introduza o seu endereço de ponto final que copiou na secção anterior.
1. Selecione **Guardar**.

    Se a ligação foi concluída com êxito, a rede privada é apresentada na lista pendente de rede.

    ![Nova rede](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Implementar o contrato inteligente

Remix é um ambiente de desenvolvimento de solidez baseada no browser. Utilizar MetaMask e Remix em conjunto, pode implementar e executar ações contratos inteligentes.

1. No seu browser, navegue até `https://remix.ethereum.org`.
1. Selecione **Executar**. 

    Conjuntos de MetaMask sua **ambiente** para **Injetado Web3** e **conta** à sua rede.

    ![Separador executar](./media/connect-metamask/injected-web3.png)

1. Selecione **criar novo ficheiro**.

    Nomeie o novo arquivo `simple.sol`.

    ![Criar ficheiro](./media/connect-metamask/create-file.png)

    Selecione **OK**.

1. No editor de Remix, cole a seguir **contrato inteligente simples** código.

    ```solidity
    pragma solidity ^0.5.0;
             
    contract simple {
        uint balance;
                 
        constructor() public{
            balance = 0;
        }
                 
        function add(uint _num) public {
            balance += _num;
        }
                 
        function get() public view returns (uint){
            return balance;
        }
    }
    ```

    O **contrato simples** declara uma variável de estado com o nome **Saldo**. Existem duas funções definidas. O **adicione** função adiciona um número para **Saldo**. O **Obtenha** função devolve o valor de **Saldo**.

1. Para compilar o contrato, selecione o **compilar > começar a compilar**. Se tiver êxito, é apresentada uma caixa verde com o nome do contrato.

    ![Compilação](./media/connect-metamask/compile.png)

1. Para executar o contrato, selecione o **executar** separador. Selecione o **simples** , em seguida, de contrato **implementar**.

    ![Custom RPC](./media/connect-metamask/deploy.png)

1. É apresentada uma notificação de MetaMask alertando-dos fundos suficientes para executar a transação.

    Para uma rede pública blockchain, precisaria Ether para pagar os custos de transação. Como se trata de uma rede privada num consórcio, pode definir o preço de gás a zero.

1.  Selecione **taxa de gás > Editar > Avançadas**, defina o **gás preço** como 0.

    ![Preço de gás](./media/connect-metamask/gas-price.png)

    Selecione **Guardar**.

1. Selecione **confirmar** para implementar o contrato inteligente para o blockchain.
1. Na **contratos implementados** secção, expanda o **simples** contrato.

    ![Contrato implementado](./media/connect-metamask/deployed-contract.png)

    Existem duas ações **adicione** e **obter** que mapeiam para as funções definidas no contrato.

1. Para efetuar uma **adicione** transação a desenvolver o blockchain, introduza um número para adicionar em seguida, selecione **adicionar**.
1. Assim como quando implementou o contrato, MetaMask é apresentada uma notificação de alerta de fundos suficientes para executar a transação.

    Como se trata de uma rede privada num consórcio, podemos definir o preço de gás a zero.

1.  Selecione **taxa de gás > Editar > Avançadas**, defina o **gás preço** para 0 e selecione **guardar**.
1. Selecione **confirmar** para executar a transação a desenvolver o blockchain.
1. Selecione **obter** ação. Esta é uma chamada para consultar dados de nó. Não é necessária uma transação.
1. No painel de depuração do Remix, pode ver detalhes sobre as transações a desenvolver o blockchain.

    ![Histórico de depuração](./media/connect-metamask/debug.png)

    Pode ver o **simples** criação, a transação para de contrato **simple.add**e a chamada para **simple.get**.

1. Também pode ver o histórico de transações em MetaMask. Abra a extensão do browser MetaMask.
1. Na **histórico** seção, pode ver um log de contrato implementado e transações.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, utilizou a extensão do browser MetaMask para ligar a um nó de transação de serviço do Azure Blockchain, implementar um contrato inteligente e enviar uma transação para o blockchain. Experimente o tutorial seguinte para implementar e enviar uma transação usando Truffle.

> [!div class="nextstepaction"]
> [Enviar uma transação](send-transaction.md)