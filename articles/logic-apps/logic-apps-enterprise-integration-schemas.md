---
title: Validar XML com esquemas - Azure Logic Apps | Documentos da Microsoft
description: Adicionar esquemas para validar os documentos XML no Azure Logic Apps com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.date: 02/06/2019
ms.openlocfilehash: 3cca995b353b88cc481cbda68df4211a724f7f09
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57727319"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>Validar XML com esquemas no Azure Logic Apps com o Enterprise Integration Pack

Para verificar que os documentos utilizam XML válido e tem os dados esperados no formato predefinido para cenários de integração do enterprise no Azure Logic Apps, a aplicação lógica pode utilizar esquemas. Um esquema também pode validar as mensagens que aplicações lógicas do exchange em cenários de empresa-empresa (B2B).

Para limites relacionados com contas de integração e artefactos, tais como esquemas, consulte [limites e informações de configuração para o Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se numa conta do Azure gratuita</a>.

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) onde armazenar seus esquemas e outros artefatos para soluções de empresa-empresa (B2B) e de integração empresarial. 

  Se o esquema seja [2 MB ou mais pequeno](#smaller-schema), pode adicionar o seu esquema para a sua conta de integração diretamente a partir do portal do Azure. No entanto, se o esquema seja maior do que 2 MB, mas não maior do que o [limite de tamanho do esquema](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), pode carregar seu esquema para uma conta de armazenamento do Azure. 
  Para adicionar esse esquema para a sua conta de integração, em seguida, pode ligar à sua conta de armazenamento da sua conta de integração. 
  Para essa tarefa, aqui estão os itens que precisa: 

  * [Conta de armazenamento do Azure](../storage/common/storage-account-overview.md) onde criar um contentor de BLOBs para seu esquema. Saiba como [criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md). 

  * Contentor de BLOBs para armazenar seu esquema. Saiba como [criar um contentor de BLOBs](../storage/blobs/storage-quickstart-blobs-portal.md). 
  Terá de URI de conteúdo do seu contentor mais tarde quando adicionar o esquema para a sua conta de integração.

  * [Explorador de armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md), que pode utilizar para gerir contas de armazenamento e contentores de Blobs. 
  Para utilizar o Explorador de armazenamento, escolha uma das opções aqui:
  
    * No portal do Azure, localize e selecione a sua conta de armazenamento. 
    No seu menu de conta de armazenamento, selecione **Explorador de armazenamento**.

    * Para a versão de área de trabalho, [baixe e instale o Explorador de armazenamento do Azure](https://www.storageexplorer.com/). 
    Em seguida, ligar o Explorador de armazenamento à sua conta de armazenamento ao seguir os passos no [introdução ao Explorador de armazenamento](../vs-azure-tools-storage-manage-with-storage-explorer.md). 
    Para obter mais informações, consulte [início rápido: Criar um blob no armazenamento de objetos com o Explorador de armazenamento do Azure](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

Não precisa de uma aplicação lógica quando criar e adicioná-los. No entanto, para utilizar um esquema, a sua aplicação lógica precisa criar uma ligação para uma conta de integração onde armazenar esse esquema. Saiba mais [como ligar aplicações lógicas para contas de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Se ainda não tiver uma aplicação lógica, saiba [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-schemas"></a>Adicionar esquemas

1. Inicie sessão no <a href="https://portal.azure.com" target="_blank">portal do Azure</a> com as credenciais da sua conta do Azure.

1. Para localizar e abra a sua conta de integração, no menu principal do Azure, selecione **todos os serviços**. Na caixa de pesquisa, introduza "conta de integração". Selecione **contas de integração**.

   ![Localizar a conta de integração](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. Selecione a conta de integração em que pretende adicionar o seu esquema, por exemplo:

   ![Selecione a conta de integração](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. Na sua conta de integração **descrição geral** página, em **componentes**, selecione o **esquemas** mosaico.

   ![Selecione "Esquemas"](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. Depois do **esquemas** é aberta a página, escolha **Add**.

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

Com base no tamanho do seu ficheiro de esquema (. xsd), siga os passos para carregar um esquema que é [até 2 MB](#smaller-schema) ou [mais de 2 MB, 8 MB](#larger-schema).

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Adicionar esquemas até 2 MB

1. Sob **Adicionar esquema**, introduza um nome para o seu esquema. 
   Manter **pequeno arquivo** selecionado. Junto a **esquema** caixa, escolha o ícone de pasta. Localize e selecione o esquema que está a carregar, por exemplo:

   ![Carregar o esquema menor](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. Quando estiver pronto, escolha **OK**.

   Depois de concluir o seu esquema de carregamento, o esquema aparece no **esquemas** lista.

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>Adicionar esquemas com mais de 2 MB

Adicionar esquemas maior, pode carregar seu esquema para um contentor de Blobs do Azure na sua conta de armazenamento do Azure. Os passos para adicioná-los divergir com base se o contentor de BLOBs tem acesso de leitura público. Primeiro, verifique se é ou não o seu contentor de BLOBs tem acesso de leitura público através dos seguintes passos: [Definir o nível de acesso público para o contentor de BLOBs](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Verifique o nível de acesso do contentor

1. Abra o Explorador de armazenamento do Azure. Na janela do Explorer, expanda a sua subscrição do Azure se não estiver expandida.

1. Expanda **contas de armazenamento** > {*conta de armazenamento your*} > **contentores de BLOBs**. Selecione o contentor de Blobs.

1. No menu de atalho do seu contentor de BLOBs, selecione **defina o nível de acesso público**.

   * Se o contentor de BLOBs tem acesso, pelo menos, público, escolha **Cancelar**e siga estes passos mais adiante nesta página: [Carregar para contentores com o acesso público](#public-access)

     ![Acesso público](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Se o contentor de BLOBs não tiver acesso público, escolha **Cancelar**e siga estes passos mais adiante nesta página: [Carregar para contentores sem acesso público](#public-access)

     ![Sem acesso público](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Carregar para contentores com o acesso público

1. Carregar o esquema para a conta de armazenamento. 
   Na janela do lado direita, escolha **carregar**.

1. Depois de concluir o carregamento, selecione o seu esquema carregada. Na barra de ferramentas, escolha **URL de cópia** para que copie de URL o esquema.

1. Regresse ao portal do Azure onde o **Adicionar esquema** painel é aberto. 
   Introduza um nome para seu assembly. 
   Escolher **ficheiros grandes (superiores a 2 MB)**. 

   O **URI de conteúdo** agora é apresentada a caixa, em vez de **esquema** caixa.

1. Na **URI de conteúdo** caixa, cole o URL do seu esquema. 
   Conclua a adição de seu esquema.

Depois de concluir o seu esquema de carregamento, o esquema aparece no **esquemas** lista. Na sua conta de integração **descrição geral** página, em **componentes**, o **esquemas** mosaico mostra agora o número de esquemas carregados.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Carregar para contentores sem acesso público

1. Carregar o esquema para a conta de armazenamento. 
   Na janela do lado direita, escolha **carregar**.

1. Depois de concluir o carregamento, gere uma assinatura de acesso partilhado (SAS) para o seu esquema. 
   No menu de atalho do seu esquema, selecione **obter assinatura de acesso partilhado**.

1. Na **assinatura de acesso partilhado** painel, selecione **URI de assinatura de acesso partilhado do nível de contêiner de gerar** > **criar**. 
   Depois do URL de SAS é gerado, junto a **URL** caixa, escolha **cópia**.

1. Regresse ao portal do Azure onde o **Adicionar esquema** painel é aberto. Escolher **ficheiros grandes**.

   O **URI de conteúdo** agora é apresentada a caixa, em vez de **esquema** caixa.

1. Na **URI de conteúdo** caixa, cole o URI de SAS gerado anteriormente. Conclua a adição de seu esquema.

Depois de concluir o seu esquema de carregamento, o esquema aparece no **esquemas** lista. Na sua conta de integração **descrição geral** página, em **componentes**, o **esquemas** mosaico mostra agora o número de esquemas carregados.

## <a name="edit-schemas"></a>Editar esquemas

Para atualizar um esquema existente, terá de carregar um novo arquivo de esquema que tenha as alterações pretendidas. No entanto, primeiro pode baixar o esquema existente para edição.

1. Na <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, localize e abra a sua conta de integração, se não estiver já abrir.

1. No menu principal do Azure, selecione **todos os serviços**. 
   Na caixa de pesquisa, introduza "conta de integração". 
   Selecione **contas de integração**.

1. Selecione a conta de integração em que pretende atualizar o seu esquema.

1. Na sua conta de integração **descrição geral** página, em **componentes**, selecione o **esquemas** mosaico.

1. Depois do **esquemas** é aberta a página, selecione o seu esquema. 
   Para transferir e editar o esquema em primeiro lugar, escolha **transferir**e guarde o esquema.

1. Quando estiver pronto para carregar o esquema atualizado, sobre o **esquemas** , selecione o esquema que pretende atualizar e escolha **atualizar**.

1. Localize e selecione o esquema atualizado que pretende carregar. 
   Depois do ficheiro de esquema concluir o carregamento, o esquema atualizado é apresentado na **esquemas** lista.

## <a name="delete-schemas"></a>Eliminar esquemas

1. Na <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, localize e abra a sua conta de integração, se não estiver já abrir.

1. No menu principal do Azure, selecione **todos os serviços**. 
   Na caixa de pesquisa, introduza "conta de integração". 
   Selecione **contas de integração**.

1. Selecione a conta de integração em que pretende eliminar o seu esquema.

1. Na sua conta de integração **descrição geral** página, em **componentes**, selecione o **esquemas** mosaico.

1. Depois do **esquemas** é aberta a página, selecione o seu esquema e escolha **eliminar**.

1. Para confirmar que pretende eliminar o esquema, escolha **Sim**.

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
* [Saiba mais sobre o maps](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Saiba mais sobre transformações](../logic-apps/logic-apps-enterprise-integration-transform.md)
