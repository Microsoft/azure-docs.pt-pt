---
title: Conectar a metamáscara a uma rede do serviço Blockchain do Azure
description: Conecte-se a uma rede do serviço Blockchain do Azure usando metamask e implante um contrato inteligente.
ms.date: 09/12/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 21e45b80a9a08dd6bdeefb0ab01fadabaa08cbce
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456000"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Início rápido: usar a metamáscara para se conectar e implantar um contrato inteligente

Neste guia de início rápido, você usará a metamáscara para se conectar a uma rede do serviço Blockchain do Azure e usará o remix para implantar um contrato inteligente. A metamáscara é uma extensão de navegador para gerenciar uma carteira de ether e executar ações de contrato inteligente.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Conclua [o início rápido: criar um membro do blockchain usando o portal do Azure](create-member.md) ou [início rápido: criar um membro Blockchain do serviço blockchain do Azure usando o CLI do Azure](create-member-cli.md)
* Instalar [extensão do navegador de Metamáscara](https://metamask.io)
* Gerar uma [carteira](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time) de metamáscara

## <a name="get-endpoint-address"></a>Obter endereço do ponto de extremidade

Você precisa do endereço do ponto de extremidade do serviço Blockchain do Azure para se conectar à rede Blockchain. Você pode encontrar o endereço do ponto de extremidade e as chaves de acesso no portal do Azure.

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
1. Navegue até o membro do serviço Blockchain do Azure. Selecione **nós de transação** e o link do nó de transação padrão.

    ![Selecionar nó de transação padrão](./media/connect-metamask/transaction-nodes.png)

1. Selecione **cadeias de conexão > chaves de acesso**.
1. Copie o endereço do ponto de extremidade do **https (tecla de acesso 1)** . Você precisa do endereço para a próxima seção.

    ![Cadeia de ligação](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>Conectar metamáscara

1. Abra a extensão do navegador metamask e entre.
1. Na lista suspensa rede, selecione **RPC personalizado**.

    ![RPC personalizado](./media/connect-metamask/custom-rpc.png)

1. Em **nova rede > nova URL RPC**, insira o endereço do ponto de extremidade copiado da seção anterior.
1. Selecione **Guardar**.

    Se a conexão tiver sido bem-sucedida, a rede privada será exibida na lista suspensa rede.

    ![Nova rede](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Implantar contrato inteligente

Remix é um ambiente de desenvolvimento de solidez baseado em navegador. Usando o metamask e o remix juntos, você pode implantar e executar ações em contratos inteligentes.

1. No seu browser, navegue até `https://remix.ethereum.org`.
1. Selecione **novo arquivo** na guia **início** em **arquivo**.

    Nomeie o novo arquivo `simple.sol`.

    ![Criar arquivo](./media/connect-metamask/create-file.png)

    Selecione **OK**.
1. No editor de remix, Cole o código **simples do contrato inteligente** a seguir.

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

    O **contrato simples** declara uma variável de estado denominada **Balance**. Há duas funções definidas. A função **Add** adiciona um número ao **saldo**. A função **Get** retorna o valor do **saldo**.
1. Para compilar o contrato, primeiro selecione o painel do compilador de solidez e, em seguida, selecione **Compilar simples. sol**. 

    ![Organizar](./media/connect-metamask/compile.png)

1. Selecione o painel **implantar & executar** e, em seguida, defina o **ambiente** como **injetado Web3** para se conectar por meio de metamask ao membro do blockchain.

    ![Guia executar](./media/connect-metamask/injected-web3.png)

1. Selecione o contrato **simples** e, em seguida, **implantar**.

    ![Implementação](./media/connect-metamask/deploy.png)


1. Uma notificação de metamáscara é exibida alertando você sobre fundos insuficientes para executar a transação.

    Para uma rede blockchain pública, você precisará de ether para pagar pelo custo da transação. Como essa é uma rede privada em um consórcio, você pode definir o preço de gás como zero.

1.  Selecione a **taxa de gás > editar > avançado**, defina o **preço de gás** como 0.

    ![Preço de gás](./media/connect-metamask/gas-price.png)

    Selecione **Guardar**.

1. Selecione **confirmar** para implantar o contrato inteligente no blockchain.
1. Na seção **contratos implantados** , expanda o contrato **simples** .

    ![Contrato implantado](./media/connect-metamask/deployed-contract.png)

    Há duas ações **Adicionar** e **obter** esse mapa para as funções definidas no contrato.

1. Para executar uma transação de **adição** no blockchain, insira um número para adicionar e selecione **Adicionar**. Você pode obter uma mensagem de falha de estimativa de gás de remix. Você está enviando a transação para um blockchain privado que não requer gás. Selecione **Enviar transação** para forçar a transação.
1. Semelhante a quando você implantou o contrato, uma notificação de metamáscara é exibida alertando você sobre fundos insuficientes para executar a transação.

    Como essa é uma rede privada em um consórcio, podemos definir o preço de gás como zero.

1.  Selecione a **taxa de gás > editar > avançado**, defina o **preço de gás** como 0 e selecione **salvar**.
1. Selecione **confirmar** para enviar a transação para o blockchain.
1. Selecione **obter** ação. Esta é uma chamada para dados do nó de consulta. Uma transação não é necessária.
1. No painel de depuração do remix, você pode ver detalhes sobre as transações no blockchain.

    ![Histórico de depuração](./media/connect-metamask/debug.png)

    Você pode ver a criação de um contrato **simples** , transação para **simples. Adicionar**e chamar para **Simple. Get**.

1. Você também pode ver o histórico de transações em metamask. Abra a extensão do navegador de metamáscara.
1. Na seção **histórico** , você pode ver um log do contrato e das transações implantadas.

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você usou a extensão do navegador de metamáscara para se conectar a um nó de transação do serviço Blockchain do Azure, implantar um contrato inteligente e enviar uma transação para o Blockchain. Experimente o próximo tutorial para usar o kit de desenvolvimento do Azure Blockchain para Ethereum e Truffle para criar, compilar, implantar e executar uma função de contrato inteligente por meio de uma transação.

> [!div class="nextstepaction"]
> [Criar, compilar e implantar contratos inteligentes no serviço Blockchain do Azure](send-transaction.md)