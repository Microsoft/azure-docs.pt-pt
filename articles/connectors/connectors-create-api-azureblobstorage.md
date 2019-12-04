---
title: Conectar-se ao armazenamento de BLOBs do Azure
description: Criar e gerenciar BLOBs em contas de armazenamento do Azure usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 10/28/2019
tags: connectors
ms.openlocfilehash: 86e8415cf2076819e23226e5e7878a2c96343f69
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789912"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Criar e gerenciar BLOBs no armazenamento de BLOBs do Azure usando o aplicativo lógico do Azure

Este artigo mostra como você pode acessar e gerenciar arquivos armazenados como BLOBs em sua conta de armazenamento do Azure de dentro de um aplicativo lógico com o conector de armazenamento de BLOBs do Azure. Dessa forma, você pode criar aplicativos lógicos que automatizam tarefas e fluxos de trabalho para gerenciar seus arquivos. Por exemplo, você pode criar aplicativos lógicos que criam, obtêm, atualizam e excluem arquivos em sua conta de armazenamento.

Suponha que você tenha uma ferramenta que é atualizada em um site do Azure. que atua como o gatilho para seu aplicativo lógico. Quando esse evento acontece, você pode fazer com que seu aplicativo lógico atualize algum arquivo em seu contêiner de armazenamento de BLOBs, que é uma ação em seu aplicativo lógico.

