---
title: Autenticação de código de acesso único para utilizadores convidados B2B - Azure AD
description: Como utilizar a senha de acesso de email para autenticar utilizadores convidados B2B sem a necessidade de uma conta Microsoft.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 714e4484c71b995bee186a2d94dc45c7ff82c50d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87909207"
---
# <a name="email-one-time-passcode-authentication-preview"></a>E-mail autenticação de código de acesso único (pré-visualização)

> [!NOTE]
> O código de acesso único por e-mail é uma funcionalidade de pré-visualização pública do Azure Ative Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo descreve como ativar a autenticação de código de acesso único para utilizadores de utilizadores convidados B2B. A funcionalidade de código de acesso de email autentica os utilizadores convidados B2B quando não podem ser autenticados através de outros meios, como o Azure AD, uma conta Microsoft (MSA) ou a federação do Google. Com a autenticação de código de acesso único, não há necessidade de criar uma conta Microsoft. Quando o utilizador convidado resgatar um convite ou aceder a um recurso partilhado, pode solicitar um código temporário, que é enviado para o seu endereço de e-mail. Depois introduzem este código para continuarem a iniciar sessão.

Esta funcionalidade está atualmente disponível para pré-visualização (ver [Opting in to the preview](#opting-in-to-the-preview) below). Após a pré-visualização, esta funcionalidade será ligada por padrão para todos os inquilinos.

> [!NOTE]
> Os utilizadores de código de acesso único devem inscrever-se através de um link que inclua o contexto do inquilino (por `https://myapps.microsoft.com/?tenantid=<tenant id>` `https://portal.azure.com/<tenant id>` exemplo, ou, no caso de um domínio verificado, `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com` ). As ligações diretas a aplicações e recursos também funcionam desde que incluam o contexto do inquilino. Os utilizadores convidados não conseguem assinar o uso de pontos finais que não têm contexto de inquilino. Por exemplo, a utilização `https://myapps.microsoft.com` de , ou o ponto final comum das Equipas `https://portal.azure.com` resultará num erro. 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Experiência do utilizador para utilizadores convidados de código de acesso único
Com a autenticação de código de acesso único, o utilizador convidado pode resgatar o seu convite clicando num link direto ou utilizando o e-mail de convite. Em qualquer dos casos, uma mensagem no navegador indica que um código será enviado para o endereço de e-mail do utilizador convidado. O utilizador convidado seleciona **Enviar código:**
 
   ![Screenshot mostrando o botão de código Enviar](media/one-time-passcode/otp-send-code.png)
 
Uma senha é enviada para o endereço de e-mail do utilizador. O utilizador recupera a senha do e-mail e introduz-a na janela do navegador:
 
   ![Screenshot mostrando a página de código enterr](media/one-time-passcode/otp-enter-code.png)
 
O utilizador convidado está agora autenticado e pode ver o recurso partilhado ou continuar a iniciar sessão. 

> [!NOTE]
> As senhas únicas são válidas por 30 minutos. Após 30 minutos, essa senha única específica já não é válida, e o utilizador deve solicitar uma nova. As sessões do utilizador expiram após 24 horas. Após esse período, o utilizador convidado recebe uma nova senha quando acede ao recurso. A expiração da sessão proporciona segurança adicional, especialmente quando um utilizador convidado sai da sua empresa ou já não precisa de acesso.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Quando é que um utilizador convidado obtém uma senha única?

Quando um utilizador convidado resgatar um convite ou utilizar um link para um recurso que tenha sido partilhado com eles, receberá uma senha única se:
- Eles não têm uma conta AD Azure 
- Não têm uma conta microsoft 
- O inquilino convidado não criou a federação do Google para @gmail.com e @googlemail.com utilizadores 

No momento do convite, não há indicação de que o utilizador que está a convidar utilize a autenticação de código de acesso único. Mas quando o utilizador convidado assinar, a autenticação de código de acesso único será o método de recuo se não forem utilizados outros métodos de autenticação. 

Pode ver os utilizadores convidados que autenticam com senhas únicas no portal Azure, indo para Utilizadores **do Diretório Ativo Azure**  >  **Users**.

![Screenshot mostrando um utilizador de código de acesso único com valor de Origem de OTP](media/one-time-passcode/otp-users.png)

> [!NOTE]
> Quando um utilizador resgatar uma senha única e mais tarde obtiver uma conta MSA, AZure AD ou outra conta federada, continuará a ser autenticada através de uma senha única. Se pretender atualizar o seu método de autenticação, pode eliminar a conta de utilizador do utilizador e convidá-la novamente.

### <a name="example"></a>Exemplo
O utilizador convidado alexdoe@gmail.com é convidado para a Fabrikam, que não tem a federação do Google configurada. Alex não tem uma conta microsoft. Receberão uma senha única para autenticação.

## <a name="opting-in-to-the-preview"></a>Optando pela pré-visualização 
Pode levar alguns minutos para que a ação de opt-in entre em vigor. Depois disso, apenas os utilizadores recém-convidados que satisfaçam as condições acima utilizarão a autenticação de código de acesso único. Os utilizadores convidados que anteriormente resgataram um convite continuarão a utilizar o mesmo método de autenticação.

### <a name="to-opt-in-using-the-azure-ad-portal"></a>Optar pela utilização do portal AD Azure
1.  Inscreva-se no [portal Azure](https://portal.azure.com/) como administrador global da Azure AD.
2.  No painel de navegação, selecione **Azure Ative Directory**.
3.  Selecione **identidades externas**  >  **Definições de colaboração externa .**
5.  Em **Enable Email One-Time Código de Acesso para os hóspedes (Pré-visualização)**, selecione **Sim**.
 
### <a name="to-opt-in-using-powershell"></a>Optar pela utilização do PowerShell

Em primeiro lugar, terá de instalar a versão mais recente do módulo Azure AD PowerShell para Graph (AzureADPreview). Então determinará se as políticas B2B já existem e executará os comandos apropriados.

#### <a name="prerequisite-install-the-latest-azureadpreview-module"></a>Pré-requisito: Instale o mais recente módulo AzureADPreview
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

#### <a name="check-for-existing-policies-and-opt-in"></a>Verifique as políticas existentes e opte por

Em seguida, verifique se existe atualmente uma B2BManagementPolicy executando o seguinte:

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- Se a saída é falsa, a apólice não existe atualmente. Crie uma nova B2BManagementPolicy e opte pela pré-visualização executando o seguinte:

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- Se a saída for verdadeira, a política de política de política de política de B2BManagement atualmente existe. Para atualizar a política e optar pela pré-visualização, execute o seguinte:
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>Optando por não fazer a pré-visualização depois de optar por
Pode levar alguns minutos para que a ação de opt-out entre em vigor. Se desativar a pré-visualização, qualquer utilizador convidado que tenha resgatado uma senha única não poderá iniciar sação. Pode eliminar o utilizador convidado e voltar a convidar o utilizador para que volte a iniciar sação utilizando outro método de autenticação.

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>Para desativar a pré-visualização utilizando o portal AD AZure
1.  Inscreva-se no [portal Azure](https://portal.azure.com/) como administrador global da Azure AD.
2.  No painel de navegação, selecione **Azure Ative Directory**.
3.  Selecione **identidades externas**  >  **Definições de colaboração externa .**
5.  Em **Enable Email One-Time Código de Acesso para os hóspedes (Pré-visualização)**, selecione **Nº**.

### <a name="to-turn-off-the-preview-using-powershell"></a>Para desativar a pré-visualização usando o PowerShell
Instale o mais recente módulo AzureADPreview se ainda não o tiver (ver [Pré-requisito: Instale o mais recente módulo AzureADPreview](#prerequisite-install-the-latest-azureadpreview-module) acima). Em seguida, verifique se a política de pré-visualização de código de acesso único existe atualmente executando o seguinte:

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

Se a saída for verdadeira, opte por não fazer a pré-visualização executando o seguinte:

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

