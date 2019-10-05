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
ms.openlocfilehash: b12acf083e83d42ff3e8d6967d747f4bb2d93543
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960189"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory logon único contínuo: Perguntas mais frequentes

Neste artigo, abordamos perguntas frequentes sobre Azure Active Directory logon único contínuo (SSO contínuo). Continue verificando o novo conteúdo.

**P: Quais métodos de entrada fazem com que o SSO contínuo funcione com @ no__t-0

O SSO contínuo pode ser combinado com os métodos de entrada de [sincronização de hash de senha](how-to-connect-password-hash-synchronization.md) ou de autenticação de [passagem](how-to-connect-pta.md) . No entanto, esse recurso não pode ser usado com Serviços de Federação do Active Directory (AD FS) (ADFS).

**P: O SSO contínuo é um recurso gratuito?**

O SSO contínuo é um recurso gratuito e você não precisa de nenhuma edição paga do Azure AD para usá-lo.

**P: O SSO contínuo está disponível no [Microsoft Azure Alemanha Cloud](https://www.microsoft.de/cloud-deutschland) e na [nuvem de Microsoft Azure governamental](https://azure.microsoft.com/features/gov/)?**

Não. O SSO contínuo só está disponível na instância Mundial do Azure AD.

**P: Quais aplicativos tiram proveito da capacidade de parâmetro `domain_hint` ou `login_hint` do SSO contínuo?**

Veja abaixo uma lista não exaustiva de aplicativos que podem enviar esses parâmetros ao Azure AD e, portanto, fornece aos usuários uma experiência de logon silenciosa usando o SSO contínuo (ou seja, não há necessidade de que os usuários insiram seus nomes de usuário ou senhas):

| Nome da aplicação | URL do aplicativo a ser usada |
| -- | -- |
| Painel de acesso | https: \//myapps. Microsoft. com/contoso. com |
| Outlook na Web | https: \//Outlook. office365. com/contoso. com |
| Portais do Office 365 | https: \//Portal. Office. com? domain_hint = contoso. com, https: \//www. Office. com? domain_hint = contoso. com |

Além disso, os usuários terão uma experiência de logon silenciosa se um aplicativo enviar solicitações de entrada para os pontos de extremidade do Azure AD configurados como locatários, ou seja, https: \//login. microsoftonline. com/contoso. com/<.. > ou https: \//login. microsoftonline. com/< tenant_ID >/<.. >-em vez do ponto de extremidade comum do AD do Azure, ou seja, https: \//login. microsoftonline. com/Common/<... >. Veja abaixo uma lista não exaustiva de aplicativos que fazem esses tipos de solicitações de entrada.

| Nome da aplicação | URL do aplicativo a ser usada |
| -- | -- |
| SharePoint Online | https: \//contoso. SharePoint. com |
| Portal do Azure | https: \//Portal. Azure. com/contoso. com |

Nas tabelas acima, substitua "contoso.com" pelo nome de domínio para obter as URLs de aplicativo corretas para seu locatário.

Se você quiser outros aplicativos usando nossa experiência de logon silencioso, informe-nos na seção de comentários.

**P: O SSO contínuo dá suporte a `Alternate ID` como o nome de usuário, em vez de `userPrincipalName`?**

Sim. O SSO contínuo dá suporte a `Alternate ID` como o nome de usuário quando configurado em Azure AD Connect como mostrado [aqui](how-to-connect-install-custom.md). Nem todos os aplicativos do Office 365 dão suporte a `Alternate ID`. Consulte a documentação do aplicativo específico para a instrução de suporte.

**P: Qual é a diferença entre a experiência de logon único fornecida pelo [ingresso do Azure ad](../active-directory-azureadjoin-overview.md) e SSO contínuo?**

O [ingresso no Azure ad](../active-directory-azureadjoin-overview.md) fornece o SSO para os usuários se seus dispositivos estiverem registrados no Azure AD. Esses dispositivos não precisam necessariamente ser ingressados no domínio. O SSO é fornecido usando *tokens de atualização primários* ou *PRTs*, e não Kerberos. A experiência do usuário é ideal para dispositivos Windows 10. O SSO ocorre automaticamente no navegador Microsoft Edge. Ele também funciona no Chrome com o uso de uma extensão de navegador.

Você pode usar a junção do Azure AD e o SSO contínuo em seu locatário. Esses dois recursos são complementares. Se ambos os recursos estiverem ativados, o SSO do ingresso no Azure AD terá precedência sobre o SSO contínuo.

**P: Quero registrar dispositivos que não são do Windows 10 com o Azure AD, sem usar AD FS. Em vez disso, posso usar o SSO contínuo?**

Sim, este cenário precisa da versão 2,1 ou posterior do [cliente de ingresso no local de trabalho](https://www.microsoft.com/download/details.aspx?id=53554).

**P: Como posso sobrepor a chave de descriptografia Kerberos da conta de computador `AZUREADSSOACC`?**

É importante sobrepor frequentemente a chave de descriptografia Kerberos da conta de computador `AZUREADSSOACC` (que representa o Azure AD) criada em sua floresta do AD local.

>[!IMPORTANT]
>É altamente recomendável que você reverta a chave de descriptografia Kerberos pelo menos a cada 30 dias.

Siga estas etapas no servidor local em que você está executando Azure AD Connect:

   **Step 1. Obter lista de florestas do AD em que o SSO contínuo foi habilitado @ no__t-0

   1. Primeiro, baixe e instale o [PowerShell do Azure ad](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Navegue para a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importe o módulo do PowerShell do SSO contínuo usando este comando: `Import-Module .\AzureADSSO.psd1`.
   4. Execute o PowerShell como administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Esse comando deve fornecer a você um pop-up para inserir as credenciais de administrador global do seu locatário.
   5. Chame `Get-AzureADSSOStatus | ConvertFrom-Json`. Esse comando fornece a lista de florestas do AD (consulte a lista "domínios") na qual esse recurso foi habilitado.

   **Step 2. Atualize a chave de descriptografia Kerberos em cada floresta do AD na qual ela foi configurada em @ no__t-0

   1. Chame `$creds = Get-Credential`. Quando solicitado, insira as credenciais de administrador de domínio para a floresta do AD pretendida.

   > [!NOTE]
   > Usamos o nome de usuário do administrador de domínio, fornecido no formato UPN (nomes de entidade de usuário) (johndoe@contoso.com) ou no formato Sam (nome da conta de domínio \ contoso\johndoe ou contoso. com\johndoe) qualificado para encontrar a floresta do AD pretendida. Se você usar o nome de conta Sam qualificado pelo domínio, usaremos a parte de domínio do nome de usuário para [localizar o controlador de domínio do administrador de domínio usando DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Se você usar o UPN, [ele será traduzido para um nome de conta Sam qualificado pelo domínio](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) antes de localizar o controlador de domínio apropriado.

   2. Chame `Update-AzureADSSOForest -OnPremCredentials $creds`. Esse comando atualiza a chave de descriptografia Kerberos para a conta de computador `AZUREADSSOACC` nesta floresta específica do AD e a atualiza no Azure AD.
   3. Repita as etapas anteriores para cada floresta do AD na qual você configurou o recurso.

   >[!IMPORTANT]
   >Certifique-se de _não_ executar o comando `Update-AzureADSSOForest` mais de uma vez. Caso contrário, o recurso para de funcionar até o momento em que os tíquetes Kerberos de seus usuários expiram e são reemitidos pelo seu Active Directory local.

**P: Como posso desabilitar o SSO contínuo?**

   **Step 1. Desabilitar o recurso em seu locatário @ no__t-0

   **Option A: Desabilitar usando Azure AD Connect @ no__t-0
    
   1. Execute Azure AD Connect, escolha **alterar página de entrada do usuário** e clique em **Avançar**.
   2. Desmarque a opção **habilitar logon único** . Prossiga com o assistente.

   Depois de concluir o assistente, o SSO contínuo será desabilitado em seu locatário. No entanto, você verá uma mensagem na tela que lê da seguinte maneira:

   "O logon único agora está desabilitado, mas há etapas manuais adicionais a serem executadas para concluir a limpeza. Saiba mais "

   Para concluir o processo de limpeza, siga as etapas 2 e 3 no servidor local em que você está executando o Azure AD Connect.

   **Option B: Desabilitar usando o PowerShell @ no__t-0

   Execute as seguintes etapas no servidor local em que você está executando Azure AD Connect:

   1. Primeiro, baixe e instale o [PowerShell do Azure ad](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Navegue para a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importe o módulo do PowerShell do SSO contínuo usando este comando: `Import-Module .\AzureADSSO.psd1`.
   4. Execute o PowerShell como administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Esse comando deve fornecer a você um pop-up para inserir as credenciais de administrador global do seu locatário.
   5. Chame `Enable-AzureADSSO -Enable $false`.

   >[!IMPORTANT]
   >Desabilitar o SSO contínuo usando o PowerShell não alterará o estado em Azure AD Connect. O SSO contínuo será exibido como habilitado na página **alterar entrada do usuário** .

   **Step 2. Obter lista de florestas do AD em que o SSO contínuo foi habilitado @ no__t-0

   Siga as tarefas 1 a 4 abaixo se você tiver desabilitado o SSO contínuo usando o Azure AD Connect. Se você tiver desabilitado o SSO contínuo usando o PowerShell, vá adiante para a tarefa 5 abaixo.

   1. Primeiro, baixe e instale o [PowerShell do Azure ad](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Navegue para a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importe o módulo do PowerShell do SSO contínuo usando este comando: `Import-Module .\AzureADSSO.psd1`.
   4. Execute o PowerShell como administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Esse comando deve fornecer a você um pop-up para inserir as credenciais de administrador global do seu locatário.
   5. Chame `Get-AzureADSSOStatus | ConvertFrom-Json`. Esse comando fornece a lista de florestas do AD (consulte a lista "domínios") na qual esse recurso foi habilitado.

   **Step 3. Exclua manualmente a conta de computador `AZUREADSSOACCT` de cada floresta do AD que você vê listada.**

## <a name="next-steps"></a>Passos seguintes

- [**Início rápido**](how-to-connect-sso-quick-start.md) -obtenha e execute o SSO contínuo do Azure AD.
- [**Aprofundamento técnico**](how-to-connect-sso-how-it-works.md) – entenda como esse recurso funciona.
- [**Solução de problemas**](tshoot-connect-sso.md) -saiba como resolver problemas comuns com o recurso.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) -para o arquivamento de novas solicitações de recursos.
