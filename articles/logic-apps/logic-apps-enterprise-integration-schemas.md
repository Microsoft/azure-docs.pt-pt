---
title: Validar XML com esquemas
description: Adicione esquemas para validar documentos XML em Azure Logic Apps com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: ade29318fdd1510d03ddd208534258a30b256e98
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "75979368"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>Validar XML com esquemas em Azure Logic Apps com Enterprise Integration Pack

Para verificar se os documentos utilizam XML válido e têm os dados esperados no formato predefinido para cenários de integração empresarial em Azure Logic Apps, a sua aplicação lógica pode usar esquemas. Um esquema também pode validar mensagens que as aplicações lógicas trocam em cenários business-to-business (B2B).

Para limites relacionados com contas de integração e artefactos como esquemas, consulte [limites e informações de configuração para Apps Azure Logic](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se numa conta do Azure gratuita</a>.

* Uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) onde armazena os seus esquemas e outros artefactos para a integração empresarial e soluções business-to-business (B2B). 

  Se o seu esquema for [de 2 MB ou menor,](#smaller-schema)pode adicionar o seu esquema à sua conta de integração diretamente a partir do portal Azure. No entanto, se o seu esquema for maior que 2 MB mas não maior do que o limite de tamanho do [esquema,](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)pode enviar o seu esquema para uma conta de armazenamento Azure. 
  Para adicionar esse esquema à sua conta de integração, pode então ligar-se à sua conta de armazenamento a partir da sua conta de integração. 
  Para esta tarefa, aqui estão os itens que precisa: 

  * [Conta de armazenamento Azure](../storage/common/storage-account-overview.md) onde se cria um recipiente para o seu esquema. Saiba como [criar uma conta de armazenamento.](../storage/common/storage-account-create.md) 

  * Recipiente blob para armazenar o seu esquema. Saiba como [criar um recipiente de bolhas.](../storage/blobs/storage-quickstart-blobs-portal.md) 
  Precisa do conteúdo do seu contentor URI mais tarde quando adicionar o esquema à sua conta de integração.

  * [Azure Storage Explorer,](../vs-azure-tools-storage-manage-with-storage-explorer.md)que pode utilizar para gerir contas de armazenamento e recipientes blob. 
  Para utilizar o Storage Explorer, escolha qualquer opção aqui:
  
    * No portal Azure, encontre e selecione a sua conta de armazenamento. 
    A partir do menu da sua conta de armazenamento, selecione **Storage Explorer**.

    * Para a versão para desktop, [descarregue e instale o Azure Storage Explorer](https://www.storageexplorer.com/). 
    Em seguida, ligue o Storage Explorer à sua conta de armazenamento seguindo os passos em [Começar com o Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). 
    Para saber mais, consulte [Quickstart: Crie uma bolha no armazenamento de objetos com o Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

Não precisa de uma aplicação lógica ao criar e adicionar esquemas. No entanto, para utilizar um esquema, a sua aplicação lógica precisa de ser ligada a uma conta de integração onde armazena esse esquema. Saiba [como ligar aplicações lógicas a contas de integração.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) Se ainda não tem uma aplicação lógica, aprenda [a criar aplicações lógicas.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="add-schemas"></a>Adicionar esquemas

1. Inicie sessão no <a href="https://portal.azure.com" target="_blank">portal do Azure</a> com as credenciais da sua conta do Azure.

1. Para encontrar e abrir a sua conta de integração, no menu Azure principal, selecione **Todos os serviços**. Na caixa de pesquisa, insira "conta de integração". Selecione **contas de integração**.

   ![Encontrar conta de integração](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. Selecione a conta de integração onde pretende adicionar o seu esquema, por exemplo:

   ![Selecione conta de integração](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. Na **página** geral da sua conta de integração, em **Componentes,** selecione o azulejo **De Schemas.**

   ![Selecione "Schemas"](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. Depois de abrir a página **De Schemas,** escolha **Adicionar**.

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

Com base no tamanho do seu ficheiro de esquema (.xsd), siga os passos para o upload de um esquema que seja [até 2 MB](#smaller-schema) ou [mais de 2 MB, até 8 MB](#larger-schema).

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Adicione esquemas até 2 MB

1. Em **Add Schema,** insira um nome para o seu esquema. 
   Mantenha **o ficheiro Small** selecionado. Ao lado da caixa **Schema,** escolha o ícone da pasta. Encontre e selecione o esquema que está a carregar, por exemplo:

   ![Carregar esquemas menores](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. Quando estiver pronto, escolha **OK.**

   Depois do seu esquema terminar o upload, o esquema aparece na lista **de Schemas.**

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>Adicione esquemas mais de 2 MB

Para adicionar esquemas maiores, pode fazer o upload do seu esquema para um recipiente de bolhas Azure na sua conta de armazenamento Azure. Os seus passos para adicionar esquemas diferem com base no facto de o seu recipiente de bolhas ter acesso público. Primeiro, verifique se o seu recipiente de bolhas tem ou não acesso público ao ler os seus passos seguindo estes passos: [Definir o nível de acesso público para o recipiente blob](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Verifique o nível de acesso ao contentor

1. Abra o Explorador de Armazenamento Azure. Na janela Explorer, expanda a subscrição do Azure se não for já expandida.

1. Expandir **contas de armazenamento** > { a sua conta de *armazenamento*} > **Blob Containers**. Selecione o seu recipiente blob.

1. No menu de atalho do seu recipiente blob, selecione **Definir o Nível de Acesso Público**.

   * Se o seu recipiente blob tiver pelo menos acesso público, escolha **Cancelar,** e siga estes passos mais tarde nesta página: [Faça upload para contentores com acesso público](#public-access)

     ![Acesso público](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Se o seu recipiente blob não tiver acesso público, escolha **Cancelar,** e siga estes passos mais tarde nesta página: [Faça upload para contentores sem acesso público](#public-access)

     ![Sem acesso público](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Upload para contentores com acesso público

1. Faça o upload do esquema para a sua conta de armazenamento. 
   Na janela da direita, escolha **upload**.

1. Depois de terminar o upload, selecione o seu esquema carregado. Na barra de ferramentas, escolha **copy URL** para que copie o URL do esquema.

1. Volte ao portal Azure onde está aberto o painel **Add Schema.** 
   Insira um nome para a sua assembleia. 
   Escolha **o ficheiro Large (superior a 2 MB)**. 

   A caixa **URI de conteúdo** aparece agora, em vez da caixa **Schema.**

1. Na caixa **Content URI,** cole o URL do seu esquema. 
   Termine de adicionar o seu esquema.

Depois do seu esquema terminar o upload, o esquema aparece na lista **de Schemas.** Na **página** geral da sua conta de integração, em **Componentes,** o azulejo **De Schemas** mostra agora o número de esquemas carregados.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Upload para contentores sem acesso público

1. Faça o upload do esquema para a sua conta de armazenamento. 
   Na janela da direita, escolha **upload**.

1. Depois de terminar o upload, gere uma assinatura de acesso partilhado (SAS) para o seu esquema. 
   No menu de atalho do seu esquema, **selecione Obter Assinatura de Acesso Partilhado**.

1. No painel de assinatura de **acesso partilhado,** selecione **Gere a assinatura de acesso partilhado ao nível do contentor URI**  >  **Create**. 
   Depois de o URL SAS ser gerado, junto à caixa **URL,** escolha **Copy**.

1. Volte ao portal Azure onde está aberto o painel **Add Schema.** Escolha **o ficheiro Large**.

   A caixa **URI de conteúdo** aparece agora, em vez da caixa **Schema.**

1. Na caixa **URI de conteúdo,** cole o SAS URI que gerou anteriormente. Termine de adicionar o seu esquema.

Depois do seu esquema terminar o upload, o esquema aparece na lista **de Schemas.** Na **página** geral da sua conta de integração, em **Componentes,** o azulejo **De Schemas** mostra agora o número de esquemas carregados.

## <a name="edit-schemas"></a>Editar esquemas

Para atualizar um esquema existente, tem de carregar um novo ficheiro de esquema que tenha as alterações que pretende. No entanto, pode primeiro descarregar o esquema existente para edição.

1. No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>encontre e abra a sua conta de integração, se não já estiver aberta.

1. No menu Azure principal, selecione **Todos os serviços**. 
   Na caixa de pesquisa, insira "conta de integração". 
   Selecione **contas de integração**.

1. Selecione a conta de integração onde pretende atualizar o seu esquema.

1. Na **página** geral da sua conta de integração, em **Componentes,** selecione o azulejo **De Schemas.**

1. Depois de a página **de Schemas** abrir, selecione o seu esquema. 
   Para baixar e editar primeiro o esquema, escolha **Baixar** e guardar o esquema.

1. Quando estiver pronto para carregar o esquema atualizado, na página **Schemas,** selecione o esquema que pretende atualizar e escolha **Update**.

1. Encontre e selecione o esquema atualizado que pretende carregar. 
   Depois de o seu ficheiro de esquema terminar o upload, o esquema atualizado aparece na lista **de Schemas.**

## <a name="delete-schemas"></a>Eliminar esquemas

1. No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>encontre e abra a sua conta de integração, se não já estiver aberta.

1. No menu Azure principal, selecione **Todos os serviços**. 
   Na caixa de pesquisa, insira "conta de integração". 
   Selecione **contas de integração**.

1. Selecione a conta de integração onde pretende eliminar o seu esquema.

1. Na **página** geral da sua conta de integração, em **Componentes,** selecione o azulejo **De Schemas.**

1. Depois de abrir a página **Schemas,** selecione o seu esquema e escolha **Eliminar**.

1. Para confirmar que pretende eliminar o esquema, escolha **Sim**.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o Pacote de Integração Empresarial](logic-apps-enterprise-integration-overview.md)
* [Saiba mais sobre mapas](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Saiba mais sobre transformações](../logic-apps/logic-apps-enterprise-integration-transform.md)
