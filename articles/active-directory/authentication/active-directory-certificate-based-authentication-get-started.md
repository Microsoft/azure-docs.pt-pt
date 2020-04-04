---
title: Autenticação baseada em certificados - Diretório Ativo Azure
description: Saiba como configurar a autenticação baseada em certificados no seu ambiente
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a6c44a8253c81b44d02351b2df9c943d9f358f8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654344"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Introdução à autenticação baseada em certificado no Azure Active Directory

A autenticação baseada em certificados permite-lhe ser autenticado pelo Azure Ative Directory com um certificado de cliente num dispositivo Windows, Android ou iOS ao ligar a sua conta online Exchange a:

- Aplicações móveis da Microsoft como o Microsoft Outlook e o Microsoft Word
- Clientes Exchange ActiveSync (EAS)

Configurar esta funcionalidade elimina a necessidade de introduzir uma combinação de nome de utilizador e palavra-passe em determinadas aplicações de correio e microsoft office no seu dispositivo móvel.

Este tópico:

- Fornece-lhe as medidas para configurar e utilizar a autenticação baseada em certificados para utilizadores de inquilinos nos planos do Office 365 Enterprise, Business, Education e US Government. Esta funcionalidade está disponível em pré-visualização no Office 365 China, Nação de Defesa do Governo dos EUA e planos federais do Governo dos EUA.
- Assume que já tem uma [infraestrutura de chaves públicas (PKI)](https://go.microsoft.com/fwlink/?linkid=841737) e [AD FS](../hybrid/how-to-connect-fed-whatis.md) configuradas.

## <a name="requirements"></a>Requisitos

Para configurar a autenticação baseada em certificados, as seguintes declarações devem ser verdadeiras:

- A autenticação baseada em certificados (CBA) só é suportada para ambientes federados para aplicações de navegador, clientes nativos que utilizem a autenticação moderna (ADAL) ou bibliotecas MSAL. A única exceção é a Exchange Ative Sync (EAS) para o Exchange Online (EXO), que pode ser usada para contas federadas e geridas.
- A autoridade do certificado de raiz e quaisquer autoridades de certificados intermédios devem ser configuradas no Diretório Ativo do Azure.
- Cada autoridade de certificadodeve ter uma lista de revogação de certificados (CRL) que possa ser referenciada através de um URL virado para a Internet.
- Deve ter pelo menos uma autoridade de certificados configurada no Diretório Ativo Azure. Pode encontrar passos relacionados na secção [De configurar as autoridades do certificado.](#step-2-configure-the-certificate-authorities)
- Para os clientes Exchange ActiveSync, o certificado de cliente deve ter o endereço de e-mail redirecionável do utilizador em Exchange online, quer no Nome Principal, quer no valor de nome RFC822 do campo Nome Alternativo sujeito. O Diretório Ativo Azure mapeia o valor RFC822 para o atributo proxy Address no diretório.
- O seu dispositivo cliente deve ter acesso a pelo menos uma autoridade de certificadoque emite certificados de cliente.
- Um certificado de cliente para autenticação de cliente deve ter sido emitido ao seu cliente.

>[!IMPORTANT]
>O tamanho máximo de um CRL para o Azure Ative Directory para descarregar e cache com sucesso é de 20MB, e o tempo necessário para descarregar o CRL não deve exceder 10 segundos.  Se o Azure Ative Directory não conseguir descarregar um CRL, as autenticações baseadas em certificados utilizando certificados emitidos pela CA correspondente falharão. As melhores práticas para garantir que os ficheiros CRL estão dentro das restrições de tamanho são manter as vidas de certificado dentro de limites razoáveis e limpar certificados expirados. 

## <a name="step-1-select-your-device-platform"></a>Passo 1: Selecione a plataforma do seu dispositivo

Como primeiro passo, para a plataforma do dispositivo com a sua preocupação, precisa de rever o seguinte:

- Suporte para aplicações móveis do Office
- Os requisitos específicos de implementação

As informações relacionadas existem para as seguintes plataformas de dispositivos:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>Passo 2: Configure as autoridades do certificado

Para configurar as autoridades de certificados no Diretório Ativo Azure, para cada autoridade de certificado, faça o upload do seguinte:

* A parte pública do certificado, em formato *.cer*
* Os URLs virados para a Internet onde residem as Listas de Revogação de Certificados (CRLs)

O esquema para uma autoridade de certificados é o seguinte:

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

Para a configuração, pode utilizar o [Diretório Ativo Azure PowerShell Versão 2:](/powershell/azure/install-adv2?view=azureadps-2.0)

1. Inicie o Windows PowerShell com privilégios de administrador.
2. Instale a versão do módulo Azure AD [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) ou superior.

        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33

Como primeiro passo de configuração, você precisa estabelecer uma ligação com o seu inquilino. Assim que existir uma ligação com o seu inquilino, pode rever, adicionar, excluir e modificar as autoridades de certificados fidedignos que são definidas no seu diretório.

### <a name="connect"></a>Ligar

Para estabelecer uma ligação com o seu inquilino, utilize o cmdlet [Connect-AzureAD:](/powershell/module/azuread/connect-azuread?view=azureadps-2.0)

    Connect-AzureAD

### <a name="retrieve"></a>Recuperar

Para recuperar as autoridades de certificados fidedignos que estão definidas no seu diretório, utilize o [cmdlet Get-AzureADTrustCertificateAuthority.](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0)

    Get-AzureADTrustedCertificateAuthority

### <a name="add"></a>Adicionar

Para criar uma autoridade de certificado fidedigno, utilize o cmdlet [New-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) e detete teo atributo **crlDistributionPoint** para um valor correto:

    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint="<CRL Distribution URL>"
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca

### <a name="remove"></a>Remover

Para remover uma autoridade de certificado fidedigno, utilize o cmdlet [Remove-AzureADTrustCertificateAuthority:](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0)

    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]

### <a name="modify"></a>Modificar

Para modificar uma autoridade de certificado fidedigno, utilize o [cmdlet Set-AzureADTrustCertificateAuthority:](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0)

    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]

