---
title: Transformar XML com mapas XSLT - Azure Logic Apps | Documentos da Microsoft
description: Adicionar o que XSLT é mapeado para transformar o XML no Azure Logic Apps com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
manager: carmonm
ms.topic: article
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.date: 02/06/2019
ms.openlocfilehash: f6d778ddbce16c223945d4683bd7a950bd2a0cb0
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455807"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>Transformar XML com mapas no Azure Logic Apps com o Enterprise Integration Pack

Para transferir dados XML entre formatos para cenários de integração do enterprise no Azure Logic Apps, a sua aplicação lógica pode utilizar mapas ou, mais especificamente, os folha de estilos extensível mapeia de transformações de idioma (XSLT). Um mapa é um documento XML que descreve como converter dados de um documento XML em outro formato. 

Por exemplo, suponha que regularmente, receber ordens de B2B ou notas fiscais de um cliente que utiliza o formato de data YYYMMDD. No entanto, a sua organização utiliza o formato de data MMDDYYY. Pode definir e utilizar um mapa que transforma o formato de data no formato de MMDDYYY YYYMMDD antes de armazenar os detalhes da ordem ou por fatura na sua base de dados de atividade do cliente.

Para limites relacionados com contas de integração e artefactos, tais como mapas, consulte [limites e informações de configuração para o Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se numa conta do Azure gratuita</a>.

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) onde armazenar os seus mapas e outros artefatos para soluções de empresa-empresa (B2B) e de integração empresarial.

