---
title: 'Azure AD Connect: Logon único contínuo-perguntas frequentes | Microsoft Docs'
description: Respostas para perguntas frequentes sobre Azure Active Directory logon único contínuo.
services: active-directory
keywords: o que é Azure AD Connect, instalar Active Directory, componentes necessários para o Azure AD, SSO, logon único
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
ms.openlocfilehash: 96d0253e1e656f4bdb5180af0d57824aceb4f0dd
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2019
ms.locfileid: "71176656"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory logon único contínuo: Perguntas mais frequentes

Neste artigo, abordamos perguntas frequentes sobre Azure Active Directory logon único contínuo (SSO contínuo). Continue verificando o novo conteúdo.

**P: Com quais métodos de entrada o SSO contínuo funciona com**

O SSO contínuo pode ser combinado com os métodos de entrada de [sincronização de hash de senha](how-to-connect-password-hash-synchronization.md) ou de autenticação de [passagem](how-to-connect-pta.md) . No entanto, esse recurso não pode ser usado com Serviços de Federação do Active Directory (AD FS) (ADFS).

**P: O SSO contínuo é um recurso gratuito?**

O SSO contínuo é um recurso gratuito e você não precisa de nenhuma edição paga do Azure AD para usá-lo.

**P: O SSO contínuo está disponível no [Microsoft Azure Alemanha Cloud](https://www.microsoft.de/cloud-deutschland) e na [Microsoft Azure governamental Cloud](https://azure.microsoft.com/features/gov/)?**

Não. O SSO contínuo só está disponível na instância Mundial do Azure AD.

**P: Quais aplicativos tiram proveito `domain_hint` ou `login_hint` capacidade de parâmetro do SSO contínuo?**

Veja abaixo uma lista não exaustiva de aplicativos que podem enviar esses parâmetros ao Azure AD e, portanto, fornece aos usuários uma experiência de logon silenciosa usando o SSO contínuo (ou seja, não há necessidade de que os usuários insiram seus nomes de usuário ou senhas):

| Nome da aplicação | URL do aplicativo a ser usada |
| -- | -- |
| Painel de acesso | https:\//myapps.Microsoft.com/contoso.com |
| Outlook na Web | https:\//Outlook.office365.com/contoso.com |
| Portais do Office 365 | https:\//Portal.Office.com? domain_hint = contoso. com, https:\//www.Office.com? domain_hint = contoso. com |

Além disso, os usuários terão uma experiência de logon silenciosa se um aplicativo enviar solicitações de entrada para os pontos de extremidade do Azure ad configurados como locatários, ou seja, https\/:/login.microsoftonline.com/contoso.com/<.. > ou https:\//login.microsoftonline.com/<tenant_ID>/<.. >-em vez do ponto de extremidade comum do Azure AD, ou seja\/, https:/login.microsoftonline.com/Common/<... >. Veja abaixo uma lista não exaustiva de aplicativos que fazem esses tipos de solicitações de entrada.

| Nome da aplicação | URL do aplicativo a ser usada |
| -- | -- |
| SharePoint Online | https:\//contoso.SharePoint.com |
| Portal do Azure | https:\//Portal.Azure.com/contoso.com |

Nas tabelas acima, substitua "contoso.com" pelo nome de domínio para obter as URLs de aplicativo corretas para seu locatário.

Se você quiser outros aplicativos usando nossa experiência de logon silencioso, informe-nos na seção de comentários.

**P: O SSO contínuo é `Alternate ID` compatível como o nome de usuário `userPrincipalName`, em vez de?**

Sim. O SSO contínuo `Alternate ID` é compatível como o nome de usuário quando configurado no Azure ad Connect, conforme mostrado [aqui](how-to-connect-install-custom.md). Nem todos os aplicativos do Office `Alternate ID`365 dão suporte ao. Consulte a documentação do aplicativo específico para a instrução de suporte.

**P: Qual é a diferença entre a experiência de logon único fornecida pelo [ingresso do Azure ad](../active-directory-azureadjoin-overview.md) e o SSO contínuo?**

O [ingresso no Azure ad](../active-directory-azureadjoin-overview.md) fornece o SSO para os usuários se seus dispositivos estiverem registrados no Azure AD. Esses dispositivos não precisam necessariamente ser ingressados no domínio. O SSO é fornecido usando *tokens de atualização primários* ou *PRTs*, e não Kerberos. A experiência do usuário é ideal para dispositivos Windows 10. O SSO ocorre automaticamente no navegador Microsoft Edge. Ele também funciona no Chrome com o uso de uma extensão de navegador.

Você pode usar a junção do Azure AD e o SSO contínuo em seu locatário. Esses dois recursos são complementares. Se ambos os recursos estiverem ativados, o SSO do ingresso no Azure AD terá precedência sobre o SSO contínuo.

**P: Quero registrar dispositivos que não são do Windows 10 com o Azure AD, sem usar AD FS. Em vez disso, posso usar o SSO contínuo?**

Sim, este cenário precisa da versão 2,1 ou posterior do [cliente de ingresso no local de trabalho](https://www.microsoft.com/download/details.aspx?id=53554).

**P: Como posso sobrepor a chave de descriptografia Kerberos da `AZUREADSSOACC` conta do computador?**

É importante passar com frequência a chave de descriptografia Kerberos da `AZUREADSSOACC` conta de computador (que representa o Azure AD) criada em sua floresta do AD local.

>[!IMPORTANT]
>É altamente recomendável que você reverta a chave de descriptografia Kerberos pelo menos a cada 30 dias.

Siga estas etapas no servidor local em que você está executando Azure AD Connect:

    **Step 1. Get list of AD forests where Seamless SSO has been enabled**

    1. Primeiro, baixe e instale o [PowerShell do Azure ad](https://docs.microsoft.com/powershell/azure/active-directory/overview).
    2. Navegue para a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
    3. Importe o módulo do PowerShell do SSO contínuo usando este `Import-Module .\AzureADSSO.psd1`comando:.
    4. Execute o PowerShell como administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Esse comando deve fornecer a você um pop-up para inserir as credenciais de administrador global do seu locatário.
    5. Chame `Get-AzureADSSOStatus | ConvertFrom-Json`. Esse comando fornece a lista de florestas do AD (consulte a lista "domínios") na qual esse recurso foi habilitado.

    **Etapa 2. Atualizar a chave de descriptografia Kerberos em cada floresta do AD na qual ela foi configurada**

    1. Chame `$creds = Get-Credential`. Quando solicitado, insira as credenciais de administrador de domínio para a floresta do AD pretendida.

    > [!NOTE]
    > Usamos o nome de usuário do administrador de domínio, fornecido no formato UPN (nome principal dojohndoe@contoso.comusuário) () ou no formato Sam qualificado do domínio (contoso\johndoe ou contoso. com\johndoe) para localizar a floresta do AD pretendida. Se você usar o nome de conta Sam qualificado pelo domínio, usaremos a parte de domínio do nome de usuário para [localizar o controlador de domínio do administrador de domínio usando DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Se você usar o UPN, [ele será traduzido para um nome de conta Sam qualificado pelo domínio](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) antes de localizar o controlador de domínio apropriado.

    2. Chame `Update-AzureADSSOForest -OnPremCredentials $creds`. Esse comando atualiza a chave de descriptografia Kerberos para `AZUREADSSOACC` a conta de computador nesta floresta específica do AD e a atualiza no Azure AD.
    3. Repita as etapas anteriores para cada floresta do AD na qual você configurou o recurso.

    >[!IMPORTANT]
    >Certifique-se de _não_ executar `Update-AzureADSSOForest` o comando mais de uma vez. Caso contrário, o recurso para de funcionar até o momento em que os tíquetes Kerberos de seus usuários expiram e são reemitidos pelo seu Active Directory local.

**P: Como posso desabilitar o SSO contínuo?**

    **Step 1. Disable the feature on your tenant**

        **Option A: Disable using Azure AD Connect**

        1. Run Azure AD Connect, choose **Change user sign-in page** and click **Next**.
        2. Uncheck the **Enable single sign on** option. Continue through the wizard.

        After completing the wizard, Seamless SSO will be disabled on your tenant. However, you will see a message on screen that reads as follows:

        "Single sign-on is now disabled, but there are additional manual steps to perform in order to complete clean-up. Learn more"

        To complete the clean-up process, follow steps 2 and 3 on the on-premises server where you are running Azure AD Connect.

        **Option B: Disable using PowerShell**

        Run the following steps on the on-premises server where you are running Azure AD Connect:

        1. First, download, and install [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
        2. Navigate to the `%programfiles%\Microsoft Azure Active Directory Connect` folder.
        3. Import the Seamless SSO PowerShell module using this command: `Import-Module .\AzureADSSO.psd1`.
        4. Run PowerShell as an Administrator. In PowerShell, call `New-AzureADSSOAuthenticationContext`. This command should give you a popup to enter your tenant's Global Administrator credentials.
        5. Call `Enable-AzureADSSO -Enable $false`.

        >[!IMPORTANT]
        >Disabling Seamless SSO using PowerShell will not change the state in Azure AD Connect. Seamless SSO will show as enabled in the **Change user sign-in** page.

    **Step 2. Get list of AD forests where Seamless SSO has been enabled**

    Follow tasks 1 through 4 below if you have disabled Seamless SSO using Azure AD Connect. If you have disabled Seamless SSO using PowerShell instead, jump ahead to task 5 below.

    1. Primeiro, baixe e instale o [PowerShell do Azure ad](https://docs.microsoft.com/powershell/azure/active-directory/overview).
    2. Navegue para a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
    3. Importe o módulo do PowerShell do SSO contínuo usando este `Import-Module .\AzureADSSO.psd1`comando:.
    4. Execute o PowerShell como administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Esse comando deve fornecer a você um pop-up para inserir as credenciais de administrador global do seu locatário.
    5. Chame `Get-AzureADSSOStatus | ConvertFrom-Json`. Esse comando fornece a lista de florestas do AD (consulte a lista "domínios") na qual esse recurso foi habilitado.

    **Etapa 3. Exclua manualmente `AZUREADSSOACCT` a conta de computador de cada floresta do AD que você vê listada.**

## <a name="next-steps"></a>Passos seguintes

- [**Início rápido**](how-to-connect-sso-quick-start.md) -obtenha e execute o SSO contínuo do Azure AD.
- [**Aprofundamento técnico**](how-to-connect-sso-how-it-works.md) – entenda como esse recurso funciona.
- [**Solução de problemas**](tshoot-connect-sso.md) -saiba como resolver problemas comuns com o recurso.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) -para o arquivamento de novas solicitações de recursos.
