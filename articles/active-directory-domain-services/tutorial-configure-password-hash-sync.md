---
title: Ativar a sincronização de haxixe de palavra-passe para serviços de domínio Azure AD / Microsoft Docs
description: Neste tutorial, aprenda a permitir a sincronização de hash de palavra-passe utilizando o Azure AD Connect a um domínio gerido por Serviços de Domínio do Diretório Ativo Azure.
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: 4daad0d0739bb3407be13f717572c8aa99947d1a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967295"
---
# <a name="tutorial-enable-password-synchronization-in-azure-active-directory-domain-services-for-hybrid-environments"></a>Tutorial: Permitir a sincronização de palavras-passe em Azure Ative Directory Domain Services para ambientes híbridos

Para ambientes híbridos, um inquilino do Azure Ative Directory (Azure AD) pode ser configurado para sincronizar com um ambiente no local Ative Directory Domain Services (AD DS) utilizando o Azure AD Connect. Por padrão, o Azure AD Connect não sincroniza o legado NT LAN Manager (NTLM) e os hashes de password Kerberos que são necessários para os Serviços de Domínio do Diretório Ativo Azure (Azure AD DS).

Para utilizar o Azure AD DS com contas sincronizadas a partir de um ambiente AD DS no local, é necessário configurar o Azure AD Connect para sincronizar as hashes de palavra-passe necessárias para a autenticação NTLM e Kerberos. Depois de configurado O Azure AD Connect, um evento de criação de conta no local ou de mudança de palavra-passe também sincroniza a palavra-passe do legado hashes para Azure AD.

Não precisa de executar estes passos se utilizar contas apenas em nuvem sem ambiente AD DS no local, ou se utilizar uma *floresta de recursos.* Para domínios geridos que utilizam uma floresta de recursos, as hashes de senha no local nunca são sincronizadas. A autenticação para contas no local utiliza o(s) forest trust(s) de volta aos seus próprios controladores de domínio AD DS.

Neste tutorial, aprende-se:

> [!div class="checklist"]
> * Por que o legado NTLM e Kerberos palavra-passe hashes são necessários
> * Como configurar a sincronização de hash de palavra-passe para Azure AD Connect

Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa dos seguintes recursos:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição que está sincronizado com um diretório no local usando o Azure AD Connect.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
    * Se necessário, [ative o Azure AD Connect para sincronização de hash de palavra-passe][enable-azure-ad-connect].
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
    * Se necessário, [crie e configuure um domínio gerido por Azure Ative Directory Domain Services][create-azure-ad-ds-instance].

## <a name="password-hash-synchronization-using-azure-ad-connect"></a>Sincronização de hash de palavra-passe usando Azure AD Connect

O Azure AD Connect é usado para sincronizar objetos como contas de utilizador e grupos de um ambiente AD DS no local para um inquilino AD Azure. Como parte do processo, a sincronização de hash de palavra-passe permite que as contas utilizem a mesma palavra-passe no ambiente DS DS AD on-prem e Azure AD.

Para autenticar os utilizadores no domínio gerido, o Azure AD DS necessita de hashes de senha num formato adequado para a autenticação NTLM e Kerberos. O Azure AD não armazena hashes de senha no formato necessário para a autenticação NTLM ou Kerberos até ativar o Azure AD DS para o seu inquilino. Por razões de segurança, a Azure AD também não armazena quaisquer credenciais de senha em formato de texto claro. Portanto, o Azure AD não pode gerar automaticamente estes hashes de palavra-passe NTLM ou Kerberos com base nas credenciais existentes dos utilizadores.

O Azure AD Connect pode ser configurado para sincronizar as hashes de palavra-passe NTLM ou Kerberos necessárias para Azure AD DS. Certifique-se de que completou os passos para ativar o [Azure AD Connect para sincronização de hash de palavra-passe][enable-azure-ad-connect]. Se tiver uma instância existente de Azure AD Connect, [faça o download e atualização para a versão mais recente][azure-ad-connect-download] para se certificar de que pode sincronizar as hashes de senhas antigas para NTLM e Kerberos. Esta funcionalidade não está disponível em lançamentos iniciais do Azure AD Connect ou na ferramenta legado DirSync. A versão AD Connect *Azure 1.1.614.0* ou mais tarde é necessária.

> [!IMPORTANT]
> O Azure AD Connect só deve ser instalado e configurado para sincronização com ambientes AD DS no local. Não é suportado para instalar o Azure AD Connect num domínio Azure AD DS gerido para sincronizar objetos de volta ao Azure AD.

## <a name="enable-synchronization-of-password-hashes"></a>Permitir a sincronização dos hashes de palavra-passe

Com o Azure AD Connect instalado e configurado para sincronizar com a Azure AD, agora configurar a sincronização de hash de palavra-passe para NTLM e Kerberos. Um script PowerShell é usado para configurar as definições necessárias e, em seguida, iniciar uma sincronização completa de senha para Azure AD. Quando o processo de sincronização de hash de password AZure AD Connect estiver concluído, os utilizadores podem iniciar sação nas aplicações através do Azure AD DS que utilizam hashes de palavra-passe legacy NTLM ou Kerberos.

1. No computador com Azure AD Connect instalado, a partir do menu Iniciar, abra o **Serviço de Sincronização AD Connect > Azure**.
1. Selecione o **separador Conectores.** As informações de ligação utilizadas para estabelecer a sincronização entre o ambiente AD DS no local e a Azure AD estão listadas.

    O **Tipo** indica que o *Windows Azure Ative Directory (Microsoft)* para o conector AD AD ou *serviços de domínio de diretório ativo* para o conector AD DS no local. Tome nota dos nomes do conector a utilizar no script PowerShell no passo seguinte.

    ![Listar os nomes do conector no Gestor de Serviços de Sincronização](media/tutorial-configure-password-hash-sync/service-sync-manager.png)

    Neste exemplo, são utilizados os seguintes conectores:

    * O conector AD AZure é nomeado *contoso.onmicrosoft.com - AAD*
    * O conector AD DS no local é nomeado *onprem.contoso.com*

1. Copie e cole o seguinte script PowerShell para o computador com Azure AD Connect instalado. O script desencadeia uma sincronização completa de senha que inclui hashes de senha legado. Atualize as `$azureadConnector` `$adConnector` variáveis e com os nomes do conector do passo anterior.

    Execute este script em cada floresta de AD para sincronizar a conta NTLM e Kerberos password hashes para Azure AD.

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

    Dependendo do tamanho do seu diretório em termos de número de contas e grupos, a sincronização da palavra-passe do legado hashes para Azure AD pode demorar algum tempo. As palavras-passe são então sincronizadas ao domínio gerido depois de sincronizadas com a Azure AD.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu:

> [!div class="checklist"]
> * Por que o legado NTLM e Kerberos palavra-passe hashes são necessários
> * Como configurar a sincronização de hash de palavra-passe para Azure AD Connect

> [!div class="nextstepaction"]
> [Saiba como funciona a sincronização num domínio gerido por Azure AD Domain Services](synchronization.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-azure-ad-connect]: ../active-directory/hybrid/how-to-connect-install-express.md

<!-- EXTERNAL LINKS -->
[azure-ad-connect-download]: https://www.microsoft.com/download/details.aspx?id=47594
