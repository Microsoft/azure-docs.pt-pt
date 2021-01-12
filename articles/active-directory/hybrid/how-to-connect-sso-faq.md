---
title: 'Azure AD Connect: Single Sign-On sem emenda - Perguntas frequentes Microsoft Docs'
description: Respostas a perguntas frequentes sobre O Diretório Ativo sem emenda sem emenda.
services: active-directory
keywords: o que é Azure AD Connect, instalar Ative Directory, componentes necessários para Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dac4b461d4506015f0ef374eae37f67c445791d
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98107876"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Ative Directory Seamless Single Sign-On: Perguntas frequentes

Neste artigo, abordamos perguntas frequentes sobre O Azure Ative Directory Seamless Single Sign-On (Seamless SSO). Continua a verificar se há novos conteúdos.

**Q: Que métodos de inscrição funcionam sem emenda SSO com**

O SSO sem emenda pode ser combinado com os métodos de [sincronização de hash de palavra-passe](how-to-connect-password-hash-synchronization.md) ou com os métodos [de autenticação pass-through.](how-to-connect-pta.md) No entanto, esta funcionalidade não pode ser utilizada com serviços da Federação de Diretórios Ativos (ADFS).

**P: Seamless SSO é uma funcionalidade gratuita?**

Seamless SSO é uma funcionalidade gratuita e você não precisa de edições pagas de Azure AD para usá-lo.

