---
title: Revogar o acesso dos utilizadores numa emergência no Azure Ative Directory | Microsoft Docs
description: Como revogar todos os acessos a um utilizador no Azure Ative Directory
services: active-directory
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
author: curtand
ms.author: curtand
manager: daveba
ms.reviewer: krbain
ms.date: 03/29/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 578e8f5f3126542c579cd573c82b732049d407b6
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105959828"
---
# <a name="revoke-user-access-in-azure-active-directory"></a>Revogar o acesso do utilizador no Azure Ative Directory

Cenários que podem exigir que um administrador revogue todo o acesso a um utilizador incluem contas comprometidas, rescisão de funcionários e outras ameaças internas. Dependendo da complexidade do ambiente, os administradores podem tomar várias medidas para garantir que o acesso seja revogado. Em alguns cenários, poderá haver um período entre o início da revogação do acesso e a revogação efetiva do acesso.

Para mitigar os riscos, tem de perceber como funcionam os símbolos. Há muitos tipos de fichas, que caem num dos padrões mencionados nas secções abaixo.

## <a name="access-tokens-and-refresh-tokens"></a>Tokens de acesso e fichas de atualização

Os tokens de acesso e tokens de atualização são frequentemente usados com aplicações grossas do cliente, e também usados em aplicações baseadas no navegador, como aplicações de página única.

- Quando os utilizadores autenticam a Azure AD, as políticas de autorização são avaliadas para determinar se o utilizador pode ter acesso a um recurso específico.  

- Se autorizado, a Azure AD emite um token de acesso e um token de atualização para o recurso.  

- Os tokens de acesso emitidos pela Azure AD por padrão duram 1 hora. Se o protocolo de autenticação permitir, a aplicação pode reautenticar silenciosamente o utilizador, passando o token refresh para o AD AZure quando o token de acesso expirar.

A Azure AD reavalia então as suas políticas de autorização. Se o utilizador ainda estiver autorizado, o Azure AD emite um novo token de acesso e atualiza o token.

Os tokens de acesso podem ser uma preocupação de segurança se o acesso tiver de ser revogado num prazo mais curto do que o tempo de vida do token, que normalmente ronda cerca de uma hora. Por esta razão, a Microsoft está a trabalhar ativamente para levar a [avaliação contínua](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation) de acesso às aplicações do Office 365, o que ajuda a garantir a invalidação de tokens de acesso em tempo real.  

## <a name="session-tokens-cookies"></a>Fichas de sessão (cookies)

A maioria das aplicações baseadas no navegador usam fichas de sessão em vez de acesso e tokens de atualização.  

- Quando um utilizador abre um browser e autentica uma aplicação via Azure AD, o utilizador recebe duas fichas de sessão. Um da Azure AD e outro da aplicação.  

- Uma vez que uma aplicação emite o seu próprio token de sessão, o acesso à aplicação é regido pela sessão da aplicação. Neste momento, o utilizador é afetado apenas pelas políticas de autorização que a aplicação tem conhecimento.

- As políticas de autorização da Azure AD são reavaliadas com a frequência com que a aplicação envia o utilizador de volta para a Azure AD. A reavaliação geralmente acontece silenciosamente, embora a frequência dependa de como a aplicação é configurada. É possível que a aplicação nunca possa enviar o utilizador de volta para a AZure AD, desde que o token da sessão seja válido.

- Para que um sinal de sessão seja revogado, o pedido deve revogar o acesso com base nas suas próprias políticas de autorização. A Azure AD não pode revogar diretamente um token de sessão emitido por um pedido.  

## <a name="revoke-access-for-a-user-in-the-hybrid-environment"></a>Revogar o acesso a um utilizador em ambiente híbrido

Para um ambiente híbrido com diretório ativo no local sincronizado com o Azure Ative Directory, a Microsoft recomenda que os administradores de TI tomem as seguintes ações.  

### <a name="on-premises-active-directory-environment"></a>Ambiente de Diretório Ativo no local

Como administrador no Ative Directory, ligue-se à sua rede no local, abra a PowerShell e tome as seguintes ações:

