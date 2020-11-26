---
title: Autenticação baseada em certificados - Azure Ative Directory
description: Saiba como configurar a autenticação baseada em certificados no seu ambiente
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: ddff11caba9d83e9ed21748fd50a3480d866d8a9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96174569"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Introdução à autenticação baseada em certificado no Azure Active Directory

A autenticação baseada em certificados permite-lhe ser autenticado pelo Azure Ative Directory com um certificado de cliente num dispositivo Windows, Android ou iOS ao ligar a sua conta online Exchange a:

- Aplicações móveis da Microsoft como Microsoft Outlook e Microsoft Word
- Clientes Exchange ActiveSync (EAS)

Configurar esta funcionalidade elimina a necessidade de introduzir um nome de utilizador e uma combinação de palavra-passe em determinadas aplicações de correio e Microsoft Office no seu dispositivo móvel.

Este tópico:

- Fornece-lhe os passos para configurar e utilizar a autenticação baseada em certificados para utilizadores de inquilinos nos planos do Office 365 Enterprise, Business, Education e US Government. Esta funcionalidade está disponível em pré-visualização no Office 365 China, defesa do governo dos EUA e planos federais do governo dos EUA.
- Assume que já tem uma [infraestrutura de chave pública (PKI)](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831740(v=ws.11)) e [FS AD](../hybrid/how-to-connect-fed-whatis.md) configuradas.

## <a name="requirements"></a>Requisitos

Para configurar a autenticação baseada em certificados, as seguintes declarações devem ser verdadeiras:

