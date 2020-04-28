---
title: Validar XML com esquemmas
description: Adicione schemas para validar documentos XML em Aplicações Lógicas Azure com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: ade29318fdd1510d03ddd208534258a30b256e98
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75979368"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>Validar XML com esquemas em Azure Logic Apps com Enterprise Integration Pack

Para verificar se os documentos utilizam o XML válido e têm os dados esperados no formato predefinido para cenários de integração empresarial em Aplicações Lógicas Azure, a sua aplicação lógica pode usar schemas. Um esquema também pode validar mensagens que as aplicações lógicas trocam em cenários de negócio-a-negócio (B2B).

Para limites relacionados com contas de integração e artefactos como schemas, consulte [Limites e informações de configuração para Aplicações Lógicas Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se numa conta do Azure gratuita</a>.

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) onde armazena os seus schemas e outros artefactos para a integração empresarial e soluções business-to-business (B2B). 

  Se o seu esquema for [de 2 MB ou menor,](#smaller-schema)pode adicionar o seu esquema à sua conta de integração diretamente do portal Azure. No entanto, se o seu esquema for maior que 2 MB mas não maior do que o limite de [tamanho do esquema,](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)pode enviar o seu esquema para uma conta de armazenamento Azure. 
  Para adicionar esse esquema à sua conta de integração, pode então ligar-se à sua conta de armazenamento a partir da sua conta de integração. 
  Para esta tarefa, aqui estão os itens que precisa: 

  * [Conta de armazenamento azure](../storage/common/storage-account-overview.md) onde você cria um recipiente de bolhas para o seu esquema. Aprenda a criar uma conta de [armazenamento.](../storage/common/storage-account-create.md) 

  * Recipiente de bolhas para armazenar o seu esquema. Aprenda a [criar um recipiente de bolhas.](../storage/blobs/storage-quickstart-blobs-portal.md) 
  Precisa do conteúdo do seu contentor URI mais tarde quando adicionar o esquema à sua conta de integração.

  * [Azure Storage Explorer,](../vs-azure-tools-storage-manage-with-storage-explorer.md)que pode utilizar para gerir contas de armazenamento e contentores blob. 
  Para utilizar o Storage Explorer, escolha qualquer uma das opções aqui:
  
    * No portal Azure, encontre e selecione a sua conta de armazenamento. 
    A partir do menu da sua conta de armazenamento, selecione **Storage Explorer**.

    * Para a versão desktop, [descarregue e instale o Azure Storage Explorer.](https://www.storageexplorer.com/) 
    Em seguida, ligue o Storage Explorer à sua conta de armazenamento seguindo os passos em [Get started with Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). 
    Para saber mais, consulte [Quickstart: Crie uma bolha no armazenamento de objetos com o Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

Não é preciso uma aplicação lógica para criar e adicionar esquems. No entanto, para usar um esquema, a sua aplicação lógica precisa de ser ligada a uma conta de integração onde armazena esse esquema. Saiba [como ligar aplicações lógicas a contas de integração.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) Se ainda não tem uma aplicação lógica, aprenda [a criar aplicações lógicas.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="add-schemas"></a>Adicionar esquemas

1. Inicie sessão no <a href="https://portal.azure.com" target="_blank">portal do Azure</a> com as credenciais da sua conta do Azure.

1. Para encontrar e abrir a sua conta de integração, no menu principal do Azure, selecione **Todos os serviços.** Na caixa de pesquisa, insira a "conta de integração". Selecione **contas de Integração.**

   ![Encontre conta de integração](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. Selecione a conta de integração onde pretende adicionar o seu esquema, por exemplo:

   ![Selecione conta de integração](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. Na página de **visão geral** da sua conta de integração, em **Componentes,** selecione o azulejo **Schemas.**

   ![Selecione "Schemas"](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. Depois da página **Schemas** abrir, escolha **Adicionar**.

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

Com base no tamanho do seu ficheiro schema (.xsd), siga os passos para carregar um esquema que [seja até 2 MB](#smaller-schema) ou mais de 2 [MB, até 8 MB](#larger-schema).

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Adicione esquemmas até 2 MB

1. Em **Add Schema,** introduza um nome para o seu esquema. 
   Mantenha **o ficheiro Small** selecionado. Ao lado da caixa **Schema,** escolha o ícone da pasta. Encontre e selecione o esquema que está a carregar, por exemplo:

   ![Carregar esquemas menores](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. Quando estiver pronto, escolha **OK.**

   Depois do seu esquema terminar o upload, o esquema aparece na lista de **Schemas.**

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>Adicione esquemmas mais de 2 MB

Para adicionar esquemos maiores, pode carregar o seu esquema para um recipiente de blob Azure na sua conta de armazenamento Azure. Os seus passos para adicionar esquemos diferem se o seu recipiente blob tem acesso de leitura pública. Em primeiro lugar, verifique se o seu recipiente de blob tem ou não acesso ao público seguindo estas etapas: [Definir nível](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container) de acesso público para recipiente de bolhas

#### <a name="check-container-access-level"></a>Verifique o nível de acesso do contentor

1. Open Azure Storage Explorer. Na janela Explorer, expanda a sua subscrição Azure se ainda não estiver expandida.

1. Expandir contas de **armazenamento** > { a sua conta*de armazenamento*} > **recipientes Blob**. Selecione o seu recipiente de bolhas.

1. A partir do menu de atalho do seu recipiente blob, selecione **Definir Nível**de Acesso Público .

   * Se o seu recipiente blob tiver pelo menos acesso público, escolha **Cancelar**, e siga estes passos mais tarde nesta página: [Faça upload para contentores com acesso público](#public-access)

     ![Acesso público](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Se o seu recipiente de blob não tiver acesso público, escolha **Cancelar**, e siga estes passos mais tarde nesta página: [Faça upload para contentores sem acesso público](#public-access)

     ![Sem acesso público](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Upload para contentores com acesso público

1. Faça o upload do esquema para a sua conta de armazenamento. 
   Na janela da direita, escolha **Carregar**.

1. Depois de terminar o upload, selecione o seu esquema carregado. Na barra de ferramentas, escolha **copy URL** para que copie o URL do esquema.

1. Volte ao portal Azure onde está aberto o painel **Add Schema.** 
   Insira um nome para a sua montagem. 
   Escolha **ficheiro Grande (superior**a 2 MB) . 

   A caixa **Content URI** aparece agora, em vez da caixa **Schema.**

1. Na caixa **Content URI,** colhe o URL do seu esquema. 
   Termine de adicionar o seu esquema.

Depois do seu esquema terminar o upload, o esquema aparece na lista de **Schemas.** Na página de **visão geral** da sua conta de integração, em **Componentes,** o azulejo **Schemas** mostra agora o número de schemas carregados.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Upload para contentores sem acesso público

1. Faça o upload do esquema para a sua conta de armazenamento. 
   Na janela da direita, escolha **Carregar**.

1. Depois de terminar o upload, gere uma assinatura de acesso partilhado (SAS) para o seu esquema. 
   A partir do menu de atalho do seu esquema, selecione Obter Assinatura de **Acesso Partilhado**.

1. No painel Signature de **Acesso Partilhado,** selecione Generate a > assinatura de acesso partilhado uri**Criar**em **nível de recipiente**. 
   Depois de gerado o URL SAS, junto à caixa **URL,** escolha **Copiar**.

1. Volte ao portal Azure onde está aberto o painel **Add Schema.** Escolha **ficheiro Grande**.

   A caixa **Content URI** aparece agora, em vez da caixa **Schema.**

1. Na caixa **Content URI,** cola o SAS URI que gerou anteriormente. Termine de adicionar o seu esquema.

Depois do seu esquema terminar o upload, o esquema aparece na lista de **Schemas.** Na página de **visão geral** da sua conta de integração, em **Componentes,** o azulejo **Schemas** mostra agora o número de schemas carregados.

## <a name="edit-schemas"></a>Editar schemas

Para atualizar um esquema existente, tem de carregar um novo ficheiro de esquemas que tenha as alterações que deseja. No entanto, você pode primeiro baixar o esquema existente para edição.

1. No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>encontre e abra a sua conta de integração, se ainda não estiver aberta.

1. No menu Azure principal, selecione **Todos os serviços.** 
   Na caixa de pesquisa, insira a "conta de integração". 
   Selecione **contas de Integração.**

1. Selecione a conta de integração onde pretende atualizar o seu esquema.

1. Na página de **visão geral** da sua conta de integração, em **Componentes,** selecione o azulejo **Schemas.**

1. Depois da página **Schemas** abrir, selecione o seu esquema. 
   Para baixar e editar primeiro o esquema, escolha **Baixar**e guardar o esquema.

1. Quando estiver pronto para fazer o upload do esquema atualizado, na página **Schemas,** selecione o esquema que pretende atualizar e escolha **Atualizar**.

1. Encontre e selecione o esquema atualizado que pretende carregar. 
   Depois do seu ficheiro de esquema saquear, o esquema atualizado aparece na lista **de Schemas.**

## <a name="delete-schemas"></a>Eliminar schemas

1. No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>encontre e abra a sua conta de integração, se ainda não estiver aberta.

1. No menu Azure principal, selecione **Todos os serviços.** 
   Na caixa de pesquisa, insira a "conta de integração". 
   Selecione **contas de Integração.**

1. Selecione a conta de integração onde pretende eliminar o seu esquema.

1. Na página de **visão geral** da sua conta de integração, em **Componentes,** selecione o azulejo **Schemas.**

1. Depois da página **Schemas** abrir, selecione o seu esquema e escolha **Eliminar**.

1. Para confirmar que pretende eliminar o esquema, escolha **Sim**.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o Pacote de Integração Empresarial](logic-apps-enterprise-integration-overview.md)
* [Saiba mais sobre mapas](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Saiba mais sobre transformações](../logic-apps/logic-apps-enterprise-integration-transform.md)
