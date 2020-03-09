---
title: Ativar a sincronização de hash de palavra-passe para os Serviços de Domínio Azure AD  Microsoft Docs
description: Neste tutorial, aprenda a ativar a sincronização de hash de palavra-passe utilizando o Azure AD Connect para um domínio gerido pelo Azure Ative Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 93e5ee9b46fb3387b70dd5092f72efcaa8a2bc19
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668741"
---
# <a name="tutorial-enable-password-synchronization-in-azure-active-directory-domain-services-for-hybrid-environments"></a>Tutorial: Ativar a sincronização de senhas em Serviços de Domínio de Diretório Ativo Azure para ambientes híbridos

Para ambientes híbridos, um inquilino azure Ative Directory (Azure AD) pode ser configurado para sincronizar com um ambiente de Serviços de Domínio ativo de diretório (AD DS) no local usando azure AD Connect. Por padrão, o Azure AD Connect não sincroniza o legado NT LAN Manager (NTLM) e hashes de senha Kerberos que são necessários para os Serviços de Domínio de Diretório Ativo Azure (Azure AD DS).

Para utilizar o Azure AD DS com contas sincronizadas a partir de um ambiente AD DS no local, é necessário configurar o Azure AD Connect para sincronizar as hashes de senha necessárias para a autenticação NTLM e Kerberos. Depois de configurado o Azure AD Connect, um evento de criação de conta no local ou de mudança de palavra-passe também sincroniza as hashes legacy password para Azure AD.

Não precisa de realizar estes passos se utilizar contas apenas em nuvem sem ambiente DS no local.

Neste tutorial, aprende-se:

> [!div class="checklist"]
> * Por que são necessários hashes de senha legadoNTLM e Kerberos
> * Como configurar a sincronização de hash de palavra-passe legado para Azure AD Connect

Se não tiver uma subscrição Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa dos seguintes recursos:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição que é sincronizado com um diretório no local usando o Azure AD Connect.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
    * Se necessário, [ative o Azure AD Connect para sincronização][enable-azure-ad-connect]de hash de palavra-passe .
* Um Azure Ative Directory Domain Services gerido domínio habilitado e configurado no seu inquilino Azure AD.
    * Se necessário, crie e configure uma instância de Serviços de [Domínio de Diretório Ativo Azure][create-azure-ad-ds-instance].

## <a name="password-hash-synchronization-using-azure-ad-connect"></a>Sincronização de hash de palavra-passe usando Azure AD Connect

O Azure AD Connect é utilizado para sincronizar objetos como contas de utilizador e grupos de um ambiente AD DS no local para um inquilino da AD Azure. Como parte do processo, a sincronização de hash de palavra-passe permite que as contas utilizem a mesma palavra-passe no ambiente ad ds on-prem e Azure AD.

Para autenticar os utilizadores no domínio gerido, o Azure AD DS necessita de hashes de senha num formato adequado para a autenticação NTLM e Kerberos. A Azure AD não armazena hashes de senha no formato que é necessário para a autenticação NTLM ou Kerberos até ativar o Azure AD DS para o seu inquilino. Por razões de segurança, a Azure AD também não armazena credenciais de senha em formato de texto claro. Portanto, a Azure AD não pode gerar automaticamente estas hashes de senha NTLM ou Kerberos com base nas credenciais existentes dos utilizadores.

O Azure AD Connect pode ser configurado para sincronizar as hashes de senha NTLM ou Kerberos necessárias para O DS Azure AD. Certifique-se de que completou os passos para ativar o [Azure AD Connect para sincronização][enable-azure-ad-connect]de hash password . Se tiver uma instância existente de Azure AD Connect, [descarregue e atualize para a versão mais recente][azure-ad-connect-download] para se certificar de que pode sincronizar as hashes de senha legacy para NTLM e Kerberos. Esta funcionalidade não está disponível nos lançamentos antecipados do Azure AD Connect ou com a ferramenta dirSync legado. É necessária a versão *1.1.614.0* do Azure AD Connect.

> [!IMPORTANT]
> O Azure AD Connect só deve ser instalado e configurado para sincronização com ambientes AD DS no local. Não é suportado para instalar o Azure AD Connect num domínio gerido pelo Azure AD DS para sincronizar objetos de volta ao Azure AD.

## <a name="enable-synchronization-of-password-hashes"></a>Ativar a sincronização de hashes de senha

Com o Azure AD Connect instalado e configurado para sincronizar com o Azure AD, configurar agora a sincronização de hash ish ish legacy para NTLM e Kerberos. Um script PowerShell é usado para configurar as definições necessárias e, em seguida, iniciar uma sincronização completa da palavra-passe para AD Azure. Quando o processo de sincronização de hash da palavra-passe Azure AD Connect estiver completo, os utilizadores podem iniciar sessão em aplicações através do Azure AD DS que utilizam hashes de senha isenções ntlm ou Kerberos.

1. No computador com Azure AD Connect instalado, a partir do menu Iniciar, abra o **Serviço Azure AD Connect > Synchronization**Service .
1. Selecione o separador **Conectores.** As informações de ligação utilizadas para estabelecer a sincronização entre o ambiente AD DS no local e a AD Azure estão listadas.

    O **Tipo** indica o *Diretório Ativo do Windows Azure (Microsoft)* para o conector Azure AD ou serviços de domínio de *diretório ativo* para o conector AD DS no local. Tome nota dos nomes do conector a utilizar no script PowerShell no próximo passo.

    ![Listar os nomes do conector no Sync Service Manager](media/tutorial-configure-password-hash-sync/service-sync-manager.png)

    Neste exemplo, são utilizados os seguintes conectores:

    * O conector Azure AD é nomeado *aaddscontoso.onmicrosoft.com - AAD*
    * O conector AD DS no local é chamado *onprem.contoso.com*

1. Copie e cole o seguinte script PowerShell no computador com Azure AD Connect instalado. O script desencadeia uma sincronização completa de palavra-passe que inclui hashes de senha legacy. Atualize as variáveis `$azureadConnector` e `$adConnector` com os nomes do conector do passo anterior.

    Execute este script em cada floresta aD para sincronizar a conta de acesso ntlm e kerberos a hashes para Azure AD.

    ```powershell
    # Define the Azure AD Connect connector names and import the required PowerShell module
    $azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
    $adConnector = "<CASE SENSITIVE AD DS CONNECTOR NAME>"
    
    Import-Module "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1"
    Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

    # Create a new ForceFullPasswordSync configuration parameter object then
    # update the existing connector with this new configuration
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c

    # Disable and re-enable Azure AD Connect to force a full password synchronization
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true
    ```

    Dependendo da dimensão do seu diretório em termos de número de contas e grupos, a sincronização das hashes de senha do legado para a AD Azure pode demorar algum tempo. As palavras-passe são então sincronizadas para o domínio gerido pelo Azure AD DS depois de terem sincronizado com o Azure AD.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeste:

> [!div class="checklist"]
> * Por que são necessários hashes de senha legadoNTLM e Kerberos
> * Como configurar a sincronização de hash de palavra-passe legado para Azure AD Connect

> [!div class="nextstepaction"]
> [Saiba como funciona a sincronização num domínio gerido pelo Azure AD Domain Services](synchronization.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-azure-ad-connect]: ../active-directory/hybrid/how-to-connect-install-express.md

<!-- EXTERNAL LINKS -->
[azure-ad-connect-download]: https://www.microsoft.com/download/details.aspx?id=47594
