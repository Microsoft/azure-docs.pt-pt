---
title: Autenticação de código de acesso único para utilizadores de convidados B2B - Azure Active Directory | Documentos da Microsoft
description: Como utilizar o código de acesso único E-Mail para autenticar utilizadores B2B sem a necessidade de uma conta Microsoft.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 1/25/2019
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f5234674bc41e1f5bfa260dfc32042a3345d9f7
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294830"
---
# <a name="email-one-time-passcode-authentication-preview"></a>Autenticação de código de acesso único por e-mail (pré-visualização)

|     |
| --- |
| Código de acesso único e-mail é uma funcionalidade de pré-visualização pública do Azure Active Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

A funcionalidade de código de acesso único E-Mail autentica utilizadores convidados B2B, quando não pode ser autenticadas através de outros meios, como o Azure AD, uma conta Microsoft (MSA) ou o Federação de Google. Com a autenticação de código de acesso único, não é necessário para criar uma conta Microsoft. Quando o utilizador convidado redeems um convite ou acede a um recurso partilhado, eles poderão solicitar um código temporário, que é enviado para o respetivo endereço de e-mail. Em seguida, eles introduza este código para continuar a iniciar sessão.

Esta funcionalidade está atualmente disponível para pré-visualização (consulte [aceitar para a pré-visualização](#opting-in-to-the-preview) abaixo). Depois de pré-visualização, esta funcionalidade será ativada por predefinição para todos os inquilinos.

> [!NOTE]
> Os utilizadores de código de acesso único tem de iniciar sessão através de uma ligação que inclui o contexto do inquilino (por exemplo, `https://myapps.microsoft.com/?tenantid=<tenant id>` ou `https://portal.azure.com/<tenant id>`, ou no caso de um domínio verificado, `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). Ligações diretas para aplicativos e recursos também funcionam, desde que eles incluem o contexto do inquilino. Os utilizadores convidados são não foi possível iniciar sessão com pontos finais que não têm nenhum contexto de inquilino. Por exemplo, utilizando `https://myapps.microsoft.com`, `https://portal.azure.com`, ou o ponto de extremidade comum as Equipes resultará num erro. 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Experiência de utilizador para os utilizadores convidados de código de acesso único
Com a autenticação de código de acesso único, o utilizador convidado pode resgatar o convite ao clicar numa ligação direta ou ao utilizar o e-mail de convite. Em ambos os casos, uma mensagem no navegador indica que será enviado um código para o endereço de e-mail do utilizador convidado. O utilizador convidado seleciona **enviar código**:
 
   ![Captura de ecrã que mostra o botão de código de envio](media/one-time-passcode/otp-send-code.png)
 
Um código de acesso é enviado para o endereço de e-mail do utilizador. O utilizador obtém o código de acesso de e-mail e insere-la na janela do browser:
 
   ![Captura de ecrã que mostra a página de código Enter](media/one-time-passcode/otp-enter-code.png)
 
O utilizador convidado já é autenticado e podem ver o recurso partilhado ou continuar a iniciar sessão. 

> [!NOTE]
> Códigos de acesso Monouso são válidos durante 30 minutos. Após 30 minutos, esse código de acesso único específico já não é válido e o utilizador tem de pedir um novo. Sessões de usuário expirem após 24 horas. Após esse tempo, o utilizador convidado recebe um novo código de acesso quando acedem o recursos. Expiração da sessão fornece segurança adicional, especialmente quando um utilizador convidado deixa a sua empresa ou já não precisa de acesso.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Quando um utilizador convidado obter um código de acesso único?

Quando um utilizador convidado redeems um convite ou utiliza uma ligação a um recurso que foi partilhado consigo, receberá um código de acesso único se:
- Não têm uma conta do Azure AD 
- Não têm uma conta Microsoft 
- O inquilino de convite não configurado o Federação Google para @gmail.com e @googlemail.com utilizadores 

No momento do convite, não existe nenhuma indicação de que o utilizador que está a convidar irá utilizar a autenticação de código de acesso único. Mas quando o utilizador convidado inicia sessão, autenticação de código de acesso único será o método de contingência se a outros métodos de autenticação podem ser utilizados. 

Pode ver os utilizadores convidados que autenticam com códigos de acesso Monouso no portal do Azure acedendo a **do Azure Active Directory** > **relações organizacionais**  >   **Os utilizadores de outras organizações**.

![Captura de ecrã que mostra um utilizador de código de acesso único com o valor de origem de OTP](media/one-time-passcode/otp-users.png)

> [!NOTE]
> Quando um usuário redeems um código de acesso único e mais tarde obtém uma MSA, a conta do Azure AD ou a outra conta federada, este irá continuar a ser autenticadas através de um código de acesso único. Se pretender atualizar o método de autenticação, pode eliminar a conta de utilizador convidado e reinvite-los.

### <a name="example"></a>Exemplo
Utilizador convidado alexdoe@gmail.com é convidado a Fabrikam, o que não tem o Federação Google configurar. Alex não tem uma conta Microsoft. Ele vai receber um código de acesso único para autenticação.

## <a name="opting-in-to-the-preview"></a>Aceitar para a pré-visualização 
Poderá demorar alguns minutos para que a ação de participação ativa entrem em vigor. Depois disso, apenas os utilizadores convidados recentemente que cumprem as condições acima irão utilizar a autenticação de código de acesso único. Utilizadores convidados que anteriormente resgatar um convite irão continuar a utilizar o mesmo método de autenticação.

### <a name="to-opt-in-using-the-azure-ad-portal"></a>Para optar ativamente por participar com o portal do Azure AD
1.  Inicie sessão para o [portal do Azure](https://portal.azure.com/) como administrador global do Azure AD.
2.  No painel de navegação, selecione **do Azure Active Directory**.
3.  Sob **Manage**, selecione **organizacionais relações**.
4.  Selecione **definições**.
5.  Sob **ativar E-Mail uso único código de acesso para convidados (pré-visualização)**, selecione **Sim**.
 
### <a name="to-opt-in-using-powershell"></a>Para optar ativamente por participar com o PowerShell

Em primeiro lugar, terá de instalar a versão mais recente do Azure AD PowerShell para o módulo de gráfico (AzureADPreview). Em seguida, vai determinar se as políticas de B2B já existem de colunas e executam os comandos apropriados.

#### <a name="prerequisite-install-the-latest-azureadpreview-module"></a>Pré-requisito: Instalar o módulo do AzureADPreview mais recente
Primeiro, verifique quais foram os módulos que instalou. Abra o Windows PowerShell como um utilizador com privilégios elevados (Executar como administrador) e execute o seguinte comando:
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

Se o módulo do AzureADPreview for apresentado sem uma mensagem que indique que existe uma versão posterior, está pronto. Caso contrário, com base no resultado, siga um dos seguintes passos:

- Se não forem devolvidos resultados, execute o seguinte comando para instalar o módulo do AzureADPreview:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Se apenas for apresentado o módulo do AzureAD nos resultados, execute os seguintes comandos para instalar o módulo do AzureADPreview: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Se apenas for apresentado o módulo do AzureADPreview nos resultados, mas receber uma mensagem que indique a existência de uma versão mais recente, execute os seguintes comandos para atualizar o módulo: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

Poderá receber um aviso a indicar que está a instalar o módulo a partir de um repositório não fidedigno. Isto ocorre se não tiver configurado previamente o repositório PSGallery como um repositório fidedigno. Prima **Y** para instalar o módulo.

#### <a name="check-for-existing-policies-and-opt-in"></a>Verificar as políticas existentes e optar ativamente por participar no

Em seguida, certifique-se atualmente existe um B2BManagementPolicy executando o seguinte:

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- Se o resultado é False, a política não existe atualmente. Crie um novo B2BManagementPolicy e optar ativamente por participar na pré-visualização do executando o seguinte:

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- Se a saída for True, a política de B2BManagementPolicy existe atualmente. Para atualizar a política e escolher a pré-visualização, execute o seguinte:
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>Desativar a pré-visualização depois de aceitar
Pode demorar alguns minutos para que a ação de recusa entrem em vigor. Se desativar a pré-visualização, qualquer usuário de convidado que tiver resgatado um código de acesso único não será capaz de iniciar sessão. Pode eliminar o utilizador convidado e reinvite o utilizador para que eles possam iniciar sessão novamente com outro método de autenticação.

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>Para desativar a pré-visualização através do portal do Azure AD
1.  Inicie sessão para o [portal do Azure](https://portal.azure.com/) como administrador global do Azure AD.
2.  No painel de navegação, selecione **do Azure Active Directory**.
3.  Sob **Manage**, selecione **organizacionais relações**.
4.  Selecione **definições**.
5.  Sob **ativar E-Mail uso único código de acesso para convidados (pré-visualização)**, selecione **não**.

### <a name="to-turn-off-the-preview-using-powershell"></a>Para desativar a pré-visualização com o PowerShell
Instalar o módulo do AzureADPreview mais recente, se não o tiver feito (consulte [pré-requisitos: Instalar o módulo mais recente do AzureADPreview](#prerequisite-install-the-latest-azureadpreview-module) acima). Em seguida, certifique-se de que a política de pré-visualização do código de acesso único atualmente existe executando o seguinte:

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

Se a saída for True, recusar a pré-visualização ao executar o seguinte:

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

