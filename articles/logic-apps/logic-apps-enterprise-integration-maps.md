---
title: Transformar XML com mapas XSLT
description: Adicionar mapas XSLT para transformar XML em aplicativos lógicos do Azure com Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: e186b9713c8464f8f37e1e0bf112c4118621925c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979412"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>Transformar XML com mapas em aplicativos lógicos do Azure com Enterprise Integration Pack

Para transferir dados XML entre formatos para cenários de integração corporativa em aplicativos lógicos do Azure, seu aplicativo lógico pode usar mapas ou, mais especificamente, mapas XSLT (transformações de linguagem de folha de estilo extensível). Um mapa é um documento XML que descreve como converter dados de um documento XML em outro formato. 

Por exemplo, suponha que você receba regularmente ordens B2B ou faturas de um cliente que usa o formato de data AAAAMMDD. No entanto, sua organização usa o formato de data MMDDAAAA. Você pode definir e usar um mapa que transforma o formato de data AAAAMMDD no formato MMDDAAAA antes de armazenar os detalhes da ordem ou da nota fiscal no banco de dados de atividades do cliente.

Para os limites relacionados a contas de integração e artefatos como mapas, consulte [limites e informações de configuração para aplicativos lógicos do Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) em que você armazena seus mapas e outros artefatos para integração corporativa e Soluções B2B (entre empresas).

