---
title: Autenticação baseada em certificado-Azure Active Directory
description: Saiba como configurar a autenticação baseada em certificado em seu ambiente
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b57c4f474b0b9def08005f32f48225d36ea8cf1
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848838"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Introdução à autenticação baseada em certificado no Azure Active Directory

A autenticação baseada em certificado permite que você seja autenticado por Azure Active Directory com um certificado de cliente em um dispositivo Windows, Android ou iOS ao conectar sua conta do Exchange Online ao:

- Aplicativos móveis da Microsoft, como o Microsoft Outlook e o Microsoft Word
- Clientes do Exchange ActiveSync (EAS)

A configuração desse recurso elimina a necessidade de inserir uma combinação de nome de usuário e senha em determinados emails e Microsoft Office aplicativos em seu dispositivo móvel.

Este tópico:

- Fornece as etapas para configurar e utilizar a autenticação baseada em certificado para usuários de locatários nos planos do Office 365 Enterprise, Business, Education e do governo dos EUA. Esse recurso está disponível em versão prévia no Office 365 na China, na defesa do governo dos EUA e nos planos federais do governo dos EUA.
- Pressupõe que você já tenha uma [PKI (infraestrutura de chave pública)](https://go.microsoft.com/fwlink/?linkid=841737) e [AD FS](../hybrid/how-to-connect-fed-whatis.md) configurada.

## <a name="requirements"></a>Requisitos

Para configurar a autenticação baseada em certificado, as seguintes instruções devem ser verdadeiras:

- A CBA (autenticação baseada em certificado) só tem suporte em ambientes federados para aplicativos de navegador, clientes nativos que usam a ADAL (autenticação moderna) ou bibliotecas MSAL. A única exceção é Exchange Active Sync (EAS) para o Exchange Online (EXO), que pode ser usado para contas federadas e gerenciadas.
- A autoridade de certificação raiz e quaisquer autoridades de certificação intermediárias devem ser configuradas no Azure Active Directory.
- Cada autoridade de certificação deve ter uma CRL (lista de certificados revogados) que pode ser referenciada por meio de uma URL voltada para a Internet.
- Você deve ter pelo menos uma autoridade de certificação configurada em Azure Active Directory. Você pode encontrar etapas relacionadas na seção [configurar as autoridades de certificação](#step-2-configure-the-certificate-authorities) .
- Para clientes do Exchange ActiveSync, o certificado do cliente deve ter o endereço de email roteável do usuário no Exchange Online no nome da entidade de segurança ou no valor do nome do RFC822 do campo nome alternativo da entidade. Azure Active Directory mapeia o valor de RFC822 para o atributo de endereço de proxy no diretório.
- Seu dispositivo cliente deve ter acesso a pelo menos uma autoridade de certificação que emite certificados de cliente.
- Um certificado de cliente para autenticação de cliente deve ter sido emitido para o cliente.

>[!IMPORTANT]
>O tamanho máximo de uma CRL para Azure Active Directory ser baixado com êxito e o cache é 20 MB, e o tempo necessário para baixar a CRL não deve exceder 10 segundos.  Se Azure Active Directory não puder baixar uma CRL, as autenticações baseadas em certificado usando certificados emitidos pela autoridade de certificação correspondente falharão. As práticas recomendadas para garantir que os arquivos de CRL estejam dentro das restrições de tamanho são manter os tempos de vida dos certificados dentro dos limites razoáveis e limpar os certificados expirados. 

## <a name="step-1-select-your-device-platform"></a>Etapa 1: selecione a plataforma do dispositivo

Como uma primeira etapa, para a plataforma de dispositivo com a qual você se preocupa, você precisa examinar o seguinte:

- O suporte aos aplicativos móveis do Office
- Os requisitos de implementação específicos

As informações relacionadas existem para as seguintes plataformas de dispositivo:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>Etapa 2: configurar as autoridades de certificação

Para configurar suas autoridades de certificação no Azure Active Directory, para cada autoridade de certificação, carregue o seguinte:

* A parte pública do certificado, no formato *. cer*
* As URLs voltadas para a Internet nas quais as listas de revogação de certificado (CRLs) residem

O esquema para uma autoridade de certificação tem a seguinte aparência:

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

Para a configuração, você pode usar o [Azure Active Directory PowerShell versão 2](/powershell/azure/install-adv2?view=azureadps-2.0):

1. Inicie o Windows PowerShell com privilégios de administrador.
2. Instale o módulo do Azure AD versão [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) ou superior.

        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33

Como uma primeira etapa de configuração, você precisa estabelecer uma conexão com seu locatário. Assim que houver uma conexão com o locatário, você poderá examinar, adicionar, excluir e modificar as autoridades de certificação confiáveis que são definidas no seu diretório.

### <a name="connect"></a>Ligar

Para estabelecer uma conexão com seu locatário, use o cmdlet [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0) :

    Connect-AzureAD

### <a name="retrieve"></a>Recuperado

Para recuperar as autoridades de certificação confiáveis que são definidas em seu diretório, use o cmdlet [Get-AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0) .

    Get-AzureADTrustedCertificateAuthority

### <a name="add"></a>Adicionar

Para criar uma autoridade de certificação confiável, use o cmdlet [New-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) e defina o atributo **crlDistributionPoint** para um valor correto:

    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint="<CRL Distribution URL>"
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca

### <a name="remove"></a>Remover

Para remover uma autoridade de certificação confiável, use o cmdlet [Remove-AzureADTrustedCertificateAuthority](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0) :

    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]

### <a name="modify"></a>Modificar

Para modificar uma autoridade de certificação confiável, use o cmdlet [set-AzureADTrustedCertificateAuthority](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0) :

    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]

