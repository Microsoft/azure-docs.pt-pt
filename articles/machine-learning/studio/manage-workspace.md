---
title: Gerir áreas de trabalho
titleSuffix: ML Studio (classic) - Azure
description: Gerir o acesso ao Azure Machine Learning Studio (clássico) espaços de trabalho e implementar e gerir serviços web da Machine Learning API
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: 68c9ca4c7b5f30d6f62d333014f4098fe7826c99
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79217955"
---
# <a name="manage-an-azure-machine-learning-studio-classic-workspace"></a>Gerir um estúdio de aprendizagem automática Azure (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> [!NOTE]
> Para obter informações sobre a gestão de serviços Web no portal machine learning Web Services, consulte [Gerir um serviço Web utilizando o portal Azure Machine Learning Web Services](manage-new-webservice.md).
> 
> 

Você pode gerir espaços de trabalho machine learning studio (clássico) no portal Azure.



## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Para gerir um espaço de trabalho studio (clássico) no portal Azure:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando uma conta de administrador de subscrição Azure.
2. Na caixa de pesquisa no topo da página, insira "machine learning Studio (clássico) espaços de trabalho" e, em seguida, selecione Espaços de **trabalho machine learning studio (clássicos).**
3. Clique no espaço de trabalho que pretende gerir.

Além das informações e opções padrão de gestão de recursos disponíveis, pode:

- Ver **Propriedades** - Esta página exibe o espaço de trabalho e informações sobre recursos, e pode alterar o grupo de subscrição e recursos com o que este espaço de trabalho está ligado.
- **Resincronizar as chaves** de armazenamento - O espaço de trabalho mantém as chaves da conta de armazenamento. Se a conta de armazenamento mudar de teclas, pode clicar em **teclas Resincronizadas** para sincronizar as teclas com o espaço de trabalho.

Para gerir os serviços web associados a este espaço de trabalho studio (clássico), utilize o portal machine learning Web Services. Consulte [gerir um serviço Web utilizando o portal Azure Machine Learning Web Services](manage-new-webservice.md) para obter informações completas.

> [!NOTE]
> Para implementar ou gerir Novos serviços web deve ser-lhe atribuído um papel de contribuinte ou administrador na subscrição à qual o serviço web é implementado. Se convidar outro utilizador para um espaço de trabalho do Estúdio de aprendizagem automática (clássico), deve atribuí-los a um papel de colaborador ou administrador na subscrição antes de poderem implementar ou gerir serviços web. 
> 
>Para obter mais informações sobre a definição de permissões de acesso, consulte [Gerir o acesso utilizando o RBAC e o portal Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [a implementação de Machine Learning com modelos](deploy-with-resource-manager-template.md)de gestor de recursos Azure . 
