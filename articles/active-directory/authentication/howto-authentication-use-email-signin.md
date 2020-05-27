---
title: Inscreva-se com e-mail como id de login alternativo para Diretório Ativo Azure
description: Saiba como configurar e permitir que os utilizadores iniciem sessão no Azure Ative Directory usando o seu endereço de e-mail como um ID de login alternativo (pré-visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/22/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: scottsta
ms.openlocfilehash: ed317039e683ef36054d5ace612e09ca75dfa11e
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837389"
---
# <a name="sign-in-to-azure-using-email-as-an-alternate-login-id-preview"></a>Iniciar sessão no Azure usando o e-mail como um ID de login alternativo (pré-visualização)

Muitas organizações querem permitir que os utilizadores inscrevam-se no Azure usando as mesmas credenciais que o seu ambiente de diretório no local. Com esta abordagem, conhecida como autenticação híbrida, os utilizadores só precisam de se lembrar de um conjunto de credenciais.

Algumas organizações não passaram para a autenticação híbrida pelas seguintes razões:

* Por predefinição, o nome principal do utilizador do Azure Ative Directory (Azure AD) (UPN) é definido para o mesmo UPN que o diretório no local.
* A alteração da UpN Azure cria uma correspondência indevida entre ambientes on-prem e Azure que podem causar problemas com determinadas aplicações e serviços.
* Devido a razões de negócio ou conformidade, a organização não quer usar a UPN no local para iniciar a sua inscrição no Azure.

Para ajudar na mudança para a autenticação híbrida, pode agora configurar o Azure AD para permitir que os utilizadores iniciem sessão no Azure com um e-mail no seu domínio verificado como um ID de login alternativo. Por exemplo, se *Contoso* renomeou *Fabrikam*, em vez de continuar a assinar com o legado `balas@contoso.com` UPN, o e-mail como um ID de login alternativo pode agora ser usado. Para aceder a uma aplicação ou serviços, os utilizadores iniciariam o contrato com o Azure utilizando o seu e-mail atribuído, como `balas@fabrikam.com` .

|     |
| --- |
| Iniciar sessão no Azure AD com e-mail como um ID de login alternativo é uma funcionalidade de pré-visualização pública do Azure Ative Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="overview-of-azure-ad-sign-in-approaches"></a>Visão geral das abordagens de entrada da AD Azure

Os Nomes Principais do Utilizador (UPNs) são identificadores únicos para uma conta de utilizador tanto no seu diretório no local como no Azure AD. Cada conta de utilizador num diretório é representada por uma UPN, como `balas@contoso.com` . Por predefinição, ao sincronizar um ambiente no local Ative Directory Domain Services (AD DS) com a Azure AD, o Azure AD UPN deverá coincidir com a UPN no local.

Em muitas organizações, é bom definir as instalações UPN e Azure AD UPN para combinar. Quando os utilizadores assinam as aplicações e serviços do Azure, utilizam o seu Azure AD UPN. No entanto, algumas organizações não podem usar UPNs correspondentes para iniciar sessão devido a políticas de negócio ou problemas de experiência do utilizador.

As organizações que não podem usar UPNs correspondentes em Azure AD têm algumas opções:

* Uma abordagem é definir o Azure AD UPN para algo diferente com base nas necessidades do negócio, como `balas@fabrikam.com` .
    * No entanto, nem todas as aplicações e serviços são compatíveis com a utilização de um valor diferente para as instalações UPN e Azure AD UPN.
* Uma melhor abordagem é garantir que as UPNs AD Azure e no local estão definidas com o mesmo valor, e configurar o Azure AD para permitir que os utilizadores assinem no Azure com o seu e-mail como um ID de login alternativo.

Com o e-mail como um ID de login alternativo, os utilizadores ainda podem iniciar sessão no Azure, inserindo a sua UPN, mas também podem iniciar sessão através do seu e-mail. Para suportar isto, define um endereço de e-mail no atributo *proxyAddresss* do utilizador no diretório no local. Este atributo *ProxyAddress* suporta um ou mais endereços de e-mail.

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>Sincronizar endereços de e-mail de entrada para a AD Azure

A autenticação tradicional dos Serviços de Domínio Ativo (AD DS) ou da Ative Directory Federation Services (AD FS) ocorre diretamente na sua rede e é tratada pela sua infraestrutura AD DS. Com a autenticação híbrida, os utilizadores podem, em vez disso, iniciar sessão diretamente para a Azure AD.

Para suportar esta abordagem de autenticação híbrida, sincroniza o ambiente AD DS no local para o Azure AD, utilizando o [Azure AD Connect][azure-ad-connect] e configurá-lo para utilizar o Password Hash Sync (PHS) ou a Autenticação Pass-Through (PTA).

Em ambas as opções de configuração, o utilizador submete o seu nome de utilizador e senha à Azure AD, que valida as credenciais e emite um bilhete. Quando os utilizadores entram no Azure AD, elimina a necessidade de a sua organização hospedar e gerir uma infraestrutura AD FS.

![Diagrama da identidade híbrida Azure AD com sincronização de hash de senha](media/howto-authentication-use-email-signin/hybrid-password-hash-sync.png)

![Diagrama da identidade híbrida Azure AD com autenticação pass-through](media/howto-authentication-use-email-signin/hybrid-pass-through-authentication.png)

Um dos atributos do utilizador que é automaticamente sincronizado pelo Azure AD Connect é *proxyAddresss*. Se os utilizadores tiverem um endereço de e-mail definido no ambiente ad dS on-prem como parte do atributo *proxyAddresss,* é automaticamente sincronizado para AD Azure. Este endereço de e-mail pode então ser utilizado diretamente no processo de entrada de adato do Azure como um ID de login alternativo.

> [!IMPORTANT]
> Apenas os e-mails em domínios verificados para o inquilino são sincronizados para a AD Azure. Cada inquilino da AD Azure tem um ou mais domínios verificados, para os quais provou a propriedade, e está exclusivamente ligado ao seu inquilino.
>
> Para mais informações, consulte Adicionar e verificar um nome de [domínio personalizado em Azure AD][verify-domain].

Para mais informações, consulte [Escolha o método de autenticação certo para a sua solução de identidade híbrida Azure AD][hybrid-auth-methods].

## <a name="enable-user-sign-in-with-an-email-address"></a>Ativar o sessão de sessão do utilizador com um endereço de e-mail

Uma vez que os utilizadores com o atributo *proxyAddresses* aplicado sincronizados para o Azure AD Connect utilizando o Azure AD Connect, é necessário ativar a funcionalidade para os utilizadores iniciarem sessão com e-mail como um ID de login alternativo para o seu inquilino. Esta funcionalidade indica aos servidores de login Azure AD para verificarnão só o nome de entrada em relação aos valores UPN, mas também contra os *valores proxyAddresss* para o endereço de e-mail.

Durante a pré-visualização, atualmente só pode ativar o login com e-mail como uma funcionalidade de ID de login alternativa utilizando o PowerShell. Precisa de permissões de *administrador de inquilinopara* completar os seguintes passos:

1. Abra uma sessão powerShell como administrador e, em seguida, instale o módulo *AzureADPreview* utilizando o cmdlet [de Módulo de Instalação:][Install-Module]

    ```powershell
    Install-Module AzureADPreview
    ```

    Se for solicitado, selecione **Y** para instalar o NuGet ou para instalar a partir de um repositório não confiável.

1. Inscreva-se no seu inquilino Azure AD como *administrador de inquilino* sintetizador utilizando o cmdlet [Connect-AzureAD:][Connect-AzureAD]

    ```powershell
    Connect-AzureAD
    ```

    O comando devolve informações sobre a sua conta, ambiente e identificação do inquilino.

1. Verifique se a política *HomeRealmDiscoveryPolicy* já existe no seu inquilino usando o cmdlet [Get-AzureADPolicy][Get-AzureADPolicy] da seguinte forma:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

1. Se não houver nenhuma política configurada atualmente, o comando não devolve nada. Se uma política for devolvida, ignore este passo e passe para o próximo passo para atualizar uma política existente.

    Para adicionar a política *HomeRealmDiscoveryPolicy* ao inquilino, use o cmdlet [New-AzureADPolicy][New-AzureADPolicy] e defino o atributo *AlternateIdLogin* para *"Enabled": verdadeiro* como mostrado no seguinte exemplo:

    ```powershell
    New-AzureADPolicy -Definition @('{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    Quando a política foi criada com sucesso, o comando devolve o ID da política, como mostra a seguinte saída de exemplo:

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. Se já existe uma política configurada, verifique se o atributo *AlternateIdLogin*   está ativado, como mostra a seguinte saída de política exemplo:

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    Se a política existir, mas o atributo *AlternateIdLogin* que não está presente ou ativado, ou se existirem outros atributos na política que pretende preservar, atualize a política existente utilizando o cmdlet [Set-AzureADPolicy.][Set-AzureADPolicy]

    > [!IMPORTANT]
    > Quando atualizar a apólice, certifique-se de que inclui quaisquer definições antigas e o novo atributo *AlternateIdLogin.*

    O exemplo seguinte adiciona o atributo *AlternateIdLogin* e preserva o atributo *allowCloudPasswordValidação* que pode já ter sido definido:

    ```powershell
    Set-AzureADPolicy -id b581c39c-8fe3-4bb5-b53d-ea3de05abb4b `
        -Definition @('{"HomeRealmDiscoveryPolicy" :{"AllowCloudPasswordValidation":true,"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    Confirme que a política atualizada mostra as suas alterações e que o atributo *AlternateIdLogin* está agora ativado:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="test-user-sign-in-with-email"></a>Teste de inscrição do utilizador com e-mail

Para testar que os utilizadores podem iniciar sessão com e-mail, navegar [https://myprofile.microsoft.com][my-profile] e iniciar sessão com uma conta de utilizador com base no seu endereço de e-mail, como, não a `balas@fabrikam.com` sua UPN, como `balas@contoso.com` . A experiência de inscrição deve parecer e sentir o mesmo que com um evento de inscrição baseado na UPN.

## <a name="troubleshoot"></a>Resolução de problemas

Se os utilizadores tiverem problemas com eventos de sessão utilizando o seu endereço de e-mail, reveja as seguintes etapas de resolução de problemas:

1. Certifique-se de que a conta de utilizador tem o seu endereço de e-mail definido para o atributo *proxyAddresss* no ambiente AD DS on-prem.
1. Verifique se o Azure AD Connect está configurado e sincroniza com sucesso as contas dos utilizadores do ambiente AD DS on-prem para o Azure AD.
1. Confirme que a política Azure *HomeRealmDiscoveryPolicy* tem o conjunto de atributo *sinuoso* *"Habilitado": verdadeiro*:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre identidade híbrida, como o Azure AD App Proxy ou o Azure AD Domain Services, consulte a [identidade híbrida Azure AD para acesso e gestão de cargas de trabalho on-prem.][hybrid-overview]

Para obter mais informações sobre operações de identidade híbrida, consulte como funciona a sincronização de sincronização de hash de [palavra-passe][phs-overview] ou a sincronização de [autenticação pass-through.][pta-overview]

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[my-profile]: https://myprofile.microsoft.com
