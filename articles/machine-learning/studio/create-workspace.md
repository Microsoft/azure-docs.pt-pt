---
title: Criar áreas de trabalho
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
ms.openlocfilehash: 91ba4d1f7d32071cce0de1de528abf02982ce7be
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427623"
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

3. Na caixa de pesquisa, digite **Machine Learning Studio espaço de trabalho (clássico)** e selecione o item correspondente. Em seguida, clique em select **criar** na parte inferior da página.

4. Introduza as suas informações de área de trabalho:

   - O *nome da área de trabalho* pode ser até 260 carateres, não termina num espaço. O nome não pode incluir estes carateres: `< > * % & : \ ? + /`
   - O *plano do serviço web* escolha (ou criar), juntamente com o associado *escalão de preço* selecionar, será utilizado se implementar os serviços da web desta área de trabalho.

     ![Criar um novo espaço de trabalho Studio (clássico)](./media/create-workspace/create-new-workspace.png)

5. Clique em **Criar**.

> [!NOTE]
> Machine Learning Studio (clássico) depende de uma conta de armazenamento do Azure que você fornece para salvar dados intermediários ao executar o fluxo de trabalho. Depois que o espaço de trabalho for criado, se a conta de armazenamento for excluída ou se as chaves de acesso forem alteradas, o espaço de trabalho deixará de funcionar e todos os experimentos nesse espaço de trabalho falharão.
Se você excluir acidentalmente a conta de armazenamento, recrie a conta de armazenamento com o mesmo nome na mesma região que a conta de armazenamento excluída e Ressincronize a chave de acesso. Se tiver alterado as chaves de acesso da conta de armazenamento, utilize o portal do Azure e volte a sincronizá-las na área de trabalho.

Depois que o espaço de trabalho for implantado, você poderá abri-lo no Machine Learning Studio (clássico).

1. Navegue até Machine Learning Studio (clássico) em [https://studio.azureml.net/](https://studio.azureml.net/).

2. Selecione a área de trabalho no canto superior direita.

    ![Selecionar área de trabalho](./media/create-workspace/open-workspace.png)

3. Clique em **minhas experimentações**.

    ![Experimentações abertas](./media/create-workspace/my-experiments.png)

Para obter informações sobre como gerenciar seu espaço de trabalho do estúdio (clássico), consulte [gerenciar um espaço de trabalho Azure Machine Learning Studio (clássico)](manage-workspace.md).
Se você encontrar um problema ao criar seu espaço de trabalho, consulte [Guia de solução de problemas: criar e conectar-se a um espaço de trabalho Machine Learning Studio (clássico)](troubleshooting-creating-ml-workspace.md).


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Compartilhar um espaço de trabalho Azure Machine Learning Studio (clássico)
Quando um espaço de trabalho Machine Learning Studio (clássico) é criado, você pode convidar usuários para seu espaço de trabalho para compartilhar o acesso ao seu espaço de trabalho e a todos os seus experimentos, conjuntos de anotações, notebooks, etc. Você pode adicionar usuários em uma das duas funções:

* **Utilizador** -um usuário de área de trabalho pode criar, abrir, modificar e eliminar experimentações, conjuntos de dados, etc. na área de trabalho.
* **Proprietário** - pode convidar um proprietário e remover utilizadores na área de trabalho, além de que um utilizador podem fazer.

> [!NOTE]
> A conta de administrador que cria a área de trabalho é automaticamente adicionada à área de trabalho como proprietário da área de trabalho. No entanto, outros administradores ou utilizadores nessa subscrição não recebem automaticamente acesso à área de trabalho - precisa para convidá-los explicitamente.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Para compartilhar um espaço de trabalho do Studio (clássico)

1. Entre no Machine Learning Studio (clássico) em [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

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



