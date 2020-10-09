---
title: Tutorial para utilizadores de colaboração B2B convidativos a granel - Azure Ative Directory / Microsoft Docs
description: Neste tutorial, você aprende a usar PowerShell e um ficheiro CSV para enviar convites em massa para utilizadores convidados de colaboração Azure AD B2B externos.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 02/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89e24d9ff76184c36aee5c14f15f9713b30f6f1d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87905957"
---
# <a name="tutorial-use-powershell-to-bulk-invite-azure-ad-b2b-collaboration-users"></a>Tutorial: Use PowerShell para convidar utilizadores de colaboração Azure AD B2B

Se utilizar a colaboração B2B do Azure Active Directory (Azure AD) para trabalhar com parceiros externos, pode convidar múltiplos utilizadores convidados para a sua organização ao mesmo tempo. Neste tutorial, saberá como utilizar o PowerShell para enviar convites em massa para utilizadores externos. Deve fazer o seguinte:

> [!div class="checklist"]
> * Preparar um ficheiro de valores separados por vírgulas (.csv) com as informações dos utilizadores
> * Executar um script do PowerShell para enviar convites
> * Verificar se os utilizadores foram adicionados ao diretório

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

### <a name="install-the-latest-azureadpreview-module"></a>Instalar o módulo do AzureADPreview mais recente

Certifique-se de que instala a versão mais recente do módulo do Azure AD PowerShell para Graph (AzureADPreview). 

Primeiro, verifique quais foram os módulos que instalou. Abra o Windows PowerShell como um utilizador com privilégios elevados (Executar como administrador) e execute o seguinte comando:

```powershell
Get-Module -ListAvailable AzureAD*
```

Com base no resultado, siga um dos seguintes passos:

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

### <a name="get-test-email-accounts"></a>Obter contas de e-mail de teste

Precisa de duas ou mais contas de e-mail de teste para as quais possa enviar os convites. As contas têm de ser externas à sua organização. Pode utilizar qualquer tipo de conta, incluindo contas sociais como os endereços do gmail.com ou outlook.com.

## <a name="prepare-the-csv-file"></a>Preparar o ficheiro CSV

No Microsoft Excel, crie um ficheiro CSV com a lista dos nomes e endereços de e-mail dos utilizadores convidados. Certifique-se de que inclui os cabeçalhos de coluna **Name** (Nome) e **InvitedUserEmailAddress**.

Por exemplo, crie uma folha de cálculo no seguinte formato:

![Resultado do PowerShell a mostrar a aceitação do utilizador pendente](media/tutorial-bulk-invite/AddUsersExcel.png)

Guarde o ficheiro como **C:\BulkInvite\Invitations.csv**. 

Se não tiver o Excel, pode criar um ficheiro CSV em qualquer editor de texto, como o Bloco de Notas. Separe cada valor por uma vírgula e cada linha por uma nova linha. 

## <a name="sign-in-to-your-tenant"></a>Iniciar sessão no seu inquilino

Execute o seguinte comando para ligar ao domínio do inquilino:

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```

Por exemplo, `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

Quando lhe for pedido, introduza as suas credenciais.

## <a name="send-bulk-invitations"></a>Enviar convites em massa

Para enviar convites, execute o seguinte script do PowerShell (em que **c:\bulkinvite\invitations.csv** é o caminho para o ficheiro CSV):

```powershell
$invitations = import-csv c:\bulkinvite\invitations.csv

$messageInfo = New-Object Microsoft.Open.MSGraph.Model.InvitedUserMessageInfo

$messageInfo.customizedMessageBody = "Hello. You are invited to the Contoso organization."

foreach ($email in $invitations)
   {New-AzureADMSInvitation `
      -InvitedUserEmailAddress $email.InvitedUserEmailAddress `
      -InvitedUserDisplayName $email.Name `
      -InviteRedirectUrl https://myapps.microsoft.com `
      -InvitedUserMessageInfo $messageInfo `
      -SendInvitationMessage $true
   }
```

O script envia um convite para os endereços de e-mail no ficheiro Invitations.csv. Deverá ver um resultado semelhante ao seguinte para cada utilizador:

![Resultado do PowerShell a mostrar a aceitação do utilizador pendente](media/tutorial-bulk-invite/B2BBulkImport.png)

## <a name="verify-users-exist-in-the-directory"></a>Verificar se existem utilizadores no diretório

Para verificar se os utilizadores convidados foram adicionados ao Azure AD, execute o seguinte comando:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

Deverá ver os utilizadores que convidou listados, com um nome principal do utilizador (UPN) no formato *emailaddress*#EXT# \@ *domínio*. Por exemplo, *lstokes_fabrikam.com#EXT# \@ contoso.onmicrosoft.com*, onde contoso.onmicrosoft.com é a organização a partir da qual enviou os convites.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não forem necessárias, pode eliminar as contas de utilizador de teste no diretório. Execute o seguinte comando para eliminar uma conta de utilizador:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Por exemplo: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, enviou convites em massa para utilizadores convidados fora da sua organização. Em seguida, saiba como o processo de resgate do convite funciona.

> [!div class="nextstepaction"]
> [Saiba mais sobre o processo de resgate do convite de colaboração B2B do Azure AD](redemption-experience.md)
