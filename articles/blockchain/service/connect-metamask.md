---
title: Ligue o MetaMask a uma rede de serviços Azure Blockchain
description: Ligue-se a uma rede de ServiçoS Azure Blockchain utilizando o MetaMask e implemente um contrato inteligente.
ms.date: 09/12/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: c0bad9efde44ce53f6b0656af3ac4af32ffe051d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79205100"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Quickstart: Use metaMask para ligar e implementar um contrato inteligente

Neste arranque rápido, utilizará o MetaMask para se ligar a uma rede de Serviços Azure Blockchain e utilizar o Remix para implementar um contrato inteligente. Metamask é uma extensão de navegador para gerir uma carteira Ether e realizar ações de contrato inteligentes.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Complete [Quickstart: Criar um membro blockchain usando o portal Azure](create-member.md) ou [Quickstart: Criar um membro blockchain Azure Blockchain Service usando o Azure CLI](create-member-cli.md)
* Instalar extensão de [navegador MetaMask](https://metamask.io)
* Gerar uma [carteira](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time) MetaMask

## <a name="get-endpoint-address"></a>Obter endereço de ponto final

Precisa do endereço final do Azure Blockchain Service para ligar à rede blockchain. O endereço final e as chaves de acesso estão no portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para o seu membro do Serviço Azure Blockchain.
1. Selecione **os nódosos de transação** e o link de nó de transação predefinido.

    ![Selecione o nó de transações por defeito](./media/connect-metamask/transaction-nodes.png)

1. Selecione **strings de ligação > Teclas**de acesso .
1. Copie o endereço final do HTTPS (Tecla de **acesso 1)** .

    ![Cadeia de ligação](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>Ligar metamask

1. Abra a extensão do navegador MetaMask e inscreva-se.
1. No dropdown da rede, selecione **Custom RPC**.

    ![RPC personalizado](./media/connect-metamask/custom-rpc.png)

1. Em **Nova Rede > Novo URL RPC,** cola o endereço final que copiou acima.
1. Selecione **Guardar**.

    Se a ligação for bem sucedida, a rede privada exibe na queda da rede.

    ![Nova rede](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Implementar contrato inteligente

Remix é um ambiente de desenvolvimento solidity baseado no navegador. Utilizando o MetaMask e o Remix juntos, pode implementar e tomar ações em contratos inteligentes.

1. No seu browser, navegue até `https://remix.ethereum.org`.
1. Selecione **Novo ficheiro** no separador **Home** em **File**.

    Diga o nome do novo ficheiro `simple.sol`.

    ![Criar ficheiro](./media/connect-metamask/create-file.png)

    Selecione **OK**.
1. No editor do Remix, cola no seguinte **código de contrato inteligente simples.**

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

    O **contrato simples** declara uma variável estatal denominada **equilíbrio.** Há duas funções definidas. A função **de adição** adiciona um número ao **equilíbrio**. A função **get** devolve o valor do **saldo.**
1. Para compilar o contrato, primeiro selecione o painel de compilador solidity e, em seguida, selecione o **Compile simple.sol**.

    ![Compilação](./media/connect-metamask/compile.png)

1. Selecione o painel **De implantação e execução** e, em **seguida,** coloque o Ambiente no **Web3 injetado** para ligar através do MetaMask ao seu membro blockchain.

    ![Executar o separador](./media/connect-metamask/injected-web3.png)

1. Selecione o contrato **simples** e, em seguida, **implementar**.

    ![Implementar](./media/connect-metamask/deploy.png)


1. Uma notificação metaMask alerta-o de fundos insuficientes para realizar a transação.

    Para uma rede pública de blockchain, precisaria do Éter para pagar o custo da transação. Uma vez que esta é uma rede privada num consórcio, você pode definir o preço do gás para zero.

1.  Selecione **Gas Fee > Edit > Advanced,** desloque o Preço do **Gás** para 0.

    ![Preço do gás](./media/connect-metamask/gas-price.png)

    Selecione **Guardar**.

1. Selecione **Confirmar** para implementar o contrato inteligente para a blockchain.
1. Na secção **contratos de implantação,** expandir o **contrato simples.**

    ![Contrato implementado](./media/connect-metamask/deployed-contract.png)

    Duas ações, **adicionar** e **obter,** mapear as funções definidas no contrato.

1. Para realizar uma transação **adicionais** na blockchain, introduza um número para adicionar e, em seguida, **selecione adicionar**. Pode receber uma mensagem de falha na estimativa de gás do Remix: "Está a enviar a transação para uma blockchain privada que não necessita de gás." Selecione **Enviar Transação** para forçar a transação.
1. À semelhança do que acontece quando implementou o contrato, uma notificação metaMask alerta-o de fundos insuficientes para realizar a transação.

    Uma vez que esta é uma rede privada num consórcio, podemos definir o preço do gás para zero.

1. Selecione **Gas Fee > Edit > Advanced,** desloque o Preço do **Gás** para 0 e selecione **Save**.
1. Selecione **Confirmar** para enviar a transação para a blockchain.
1. Selecione **obter** ação. Esta é uma chamada para consultar dados do nó. Uma transação não é necessária.

O painel de depuração do Remix mostra detalhes sobre as transações na blockchain:

    ![Debug history](./media/connect-metamask/debug.png)

    You can see the **simple** contract creation, transaction for **simple.add**, and call to **simple.get**.

Para ver o histórico de transações no MetaMask, abra a extensão do navegador MetaMask e procure na secção **História** um registo do contrato e transações implementados.

## <a name="next-steps"></a>Passos Seguintes

Neste arranque rápido, usou a extensão do navegador MetaMask para se ligar a um nó de transação do Serviço Blockchain Azure, implementar um contrato inteligente e enviar uma transação para a blockchain. Experimente o próximo tutorial para usar o Kit de Desenvolvimento Azure Blockchain para ethereum e Truffle para criar, construir, implementar e executar uma função de contrato inteligente através de uma transação.

> [!div class="nextstepaction"]
> [Criar, construir e implementar contratos inteligentes no Serviço Azure Blockchain](send-transaction.md)