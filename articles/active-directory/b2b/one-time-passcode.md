---
title: Autenticação de senha única para usuários convidados B2B – Azure AD
description: Como usar o email de senha de uso único para autenticar usuários de convidados B2B sem a necessidade de um conta Microsoft.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6d897bb983eb06baa4f1573f1f875eea8bb8afc
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272312"
---
# <a name="email-one-time-passcode-authentication-preview"></a>Autenticação de senha de uso único de email (versão prévia)

|     |
| --- |
| O email de senha de uso único é um recurso de visualização pública do Azure Active Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Este artigo descreve como habilitar a autenticação de senha de uso único de email para usuários de convidado B2B. O recurso de senha de email único autentica os usuários convidados B2B quando eles não podem ser autenticados por outros meios como o Azure AD, um conta Microsoft (MSA) ou Google Federation. Com a autenticação de senha de uso único, não há necessidade de criar um conta Microsoft. Quando o usuário convidado resgata um convite ou acessa um recurso compartilhado, ele pode solicitar um código temporário, que é enviado para seu endereço de email. Em seguida, eles inserem esse código para continuar a entrar.

Este recurso está disponível para visualização (consulte [optando pela versão prévia](#opting-in-to-the-preview) abaixo). Após a visualização, esse recurso será ativado por padrão para todos os locatários.

> [!NOTE]
> Usuários de senha de uso único devem entrar usando um link que inclui o contexto do locatário (por exemplo, `https://myapps.microsoft.com/?tenantid=<tenant id>` ou `https://portal.azure.com/<tenant id>`, ou no caso de um domínio verificado, `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). Links diretos para aplicativos e recursos também funcionam contanto que incluam o contexto do locatário. Atualmente, os usuários convidados não podem entrar usando pontos de extremidade que não têm nenhum contexto de locatário. Por exemplo, usar `https://myapps.microsoft.com`, `https://portal.azure.com`ou o ponto de extremidade comum das equipes resultará em um erro. 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Experiência do usuário para usuários convidados de senha de uso único
Com a autenticação de senha de uso único, o usuário convidado pode resgatar seu convite clicando em um link direto ou usando o email de convite. Em ambos os casos, uma mensagem no navegador indica que um código será enviado para o endereço de email do usuário convidado. O usuário convidado seleciona **enviar código**:
 
   ![Captura de tela mostrando o botão enviar código](media/one-time-passcode/otp-send-code.png)
 
Uma senha é enviada para o endereço de email do usuário. O usuário recupera a senha do email e a insere na janela do navegador:
 
   ![Captura de tela mostrando a página inserir código](media/one-time-passcode/otp-enter-code.png)
 
O usuário convidado agora é autenticado e pode ver o recurso compartilhado ou continuar a entrar. 

> [!NOTE]
> Senhas de uso único são válidas por 30 minutos. Após 30 minutos, essa senha de uso único específica não é mais válida e o usuário deve solicitar uma nova. As sessões de usuário expiram após 24 horas. Após esse período, o usuário convidado recebe uma nova senha quando acessa o recurso. A expiração da sessão fornece segurança adicional, especialmente quando um usuário convidado deixa sua empresa ou não precisa mais de acesso.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Quando um usuário convidado recebe uma senha de uso único?

Quando um usuário convidado resgatar um convite ou usar um link para um recurso que foi compartilhado com eles, ele receberá uma senha de uso único se:
- Eles não têm uma conta do Azure AD 
- Eles não têm um conta Microsoft 
- O locatário que está convidando não configurou o Google Federation para @gmail.com e @googlemail.com usuários 

No momento do convite, não há nenhuma indicação de que o usuário que você está convidando usará a autenticação de senha única. Mas quando o usuário convidado entrar, a autenticação de senha de uso único será o método de fallback se nenhum outro método de autenticação puder ser usado. 

Você pode exibir os usuários convidados que se autenticam com senhas de uso único no portal do Azure acessando **Azure Active Directory** > **relações organizacionais** > **usuários de outras organizações**.

![Captura de tela mostrando um usuário de senha de uso único com o valor de origem de OTP](media/one-time-passcode/otp-users.png)

> [!NOTE]
> Quando um usuário resgatar uma senha de uso único e posteriormente obtiver uma conta do MSA, do Azure AD ou de outra conta federada, ele continuará a ser autenticado usando uma senha de uso único. Se você quiser atualizar seu método de autenticação, poderá excluir sua conta de usuário convidado e convidá-la novamente.

### <a name="example"></a>Exemplo
O alexdoe@gmail.com de usuário convidado é convidado para a Fabrikam, que não tem a configuração do Google Federation configurada. Alex não tem um conta Microsoft. Eles receberão uma senha de uso único para autenticação.

## <a name="opting-in-to-the-preview"></a>Optando pela versão prévia 
Pode levar alguns minutos para que a ação de aceitação entre em vigor. Depois disso, somente os usuários recentemente convidados que atendem às condições acima usarão a autenticação de senha única. Os usuários convidados que anteriormente resgataram um convite continuarão a usar o mesmo método de autenticação.

### <a name="to-opt-in-using-the-azure-ad-portal"></a>Para aceitar o uso do portal do AD do Azure
1.  Entre no [portal do Azure](https://portal.azure.com/) como um administrador global do Azure AD.
2.  No painel de navegação, selecione **Azure Active Directory**.
3.  Em **gerenciar**, selecione **relações organizacionais**.
4.  Selecione **definições**.
5.  Em **habilitar o email de senha de uso único para convidados (versão prévia)** , selecione **Sim**.
 
### <a name="to-opt-in-using-powershell"></a>Para aceitar o uso do PowerShell

Primeiro, você precisará instalar a versão mais recente do módulo do Azure AD PowerShell para Graph (AzureADPreview). Em seguida, você determinará se as políticas B2B já existem e executará os comandos apropriados.

#### <a name="prerequisite-install-the-latest-azureadpreview-module"></a>Pré-requisito: instalar o módulo AzureADPreview mais recente
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

#### <a name="check-for-existing-policies-and-opt-in"></a>Verificar se há políticas existentes e aceitar

Em seguida, verifique se existe uma B2BManagementPolicy no momento executando o seguinte:

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- Se a saída for falsa, a política não existirá no momento. Crie um novo B2BManagementPolicy e aceite a visualização executando o seguinte:

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- Se a saída for verdadeira, a política B2BManagementPolicy existirá no momento. Para atualizar a política e aceitar a visualização, execute o seguinte:
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>Recusando a visualização depois de se recusar
Pode levar alguns minutos para que a ação de aceitação entre em vigor. Se você desativar a visualização, todos os usuários convidados que tiverem resgatado uma senha de uso único não poderão entrar. Você pode excluir o usuário convidado e convidar novamente o usuário para habilitá-los a entrar novamente usando outro método de autenticação.

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>Para desativar a visualização usando o portal do AD do Azure
1.  Entre no [portal do Azure](https://portal.azure.com/) como um administrador global do Azure AD.
2.  No painel de navegação, selecione **Azure Active Directory**.
3.  Em **gerenciar**, selecione **relações organizacionais**.
4.  Selecione **definições**.
5.  Em **habilitar o email de senha de uso único para convidados (versão prévia)** , selecione **não**.

### <a name="to-turn-off-the-preview-using-powershell"></a>Para desativar a visualização usando o PowerShell
Instale o módulo AzureADPreview mais recente se você ainda não o tiver (consulte [pré-requisito: instalar o módulo AzureADPreview mais recente](#prerequisite-install-the-latest-azureadpreview-module) acima). Em seguida, verifique se a política de visualização de senha de uso único existe no momento executando o seguinte:

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

Se a saída for verdadeira, cancele a visualização executando o seguinte:

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

