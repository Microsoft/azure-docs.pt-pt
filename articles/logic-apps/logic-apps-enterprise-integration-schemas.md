---
title: Validar XML com esquemas
description: Adicionar esquemas para validar documentos XML em aplicativos lógicos do Azure com Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: 6cde620b4949da8a6cff4ad89a863c80f0514f1c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792399"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>Validar XML com esquemas em aplicativos lógicos do Azure com Enterprise Integration Pack

Para verificar se os documentos usam um XML válido e ter os dados esperados no formato predefinido para cenários de integração corporativa em aplicativos lógicos do Azure, seu aplicativo lógico pode usar esquemas. Um esquema também pode validar mensagens que os aplicativos lógicos trocam em cenários B2B (entre empresas).

Para os limites relacionados a contas de integração e artefatos como esquemas, consulte [limites e informações de configuração para aplicativos lógicos do Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se numa conta do Azure gratuita</a>.

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) em que você armazena seus esquemas e outros artefatos para integração corporativa e Soluções B2B (entre empresas). 

  Se o esquema for de [2 MB ou menor](#smaller-schema), você poderá adicionar o esquema à sua conta de integração diretamente do portal do Azure. No entanto, se o esquema for maior que 2 MB, mas não maior que o [limite de tamanho do esquema](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), você poderá carregar seu esquema para uma conta de armazenamento do Azure. 
  Para adicionar esse esquema à sua conta de integração, você pode vincular à sua conta de armazenamento de sua conta de integração. 
  Para esta tarefa, aqui estão os itens de que você precisa: 

  * [Conta de armazenamento do Azure](../storage/common/storage-account-overview.md) em que você cria um contêiner de BLOB para seu esquema. Saiba como [criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md). 

  * Contêiner de BLOB para armazenar seu esquema. Saiba como [criar um contêiner de BLOBs](../storage/blobs/storage-quickstart-blobs-portal.md). 
  Você precisará do URI de conteúdo do contêiner mais tarde quando adicionar o esquema à sua conta de integração.

  * [Gerenciador de armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md), que você pode usar para gerenciar contas de armazenamento e contêineres de BLOB. 
  Para usar Gerenciador de Armazenamento, escolha uma das opções aqui:
  
    * Na portal do Azure, localize e selecione sua conta de armazenamento. 
    No menu da sua conta de armazenamento, selecione **Gerenciador de armazenamento**.

    * Para a versão da área de trabalho, [Baixe e instale o Gerenciador de armazenamento do Azure](https://www.storageexplorer.com/). 
    Em seguida, conecte Gerenciador de Armazenamento à sua conta de armazenamento seguindo as etapas em introdução [ao Gerenciador de armazenamento](../vs-azure-tools-storage-manage-with-storage-explorer.md). 
    Para saber mais, consulte [início rápido: criar um blob no armazenamento de objetos com o Gerenciador de armazenamento do Azure](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

Você não precisa de um aplicativo lógico ao criar e adicionar esquemas. No entanto, para usar um esquema, seu aplicativo lógico precisa vincular a uma conta de integração em que você armazena esse esquema. Saiba [como vincular aplicativos lógicos a contas de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Se você ainda não tiver um aplicativo lógico, saiba [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-schemas"></a>Adicionar esquemas

1. Inicie sessão no <a href="https://portal.azure.com" target="_blank">portal do Azure</a> com as credenciais da sua conta do Azure.

1. Para localizar e abrir sua conta de integração, no menu principal do Azure, selecione **todos os serviços**. Na caixa de pesquisa, digite "conta de integração". Selecione **contas de integração**.

   ![Localizar conta de integração](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. Selecione a conta de integração à qual você deseja adicionar o esquema, por exemplo:

   ![Selecionar conta de integração](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. Na página **visão geral** da sua conta de integração, em **componentes**, selecione o bloco **esquemas** .

   ![Selecione "esquemas"](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. Depois que a página **esquemas** for aberta, escolha **Adicionar**.

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

Com base no tamanho do arquivo de esquema (. xsd), siga as etapas para carregar um esquema que seja de [até 2 MB](#smaller-schema) ou [mais de 2 MB, até 8 MB](#larger-schema).

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Adicionar esquemas de até 2 MB

1. Em **Adicionar esquema**, insira um nome para o esquema. 
   Manter **arquivo pequeno** selecionado. Ao lado da caixa **esquema** , escolha o ícone de pasta. Localize e selecione o esquema que você está carregando, por exemplo:

   ![Carregar esquema menor](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. Quando estiver pronto, escolha **OK**.

   Depois que o esquema terminar de carregar, o esquema aparecerá na lista **esquemas** .

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>Adicionar esquemas mais de 2 MB

Para adicionar esquemas maiores, você pode carregar seu esquema em um contêiner de BLOBs do Azure em sua conta de armazenamento do Azure. Suas etapas para adicionar esquemas diferem se o seu contêiner de blob tem acesso de leitura público. Primeiro, verifique se seu contêiner de BLOBs tem acesso de leitura público seguindo estas etapas: [definir o nível de acesso público para o contêiner de blob](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Verificar nível de acesso do contêiner

1. Abra Gerenciador de Armazenamento do Azure. Na janela do Explorer, expanda sua assinatura do Azure, se ainda não estiver expandida.

1. Expanda **contas de armazenamento** > {*Your-Storage-Account*} > **contêineres de blob**. Selecione seu contêiner de BLOB.

1. No menu de atalho do contêiner de BLOB, selecione **definir nível de acesso público**.

   * Se o seu contêiner de blob tiver pelo menos acesso público, escolha **Cancelar**e siga estas etapas posteriormente nesta página: [carregar para contêineres com acesso público](#public-access)

     ![Acesso público](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Se o seu contêiner de BLOB não tiver acesso público, escolha **Cancelar**e siga estas etapas posteriormente nesta página: [carregar para contêineres sem acesso público](#public-access)

     ![Sem acesso público](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Carregar para contêineres com acesso público

1. Carregue o esquema para sua conta de armazenamento. 
   Na janela à direita, escolha **carregar**.

1. Depois de concluir o carregamento, selecione o esquema carregado. Na barra de ferramentas, escolha **Copiar URL** para copiar a URL do esquema.

1. Retorne para a portal do Azure em que o painel **Adicionar esquema** está aberto. 
   Insira um nome para o assembly. 
   Escolha **arquivo grande (mais de 2 MB)** . 

   A caixa **Content URI** agora é exibida, em vez da caixa **Schema** .

1. Na caixa **URI de conteúdo** , Cole a URL do esquema. 
   Conclua a adição de seu esquema.

Depois que o esquema terminar de carregar, o esquema aparecerá na lista **esquemas** . Na página **visão geral** da sua conta de integração, em **componentes**, o bloco **esquemas** agora mostra o número de esquemas carregados.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Carregar para contêineres sem acesso público

1. Carregue o esquema para sua conta de armazenamento. 
   Na janela à direita, escolha **carregar**.

1. Depois de concluir o carregamento, gere uma SAS (assinatura de acesso compartilhado) para seu esquema. 
   No menu de atalho do esquema, selecione **obter assinatura de acesso compartilhado**.

1. No painel **assinatura de acesso compartilhado** , selecione **gerar URI de assinatura de acesso compartilhado em nível de contêiner** > **criar**. 
   Depois que a URL SAS é gerada, ao lado da caixa **URL** , escolha **copiar**.

1. Retorne para a portal do Azure em que o painel **Adicionar esquema** está aberto. Escolha **arquivo grande**.

   A caixa **Content URI** agora é exibida, em vez da caixa **Schema** .

1. Na caixa **URI de conteúdo** , Cole o URI de SAS que você gerou anteriormente. Conclua a adição de seu esquema.

Depois que o esquema terminar de carregar, o esquema aparecerá na lista **esquemas** . Na página **visão geral** da sua conta de integração, em **componentes**, o bloco **esquemas** agora mostra o número de esquemas carregados.

## <a name="edit-schemas"></a>Editar esquemas

Para atualizar um esquema existente, você precisa carregar um novo arquivo de esquema que tenha as alterações desejadas. No entanto, você pode primeiro baixar o esquema existente para edição.

1. No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, localize e abra sua conta de integração, se ainda não estiver aberta.

1. No menu principal do Azure, selecione **todos os serviços**. 
   Na caixa de pesquisa, digite "conta de integração". 
   Selecione **contas de integração**.

1. Selecione a conta de integração na qual você deseja atualizar o esquema.

1. Na página **visão geral** da sua conta de integração, em **componentes**, selecione o bloco **esquemas** .

1. Depois que a página **esquemas** for aberta, selecione o esquema. 
   Para baixar e editar o esquema primeiro, escolha **baixar**e salve o esquema.

1. Quando estiver pronto para carregar o esquema atualizado, na página **esquemas** , selecione o esquema que você deseja atualizar e escolha **Atualizar**.

1. Localize e selecione o esquema atualizado que você deseja carregar. 
   Depois que o arquivo de esquema terminar de carregar, o esquema atualizado aparecerá na lista **esquemas** .

## <a name="delete-schemas"></a>Excluir esquemas

1. No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, localize e abra sua conta de integração, se ainda não estiver aberta.

1. No menu principal do Azure, selecione **todos os serviços**. 
   Na caixa de pesquisa, digite "conta de integração". 
   Selecione **contas de integração**.

1. Selecione a conta de integração na qual você deseja excluir o esquema.

1. Na página **visão geral** da sua conta de integração, em **componentes**, selecione o bloco **esquemas** .

1. Depois que a página **esquemas** for aberta, selecione o esquema e escolha **excluir**.

1. Para confirmar que deseja excluir o esquema, escolha **Sim**.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
* [Saiba mais sobre mapas](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Saiba mais sobre transformações](../logic-apps/logic-apps-enterprise-integration-transform.md)
