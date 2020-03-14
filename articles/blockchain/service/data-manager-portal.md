---
title: Configure Blockchain Data Manager usando portal Azure - Serviço Azure Blockchain
description: Crie e gerencie o Gestor de Dados blockchain para o Serviço Azure Blockchain utilizando o portal Azure.
ms.date: 11/04/2019
ms.topic: article
ms.reviewer: chroyal
ms.openlocfilehash: 03c22a7a23f1579a846746f21ce048b3425399c3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273166"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Configurar o Blockchain Data Manager com o portal do Azure

Configure o Gestor de Dados blockchain para o Serviço Azure Blockchain para capturar dados blockchain e enviá-los para um Tópico de Grelha de Eventos Azure.

Para configurar uma instância do Gestor de Dados blockchain, você:

* Criar um Gestor de Dados blockchain por exemplo para um nó de transação do Serviço Blockchain Azure
* Adicione as suas aplicações blockchain

## <a name="prerequisites"></a>Pré-requisitos

* Complete [Quickstart: Criar um membro blockchain usando o portal Azure](create-member.md) ou [Quickstart: Criar um membro blockchain Azure Blockchain Service usando o Azure CLI](create-member-cli.md)
* Criar um tópico de grelha de [eventos](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Saiba mais sobre [os manipuladores de eventos na Grelha de Eventos Azure](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Criar instância

Uma instância do Gestor de Dados blockchain conecta e monitoriza um nó de transações do Serviço Azure Blockchain. Apenas os utilizadores com acesso ao nó de transações podem criar uma ligação. Um caso captura todos os dados de transações brutas e blocos brutos do nó de transações.

Uma ligação de saída envia dados blockchain para a Rede de Eventos Azure. Configura uma única ligação de saída quando cria a instância. O Blockchain Data Manager suporta várias ligações de saída do Tópico de Evento para qualquer instância do Gestor de Dados blockchain. Pode enviar dados blockchain para um único destino ou enviar dados blockchain para vários destinos. Para adicionar outro destino, adicione apenas ligações adicionais de saída à instância.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Vá ao membro do Serviço Azure Blockchain que pretende ligar ao Blockchain Data Manager. Selecione **Blockchain Data Manager**.
1. Selecione **Adicionar**.

    ![Adicionar Gestor de Dados blockchain](./media/data-manager-portal/add-instance.png)

    Introduza os seguintes detalhes:

    Definição | Descrição
    --------|------------
    Nome | Introduza um nome único para um Gestor de Dados blockchain conectado. O nome Blockchain Data Manager pode conter letras e números minúsculos e tem um comprimento máximo de 20 caracteres.
    Nó de transação | Escolha um nó de transação. Apenas os nódosos de transação que leu acesso estão listados.
    Nome da ligação | Introduza um nome único da ligação de saída onde os dados de transação blockchain são enviados.
    Ponto final da grelha do evento | Escolha um tópico de grelha de eventos na mesma subscrição que a instância do Blockchain Data Manager.

1. Selecione **OK**.

    Demora menos de um minuto a criar uma instância do Gestor de Dados blockchain. Após a ocorrência ser implantada, é automaticamente iniciada. Uma instância de Blockchain Data Manager captura eventos blockchain a partir do nó de transações e envia dados para as ligações de saída.

    A nova instância aparece na lista de instâncias do Blockchain Data Manager para o membro do Serviço Blockchain Azure.

    ![Lista de casos de Membro saquede-dados blockchain](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Adicionar aplicação blockchain

Se adicionar uma aplicação blockchain, o Blockchain Data Manager descodifica o estado de eventos e propriedades para a aplicação. Caso contrário, apenas são enviados dados relativos a blocos brutos e transações brutas. O Blockchain Data Manager também descobre endereços de contrato quando o contrato é implementado. Pode adicionar várias aplicações blockchain a uma instância do Blockchain Data Manager.

> [!IMPORTANT]
> Atualmente, as aplicações blockchain que declaram tipos de [matriz](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) solidez ou tipos de [mapeamento](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) não são totalmente suportadas. As propriedades declaradas como tipos de matriz ou mapeamento não serão descodificadas em mensagens *ContractPropertiesMsg* ou *DecodedContractEventsMsg.*

O Blockchain Data Manager requer um contrato inteligente ABI e ficheiro de código de envio implementado para adicionar a aplicação.

### <a name="get-contract-abi-and-bytecode"></a>Obter contrato ABI e bytecode

O contrato ABI define as interfaces inteligentes do contrato. Descreve como interagir com o contrato inteligente. Pode utilizar o Kit de [Desenvolvimento azure blockchain para extensão Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) para copiar o contrato ABI para a área de receita.

1. No painel de explorador esguia do Código do Estúdio Visual, expanda a pasta **build/contracts** do seu projeto Solidity.
1. Clique no ficheiro JSON dos metadados do contrato. O nome do ficheiro é o nome do contrato inteligente seguido da extensão **.json.**
1. Selecione **Copy Contract ABI**.

    ![Painel de Código de Estúdio Visual com a seleção ABI do contrato de cópia](./media/data-manager-portal/abi-devkit.png)

    O contrato ABI é copiado para a prancheta.

1. Guarde a matriz **de abi** como um ficheiro JSON. Por exemplo, *abi.json*. Usa o ficheiro num passo posterior.

O Blockchain Data Manager requer o código de envio implementado para o contrato inteligente. O código de envio é diferente do código de contrato inteligente. Utilize a extensão do kit de desenvolvimento da blockchain Azure para copiar o código de acesso à pasta.

1. No painel de explorador esguia do Código do Estúdio Visual, expanda a pasta **build/contracts** do seu projeto Solidity.
1. Clique no ficheiro JSON dos metadados do contrato. O nome do ficheiro é o nome do contrato inteligente seguido da extensão **.json.**
1. Selecione **Copy Transaction Bytecode**.

    ![Painel de código de estúdio visual com a seleção de Bytecode de Transação de Cópia](./media/data-manager-portal/bytecode-devkit.png)

    O código de acesso é copiado para a pasta.

1. Guarde o valor do **código de byte** como ficheiro JSON. Por exemplo, *bytecode.json*. Usa o ficheiro num passo posterior.

O exemplo que se segue mostra *ficheiros abi.json* e *bytecode.json abertos* no editor do Código VS. Os seus ficheiros devem parecer semelhantes.

![Exemplo de ficheiros abi.json e bytecode.json](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Criar contrato ABI e bytecode URL

O Blockchain Data Manager exige que os ficheiros ABI e bytecode sejam acessíveis por um URL ao adicionar uma aplicação. Pode utilizar uma conta de Armazenamento Azure para fornecer um URL acessível a privados.

#### <a name="create-storage-account"></a>Criar conta de armazenamento

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Carregar ficheiros de contratos

1. Crie um novo recipiente para a conta de armazenamento. Selecione **Recipientes > Recipiente**.

    ![Criar um recipiente de conta de armazenamento](./media/data-manager-portal/create-container.png)

    | Campo | Descrição |
    |-------|-------------|
    | Nome  | Dê um nome ao contentor. Por exemplo, *contrato inteligente* |
    | Nível de acesso público | Escolha *O Privado (sem acesso anónimo)* |

1. Selecione **OK** para criar o contentor.
1. Selecione o recipiente e selecione **Upload**.
1. Escolha os dois ficheiros JSON que criou na secção [Get Contract ABI e bytecode.](#get-contract-abi-and-bytecode)

    ![Carregar blob](./media/data-manager-portal/upload-blobs.png)

    Selecione **Upload**.

#### <a name="generate-url"></a>Gerar URL

Para cada bolha, gere uma assinatura de acesso partilhado.

1. Selecione a bolha ABI JSON.
1. Selecione **Generate SAS**
1. Detete a expiração da assinatura de acesso desejada e, em seguida, **selecione Generate blob SAS token e URL**.

    ![Gerar ficha SAS](./media/data-manager-portal/generate-sas.png)

1. Copie o **URL Blob SAS** e guarde-o para a próxima secção.
1. Repita os passos [de URL de geração](#generate-url) para a bolha JSON bytecode.

### <a name="add-application-to-instance"></a>Adicionar aplicação à instância

1. Selecione a sua instância de Gestor de Dados blockchain na lista de instâncias.
1. Selecione **aplicações Blockchain**.
1. Selecione **Adicionar**.

    ![Adicione uma aplicação blockchain](./media/data-manager-portal/add-application.png)

    Introduza o nome da aplicação blockchain e do contrato inteligente ABI e bytecode URLs.

    Definição | Descrição
    --------|------------
    Nome | Introduza um nome único para a aplicação blockchain rastrear.
    Contrato ABI | URL caminho para o ficheiro CONTRACT ABI. Para mais informações, consulte [Criar contrato ABI e bytecode URL](#create-contract-abi-and-bytecode-url).
    Código de contrato | URL caminho para ficheiro bytecode. Para mais informações, consulte [Criar contrato ABI e bytecode URL](#create-contract-abi-and-bytecode-url).

1. Selecione **OK**.

    Uma vez criada a aplicação, a aplicação aparece na lista de aplicações blockchain.

    ![Lista de aplicações blockchain](./media/data-manager-portal/artifact-list.png)

Pode eliminar a conta De armazenamento do Azure ou usá-la para configurar mais aplicações blockchain. Se pretender eliminar a conta De armazenamento Azure, pode eliminar o grupo de recursos. Ao eliminar o grupo de recursos também elimina a conta de armazenamento associada e quaisquer outros recursos associados ao grupo de recursos.

## <a name="stop-instance"></a>Parar instância

Pare a instância do Blockchain Manager quando pretender parar de capturar eventos blockchain e enviar dados para as ligações de saída. Quando a instância é interrompida, não são incorridos encargos para o Gestor de Dados blockchain. Para obter mais informações, veja os [preços](https://azure.microsoft.com/pricing/details/blockchain-service).

1. Vá ao **Overview e** selecione **Stop**.

    ![Parar instância](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>Passos seguintes

Experimente o próximo tutorial criando um explorador de mensagens de transação blockchain usando blockchain Data Manager e Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Use o Gestor de Dados blockchain para enviar dados para o Azure Cosmos DB](data-manager-cosmosdb.md)