- A autenticação baseada em certificados (CBA) só é suportada para ambientes federados para aplicações de navegador, clientes nativos que usam autenticação moderna (ADAL) ou bibliotecas MSAL. A única exceção é o Exchange Ative Sync (EAS) para Exchange Online (EXO), que pode ser usado para contas federadas e geridas.
- A autoridade de certificados de raiz e quaisquer autoridades de certificados intermédios devem ser configuradas no Diretório Ativo Azure.
- Cada autoridade de certificado deve ter uma lista de revogação de certificados (CRL) que pode ser referenciada através de um URL virado para a Internet.
- Deve ter pelo menos uma autoridade de certificados configurada no Diretório Ativo Azure. Pode encontrar etapas relacionadas na secção [de informações das autoridades certificados.](#step-2-configure-the-certificate-authorities)
- Para os clientes Exchange ActiveSync, o certificado de cliente deve ter o endereço de e-mail encaminhável do utilizador em Exchange online, quer no Nome Principal, quer no valor do nome RFC822 do campo Nome Alternativo sujeito. O Azure Ative Directory mapeia o valor RFC822 para o atributo Proxy Address no diretório.
- O seu dispositivo cliente deve ter acesso a pelo menos uma autoridade de certificados que emite certificados de cliente.
- Um certificado de cliente para autenticação do cliente deve ter sido emitido para o seu cliente.

>[!IMPORTANT]
>O tamanho máximo de um CRL para o Azure Ative Directory para descarregar e cache com sucesso é de 20MB, e o tempo necessário para descarregar o CRL não deve exceder 10 segundos.  Se o Azure Ative Directory não conseguir descarregar um CRL, as autenticações baseadas em certificados utilizando certificados emitidos pela AC correspondente falharão. As melhores práticas para garantir que os ficheiros CRL estão dentro das restrições de tamanho são manter os prazos de vida dos certificados dentro de limites razoáveis e limpar certificados expirados.

## <a name="step-1-select-your-device-platform"></a>Passo 1: Selecione a plataforma do seu dispositivo

Como primeiro passo, para a plataforma do dispositivo que gosta, precisa de rever o seguinte:

- Suporte de aplicações móveis do Office
- Os requisitos específicos de implementação

As informações relacionadas existem para as seguintes plataformas de dispositivo:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>Passo 2: Configurar as autoridades de certificados

Para configurar as autoridades de certificados no Diretório Ativo Azure, para cada autoridade de certificados, faça o upload:

* A parte pública do certificado, em *formato .cer*
* Os URLs virados para a Internet onde residem as Listas de Revogação de Certificados (LLs)

O esquema de uma autoridade de certificados é o seguinte:

```csharp
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
```

Para a configuração, pode utilizar o [Azure Ative Directory PowerShell Version 2](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0):

1. Inicie o Windows PowerShell com privilégios de administrador.
2. Instale a versão [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) ou superior do módulo AD Azure.

```powershell
    Install-Module -Name AzureAD –RequiredVersion 2.0.0.33
```

Como primeiro passo de configuração, você precisa estabelecer uma ligação com o seu inquilino. Assim que existir uma ligação com o seu inquilino, pode rever, adicionar, excluir e modificar as autoridades de certificados fidedignos que são definidas no seu diretório.

### <a name="connect"></a>Ligar

Para estabelecer uma ligação com o seu inquilino, utilize o cmdlet [Connect-AzureAD:](/powershell/module/azuread/connect-azuread?view=azureadps-2.0)

```azurepowershell
    Connect-AzureAD
```

### <a name="retrieve"></a>Recuperar

Para recuperar as autoridades de certificados fidedignos que estão definidas no seu diretório, utilize o [cmdlet Get-AzureADTrustedCertificateAuthority.](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0)

```azurepowershell
    Get-AzureADTrustedCertificateAuthority
```

### <a name="add"></a>Adicionar

Para criar uma autoridade de certificados de confiança, utilize o cmdlet de certificado [new-AzureADTrustedCertificatee](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) e desconte o atributo **crlDistributionPoint** para um valor correto:

```azurepowershell
    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint="<CRL Distribution URL>"
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca
```

### <a name="remove"></a>Remover

Para remover uma autoridade de certificados fidedigna, utilize o [cmdlet Remove-AzureADTrustedCertificateAuthority:](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0)

```azurepowershell
    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]
```

### <a name="modify"></a>Modificar

Para modificar uma autoridade de certificados fidedigna, utilize o cmdlet de certificado [Set-AzureADTrustedCertificate:](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0)

```azurepowershell
    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]
```

## <a name="step-3-configure-revocation"></a>Passo 3: Revogação de configuração

Para revogar um certificado de cliente, o Azure Ative Directory recolhe a lista de revogação do certificado (CRL) dos URLs carregados como parte da informação da autoridade de certificados e caches-lo. A última data de publicação (propriedade **Data Efetiva)** no CRL é utilizada para garantir que o CRL ainda é válido. O CRL é periodicamente referenciado para revogar o acesso a certificados que fazem parte da lista.

Se for necessária uma revogação mais instantânea (por exemplo, se um utilizador perder um dispositivo), o sinal de autorização do utilizador pode ser invalidado. Para invalidar o token de autorização, desloque o campo **StsRefreshTokenValidD** para este utilizador em particular utilizando o Windows PowerShell. Tem de atualizar o campo **StsRefreshTokenValidD para** cada utilizador para o quais pretende revogar o acesso.

Para garantir que a revogação persista, deve definir a **Data Efetiva** do CRL para uma data após o valor definido pela **StsRefreshTokenValidFrom** e garantir que o certificado em questão está na CRL.

Os passos seguintes descrevem o processo de atualização e invalidação do token de autorização, definindo o campo **StsRefreshTokenValidD.**

**Para configurar a revogação:**

1. Conecte-se com credenciais de administração ao serviço MSOL:

```powershell
        $msolcred = get-credential
        connect-msolservice -credential $msolcred
```

2. Recupere o valor atual da StsRefreshTokensValidFrom para um utilizador:

```powershell
        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom
```

3. Configure um novo valor StsRefreshTokensValidD valor para o utilizador igual à atual timetamp:

```powershell
        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")
```

A data que definiu deve ser no futuro. Se a data não for no futuro, a propriedade **StsRefreshTokensValidFrom** não está definida. Se a data for no futuro, **o StsRefreshTokensValidFrom** está definido para a hora atual (não a data indicada pelo comando Set-MsolUser).

## <a name="step-4-test-your-configuration"></a>Passo 4: Teste a sua configuração

### <a name="testing-your-certificate"></a>Testar o seu certificado

Como primeiro teste de configuração, deverá tentar iniciar sação no [Outlook Web Access](https://outlook.office365.com) ou no [SharePoint Online](https://microsoft.sharepoint.com) utilizando o seu navegador no **dispositivo**.

Se o seu s-in for bem sucedido, então sabe que:

- O certificado de utilizador foi a provisionado ao seu dispositivo de teste
- AD FS está configurada corretamente

### <a name="testing-office-mobile-applications"></a>Aplicações móveis do Gabinete de Testes

**Para testar a autenticação baseada em certificados na sua aplicação móvel Office:**

1. No seu dispositivo de teste, instale uma aplicação móvel do Office (por exemplo, OneDrive).
3. Lance a aplicação.
4. Introduza o seu nome de utilizador e, em seguida, selecione o certificado de utilizador que pretende utilizar.

Devias ter assinado com sucesso.

### <a name="testing-exchange-activesync-client-applications"></a>Teste de aplicações de clientes ActiveSync de Intercâmbio

Para aceder ao Exchange ActiveSync (EAS) através da autenticação baseada em certificados, deve estar disponível um perfil EAS que contenha o certificado de cliente.

O perfil EAS deve conter as seguintes informações:

- O certificado de utilizador a utilizar para a autenticação

- O ponto final da AEA (por exemplo, outlook.office365.com)

Um perfil EAS pode ser configurado e colocado no dispositivo através da utilização da gestão de dispositivos móveis (MDM) como o Intune ou colocando manualmente o certificado no perfil EAS no dispositivo.

### <a name="testing-eas-client-applications-on-android"></a>Testar aplicações de clientes EAS no Android

**Para testar a autenticação do certificado:**

1. Configurar um perfil EAS na aplicação que satisfaça os requisitos na secção anterior.
2. Abra a aplicação e verifique se o correio está sincronizado.

## <a name="next-steps"></a>Passos seguintes

[Informações adicionais sobre a autenticação baseada em certificados em dispositivos Android.](active-directory-certificate-based-authentication-android.md)

[Informações adicionais sobre a autenticação baseada em certificados em dispositivos iOS.](active-directory-certificate-based-authentication-ios.md)