* Se o mapa fizer referência a um assembly externo, você precisará carregar *o assembly e o mapa* para sua conta de integração. Certifique-se de [*carregar o assembly primeiro*](#add-assembly)e, em seguida, carregue o mapa que faz referência ao assembly.

  Se o assembly for de 2 MB ou menor, você poderá adicionar seu assembly à sua conta de integração *diretamente* do portal do Azure. No entanto, se seu assembly ou mapa for maior que 2 MB, mas não maior que o [limite de tamanho para assemblies ou mapas](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), você terá estas opções:

  * Para assemblies, você precisa de um contêiner de blob do Azure onde você pode carregar seu assembly e o local do contêiner. Dessa forma, você pode fornecer esse local mais tarde quando adicionar o assembly à sua conta de integração. 
  Para esta tarefa, você precisa destes itens:

    | Item | Descrição |
    |------|-------------|
    | [Conta de armazenamento do Azure](../storage/common/storage-account-overview.md) | Nessa conta, crie um contêiner de blob do Azure para seu assembly. Saiba [como criar uma conta de armazenamento](../storage/common/storage-account-create.md). |
    | Contentor de blobs | Nesse contêiner, você pode carregar seu assembly. Você também precisa do local desse contêiner ao adicionar o assembly à sua conta de integração. Saiba como [criar um contêiner de BLOBs](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Explorador do Armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Essa ferramenta ajuda você a gerenciar mais facilmente as contas de armazenamento e os contêineres de BLOB. Para usar Gerenciador de Armazenamento, [Baixe e instale o Gerenciador de armazenamento do Azure](https://www.storageexplorer.com/). Em seguida, conecte Gerenciador de Armazenamento à sua conta de armazenamento seguindo as etapas em introdução [ao Gerenciador de armazenamento](../vs-azure-tools-storage-manage-with-storage-explorer.md). Para saber mais, consulte [início rápido: criar um blob no armazenamento de objetos com o Gerenciador de armazenamento do Azure](../storage/blobs/storage-quickstart-blobs-storage-explorer.md). <p>Ou, no portal do Azure, localize e selecione sua conta de armazenamento. No menu da sua conta de armazenamento, selecione **Gerenciador de armazenamento**. |
    |||

  * Para mapas, você pode adicionar mapas maiores usando os mapas da [API REST dos aplicativos lógicos do Azure](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

Você não precisa de um aplicativo lógico ao criar e adicionar mapas. No entanto, para usar um mapa, seu aplicativo lógico precisa vincular a uma conta de integração em que você armazena esse mapa. Saiba [como vincular aplicativos lógicos a contas de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Se você ainda não tiver um aplicativo lógico, saiba [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies"></a>Adicionar assemblies referenciados

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure.

1. Para localizar e abrir sua conta de integração, no menu principal do Azure, selecione **todos os serviços**. 
   Na caixa de pesquisa, digite "conta de integração". 
   Selecione **contas de integração**.

   ![Localizar conta de integração](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Selecione a conta de integração à qual você deseja adicionar o assembly, por exemplo:

   ![Selecionar conta de integração](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Na página **visão geral** da sua conta de integração, em **componentes**, selecione o bloco **assemblies** .

   ![Selecione "assemblies"](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. Depois que a página **assemblies** for aberta, escolha **Adicionar**.

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

Com base no tamanho do seu arquivo de assembly, siga as etapas para carregar um assembly que seja de [até 2 MB](#smaller-assembly) ou [mais de 2 MB, mas apenas até 8 MB](#larger-assembly).
Para limites de quantidades de assembly em contas de integração, consulte [limites e configuração para aplicativos lógicos do Azure](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits).

> [!NOTE]
> Se você alterar o assembly, também deverá atualizar seu mapa se o mapa tiver ou não alterações.

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Adicionar assemblies de até 2 MB

1. Em **Adicionar assembly**, insira um nome para o assembly. Manter **arquivo pequeno** selecionado. Ao lado da caixa **assembly** , escolha o ícone de pasta. Localize e selecione o assembly que você está carregando, por exemplo:

   ![Carregar um assembly menor](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   Na propriedade **nome do assembly** , o nome do arquivo do assembly aparece automaticamente depois que você seleciona o assembly.

1. Quando estiver pronto, escolha **OK**.

   Depois que o arquivo do assembly terminar o carregamento, o assembly aparecerá na lista de **assemblies** .

   ![Lista de assemblies carregados](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   Na página **visão geral** da sua conta de integração, em **componentes**, o bloco **assemblies** agora mostra o número de assemblies carregados, por exemplo:

   ![Assemblies carregados](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>Adicionar assemblies com mais de 2 MB

Para adicionar assemblies maiores, você pode carregar seu assembly em um contêiner de BLOBs do Azure em sua conta de armazenamento do Azure. Suas etapas para adicionar assemblies diferem se o seu contêiner de blob tem acesso de leitura público. Primeiro, verifique se seu contêiner de BLOBs tem acesso de leitura público seguindo estas etapas: [definir o nível de acesso público para o contêiner de blob](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Verificar nível de acesso do contêiner

1. Abra Gerenciador de Armazenamento do Azure. Na janela do Explorer, expanda sua assinatura do Azure, se ainda não estiver expandida.

1. Expanda **contas de armazenamento** > {*Your-Storage-Account*} > **contêineres de blob**. Selecione seu contêiner de BLOB.

1. No menu de atalho do contêiner de BLOB, selecione **definir nível de acesso público**.

   * Se o seu contêiner de blob tiver pelo menos acesso público, escolha **Cancelar**e siga estas etapas posteriormente nesta página: [carregar para contêineres com acesso público](#public-access-assemblies)

     ![Acesso público](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Se o seu contêiner de BLOB não tiver acesso público, escolha **Cancelar**e siga estas etapas posteriormente nesta página: [carregar para contêineres sem acesso público](#no-public-access-assemblies)

     ![Sem acesso público](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Carregar para contêineres com acesso público

1. Carregue o assembly em sua conta de armazenamento. 
   Na janela à direita, escolha **carregar**.

1. Depois de concluir o carregamento, selecione o assembly carregado. Na barra de ferramentas, escolha **Copiar URL** para copiar a URL do assembly.

1. Retorne para a portal do Azure em que o painel **Adicionar assembly** está aberto. 
   Insira um nome para o assembly. 
   Escolha **arquivo grande (mais de 2 MB)** .

   A caixa **Content URI** agora é exibida, em vez da caixa **assembly** .

1. Na caixa **URI de conteúdo** , Cole a URL do assembly. 
   Conclua a adição do assembly.

Depois que o assembly terminar de carregar, o esquema aparecerá na lista de **assemblies** .
Na página **visão geral** da sua conta de integração, em **componentes**, o bloco **assemblies** agora mostra o número de assemblies carregados.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Carregar para contêineres sem acesso público

1. Carregue o assembly em sua conta de armazenamento. 
   Na janela à direita, escolha **carregar**.

1. Depois de concluir o carregamento, gere uma SAS (assinatura de acesso compartilhado) para seu assembly. 
   No menu de atalho do assembly, selecione **obter assinatura de acesso compartilhado**.

1. No painel **assinatura de acesso compartilhado** , selecione **gerar URI de assinatura de acesso compartilhado em nível de contêiner** > **criar**. 
   Depois que a URL SAS é gerada, ao lado da caixa **URL** , escolha **copiar**.

1. Retorne para a portal do Azure em que o painel **Adicionar assembly** está aberto. 
   Insira um nome para o assembly. 
   Escolha **arquivo grande (mais de 2 MB)** .

   A caixa **Content URI** agora é exibida, em vez da caixa **assembly** .

1. Na caixa **URI de conteúdo** , Cole o URI de SAS que você gerou anteriormente. Conclua a adição do assembly.

Depois que o assembly terminar de carregar, o assembly aparecerá na lista de **esquemas** . Na página **visão geral** da sua conta de integração, em **componentes**, o bloco **assemblies** agora mostra o número de assemblies carregados.

## <a name="create-maps"></a>Criar mapas

Para criar um documento XSLT que você pode usar como um mapa, você pode usar o Visual Studio 2015 para criar um projeto de integração do BizTalk usando o [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md). Neste projeto, você pode criar um arquivo de mapa de integração, que permite que você mapeie visualmente os itens entre dois arquivos de esquema XML. Depois de criar esse projeto, você obtém um documento XSLT.
Para limites em quantidades de mapa em contas de integração, consulte [limites e configuração para aplicativos lógicos do Azure](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). 

## <a name="add-maps"></a>Adicionar mapas

Depois de carregar todos os assemblies que seu mapa referencia, agora você pode carregar seu mapa.

1. Se ainda não tiver entrado, entre no [portal do Azure](https://portal.azure.com) com suas credenciais de conta do Azure. 

1. Se sua conta de integração ainda não estiver aberta, no menu principal do Azure, selecione **todos os serviços**. 
   Na caixa de pesquisa, digite "conta de integração". 
   Selecione **contas de integração**.

   ![Localizar conta de integração](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Selecione a conta de integração na qual você deseja adicionar o mapa, por exemplo:

   ![Selecionar conta de integração](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Na página **visão geral** da sua conta de integração, em **componentes**, selecione o bloco **mapas** .

   ![Selecione "mapas"](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. Depois que a página **mapas** for aberta, escolha **Adicionar**.

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>Adicionar mapas de até 2 MB

1. Em **adicionar mapa**, insira um nome para o mapa. 

1. Em **tipo de mapa**, selecione o tipo, por exemplo **: Liquid**, **XSLT**, **XSLT 2,0**ou **XSLT 3,0**.

1. Manter **arquivo pequeno** selecionado. Ao lado da caixa **mapa** , escolha o ícone de pasta. Localize e selecione o mapa que você está carregando, por exemplo:

   ![Carregar mapa](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   Se você saiu da propriedade **Name** vazia, o nome do arquivo do mapa aparecerá automaticamente na propriedade automaticamente depois que você selecionar o arquivo de mapa. 
   No entanto, você pode usar qualquer nome exclusivo.

1. Quando estiver pronto, escolha **OK**. 
   Depois que o arquivo de mapa terminar de carregar, o mapa aparecerá na lista **mapas** .

   ![Lista de mapas carregados](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   Na página **visão geral** da sua conta de integração, em **componentes**, o bloco **mapas** agora mostra o número de mapas carregados, por exemplo:

   ![Mapas carregados](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>Adicionar mapas mais de 2 MB

Atualmente, para adicionar mapas maiores, use os [mapas da API REST dos aplicativos lógicos do Azure](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

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

## <a name="edit-maps"></a>Editar mapas

Para atualizar um mapa existente, você precisa carregar um novo arquivo de mapa que tenha as alterações desejadas. No entanto, você pode primeiro baixar o mapa existente para edição.

1. No [portal do Azure](https://portal.azure.com), localize e abra sua conta de integração, se ainda não estiver aberta.

1. No menu principal do Azure, selecione **todos os serviços**. Na caixa de pesquisa, digite "conta de integração". Selecione **contas de integração**.

1. Selecione a conta de integração na qual você deseja atualizar o mapa.

1. Na página **visão geral** da sua conta de integração, em **componentes**, selecione o bloco **mapas** .

1. Depois que a página **mapas** for aberta, selecione o mapa. 
   Para baixar e editar o mapa primeiro, escolha **baixar**e salve o mapa.

1. Quando estiver pronto para carregar o mapa atualizado, na página **mapas** , selecione o mapa que você deseja atualizar e escolha **Atualizar**.

1. Localize e selecione o mapa atualizado que você deseja carregar. 
   Depois que o arquivo de mapa terminar de carregar, o mapa atualizado aparecerá na lista **mapas** .

## <a name="delete-maps"></a>Excluir mapas

1. No [portal do Azure](https://portal.azure.com), localize e abra sua conta de integração, se ainda não estiver aberta.

1. No menu principal do Azure, selecione **todos os serviços**. 
   Na caixa de pesquisa, digite "conta de integração". 
   Selecione **contas de integração**.

1. Selecione a conta de integração na qual você deseja excluir o mapa.

1. Na página **visão geral** da sua conta de integração, em **componentes**, selecione o bloco **mapas** .

1. Depois que a página **mapas** for aberta, selecione o mapa e escolha **excluir**.

1. Para confirmar que deseja excluir o mapa, escolha **Sim**.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [Saiba mais sobre esquemas](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [Saiba mais sobre transformações](../logic-apps/logic-apps-enterprise-integration-transform.md)
