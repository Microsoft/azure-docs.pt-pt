---
title: Início rápido adicionar um utilizador convidado com o PowerShell - Azure Active Directory | Documentos da Microsoft
description: Neste guia de início rápido, irá aprender a utilizar o PowerShell para enviar um convite para um utilizador externo de colaboração B2B do Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: mimart
author: msmimart
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b9274652b7164a4aef71499912cb8b38ace29ff
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57854454"
---
# <a name="quickstart-add-a-guest-user-with-powershell"></a>Início rápido: Adicionar um utilizador convidado com o PowerShell

Existem várias formas de convidar parceiros externos para as aplicações e serviços com a colaboração B2B do Azure Active Directory. No guia de início rápido anterior, viu como adicionar utilizadores convidados diretamente no portal de administrador do Azure Active Directory. Pode também utilizar o PowerShell para adicionar utilizadores convidados, um de cada vez ou em lote. Neste guia de início rápido, irá utilizar o comando New-AzureADMSInvitation para adicionar um utilizador convidado ao seu inquilino do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

### <a name="install-the-latest-azureadpreview-module"></a>Instalar o módulo do AzureADPreview mais recente
Certifique-se de que instala a versão mais recente do módulo do Azure AD PowerShell para Graph (AzureADPreview). 

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

### <a name="get-a-test-email-account"></a>Obter uma conta de e-mail de teste

Precisa de uma conta de e-mail de teste para a qual possa enviar os convites. A conta tem de ser externa à sua organização. Pode utilizar qualquer tipo de conta, incluindo uma conta social como o endereço do gmail.com ou outlook.com.

## <a name="sign-in-to-your-tenant"></a>Iniciar sessão no seu inquilino

Execute o seguinte comando para ligar ao domínio do inquilino:

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
Por exemplo, `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

Quando lhe for pedido, introduza as suas credenciais.

## <a name="send-an-invitation"></a>Enviar um convite

1. Para enviar um convite para a sua conta de e-mail de teste, execute o seguinte comando do PowerShell (substitua **"Sanda"** e **sanda\@fabrikam.com** com seu teste e-mail conta nome e endereço de e-mail): 

   ```powershell
   New-AzureADMSInvitation -InvitedUserDisplayName "Sanda" -InvitedUserEmailAddress sanda@fabrikam.com -InviteRedirectURL https://myapps.azure.com -SendInvitationMessage $true
   ```
2. O comando envia um convite para o endereço de e-mail especificado. Verifique o resultado, que deve ter um aspeto semelhante ao seguinte:

   ![Resultado do PowerShell a mostrar a aceitação do utilizador pendente](media/quickstart-invite-powershell/powershell-azureadmsinvitation-result.png)

## <a name="verify-the-user-exists-in-the-directory"></a>Certifique-se de que o utilizador existe no diretório

1. Para verificar se o utilizador convidado foi adicionado ao Azure AD, execute o seguinte comando:
 
   ```powershell
   Get-AzureADUser -Filter "UserType eq 'Guest'"
   ```
3. Verificar a saída para se certificar de que o utilizador que convidou está listado, com um nome principal de utilizador (UPN), no formato *emailaddress*EXT de # #\@*domínio*. Por exemplo, *sanda_fabrikam.com#EXT#\@contoso.onmicrosoft.com*, em que contoso.onmicrosoft.com é a organização a partir do qual enviados os convites.

   ![Resultado do PowerShell que mostra o utilizador convidado adicionado](media/quickstart-invite-powershell/powershell-guest-user-added.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar da conta de utilizador de teste no diretório, pode eliminá-la. Execute o seguinte comando para eliminar uma conta de utilizador:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
Por exemplo: `Remove-AzureADUser -ObjectId "sanda_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>Passos Seguintes
Neste guia de início rápido, convidou e adicionou um único utilizador convidado para o seu diretório com o PowerShell. Em seguida, aprenda a convidar utilizadores em lote com o PowerShell.

> [!div class="nextstepaction"]
> [Tutorial: Em massa convidar utilizadores de colaboração B2B do Azure AD](tutorial-bulk-invite.md)
