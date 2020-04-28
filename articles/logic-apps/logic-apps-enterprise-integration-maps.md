---
title: Transforme xml com mapas XSLT
description: Adicione mapas XSLT para transformar XML em Aplicações Lógicas Azure com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: e186b9713c8464f8f37e1e0bf112c4118621925c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75979412"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>Transformar XML com mapas em Azure Logic Apps com Enterprise Integration Pack

Para transferir dados XML entre formatos para cenários de integração empresarial em Aplicações Lógicas Azure, a sua aplicação lógica pode usar mapas, ou mais especificamente, mapas de transformações linguísticas de estilo extensível (XSLT). Um mapa é um documento XML que descreve como converter dados de um documento XML em outro formato. 

Por exemplo, suponha que receba regularmente encomendas B2B ou faturas de um cliente que utiliza o formato de data YYYMMDD. No entanto, a sua organização utiliza o formato de data MMDDYYY. Pode definir e utilizar um mapa que transforme o formato de data YYYMMDD para o formato MMDDYYY antes de armazenar os detalhes da encomenda ou fatura na sua base de dados de atividade do cliente.

Para limites relacionados com contas de integração e artefactos como mapas, consulte [Limites e informações de configuração para Aplicações Lógicas Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) onde armazena os seus mapas e outros artefactos para a integração empresarial e soluções business-to-business (B2B).

