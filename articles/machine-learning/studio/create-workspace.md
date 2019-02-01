---
title: Criar uma área de trabalho
titleSuffix: Azure Machine Learning Studio
description: Para utilizar o Azure Machine Learning Studio, tem de ter uma área de trabalho do Machine Learning Studio. Esta área de trabalho contém as ferramentas de que precisa para criar, gerir e publicar experimentações.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 16c67c217c8ef33a360fd479a45317d6c42af494
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55486322"
---
# <a name="create-and-share-an-azure-machine-learning-studio-workspace"></a>Criar e partilhar uma área de trabalho do Azure Machine Learning Studio

Para utilizar o Azure Machine Learning Studio, tem de ter uma área de trabalho do Machine Learning Studio. Esta área de trabalho contém as ferramentas de que precisa para criar, gerir e publicar experimentações.



### <a name="to-create-a-workspace"></a>Para criar uma área de trabalho
1. Inicie sessão no [portal do Azure](https://portal.azure.com/)

    > [!NOTE]
    > Para iniciar sessão e criar uma área de trabalho, terá de ser um administrador de subscrição do Azure. 
    >
    > 

2. Clique em **+ novo**

3. Na caixa de pesquisa, escreva **área de trabalho do Machine Learning Studio** e selecione o item correspondente. Em seguida, clique em select **criar** na parte inferior da página.

4. Introduza as suas informações de área de trabalho:

    - O *nome da área de trabalho* pode ser até 260 carateres, não termina num espaço. O nome não pode incluir estes carateres: `< > * % & : \ ? + /`
    - O *plano do serviço web* escolha (ou criar), juntamente com o associado *escalão de preço* selecionar, será utilizado se implementar os serviços da web desta área de trabalho.

    ![Criar uma nova área de trabalho](./media/create-workspace/create-new-workspace.png)

5. Clique em **Criar**.

Assim que a área de trabalho é implementada, pode abri-lo no Machine Learning Studio.

1. Procurar Machine Learning Studio no [ https://studio.azureml.net/ ](https://studio.azureml.net/).

2. Selecione a área de trabalho no canto superior direita.

    ![Selecionar área de trabalho](./media/create-workspace/open-workspace.png)

3. Clique em **minhas experimentações**.

    ![Experimentações abertas](./media/create-workspace/my-experiments.png)

Para obter informações sobre como gerir a sua área de trabalho, consulte [gerir uma área de trabalho do Azure Machine Learning](manage-workspace.md).
Se ocorrer um problema ao criar a sua área de trabalho, consulte [guia de resolução de problemas: Criar e ligar a uma área de trabalho do Machine Learning](troubleshooting-creating-ml-workspace.md).


## <a name="sharing-an-azure-machine-learning-workspace"></a>Partilhar uma área de trabalho do Azure Machine Learning
Uma vez Machine Learning é criada a área de trabalho, pode convidar utilizadores à sua área de trabalho para partilhar o acesso a sua área de trabalho e todas as suas experimentações, conjuntos de dados, blocos de notas, etc. Pode adicionar utilizadores de uma das duas funções:

* **Utilizador** -um usuário de área de trabalho pode criar, abrir, modificar e eliminar experimentações, conjuntos de dados, etc. na área de trabalho.
* **Proprietário** - pode convidar um proprietário e remover utilizadores na área de trabalho, além de que um utilizador podem fazer.

> [!NOTE]
> A conta de administrador que cria a área de trabalho é automaticamente adicionada à área de trabalho como proprietário da área de trabalho. No entanto, outros administradores ou utilizadores nessa subscrição não recebem automaticamente acesso à área de trabalho - precisa para convidá-los explicitamente.
> 
> 

### <a name="to-share-a-workspace"></a>Para partilhar uma área de trabalho

1. Inicie sessão no Machine Learning Studio no [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. No painel esquerdo, clique em **definições**

3. Clique nas **utilizadores** separador

4. Clique em **CONVIDAR utilizadores mais** na parte inferior da página

    ![Configurações do Studio](./media/create-workspace/settings.png)

5. Introduza um ou mais endereços de e-mail. Os usuários precisarão de uma conta Microsoft válida ou uma conta institucional (a partir do Azure Active Directory).

6. Selecione se pretende adicionar os utilizadores como proprietário ou de utilizador.

7. Clique nas **OK** botão de marca de verificação.

Cada usuário que adicionar irão receber um e-mail com instruções sobre como iniciar sessão para a área de trabalho partilhada.

> [!NOTE]
> Para os utilizadores poder implementar ou gerir os serviços da web nesta área de trabalho, têm de ser Contribuidor ou administrador na subscrição do Azure. 



