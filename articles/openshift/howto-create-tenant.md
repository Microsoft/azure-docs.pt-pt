---
title: Crie um inquilino da AD Azure para o Azure Red Hat OpenShift
description: Eis como criar um inquilino azure Ative Directory (Azure AD) para acolher o seu cluster OpenShift do Microsoft Azure Red Hat.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ad03538cafcce9c1d660d0f2ac5eb3c6ae5f4f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243695"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Crie um inquilino da AD Azure para o Azure Red Hat OpenShift

O Microsoft Azure Red Hat OpenShift requer um inquilino do [Azure Ative Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) para criar o seu cluster. Um *inquilino* é um caso dedicado de Azure AD que uma organização ou desenvolvedor de aplicações recebe quando cria uma relação com a Microsoft ao inscrever-se no Azure, Microsoft Intune ou Microsoft 365. Cada inquilino da Azure AD é distinto e separado de outros inquilinos da AD Azure e tem o seu próprio trabalho e identidades escolares e registos de aplicações.

Se ainda não tem um inquilino da AD Azure, siga estas instruções para criar uma.

## <a name="create-a-new-azure-ad-tenant"></a>Criar um novo inquilino do Azure AD

Para criar um inquilino:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando a conta que pretende associar ao seu cluster Azure Red Hat OpenShift.
2. Abra a [lâmina azure Ative Diretório](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) para criar um novo inquilino (também conhecido como um novo *Diretório Ativo Azure).*
3. Forneça um nome de **Organização**.
4. Forneça um nome de **domínio inicial**. Isto terá *onmicrosoft.com* anexado a ele. Pode reutilizar o valor para o nome da *Organização* aqui.
5. Escolha um país ou região onde o inquilino será criado.
6. Clique em **Criar**.
7. Depois de criado o seu inquilino Azure AD, selecione o **Click aqui para gerir o seu novo link de diretório.** O seu novo nome de inquilino deve ser exibido na parte superior direita do portal Azure:  

    ![Screenshot do portal mostrando o nome do inquilino no canto superior direito][tenantcallout]  

8. Tome nota do ID do *inquilino* para que possa especificar mais tarde onde criar o seu cluster Azure Red Hat OpenShift. No portal, deverá agora ver a lâmina de visão geral do Azure Ative Diretório para o seu novo inquilino. Selecione **Propriedades** e copie o valor para o seu **ID de Diretório**. Vamos referir-nos a `TENANT` este valor como no tutorial de [cluster Create a Azure Red Hat OpenShift.](tutorial-create-cluster.md)

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Recursos

Consulte a [documentação do Diretório Ativo azure](https://docs.microsoft.com/azure/active-directory/) para obter mais informações sobre [os inquilinos da AD Azure.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

## <a name="next-steps"></a>Passos seguintes

Aprenda a criar um diretor de serviço, gere um URL de callback secreto e autenticado do cliente e crie um novo utilizador ative Directy para testar aplicações no seu cluster Azure Red Hat OpenShift.

[Criar um objeto e utilizador de aplicação do Azure AD](howto-aad-app-configuration.md)
