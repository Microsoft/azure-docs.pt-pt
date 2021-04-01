---
title: Ligue-se ao armazenamento de blob Azure
description: Criar e gerir blobs em contas de armazenamento Azure utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: cd23ff0f5ad9912440d38903a344011b069aaf16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92677716"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Criar e gerir blobs no Azure Blob Storage utilizando apps Azure Logic

Este artigo mostra como pode aceder e gerir ficheiros armazenados como bolhas na sua conta de armazenamento Azure a partir de dentro de uma aplicação lógica com o conector de armazenamento Azure Blob. Desta forma, pode criar aplicações lógicas que automatizam tarefas e fluxos de trabalho para gerir os seus ficheiros. Por exemplo, pode criar aplicações lógicas que criam, obtêm, atualizam e eliminam ficheiros na sua conta de armazenamento.

Suponha que tem uma ferramenta que é atualizada num site da Azure. que funciona como o gatilho para a sua aplicação lógica. Quando este evento acontecer, pode ter a sua aplicação lógica a atualizar algum ficheiro no seu recipiente de armazenamento de bolhas, que é uma ação na sua aplicação lógica.

Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para obter informações técnicas específicas do conector, consulte a referência do [conector de armazenamento Azure Blob](/connectors/azureblobconnector/).

> [!IMPORTANT]
> As aplicações lógicas não conseguem aceder diretamente às contas de armazenamento que estão por trás de firewalls se ambas estiverem na mesma região. Como uma solução alternativa, você pode ter suas aplicações lógicas e conta de armazenamento em diferentes regiões. Para obter mais informações sobre como permitir o acesso a partir de Azure Logic Apps para armazenar contas atrás de firewalls, consulte as contas de armazenamento Access por trás da secção [de firewalls](#storage-firewalls) mais tarde neste tópico.

<a name="blob-storage-limits"></a>

## <a name="limits"></a>Limites

* Por predefinição, as ações de Armazenamento Azure Blob podem ler ou escrever ficheiros que sejam *de 50 MB ou menores*. Para lidar com ficheiros maiores do que 50 MB mas até 1024 MB, as ações de armazenamento Azure Blob [suportam a mensagem em pedaços](../logic-apps/logic-apps-handle-large-messages.md). A ação **de conteúdo sonoro Get blob** usa implicitamente o chunking.

* Os gatilhos de armazenamento Azure Blob não suportam o chunking. Ao solicitar o conteúdo do ficheiro, os gatilhos selecionam apenas ficheiros com 50 MB ou menores. Para obter ficheiros maiores que 50 MB, siga este padrão:

  * Utilize um gatilho de armazenamento Azure Blob que devolva propriedades de ficheiros, tais como **Quando uma bolha é adicionada ou modificada (apenas propriedades)**.

  * Siga o gatilho com o Azure Blob Storage **Get blob content** action, que lê o ficheiro completo e usa implicitamente o chunking.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma [conta de armazenamento e um recipiente de armazenamento Azure](../storage/blobs/storage-quickstart-blobs-portal.md)

* A aplicação lógica onde precisa de acesso à sua conta de armazenamento de blob Azure. Para iniciar a sua aplicação lógica com um gatilho de armazenamento Azure Blob, precisa de uma [aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Adicione o gatilho de armazenamento de bolhas

Nas Azure Logic Apps, todas as aplicações lógicas devem começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que dispara quando um evento específico acontece ou quando uma condição específica é cumprida. Cada vez que o gatilho dispara, o motor Logic Apps cria uma instância lógica de aplicações e começa a executar o fluxo de trabalho da sua aplicação.

Este exemplo mostra como pode iniciar um fluxo de trabalho de aplicações lógicas com o **quando uma bolha é adicionada ou modificada (apenas propriedades)** quando as propriedades de uma bolha são adicionadas ou atualizadas no seu recipiente de armazenamento.

1. No [portal Azure](https://portal.azure.com) ou Visual Studio, crie uma aplicação lógica em branco, que abre o Logic App Designer. Este exemplo utiliza o portal Azure.

2. Na caixa de pesquisa, introduza "azure blob" como filtro. A partir da lista de gatilhos, selecione o gatilho que deseja.

   Este exemplo utiliza este gatilho: **Quando uma bolha é adicionada ou modificada (apenas propriedades)**

   ![Selecione o gatilho de armazenamento Azure Blob](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. Se for solicitado para obter detalhes de ligação, [crie já a sua ligação de armazenamento de bolhas](#create-connection). Ou, se a sua ligação já existe, forneça as informações necessárias para o gatilho.

   Para este exemplo, selecione o recipiente e a pasta que pretende monitorizar.

   1. Na caixa **Do Recipiente,** selecione o ícone da pasta.

   2. Na lista de pastas, escolha o suporte de ângulo reto ( **>** ) e, em seguida, navegue até encontrar e selecione a pasta desejada.

      ![Selecione a pasta de armazenamento para usar com gatilho](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Selecione o intervalo e a frequência para a frequência com que pretende que o gatilho verifique se a pasta se verifica.

4. Quando terminar, na barra de ferramentas do designer, escolha **Save**.

5. Agora continue a adicionar uma ou mais ações à sua aplicação lógica para as tarefas que pretende executar com os resultados do gatilho.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Adicione ação de armazenamento de bolhas

Nas Azure Logic Apps, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um passo no seu fluxo de trabalho que segue um gatilho ou outra ação. Para este exemplo, a aplicação lógica começa com o [gatilho de Recorrência](../connectors/connectors-native-recurrence.md).

1. No [portal Azure](https://portal.azure.com) ou Visual Studio, abra a sua aplicação lógica no Logic App Designer. Este exemplo utiliza o portal Azure.

2. No Logic App Designer, sob o gatilho ou ação, escolha **Novo passo**.

   ![Adicione um novo passo ao fluxo de trabalho de aplicações lógicas](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   Para adicionar uma ação entre os passos existentes, mova o rato sobre a seta de ligação. Escolha o sinal de mais **+** () que aparece e selecione **Adicione uma ação**.

3. Na caixa de pesquisa, introduza "azure blob" como filtro. Na lista de ações, selecione a ação desejada.

   Este exemplo usa esta ação: **Obtenha conteúdo blob**

   ![Selecione Azure Blob Storage action](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. Se for solicitado para obter detalhes de ligação, [crie já a sua ligação Azure Blob Storage](#create-connection).
Ou, se a sua ligação já existir, forneça as informações necessárias para a ação.

   Para este exemplo, selecione o ficheiro que pretende.

   1. Na caixa **Blob,** selecione o ícone da pasta.
  
      ![Selecione a pasta de armazenamento para usar com ação](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Encontre e selecione o ficheiro que deseja com base no número de **identificação** da bolha. Pode encontrar este número **de identificação** nos metadados da bolha que é devolvido pelo gatilho de armazenamento de bolhas previamente descrito.

5. Quando terminar, na barra de ferramentas do designer, escolha **Save**.
Para testar a sua aplicação lógica, certifique-se de que a pasta selecionada contém uma bolha.

Este exemplo só obtém o conteúdo de uma bolha. Para visualizar o conteúdo, adicione outra ação que cria um ficheiro com a bolha utilizando outro conector. Por exemplo, adicione uma ação OneDrive que cria um ficheiro baseado no conteúdo da bolha.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Ligar à conta de armazenamento

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Quando for solicitado a criar a ligação, forneça esta informação:

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome de conexão** | Yes | <*nome de conexão*> | O nome para criar para a sua ligação |
   | **Conta de Armazenamento** | Yes | <*conta de armazenamento*> | Selecione a sua conta de armazenamento na lista. |
   ||||

   Por exemplo:

   ![Criar ligação de conta de armazenamento Azure Blob](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png) 

1. Quando estiver pronto, selecione **Criar**

1. Depois de criar a sua ligação, continue com [adicione o gatilho de armazenamento de bolhas](#add-trigger) ou adicione a ação de armazenamento de [bolhas](#add-action).

## <a name="connector-reference"></a>Referência do conector

Para obter mais detalhes técnicos sobre este conector, tais como gatilhos, ações e limites descritos pelo ficheiro Swagger do conector, consulte a [página de referência do conector](/connectors/azureblobconnector/).

> [!NOTE]
> Para aplicações lógicas num ambiente de [serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão com rótulo ISE deste conector utiliza os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>Aceder a contas de armazenamento por trás de firewalls

Pode adicionar segurança de rede a uma conta de armazenamento Azure, restringindo o acesso com [uma firewall e regras de firewall](../storage/common/storage-network-security.md). No entanto, esta configuração cria um desafio para o Azure e outros serviços da Microsoft que precisam de acesso à conta de armazenamento. A comunicação local no datacenter abstrata os endereços IP internos, pelo que não é possível configurar regras de firewall com restrições de IP. Para obter mais informações, veja [Configurar firewalls e redes virtuais do Armazenamento do Microsoft Azure](../storage/common/storage-network-security.md).

Aqui estão várias opções para aceder a contas de armazenamento atrás de firewalls de Azure Logic Apps, utilizando o conector de armazenamento Azure Blob ou outras soluções:

* Conector do Azure Storage Blob

  * [Contas de armazenamento de acesso noutras regiões](#access-other-regions)
  * [Aceder a contas de armazenamento através de uma rede virtual fidedigna](#access-trusted-virtual-network)

* Outras soluções

  * [Aceder às contas de armazenamento como um serviço de confiança com identidades geridas](#access-trusted-service)
  * [Aceder a contas de armazenamento através da Azure API Management](#access-api-management)

<a name="access-other-regions"></a>

### <a name="problems-accessing-storage-accounts-in-the-same-region"></a>Problemas no acesso a contas de armazenamento na mesma região

As aplicações lógicas não conseguem aceder diretamente às contas de armazenamento por trás de firewalls quando ambas estão na mesma região. Como solução alternativa, coloque as suas aplicações lógicas numa região que difere da sua conta de armazenamento e dê acesso aos [endereços IP de saída para os conectores geridos na sua região.](../logic-apps/logic-apps-limits-and-config.md#outbound)

> [!NOTE]
> Esta solução não se aplica ao conector de armazenamento de mesa Azure e ao conector de armazenamento da fila Azure. Em vez disso, para aceder ao armazenamento de mesa ou ao armazenamento de filas, utilize o gatilho HTTP incorporado e as ações.

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>Aceder a contas de armazenamento através de uma rede virtual fidedigna

Pode colocar a conta de armazenamento numa rede virtual Azure que gere e, em seguida, adicionar essa rede virtual à lista de redes virtuais fidedignas. Para que a sua aplicação lógica aceda à conta de armazenamento através de uma [rede virtual fidedigna,](../virtual-network/virtual-networks-overview.md)é necessário implementar essa aplicação lógica para um [ambiente de serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)que pode ligar-se a recursos numa rede virtual. Em seguida, pode adicionar as sub-redes dessa ISE à lista de fidedignos. Os conectores de armazenamento Azure, como o conector blob storage, podem aceder diretamente ao recipiente de armazenamento. Esta configuração é a mesma experiência que usar os pontos finais de serviço de um ISE.

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>Aceder às contas de armazenamento como um serviço de confiança com identidades geridas

Para dar à Microsoft acesso a uma conta de armazenamento através de uma firewall, pode configurar uma exceção nessa conta de armazenamento para esses serviços. Esta solução permite aos serviços Azure que [suportam identidades geridas para a autenticação](../active-directory/managed-identities-azure-resources/overview.md) acederem a contas de armazenamento atrás de firewalls como serviços fidedignos. Especificamente, para uma aplicação lógica no multi-inquilino global Azure para aceder a estas contas de armazenamento, você primeiro [ativa o suporte de identidade gerido](../logic-apps/create-managed-service-identity.md) na aplicação lógica. Em seguida, utilize a ação HTTP ou o gatilho na sua aplicação lógica e [desempenhe o seu tipo de autenticação para utilizar a identidade gerida da sua aplicação lógica.](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) Para este cenário, pode utilizar *apenas* a ação http ou gatilho.

Para estabelecer a exceção e o apoio à identidade gerido, siga estes passos gerais:

1. Na sua conta de armazenamento, em **Definições,** selecione **Firewalls e redes virtuais**. In **Allow access from**, selecione a opção redes **selecionadas** para que apareçam as definições relacionadas.

1. Em **Exceções**, **selecione Permita que os serviços fidedignos da Microsoft acedam a esta conta de armazenamento** e, em seguida, selecione **Save**.

   ![Selecione exceção que permite serviços fidedignos da Microsoft](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. Nas definições da sua aplicação lógica, [ative o suporte para a identidade gerida.](../logic-apps/create-managed-service-identity.md)

1. No fluxo de trabalho da sua aplicação lógica, adicione e crie a ação HTTP ou gatilho para aceder à conta ou entidade de armazenamento.

   > [!IMPORTANT]
   > Para ações HTTP ou chamadas de gatilho de saída para as contas de Armazenamento Azure, certifique-se de que o cabeçalho do pedido inclui a `x-ms-version` propriedade e a versão API para a operação que pretende executar na conta de armazenamento. Para obter mais informações, consulte [autenticar o acesso com identidade gerida](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) e versão para [serviços de Armazenamento Azure.](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests)

1. Nessa ação, [selecione a identidade gerida](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) para a autenticação.

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>Aceder a contas de armazenamento através da Azure API Management

Se utilizar um nível dedicado para [a Gestão da API,](../api-management/api-management-key-concepts.md)pode fazer frente à API de Armazenamento utilizando a API Management e permitindo que os endereços IP deste último passem pela firewall. Basicamente, adicione a rede virtual Azure que é usada pela API Management na definição de firewall da conta de armazenamento. Em seguida, pode utilizar a ação de Gestão da API ou a ação HTTP para chamar as APIs de Armazenamento Azure. No entanto, se escolher esta opção, tem de lidar com o processo de autenticação sozinho. Para mais informações, consulte [a arquitetura de integração simples da empresa.](/azure/architecture/reference-architectures/enterprise-integration/basic-enterprise-integration)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)