## <a name="step-3-configure-revocation"></a>Etapa 3: configurar a revogação

Para revogar um certificado de cliente, Azure Active Directory busca a CRL (lista de certificados revogados) das URLs carregadas como parte das informações da autoridade de certificação e a armazena em cache. O último carimbo de data/hora de publicação (propriedade**Date de efetivação** ) na CRL é usado para garantir que a CRL ainda seja válida. A CRL é referenciada periodicamente para revogar o acesso a certificados que fazem parte da lista.

Se uma revogação mais instantânea for necessária (por exemplo, se um usuário perder um dispositivo), o token de autorização do usuário poderá ser invalidado. Para invalidar o token de autorização, defina o campo **StsRefreshTokenValidFrom** para esse usuário específico usando o Windows PowerShell. Você deve atualizar o campo **StsRefreshTokenValidFrom** para cada usuário para o qual você deseja revogar o acesso.

Para garantir que a revogação persista, você deve definir a **data de efetivação** da CRL como uma data após o valor definido por **StsRefreshTokenValidFrom** e garantir que o certificado em questão esteja na CRL.

As etapas a seguir descrevem o processo de atualização e invalidação do token de autorização, definindo o campo **StsRefreshTokenValidFrom** .

**Para configurar a revogação:**

1. Conecte-se com credenciais de administrador para o serviço MSOL:

        $msolcred = get-credential
        connect-msolservice -credential $msolcred

2. Recuperar o valor de StsRefreshTokensValidFrom atual para um usuário:

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom

3. Configure um novo valor de StsRefreshTokensValidFrom para o usuário igual ao carimbo de data/hora atual:

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

A data definida deve estar no futuro. Se a data não estiver no futuro, a propriedade **StsRefreshTokensValidFrom** não será definida. Se a data estiver no futuro, **StsRefreshTokensValidFrom** será definido como a hora atual (não a data indicada pelo comando Set-MsolUser).

## <a name="step-4-test-your-configuration"></a>Etapa 4: testar sua configuração

### <a name="testing-your-certificate"></a>Testando seu certificado

Como um primeiro teste de configuração, você deve tentar entrar no [Outlook acesso via Web](https://outlook.office365.com) ou no [SharePoint Online](https://microsoft.sharepoint.com) usando seu **navegador no dispositivo**.

Se sua entrada for bem-sucedida, você saberá que:

- O certificado de usuário foi provisionado para seu dispositivo de teste
- AD FS está configurado corretamente

### <a name="testing-office-mobile-applications"></a>Testando aplicativos móveis do Office

**Para testar a autenticação baseada em certificado em seu aplicativo móvel do Office:**

1. Em seu dispositivo de teste, instale um aplicativo móvel do Office (por exemplo, OneDrive).
3. Inicie o aplicativo.
4. Insira seu nome de usuário e, em seguida, selecione o certificado que você deseja usar.

Você deve ter entrado com êxito.

### <a name="testing-exchange-activesync-client-applications"></a>Testando aplicativos cliente do Exchange ActiveSync

Para acessar o Exchange ActiveSync (EAS) por meio da autenticação baseada em certificado, um perfil do EAS que contém o certificado do cliente deve estar disponível para o aplicativo.

O perfil do EAS deve conter as seguintes informações:

- O certificado de usuário a ser usado para autenticação

- O ponto de extremidade EAS (por exemplo, outlook.office365.com)

Um perfil do EAS pode ser configurado e colocado no dispositivo por meio da utilização do MDM (gerenciamento de dispositivo móvel), como o Intune, ou colocando manualmente o certificado no perfil do EAS no dispositivo.

### <a name="testing-eas-client-applications-on-android"></a>Testando aplicativos cliente do EAS no Android

**Para testar a autenticação de certificado:**

1. Configure um perfil do EAS no aplicativo que atenda aos requisitos na seção anterior.
2. Abra o aplicativo e verifique se o email está sincronizando.

## <a name="next-steps"></a>Passos seguintes

[Informações adicionais sobre a autenticação baseada em certificado em dispositivos Android.](active-directory-certificate-based-authentication-android.md)

[Informações adicionais sobre a autenticação baseada em certificado em dispositivos iOS.](active-directory-certificate-based-authentication-ios.md)
