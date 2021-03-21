---
title: Crie um inquilino Azure AD para Azure Red Hat OpenShift
description: Eis como criar um inquilino Azure Ative Directory (Azure AD) para hospedar o seu cluster Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: b98f02adeb850f16127658c7d02d44754512e216
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100635014"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Crie um inquilino Azure AD para Azure Red Hat OpenShift

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 será retirado a 30 de junho de 2022. O apoio à criação de novos clusters Azure Red Hat OpenShift 3.11 continua até 30 de novembro de 2020. Após a reforma, os restantes clusters Azure Red Hat OpenShift 3.11 serão encerrados para evitar vulnerabilidades de segurança.
> 
> Siga este guia para [criar um cluster Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Se tiver questões específicas, [contacte-nos.](mailto:arofeedback@microsoft.com)

O Microsoft Azure Red Hat OpenShift requer um inquilino [do Azure Ative Directory (Azure AD)](../active-directory/develop/quickstart-create-new-tenant.md) no qual criar o seu cluster. Um *inquilino* é um exemplo dedicado do Azure AD que uma organização ou programador de aplicações recebe quando criam uma relação com a Microsoft ao inscreverem-se no Azure, Microsoft Intune ou Microsoft 365. Cada inquilino da AZure AD é distinto e separado de outros inquilinos da AD Azure e tem o seu próprio trabalho e identidades escolares e registos de aplicações.

Se ainda não tem um inquilino da AD Azure, siga estas instruções para criar um.

## <a name="create-a-new-azure-ad-tenant"></a>Criar um novo inquilino do Azure AD

Para criar um inquilino:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando a conta que pretende associar ao seu cluster Azure Red Hat OpenShift.
2. Abra a [lâmina do Diretório Ativo Azure](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) para criar um novo inquilino (também conhecido como novo *Diretório Ativo Azure).*
3. Fornecer um **nome de Organização**.
4. Fornecer um **nome de domínio inicial**. Isto terá *onmicrosoft.com* anexado a ele. Pode reutilizar o valor do *nome da Organização* aqui.
5. Escolha um país ou região onde o inquilino será criado.
6. Clique em **Criar**.
7. Depois da criação do seu inquilino Azure AD, selecione o **Click aqui para gerir o seu novo** link de diretório. O seu novo nome de inquilino deve ser apresentado no canto superior direito do portal Azure:  

    ![Screenshot do portal mostrando o nome do inquilino no canto superior direito][tenantcallout]  

8. Tome nota do *ID* do inquilino para que possa especificar mais tarde onde criar o seu cluster Azure Red Hat OpenShift. No portal, deverá agora ver a lâmina geral do Azure Ative Directory para o seu novo inquilino. Selecione **Propriedades** e copie o valor para o seu **ID do Diretório.** Vamos referir-nos a este valor como `TENANT` no tutorial de cluster [OpenShift do Chapéu Vermelho Azure.](tutorial-create-cluster.md)

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Recursos

Consulte a [documentação do Azure Ative Directory](../active-directory/index.yml) para obter mais informações sobre [os inquilinos da AD Azure.](../active-directory/develop/quickstart-create-new-tenant.md)

## <a name="next-steps"></a>Passos seguintes

Aprenda a criar um principal de serviço, gere um URL de callback secreto e de autenticação do cliente e crie um novo utilizador ative Directory para testar aplicações no seu cluster Azure Red Hat OpenShift.

[Criar um objeto e utilizador de aplicação do Azure AD](howto-aad-app-configuration.md)