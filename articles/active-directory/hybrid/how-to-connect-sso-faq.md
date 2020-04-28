---
title: 'Azure AD Connect: Single Sign-On sem emenda - Perguntas frequentes Microsoft Docs'
description: Respostas a perguntas frequentes sobre o Azure Ative Directory Seamless Single Sign-On.
services: active-directory
keywords: o que é Azure AD Connect, instalar Diretório Ativo, componentes necessários para Azure AD, SSO, Sign-on Único
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7241c8dfbedb24f95c29ea9e1c3f763218a5668d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72025672"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Ative Directory Seamless Single Sign-On: Perguntas frequentes

Neste artigo, abordamos frequentemente perguntas sobre o Azure Ative Directory Seamless Single Sign-On (Seamless SSO). Continua a verificar se há novos conteúdos.

**P: Quais os métodos de inscrição que o SSO sem emenda funciona**

O SSO sem emenda pode ser combinado com a Sincronização de [Hash password](how-to-connect-password-hash-synchronization.md) ou com os métodos de inscrição de [autenticação pass-through.](how-to-connect-pta.md) No entanto, esta funcionalidade não pode ser utilizada com serviços da Federação de Diretórios Ativos (ADFS).

**P: O SSO SSO sem emenda é uma funcionalidade gratuita?**

O SSO Sso sem emenda é uma funcionalidade gratuita e não é preciso edições pagas do Azure AD para o utilizar.

