---
title: Criar uma área de trabalho
titleSuffix: ML Studio (classic) - Azure
description: Para usar Azure Machine Learning Studio (clássico), você precisa ter um espaço de trabalho Machine Learning Studio (clássico). Esta área de trabalho contém as ferramentas de que precisa para criar, gerir e publicar experimentações.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 1652febf6c3eb906990b2405590274911a45348e
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839920"
---
# <a name="create-and-share-an-azure-machine-learning-studio-classic-workspace"></a>Criar e compartilhar um espaço de trabalho Azure Machine Learning Studio (clássico)

Para usar Azure Machine Learning Studio (clássico), você precisa ter um espaço de trabalho Machine Learning Studio (clássico). Esta área de trabalho contém as ferramentas de que precisa para criar, gerir e publicar experimentações.

## <a name="create-a-studio-classic-workspace"></a>Criar um espaço de trabalho do estúdio (clássico)

1. Inicie sessão no [portal do Azure](https://portal.azure.com/)

    > [!NOTE]
    > Para entrar e criar um espaço de trabalho do estúdio (clássico), você precisa ser um administrador de assinatura do Azure. 
    >
    > 

2. Clique em **+ novo**

3. Na caixa de pesquisa, digite **Machine Learning Studio espaço de trabalho (clássico)** e selecione o item correspondente. Em seguida, selecione clique em **criar** na parte inferior da página.

4. Insira as informações do espaço de trabalho:

   - O *nome do espaço de trabalho* pode ter até 260 caracteres, não terminando em um espaço. O nome não pode incluir estes caracteres: `< > * % & : \ ? + /`
   - O *plano de serviço Web* que você escolher (ou criar), juntamente com o *tipo de preço* associado que você selecionar, será usado se você implantar serviços Web desse espaço de trabalho.

     ![Criar um novo espaço de trabalho Studio (clássico)](./media/create-workspace/create-new-workspace.png)

5. Clique em **Criar**.

> [!NOTE]
> Machine Learning Studio (clássico) depende de uma conta de armazenamento do Azure que você fornece para salvar dados intermediários ao executar o fluxo de trabalho. Depois que o espaço de trabalho for criado, se a conta de armazenamento for excluída ou se as chaves de acesso forem alteradas, o espaço de trabalho deixará de funcionar e todos os experimentos nesse espaço de trabalho falharão.
Se você excluir acidentalmente a conta de armazenamento, recrie a conta de armazenamento com o mesmo nome na mesma região que a conta de armazenamento excluída e Ressincronize a chave de acesso. Se tiver alterado as chaves de acesso da conta de armazenamento, utilize o portal do Azure e volte a sincronizá-las na área de trabalho.

Depois que o espaço de trabalho for implantado, você poderá abri-lo na versão clássica do Machine Learning Studio.

1. Navegue até Machine Learning Studio (clássico) em [https://studio.azureml.net/](https://studio.azureml.net/).

2. Selecione seu espaço de trabalho no canto superior direito.

    ![Selecionar área de trabalho](./media/create-workspace/open-workspace.png)

3. Clique em **meus experimentos**.

    ![Experiências em aberto](./media/create-workspace/my-experiments.png)

Para obter informações sobre como gerenciar seu espaço de trabalho do estúdio (clássico), consulte [gerenciar um espaço de trabalho Azure Machine Learning Studio (clássico)](manage-workspace.md).
Se você encontrar um problema ao criar seu espaço de trabalho, consulte [Guia de solução de problemas: criar e conectar-se a um espaço de trabalho Machine Learning Studio (clássico)](troubleshooting-creating-ml-workspace.md).


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Compartilhar um espaço de trabalho Azure Machine Learning Studio (clássico)
Quando um espaço de trabalho Machine Learning Studio (clássico) é criado, você pode convidar usuários para seu espaço de trabalho para compartilhar o acesso ao seu espaço de trabalho e a todos os seus experimentos, conjuntos de anotações, notebooks, etc. Você pode adicionar usuários em uma das duas funções:

* **Usuário** – um usuário de espaço de trabalho pode criar, abrir, modificar e excluir experimentos, conjuntos de valores, etc. no espaço de trabalho.
* **Proprietário** : um proprietário pode convidar e remover usuários no espaço de trabalho, além do que um usuário pode fazer.

> [!NOTE]
> A conta de administrador que cria o espaço de trabalho é automaticamente adicionada ao espaço de trabalho como o proprietário do Workspace. No entanto, outros administradores ou usuários nessa assinatura não recebem automaticamente o acesso ao espaço de trabalho, você precisa convidá-los explicitamente.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Para compartilhar um espaço de trabalho do Studio (clássico)

1. Entrar na versão clássica do Machine Learning Studio em [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. No painel esquerdo, clique em **configurações**

3. Clique na guia **usuários**

4. Clique em **convidar mais usuários** na parte inferior da página

    ![Configurações do estúdio](./media/create-workspace/settings.png)

5. Insira um ou mais endereços de email. Os usuários precisam de uma conta Microsoft válida ou de uma conta organizacional (de Azure Active Directory).

6. Selecione se deseja adicionar os usuários como proprietário ou usuário.

7. Clique no botão de marca de seleção **OK** .

Cada usuário que você adicionar receberá um email com instruções sobre como entrar no espaço de trabalho compartilhado.

> [!NOTE]
> Para que os usuários possam implantar ou gerenciar serviços Web nesse espaço de trabalho, eles devem ser um colaborador ou administrador na assinatura do Azure. 