Se você for novo em aplicativos lógicos, examine [o que é o início rápido e aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) [: Crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para obter informações técnicas específicas do conector, consulte a [referência do conector de armazenamento de BLOBs do Azure](https://docs.microsoft.com/connectors/azureblobconnector/).

> [!IMPORTANT]
> Para habilitar o acesso de aplicativos lógicos do Azure a contas de armazenamento por trás de firewalls, consulte a seção [acessar contas de armazenamento por trás de firewalls](#storage-firewalls) mais adiante neste tópico.

<a name="blob-storage-limits"></a>

## <a name="limits"></a>Limites

* Por padrão, as ações do armazenamento de BLOBs do Azure podem ler ou gravar arquivos que são *50 MB ou menores*. Para lidar com arquivos com mais de 50 MB, mas até 1024 MB, as ações do armazenamento de BLOBs do Azure dão suporte ao [agrupamento de mensagens](../logic-apps/logic-apps-handle-large-messages.md). A ação **obter conteúdo do blob** usa implicitamente o agrupamento.

* Os gatilhos do armazenamento de BLOBs do Azure não dão suporte ao agrupamento. Ao solicitar o conteúdo do arquivo, os gatilhos selecionam apenas os arquivos que são 50 MB ou menores. Para obter arquivos com mais de 50 MB, siga este padrão:

  * Use um gatilho de armazenamento de BLOBs do Azure que retorne Propriedades de arquivo, como **quando um blob é adicionado ou modificado (somente Propriedades)** .

  * Siga o gatilho com a ação **obter conteúdo de blob** do armazenamento de BLOBs do Azure, que lê o arquivo completo e usa implicitamente o agrupamento.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma [conta de armazenamento do Azure e um contêiner de armazenamento](../storage/blobs/storage-quickstart-blobs-portal.md)

* O aplicativo lógico em que você precisa ter acesso à sua conta de armazenamento de BLOBs do Azure. Para iniciar seu aplicativo lógico com um gatilho do armazenamento de BLOBs do Azure, você precisa de um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Adicionar gatilho de armazenamento de BLOBs

Em aplicativos lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é acionado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de aplicativos lógicos cria uma instância de aplicativo lógico e começa a executar o fluxo de trabalho do aplicativo.

Este exemplo mostra como você pode iniciar um fluxo de trabalho de aplicativo lógico com o gatilho **quando um blob é adicionado ou modificado (somente Propriedades)** quando as propriedades de um blob são adicionadas ou atualizadas no seu contêiner de armazenamento.

1. No [portal do Azure](https://portal.azure.com) ou no Visual Studio, crie um aplicativo lógico em branco, que abre o designer de aplicativo lógico. Este exemplo usa o portal do Azure.

2. Na caixa de pesquisa, insira "blob do Azure" como seu filtro. Na lista de gatilhos, selecione o gatilho desejado.

   Este exemplo usa este gatilho: **quando um blob é adicionado ou modificado (somente Propriedades)**

   ![Selecionar gatilho do armazenamento de BLOBs do Azure](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. Se você for solicitado a fornecer detalhes de conexão, [Crie sua conexão de armazenamento de BLOBs agora](#create-connection). Ou, se sua conexão já existir, forneça as informações necessárias para o gatilho.

   Para este exemplo, selecione o contêiner e a pasta que você deseja monitorar.

   1. Na caixa **contêiner** , selecione o ícone de pasta.

   2. Na lista pasta, escolha o colchete angular direito ( **>** ) e, em seguida, navegue até encontrar e selecione a pasta desejada.

      ![Selecione a pasta de armazenamento a ser usada com o gatilho](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Selecione o intervalo e a frequência para a frequência com que você deseja que o gatilho Verifique a pasta em busca de alterações.

4. Quando terminar, na barra de ferramentas do designer, escolha **salvar**.

5. Agora, continue adicionando uma ou mais ações ao seu aplicativo lógico para as tarefas que você deseja executar com os resultados do gatilho.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Adicionar ação de armazenamento de BLOBs

Em aplicativos lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa no fluxo de trabalho que segue um gatilho ou outra ação. Para este exemplo, o aplicativo lógico começa com o [gatilho de recorrência](../connectors/connectors-native-recurrence.md).

1. No [portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no designer de aplicativo lógico. Este exemplo usa o portal do Azure.

2. No designer do aplicativo lógico, no gatilho ou na ação, escolha **nova etapa**.

   ![Adicionar nova etapa ao fluxo de trabalho do aplicativo lógico](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   Para adicionar uma ação entre as etapas existentes, mova o mouse sobre a seta de conexão. Escolha o sinal de adição ( **+** ) que aparece e selecione **Adicionar uma ação**.

3. Na caixa de pesquisa, insira "blob do Azure" como seu filtro. Na lista ações, selecione a ação desejada.

   Este exemplo usa esta ação: **obter conteúdo de blob**

   ![Selecionar ação de armazenamento de BLOBs do Azure](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. Se você for solicitado a fornecer detalhes de conexão, [Crie sua conexão de armazenamento de BLOBs do Azure agora](#create-connection).
Ou, se sua conexão já existir, forneça as informações necessárias para a ação.

   Para este exemplo, selecione o arquivo desejado.

   1. Na caixa **blob** , selecione o ícone de pasta.
  
      ![Selecione a pasta de armazenamento a ser usada com a ação](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Localize e selecione o arquivo desejado com base no número de **ID** do blob. Você pode encontrar esse número de **ID** nos metadados do blob que são retornados pelo gatilho de armazenamento de blob descrito anteriormente.

5. Quando terminar, na barra de ferramentas do designer, escolha **salvar**.
Para testar seu aplicativo lógico, verifique se a pasta selecionada contém um blob.

Este exemplo só Obtém o conteúdo de um blob. Para exibir o conteúdo, adicione outra ação que cria um arquivo com o blob usando outro conector. Por exemplo, adicione uma ação do OneDrive que cria um arquivo com base no conteúdo do blob.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Conectar-se à conta de armazenamento

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Quando você for solicitado a criar a conexão, forneça estas informações:

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome da Ligação** | Sim | <*nome da conexão*> | O nome a ser criado para a conexão |
   | **Storage Account** | Sim | <> *de conta de armazenamento* | Selecione sua conta de armazenamento na lista. |
   ||||

   Por exemplo:

   ![Criar conexão de conta de armazenamento de BLOBs do Azure](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png)  

1. Quando estiver pronto, selecione **criar**

1. Depois de criar a conexão, continue com a ação [Adicionar gatilho de armazenamento de BLOBs](#add-trigger) ou [Adicionar armazenamento de BLOBs](#add-action).

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo arquivo de API aberta do conector (anteriormente, Swagger), consulte a [página de referência do conector](https://docs.microsoft.com/connectors/azureblobconnector/).

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>Acessar contas de armazenamento por trás de firewalls

Você pode adicionar segurança de rede a uma conta de armazenamento do Azure restringindo o acesso com regras de firewall [e firewall](../storage/common/storage-network-security.md). No entanto, essa configuração cria um desafio para o Azure e outros serviços da Microsoft que precisam acessar a conta de armazenamento. A comunicação local no datacenter abstrai os endereços IP internos, portanto, você não pode configurar regras de firewall com restrições de IP. Para obter mais informações, veja [Configurar firewalls e redes virtuais do Armazenamento do Microsoft Azure](../storage/common/storage-network-security.md).

Aqui estão várias opções para acessar contas de armazenamento por trás de firewalls de aplicativos lógicos do Azure usando o conector de armazenamento de BLOBs do Azure ou outras soluções:

* Conector do Azure Storage Blob

  * [Acessar contas de armazenamento em outras regiões](#access-other-regions)
  * [Acessar contas de armazenamento por meio de uma rede virtual confiável](#access-trusted-virtual-network)

* Outras soluções

  * [Acesse contas de armazenamento como um serviço confiável com identidades gerenciadas](#access-trusted-service)
  * [Acessar contas de armazenamento por meio do gerenciamento de API do Azure](#access-api-management)

<a name="access-other-regions"></a>

### <a name="access-to-storage-accounts-in-other-regions"></a>Acesso a contas de armazenamento em outras regiões

Os aplicativos lógicos não podem acessar diretamente as contas de armazenamento que têm regras de firewall e estão na mesma região. No entanto, se você permitir o acesso para os [endereços IP de saída para conectores gerenciados em sua região](../logic-apps/logic-apps-limits-and-config.md#outbound), seus aplicativos lógicos poderão acessar contas de armazenamento em uma região diferente, exceto quando você usar o conector de armazenamento de tabela do Azure ou o conector de armazenamento de filas do Azure. Para acessar o armazenamento de tabelas ou o armazenamento de filas, você ainda pode usar o gatilho HTTP interno e as ações.

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>Acessar contas de armazenamento por meio de uma rede virtual confiável

Você pode colocar a conta de armazenamento em uma rede virtual do Azure que você gerencia e, em seguida, adicionar essa rede virtual à lista de redes virtuais confiáveis. Para que seu aplicativo lógico acesse a conta de armazenamento por meio de uma [rede virtual confiável](../virtual-network/virtual-networks-overview.md), você precisa implantar esse aplicativo lógico em um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), que pode se conectar a recursos em uma rede virtual. Em seguida, você pode adicionar as sub-redes no ISE à lista de confiáveis. Os conectores de armazenamento do Azure, como o conector de armazenamento de BLOBs, podem acessar diretamente o contêiner de armazenamento. Essa configuração é a mesma experiência usada para usar os pontos de extremidade de serviço de um ISE.

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>Acesse contas de armazenamento como um serviço confiável com identidades gerenciadas

Para conceder aos serviços confiáveis da Microsoft acesso a uma conta de armazenamento por meio de um firewall, você pode configurar uma exceção nessa conta de armazenamento para esses serviços. Essa solução permite que os serviços do Azure que dão suporte a [identidades gerenciadas para autenticação](../active-directory/managed-identities-azure-resources/overview.md) acessem contas de armazenamento por trás de firewalls como serviços confiáveis. Especificamente, para um aplicativo lógico no Azure multilocatário global para acessar essas contas de armazenamento, você primeiro [habilita o suporte de identidade gerenciada](../logic-apps/create-managed-service-identity.md) no aplicativo lógico. Em seguida, use a ação HTTP ou o gatilho em seu aplicativo lógico e [defina seu tipo de autenticação para usar a identidade gerenciada do aplicativo lógico](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). Para esse cenário, você pode usar *apenas* a ação ou o gatilho http.

Para configurar a exceção e o suporte de identidade gerenciada, siga estas etapas gerais:

1. Em sua conta de armazenamento, em **configurações**, selecione **firewalls e redes virtuais**. Em **permitir acesso de**, selecione a opção **redes selecionadas** para que as configurações relacionadas sejam exibidas.

1. Em **exceções**, selecione **permitir que os serviços confiáveis da Microsoft acessem essa conta de armazenamento**e, em seguida, selecione **salvar**.

   ![Selecione uma exceção que permita que os serviços confiáveis da Microsoft](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. Nas configurações do aplicativo lógico, [habilite o suporte para a identidade gerenciada](../logic-apps/create-managed-service-identity.md).

1. No fluxo de trabalho do aplicativo lógico, adicione e configure a ação HTTP ou o gatilho para acessar a conta ou a entidade de armazenamento.

   > [!IMPORTANT]
   > Para a ação HTTP de saída ou chamadas de gatilho para contas de armazenamento do Azure, certifique-se de que o cabeçalho de solicitação inclui a propriedade `x-ms-version` e a versão da API para a operação que você deseja executar na conta de armazenamento. Para obter mais informações, consulte [autenticar o acesso com identidade gerenciada](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) e [controle de versão para serviços de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests).

1. Nessa ação, [Selecione a identidade gerenciada](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) a ser usada para autenticação.

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>Acessar contas de armazenamento por meio do gerenciamento de API do Azure

Se você usar uma camada dedicada para o [Gerenciamento de API](../api-management/api-management-key-concepts.md), poderá antecipar a API de armazenamento usando o gerenciamento de API e permitindo os endereços IP da última por meio do firewall. Basicamente, adicione a rede virtual do Azure que é usada pelo gerenciamento de API à configuração de firewall da conta de armazenamento. Em seguida, você pode usar a ação de gerenciamento de API ou a ação HTTP para chamar as APIs de armazenamento do Azure. No entanto, se você escolher essa opção, precisará manipular o processo de autenticação por conta própria. Para obter mais informações, consulte [arquitetura de integração corporativa simples](https://aka.ms/aisarch).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)
