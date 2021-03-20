---
title: Configure Gestor de Dados blockchain usando o portal Azure - Azure Blockchain Service
description: Crie e gere o Blockchain Data Manager para o Serviço Azure Blockchain utilizando o portal Azure.
ms.date: 03/30/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: a151c28d31bf0bb7f21185fb161315d42f9563d8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85200686"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Configurar o Blockchain Data Manager com o portal do Azure

Configure o Gestor de Dados blockchain para o Serviço Azure Blockchain para capturar dados blockchain e enviá-los para um Tópico de Grelha de Eventos Azure.

Para configurar uma instância do Gestor de Dados blockchain, você:

* Criar uma instância do Gestor de Dados blockchain para um nó de transação do Serviço Azure Blockchain
* Adicione as suas aplicações blockchain

## <a name="prerequisites"></a>Pré-requisitos

* [Quickstart Completo: Criar um membro blockchain utilizando o portal Azure](create-member.md) ou [Quickstart: Criar um membro blockchain do Azure Blockchain Service utilizando o Azure CLI](create-member-cli.md). Recomenda-se o nível *padrão* do serviço Azure Blockchain ao utilizar o Blockchain Data Manager.
* Criar um [tópico de grelha de evento](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Saiba mais sobre [os manipuladores de eventos em Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Criar exemplo

Uma instância do Gestor de Dados blockchain conecta e monitoriza um nó de transação do Serviço Azure Blockchain. Apenas os utilizadores com acesso ao nó de transação podem criar uma ligação. Um caso captura todos os dados brutos de blocos brutos e transações brutas do nó de transação. Blockchain Data Manager publica uma mensagem **RawBlockAndTransactionMsg** que é um superconjunto de informações devolvidas a partir de web3.eth [getBlock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) e obter consultas de [transação.](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction)

Uma ligação de saída envia dados blockchain para Azure Event Grid. Configura uma única ligação de saída quando cria o caso. O Blockchain Data Manager suporta várias ligações de saída de Tópicos de Grelha de Evento para qualquer instância do Gestor de Dados blockchain. Pode enviar dados blockchain para um único destino ou enviar dados blockchain para vários destinos. Para adicionar outro destino, basta adicionar ligações adicionais de saída ao caso.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Vá ao membro do Serviço Azure Blockchain que pretende ligar ao Blockchain Data Manager. Selecione **Blockchain Data Manager**.
1. Selecione **Adicionar**.

    ![Adicionar Gestor de Dados blockchain](./media/data-manager-portal/add-instance.png)

    Introduza os seguintes detalhes:

    Definições | Descrição
    --------|------------
    Nome | Insira um nome único para um Gestor de Dados blockchain conectado. O nome blockchain Data Manager pode conter letras minúsculas e números e tem um comprimento máximo de 20 caracteres.
    Nó de transação | Escolha um nó de transação. Estão listados apenas os nós de transação que leu.
    Nome da ligação | Introduza um nome único da ligação de saída onde os dados de transação blockchain são enviados.
    Ponto final da grelha de eventos | Escolha um tópico de grelha de evento na mesma subscrição que a instância do Gestor de Dados blockchain.

1. Selecione **OK**.

    Demora menos de um minuto a criar uma instância do Gestor de Dados blockchain. Após a implementação do caso, é automaticamente iniciado. Uma instância do Gestor de Dados blockchain em execução captura eventos blockchain a partir do nó de transação e envia dados para as ligações de saída.

    A nova instância aparece na lista de instâncias do Gestor de Dados blockchain para o membro do Serviço Azure Blockchain.

    ![Lista de instâncias do Membro de Dados blockchain](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Adicionar aplicação blockchain

Se adicionar uma aplicação blockchain, o Blockchain Data Manager descodifica o evento e o estado de propriedade para a aplicação. Caso contrário, apenas são enviados dados de blocos brutos e de transações brutas. O Gestor de Dados blockchain também descobre endereços de contrato quando o contrato é implementado. Pode adicionar várias aplicações blockchain a uma instância do Gestor de Dados blockchain.

> [!IMPORTANT]
> Atualmente, aplicações blockchain que declaram [tipos de matrizes](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) solidity ou [tipos de mapeamento](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) não são totalmente suportados. As propriedades declaradas como tipos de matriz ou mapeamento não serão descodificadas em mensagens *ContractPropertiesMsg* ou *DecodedContractEventsMsg.*

O Blockchain Data Manager requer um contrato inteligente ABI e ficheiro por código portecode implantado para adicionar a aplicação.

### <a name="get-contract-abi-and-bytecode"></a>Obtenha contrato ABI e bytecode

O contrato ABI define as interfaces de contrato inteligente. Descreve como interagir com o contrato inteligente. Pode utilizar o [Kit de Desenvolvimento Azure Blockchain para a extensão Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) para copiar o contrato ABI para a área de transferência.

1. No painel de exploradores visual Studio Code, expanda a pasta **de construção/contratos** do seu projeto Solidity.
1. Clique com o botão direito no ficheiro JSON de metadados contratuais. O nome do ficheiro é o nome do contrato inteligente seguido da extensão **.json.**
1. Selecione **Copy Contract ABI**.

    ![Painel visual studio code com a seleção de ABI do contrato de cópia](./media/data-manager-portal/abi-devkit.png)

    O contrato ABI é copiado para a área de transferência.

1. Guarde a matriz **abi** como um ficheiro JSON. Por exemplo, *abi.js.* Usa o ficheiro num passo posterior.

O Gestor de Dados blockchain requer o código de acesso implementado para o contrato inteligente. O código porte código implementado é diferente do código de contrato inteligente. Utilize a extensão do kit de desenvolvimento da blockchain Azure para copiar o código de acesso à área de transferência.

1. No painel de exploradores visual Studio Code, expanda a pasta **de construção/contratos** do seu projeto Solidity.
1. Clique com o botão direito no ficheiro JSON de metadados contratuais. O nome do ficheiro é o nome do contrato inteligente seguido da extensão **.json.**
1. Selecione **Copy Transaction Bytecode**.

    ![Painel visual studio code com a seleção de código de transação de cópia](./media/data-manager-portal/bytecode-devkit.png)

    O código bytecode é copiado para a área de transferência.

1. Guarde o valor **do código de acesso** como ficheiro JSON. Por exemplo, *bytecode.js.* Usa o ficheiro num passo posterior.

O exemplo que se segue mostra *abi.jse* *bytecode.jsem* ficheiros abertos no editor do Código VS. Os seus ficheiros devem ser semelhantes.

![Exemplo de abi.jsem e bytecode.jsem ficheiros](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Criar contrato ABI e BYTECODE URL

O Blockchain Data Manager exige que os ficheiros ABI e bytecode do contrato sejam acessíveis por um URL ao adicionar uma aplicação. Pode utilizar uma conta de Armazenamento Azure para fornecer um URL acessível a privados.

#### <a name="create-storage-account"></a>Criar conta de armazenamento

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Carregar ficheiros de contratos

1. Criar um novo recipiente para a conta de armazenamento. Selecione **recipientes > Recipiente**.

    ![Criar um recipiente de conta de armazenamento](./media/data-manager-portal/create-container.png)

    | Campo | Descrição |
    |-------|-------------|
    | Nome  | Dê um nome ao contentor. Por exemplo, *smartcontract* |
    | Nível de acesso público | Escolha *Privado (sem acesso anónimo)* |

1. Selecione **OK** para criar o contentor.
1. Selecione o recipiente e, em seguida, **selecione Upload**.
1. Escolha ambos os ficheiros JSON que criou na secção [Get Contract ABI e bytecode.](#get-contract-abi-and-bytecode)

    ![Bolha de upload](./media/data-manager-portal/upload-blobs.png)

    Selecione **Carregar**.

#### <a name="generate-url"></a>Gerar URL

Para cada bolha, gere uma assinatura de acesso partilhado.

1. Selecione a bolha ABI JSON.
1. **Selecione gerar SAS**
1. Descreva a expiração da assinatura de acesso desejada e, em seguida, **selecione Gerar o símbolo e o URL blob SAS**.

    ![Gerar ficha SAS](./media/data-manager-portal/generate-sas.png)

1. Copie o **URL Blob SAS** e guarde-o para a secção seguinte.
1. Repita os passos [de URL de Geração](#generate-url) para a bolha de código JSON.

### <a name="add-application-to-instance"></a>Adicionar aplicação à instância

1. Selecione a sua instância do Gestor de Dados blockchain na lista de casos.
1. Selecione **aplicações Blockchain**.
1. Selecione **Adicionar**.

    ![Adicionar uma aplicação blockchain](./media/data-manager-portal/add-application.png)

    Introduza o nome da aplicação blockchain e o contrato inteligente ABI e BYtecode URLs.

    Definições | Descrição
    --------|------------
    Nome | Introduza um nome único para a aplicação blockchain para rastrear.
    Contrato ABI | Caminho URL para o ficheiro ABI do contrato. Para obter mais informações, consulte [Criar contrato ABI e BYTECODE URL](#create-contract-abi-and-bytecode-url).
    Bytecode do Contrato | Caminho URL para o ficheiro bytecode. Para obter mais informações, consulte [Criar contrato ABI e BYTECODE URL](#create-contract-abi-and-bytecode-url).

1. Selecione **OK**.

    Uma vez criada a aplicação, a aplicação aparece na lista de aplicações blockchain.

    ![Lista de aplicações blockchain](./media/data-manager-portal/artifact-list.png)

Pode eliminar a conta de Armazenamento Azure ou usá-la para configurar mais aplicações blockchain. Se desejar eliminar a conta de Armazenamento Azure, pode eliminar o grupo de recursos. Ao eliminar o grupo de recursos também elimina a conta de armazenamento associada e quaisquer outros recursos associados ao grupo de recursos.

## <a name="stop-instance"></a>Paragem da instância

Pare a instância do Blockchain Manager quando pretender parar de capturar eventos blockchain e enviar dados para as ligações de saída. Quando o caso é interrompido, não são incorridos encargos para o Gestor de Dados blockchain. Para obter mais informações, veja os [preços](https://azure.microsoft.com/pricing/details/blockchain-service).

1. Vá ao **Overview** e **selecione Stop**.

    ![Paragem da instância](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>Passos seguintes

Experimente o próximo tutorial criando um explorador de mensagens de transação blockchain usando blockchain Data Manager e Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Utilizar o Blockchain Data Manager para enviar dados para o Azure Cosmos DB](data-manager-cosmosdb.md)