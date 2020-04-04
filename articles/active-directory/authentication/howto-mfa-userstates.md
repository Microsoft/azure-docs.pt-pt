---
title: Autenticação por utilizador multifactor - Diretório Ativo Azure
description: Ativar o MFA alterando os estados de utilizador na autenticação de multifactor estoque.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e955a3d10eea0a23154396a44fe6e1fc552a9ccb
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653482"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Como exigir verificação em duas etapas para um utilizador

Pode tomar uma de duas abordagens para exigir uma verificação em duas etapas, ambas requerem a utilização de uma conta de administrador global. A primeira opção é ativar cada utilizador para autenticação de multi-factores Azure (MFA). Quando os utilizadores são ativados individualmente, realizam verificação em duas etapas cada vez que entram (com algumas exceções, como quando entram a partir de endereços IP fidedignos ou quando a função de _dispositivos recordados_ é ativada). A segunda opção é criar uma política de acesso condicional que exija uma verificação em duas etapas em determinadas condições.

> [!TIP]
> Permitir a autenticação multi-factor azure utilizando políticas de acesso condicional é a abordagem recomendada. A alteração dos estados de utilizador já não é recomendada a menos que as suas licenças não incluam o Acesso Condicional, uma vez que exigirá que os utilizadores realizem MFA sempre que iniciarem o seu insessão.

## <a name="choose-how-to-enable"></a>Escolha como ativar

**Habilitado pela alteração** do estado do utilizador - Este é o método tradicional para exigir uma verificação em duas etapas e é discutido neste artigo. Funciona com o Azure MFA na nuvem e com o Azure MFA Server. A utilização deste método requer que os utilizadores realizem uma verificação em duas etapas **sempre** que entram e substituem as políticas de Acesso Condicional.

**Ativado pela política** de acesso condicional - Este é o meio mais flexível para permitir a verificação em duas etapas para os seus utilizadores. Permitir a utilização da política de Acesso Condicional só funciona para o Azure MFA na nuvem e é uma característica premium da Azure AD. Mais informações sobre este método podem ser encontradas na Implementação da [Autenticação multi-factor Azure baseada na nuvem.](howto-mfa-getstarted.md)

**Habilitado pela Azure AD Identity Protection** - Este método utiliza a política de risco de proteção de identidade Azure AD para exigir uma verificação em duas etapas baseada apenas no risco de entrada em todas as aplicações na nuvem. Este método requer licenciamento Azure Ative Directory P2. Mais informações sobre este método podem ser encontradas na Proteção de Identidade do [Diretório Ativo do Azure](../identity-protection/howto-sign-in-risk-policy.md)

