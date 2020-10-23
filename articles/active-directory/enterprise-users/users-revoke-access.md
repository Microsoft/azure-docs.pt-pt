---
title: Revogar o acesso dos utilizadores a uma emergência no Azure Ative Directory Microsoft Docs
description: Como revogar todos os acessos a um utilizador no Azure Ative Directory
services: active-directory
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.topic: how-to
author: curtand
ms.author: curtand
manager: daveba
ms.reviewer: krbain
ms.date: 07/15/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16577c3509c9d3d9b02ead5e69832bacc7d083bb
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92376400"
---
# <a name="revoke-user-access-in-azure-active-directory"></a>Revogar o acesso do utilizador no Azure Ative Directory

Entre os cenários que podem exigir que um administrador revogue todo o acesso a um utilizador incluem contas comprometidas, rescisão de funcionários e outras ameaças internas. Dependendo da complexidade do ambiente, os administradores podem tomar várias medidas para garantir que o acesso seja revogado. Em alguns cenários, poderá haver um período entre o início da revogação do acesso e a revogação efetiva do acesso.

Para mitigar os riscos, tem de perceber como funcionam os símbolos. Há muitos tipos de fichas, que caem num dos padrões mencionados nas secções abaixo.

## <a name="access-tokens-and-refresh-tokens"></a>Tokens de acesso e fichas de atualização

Os tokens de acesso e tokens de atualização são frequentemente usados com aplicações grossas do cliente, e também usados em aplicações baseadas no navegador, como aplicações de página única.

- Quando os utilizadores autenticam a Azure AD, as políticas de autorização são avaliadas para determinar se o utilizador pode ter acesso a um recurso específico.  

- Se autorizado, a Azure AD emite um token de acesso e um token de atualização para o recurso.  

- Os tokens de acesso emitidos pela Azure AD por padrão duram 1 hora. Se o protocolo de autenticação permitir, a aplicação pode reautenticar silenciosamente o utilizador, passando o token refresh para o AD AZure quando o token de acesso expirar.

A Azure AD reavalia então as suas políticas de autorização. Se o utilizador ainda estiver autorizado, o Azure AD emite um novo token de acesso e atualização.

Os tokens de acesso podem ser uma preocupação de segurança se o acesso tiver de ser revogado num prazo mais curto do que o tempo de vida do token, que normalmente ronda cerca de uma hora. Por esta razão, a Microsoft está a trabalhar ativamente para levar a [avaliação contínua](../conditional-access/concept-continuous-access-evaluation.md) de acesso às aplicações da Microsoft 365, o que ajuda a garantir a invalidação de tokens de acesso em tempo real.  

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

1. Desative o utilizador no Ative Directory. Consulte o [Desativado-ADAccount](/powershell/module/addsadministration/disable-adaccount?view=win10-ps).

    ```PowerShell
    Disable-ADAccount -Identity johndoe  
    ```

1. Repôs a palavra-passe do utilizador duas vezes no Ative Directory. Consulte o [Set-ADAccountPassword](/powershell/module/addsadministration/set-adaccountpassword?view=win10-ps).

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

1. Desative o utilizador em Azure AD. Consulte o [Set-AzureADUser](/powershell/module/azuread/Set-AzureADUser?view=azureadps-2.0).

    ```PowerShell
    Set-AzureADUser -ObjectId johndoe@contoso.com -AccountEnabled $false
    ```
1. Revogue os tokens de atualização Azure AD do utilizador. Consulte a [Revoke-AzureADUserAllRefreshToken](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0).

    ```PowerShell
    Revoke-AzureADUserAllRefreshToken -ObjectId johndoe@contoso.com
    ```

1. Desative os dispositivos do utilizador. Consulte a [Get-AzureADUserRegisteredDevice](/powershell/module/azuread/get-azureaduserregistereddevice?view=azureadps-2.0).

    ```PowerShell
    Get-AzureADUserRegisteredDevice -ObjectId johndoe@contoso.com | Set-AzureADDevice -AccountEnabled $false
    ```

## <a name="optional-steps"></a>Passos opcionais

- [Limpe os dados corporativos das aplicações geridas pela Intune.](/mem/intune/apps/apps-selective-wipe)

- [Limpar dispositivos corporativos está a reiniciar o dispositivo para as definições predefinidas da fábrica](/mem/intune/remote-actions/devices-wipe).

> [!NOTE]
> Os dados do dispositivo não podem ser recuperados após uma limpeza.

## <a name="when-access-is-revoked"></a>Quando o acesso é revogado

Uma vez que os administradores tenham tomado as medidas acima, o utilizador não pode obter novos tokens para qualquer aplicação ligada ao Azure Ative Directory. O tempo decorrido entre a revogação e o utilizador a perda de acesso depende da forma como a aplicação está a conceder acesso:

- Para **aplicações que utilizem fichas de acesso,** o utilizador perde acesso quando o token de acesso expira.

- Para **aplicações que utilizem fichas de sessão,** as sessões existentes terminam assim que o token expirar. Se o estado desativado do utilizador for sincronizado com a aplicação, a aplicação pode revogar automaticamente as sessões existentes do utilizador se estiver configurada para o fazer.  O tempo que demora depende da frequência de sincronização entre a aplicação e a Azure AD.

## <a name="next-steps"></a>Passos seguintes

- [Práticas de acesso seguros para administradores da AD Azure](../roles/security-planning.md)
- [Adicionar ou atualizar informações sobre o perfil do utilizador](../fundamentals/active-directory-users-profile-azure-portal.md)