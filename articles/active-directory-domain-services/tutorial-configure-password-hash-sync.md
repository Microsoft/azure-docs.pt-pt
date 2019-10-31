---
title: Habilitar sincronização de hash de senha para Azure AD Domain Services | Microsoft Docs
description: Neste tutorial, saiba como habilitar a sincronização de hash de senha usando o Azure AD Connect para um domínio gerenciado Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 41e61376d12d447dd480a39ef7200db6af7cca89
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73172859"
---
# <a name="tutorial-enable-password-synchronization-in-azure-active-directory-domain-services-for-hybrid-environments"></a>Tutorial: habilitar a sincronização de senha em Azure Active Directory Domain Services para ambientes híbridos

Para ambientes híbridos, um locatário Azure Active Directory (Azure AD) pode ser configurado para sincronizar com um ambiente de Active Directory Domain Services (AD DS) local usando Azure AD Connect. Por padrão, Azure AD Connect não sincroniza os hashes de senha Kerberos e NTLM (NT LAN Manager) herdados necessários para Azure Active Directory Domain Services (Azure AD DS).

Para usar o Azure AD DS com contas sincronizadas de um ambiente de AD DS local, você precisa configurar o Azure AD Connect para sincronizar os hashes de senha necessários para a autenticação NTLM e Kerberos. Depois que Azure AD Connect estiver configurado, um evento de criação de conta local ou alteração de senha também sincronizará os hashes de senha herdados para o Azure AD.

Você não precisa executar essas etapas se usar contas somente de nuvem sem um ambiente de AD DS local.

Neste tutorial, você aprende:

> [!div class="checklist"]
> * Por que os hashes de senha NTLM e Kerberos herdados são necessários
> * Como configurar a sincronização de hash de senha herdada para Azure AD Connect

Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa dos seguintes recursos:

* Uma subscrição ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário Azure Active Directory associado à sua assinatura que é sincronizado com um diretório local usando Azure AD Connect.
    * Se necessário, [crie um locatário Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
    * Se necessário, [habilite Azure ad Connect para a sincronização de hash de senha][enable-azure-ad-connect].
* Um Azure Active Directory Domain Services domínio gerenciado habilitado e configurado em seu locatário do Azure AD.
    * Se necessário, [crie e configure uma instância de Azure Active Directory Domain Services][create-azure-ad-ds-instance].

## <a name="password-hash-synchronization-using-azure-ad-connect"></a>Sincronização de hash de senha usando Azure AD Connect

Azure AD Connect é usado para sincronizar objetos como contas de usuário e grupos de um ambiente de AD DS local em um locatário do Azure AD. Como parte do processo, a sincronização de hash de senha permite que as contas usem a mesma senha no ambiente de AD DS local e no Azure AD.

Para autenticar usuários no domínio gerenciado, o Azure AD DS precisa de hashes de senha em um formato adequado para autenticação NTLM e Kerberos. O Azure AD não armazena hashes de senha no formato necessário para autenticação NTLM ou Kerberos até que você habilite o Azure AD DS para seu locatário. Por motivos de segurança, o Azure AD também não armazena nenhuma credencial de senha no formato de texto não criptografado. Portanto, o Azure AD não pode gerar automaticamente esses hashes de senha NTLM ou Kerberos com base nas credenciais existentes dos usuários.

Azure AD Connect pode ser configurado para sincronizar os hashes de senha NTLM ou Kerberos necessários para o AD DS do Azure. Verifique se você concluiu as etapas para [habilitar a Azure ad Connect para a sincronização de hash de senha][enable-azure-ad-connect]. Se você tivesse uma instância existente do Azure AD Connect, [Baixe e atualize para a versão mais recente][azure-ad-connect-download] para certificar-se de que você possa sincronizar os hashes de senha herdados para NTLM e Kerberos. Essa funcionalidade não está disponível em versões anteriores do Azure AD Connect ou com a ferramenta DirSync herdada. Azure AD Connect versão *1.1.614.0* ou posterior é necessária.

## <a name="enable-synchronization-of-password-hashes"></a>Habilitar a sincronização de hashes de senha

Com Azure AD Connect instalado e configurado para sincronizar com o Azure AD, agora configure a sincronização de hash de senha herdada para NTLM e Kerberos. Um script do PowerShell é usado para definir as configurações necessárias e, em seguida, iniciar uma sincronização de senha completa para o Azure AD. Quando esse Azure AD Connect processo de sincronização de hash de senha for concluído, os usuários poderão entrar em aplicativos por meio de AD DS do Azure que usam hashes de senha NTLM ou Kerberos herdados.

1. No computador com Azure AD Connect instalado, no menu Iniciar, abra o **Azure AD Connect > serviço de sincronização**.
1. Selecione a guia **conectores** . As informações de conexão usadas para estabelecer a sincronização entre o ambiente de AD DS local e o Azure AD são listadas.

    O **tipo** indica o *Windows Azure Active Directory (Microsoft)* para o conector do Azure ad ou *Active Directory Domain Services* para o conector de AD DS local. Anote os nomes de conector a serem usados no script do PowerShell na próxima etapa.

    ![Listar os nomes de conectores no Service Manager de sincronização](media/tutorial-configure-password-hash-sync/service-sync-manager.png)

    Nesta captura de tela de exemplo, os seguintes conectores são usados:

    * O conector do AD do Azure é denominado *contoso.onmicrosoft.com-AAD*
    * O conector de AD DS local é chamado de *OnPrem.contoso.com*

1. Copie e cole o seguinte script do PowerShell no computador com Azure AD Connect instalado. O script dispara uma sincronização de senha completa que inclui hashes de senha herdados. Atualize as variáveis `$azureadConnector` e `$adConnector` com os nomes dos conectores da etapa anterior.

    Execute esse script em cada floresta do AD para sincronizar os hashes de senha NTLM e Kerberos da conta local com o Azure AD.

    ```powershell
    # Define the Azure AD Connect connector names and import the required PowerShell module
    $azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
    $adConnector = "<CASE SENSITIVE AD DS CONNECTOR NAME>"
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

    Dependendo do tamanho do diretório em termos de número de contas e grupos, a sincronização dos hashes de senha herdados para o Azure AD pode levar algum tempo. As senhas são então sincronizadas com o domínio gerenciado AD DS do Azure depois de serem sincronizadas com o Azure AD.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu:

> [!div class="checklist"]
> * Por que os hashes de senha NTLM e Kerberos herdados são necessários
> * Como configurar a sincronização de hash de senha herdada para Azure AD Connect

> [!div class="nextstepaction"]
> [Saiba como a sincronização funciona em um domínio gerenciado Azure AD Domain Services](synchronization.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-azure-ad-connect]: ../active-directory/hybrid/how-to-connect-install-express.md

<!-- EXTERNAL LINKS -->
[azure-ad-connect-download]: https://www.microsoft.com/download/details.aspx?id=47594