* Se o seu mapa refere uma montagem externa, tem de carregar *tanto o conjunto como o mapa* para a sua conta de integração. Certifique-se de [*que faz o upload da sua montagem primeiro*](#add-assembly)e, em seguida, faça upload do mapa que faz referência à montagem.

  Se o seu conjunto for de 2 MB ou menor, pode adicionar a sua montagem à sua conta de integração *diretamente* do portal Azure. No entanto, se o seu conjunto ou mapa for maior do que 2 MB, mas não maior do que o limite de [tamanho para conjuntos ou mapas,](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)tem estas opções:

  * Para conjuntos, precisa de um recipiente de blob Azure onde possa fazer upload da sua montagem e da localização do recipiente. Dessa forma, pode fornecer esse local mais tarde quando adicionar a montagem à sua conta de integração. 
  Para esta tarefa, precisa destes itens:

    | Item | Descrição |
    |------|-------------|
    | [Conta de armazenamento azure](../storage/common/storage-account-overview.md) | Nesta conta, crie um recipiente de blob Azure para a sua montagem. Aprenda a criar uma conta de [armazenamento.](../storage/common/storage-account-create.md) |
    | Contentor de blobs | Neste recipiente, pode fazer o upload da sua montagem. Também precisa da localização deste contentor quando adicionar o conjunto à sua conta de integração. Aprenda a [criar um recipiente de bolhas.](../storage/blobs/storage-quickstart-blobs-portal.md) |
    | [Explorador do Storage do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Esta ferramenta ajuda-o a gerir mais facilmente as contas de armazenamento e os recipientes blob. Para utilizar o Storage Explorer, [descarregue e instale o Azure Storage Explorer.](https://www.storageexplorer.com/) Em seguida, ligue o Storage Explorer à sua conta de armazenamento seguindo os passos em [Get started with Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). Para saber mais, consulte [Quickstart: Crie uma bolha no armazenamento de objetos com o Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md). <p>Ou, no portal Azure, encontre e selecione a sua conta de armazenamento. A partir do menu da sua conta de armazenamento, selecione **Storage Explorer**. |
    |||

  * Para mapas, você pode atualmente adicionar mapas maiores usando as [aplicações lógicas azure REST API - Maps](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

Não é preciso uma aplicação lógica para criar e adicionar mapas. No entanto, para utilizar um mapa, a sua aplicação lógica precisa de ser ligada a uma conta de integração onde armazena esse mapa. Saiba [como ligar aplicações lógicas a contas de integração.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) Se ainda não tem uma aplicação lógica, aprenda [a criar aplicações lógicas.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies"></a>Adicionar conjuntos referenciados

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure.

1. Para encontrar e abrir a sua conta de integração, no menu principal do Azure, selecione **Todos os serviços.** 
   Na caixa de pesquisa, insira a "conta de integração". 
   Selecione **contas de Integração.**

   ![Encontre conta de integração](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Selecione a conta de integração onde pretende adicionar o seu conjunto, por exemplo:

   ![Selecione conta de integração](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Na página de **visão geral** da sua conta de integração, em **Componentes,** selecione o azulejo **Assemblies.**

   ![Selecione "Assembléias"](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. Depois da página **Assemblies** abrir, escolha **Adicionar**.

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

Com base no tamanho do seu ficheiro de montagem, siga os passos para o upload de um conjunto que [seja até 2 MB](#smaller-assembly) ou superior a 2 [MB, mas apenas até 8 MB](#larger-assembly).
Para limites nas quantidades de montagem nas contas de integração, consulte [Limites e configuração para Aplicações Lógicas Azure](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits).

> [!NOTE]
> Se alterar a sua montagem, também deve atualizar o seu mapa se o mapa tem alterações ou não.

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Adicione conjuntos até 2 MB

1. Em **Conjunto Add,** insira um nome para a sua montagem. Mantenha **o ficheiro Small** selecionado. Ao lado da caixa **de Montagem,** escolha o ícone da pasta. Encontre e selecione o conjunto que está a carregar, por exemplo:

   ![Carregar montagem mais pequena](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   Na propriedade **'Nome da Montagem',** o nome do ficheiro da assembléia aparece automaticamente depois de selecionar o conjunto.

1. Quando estiver pronto, escolha **OK.**

   Depois de o seu ficheiro de montagem terminar o upload, a montagem aparece na lista de **Assembles.**

   ![Lista de conjuntos carregados](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   Na página de **visão geral** da sua conta de integração, em **Componentes,** o azulejo **assembléia** mostra agora o número de conjuntos carregados, por exemplo:

   ![Conjuntos carregados](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>Adicione conjuntos superiores a 2 MB

Para adicionar conjuntos maiores, pode fazer o upload da sua montagem para um recipiente de blob Azure na sua conta de armazenamento Azure. Os seus passos para adicionar conjuntos diferem se o seu recipiente blob tem acesso de leitura pública. Em primeiro lugar, verifique se o seu recipiente de blob tem ou não acesso ao público seguindo estas etapas: [Definir nível](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container) de acesso público para recipiente de bolhas

#### <a name="check-container-access-level"></a>Verifique o nível de acesso do contentor

1. Open Azure Storage Explorer. Na janela Explorer, expanda a sua subscrição Azure se ainda não estiver expandida.

1. Expandir contas de **armazenamento** > { a sua conta*de armazenamento*} > **recipientes Blob**. Selecione o seu recipiente de bolhas.

1. A partir do menu de atalho do seu recipiente blob, selecione **Definir Nível**de Acesso Público .

   * Se o seu recipiente blob tiver pelo menos acesso público, escolha **Cancelar**, e siga estes passos mais tarde nesta página: [Faça upload para contentores com acesso público](#public-access-assemblies)

     ![Acesso público](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Se o seu recipiente de blob não tiver acesso público, escolha **Cancelar**, e siga estes passos mais tarde nesta página: [Faça upload para contentores sem acesso público](#no-public-access-assemblies)

     ![Sem acesso público](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Upload para contentores com acesso público

1. Faça o upload do conjunto para a sua conta de armazenamento. 
   Na janela da direita, escolha **Carregar**.

1. Depois de terminar o upload, selecione o seu conjunto carregado. Na barra de ferramentas, escolha **copy URL** para que copie o URL do conjunto.

1. Volte ao portal Azure onde está aberto o painel **Add Assembly.** 
   Insira um nome para a sua montagem. 
   Escolha **ficheiro Grande (superior**a 2 MB) .

   A caixa **Content URI** aparece agora, em vez da caixa de **Montagem.**

1. Na caixa **Content URI,** colhe o URL da sua montagem. 
   Termine de adicionar a sua montagem.

Depois de a sua montagem terminar o upload, o esquema aparece na lista de **Assembles.**
Na página de **visão geral** da sua conta de integração, em **Componentes,** o azulejo **assembléia** mostra agora o número de conjuntos carregados.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Upload para contentores sem acesso público

1. Faça o upload do conjunto para a sua conta de armazenamento. 
   Na janela da direita, escolha **Carregar**.

1. Depois de terminar o upload, gere uma assinatura de acesso partilhado (SAS) para a sua montagem. 
   A partir do menu de atalho da sua assembléia, selecione Obter Assinatura de **Acesso Partilhado**.

1. No painel Signature de **Acesso Partilhado,** selecione Generate a > assinatura de acesso partilhado uri**Criar**em **nível de recipiente**. 
   Depois de gerado o URL SAS, junto à caixa **URL,** escolha **Copiar**.

1. Volte ao portal Azure onde está aberto o painel **Add Assembly.** 
   Insira um nome para a sua montagem. 
   Escolha **ficheiro Grande (superior**a 2 MB) .

   A caixa **Content URI** aparece agora, em vez da caixa de **Montagem.**

1. Na caixa **Content URI,** cola o SAS URI que gerou anteriormente. Termine de adicionar a sua montagem.

Depois de a montagem terminar o upload, a montagem aparece na lista **Schemas.** Na página de **visão geral** da sua conta de integração, em **Componentes,** o azulejo **assembléia** mostra agora o número de conjuntos carregados.

## <a name="create-maps"></a>Criar mapas

Para criar um documento XSLT que pode usar como mapa, pode utilizar o Visual Studio 2015 para criar um projeto bizTalk Integration utilizando o [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md). Neste projeto, pode construir um ficheiro de mapa de integração, que permite mapear visualmente itens entre dois ficheiros de esquemas XML. Depois de construir este projeto, obtém um documento XSLT.
Para limites nas quantidades de mapas nas contas de integração, consulte [Limites e configuração para Aplicações Lógicas Azure](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). 

## <a name="add-maps"></a>Adicionar mapas

Depois de fazer o upload de quaisquer conjuntos que o seu mapa referências, agora pode fazer o upload do seu mapa.

1. Se ainda não assinou, inscreva-se no [portal Azure](https://portal.azure.com) com as credenciais da sua conta Azure. 

1. Se a sua conta de integração ainda não estiver aberta, no menu principal do Azure, selecione **Todos os serviços.** 
   Na caixa de pesquisa, insira a "conta de integração". 
   Selecione **contas de Integração.**

   ![Encontre conta de integração](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Selecione a conta de integração onde pretende adicionar o seu mapa, por exemplo:

   ![Selecione conta de integração](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Na página de **visão geral** da sua conta de integração, em **Componentes,** selecione o azulejo **Maps.**

   ![Selecione "Maps"](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. Depois da página do **Maps** abrir, escolha **Adicionar**.

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>Adicione mapas até 2 MB

1. Em **Adicionar Mapa,** introduza um nome para o seu mapa. 

1. No **tipo de mapa,** selecione o tipo, por exemplo: **Líquido,** **XSLT,** **XSLT 2.0**, ou **XSLT 3.0**.

1. Mantenha **o ficheiro Small** selecionado. Ao lado da caixa **do Mapa,** escolha o ícone da pasta. Encontre e selecione o mapa que está a enviar, por exemplo:

   ![Mapa de upload](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   Se deixou a propriedade **Nome** vazia, o nome de ficheiro do mapa aparece automaticamente nessa propriedade depois de selecionar o ficheiro do mapa. 
   No entanto, pode usar qualquer nome único.

1. Quando estiver pronto, escolha **OK.** 
   Depois de o ficheiro do mapa terminar o upload, o mapa aparece na lista do **Maps.**

   ![Lista de mapas carregados](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   Na página de **visão geral** da sua conta de integração, em **Componentes,** o azulejo **maps** mostra agora o número de mapas carregados, por exemplo:

   ![Mapas carregados](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>Adicione mapas mais de 2 MB

Atualmente, para adicionar mapas maiores, use as [Aplicações Lógicas Azure REST API - Maps](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

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

Para atualizar um mapa existente, tem de carregar um novo ficheiro de mapas que tenha as alterações que deseja. No entanto, pode primeiro descarregar o mapa existente para edição.

1. No [portal Azure,](https://portal.azure.com)encontre e abra a sua conta de integração, se ainda não estiver aberta.

1. No menu Azure principal, selecione **Todos os serviços.** Na caixa de pesquisa, insira a "conta de integração". Selecione **contas de Integração.**

1. Selecione a conta de integração onde pretende atualizar o seu mapa.

1. Na página de **visão geral** da sua conta de integração, em **Componentes,** selecione o azulejo **Maps.**

1. Depois da página do **Maps** abrir, selecione o seu mapa. 
   Para descarregar e editar primeiro o mapa, escolha **O Download**e guarde o mapa.

1. Quando estiver pronto para fazer o upload do mapa atualizado, na página do **Maps,** selecione o mapa que pretende atualizar e escolha **Atualizar**.

1. Encontre e selecione o mapa atualizado que pretende carregar. 
   Depois de o ficheiro do mapa terminar o upload, o mapa atualizado aparece na lista do **Maps.**

## <a name="delete-maps"></a>Eliminar mapas

1. No [portal Azure,](https://portal.azure.com)encontre e abra a sua conta de integração, se ainda não estiver aberta.

1. No menu Azure principal, selecione **Todos os serviços.** 
   Na caixa de pesquisa, insira a "conta de integração". 
   Selecione **contas de Integração.**

1. Selecione a conta de integração onde pretende apagar o seu mapa.

1. Na página de **visão geral** da sua conta de integração, em **Componentes,** selecione o azulejo **Maps.**

1. Depois da página **do Maps** abrir, selecione o seu mapa e escolha **Eliminar**.

1. Para confirmar se pretende eliminar o mapa, escolha **Sim**.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o Pacote de Integração Empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [Saiba mais sobre os schemas](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [Saiba mais sobre transformações](../logic-apps/logic-apps-enterprise-integration-transform.md)
