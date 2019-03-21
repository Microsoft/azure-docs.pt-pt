---
title: Consentimento para serviços do LinkedIn para a sua organização - Azure Active Directory | Documentos da Microsoft
description: Explica como ativar ou desativar a integração do LinkedIn para aplicações da Microsoft no Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: abcb1696efe44293d01153aa37a9835ba5f43370
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199703"
---
# <a name="consent-to-linkedin-integration-for-your-azure-active-directory-organization"></a>Dar consentimento a integração do LinkedIn para a sua organização do Azure Active Directory

Neste artigo, pode saber como ativar ou desativar a integração do LinkedIn para a sua organização no Centro de administração do Azure Active Directory (Azure AD).

> [!IMPORTANT]
> A definição de integração do LinkedIn está atualmente a ser implementada em organizações do Azure AD. Quando implementar a sua organização, ele é habilitado por padrão.
> 
> Exceções:
> * A definição não está disponível para clientes que utilizam o Microsoft Cloud para administração pública dos EUA, o Microsoft Cloud Alemanha, ou o Azure e o Office 365, operado pela 21Vianet na China.
> * A definição está desativada por predefinição para os inquilinos aprovisionados na Alemanha. Tenha em atenção que a definição não está disponível para clientes que utilizam o Microsoft Cloud Alemanha.
> * A definição está desativada por predefinição para os inquilinos aprovisionados na França.
>
> A integração funciona somente se tiver habilitado *e* depois dos utilizadores dar consentimento a aplicações acedam aos dados da empresa em nome deles. Para obter informações sobre a definição de consentimento do utilizador, consulte [como remover o acesso de um utilizador a uma aplicação](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-the-azure-portal"></a>Ativar ou desativar a integração do LinkedIn para os seus utilizadores no portal do Azure

Pode ativar ou desativar a integração do LinkedIn para todo o seu inquilino ou para apenas os utilizadores selecionados no seu inquilino.

1. Inicie sessão para o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/) com uma conta que seja um administrador global do inquilino do Azure AD.
2. Selecione **utilizadores**.
3. Sobre o **usuários** painel, selecione **as definições de utilizador**.
4. Sob **ligações de conta do LinkedIn**:

   * Selecione **Sim** para dar consentimento para todos os utilizadores na organização ligar as suas contas para obter acesso aos seus contatos do LinkedIn dentro de algumas aplicações da Microsoft.
   * Selecione **selecionados** para dar consentimento para apenas utilizadores selecionados da organização ligar as suas contas para obter acesso aos seus contatos do LinkedIn dentro de algumas aplicações da Microsoft.
   * Selecione **não** para retirar o consentimento para utilizadores na sua organização para ligar as suas contas para obter acesso aos seus contatos do LinkedIn dentro de algumas aplicações da Microsoft.
    ![Ativar a integração do LinkedIn da organização](./media/linkedin-integration/linkedin-integration.png)
5. Guardar as definições, quando tiver terminado, selecionando **guardar**.

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-group-policy"></a>Ativar ou desativar a integração do LinkedIn para os seus utilizadores na diretiva de grupo

1. Transferir o [arquivos de modelo de administração do Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Extrair os **ADMX** arquivos e copie-os para o armazenamento central.
3. Gerenciamento de diretiva de grupo de Open.
4. Crie um objeto de política de grupo com a definição seguinte: **Configuração do usuário** > **modelos administrativos** > **Microsoft Office 2016** > **diversas**  >  **Funcionalidades do LinkedIn Mostrar em aplicativos do Office**.
5. Selecione **habilitado** ou **desativado**.
  
   Estado | Efeito
   ------ | ------
   **Ativado** | O **funcionalidades do LinkedIn Mostrar em aplicativos do Office** nas opções do Office 2016 está habilitada. Os utilizadores na sua organização podem utilizar funcionalidades do LinkedIn nas aplicações do Office.
   **Desativado** | O **funcionalidades do LinkedIn Mostrar em aplicativos do Office** definição nas opções do Office 2016 está desativada e os utilizadores finais não é possível alterar esta definição. Os utilizadores na sua organização não é possível utilizar funcionalidades do LinkedIn em seus aplicativos do Office 2016.

Esta política de grupo afeta apenas as aplicações do Office 2016 para um computador local. Os utilizadores podem ver as funcionalidades de LinkedIn em cartões de perfil em todo o Office 365, mesmo que elas desativam LinkedIn nas suas aplicações do Office 2016.

## <a name="learn-more"></a>Saiba mais

* [Integrar o LinkedIn na sua organização](linkedin-user-consent.md)

* [Informações do LinkedIn e funcionalidades nas suas aplicações da Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centro de ajuda do LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Passos Seguintes

Utilize a seguinte ligação para ver a definição de integração do LinkedIn atual no portal do Azure:

[Ver a definição de integração do LinkedIn atual no portal do Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
