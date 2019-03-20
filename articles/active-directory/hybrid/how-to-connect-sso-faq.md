---
title: 'Azure AD Connect: Totalmente integrado Single Sign-On - perguntas mais frequentes | Documentos da Microsoft'
description: Respostas a perguntas mais frequentes sobre o Azure Active Directory totalmente integrada início de sessão único.
services: active-directory
keywords: o que é o Azure AD Connect, a instalação do Active Directory, necessário componentes para o Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8de47aab231c66f3539c2d2f0f0e4c535a04038a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085376"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>O Azure Active Directory totalmente integrada início de sessão único: Perguntas mais frequentes

Neste artigo, vamos abordar perguntas mais frequentes sobre o Azure Active Directory totalmente integrada logon único (SSO totalmente integrado). Manter a verificação de volta para o novo conteúdo.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>Que métodos de início de sessão funcionam SSO totalmente integrado com?

SSO totalmente integrado pode ser combinada com ambos os [sincronização de Hash de palavra-passe](how-to-connect-password-hash-synchronization.md) ou [autenticação pass-through](how-to-connect-pta.md) métodos de início de sessão. No entanto esta funcionalidade não pode ser utilizada com o Active Directory Federation Services (ADFS).

## <a name="is-seamless-sso-a-free-feature"></a>É uma funcionalidade gratuita de SSO totalmente integrado?

SSO totalmente integrado é uma funcionalidade gratuita e não é necessário qualquer nas edições pagas do Azure AD para utilizá-lo.

## <a name="is-seamless-sso-available-in-the-microsoft-azure-germany-cloudhttpswwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>É o SSO totalmente integrado disponível na [cloud do Microsoft Azure Alemanha](https://www.microsoft.de/cloud-deutschland) e o [cloud do Microsoft Azure Government](https://azure.microsoft.com/features/gov/)?

Não. SSO totalmente integrado só está disponível na instância do Azure AD em todo o mundo.

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>Quais aplicativos aproveitar `domain_hint` ou `login_hint` capacidade de parâmetro do SSO totalmente integrado?

Abaixo encontra uma lista parcial das aplicações que pode enviar estes parâmetros para o Azure AD e, portanto, fornece aos usuários uma silenciosa experiência de logon usando o SSO totalmente integrado (ou seja, não é necessário para os seus utilizadores introduzir os nomes de utilizador ou palavras-passe):

| Nome da aplicação | URL da aplicação a ser utilizado |
| -- | -- |
| Painel de acesso | https://myapps.microsoft.com/contoso.com |
| Outlook na Web | https://outlook.office365.com/contoso.com |
| Portais do Office 365 | <https://portal.office.com?domain_hint=contoso.com>, <https://www.office.com?domain_hint=contoso.com> |

Além disso, os utilizadores obtêm uma experiência de início de sessão silenciosa se uma aplicação envia pedidos de início de sessão para os pontos finais do Azure AD configurar como os inquilinos - ou seja, https://login.microsoftonline.com/contoso.com/<..> ou https://login.microsoftonline.com/<tenant_ID>/<..> - em vez ponto de extremidade comum do Azure AD - ou seja, https://login.microsoftonline.com/common/<...>. Abaixo encontra uma lista parcial de aplicativos que realizar estes tipos de pedidos de início de sessão.

| Nome da aplicação | URL da aplicação a ser utilizado |
| -- | -- |
| SharePoint Online | https://contoso.sharepoint.com |
| Portal do Azure | https://portal.azure.com/contoso.com |

Nas tabelas acima, substitua "contoso.com" com o seu nome de domínio para obter os URLs de aplicativo adequado para o seu inquilino.

Se pretender que outras aplicações através da nossa experiência de início de sessão silenciosa, fale na secção de comentários.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Oferece suporte a SSO totalmente integrado `Alternate ID` como o nome de utilizador, em vez de `userPrincipalName`?