> [!Note]
> Mais informações sobre licenças e preços podem ser encontradas nas páginas de preços de Autenticação De [AD Azure](https://azure.microsoft.com/pricing/details/active-directory/
) e [Multi-Factor.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

## <a name="enable-azure-mfa-by-changing-user-state"></a>Ativar o Azure MFA alterando o estado de utilizador

As contas de utilizador na Autenticação Multifactor Azure têm os seguintes três estados distintos:

> [!IMPORTANT]
> Permitir o Azure MFA através de uma política de Acesso Condicional não mudará o estado do utilizador. Não se assuste que os utilizadores pareçam desativados. O Acesso Condicional não muda o Estado. **As organizações não devem ativar ou impor os utilizadores se estiverem a utilizar políticas de acesso condicional.**

| Estado | Descrição | Aplicações não-navegador afetadas | Aplicativos de navegador afetados | Autenticação moderna afetada |
|:---:| --- |:---:|:--:|:--:|
| Desativado | O estado padrão para um novo utilizador não inscrito no Azure MFA. | Não | Não | Não |
| Ativado | O utilizador foi inscrito no Azure MFA, mas não se registou. Recebem uma solicitação para se registarem da próxima vez que assinarem. | Não.  Continuam a trabalhar até que o processo de registo esteja concluído. | Sim. Após o termo da sessão, é necessário registar o Azure MFA.| Sim. Após o termo do sinal de acesso, é necessário o registo do Azure MFA. |
| Imposto | O utilizador foi inscrito e concluiu o processo de registo do Azure MFA. | Sim. As aplicações requerem senhas de aplicação. | Sim. O Azure MFA é necessário no login. | Sim. O Azure MFA é necessário no login. |

O estado de um utilizador reflete se um administrador os inscreveu no Azure MFA e se completaram o processo de registo.

Todos os utilizadores iniciam *o Desativado*. Quando inscreve os utilizadores no Azure MFA, o seu estado muda para *Enabled*. Quando os utilizadores habilitados assinam e completam o processo de registo, o seu estado muda para *O Imposto*.

> [!NOTE]
> Se o MFA for reativado num objeto de utilizador que já tenha dados de registo, como telefone ou e-mail, os administradores precisam de ter esse utilizador a reregistar o MFA através do portal Azure ou da PowerShell. Se o utilizador não se reregistar, o seu estado de MFA não transita de *Enabled* para *Enforced* na UI de gestão de MFA.

### <a name="view-the-status-for-a-user"></a>Ver o estado de um utilizador

Utilize os seguintes passos para aceder à página onde pode ver e gerir os estados do utilizador:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Procure e selecione *Azure Active Directory*. Selecione **Utilizadores** > **Todos os utilizadores**.
3. **Selecione a autenticação de vários fatores.** Pode ser necessário deslocar-se para a direita para ver esta opção de menu. Selecione a imagem de exemplo abaixo para ver a janela completa do portal Azure e a localização do menu:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Selecione autenticação multi-factor da janela utilizadores em Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
4. Abre-se uma nova página que exibe os estados de utilizador.
   ![estado do utilizador da autenticação de vários fatores - screenshot](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Alterar o estado para um utilizador

1. Utilize os passos anteriores para chegar à página de **utilizadores** de autenticação multifactor Azure.
2. Encontre o utilizador que pretende ativar para o Azure MFA. Talvez precises de mudar a vista no topo.
   ![Selecione o utilizador para alterar o estado do separador utilizadores](./media/howto-mfa-userstates/enable1.png)
3. Verifique a caixa ao lado do nome deles.
4. À direita, sob **passos rápidos,** escolha **Ativar** ou **Desativar**.
   ![Ativar o utilizador selecionado clicando em ativar no menu passos rápidos](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Os utilizadores *ativados* são automaticamente transferidos para *'Enforced'* quando se registam para o Azure MFA. Não altere manualmente o estado do utilizador para *'Imposto'.*

5. Confirme a sua seleção na janela pop-up que se abre.

Depois de ativar os utilizadores, notifique-os por e-mail. Diga-lhes que serão convidados a registarem-se da próxima vez que assinarem. Além disso, se a sua organização usa aplicações não-navegador as que não suportam a autenticação moderna, precisam de criar senhas de aplicação. Também pode incluir um link para o guia de utilizador final do [Azure MFA para ajudá-los](../user-help/multi-factor-authentication-end-user.md) a começar.

### <a name="use-powershell"></a>Utilizar o PowerShell

Para alterar o estado de utilizador utilizando o `$st.State` [Azure AD PowerShell,](/powershell/azure/overview)altere . Há três estados possíveis:

* Ativado
* Imposto
* Desativado  

Não mova os utilizadores diretamente para o estado *forçado.* Se o fizer, as aplicações não baseadas no navegador deixam de funcionar porque o utilizador não passou pelo registo do Azure MFA e obteve uma senha de [aplicação.](howto-mfa-mfasettings.md#app-passwords)

Instale primeiro o Módulo, utilizando:

   ```PowerShell
   Install-Module MSOnline
   ```

> [!TIP]
> Não se esqueça de ligar primeiro usando **o Connect-MsolService**

   ```PowerShell
   Connect-MsolService
   ```

Este script de exemplo PowerShell permite mFA para um utilizador individual:

   ```PowerShell
   Import-Module MSOnline
   Connect-MsolService
   $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
   $st.RelyingParty = "*"
   $st.State = "Enabled"
   $sta = @($st)
   Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
   ```

Utilizar o PowerShell é uma boa opção quando precisa de ativar os utilizadores em massa. Como exemplo, o seguinte script passa através de uma lista de utilizadores e permite o MFA nas suas contas:

   ```PowerShell
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

Para desativar o MFA, utilize este script:

   ```PowerShell
   Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationRequirements @()
   ```

que também pode ser encurtado para:

   ```PowerShell
   Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()
   ```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Converter utilizadores de MFA por utilizador para MFA baseado em Acesso Condicional

O seguinte PowerShell pode ajudá-lo a efetuar a conversão para A autenticação multi-factor azure baseada em acesso condicional.

Execute esta PowerShell numa janela ISE ou guarde como . Arquivo PS1 para executar localmente.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Recentemente mudamos o comportamento e o script PowerShell acima em conformidade. Anteriormente, o guião salvou os métodos de MFA, desativou o MFA e restaurou os métodos. Isto já não é necessário agora que o comportamento padrão para desativar não limpa os métodos.
>
> Se o MFA for reativado num objeto de utilizador que já tenha dados de registo, como telefone ou e-mail, os administradores precisam de ter esse utilizador a reregistar o MFA através do portal Azure ou da PowerShell. Se o utilizador não se reregistar, o seu estado de MFA não transita de *Enabled* para *Enforced* na UI de gestão de MFA.

## <a name="next-steps"></a>Passos seguintes

* Porque é que um utilizador foi solicitado ou não para executar MFA? Consulte o relatório de inscrição da [AD Azure nos relatórios do documento de autenticação multifactor Azure](howto-mfa-reporting.md#azure-ad-sign-ins-report).
* Para configurar configurações adicionais como IPs fidedignos, mensagens de voz personalizadas e alertas de fraude, consulte as definições de [autenticação multi-factor do Azure Azure](howto-mfa-mfasettings.md)
* Informações sobre a gestão das definições de utilizador para autenticação multi-factor azure podem ser encontradas no artigo Gerir as definições do [utilizador com autenticação de multi-factor azure na nuvem](howto-mfa-userdevicesettings.md)
