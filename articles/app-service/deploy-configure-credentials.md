---
title: Configurar as credenciais de implementação - serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba como utilizar as credenciais de implementação do serviço de aplicações do Azure.
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnoc
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/22/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: d89197fad8354b0bae41ab67b9bb1dfac0a179eb
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820302"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Configurar as credenciais de implementação para o serviço de aplicações do Azure
[Serviço de aplicações do Azure](https://go.microsoft.com/fwlink/?LinkId=529714) suporta dois tipos de credenciais para [implementação de Git local](deploy-local-git.md) e [implementação de FTP/S](deploy-ftp.md). Essas não são o mesmo que as suas credenciais do Azure Active Directory.

* **Credenciais de nível de usuário**: um conjunto de credenciais para toda a conta do Azure. Ele pode ser utilizado para implementar no serviço de aplicações para qualquer aplicação, em qualquer subscrição, o que a conta do Azure tem permissão para aceder. É o conjunto predefinido que é apresentado no portal do GUI (como o **descrição geral** e **propriedades** da aplicação [página de recursos](../azure-resource-manager/manage-resources-portal.md#manage-resources)). Quando um utilizador é concedido acesso à aplicação através do controlo de acesso baseado em funções (RBAC) ou permissões coadmin, esse utilizador pode usar sua próprias credenciais de nível de usuário até que o acesso foi revogado. Não partilhe estas credenciais com outros utilizadores do Azure.

* **Credenciais ao nível da aplicação**: um conjunto de credenciais para cada aplicação. Ele pode ser usado para implementar essa aplicação apenas. As credenciais para cada aplicação são geradas automaticamente durante a criação de aplicações. Não é possível configurar manualmente, mas podem ser repostas em qualquer altura. Para um utilizador ter acesso às credenciais ao nível da aplicação através de (RBAC), que o utilizador tem de ser Contribuidor ou superior na aplicação. Os leitores não são permitidos para publicar e não é possível aceder essas credenciais.

## <a name="userscope"></a>Definir e repor as credenciais de nível de usuário

Pode configurar as suas credenciais de nível de usuário em qualquer aplicação [página de recursos](../azure-resource-manager/manage-resources-portal.md#manage-resources). Independentemente na aplicação que configurar estas credenciais, ele se aplica a todas as aplicações e para todas as subscrições na sua conta do Azure. 

Para configurar as suas credenciais de nível de usuário:

1. Na [portal do Azure](https://portal.azure.com), no menu à esquerda, clique em **dos serviços de aplicações** > **&lt;any_app >** > **implementação centro** > **credenciais de implementação**.

    No portal, tem de ter pelo menos uma aplicação antes de poder aceder a página de credenciais de implementação. No entanto, com o [CLI do Azure](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set), pode configurar as credenciais de nível de usuário sem uma aplicação existente.

2. Clique em **credenciais do usuário**, configure o nome de utilizador e palavra-passe e, em seguida, clique em **guardar credenciais**.

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

Depois de definir as suas credenciais de implementação, é possível encontrar o *Git* nome de utilizador de implementação na sua aplicação **descrição geral**,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

e *FTP* nome de utilizador de implementação na sua aplicação **propriedades**.

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure não mostra a palavra-passe de implementação de nível de usuário. Se se esquecer da palavra-passe, pode repor as suas credenciais ao seguir os passos nesta secção.
>
>  

## <a name="appscope"></a>Obter e repor as credenciais de ao nível da aplicação
Para obter as credenciais ao nível da aplicação:

1. Na [portal do Azure](https://portal.azure.com), no menu à esquerda, clique em **dos serviços de aplicações** > **&lt;any_app >** > **implementação centro** > **credenciais de implementação**.

2. Clique em **aplicação credenciais**e clique nas **cópia** ligação para copiar o nome de utilizador ou palavra-passe.

    ![](./media/app-service-deployment-credentials/deployment_credentials_app_level.png)

Para repor as credenciais ao nível da aplicação, clique em **repor as credenciais** na mesma caixa de diálogo.

## <a name="next-steps"></a>Passos Seguintes

Saiba como utilizar estas credenciais para implementar a aplicação a partir [local Git](deploy-local-git.md) ou a utilizar [FTP/S](deploy-ftp.md).