Sim. Suporta o SSO totalmente integrado `Alternate ID` como o nome de utilizador quando configurado no Azure AD Connect, conforme mostrado [aqui](how-to-connect-install-custom.md). Nem todas as aplicações do Office 365 suportam `Alternate ID`. Consulte a documentação do aplicativo específicas para a instrução de suporte.

## <a name="what-is-the-difference-between-the-single-sign-on-experience-provided-by-azure-ad-joinactive-directory-azureadjoin-overviewmd-and-seamless-sso"></a>O que é a diferença entre a experiência de início de sessão única fornecida pela [associação do Azure AD](../active-directory-azureadjoin-overview.md) e SSO totalmente integrado?

[Associação do Azure AD](../active-directory-azureadjoin-overview.md) fornece o SSO para os utilizadores se os dispositivos estiverem registados com o Azure AD. Estes dispositivos não precisam necessariamente ser associado a um domínio. SSO é fornecido usando *tokens de atualização principal* ou *PRTs*e não Kerberos. A experiência do usuário é ideal em dispositivos Windows 10. SSO ocorre automaticamente no browser Microsoft Edge. Ele também funciona no Chrome com o uso de uma extensão de navegador.

Pode utilizar a associação do Azure AD e SSO totalmente integrado no seu inquilino. Esses dois recursos são complementares. Se os dois recursos estão ativados, o SSO da associação do Azure AD tem precedência sobre SSO totalmente integrado.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Pretende registar dispositivos não Windows 10 no Azure AD, sem utilizar o AD FS. Posso utilizar o SSO totalmente integrado em vez disso?