## <a name="step-3-configure-revocation"></a>Passo 3: Reconfigurar a revogação

Para revogar um certificado de cliente, o Azure Ative Directory rebusca a lista de revogação de certificados (CRL) dos URLs enviados como parte de informações da autoridade de certificados e cache-lo. A última marca de tempo de publicação (propriedade**Data Efetiva)** no CRL é utilizada para garantir que o CRL ainda é válido. O CRL é periodicamente referenciado para revogar o acesso a certificados que fazem parte da lista.

Se for necessária uma revogação mais instantânea (por exemplo, se um utilizador perder um dispositivo), o sinal de autorização do utilizador pode ser invalidado. Para invalidar o token de autorização, detete teça o campo **StsRefreshTokenValidFrom** para este utilizador em particular utilizando o Windows PowerShell. Tem de atualizar o campo **StsRefreshTokenValidFrom** para cada utilizador para o que pretende revogar o acesso.

Para garantir que a revogação persiste, deve fixar a **Data Efetiva** do CRL para uma data após o valor definido pela **StsRefreshTokenValidFrom** e garantir que o certificado em questão está no CRL.

Os seguintes passos delineiam o processo de atualização e invalidação do símbolo de autorização, definindo o campo **StsRefreshTokenValidFrom.**

**Para configurar a revogação:**

1. Conecte-se com credenciais de administração ao serviço MSOL:

        $msolcred = get-credential
        connect-msolservice -credential $msolcred

2. Recupere os atuais StsRefreshTokensValidFrom Do valor para um utilizador:

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom

3. Configure um novo StsRefreshTokensValidFromFromDo valor para o utilizador igual ao atual carimbo de tempo:

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

A data que marcou deve ser no futuro. Se a data não for no futuro, a propriedade **StsRefreshTokensValidFrom** não está definida. Se a data for no futuro, o **StsRefreshTokensValidFrom** está definido para a hora atual (não a data indicada pelo comando Set-MsolUser).

## <a name="step-4-test-your-configuration"></a>Passo 4: Testar a sua configuração

### <a name="testing-your-certificate"></a>Testar o seu certificado

Como primeiro teste de configuração, deve tentar iniciar sessão no [Outlook Web Access](https://outlook.office365.com) ou no [SharePoint Online](https://microsoft.sharepoint.com) utilizando o seu navegador no **dispositivo.**

Se o seu sign-in for bem sucedido, então sabe que:

- O certificado de utilizador foi provisionado ao seu dispositivo de teste
- AD FS está configurada corretamente

### <a name="testing-office-mobile-applications"></a>Aplicações móveis do Gabinete de Ensaio

**Para testar a autenticação baseada em certificados na sua aplicação móvel do Office:**

1. No seu dispositivo de teste, instale uma aplicação móvel do Office (por exemplo, OneDrive).
3. Lançar a aplicação.
4. Introduza o seu nome de utilizador e, em seguida, selecione o certificado de utilizador que pretende utilizar.

Devia saqueado com sucesso.

### <a name="testing-exchange-activesync-client-applications"></a>Aplicações de clientes ActiveSync de intercâmbio de testes

Para aceder ao Exchange ActiveSync (EAS) através da autenticação baseada em certificados, um perfil EAS que contenha o certificado de cliente deve estar disponível para a aplicação.

O perfil DaEA deve conter as seguintes informações:

- O certificado de utilizador a utilizar para autenticação

- O ponto final da AEA (por exemplo, outlook.office365.com)

Um perfil EAS pode ser configurado e colocado no dispositivo através da utilização da gestão de dispositivos móveis (MDM) como intune ou colocando manualmente o certificado no perfil EAS no dispositivo.

### <a name="testing-eas-client-applications-on-android"></a>Testar aplicações de clientes DaEAs no Android

**Para testar a autenticação do certificado:**

1. Configure um perfil EAS na aplicação que satisfaça os requisitos na secção anterior.
2. Abra a aplicação e verifique se o correio é sincronizado.

## <a name="next-steps"></a>Passos seguintes

[Informações adicionais sobre autenticação baseada em certificados em dispositivos Android.](active-directory-certificate-based-authentication-android.md)

[Informações adicionais sobre autenticação baseada em certificados em dispositivos iOS.](active-directory-certificate-based-authentication-ios.md)
