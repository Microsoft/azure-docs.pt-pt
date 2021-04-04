---
title: Ligue o MetaMask a uma rede de serviços Azure Blockchain
description: Ligue-se a uma rede de serviços Azure Blockchain usando o MetaMask e implemente um contrato inteligente.
ms.date: 09/12/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 4a45e02a861ff20a4dc774668a4e008f9b42aeea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530442"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Quickstart: Use o MetaMask para ligar e implementar um contrato inteligente

Neste arranque rápido, você usará MetaMask para ligar a uma rede de Serviço Azure Blockchain e usar remix para implementar um contrato inteligente. Metamask é uma extensão de navegador para gerir uma carteira Éter e realizar ações de contrato inteligentes.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Quickstart Completo: Criar um membro blockchain utilizando o portal Azure](create-member.md) ou [Quickstart: Criar um membro blockchain do Azure Blockchain Service utilizando o Azure CLI](create-member-cli.md)
* Instale [a extensão do navegador MetaMask](https://metamask.io)
* Gerar uma [carteira](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time) MetaMask

## <a name="get-endpoint-address"></a>Obter endereço de ponto final

Precisa do endereço de ponto final do Serviço Azure Blockchain para se ligar à rede blockchain. O endereço de ponta e as teclas de acesso estão no portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para o seu membro do Serviço Azure Blockchain.
1. Selecione **os nós de transação** e o link de nó de transação padrão.

    ![Selecione o nó de transação padrão](./media/connect-metamask/transaction-nodes.png)

1. Selecione **as cordas de ligação > teclas de acesso**.
1. Copie o endereço de ponto final a partir de **HTTPS (Tecla de acesso 1)**.

    ![Cadeia de ligação](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>Ligar MetaMask

1. Abra a extensão do navegador MetaMask e inscreva-se.
1. No dropdown da rede, selecione  **O RPC personalizado**.

    ![RPC personalizado](./media/connect-metamask/custom-rpc.png)

1. Em **New Network > NOVO URL RPC,** cole o endereço de ponto final que copiou acima.
1. Selecione **Guardar**.

    Se a ligação tiver sido bem sucedida, a rede privada aparece na rede de lançamento.

    ![Nova rede](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Implementar contrato inteligente

Remix é um ambiente de desenvolvimento de solidez baseado no navegador. Usando o MetaMask e o Remix juntos, pode implementar e tomar ações em contratos inteligentes.

1. No browser, navegue até `https://remix.ethereum.org`.
1. Selecione **novo ficheiro** no separador **'Casa'** **em 'Ficheiro'.**

    Diga o nome do novo `simple.sol` ficheiro.

    ![Criar ficheiro](./media/connect-metamask/create-file.png)

    Selecione **OK**.
1. No editor do Remix, cole no seguinte **código de contrato inteligente.**

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

    O **contrato simples** declara uma variável do Estado nomeada **equilíbrio.** Há duas funções definidas. A função **de adicionar** adiciona um número ao **equilíbrio**. A função **get** devolve o valor do **saldo.**
1. Para compilar o contrato, selecione primeiro o painel de compilador solidity e, em seguida, selecione o  **Compile simple.sol**.

    ![A captura do ecrã mostra um contrato a ser compilado.](./media/connect-metamask/compile.png)

1. Selecione o **painel de execução & executar** e, em seguida, definir o **Ambiente** para **Web3 injetado** para ligar através do MetaMask ao seu membro blockchain.

    ![Executar separador](./media/connect-metamask/injected-web3.png)

1. Selecione o contrato **simples** e, em seguida, **implemente**.

    ![A captura de ecrã mostra implementar e executar transações com um contrato selecionado e implementar selecionado.](./media/connect-metamask/deploy.png)


1. Uma notificação MetaMask alerta-o de fundos insuficientes para realizar a transação.

    Para uma rede pública de blockchain, precisaria do Éter para pagar o custo da transação. Uma vez que esta é uma rede privada num consórcio, você pode definir o preço do gás para zero.

1.  Selecione **Gas Fee > Editar > Advanced**, definir o preço do **gás** para 0.

    ![Preço do gás](./media/connect-metamask/gas-price.png)

    Selecione **Guardar**.

1. **Selecione Confirmar** para implementar o contrato inteligente na blockchain.
1. Na secção **Contratos Implantados,** expanda o **contrato simples.**

    ![Contrato implantado](./media/connect-metamask/deployed-contract.png)

    Duas ações, **adicionar** e **obter,** mapear para as funções definidas no contrato.

1. Para **efetuar** uma transação de adição na blockchain, introduza um número para adicionar e, em seguida, selecione **adicionar**. Pode receber uma mensagem de falha de estimativa de gás do Remix: "Está a enviar a transação para uma blockchain privada que não necessita de gás." Selecione **Enviar Transações** para forçar a transação.
1. À semelhança do que foi quando implementou o contrato, uma notificação MetaMask alerta-o de fundos insuficientes para realizar a transação.

    Como esta é uma rede privada num consórcio, podemos definir o preço do gás para zero.

1. Selecione **Taxa de Gás > Editar > Advanced**, definir o preço do **gás** para 0, e selecione **Guardar**.
1. **Selecione Confirmar** para enviar a transação para a blockchain.
1. **Selecione obter** ação. Esta é uma chamada para consulta de dados de nó. Uma transação não é necessária.

O painel de depuração do Remix mostra detalhes sobre as transações na blockchain:

![História do depurg](./media/connect-metamask/debug.png)

Você pode ver a **simples** criação de contrato, transação para **simples.add,** e ligar para **simple.get**.

Para ver o histórico de transações no MetaMask, abra a extensão do navegador MetaMask e procure na secção **História** um registo do contrato e transações implementadas.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você usou a extensão do navegador MetaMask para ligar a um nó de transação do Azure Blockchain Service, implementar um contrato inteligente e enviar uma transação para a blockchain. Experimente o próximo tutorial para usar o Azure Blockchain Development Kit para Ethereum e Trufas para criar, construir, implementar e executar uma função de contrato inteligente através de uma transação.

> [!div class="nextstepaction"]
> [Criar, construir e implementar contratos inteligentes no Azure Blockchain Service](send-transaction.md)