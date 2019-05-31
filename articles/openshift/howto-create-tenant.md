---
title: Criar um inquilino do Azure AD para a Azure Red Hat OpenShift | Documentos da Microsoft
description: Eis como criar um inquilino do Azure Active Directory (Azure AD) para alojar o seu cluster do Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
manager: jeconnoc
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 560cdcf8a99a486c7f5177b675cff327c6fb6a41
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306453"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Criar um inquilino do Azure AD para a Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift requer uma [do Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) inquilino no qual pretende criar o cluster. R *inquilino* é uma instância dedicada do Azure AD que um programador de aplicações ou de organização recebe quando cria uma relação com a Microsoft ao inscrever-se para o Azure, o Microsoft Intune ou o Microsoft 365. Cada inquilino do Azure AD é distinto e separado do outro Azure AD tem seu próprio trabalho e identidades de instituição de ensino e registos de aplicações e inquilinos.

Se ainda não tiver um inquilino do Azure AD, siga estas instruções para criar um.

## <a name="create-a-new-azure-ad-tenant"></a>Criar um novo inquilino do Azure AD

Para criar um inquilino:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com/) com a conta que pretende associar o seu cluster do Azure Red Hat OpenShift.
2. Abra o [painel Azure Active Directory](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) para criar um novo inquilino (também conhecido como uma nova *Azure Active Directory*).
3. Fornecer uma **nome da organização**.
4. Fornecer uma **nome de domínio inicial**. Isso terá *onmicrosoft.com* acrescentado. É possível reutilizar o valor para *nome da organização* aqui.
5. Escolha um país ou região onde será criado o inquilino.
6. Clique em **Criar**.
7. Depois de criado o inquilino do Azure AD, selecione o **clique aqui para gerir o seu novo diretório** ligação. O nome do seu inquilino novo deverá ser apresentado no canto superior direito do portal do Azure:  

    ![Captura de ecrã do portal do que mostra o nome do inquilino no canto superior direito][tenantcallout]  

8. Anote o *ID de inquilino* , para que mais tarde pode especificar onde pretende criar o cluster do Azure Red Hat OpenShift. No portal, agora, deve ver o painel de descrição geral do Azure Active Directory para o novo inquilino. Selecione **propriedades** e copie o valor para seu **ID do diretório**. Nós nos referiremos a este valor como `TENANT` no [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) tutorial.

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Recursos

Confira [documentação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) para obter mais informações sobre [inquilinos do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).

## <a name="next-steps"></a>Passos Seguintes

Saiba como criar um principal de serviço, gerar um URL de retorno de chamada de autenticação e o segredo do cliente e criar um novo utilizador do Active Directory para fins de teste de aplicações no seu cluster do Azure Red Hat OpenShift.

[Criar um objeto de aplicação do Azure AD e um utilizador](howto-aad-app-configuration.md)