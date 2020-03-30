---
title: Criar uma área de trabalho
titleSuffix: ML Studio (classic) - Azure
description: Para utilizar o Azure Machine Learning Studio (clássico), é necessário ter um espaço de trabalho do Estúdio de Aprendizagem automática (clássico). Esta área de trabalho contém as ferramentas de que precisa para criar, gerir e publicar experimentações.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 1a391a7a061d1382b5e07b45625c44fc0f5dec54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204465"
---
# <a name="create-and-share-an-azure-machine-learning-studio-classic-workspace"></a>Criar e partilhar um estúdio de aprendizagem automática Azure (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Para utilizar o Azure Machine Learning Studio (clássico), é necessário ter um espaço de trabalho do Estúdio de Aprendizagem automática (clássico). Esta área de trabalho contém as ferramentas de que precisa para criar, gerir e publicar experimentações.

## <a name="create-a-studio-classic-workspace"></a>Criar um espaço de trabalho studio (clássico)

1. Inscreva-se no [portal Azure](https://portal.azure.com/)

    > [!NOTE]
    > Para iniciar sessão e criar um espaço de trabalho studio (clássico), você precisa ser um administrador de subscrição Azure. 
    >
    > 

2. Clique **+Novo**

3. Na caixa de pesquisa, digite **machine learning studio (clássico) Workspace** e selecione o item correspondente. Em seguida, selecione clique **Criar** na parte inferior da página.

4. Insira as informações do espaço de trabalho:

   - O *nome do espaço de trabalho* pode ser de até 260 caracteres, não terminando num espaço. O nome não pode incluir estes caracteres:`< > * % & : \ ? + /`
   - O plano de *serviço web* que escolher (ou criar), juntamente com o *nível* de preços associado que selecionar, é utilizado se implementar serviços web a partir deste espaço de trabalho.

     ![Criar um novo espaço de trabalho studio (clássico)](./media/create-workspace/create-new-workspace.png)

5. Clique em **Criar**.

> [!NOTE]
> O Machine Learning Studio (clássico) baseia-se numa conta de armazenamento Azure que fornece para guardar dados intermediários quando executa o fluxo de trabalho. Após a criação do espaço de trabalho, se a conta de armazenamento for eliminada, ou se as teclas de acesso forem alteradas, o espaço de trabalho deixará de funcionar e todas as experiências nesse espaço de trabalho falharão.
Se apagar acidentalmente a conta de armazenamento, recrie a conta de armazenamento com o mesmo nome na mesma região que a conta de armazenamento eliminada e resintifique a chave de acesso. Se tiver alterado as chaves de acesso da conta de armazenamento, utilize o portal do Azure e volte a sincronizá-las na área de trabalho.

Uma vez que o espaço de trabalho é implantado, você pode abri-lo no Machine Learning Studio (clássico).

1. Navegue no Machine Learning Studio [https://studio.azureml.net/](https://studio.azureml.net/)(clássico) em .

2. Selecione a sua área de trabalho no canto superior direito.

    ![Selecionar área de trabalho](./media/create-workspace/open-workspace.png)

3. Clique nas **minhas experiências.**

    ![Experiências abertas](./media/create-workspace/my-experiments.png)

Para obter informações sobre a gestão do seu espaço de trabalho studio (clássico), consulte [Manage a Azure Machine Learning Studio (clássico) espaço](manage-workspace.md)de trabalho.
Se encontrar algum problema ao criar o seu espaço de trabalho, consulte o [guia de resolução de problemas: Crie e ligue-se a um espaço de trabalho do Machine Learning Studio (clássico).](troubleshooting-creating-ml-workspace.md)


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Partilhe um Estúdio de Aprendizagem automática Azure (clássico)
Uma vez criado um espaço de trabalho do Machine Learning Studio (clássico), pode convidar os utilizadores para o seu espaço de trabalho para partilharem acesso ao seu espaço de trabalho e a todas as suas experiências, conjuntos de dados, cadernos, etc. Pode adicionar utilizadores numa de duas funções:

* **Utilizador** - Um utilizador do espaço de trabalho pode criar, abrir, modificar e eliminar experiências, conjuntos de dados, etc. no espaço de trabalho.
* **Proprietário** - Um proprietário pode convidar e remover os utilizadores no espaço de trabalho, além do que um utilizador pode fazer.

> [!NOTE]
> A conta de administrador que cria o espaço de trabalho é automaticamente adicionada ao espaço de trabalho como Proprietário do espaço de trabalho. No entanto, outros administradores ou utilizadores dessa subscrição não têm acesso automaticamente ao espaço de trabalho - é necessário convidá-los explicitamente.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Para partilhar um espaço de trabalho studio (clássico)

1. Inscreva-se no Machine Learning Studio (clássico) em[https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. No painel esquerdo, clique em **DEFINIÇÕES**

3. Clique no separador **UTILIZADORES**

4. Clique em **CONVIDAR MAIS UTILIZADORES** na parte inferior da página

    ![Configurações do estúdio](./media/create-workspace/settings.png)

5. Insira um ou mais endereços de e-mail. Os utilizadores precisam de uma conta Microsoft válida ou de uma conta organizacional (do Azure Ative Directory).

6. Selecione se pretende adicionar os utilizadores como Proprietário ou Utilizador.

7. Clique no botão **OK** checkmark.

Cada utilizador que adicionar receberá um e-mail com instruções sobre como iniciar sessão no espaço de trabalho partilhado.

> [!NOTE]
> Para que os utilizadores possam implementar ou gerir serviços web neste espaço de trabalho, devem ser colaboradores ou administradores na subscrição do Azure. 