**P: O SSO Sso sem emenda está disponível na nuvem da [Microsoft Azure Germany](https://www.microsoft.de/cloud-deutschland) e na [nuvem do Governo Microsoft Azure?](https://azure.microsoft.com/features/gov/)**

Não. O SSO SSO sem emenda só está disponível no caso mundial de Azure AD.

**P: Que aplicações `domain_hint` aproveitam ou `login_hint` capacidade de parâmetro do SSO SSO sem emenda?**

Listado abaixo é uma lista não exaustiva de aplicações que podem enviar estes parâmetros para a AD Azure, e por isso fornece aos utilizadores uma experiência de inscrição silenciosa usando SSO sem emenda (isto é, não há necessidade de os seus utilizadores instarem os seus nomes de utilizador ou palavras-passe):

| Nome da aplicação | URL de aplicação a ser usado |
| -- | -- |
| Painel de acesso | https:\//myapps.microsoft.com/contoso.com |
| Outlook na Web | https:\//outlook.office365.com/contoso.com |
| Escritório 365 portais | https:\//portal.office.com?domain_hint=contoso.com,\/https: /www.office.com?domain_hint=contoso.com |

Além disso, os utilizadores obtêm uma experiência de inscrição silenciosa se uma aplicação enviar pedidos de inscrição para\/os pontos\/finais da Azure AD configurados como inquilinos - isto é, https: https: /login.microsoftonline.com/contoso.com/<..> ou https: /login.microsoftonline.com/<tenant_ID>/<.. > - em vez do ponto final comum da\/Azure AD - isto é, https: /login.microsoftonline.com/common/<...>. Na lista abaixo está uma lista não exaustiva de aplicações que fazem este tipo de pedidos de inscrição.

| Nome da aplicação | URL de aplicação a ser usado |
| -- | -- |
| SharePoint Online | https:\//contoso.sharepoint.com |
| Portal do Azure | https:\//portal.azure.com/contoso.com |

Nas tabelas acima, substitua "contoso.com" pelo seu nome de domínio para chegar aos URLs de aplicação certos para o seu inquilino.

Se quiser outras aplicações utilizando a nossa experiência de inscrição silenciosa, informe-nos na secção de feedback.

**P: O SSO `Alternate ID` SSO sem `userPrincipalName`emenda suporta como nome de utilizador, em vez de ?**

Sim. SSO sem `Alternate ID` emenda suporta como nome de utilizador quando configurado no Azure AD Connect, como mostrado [aqui](how-to-connect-install-custom.md). Nem todos os suportes `Alternate ID`de candidaturas do Office 365. Consulte a documentação da aplicação específica para a declaração de suporte.

**P: Qual é a diferença entre a única experiência de inscrição fornecida pela [Azure AD Join](../active-directory-azureadjoin-overview.md) e SSO SSO sem emenda?**

[O Azure AD Join](../active-directory-azureadjoin-overview.md) fornece SSO aos utilizadores se os seus dispositivos estiverem registados no Azure AD. Estes dispositivos não têm necessariamente de ser unidos pelo domínio. O SSO é fornecido usando *fichas de refrescamento primários* ou *PRTs,* e não Kerberos. A experiência do utilizador é a mais ideal nos dispositivos Windows 10. O SSO acontece automaticamente no navegador Microsoft Edge. Também funciona no Chrome com a utilização de uma extensão de navegador.

Você pode usar tanto Azure AD Join e Seamless SSO no seu inquilino. Estas duas características são complementares. Se ambas as funcionalidades forem ligadas, então o SSO do Azure AD Join tem precedência sobre o SSO SSO sem emenda.

**P: Quero registar dispositivos não Windows 10 com AD Azure, sem utilizar AD FS. Posso usar SSO SSO sem emenda?**

Sim, este cenário precisa da versão 2.1 ou posterior do [cliente de adesão ao local de trabalho.](https://www.microsoft.com/download/details.aspx?id=53554)

**P: Como posso passar a chave de desencriptação kerberos da conta do `AZUREADSSOACC` computador?**

É importante rolar frequentemente sobre a chave de desencriptação Kerberos da `AZUREADSSOACC` conta de computador (que representa a AD Azure) criada na sua floresta ad-local.

>[!IMPORTANT]
>Recomendamos vivamente que revire a chave de desencriptação Kerberos pelo menos a cada 30 dias.

Siga estes passos no servidor no local onde está a executar o Azure AD Connect:

   **Passo 1. Obtenha a lista de florestas aD onde o SSO Sem Emenda foi ativado**

   1. Primeiro, descarregue e [instale o Azure AD PowerShell.](https://docs.microsoft.com/powershell/azure/active-directory/overview)
   2. Navegue para a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importar o módulo SSO PowerShell `Import-Module .\AzureADSSO.psd1`sem emenda utilizando este comando: .
   4. Executar powerShell como administrador. Na PowerShell, `New-AzureADSSOAuthenticationContext`ligue. Este comando deve dar-lhe um popup para inserir as credenciais do Administrador Global do seu inquilino.
   5. Ligue. `Get-AzureADSSOStatus | ConvertFrom-Json` Este comando fornece-lhe a lista de florestas aD (veja a lista de "Domínios") em que esta funcionalidade foi ativada.

   **Passo 2. Atualizar a chave de desencriptação Kerberos em cada floresta aD em que foi configurado em**

   1. Ligue. `$creds = Get-Credential` Quando solicitado, introduza as credenciais do Administrador de Domínio para a floresta AD pretendida.

   > [!NOTE]
   >As credenciais de utilizador do administrador de domínio devem ser inseridas no formato de nome da conta SAM (contoso\johndoe ou contoso.com\johndoe). Utilizamos a parte de domínio do nome de utilizador para localizar o Controlador de Domínio do Administrador de Domínio utilizando DNS.

   >[!NOTE]
   >A conta de administrador de domínio utilizada não deve ser membro do grupo Utilizadores Protegidos. Em caso afirmativo, a operação falhará.

   2. Ligue. `Update-AzureADSSOForest -OnPremCredentials $creds` Este comando atualiza a chave de desencriptação Kerberos para a `AZUREADSSOACC` conta de computador nesta floresta ad específica e atualiza-a em Azure AD.
   3. Repita os passos anteriores para cada floresta aD em que configuraste a funcionalidade.

   >[!IMPORTANT]
   >Certifique-se _don't_ de que `Update-AzureADSSOForest` não dirige o comando mais de uma vez. Caso contrário, a funcionalidade deixa de funcionar até ao momento em que os bilhetes kerberos dos seus utilizadores expiram e são reemitidos pelo seu Diretório Ativo no local.

**P: Como posso desativar o SSO sem emenda?**

   **Passo 1. Desative a funcionalidade no seu inquilino**

   **Opção A: Desative utilizando a Ligação AD Azure**
    
   1. Executar O Azure AD Connect, escolha alterar a **página de entrada do utilizador** e clique em **Next**.
   2. Desverifique o **sinal único enable na** opção. Continue através do feiticeiro.

   Depois de completar o assistente, o SSO SSO sem emenda será desativado no seu inquilino. No entanto, verá uma mensagem no ecrã que diz o seguinte:

   "O único sinal está agora desativado, mas existem passos manuais adicionais a executar para completar a limpeza. Saiba mais"

   Para completar o processo de limpeza, siga os passos 2 e 3 no servidor no local onde está a executar o Azure AD Connect.

   **Opção B: Desative utilizando powerShell**

   Faça os seguintes passos no servidor no local onde está a executar o Azure AD Connect:

   1. Primeiro, descarregue e [instale o Azure AD PowerShell.](https://docs.microsoft.com/powershell/azure/active-directory/overview)
   2. Navegue para a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importar o módulo SSO PowerShell `Import-Module .\AzureADSSO.psd1`sem emenda utilizando este comando: .
   4. Executar powerShell como administrador. Na PowerShell, `New-AzureADSSOAuthenticationContext`ligue. Este comando deve dar-lhe um popup para inserir as credenciais do Administrador Global do seu inquilino.
   5. Ligue. `Enable-AzureADSSO -Enable $false`

   >[!IMPORTANT]
   >Desativar o SSO sem emenda utilizando o PowerShell não mudará o estado no Azure AD Connect. O SSO sem emenda mostrará como está ativado na página **de sessão de sessão** do utilizador Change.

   **Passo 2. Obtenha a lista de florestas aD onde o SSO Sem Emenda foi ativado**

   Siga as tarefas 1 a 4 abaixo se tiver desativado o SSO SSO sem emenda utilizando o Azure AD Connect. Se tiver desativado o SSO sem emenda utilizando o PowerShell, salte para a tarefa 5 abaixo.

   1. Primeiro, descarregue e [instale o Azure AD PowerShell.](https://docs.microsoft.com/powershell/azure/active-directory/overview)
   2. Navegue para a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importar o módulo SSO PowerShell `Import-Module .\AzureADSSO.psd1`sem emenda utilizando este comando: .
   4. Executar powerShell como administrador. Na PowerShell, `New-AzureADSSOAuthenticationContext`ligue. Este comando deve dar-lhe um popup para inserir as credenciais do Administrador Global do seu inquilino.
   5. Ligue. `Get-AzureADSSOStatus | ConvertFrom-Json` Este comando fornece-lhe a lista de florestas aD (veja a lista de "Domínios") em que esta funcionalidade foi ativada.

   **Passo 3. Elimine manualmente a `AZUREADSSOACCT` conta de computador de cada floresta aD que vê listada.**

## <a name="next-steps"></a>Passos seguintes

- [**Quickstart**](how-to-connect-sso-quick-start.md) - Prepare-se e execute Azure AD Seamless SSO.
- [**Mergulho Técnico Deep**](how-to-connect-sso-how-it-works.md) - Entenda como esta funcionalidade funciona.
- [**Troubleshoot**](tshoot-connect-sso.md) - Aprenda a resolver problemas comuns com a funcionalidade.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - Para arquivar novos pedidos de funcionalidades.
