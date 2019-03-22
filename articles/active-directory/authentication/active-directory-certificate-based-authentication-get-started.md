---
title: Introdução à autenticação baseada em certificados - Azure Active Directory
description: Saiba como configurar a autenticação baseada em certificado no seu ambiente
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: f57d4615fc80df6c5df9ba295288ad71ae12fa23
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310834"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Introdução à autenticação baseada em certificados no Azure Active Directory

Autenticação baseada em certificado permite-lhe ser autenticado pelo Azure Active Directory com um certificado de cliente num dispositivo Windows, Android ou iOS ao ligar a sua conta do Exchange online para:

- Aplicações móveis da Microsoft, como o Microsoft Outlook e o Microsoft Word
- Clientes do Exchange ActiveSync (EAS)

Configurar esta funcionalidade elimina a necessidade de inserir uma combinação de nome de utilizador e palavra-passe em determinados email e aplicativos do Microsoft Office no seu dispositivo móvel.

Neste tópico:

- Fornece os passos para configurar e utilizar a autenticação baseada em certificado para os utilizadores de inquilinos nos planos do Office 365 Enterprise, a empresa, educação e administração pública dos EUA. Esta funcionalidade está disponível em pré-visualização nos planos do Office 365 China, US Government defesa e Governo Federal.
- Pressupõe que já tem um [infraestrutura de chaves públicas (PKI)](https://go.microsoft.com/fwlink/?linkid=841737) e [do AD FS](../hybrid/how-to-connect-fed-whatis.md) configurado.

## <a name="requirements"></a>Requisitos

Para configurar a autenticação baseada em certificados, as seguintes instruções devem ser verdadeiras:

- Autenticação baseada em certificados (CBA) só é suportada para ambientes de federados para aplicativos de navegador ou clientes nativos que utilizam autenticação moderna (ADAL). A única exceção é o Exchange Active Sync (EAS) para o Exchange Online (EXO), que pode ser utilizado para contas federadas e geridas.
- A autoridade de certificação de raiz e quaisquer autoridades de certificação intermediárias devem ser configuradas no Azure Active Directory.
- Cada autoridade de certificação tem de ter uma lista de revogação de certificados (CRL) que pode ser referenciada através de um URL de acesso à internet.
- Tem de ter autoridade de pelo menos um certificado configurada no Azure Active Directory. Pode encontrar passos relacionados a [configurar autoridades de certificação](#step-2-configure-the-certificate-authorities) secção.
- Para clientes do Exchange ActiveSync, o certificado de cliente tem de ter o endereço de e-mail encaminhável do utilizador no Exchange online no nome do Principal ou o valor de nome de RFC822 do campo nome alternativo do requerente. O Azure Active Directory é o valor de RFC822 mapeado para o atributo de endereço Proxy no diretório.
- O dispositivo de cliente tem de ter acesso à autoridade de certificação, pelo menos, um que emite certificados de cliente.
- Um certificado de cliente para autenticação de cliente tem de ter sido emitido para o seu cliente.

## <a name="step-1-select-your-device-platform"></a>Passo 1: Selecione a plataforma de dispositivo

Como primeiro passo, para a plataforma de dispositivo que mais lhe interessa, terá de rever o seguinte:

- O suporte de aplicações móveis do Office
- Os requisitos de implementação específica

As informações relacionadas existem para as seguintes plataformas de dispositivos:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>Passo 2: Configurar as autoridades de certificação

Para configurar as autoridades de certificado no Azure Active Directory, para cada autoridade de certificação, carregar o seguinte:

* A parte pública do certificado, na *. cer* formato
* Os URLs de acesso à internet onde residem as listas de revogação de certificados (CRL)

O esquema para uma autoridade de certificação será semelhante ao seguinte:

    class TrustedCAsForPasswordlessAuth
    {
       CertificateAuthorityInformation[] certificateAuthorities;
    }

    class CertificateAuthorityInformation

    {
        CertAuthorityType authorityType;
        X509Certificate trustedCertificate;
        string crlDistributionPoint;
        string deltaCrlDistributionPoint;
        string trustedIssuer;
        string trustedIssuerSKI;
    }

    enum CertAuthorityType
    {
        RootAuthority = 0,
        IntermediateAuthority = 1
    }

Para a configuração, pode utilizar o [do Azure Active Directory PowerShell versão 2](/powershell/azure/install-adv2?view=azureadps-2.0):

1. Inicie o Windows PowerShell com privilégios de administrador.
2. Instale a versão do módulo do Azure AD [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) ou superior.

        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33

Como uma primeira etapa de configuração, tem de estabelecer uma ligação com o seu inquilino. Assim que existir uma ligação com o seu inquilino, pode rever, adicionar, eliminar e modificar as autoridades de certificado fidedigno que estão definidas no seu diretório.

### <a name="connect"></a>Ligar

Para estabelecer uma ligação com o seu inquilino, utilize o [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0) cmdlet:

    Connect-AzureAD

### <a name="retrieve"></a>Obter

Para obter as autoridades de certificado fidedigno que estão definidas no seu diretório, utilize o [Get-AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet.

    Get-AzureADTrustedCertificateAuthority

### <a name="add"></a>Adicionar

Para criar uma autoridade de certificação fidedigna, utilize o [New-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet e defina a **crlDistributionPoint** atributo para um valor correto:

    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint="<CRL Distribution URL>"
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca

### <a name="remove"></a>Remover

Para remover uma autoridade de certificação fidedigna, utilize o [Remove-AzureADTrustedCertificateAuthority](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet:

    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]

### <a name="modify"></a>Modificar

Para modificar uma autoridade de certificação fidedigna, utilize o [Set-AzureADTrustedCertificateAuthority](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet:

    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]

## <a name="step-3-configure-revocation"></a>Passo 3: Configurar revogação

Para revogar um certificado de cliente, o Azure Active Directory obtém a lista de revogação de certificados (CRL) a partir dos URLs carregados como parte das informações da autoridade de certificado e armazena em cache. A última publicação timestamp (**data efetiva** propriedade) no CRL é utilizado para garantir a CRL ainda é válida. A CRL é referenciada periodicamente para revogar o acesso aos certificados que fazem parte da lista.

Se uma revogação instantânea mais for necessária (por exemplo, se um utilizador perder um dispositivo), o token de autorização do utilizador pode ser invalidado. Para invalidar o token de autorização, defina o **StsRefreshTokenValidFrom** campo para este utilizador em particular com o Windows PowerShell. Tem de atualizar o **StsRefreshTokenValidFrom** para cada utilizador que pretende revogar o acesso de campo.

Para garantir que a revogação persistir, tem de definir o **data efetiva** da CRL para uma data posterior o valor definido pelo **StsRefreshTokenValidFrom** e certifique-se de que o certificado em questão é no CRL.

Os passos seguintes descrevem o processo de atualização e invalidar o token de autorização, definindo a **StsRefreshTokenValidFrom** campo.

**Para configurar revogação:**

1. Ligue-se com credenciais de administrador para o serviço MSOL:

        $msolcred = get-credential
        connect-msolservice -credential $msolcred

2. Recupere o valor de StsRefreshTokensValidFrom atual para um utilizador:

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom

3. Configure um novo valor de StsRefreshTokensValidFrom para igual a usuário, como o timestamp atual:

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

A data que definir tem de ser no futuro. Se a data não está no futuro, o **StsRefreshTokensValidFrom** propriedade não está definida. Se a data no futuro, for **StsRefreshTokensValidFrom** está definido para a hora atual (e não da data indicada pelo comando Set-MsolUser).

## <a name="step-4-test-your-configuration"></a>Passo 4: Testar a configuração

### <a name="testing-your-certificate"></a>O certificado de teste

Como um primeiro teste de configuração, deve tentar iniciar sessão no [Outlook Web Access](https://outlook.office365.com) ou [SharePoint Online](https://microsoft.sharepoint.com) usando sua **browser no dispositivo**.

Se o seu início de sessão for bem-sucedida, em seguida, sabe que:

- O certificado de utilizador tiver sido aprovisionado para o seu dispositivo de teste
- AD FS está configurado corretamente

### <a name="testing-office-mobile-applications"></a>Teste de aplicativos móveis do Office

**Para testar a autenticação baseada em certificado na sua aplicação móvel do Office:**

1. No seu dispositivo de teste, instale uma aplicação móvel do Office (por exemplo, o OneDrive).
3. Inicie a aplicação.
4. Introduza o seu nome de utilizador e, em seguida, selecione o certificado de utilizador que pretende utilizar.

Deve ser com êxito conectado.

### <a name="testing-exchange-activesync-client-applications"></a>Teste de aplicativos de cliente do Exchange ActiveSync

Para aceder ao Exchange ActiveSync (EAS) através da autenticação baseada em certificados, um perfil EAS que contém o certificado de cliente tem de estar disponível para o aplicativo.

O perfil EAS tem de conter as seguintes informações:

- O certificado de utilizador a ser utilizado para autenticação

- O ponto de extremidade do EAS (por exemplo, outlook.office365.com)

Um perfil EAS pode ser configurado e colocado no dispositivo por meio da utilização de gestão de dispositivos móveis (MDM), como o Intune ou colocando manualmente o certificado no perfil EAS no dispositivo.

### <a name="testing-eas-client-applications-on-android"></a>Teste de aplicativos de cliente EAS no Android

**Para testar a autenticação de certificado:**

1. Configure um perfil do EAS no aplicativo que satisfaça os requisitos na secção anterior.
2. Abra a aplicação e certifique-se de que o email está a sincronizar.

## <a name="next-steps"></a>Passos Seguintes

[Obter informações adicionais sobre a autenticação baseada em certificados em dispositivos Android.](active-directory-certificate-based-authentication-android.md)

[Obter informações adicionais sobre a autenticação baseada em certificados em dispositivos iOS.](active-directory-certificate-based-authentication-ios.md)