Sim, este cenário tem a versão 2.1 ou posterior dos [cliente associação à área de trabalho](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>Como posso reverter sobre a chave de desencriptação do Kerberos do `AZUREADSSOACC` conta de computador?

É importante com freqüência o rollover da chave de desencriptação do Kerberos do `AZUREADSSOACC` conta de computador (que representa o Azure AD) criada no seu local floresta do AD.

>[!IMPORTANT]
>Recomendamos vivamente que efetue o rollover da chave de desencriptação do Kerberos, pelo menos, a cada 30 dias.

Siga estes passos no servidor no local onde está a executar o Azure AD Connect:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Passo 1. Obter lista de florestas do AD em que tenha sido ativado SSO totalmente integrado

1. Em primeiro lugar, transfira e instale [do Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Navegue para a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importe o módulo do PowerShell do SSO totalmente integrado com o comando: `Import-Module .\AzureADSSO.psd1`.
4. Execute o PowerShell como administrador. No PowerShell, chamar `New-AzureADSSOAuthenticationContext`. Este comando deverá dar-lhe um pop-up para introduzir as credenciais de Administrador Global do seu inquilino.
5. Chamar `Get-AzureADSSOStatus`. Este comando apresenta a lista de florestas do AD (consulte a lista de "Domínios") em que esta funcionalidade foi ativada.

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>Passo 2. Atualizar a chave de desencriptação do Kerberos em cada floresta do AD que ele foi configurá-lo no

1. Chamar `$creds = Get-Credential`. Quando lhe for pedido, introduza as credenciais de administrador de domínio de floresta do AD pretendida.

   > [!NOTE]
   > Utilizamos o nome de utilizador do administrador de domínio, fornecido em nomes de Principal utilizador (UPN) (johndoe@contoso.com) formato ou o domínio sam conta qualificado (contoso\diogoandrade ou com\johndoe) formato de nome, para localizar a floresta de AD pretendida. Se utilizar o nome qualificado de sam conta de domínio, usamos a parte do nome de utilizador de domínio [localizar o controlador de domínio, o administrador de domínio através de DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Se utilizar o UPN em vez disso, podemos [traduzi-la para um nome de sam conta qualificado do domínio](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) antes de localizar o controlador de domínio apropriadas.

2. Chamar `Update-AzureADSSOForest -OnPremCredentials $creds`. Este comando atualiza a chave de desencriptação do Kerberos para o `AZUREADSSOACC` conta de computador nesta floresta de AD específico e atualiza-o no Azure AD.
3. Repita os passos anteriores para cada floresta do AD que configurou a funcionalidade no.

>[!IMPORTANT]
>Certifique-se de que _não_ executar o `Update-AzureADSSOForest` comando mais de uma vez. Caso contrário, a funcionalidade deixa de funcionar até à hora de permissões de Kerberos dos seus utilizadores expirarem e são emitida novamente por seu Active Directory no local.

## <a name="how-can-i-disable-seamless-sso"></a>Como posso desativar o SSO totalmente integrado?

### <a name="step-1-disable-the-feature-on-your-tenant"></a>Passo 1. Desativar a funcionalidade no seu inquilino

#### <a name="option-a-disable-using-azure-ad-connect"></a>Opção a: Desativar a com o Azure AD Connect

1. Execute o Azure AD Connect, escolha **página de início de sessão da utilizador de alteração** e clique em **próxima**.
2. Desmarque os **ativar o início de sessão único em** opção. Continue através do assistente.

Depois de concluir o assistente, será desativada SSO totalmente integrado no seu inquilino. No entanto, verá uma mensagem no ecrã que é a seguinte:

"Início de sessão único está agora desativado, mas existem passos manuais adicionais para realizar para concluir a limpeza. Saiba mais"

Para concluir o processo de limpeza, siga os passos 2 e 3 no servidor no local onde está a executar o Azure AD Connect.

#### <a name="option-b-disable-using-powershell"></a>Opção b: Desativar a utilizar o PowerShell

Execute os seguintes passos no servidor no local onde está a executar o Azure AD Connect:

1. Em primeiro lugar, transfira e instale [do Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Navegue para a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importe o módulo do PowerShell do SSO totalmente integrado com o comando: `Import-Module .\AzureADSSO.psd1`.
4. Execute o PowerShell como administrador. No PowerShell, chamar `New-AzureADSSOAuthenticationContext`. Este comando deverá dar-lhe um pop-up para introduzir as credenciais de Administrador Global do seu inquilino.
5. Chamar `Enable-AzureADSSO -Enable $false`.

>[!IMPORTANT]
>Desativar o SSO totalmente integrado com o PowerShell não irá alterar o estado no Azure AD Connect. SSO totalmente integrado irá aparecer como ativada no **alterar utilizador inicie sessão** página.

### <a name="step-2-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Passo 2. Obter lista de florestas do AD em que tenha sido ativado SSO totalmente integrado

Se tiver desativado SSO totalmente integrado com o Azure AD Connect, siga tarefas 1 a 4 abaixo. Se tiver desativado SSO totalmente integrado com o PowerShell em vez disso, ir diretamente para a tarefa 5 abaixo.

1. Em primeiro lugar, transfira e instale [do Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Navegue para a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importe o módulo do PowerShell do SSO totalmente integrado com o comando: `Import-Module .\AzureADSSO.psd1`.
4. Execute o PowerShell como administrador. No PowerShell, chamar `New-AzureADSSOAuthenticationContext`. Este comando deverá dar-lhe um pop-up para introduzir as credenciais de Administrador Global do seu inquilino.
5. Chamar `Get-AzureADSSOStatus`. Este comando apresenta a lista de florestas do AD (consulte a lista de "Domínios") em que esta funcionalidade foi ativada.

### <a name="step-3-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>Passo 3. Elimine manualmente o `AZUREADSSOACCT` conta de computador de cada floresta do AD que vê listados.

## <a name="next-steps"></a>Passos Seguintes

- [**Início Rápido** ](how-to-connect-sso-quick-start.md) - colocar em funcionamento o SSO totalmente integrado do Azure AD.
- [**Technical Deep Dive** ](how-to-connect-sso-how-it-works.md) -compreender como funciona esse recurso.
- [**Resolução de problemas** ](tshoot-connect-sso.md) -Saiba como resolver problemas comuns com a funcionalidade.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – para preenchimento de pedidos de novas funcionalidades.