1. Desative o utilizador no Ative Directory. Consulte o [Desativado-ADAccount](https://docs.microsoft.com/powershell/module/addsadministration/disable-adaccount?view=win10-ps).

    ```PowerShell
    Disable-ADAccount -Identity johndoe  
    ```

2. Repôs a palavra-passe do utilizador duas vezes no Ative Directory. Consulte o [Set-ADAccountPassword](https://docs.microsoft.com/powershell/module/addsadministration/set-adaccountpassword?view=win10-ps).

    > [!NOTE]
    > A razão para alterar duas vezes a palavra-passe de um utilizador é mitigar o risco de passar o hash, especialmente se houver atrasos na replicação da palavra-passe no local. Se conseguir assumir com segurança que esta conta não está comprometida, pode redefinir a palavra-passe apenas uma vez.

    > [!IMPORTANT]
    > Não utilize as palavras-passe de exemplo nos seguintes cmdlets. Certifique-se de mudar as palavras-passe para uma cadeia aleatória.

    ```PowerShell
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd1" -Force)
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd2" -Force)
    ```

### <a name="azure-active-directory-environment"></a>Ambiente do Diretório Ativo Azure

Como administrador no Azure Ative Directory, abra a PowerShell, corra ``Connect-AzureAD`` e tome as seguintes ações:

1. Desative o utilizador em Azure AD. Consulte o [Set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/Set-AzureADUser?view=azureadps-2.0).

    ```PowerShell
    Set-AzureADUser -ObjectId johndoe@contoso.com -AccountEnabled $false
    ```

2. Revogue os tokens de atualização Azure AD do utilizador. Consulte a [Revoke-AzureADUserAllRefreshToken](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0).

    ```PowerShell
    Revoke-AzureADUserAllRefreshToken -ObjectId johndoe@contoso.com
    ```

3. Desative os dispositivos do utilizador. Consulte a [Get-AzureADUserRegisteredDevice](https://docs.microsoft.com/powershell/module/azuread/get-azureaduserregistereddevice?view=azureadps-2.0).

    ```PowerShell
    Get-AzureADUserRegisteredDevice -ObjectId johndoe@contoso.com | Set-AzureADDevice -AccountEnabled $false
    ```
## <a name="when-access-is-revoked"></a>Quando o acesso é revogado

Uma vez que os administradores tenham tomado as medidas acima, o utilizador não pode obter novos tokens para qualquer aplicação ligada ao Azure Ative Directory. O tempo decorrido entre a revogação e o utilizador a perda de acesso depende da forma como a aplicação está a conceder acesso:

- Para **aplicações que utilizem fichas de acesso,** o utilizador perde acesso quando o token de acesso expira.

- Para **aplicações que utilizem fichas de sessão,** as sessões existentes terminam assim que o token expirar. Se o estado desativado do utilizador for sincronizado com a aplicação, a aplicação pode revogar automaticamente as sessões existentes do utilizador se estiver configurada para o fazer.  O tempo que demora depende da frequência de sincronização entre a aplicação e a Azure AD.

## <a name="best-practices"></a>Melhores práticas

- Implementar uma solução automatizada de provisionamento e desprovisionamento. Desprovisionar os utilizadores das aplicações é uma forma eficaz de revogar o acesso, especialmente para aplicações que usam fichas de sessões. Desenvolver um processo para desprovisionar os utilizadores para apps que não suportem o fornecimento automático e a desprovisionamento. Certifique-se de que as aplicações revogam os seus próprios tokens de sessão e pare de aceitar tokens de acesso AZure AD, mesmo que ainda sejam válidos.

  - Utilize [a aplicação Azure AD SaaS Provisioning](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning). O Azure AD SaaS App Provisioning normalmente funciona automaticamente a cada 20-40 minutos. [Configurar a Ad Azure para](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) desprovisionar ou desativar utilizadores com deficiência em aplicações.
  
  - Para aplicações que não utilizem a Azure AD SaaS App Provisioning, utilize [o Identity Manager (MIM)](https://docs.microsoft.com/microsoft-identity-manager/mim-how-provision-users-adds) ou uma solução de terceiros para automatizar a desprovisionamento dos utilizadores.  
  - Identificar e desenvolver um processo para aplicações que requer desprovisionamento manual. Certifique-se de que os administradores podem executar rapidamente as tarefas manuais necessárias para desprovisionar o utilizador destas aplicações quando necessário.
  
- [Gerencie os seus dispositivos e aplicações com o Microsoft Intune.](https://docs.microsoft.com/mem/intune/remote-actions/device-management) Os dispositivos geridos por intune [podem ser reiniciados para as definições de fábrica](https://docs.microsoft.com/mem/intune/remote-actions/devices-wipe). Se o dispositivo não for gerido, pode [eliminar os dados corporativos de aplicações geridas.](https://docs.microsoft.com/mem/intune/apps/apps-selective-wipe) Estes processos são eficazes para remover dados potencialmente sensíveis dos dispositivos dos utilizadores finais. No entanto, para que qualquer dos processos seja acionado, o dispositivo deve ser ligado à internet. Se o dispositivo estiver offline, o dispositivo ainda terá acesso a quaisquer dados armazenados localmente.

> [!NOTE]
> Os dados do dispositivo não podem ser recuperados após uma limpeza.

- Utilize [o Microsoft Cloud App Security (MCAS) para bloquear o download de dados](https://docs.microsoft.com/cloud-app-security/use-case-proxy-block-session-aad) quando apropriado. Se os dados só puderem ser acedidos online, as organizações podem monitorizar as sessões e conseguir a aplicação da política em tempo real.

- Ativar [a Avaliação contínua de Acesso (CAE) em Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation). O CAE permite que os administradores revoguem os tokens da sessão e acedam a fichas de acesso para aplicações que sejam capazes de CAE.  

## <a name="next-steps"></a>Passos seguintes

- [Práticas de acesso seguros para administradores da AD Azure](https://docs.microsoft.com/azure/active-directory/roles/security-planning)
- [Adicionar ou atualizar informações sobre o perfil do utilizador](../fundamentals/active-directory-users-profile-azure-portal.md)
