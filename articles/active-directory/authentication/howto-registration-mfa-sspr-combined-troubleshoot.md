---
title: Resolver problemas de registo combinado para SSPR do Azure AD e MFA (pré-visualização) - Azure Active Directory
description: Resolver problemas do Azure AD multi-factor Authentication e o registo de combinada de reposição de palavra-passe self-service (pré-visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: d926f7312b62e788289939dfd81c236a33503b43
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370470"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>Resolução de problemas combinados o registo de informações de segurança (pré-visualização)

As informações fornecidas neste artigo podem orientar os administradores que esteja a resolver problemas com a experiência de registo combinado enviada pelos seus utilizadores finais.

|     |
| --- |
| Registo de informações de segurança combinado para a reposição de palavra-passe self-service do Azure multi-factor Authentication e o Azure AD é uma funcionalidade de pré-visualização pública do Azure Active Directory. Para obter mais informações sobre pré-visualizações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="audit-logs"></a>Registos de auditoria

Os eventos registados para o registo combinado estão sob a categoria "Métodos de autenticação" no Azure AD registos de auditoria.

![Eventos de registo que mostra de interface de registos de auditoria do Azure AD](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

A seguir listam-se todos os eventos de auditoria gerados pelo registo combinado:

| Atividade | Estado | Razão | Descrição |
| --- | --- | --- | --- |
| Utilizador registado todas as informações de segurança necessárias | Êxito | Utilizador registado todas as informações de segurança necessário. | Este evento ocorre quando um utilizador foi concluída com êxito o registo.|
| Utilizador registado todas as informações de segurança necessárias | Falha | O utilizador cancelou o registo de informações de segurança. | Este evento ocorre quando um utilizador cancelar o Registro do modo de interrupção.|
| Informações de segurança do utilizador registado | Êxito | Utilizador registado "método". | Este evento ocorre quando um usuário registra um método individual. "Método" pode ser a aplicação de autenticação, telefone, E-Mail, perguntas de segurança, palavra-passe de aplicação, telefone alternativo, etc.| 
| Informações de segurança do utilizador revisto | Êxito | Utilizador analisado com êxito as informações de segurança. | Este evento ocorre quando um usuário clicar em "Se parece bom" na página de revisão de informações de segurança.|
| Informações de segurança do utilizador revisto | Falha | Utilizador não foi possível analisar informações de segurança. | Este evento ocorre quando um usuário clicar em "Parece bem" sobre as informações de segurança reveja a página, mas algo falhar no back-end.|
| Informações de segurança do utilizador eliminado | Êxito | O utilizador eliminado "método". | Este evento ocorre quando um usuário exclui um método individual. "Método" pode ser a aplicação de autenticação, telefone, E-Mail, perguntas de segurança, palavra-passe de aplicação, telefone alternativo, etc.|
| Informações de segurança do utilizador eliminado | Falha | Não foi possível eliminar o "método" o utilizador. | Este evento ocorre quando um usuário tentar excluir um método, mas falhar por algum motivo. "Método" pode ser a aplicação de autenticação, telefone, E-Mail, perguntas de segurança, palavra-passe de aplicação, telefone alternativo, etc.|
| Informações de segurança de predefinição do utilizador foi alterado | Êxito | Utilizador alterou as informações de segurança predefinido para "método". | Este evento ocorre quando um utilizador altera o método de predefinição. "Método" pode ser a notificação de aplicação de autenticador, código da minha aplicação authenticator ou o token, chamada + X XXXXXXXXXX, texto de um código para + X XXXXXXXXX, etc.|
| Informações de segurança de predefinição do utilizador foi alterado | Falha | Falha ao utilizador alterar as informações de segurança predefinido para "método". | Este evento ocorre quando um usuário tentar alterar o respetivo método padrão, mas falhar por algum motivo. "Método" pode ser a notificação de aplicação de autenticador, um código da minha aplicação authenticator ou o token, chamada + X XXXXXXXXXX, texto um código para + X XXXXXXXXX, etc.|

## <a name="troubleshooting-interrupt-mode"></a>Modo de interrupção de resolução de problemas

| Sintoma | Passos de resolução de problemas |
| --- | --- |
| Não estou a ver os métodos que eu esperava ver. | 1. Verifique se o utilizador tem uma função de administrador do Azure AD. Se Sim, reveja as diferenças de política do administrador SSPR. <br> 2. Determine se o utilizador está a ser interrompido devido a imposição de registo MFA ou a imposição de registo SSPR. Reveja o fluxograma em modos de registo combinado para determinar quais métodos devem ser mostrados. <br> 3. Determine como recentemente a política de MFA ou SSPR foi alterada. Se a alteração recente, pode demorar algum tempo para a política atualizada propagar.|

## <a name="troubleshooting-manage-mode"></a>Resolução de problemas a gerenciar o modo

| Sintoma | Passos de resolução de problemas |
| --- | --- |
| Não tenho a opção de adicionar um método específico. | 1. Determine se o método está ativado para a MFA ou para SSPR. <br> 2. Se o método estiver ativado, volte a guardar as políticas e aguarde 1-2 horas antes de testar novamente. <br> 3. Se o método estiver ativado, certifique-se de que o utilizador não tiver configurado o número máximo desse método que eles têm permissão para configurar.|

## <a name="disable-combined-registration"></a>Desativar o registo combinado

Quando um utilizador regista o número de telefone e/ou a aplicação móvel no novo combinado a experiência, nossa carimbos de data / serviço um conjunto de sinalizadores (StrongAuthenticationMethods) para esses métodos em que o utilizador. Esta funcionalidade permite ao utilizador executar o Azure multi-factor Authentication (MFA) com os métodos sempre que a MFA é necessária.

Os métodos que os utilizadores registem por meio da nova experiência de tem a propriedade de StrongAuthenticationMethods definida. Se um administrador ativa a pré-visualização, os utilizadores registem através da experiência nova e, em seguida, o administrador desative a pré-visualização, os utilizadores podem inconscientemente ser registados para MFA também.

Se um utilizador que foi concluída combinado registo navega para a página registo de reposição (SSPR) da palavra-passe self-service atual, em [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup), eles serão solicitados a executar a MFA antes de poderem aceder nessa página. Este passo é um comportamento esperado do ponto de vista técnico, mas para os utilizadores que foram anteriormente registados para SSPR apenas, este passo é um novo comportamento. Embora essa etapa adicional melhorar a postura de segurança do usuário, fornecendo um nível adicional de segurança, os administradores querem podem revertê-lo aos utilizadores, para que eles já não são capazes de realização de MFA.  

### <a name="how-to-roll-back-users"></a>Como reverte os utilizadores

Se, como um administrador pretende repor as definições da MFA do utilizador, criamos um script do PowerShell que limpará a propriedade StrongAuthenticationMethods para aplicação móvel de um utilizador e/ou número de telefone. Executar este script para os seus utilizadores significa que o utilizador tem voltar a registar para MFA se for necessário. Recomendamos que teste a reversão com um ou dois utilizadores antes de reverter a todos os utilizadores afetados.

Os passos que se seguem irão ajudá-lo a reverter um utilizador ou grupo de utilizadores:

#### <a name="prerequisites"></a>Pré-requisitos

1. Terá de instalar os módulos do Azure AD PowerShell apropriados. Numa janela do PowerShell, execute estes comandos para instalar os módulos:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Guarde a lista de ID/IDs de objeto de utilizador afectado no seu computador como um ficheiro de texto com o ID de um por linha. Tome nota da localização do ficheiro.
1. Guarde o seguinte script no seu computador e tome nota da localização do script:

```powershell
<# 
//********************************************************
//*                                                      *
//*   Copyright (C) Microsoft. All rights reserved.      *
//*                                                      *
//********************************************************
#>

param($path)

# Define Remediation Fn
function RemediateUser {

    param  
    (
        $ObjectId
    )

    $user = Get-MsolUser -ObjectId $ObjectId

    Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

    $hasMfaRelyingParty = $false
    foreach($p in $user.StrongAuthenticationRequirements)
    {
        if ($p.RelyingParty -eq "*")
        {
            $hasMfaRelyingParty = $true
            Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
        }
    }

    if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
    {
        Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
        Write-Host "Rolling back user ..." -ForegroundColor Yellow
        Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
        Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
    }
    else
    {
        Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
    }

    Write-Host ""
    Start-Sleep -Milliseconds 750
}

# Connect
Import-Module MSOnline
Connect-MsolService

foreach($line in Get-Content $path)
{
    RemediateUser -ObjectId $line
}
```

#### <a name="rollback"></a>Reversão

Numa janela do PowerShell, execute o seguinte comando depois de atualizar as localizações realçadas. Introduza as credenciais de administrador global quando lhe for pedido. O script irá transmitir o resultado de cada operação de atualização de utilizador.

`<script location> -path <user file location>`

### <a name="disable-preview-experience"></a>Desativar a experiência de pré-visualização

Para desativar a experiência de pré-visualização para os seus utilizadores, conclua os seguintes passos:

1. Inicie sessão no portal do Azure como um administrador global ou administrador de utilizadores.
2. Navegue até **do Azure Active Directory**, **definições do utilizador**, **gerir as definições para as funcionalidades de pré-visualização do painel de acesso**.
3. Sob **os utilizadores podem utilizar funcionalidades de pré-visualização para registar e gerir informações de segurança**, defina o seletor para **None** e clique em **guardar**.

Os utilizadores já não serão pedidos para se registar com a experiência de pré-visualização.

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre a pré-visualização pública do combinado de registo para reposição de palavra-passe self-service e o Azure multi-factor Authentication](concept-registration-mfa-sspr-combined.md)
