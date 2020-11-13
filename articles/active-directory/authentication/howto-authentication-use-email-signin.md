---
title: Inicie sessão com e-mail como iD de login alternativo para Azure Ative Directory
description: Saiba como configurar e permitir que os utilizadores iniciem sessão no Azure Ative Directory utilizando o seu endereço de e-mail como ID de login alternativo (pré-visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calui
ms.openlocfilehash: c3fcff5673f4498e92f5d66fe96d806a08527197
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576024"
---
# <a name="sign-in-to-azure-active-directory-using-email-as-an-alternate-login-id-preview"></a>Iniciar sessão no Azure Ative Directory usando o e-mail como iD de login alternativo (pré-visualização)

> [!NOTE]
> Iniciar sessão no Azure AD com e-mail como ID de login alternativo é uma funcionalidade de pré-visualização pública do Azure Ative Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Muitas organizações querem permitir que os utilizadores inscrevam-se no Azure Ative Directory (Azure AD) usando as mesmas credenciais que o seu ambiente de diretório no local. Com esta abordagem, conhecida como autenticação híbrida, os utilizadores apenas precisam de se lembrar de um conjunto de credenciais.

Algumas organizações não mudaram para a autenticação híbrida pelas seguintes razões:

* Por predefinição, o nome principal do utilizador Azure AD (UPN) é definido para o mesmo UPN que o diretório no local.
* A alteração da UPN AZure cria uma correspondência errada entre ambientes on-prem e Ad Azure que podem causar problemas com certas aplicações e serviços.
* Devido a razões de negócio ou conformidade, a organização não quer usar a UPN no local para assinar no Azure AD.

Para ajudar na mudança para a autenticação híbrida, pode agora configurar a AD Azure para permitir que os utilizadores iniciem sessão com um e-mail no seu domínio verificado como um ID de login alternativo. Por exemplo, se *Contoso* rebranded para *Fabrikam* , em vez de continuar a assinar com o legado `balas@contoso.com` UPN, o e-mail como um ID de login alternativo pode agora ser usado. Para aceder a uma aplicação ou serviços, os utilizadores iniciariam sessão no AZure AD utilizando o seu e-mail atribuído, como `balas@fabrikam.com` .

Este artigo mostra-lhe como ativar e usar o e-mail como um ID de login alternativo. Esta funcionalidade está disponível na edição Azure AD Free e superior.

> [!NOTE]
> Esta funcionalidade destina-se apenas aos utilizadores Azure AD autenticados na nuvem.

## <a name="overview-of-azure-ad-sign-in-approaches"></a>Visão geral das abordagens de inscrição da AD AZure

Para iniciar seduca no Azure AD, os utilizadores introduzem um nome que identifica exclusivamente a sua conta. Historicamente, só se pode usar o Azure AD UPN como nome de inscrição.

Para as organizações onde a UPN no local é o e-mail de inscrição preferido do utilizador, esta abordagem foi ótima. Essas organizações definiriam a Azure AD UPN exatamente o mesmo valor que a UPN no local, e os utilizadores teriam uma experiência de inscrição consistente.

No entanto, em algumas organizações, a UPN não é usada como um nome de inscrição. Nos ambientes no local, configuraria o DS AD local para permitir o login com um ID de login alternativo. Definir o Azure AD UPN para o mesmo valor que a UPN no local não é uma opção, uma vez que a Azure AD exigiria que os utilizadores inscrevam-se com esse valor.

A solução típica para este problema foi definir o Azure AD UPN para o endereço de e-mail com o que o utilizador espera iniciar súm. Esta abordagem funciona, embora resulte em diferentes UPNs entre o AD no local e em Azure AD, e esta configuração não é compatível com todas as cargas de trabalho da Microsoft 365.

Uma abordagem diferente é sincronizar o AZure AD e as UPNs no local pelo mesmo valor e, em seguida, configurar a Azure AD para permitir que os utilizadores inscrevam-se no AD Azure com um e-mail verificado. Para fornecer esta capacidade, define um ou mais endereços de e-mail nos *ProxyAddresses* do utilizador atribuídos no diretório no local. *Os ProxyAddresses* são então sincronizados para Azure AD automaticamente usando O AZURE AD Connect.

## <a name="preview-limitations"></a>Limitações de pré-visualização

Inicie sessão no Azure AD com e-mail como iD de login alternativo está disponível na edição Azure AD Free e superior.

No estado de pré-visualização atual, aplicam-se as seguintes limitações quando um utilizador assina com um e-mail não UPN como um ID de login alternativo:

* Os utilizadores podem ver a sua UPN, mesmo quando o bôde-assinado com o seu e-mail não UPN. Pode ver-se o seguinte exemplo:
    * Solicita-se ao utilizador que assine com a UPN quando direcionado para o Azure AD com `login_hint=<non-UPN email>` .
    * Quando um utilizador faz sê-lo com um e-mail não UPN e introduz uma palavra-passe incorreta, a página *"Introduza a sua palavra-passe"* altera-se para exibir a UPN.
    * Em alguns sites e aplicações da Microsoft, tais como [https://portal.azure.com](https://portal.azure.com) e Microsoft Office, o controlo **do Gestor de Contas** normalmente exibido no canto superior direito pode exibir a UPN do utilizador em vez do e-mail não UPN usado para iniciar scontabilidade.

* Alguns fluxos não são atualmente compatíveis com o e-mail não UPN, tais como:
    * A proteção de identidade não corresponde atualmente a IDs de login alternativos de e-mail com deteção de risco *de credenciais vazadas.* Esta deteção de risco utiliza a UPN para corresponder às credenciais que foram vazadas. Para obter mais informações, consulte [a deteção e reparação de riscos de proteção de identidade Azure AD][identity-protection].
    * Os convites B2B enviados para um e-mail alternativo de identificação de login não estão totalmente suportados. Depois de aceitar um convite enviado para um e-mail como um ID de login alternativo, inicie sessão com o e-mail alternativo pode não funcionar para o utilizador no ponto final do arrendado.

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>Sincronizar endereços de e-mail de inscrição para Azure AD

A autenticação dos Serviços tradicionais de Domínio do Diretório Ativo (AD DS) ou dos Serviços da Federação de Diretórios Ativos (AD FS) ocorre diretamente na sua rede e é gerida pela sua infraestrutura de DS AD. Com a autenticação híbrida, os utilizadores podem, em vez disso, iniciar súmis diretamente no Azure AD.

Para suportar esta abordagem de autenticação híbrida, sincroniza o ambiente AD DS no local para a Azure AD utilizando [o Azure AD Connect][azure-ad-connect] e configura-o para utilizar o Password Hash Sync (PHS) ou Pass-Through Autenticação (PTA).

Em ambas as opções de configuração, o utilizador submete o seu nome de utilizador e palavra-passe ao Azure AD, que valida as credenciais e emite um bilhete. Quando os utilizadores insinam-se no Azure AD, elimina a necessidade de a sua organização hospedar e gerir uma infraestrutura AD FS.

![Diagrama de identidade híbrida AD AD Azure com sincronização de hash de palavra-passe](media/howto-authentication-use-email-signin/hybrid-password-hash-sync.png)

![Diagrama de identidade híbrida AD AD Azure com autenticação pass-through](media/howto-authentication-use-email-signin/hybrid-pass-through-authentication.png)

Um dos atributos do utilizador que é automaticamente sincronizado pelo Azure AD Connect é *o ProxyAddresses*. Se os utilizadores tiverem um endereço de e-mail definido no ambiente DS AD on-prem como parte do atributo *ProxyAddresses,* é automaticamente sincronizado com Azure AD. Este endereço de e-mail pode então ser usado diretamente no processo de login AZure AD como um ID de login alternativo.

> [!IMPORTANT]
> Apenas os e-mails em domínios verificados para o inquilino são sincronizados com a Azure AD. Cada inquilino da AD AZure tem um ou mais domínios verificados, para os quais provou a sua propriedade, e está exclusivamente ligado a si inquilino.
>
> Para obter mais informações, consulte [Adicionar e verificar um nome de domínio personalizado em Azure AD][verify-domain].

Para mais informações, consulte [Escolha o método de autenticação certo para a sua solução de identidade híbrida AZure AD.][hybrid-auth-methods]

## <a name="enable-user-sign-in-with-an-email-address"></a>Ativar o sôm-in do utilizador com um endereço de e-mail

Uma vez que os utilizadores com o atributo *ProxyAddresss* são sincronizados com a AD AZure usando o Azure AD Connect, é necessário ativar a funcionalidade para os utilizadores iniciarem sessão com e-mail como um ID de login alternativo para o seu inquilino. Esta funcionalidade diz aos servidores de login AD Azure para não só verificarem o nome de entrada contra os valores UPN, mas também contra os *valores de ProxyAddresses* para o endereço de e-mail.

Durante a pré-visualização, atualmente só pode ativar o login com e-mail como uma função de ID de login alternativa usando o PowerShell. Precisa de permissões de administrador de *inquilinos* para completar os seguintes passos:

1. Abra uma sessão PowerShell como administrador e, em seguida, instale o módulo *AzureADPreview* utilizando o cmdlet [install-Module:][Install-Module]

    ```powershell
    Install-Module AzureADPreview
    ```

    Se solicitado, selecione **Y** para instalar o NuGet ou para instalar a partir de um repositório não fidedvo.

1. Inscreva-se no seu inquilino Azure AD como *administrador de inquilino* utilizando o cmdlet [Connect-AzureAD:][Connect-AzureAD]

    ```powershell
    Connect-AzureAD
    ```

    O comando devolve informações sobre a sua conta, ambiente e identificação do inquilino.

1. Verifique se a política *homeRealmDiscoveryPolicy* já existe no seu inquilino usando o cmdlet [Get-AzureADPolicy][Get-AzureADPolicy] da seguinte forma:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

1. Se não houver nenhuma política configurada, o comando não devolve nada. Se uma política for devolvida, ignore este passo e passe para o próximo passo para atualizar uma política existente.

    Para adicionar a política *homeRealmDiscoveryPolicy* ao inquilino, use o cmdlet [New-AzureADPolicy][New-AzureADPolicy] e coloque o atributo *AlternateIdLogin* a *"Enabled": verdadeiro* como mostrado no seguinte exemplo:

    ```powershell
    New-AzureADPolicy -Definition @('{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    Quando a apólice foi criada com sucesso, o comando devolve o ID da política, como mostra a seguinte saída de exemplo:

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. Se já existe uma política configurada, verifique se o atributo *AlternateIdLogin*   está ativado, como mostra o seguinte exemplo de saída de política:

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

    Se a política existir mas o atributo *AlternateIdLogin* que não está presente ou ativado, ou se existirem outros atributos na política que pretende preservar, atualize a política existente utilizando o [cmdlet Set-AzureADPolicy.][Set-AzureADPolicy]

    > [!IMPORTANT]
    > Quando atualizar a apólice, certifique-se de que inclui quaisquer definições antigas e o novo atributo *AlternateIdLogin.*

    O exemplo a seguir adiciona o atributo *AlternateIdLogin* e preserva o atributo *AllowCloudPasswordValidation* que pode já ter sido definido:

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

Com a política aplicada, pode levar até uma hora para se propagar e para que os utilizadores possam iniciar sessão usando o seu ID de login alternativo.

## <a name="test-user-sign-in-with-email"></a>Teste de s-in do utilizador com e-mail

Para testar que os utilizadores podem iniciar scontabilidade com e-mail, navegue [https://myprofile.microsoft.com][my-profile] e inscreva-se com uma conta de utilizador com base no seu endereço de e-mail, `balas@fabrikam.com` como, por exemplo, não a SUA UPN, como `balas@contoso.com` . . A experiência de inscrição deve parecer e sentir o mesmo que com um evento de inscrição baseado na UPN.

## <a name="enable-staged-rollout-to-test-user-sign-in-with-an-email-address"></a>Ativar o lançamento encenado para testar o sence do utilizador com um endereço de e-mail  

[O lançamento encenado][staged-rollout] permite aos administradores de inquilinos permitir funcionalidades para grupos específicos. Recomenda-se que os administradores de inquilinos utilizem o roll-in encenado para testar o pedido de entrada do utilizador com um endereço de e-mail. Quando os administradores estiverem prontos para implementar esta funcionalidade em todo o seu inquilino, devem usar uma política de Home Realm Discovery.  


Precisa de permissões de administrador de *inquilinos* para completar os seguintes passos:

1. Abra uma sessão PowerShell como administrador e, em seguida, instale o módulo *AzureADPreview* utilizando o cmdlet [install-Module:][Install-Module]

    ```powershell
    Install-Module AzureADPreview
    ```

    Se solicitado, selecione **Y** para instalar o NuGet ou para instalar a partir de um repositório não fidedvo.

2. Inscreva-se no seu inquilino Azure AD como *administrador de inquilino* utilizando o cmdlet [Connect-AzureAD:][Connect-AzureAD]

    ```powershell
    Connect-AzureAD
    ```

    O comando devolve informações sobre a sua conta, ambiente e identificação do inquilino.

3. Listar todas as políticas de lançamento encenadas existentes utilizando o seguinte cmdlet:
   
   ```powershell
   Get-AzureADMSFeatureRolloutPolicy
   ``` 

4. Se não houver políticas de lançamento encenadas existentes para esta funcionalidade, crie uma nova política de implementação encenada e tome nota do ID da política:

   ```powershell
   New-AzureADMSFeatureRolloutPolicy -Feature EmailAsAlternateId -DisplayName "EmailAsAlternateId Rollout Policy" -IsEnabled $true
   ```

5. Encontre o iD do directórioObject para que o grupo seja adicionado à política de lançamento encenada. Note o valor devolvido para o parâmetro *ID,* porque será usado no passo seguinte.
   
   ```powershell
   Get-AzureADMSGroup -SearchString "Name of group to be added to the staged rollout policy"
   ```

6. Adicione o grupo à política de lançamento encenada, como mostra o exemplo a seguir. Substitua o valor no parâmetro *-Id* pelo valor devolvido para o ID da apólice no passo 4 e substitua o valor no parâmetro *-RefObjectId* pelo *ID* indicado no passo 5. Pode demorar até 1 hora até que os utilizadores do grupo possam usar os seus endereços de procuração para iniciar surgiu.

   ```powershell
   Add-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -RefObjectId "GROUP_OBJECT_ID"
   ```
   
Para os novos membros adicionados ao grupo, pode demorar até 24 horas até que possam usar os seus endereços de procuração para iniciar siva.

### <a name="removing-groups"></a>Remoção de grupos

Para remover um grupo de uma política de lançamento encenada, executar o seguinte comando:

```powershell
Remove-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -ObjectId "GROUP_OBJECT_ID" 
```

### <a name="removing-policies"></a>Remoção de políticas

Para remover uma política de implementação encenada, primeiro desative a política e depois remova-a do sistema:

```powershell
Set-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID" -IsEnabled $false 
Remove-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID"
```

## <a name="troubleshoot"></a>Resolução de problemas

Se os utilizadores tiverem problemas com eventos de inscrição utilizando o seu endereço de e-mail, reveja as seguintes etapas de resolução de problemas:

1. Certifique-se de que a conta de utilizador tem o seu endereço de e-mail definido para o atributo *ProxyAddresses* no ambiente DS AD on-prem.
1. Verifique se o Azure AD Connect está configurado e sincroniza com sucesso as contas dos utilizadores do ambiente DS AD pré-pré-m em Azure AD.
1. Confirme que a política Azure AD *HomeRealmDiscoveryPolicy* tem o atributo *AlternateIdLogin* definido como *"Enabled": verdadeiro* :

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a identidade híbrida, como a Azure AD App Proxy ou a Azure AD Domain Services, consulte [a identidade híbrida AD AD Azure para acesso e gestão de cargas de trabalho on-prem][hybrid-overview].

Para obter mais informações sobre operações de identidade híbrida, consulte como funciona a sincronização de [haxixe de palavra-passe][phs-overview] ou a [sincronização de autenticação de autenticação pass-through.][pta-overview]

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md
[identity-protection]: ../identity-protection/overview-identity-protection.md#risk-detection-and-remediation

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[staged-rollout]: /powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged-rollout
[my-profile]: https://myprofile.microsoft.com
