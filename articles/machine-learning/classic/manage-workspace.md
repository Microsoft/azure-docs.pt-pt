---
title: 'Estúdio ML (clássico): Gerir espaços de trabalho - Azure'
description: Gerir o acesso ao Azure Machine Learning Studio (clássico) espaços de trabalho e implementar e gerir serviços web machine learning API
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: c41c5350e29555a11bb74eeca9af62a6b87cfced
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101094274"
---
# <a name="manage-an-azure-machine-learning-studio-classic-workspace"></a>Gerir um espaço de trabalho Azure Machine Learning Studio (clássico)

**APLICA-SE A:** ![ Aplica-se a. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


> [!NOTE]
> Para obter informações sobre a gestão de serviços Web no portal Machine Learning Web Services, consulte [Gerir um serviço Web utilizando o portal Azure Machine Learning Web Services](manage-new-webservice.md).
> 
> 

Você pode gerir espaços de trabalho machine learning (clássicos) no portal Azure.



## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Para gerir um espaço de trabalho studio (clássico) no portal Azure:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando uma conta de administrador de subscrição Azure.
2. Na caixa de pesquisa no topo da página, insira "machine learning Studio (classic) workspaces" e, em seguida, selecione **espaços de trabalho do Machine Learning Studio (clássico).**
3. Clique no espaço de trabalho que pretende gerir.

Além das informações e opções padrão de gestão de recursos disponíveis, pode:

- Ver **Propriedades** - Esta página exibe o espaço de trabalho e informações de recursos, e pode alterar o grupo de subscrição e recursos com o qual este espaço de trabalho está ligado.
- **Chaves de armazenamento Resync** - O espaço de trabalho mantém as chaves da conta de armazenamento. Se a conta de armazenamento alterar as teclas, pode clicar nas **teclas Resync** para sincronizar as teclas com o espaço de trabalho.

Para gerir os serviços web associados a este espaço de trabalho Studio (clássico), utilize o portal Machine Learning Web Services. Consulte [Gerir um serviço Web utilizando o portal Azure Machine Learning Web Services](manage-new-webservice.md) para obter informações completas.

> [!NOTE]
> Para implementar ou gerir Novos serviços web, deve ser-lhe atribuída uma função de contribuinte ou administrador na subscrição para a qual o serviço web é implantado. Se convidar outro utilizador para um espaço de trabalho do Machine Learning Studio (clássico), deve atribuí-lo a um colaborador ou administrador na subscrição antes de poder implementar ou gerir serviços web. 
> 
>Para obter mais informações sobre a definição de permissões de acesso, consulte [as funções De Atribuição Azure utilizando o portal Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [a implementação de Machine Learning com modelos de gestor de recursos Azure.](deploy-with-resource-manager-template.md)