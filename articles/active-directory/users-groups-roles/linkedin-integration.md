---
title: Consentimento de administrador para ligações de conta do LinkedIn - Azure Active Directory | Documentos da Microsoft
description: Explica como ativar ou desativar ligações de conta de integração do LinkedIn nas aplicações da Microsoft no Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e07c53192ea2c8b792256af944c81c9c909dc55
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368131"
---
# <a name="consent-to-linkedin-account-connections-for-an-azure-active-directory-organization"></a>Consentimento para ligações de conta do LinkedIn para uma organização do Azure Active Directory

Pode permitir que os utilizadores na sua organização para aceder às suas ligações do LinkedIn dentro de algumas aplicações da Microsoft. Não existem dados são partilhados até que os utilizadores dar consentimento para ligar as suas contas. Pode integrar a sua organização no Azure Active Directory (Azure AD) [Centro de administração](https://aad.portal.azure.com).

> [!IMPORTANT]
> As ligações de conta do LinkedIn definição é implementado para organizações do Azure AD. Quando implementar a sua organização, ele é habilitado por padrão.
> 
> Exceções:
> * A definição não está disponível para clientes que utilizam o Microsoft Cloud para administração pública dos EUA, o Microsoft Cloud Alemanha, ou o Azure e o Office 365, operado pela 21Vianet na China.
> * A definição está desativada por predefinição para os inquilinos aprovisionados na Alemanha. Tenha em atenção que a definição não está disponível para clientes que utilizam o Microsoft Cloud Alemanha.
> * A definição está desativada por predefinição para os inquilinos aprovisionados na França.
>
> Depois de ativar as ligações de conta do LinkedIn para a sua organização, as ligações de conta funcionam depois dos utilizadores dar consentimento a aplicações acedam aos dados da empresa em nome deles. Para obter informações sobre a definição de consentimento do utilizador, consulte [como remover o acesso de um utilizador a uma aplicação](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="use-the-azure-portal-to-enable-linkedin-account-connections"></a>Utilizar o portal do Azure para ativar as ligações de conta do LinkedIn

Pode ativar ligações de conta do LinkedIn para apenas os utilizadores que pretende ter acesso, a partir de toda a sua organização para apenas os utilizadores selecionados na sua organização.

1. Inicie sessão para o [Centro de administração do Azure AD](https://aad.portal.azure.com/) com uma conta que seja um administrador global para a organização do Azure AD.
2. Selecione **utilizadores**.
3. Sobre o **usuários** painel, selecione **as definições de utilizador**.
4. Sob **ligações de conta do LinkedIn**, permitir que os utilizadores liguem as contas para aceder às suas ligações do LinkedIn dentro de algumas aplicações da Microsoft. Não existem dados são partilhados até que os utilizadores dar consentimento para ligar as suas contas.

  * Selecione **Sim** para dar consentimento ao serviço para todos os utilizadores na organização
  * Selecione **selecionados** para dar consentimento para apenas os utilizadores selecionados da organização
  * Selecione **não** para retirar o consentimento para utilizadores na sua organização

    ![Integrar o LinkedIn ligações de conta da organização](./media/linkedin-integration/linkedin-integration.png)

5. Quando tiver terminado, selecione **guardar** para guardar as definições.
     
## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Utilizar a política de grupo para ativar as ligações de conta do LinkedIn

1. Transferir o [arquivos de modelo de administração do Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extrair os **ADMX** arquivos e copie-os para o armazenamento central.
3. Gerenciamento de diretiva de grupo de Open.
4. Crie um objeto de política de grupo com a definição seguinte: **Configuração do usuário** > **modelos administrativos** > **Microsoft Office 2016** > **diversas**  >  **Funcionalidades do LinkedIn Mostrar em aplicativos do Office**.
5. Selecione **habilitado** ou **desativado**.
  
   Estado | Efeito
   ------ | ------
   **Ativado** | O **funcionalidades do LinkedIn Mostrar em aplicativos do Office** nas opções do Office 2016 está habilitada. Os utilizadores na sua organização podem utilizar funcionalidades do LinkedIn em seus aplicativos do Office 2016.
   **Desativado** | O **funcionalidades do LinkedIn Mostrar em aplicativos do Office** definição nas opções do Office 2016 está desativada e os utilizadores finais não é possível alterar esta definição. Os utilizadores na sua organização não é possível utilizar funcionalidades do LinkedIn em seus aplicativos do Office 2016.

Esta política de grupo afeta apenas as aplicações do Office 2016 para um computador local. Se os utilizadores desativarem LinkedIn nas suas aplicações do Office 2016, ainda pode ver funcionalidades do LinkedIn no Office 365.

## <a name="next-steps"></a>Passos Seguintes

* [Consentimento do utilizador e dados de partilha para o LinkedIn](linkedin-user-consent.md)

* [Informações do LinkedIn e funcionalidades nas suas aplicações da Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centro de ajuda do LinkedIn](https://www.linkedin.com/help/linkedin)

* [Ver a definição de integração do LinkedIn atual no portal do Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
