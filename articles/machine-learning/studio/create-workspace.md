---
title: Criar uma área de trabalho
titleSuffix: ML Studio (classic) - Azure
description: Para utilizar o Azure Machine Learning Studio (clássico), é necessário ter um espaço de trabalho do Estúdio de Aprendizagem automática (clássico). Esta área de trabalho contém as ferramentas de que precisa para criar, gerir e publicar experimentações.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 419fbd987c1deb95a7c903d85105958443d06b01
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153609"
---
# <a name="create-and-share-an-azure-machine-learning-studio-classic-workspace"></a>Criar e partilhar um estúdio de aprendizagem automática Azure (clássico)

Para utilizar o Azure Machine Learning Studio (clássico), é necessário ter um espaço de trabalho do Estúdio de Aprendizagem automática (clássico). Esta área de trabalho contém as ferramentas de que precisa para criar, gerir e publicar experimentações.

## <a name="create-a-studio-classic-workspace"></a>Criar um espaço de trabalho studio (clássico)

1. Inicie sessão no [portal do Azure](https://portal.azure.com/)

    > [!NOTE]
    > Para iniciar sessão e criar um espaço de trabalho studio (clássico), você precisa ser um administrador de subscrição Azure. 
    >
    > 

2. Clique **+Novo**

3. Na caixa de pesquisa, digite **machine learning studio (clássico) Workspace** e selecione o item correspondente. Em seguida, selecione clique **Criar** na parte inferior da página.

4. Introduza as suas informações de área de trabalho:

   - O *nome do espaço de trabalho* pode ser de até 260 caracteres, não terminando num espaço. O nome não pode incluir estes caracteres: `< > * % & : \ ? + /`
   - O plano de *serviço web* que escolher (ou criar), juntamente com o *nível* de preços associado que selecionar, é utilizado se implementar serviços web a partir deste espaço de trabalho.

     ![Criar um novo espaço de trabalho studio (clássico)](./media/create-workspace/create-new-workspace.png)

5. Clique em **Criar**.

> [!NOTE]
> O Machine Learning Studio (clássico) baseia-se numa conta de armazenamento Azure que fornece para guardar dados intermediários quando executa o fluxo de trabalho. Após a criação do espaço de trabalho, se a conta de armazenamento for eliminada, ou se as teclas de acesso forem alteradas, o espaço de trabalho deixará de funcionar e todas as experiências nesse espaço de trabalho falharão.
Se apagar acidentalmente a conta de armazenamento, recrie a conta de armazenamento com o mesmo nome na mesma região que a conta de armazenamento eliminada e resintifique a chave de acesso. Se tiver alterado as chaves de acesso da conta de armazenamento, utilize o portal do Azure e volte a sincronizá-las na área de trabalho.

Uma vez que o espaço de trabalho é implantado, você pode abri-lo no Machine Learning Studio (clássico).

1. Navegue no Machine Learning Studio (clássico) no [https://studio.azureml.net/](https://studio.azureml.net/).

2. Selecione a área de trabalho no canto superior direita.

    ![Selecionar área de trabalho](./media/create-workspace/open-workspace.png)

3. Clique nas **minhas experiências.**

    ![Experimentações abertas](./media/create-workspace/my-experiments.png)

Para obter informações sobre a gestão do seu espaço de trabalho studio (clássico), consulte [Manage a Azure Machine Learning Studio (clássico) espaço](manage-workspace.md)de trabalho.
Se encontrar algum problema ao criar o seu espaço de trabalho, consulte o [guia de resolução de problemas: Crie e ligue-se a um espaço de trabalho do Machine Learning Studio (clássico).](troubleshooting-creating-ml-workspace.md)


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Partilhe um Estúdio de Aprendizagem automática Azure (clássico)
Uma vez criado um espaço de trabalho do Machine Learning Studio (clássico), pode convidar os utilizadores para o seu espaço de trabalho para partilharem acesso ao seu espaço de trabalho e a todas as suas experiências, conjuntos de dados, cadernos, etc. Pode adicionar utilizadores numa de duas funções:

* **Utilizador** - Um utilizador do espaço de trabalho pode criar, abrir, modificar e eliminar experiências, conjuntos de dados, etc. no espaço de trabalho.
* **Proprietário** - Um proprietário pode convidar e remover os utilizadores no espaço de trabalho, além do que um utilizador pode fazer.

> [!NOTE]
> A conta de administrador que cria a área de trabalho é automaticamente adicionada à área de trabalho como proprietário da área de trabalho. No entanto, outros administradores ou utilizadores nessa subscrição não recebem automaticamente acesso à área de trabalho - precisa para convidá-los explicitamente.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Para partilhar um espaço de trabalho studio (clássico)

1. Inscreva-se no Machine Learning Studio (clássico) no [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. No painel esquerdo, clique em **DEFINIÇÕES**

3. Clique no separador **UTILIZADORES**

4. Clique em **CONVIDAR MAIS UTILIZADORES** na parte inferior da página

    ![Configurações do Studio](./media/create-workspace/settings.png)

5. Introduza um ou mais endereços de e-mail. Os usuários precisarão de uma conta Microsoft válida ou uma conta institucional (a partir do Azure Active Directory).

6. Selecione se pretende adicionar os utilizadores como proprietário ou de utilizador.

7. Clique no botão **OK** checkmark.

Cada usuário que adicionar irão receber um e-mail com instruções sobre como iniciar sessão para a área de trabalho partilhada.

> [!NOTE]
> Para os utilizadores poder implementar ou gerir os serviços da web nesta área de trabalho, têm de ser Contribuidor ou administrador na subscrição do Azure. 



