---
title: Gerir áreas de trabalho
titleSuffix: ML Studio (classic) Azure
description: Gerenciar o acesso a espaços de trabalho Azure Machine Learning Studio (clássico) e implantar e gerenciar serviços Web de API Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: 0ec619e765dde39f6d4c24cdadaab8bff4729f79
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684753"
---
# <a name="manage-an-azure-machine-learning-studio-classic-workspace"></a>Gerenciar um espaço de trabalho Azure Machine Learning Studio (clássico)

> [!NOTE]
> Para obter informações sobre como gerenciar serviços Web no portal de serviços da Web Machine Learning, consulte [gerenciar um serviço Web usando o portal de serviços Web do Azure Machine Learning](manage-new-webservice.md).
> 
> 

Você pode gerenciar os espaços de trabalho do Machine Learning Studio (clássico) no portal do Azure.



## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Para gerenciar um espaço de trabalho do Studio (clássico) no portal do Azure:

1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta de administrador de assinatura do Azure.
2. Na caixa de pesquisa na parte superior da página, insira "espaços de trabalho do Machine Learning Studio (clássico)" e, em seguida, selecione os **espaços de trabalho do Machine Learning Studio (clássico)** .
3. Clique no espaço de trabalho que você deseja gerenciar.

Além das informações e opções de gerenciamento de recursos padrão disponíveis, você pode:

- Exibir **Propriedades** – esta página exibe as informações de espaço de trabalho e recurso, e você pode alterar a assinatura e o grupo de recursos aos quais esse espaço de trabalho está conectado.
- **Resincronizar as chaves de armazenamento** – o espaço de trabalho mantém as chaves para a conta de armazenamento. Se a conta de armazenamento alterar as chaves, você poderá clicar em **ressincronizar chaves** para sincronizar as chaves com o espaço de trabalho.

Para gerenciar os serviços Web associados a este espaço de trabalho do estúdio (clássico), use o Machine Learning Portal de serviços Web. Consulte [gerenciar um serviço Web usando o portal de serviços da web Azure Machine Learning](manage-new-webservice.md) para obter informações completas.

> [!NOTE]
> Para implantar ou gerenciar novos serviços Web, você deve receber uma função de colaborador ou administrador na assinatura na qual o serviço Web está implantado. Se você convidar outro usuário para um espaço de trabalho do Machine Learning Studio (clássico), deverá atribuí-lo a uma função de colaborador ou de administrador na assinatura para poder implantar ou gerenciar serviços Web. 
> 
>Para obter mais informações sobre como definir permissões de acesso, consulte [gerenciar o acesso usando RBAC e o portal do Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre como [implantar Machine Learning com modelos de Azure Resource Manager](deploy-with-resource-manager-template.md). 
