---
title: Configurar Blockchain Gerenciador de Dados-portal do Azure
description: Como criar e gerenciar Blockchain Gerenciador de Dados usando o portal do Azure.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: chroyal
ms.openlocfilehash: 1f46fe92fd6650daa3ba4b9a930c4d781925d3fc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518258"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Configurar Gerenciador de Dados Blockchain usando o portal do Azure

Configure o Blockchain Gerenciador de Dados para o serviço Blockchain do Azure para capturar dados do Blockchain e enviá-los para um tópico da grade de eventos do Azure.

Para configurar uma instância de Gerenciador de Dados do Blockchain, você:

* Criar uma instância de Gerenciador de Dados do Blockchain para um nó de transação do serviço Blockchain do Azure
* Adicionar seus aplicativos blockchain

## <a name="prerequisites"></a>Pré-requisitos

* Conclua [o início rápido: criar um membro do blockchain usando o portal do Azure](create-member.md) ou [início rápido: criar um membro Blockchain do serviço blockchain do Azure usando o CLI do Azure](create-member-cli.md)
* Criar um [tópico de grade de eventos](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Saiba mais sobre [manipuladores de eventos na grade de eventos do Azure](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Criar instância

Uma instância de Gerenciador de Dados do Blockchain conecta e monitora um nó de transação do serviço Blockchain do Azure. Somente os usuários com acesso ao nó de transação podem criar uma conexão. Uma instância captura todos os dados brutos de bloqueio e de transação do nó de transação.

Uma conexão de saída envia dados blockchain para a grade de eventos do Azure. Você configura uma conexão de saída única quando cria a instância. Blockchain Gerenciador de Dados dá suporte a várias conexões de saída de tópico de grade de eventos para qualquer instância de Gerenciador de Dados de Blockchain especificada. Você pode enviar dados do blockchain para um único destino ou enviar dados do blockchain para vários destinos. Para adicionar outro destino, basta adicionar outras conexões de saída à instância.

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
1. Vá para o membro do serviço Blockchain do Azure que você deseja conectar ao Blockchain Gerenciador de Dados. Selecione **Blockchain Gerenciador de dados**.
1. Selecione **Adicionar**.

    ![Adicionar Gerenciador de Dados Blockchain](./media/data-manager-portal/add-instance.png)

    Introduza os seguintes detalhes:

    Definição | Descrição
    --------|------------
    Nome | Insira um nome exclusivo para um Gerenciador de Dados Blockchain conectado. O nome de Gerenciador de Dados Blockchain pode conter letras minúsculas e números e tem um comprimento máximo de 20 caracteres.
    Nó de transação | Escolha um nó de transação. Somente os nós de transação aos quais você tem acesso de leitura são listados.
    Nome da ligação | Insira um nome exclusivo da conexão de saída onde os dados da transação blockchain são enviados.
    Ponto de extremidade da grade de eventos | Escolha um tópico de grade de eventos na mesma assinatura que a instância de Gerenciador de Dados do Blockchain.

1. Selecione **OK**.

    Leva menos de um minuto para criar uma instância de Gerenciador de Dados Blockchain. Depois que a instância for implantada, ela será iniciada automaticamente. Uma instância de Gerenciador de Dados em execução Blockchain captura eventos de Blockchain do nó de transação e envia dados para as conexões de saída. Se você também deseja capturar dados de eventos e propriedades decodificados do nó transação, crie um aplicativo blockchain para a instância de Gerenciador de Dados do Blockchain.

    A nova instância aparece na lista de instâncias de Gerenciador de Dados Blockchain para o membro do serviço Blockchain do Azure.

    ![Lista de instâncias de membro de dados Blockchain](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Adicionar aplicativo blockchain

Se você adicionar um aplicativo blockchain, Blockchain Gerenciador de Dados decodificará o evento e o estado da propriedade para o aplicativo. Caso contrário, somente os dados brutos de bloco e de transação bruto serão enviados. Blockchain Gerenciador de Dados também descobre os endereços de contrato quando o contrato é implantado. Você pode adicionar vários aplicativos blockchain a uma instância de Gerenciador de Dados do Blockchain.

Blockchain Gerenciador de Dados requer uma ABI de contrato inteligente e um arquivo de código de bytes para adicionar o aplicativo.

### <a name="get-contract-abi-and-bytecode"></a>Obter ABI do contrato e código de bytes

A ABI do contrato define as interfaces do contrato inteligente. Ele descreve como interagir com o contrato inteligente. Você pode usar a [extensão do kit de desenvolvimento do Azure Blockchain para Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) para copiar a Abi do contrato para a área de transferência.

1. No painel Visual Studio Code Explorer, expanda a pasta **Build/Contracts** do projeto de sólidaidade.
1. Clique com o botão direito do mouse no arquivo JSON de metadados do contrato. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **. JSON** .
1. Selecione **copiar o Abi do contrato**.

    ![Painel de Visual Studio Code com a seleção de ABI do contrato de cópia](./media/data-manager-portal/abi-devkit.png)

    A ABI do contrato é copiada para a área de transferência.

1. Salve a matriz **Abi** como um arquivo JSON. Por exemplo, *Abi. JSON*. Você usará o arquivo em uma etapa posterior.

O código de bytes do contrato é o contrato inteligente compilado executado pela máquina virtual Ethereum. Você pode usar a extensão para copiar o código de bytes do contrato para a área de transferência.

1. No painel Visual Studio Code Explorer, expanda a pasta **Build/Contracts** do projeto de sólidaidade.
1. Clique com o botão direito do mouse no arquivo JSON de metadados do contrato. O nome do arquivo é o nome do contrato inteligente seguido pela extensão **. JSON** .
1. Selecione **copiar código de bytes do contrato**.

    ![Painel de Visual Studio Code com a seleção copiar código de bytes de contrato](./media/data-manager-portal/bytecode-devkit.png)

    O código de bytes do contrato é copiado para a área de transferência.

1. Salve o valor do **código de bytes** como um arquivo JSON. Por exemplo, *código de bytes. JSON*. Salve apenas o valor hexadecimal. Você usará o arquivo em uma etapa posterior.

O exemplo a seguir mostra o *Abi. JSON* e os arquivos de *código de bytes. JSON* abertos no editor de vs Code. Os arquivos devem ser semelhantes.

![Exemplo de arquivos Abi. JSON e código de bytes. JSON](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Criar o contrato ABI e URL do código de bytes

Blockchain Gerenciador de Dados requer que os arquivos de código de bytes ABI e do contrato sejam acessíveis por uma URL ao adicionar um aplicativo. Você pode usar uma conta de armazenamento do Azure para fornecer uma URL acessível de forma privada.

#### <a name="create-storage-account"></a>Criar conta de armazenamento

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Carregar arquivos de contrato

1. Crie um novo contêiner para a conta de armazenamento. Selecione **contêineres > contêiner**.

    ![Criar um contêiner de conta de armazenamento](./media/data-manager-portal/create-container.png)

    | Campo | Descrição |
    |-------|-------------|
    | Nome  | Nomeie o contêiner. Por exemplo, *smartcontract* |
    | Nível de acesso público | Escolha *privado (sem acesso anônimo)* |

1. Selecione **OK** para criar o contentor.
1. Selecione o contêiner e, em seguida, selecione **carregar**.
1. Escolha os arquivos JSON que você criou na seção [obter o Abi do contrato e o código de bytes](#get-contract-abi-and-bytecode) .

    ![Carregar blob](./media/data-manager-portal/upload-blobs.png)

    Selecione **Upload**.

#### <a name="generate-url"></a>Gerar URL

Para cada blob, gere uma assinatura de acesso compartilhado.

1. Selecione o blob do ABI JSON.
1. Selecione **gerar SAS**
1. Defina expiração de assinatura de acesso desejado e, em seguida, selecione **gerar token SAS de BLOB e URL**.

    ![Gerar token SAS](./media/data-manager-portal/generate-sas.png)

1. Copie a **URL de SAS do blob** e salve-a para a próxima seção.
1. Repita as etapas de [geração de URL](#generate-url) para o blob JSON de código de bytes.

### <a name="add-application-to-instance"></a>Adicionar aplicativo à instância

1. Selecione sua instância de Gerenciador de Dados do Blockchain na lista de instâncias.
1. Selecione **aplicativos Blockchain**.
1. Selecione **Adicionar**.

    ![Adicionar um aplicativo blockchain](./media/data-manager-portal/add-application.png)

    Insira o nome do aplicativo blockchain e as URLs ABI do Smart Contract e código de bytes.

    Definição | Descrição
    --------|------------
    Nome | Insira um nome exclusivo para o aplicativo blockchain rastrear.
    ABI do contrato | Caminho da URL para o arquivo da ABI do contrato. Para obter mais informações, consulte [criar contrato Abi e URL de código de bytes](#create-contract-abi-and-bytecode-url).
    Código de bytes do contrato | Caminho da URL para o arquivo de código de bytes. Para obter mais informações, consulte [criar contrato Abi e URL de código de bytes](#create-contract-abi-and-bytecode-url).

1. Selecione **OK**.

    Depois que o aplicativo é criado, o aplicativo aparece na lista de aplicativos blockchain.

    ![Lista de aplicativos do Blockchain](./media/data-manager-portal/artifact-list.png)

Você pode excluir a conta de armazenamento do Azure ou usá-la para configurar aplicativos mais blockchain. Se você quiser excluir a conta de armazenamento do Azure, poderá excluir o grupo de recursos. Ao eliminar o grupo de recursos também elimina a conta de armazenamento associada e quaisquer outros recursos associados ao grupo de recursos.

## <a name="stop-instance"></a>Parar instância

Pare a instância do Gerenciador de Blockchain quando desejar parar de capturar eventos de Blockchain e enviar dados para as conexões de saída. Quando a instância é interrompida, nenhum encargo é incorrido para Blockchain Gerenciador de Dados. Para obter mais informações, veja os [preços](https://azure.microsoft.com/pricing/details/blockchain-service).

1. Acesse **visão geral** e selecione **parar**.

    ![Parar instância](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [manipuladores de eventos na grade de eventos do Azure](../../event-grid/event-handlers.md).