**P: O SSO sem emenda está disponível na nuvem do [Microsoft Azure Germany](https://www.microsoft.de/cloud-deutschland) e na nuvem do [Governo Microsoft Azure](https://azure.microsoft.com/features/gov/)?**

O SSO sem emenda está disponível para a nuvem do Governo Azure. Para mais detalhes, consulte [As Considerações de Identidade Híbrida para o Governo Azure](./reference-connect-government-cloud.md).

**P: Que aplicações tiram partido ou capacidade de `domain_hint` `login_hint` parâmetro de SSO sem emenda?**

Listado abaixo está uma lista não exaustiva de aplicações que podem enviar estes parâmetros para Azure AD, e, portanto, fornece aos utilizadores uma experiência de s-on silenciosa usando SSO sem emenda (ou seja, não há necessidade de os seus utilizadores inserirem os seus nomes de utilizador ou palavras-passe):

| Nome da aplicação | URL de aplicação a utilizar |
| -- | -- |
| Painel de acesso | https: \/ /myapps.microsoft.com/contoso.com |
| Perspetivas na Web | https: \/ /outlook.office365.com/contoso.com |
| Portais do Office 365 | https: \/ /portal.office.com?domain_hint=contoso.com, https: \/ /www.office.com?domain_hint=contoso.com |

Além disso, os utilizadores obtêm uma experiência de inscrição silenciosa se uma aplicação enviar pedidos de inscrição para os pontos finais da Azure AD configurados como inquilinos - isto é, https: \/ /login.microsoftonline.com/contoso.com/<..> ou https: \/ /login.microsoftonline.com/<tenant_ID>/<.. > - em vez do ponto final comum da AD AD - isto é, https: \/ /login.microsoftonline.com/common/<...>. Listado abaixo está uma lista não exaustiva de aplicações que fazem este tipo de pedidos de inscrição.

| Nome da aplicação | URL de aplicação a utilizar |
| -- | -- |
| SharePoint Online | https: \/ /contoso.sharepoint.com |
| Portal do Azure | https: \/ /portal.azure.com/contoso.com |

Nas tabelas acima, substitua o "contoso.com" pelo seu nome de domínio para chegar aos URLs de aplicação certos para o seu inquilino.

Se quiser outras aplicações utilizando a nossa experiência de inscrição silenciosa, informe-nos na secção de feedback.

**P: O SSO sem emenda suporta `Alternate ID` como nome de utilizador, em vez de `userPrincipalName` ?**

Yes. O SSO sem emenda suporta `Alternate ID` como nome de utilizador quando configurado no Azure AD Connect como mostrado [aqui](how-to-connect-install-custom.md). Nem todos os suportes de aplicações da Microsoft 365. `Alternate ID` Consulte a documentação específica do pedido para a declaração de apoio.

**P: Qual é a diferença entre a experiência de inscrição única fornecida pela [Azure AD Join](../devices/overview.md) e pela Seamless SSO?**

[O Azure AD Join](../devices/overview.md) fornece SSO aos utilizadores se os seus dispositivos estiverem registados no Azure AD. Estes dispositivos não têm necessariamente de ser unidos pelo domínio. SSO é fornecido usando *tokens* de atualização primária ou *PRTs*, e não Kerberos. A experiência do utilizador é ideal em dispositivos Windows 10. SSO acontece automaticamente no navegador Microsoft Edge. Também funciona no Chrome com o uso de uma extensão de navegador.

Você pode usar tanto Azure AD Join como Seamless SSO no seu inquilino. Estas duas características são complementares. Se ambas as funcionalidades estiverem ligadas, então o SSO da Azure AD Join tem precedência sobre o SSO sem emenda.

**P: Quero registar dispositivos não-Windows 10 com Azure AD, sem utilizar O FS AD. Posso usar sSO sem emenda em vez disso?**

Sim, este cenário precisa da versão 2.1 ou posterior do cliente de junção ao [local de trabalho.](https://www.microsoft.com/download/details.aspx?id=53554)

**P: Como posso passar a chave de desencriptação kerberos da conta do `AZUREADSSO` computador?**

É importante rolar frequentemente sobre a chave de desencriptação Kerberos da `AZUREADSSO` conta do computador (que representa Azure AD) criada na sua floresta de AD no local.

>[!IMPORTANT]
>Recomendamos vivamente que rebolar a chave de desencriptação Kerberos pelo menos a cada 30 dias.

Siga estes passos no servidor no local onde está a executar Azure AD Connect:

   > [!NOTE]
   >Você precisará de credenciais de administrador de domínio e de administrador global para os passos abaixo.
   >Se você não é um administrador de domínio e foi-lhe atribuídas permissões pelo administrador de domínio, você deve ligar para `Update-AzureADSSOForest -OnPremCredentials $creds -PreserveCustomPermissionsOnDesktopSsoAccount`

   **Passo 1. Obtenha a lista de florestas de AD onde o SSO sem emenda foi ativado**

   1. Primeiro, faça o download e instale [o Azure AD PowerShell](/powershell/azure/active-directory/overview).
   2. Navegue para a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importar o módulo SSO PowerShell sem costura utilizando este comando: `Import-Module .\AzureADSSO.psd1` .
   4. Executar PowerShell como administrador. Em PowerShell, `New-AzureADSSOAuthenticationContext` ligue. Este comando deve dar-lhe um pop-up para inserir as credenciais de Administrador Global do seu inquilino.
   5. `Get-AzureADSSOStatus | ConvertFrom-Json`Ligue. Este comando fornece-lhe a lista de florestas de AD (veja a lista de "Domínios") nas quais esta funcionalidade foi ativada.

   **Passo 2. Atualize a chave de desencriptação Kerberos em cada floresta AD em que foi configurado em**

   1. `$creds = Get-Credential`Ligue. Quando solicitado, insira as credenciais do Administrador de Domínio para a floresta AD pretendida.

   > [!NOTE]
   >O nome de utilizador de credenciais de administrador de domínio deve ser inserido no formato nome da conta SAM (contoso\johndoe ou contoso.com\johndoe). Utilizamos a parte de domínio do nome de utilizador para localizar o Controlador de Domínio do Administrador de Domínio utilizando DNS.

   >[!NOTE]
   >A conta de administrador de domínio utilizada não deve ser membro do grupo de Utilizadores Protegidos. Em caso afirmativo, a operação falhará.

   2. `Update-AzureADSSOForest -OnPremCredentials $creds`Ligue. Este comando atualiza a chave de desencriptação Kerberos para a `AZUREADSSO` conta do computador nesta floresta específica de AD e atualiza-a em Azure AD.
   
   3. Repita os passos anteriores para cada floresta AD em que configurar a funcionalidade.
   
  >[!NOTE]
   >Se estiver a atualizar uma floresta, para além da Azure AD Connect, certifique-se de que a conectividade com o servidor de catálogo global (TCP 3268 e TCP 3269) está disponível.

   >[!IMPORTANT]
   >Certifique-se de que _não_ dirige o comando mais de `Update-AzureADSSOForest` uma vez. Caso contrário, a funcionalidade deixa de funcionar até ao momento em que os bilhetes Kerberos dos seus utilizadores expiram e são reemitidos pelo seu Ative Directory no local.

**P: Como posso desativar o SSO sem emenda?**

   **Passo 1. Desativar a funcionalidade no seu inquilino**

   **Opção A: Desativar usando Azure AD Connect**
    
   1. Executar Azure AD Connect, escolher **Alterar página de súmis do utilizador** e clicar em **Seguinte**.
   2. Desmarque o **sinal único de ativação na** opção. Continue através do assistente.

   Após completar o assistente, o Seamless SSO será desativado no seu inquilino. No entanto, verá uma mensagem no ecrã que diz o seguinte:

   "O único sinal de sação está agora desativado, mas existem passos manuais adicionais a serem cumpridos para completar a limpeza. Saiba mais"

   Para completar o processo de limpeza, siga os passos 2 e 3 no servidor no local onde está a executar O AZure AD Connect.

   **Opção B: Desativar usando o PowerShell**

   Execute os seguintes passos no servidor no local onde está a executar Azure AD Connect:

   1. Primeiro, faça o download e instale [o Azure AD PowerShell](/powershell/azure/active-directory/overview).
   2. Navegue para a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importar o módulo SSO PowerShell sem costura utilizando este comando: `Import-Module .\AzureADSSO.psd1` .
   4. Executar PowerShell como administrador. Em PowerShell, `New-AzureADSSOAuthenticationContext` ligue. Este comando deve dar-lhe um pop-up para inserir as credenciais de Administrador Global do seu inquilino.
   5. `Enable-AzureADSSO -Enable $false`Ligue.
   
   Neste momento, o Seamless SSO está desativado, mas os domínios permanecerão configurados caso queira ativar o SSO sem emenda. Se pretender remover completamente os domínios da configuração SSO sem emenda, ligue para o seguinte cmdlet depois de ter completado o passo 5 acima: `Disable-AzureADSSOForest -DomainFqdn <fqdn>` .

   >[!IMPORTANT]
   >Desativar o SSO sem emenda usando o PowerShell não mudará o estado no Azure AD Connect. O SSO sem emenda mostrará como ativado na página **de insusição** do utilizador Change.

   **Passo 2. Obtenha a lista de florestas de AD onde o SSO sem emenda foi ativado**

   Siga as tarefas 1 a 4 abaixo se tiver desativado o Seamless SSO utilizando o Azure AD Connect. Se tiver desativado o Seamless SSO utilizando o PowerShell, avance para a tarefa 5 abaixo.

   1. Primeiro, faça o download e instale [o Azure AD PowerShell](/powershell/azure/active-directory/overview).
   2. Navegue para a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importar o módulo SSO PowerShell sem costura utilizando este comando: `Import-Module .\AzureADSSO.psd1` .
   4. Executar PowerShell como administrador. Em PowerShell, `New-AzureADSSOAuthenticationContext` ligue. Este comando deve dar-lhe um pop-up para inserir as credenciais de Administrador Global do seu inquilino.
   5. `Get-AzureADSSOStatus | ConvertFrom-Json`Ligue. Este comando fornece-lhe a lista de florestas de AD (veja a lista de "Domínios") nas quais esta funcionalidade foi ativada.

   **Passo 3. Elimine manualmente a `AZUREADSSO` conta do computador de cada floresta AD que vê listada.**

## <a name="next-steps"></a>Passos seguintes

- [**Quickstart**](how-to-connect-sso-quick-start.md) - Levante-se e execute Azure AD Seamless SSO.
- [**Mergulho Profundo Técnico**](how-to-connect-sso-how-it-works.md) - Entenda como funciona esta funcionalidade.
- [**Resolução de problemas**](tshoot-connect-sso.md) - Aprenda a resolver problemas comuns com a funcionalidade.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - Para arquivar novos pedidos de funcionalidades.