* Se o seu mapa faz referência a um assembly externo, terá de carregar *o assembly e o mapa* à sua conta de integração. Certifique-se de que *carregar o assembly primeiro*em seguida, carregue o mapa que faça referência ao assembly.

  Se o assembly é 2 MB ou mais pequenas, pode adicionar o assembly para a sua conta de integração *diretamente* do portal do Azure. No entanto, se o seu assembly ou um mapa é maior do que 2 MB, mas não maior do que o [limite de tamanho para assemblies ou mapas](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), dispõe das seguintes opções:

  * Para assemblies, precisa de um contentor de Blobs do Azure, onde pode carregar o assembly e a localização de nesse contentor. Dessa forma, pode fornecer esse local mais tarde quando adicionar o assembly para a sua conta de integração. 
  Para essa tarefa, precisa destes itens:

    | Item | Descrição |
    |------|-------------|
    | [Conta de armazenamento do Azure](../storage/common/storage-account-overview.md) | Esta conta, crie um contentor de Blobs do Azure para o assembly. Saiba mais [como criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md). |
    | Contentor de BLOBs | Neste contentor, pode carregar o assembly. Também precisa localização este contentor ao adicionar o assembly para a sua conta de integração. Saiba como [criar um contentor de BLOBs](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Explorador do Armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Essa ferramenta ajuda-o a mais fácil gerir contas de armazenamento e contentores de Blobs. Utilizar o Explorador de armazenamento, seja [baixe e instale o Explorador de armazenamento do Azure](https://www.storageexplorer.com/). Em seguida, ligar o Explorador de armazenamento à sua conta de armazenamento ao seguir os passos no [introdução ao Explorador de armazenamento](../vs-azure-tools-storage-manage-with-storage-explorer.md). Para obter mais informações, consulte [início rápido: Criar um blob no armazenamento de objetos com o Explorador de armazenamento do Azure](../storage/blobs/storage-quickstart-blobs-storage-explorer.md). <p>Ou, no portal do Azure, encontre e selecione a sua conta de armazenamento. No seu menu de conta de armazenamento, selecione **Explorador de armazenamento**. |
    |||

  * Para mapas, atualmente pode adicionar mapas maiores, utilizando o [mapeia a API de REST do Azure Logic Apps -](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

Não precisa de uma aplicação lógica quando criar e adicionar mapas. No entanto, para utilizar um mapa, a sua aplicação lógica precisa criar uma ligação para uma conta de integração onde armazenar esse mapa. Saiba mais [como ligar aplicações lógicas para contas de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Se ainda não tiver uma aplicação lógica, saiba [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-referenced-assemblies"></a>Adicionar assemblies referenciados

1. Inicie sessão no <a href="https://portal.azure.com" target="_blank">portal do Azure</a> com as credenciais da sua conta do Azure.

1. Para localizar e abra a sua conta de integração, no menu principal do Azure, selecione **todos os serviços**. 
   Na caixa de pesquisa, introduza "conta de integração". 
   Selecione **contas de integração**.

   ![Localizar a conta de integração](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Selecione a conta de integração em que pretende adicionar o assembly, por exemplo:

   ![Selecione a conta de integração](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Na sua conta de integração **descrição geral** página, em **componentes**, selecione o **Assemblies** mosaico.

   ![Selecione "Assemblies"](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. Depois do **Assemblies** é aberta a página, escolha **Add**.

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

Com base no tamanho do ficheiro de assemblagem, siga os passos para carregar um assembly que está [até 2 MB](#smaller-assembly) ou [mais de 2 MB, mas apenas de 8 MB](#larger-assembly).
Para limites em quantidades de assembly nas contas de integração, consulte [limites e configuração para o Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits).

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Adicionar assemblies até 2 MB

1. Sob **Adicionar assemblagem**, introduza um nome para seu assembly. Manter **pequeno arquivo** selecionado. Junto a **Assembly** caixa, escolha o ícone de pasta. Localize e selecione o assembly que está a carregar, por exemplo:

   ![Carregar a assemblagem menor](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   Na **nome da assemblagem** propriedade, o nome de ficheiro do assembly aparece automaticamente depois de selecionar o assembly.

1. Quando estiver pronto, escolha **OK**.

   Depois de concluir o seu ficheiro de assemblagem do carregamento, o assembly é apresentado na **Assemblies** lista.

   ![Lista de assemblies carregados](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   Na sua conta de integração **descrição geral** página, em **componentes**, o **Assemblies** mosaico agora mostra o número de assemblies carregados, por exemplo:

   ![Assemblies carregados](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>Adicionar assemblies com mais de 2 MB

Para adicionar assemblies maior, pode carregar o assembly para um contentor de Blobs do Azure na sua conta de armazenamento do Azure. Os passos para adicionar assemblies diferem com base se o contentor de BLOBs tem acesso de leitura público. Primeiro, verifique se é ou não o seu contentor de BLOBs tem acesso de leitura público através dos seguintes passos: [Definir o nível de acesso público para o contentor de BLOBs](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Verifique o nível de acesso do contentor

1. Abra o Explorador de armazenamento do Azure. Na janela do Explorer, expanda a sua subscrição do Azure se não estiver expandida.

1. Expanda **contas de armazenamento** > {*conta de armazenamento your*} > **contentores de BLOBs**. Selecione o contentor de Blobs.

1. No menu de atalho do seu contentor de BLOBs, selecione **defina o nível de acesso público**.

   * Se o contentor de BLOBs tem acesso, pelo menos, público, escolha **Cancelar**e siga estes passos mais adiante nesta página: [Carregar para contentores com o acesso público](#public-access-assemblies)

     ![Acesso público](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Se o contentor de BLOBs não tiver acesso público, escolha **Cancelar**e siga estes passos mais adiante nesta página: [Carregar para contentores sem acesso público](#no-public-access-assemblies)

     ![Sem acesso público](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Carregar para contentores com o acesso público

1. Carregar o assembly para a conta de armazenamento. 
   Na janela do lado direita, escolha **carregar**.

1. Depois de concluir o carregamento, selecione o assembly carregado. Na barra de ferramentas, escolha **URL de cópia** para que copie o URL do assembly.

1. Regresse ao portal do Azure onde o **Adicionar assemblagem** painel é aberto. 
   Introduza um nome para seu assembly. 
   Escolher **ficheiros grandes (superiores a 2 MB)**.

   O **URI de conteúdo** agora é apresentada a caixa, em vez de **Assembly** caixa.

1. Na **URI de conteúdo** caixa, cole o URL do seu assembly. 
   Conclua a adição de seu assembly.

Depois do assembly concluir o carregamento, o esquema aparece no **Assemblies** lista.
Na sua conta de integração **descrição geral** página, em **componentes**, o **Assemblies** mosaico mostra agora o número de assemblies carregados.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Carregar para contentores sem acesso público

1. Carregar o assembly para a conta de armazenamento. 
   Na janela do lado direita, escolha **carregar**.

1. Depois de concluir o carregamento, gere uma assinatura de acesso partilhado (SAS) para o assembly. 
   No menu de atalho do seu assembly, selecione **obter assinatura de acesso partilhado**.

1. Na **assinatura de acesso partilhado** painel, selecione **URI de assinatura de acesso partilhado do nível de contêiner de gerar** > **criar**. 
   Depois do URL de SAS é gerado, junto a **URL** caixa, escolha **cópia**.

1. Regresse ao portal do Azure onde o **Adicionar assemblagem** painel é aberto. 
   Introduza um nome para seu assembly. 
   Escolher **ficheiros grandes (superiores a 2 MB)**.

   O **URI de conteúdo** agora é apresentada a caixa, em vez de **Assembly** caixa.

1. Na **URI de conteúdo** caixa, cole o URI de SAS gerado anteriormente. Conclua a adição de seu assembly.

Depois do assembly concluir o carregamento, o assembly é apresentado na **esquemas** lista. Na sua conta de integração **descrição geral** página, em **componentes**, o **Assemblies** mosaico mostra agora o número de assemblies carregados.

## <a name="create-maps"></a>Criar mapas

Para criar um documento XSLT, pode utilizar como um mapa, pode utilizar o Visual Studio 2015 para a criação de um projeto de integração BizTalk utilizando o [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md). Neste projeto, pode criar um ficheiro de mapa de integração, o que permite que mapeie visualmente itens entre dois arquivos de esquema XML. Depois de criar este projeto, obtém um documento XSLT.
Para limites em quantidades de mapa nas contas de integração, consulte [limites e configuração para o Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). 

## <a name="add-maps"></a>Adicionar mapas

Depois de carregar todos os assemblies que faz referência a seu mapa, agora, pode carregar seu mapa.

1. Se ainda não tiver iniciado sessão, inicie sessão para o <a href="https://portal.azure.com" target="_blank">portal do Azure</a> com as suas credenciais de conta do Azure. 

1. Se a sua conta de integração não estiver aberta, no menu principal do Azure, selecione **todos os serviços**. 
   Na caixa de pesquisa, introduza "conta de integração". 
   Selecione **contas de integração**.

   ![Localizar a conta de integração](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Selecione a conta de integração em que pretende adicionar o seu mapa, por exemplo:

   ![Selecione a conta de integração](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Na sua conta de integração **descrição geral** página, em **componentes**, selecione o **Maps** mosaico.

   ![Selecione "Maps"](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. Depois do **Maps** é aberta a página, escolha **Add**.

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>Adicionar mapas até 2 MB

1. Sob **adicionar mapeamento de**, introduza um nome para o seu mapa. 

1. Sob **tipo de mapa**, selecione o tipo, por exemplo: **Líquidos**, **XSLT**, **XSLT 2.0**, ou **XSLT 3.0**.

1. Manter **pequeno arquivo** selecionado. Junto a **mapa** caixa, escolha o ícone de pasta. Localize e selecione o mapa que está a carregar, por exemplo:

   ![Carregar mapa](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   Se marcou a **nome** propriedade vazia, o nome de ficheiro do mapa é apresentada automaticamente nessa propriedade automaticamente depois de selecionar o ficheiro de mapa. 
   No entanto, pode utilizar qualquer nome exclusivo.

1. Quando estiver pronto, escolha **OK**. 
   Depois do ficheiro de mapa concluir o carregamento, o mapa aparece no **Maps** lista.

   ![Lista de mapas carregado](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   Na sua conta de integração **descrição geral** página, em **componentes**, o **mapeia** mosaico agora mostra o número de mapas carregados, por exemplo:

   ![Mapas carregados](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>Adicionar mapas mais de 2 MB

Atualmente, para adicionar mapas maior, utilize o [mapeia a API de REST do Azure Logic Apps -](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

<!--

To add larger maps, you can upload your map to 
an Azure blob container in your Azure storage account. 
Your steps for adding maps differ based whether your 
blob container has public read access. So first, check 
whether or not your blob container has public read 
access by following these steps: 
[Set public access level for blob container](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### Check container access level

1. Open Azure Storage Explorer. In the Explorer window, 
   expand your Azure subscription if not already expanded.

1. Expand **Storage Accounts** > {*your-storage-account*} > 
   **Blob Containers**. Select your blob container.

1. From your blob container's shortcut menu, 
   select **Set Public Access Level**.

   * If your blob container has at least public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers with public access](#public-access)

     ![Public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * If your blob container doesn't have public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers without public access](#public-access)

     ![No public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-maps"></a>

### Add maps to containers with public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**. 

1. After you finish uploading, select your 
   uploaded map. On the toolbar, choose **Copy URL** 
   so that you copy the map's URL.

1. Return to the Azure portal where the 
   **Add Map** pane is open. Choose **Large file**. 

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste your map's URL. 
   Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

<a name="no-public-access-maps"></a>

### Add maps to containers with no public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**.

1. After you finish uploading, generate a 
   shared access signature (SAS) for your schema. 
   From your map's shortcut menu, 
   select **Get Shared Access Signature**.

1. In the **Shared Access Signature** pane, select 
   **Generate container-level shared access signature URI** > **Create**. 
   After the SAS URL gets generated, next to the **URL** box, choose **Copy**.

1. Return to the Azure portal where the 
   **Add Maps** pane is open. Choose **Large file**.

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste the SAS URI 
   you previously generated. Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

-->

## <a name="edit-maps"></a>Editar maps

Para atualizar um mapa existente, terá de carregar um novo ficheiro de mapa, que tem as alterações pretendidas. No entanto, primeiro pode baixar o mapa existente para edição.

1. Na <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, localize e abra a sua conta de integração, se não estiver já abrir.

1. No menu principal do Azure, selecione **todos os serviços**. Na caixa de pesquisa, introduza "conta de integração". Selecione **contas de integração**.

1. Selecione a conta de integração em que pretende atualizar o seu mapa.

1. Na sua conta de integração **descrição geral** página, em **componentes**, selecione o **Maps** mosaico.

1. Depois do **Maps** é aberta a página, selecione o seu mapa. 
   Para transferir e editar o mapa em primeiro lugar, escolha **transferir**e guarde o mapa.

1. Quando estiver pronto para carregar o mapa atualizado, sobre o **Maps** , selecione o mapa de que pretende atualizar e escolha **atualizar**.

1. Localize e selecione o mapa atualizado que pretende carregar. 
   Depois do ficheiro de mapa concluir o carregamento, o mapa atualizado é apresentado na **Maps** lista.

## <a name="delete-maps"></a>Eliminar maps

1. Na <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, localize e abra a sua conta de integração, se não estiver já abrir.

1. No menu principal do Azure, selecione **todos os serviços**. 
   Na caixa de pesquisa, introduza "conta de integração". 
   Selecione **contas de integração**.

1. Selecione a conta de integração em que pretende eliminar o seu mapa.

1. Na sua conta de integração **descrição geral** página, em **componentes**, selecione o **Maps** mosaico.

1. Depois do **Maps** é aberta a página, selecione o mapa e escolha **eliminar**.

1. Para confirmar que pretende eliminar o mapa, escolha **Sim**.

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [Saiba mais sobre esquemas](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [Saiba mais sobre transformações](../logic-apps/logic-apps-enterprise-integration-transform.md)