---
title: Ligue-se ao Armazenamento De Blob Azure
description: Crie e gerencie bolhas em contas de armazenamento Azure usando aplicações da Lógica Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: eb943bfe36be10d1e95d569a5c1bf48563e909c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247361"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Crie e gerencie bolhas no armazenamento de Blob Azure usando aplicações da Azure Logic

Este artigo mostra como pode aceder e gerir ficheiros armazenados como bolhas na sua conta de armazenamento Azure a partir de dentro de uma aplicação lógica com o conector de armazenamento Azure Blob. Desta forma, pode criar aplicações lógicas que automatizam tarefas e fluxos de trabalho para gerir os seus ficheiros. Por exemplo, pode criar aplicações lógicas que criam, obtêm, atualizam e eliminam ficheiros na sua conta de armazenamento.

Suponha que tenha uma ferramenta que seja atualizada num site do Azure. que funciona como o gatilho para a sua aplicação lógica. Quando este evento acontecer, pode ter a sua aplicação lógica atualizar algum ficheiro no seu recipiente de armazenamento blob, o que é uma ação na sua aplicação lógica.

Se você é novo em aplicações lógicas, reveja [o que são As Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) e [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para obter informações técnicas específicas do conector, consulte a referência do [conector Azure Blob .](https://docs.microsoft.com/connectors/azureblobconnector/)

> [!IMPORTANT]
> As aplicações lógicas não podem aceder diretamente a contas de armazenamento que estão por trás de firewalls se ambas estiverem na mesma região. Como suposições, pode ter as suas aplicações lógicas e conta de armazenamento em diferentes regiões. Para obter mais informações sobre o acesso das Aplicações Lógicas Azure às contas de armazenamento atrás de firewalls, consulte as contas de armazenamento do [Access por detrás](#storage-firewalls) da secção de firewalls mais tarde neste tópico.

<a name="blob-storage-limits"></a>

## <a name="limits"></a>Limites

* Por predefinição, as ações de armazenamento de Blob Azure podem ler ou escrever ficheiros com *50 MB ou menores*. Para lidar com ficheiros superiores a 50 MB mas até 1024 MB, as ações de armazenamento de Blob Azure suportam [o chunking da mensagem](../logic-apps/logic-apps-handle-large-messages.md). A ação de **conteúdo de blob Get** usa implicitamente o chunking.

* Os gatilhos de armazenamento de Blob Azure não suportam o chunking. Ao solicitar o conteúdo do ficheiro, os gatilhos selecionam apenas ficheiros com 50 MB ou menores. Para obter ficheiros superiores a 50 MB, siga este padrão:

  * Utilize um gatilho de armazenamento de blob Azure que deredere propriedades de ficheiros, como **quando uma bolha é adicionada ou modificada (apenas propriedades)**.

  * Siga o gatilho com a ação de **conteúdo blob Blob** Azure Get, que lê o ficheiro completo e utiliza implicitamente a chunking.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma [conta de armazenamento azure e recipiente de armazenamento](../storage/blobs/storage-quickstart-blobs-portal.md)

* A aplicação lógica onde precisa de acesso à sua conta de armazenamento de blob Azure. Para iniciar a sua aplicação lógica com um gatilho de Armazenamento Azure Blob, precisa de uma [aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Adicione o gatilho de armazenamento de bolhas

Nas Aplicações Lógicas Do Azure, todas as aplicações lógicas devem começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que dispara quando um evento específico acontece ou quando uma condição específica é satisfeita. Cada vez que o gatilho dispara, o motor Logic Apps cria uma instância de aplicação lógica e começa a executar o fluxo de trabalho da sua aplicação.

Este exemplo mostra como pode iniciar um fluxo de trabalho de aplicações lógica com o **gatilho Quando uma bolha é adicionada ou modificada (apenas propriedades)** quando as propriedades de uma bolha são adicionadas ou atualizadas no seu recipiente de armazenamento.

1. No [portal Azure](https://portal.azure.com) ou Visual Studio, crie uma aplicação lógica em branco, que abre o Logic App Designer. Este exemplo utiliza o portal Azure.

2. Na caixa de pesquisa, introduza "borbulh" como filtro. A partir da lista de gatilhos, selecione o gatilho que deseja.

   Este exemplo utiliza este gatilho: **Quando uma bolha é adicionada ou modificada (apenas propriedades)**

   ![Selecione gatilho de armazenamento de blob Azure](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. Se for solicitado para obter detalhes de ligação, crie agora a [sua ligação de armazenamento de bolhas](#create-connection). Ou, se a sua ligação já existir, forneça as informações necessárias para o gatilho.

   Para este exemplo, selecione o recipiente e a pasta que pretende monitorizar.

   1. Na caixa **de contentores,** selecione o ícone da pasta.

   2. Na lista de pastas, escolha **>** o suporte do ângulo reto e, em seguida, navegue até encontrar e selecionar a pasta que deseja.

      ![Selecione pasta de armazenamento para usar com gatilho](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Selecione o intervalo e a frequência para saber quantas vezes pretende que o gatilho verifique se a pasta está a verificar se há alterações.

4. Quando terminar, na barra de ferramentas de design, escolha **Save**.

5. Agora continue a adicionar uma ou mais ações à sua aplicação lógica para as tarefas que pretende executar com os resultados do gatilho.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Adicione ação de armazenamento de blob

Nas Aplicações Lógicas Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um passo no seu fluxo de trabalho que segue um gatilho ou outra ação. Para este exemplo, a aplicação lógica começa com o [gatilho de Recorrência](../connectors/connectors-native-recurrence.md).

1. No [portal Azure](https://portal.azure.com) ou No Estúdio Visual, abra a sua aplicação lógica no Logic App Designer. Este exemplo utiliza o portal Azure.

2. No Logic App Designer, sob o gatilho ou ação, escolha **novo passo**.

   ![Adicione um novo passo para o fluxo de trabalho de aplicações lógicas](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   Para adicionar uma ação entre os passos existentes, mova o rato sobre a seta de ligação. Escolha o sinal**+** de mais () que aparece e selecione **Adicionar uma ação**.

3. Na caixa de pesquisa, introduza "borbulh" como filtro. Na lista de ações, selecione a ação que deseja.

   Este exemplo usa esta ação: **Obter conteúdo blob**

   ![Selecione ação de armazenamento de Blob Azure](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. Se for solicitado para obter detalhes de ligação, crie agora a [sua ligação de armazenamento Azure Blob](#create-connection).
Ou, se a sua ligação já existir, forneça as informações necessárias para a ação.

   Para este exemplo, selecione o ficheiro que pretende.

   1. A partir da caixa **Blob,** selecione o ícone da pasta.
  
      ![Selecione pasta de armazenamento para usar com ação](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Encontre e selecione o ficheiro que deseja com base no número de **identificação** da bolha. Pode encontrar este número de **identificação** nos metadados da bolha que é devolvido pelo gatilho de armazenamento blob previamente descrito.

5. Quando terminar, na barra de ferramentas de design, escolha **Save**.
Para testar a sua aplicação lógica, certifique-se de que a pasta selecionada contém uma bolha.

Este exemplo só recebe o conteúdo para uma bolha. Para visualizar o conteúdo, adicione outra ação que crie um ficheiro com a bolha utilizando outro conector. Por exemplo, adicione uma ação OneDrive que cria um ficheiro baseado no conteúdo blob.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Ligar à conta de armazenamento

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Quando for solicitado a criar a ligação, forneça esta informação:

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome de ligação** | Sim | <*nome de ligação*> | O nome a criar para a sua ligação |
   | **Conta de Armazenamento** | Sim | <*conta de armazenamento*> | Selecione a sua conta de armazenamento na lista. |
   ||||

   Por exemplo:

   ![Criar conexão de conta de armazenamento Azure Blob](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png) 

1. Quando estiver pronto, selecione **Criar**

1. Depois de criar a sua ligação, continue com [o gatilho de armazenamento de blob adicionar](#add-trigger) ou adicionar ação de armazenamento [blob](#add-action).

## <a name="connector-reference"></a>Referência do conector

Para obter mais detalhes técnicos sobre este conector, tais como gatilhos, ações e limites descritos pelo ficheiro Swagger do conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/azureblobconnector/).

> [!NOTE]
> Para aplicações lógicas num ambiente de serviço de [integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão do conector com o rótulo ISE utiliza os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>Contas de armazenamento de acesso atrás de firewalls

Pode adicionar segurança de rede a uma conta de armazenamento Azure, restringindo o acesso com regras de [firewall e firewall](../storage/common/storage-network-security.md). No entanto, esta configuração cria um desafio para o Azure e outros serviços da Microsoft que precisam de acesso à conta de armazenamento. A comunicação local no datacenter resumia os endereços IP internos, por isso não pode configurar regras de firewall com restrições IP. Para obter mais informações, veja [Configurar firewalls e redes virtuais do Armazenamento do Microsoft Azure](../storage/common/storage-network-security.md).

Aqui estão várias opções para aceder a contas de armazenamento atrás de firewalls de Aplicações Lógicas Azure, utilizando o conector de armazenamento Azure Blob ou outras soluções:

* Conector do Azure Storage Blob

  * [Contas de armazenamento de acesso noutras regiões](#access-other-regions)
  * [Aceder a contas de armazenamento através de uma rede virtual confiável](#access-trusted-virtual-network)

* Outras soluções

  * [Aceder a contas de armazenamento como um serviço de confiança com identidades geridas](#access-trusted-service)
  * [Contas de armazenamento de acesso através da Azure API Management](#access-api-management)

<a name="access-other-regions"></a>

### <a name="problems-accessing-storage-accounts-in-the-same-region"></a>Problemas no acesso a contas de armazenamento na mesma região

As aplicações lógicas não conseguem aceder diretamente a contas de armazenamento atrás de firewalls quando ambas estão na mesma região. Como uma suver, coloque as suas aplicações lógicas numa região que difere da sua conta de armazenamento e dê acesso aos endereços IP de [saída para os conectores geridos na sua região](../logic-apps/logic-apps-limits-and-config.md#outbound).

> [!NOTE]
> Esta solução não se aplica ao conector de armazenamento de mesa Azure e ao conector de armazenamento de fila Azure. Em vez disso, para aceder ao armazenamento de mesa ou armazenamento de fila, utilize o gatilho e ações embutidas em HTTP.

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>Aceder a contas de armazenamento através de uma rede virtual confiável

Pode colocar a conta de armazenamento numa rede virtual Azure que gere e, em seguida, adicionar essa rede virtual à lista de redes virtuais fidedignas. Para que a sua aplicação lógica aceda à conta de armazenamento através de uma [rede virtual fidedigna,](../virtual-network/virtual-networks-overview.md)é necessário implementar essa aplicação lógica para um ambiente de serviço de [integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)que pode ligar-se aos recursos numa rede virtual. Em seguida, pode adicionar as subredes nesse ISE à lista de confiança. Os conectores de armazenamento Azure, como o conector blob storage, podem aceder diretamente ao recipiente de armazenamento. Esta configuração é a mesma experiência que usar os pontos finais de serviço de um ISE.

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>Aceder a contas de armazenamento como um serviço de confiança com identidades geridas

Para dar acesso a serviços fidedignos da Microsoft a uma conta de armazenamento através de uma firewall, pode configurar uma exceção nessa conta de armazenamento para esses serviços. Esta solução permite aos serviços Azure que suportam [identidades geridas para autenticação](../active-directory/managed-identities-azure-resources/overview.md) aceder a contas de armazenamento atrás de firewalls como serviços de confiança. Especificamente, para uma aplicação lógica no Azure multi-inquilino global para aceder a estas contas de armazenamento, você primeiro permite o [suporte de identidade gerido](../logic-apps/create-managed-service-identity.md) na aplicação lógica. Em seguida, você usa a ação HTTP ou gatilho na sua aplicação lógica e define o seu tipo de [autenticação para usar a identidade gerida da sua aplicação lógica](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). Para este cenário, só pode *utilizar* a ação HTTP ou o gatilho.

Para estabelecer a exceção e o apoio de identidade gerido, siga estes passos gerais:

1. Na sua conta de armazenamento, em **Definições,** selecione **Firewalls e redes virtuais**. Em modo **de permitir o acesso a partir de**, selecione a opção redes **Selecionadas para** que as definições relacionadas apareçam.

1. Em **exceções,** **selecione Permitir que os serviços fidedignos da Microsoft acedam a esta conta de armazenamento**e, em seguida, selecione **Save**.

   ![Selecione exceção que permite serviços fidedignos da Microsoft](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. Nas definições da sua aplicação lógica, [ative suporte para a identidade gerida](../logic-apps/create-managed-service-identity.md).

1. No fluxo de trabalho da sua aplicação lógica, adicione e instale a ação HTTP ou desencadeie o acesso à conta de armazenamento ou entidade.

   > [!IMPORTANT]
   > Para a ação de HTTP de saída ou chamadas de gatilho `x-ms-version` para contas de Armazenamento Azure, certifique-se de que o cabeçalho de pedido inclui o imóvel e a versão API para a operação que pretende executar na conta de armazenamento. Para mais informações, consulte [O acesso authenticado com identidade gerida](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) e versão para serviços de armazenamento [Azure.](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests)

1. Nessa ação, [selecione a identidade gerida](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) para usar para autenticação.

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>Contas de armazenamento de acesso através da Azure API Management

Se utilizar um nível dedicado para [a Gestão de API,](../api-management/api-management-key-concepts.md)pode fazer frente à API de Armazenamento utilizando a API Management e permitindo os endereços IP deste último através da firewall. Basicamente, adicione a rede virtual Azure que é usada pela API Management à definição de firewall da conta de armazenamento. Em seguida, pode utilizar a ação de Gestão API ou a ação HTTP para chamar as APIs de Armazenamento Azure. No entanto, se escolher esta opção, terá de lidar com o processo de autenticação. Para mais informações, consulte [A arquitetura de integração empresarial simples.](https://aka.ms/aisarch)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)
