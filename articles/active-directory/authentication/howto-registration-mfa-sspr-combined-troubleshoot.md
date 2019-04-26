---
title: Resolver problemas de registo combinado para SSPR do Azure AD e o multi-factor Authentication (pré-visualização) - Azure Active Directory
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
ms.openlocfilehash: 40918493071fe0dd694c43e2b087a2bf7eb197d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60414631"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>Resolução de problemas combinados o registo de informações de segurança (pré-visualização)

As informações neste artigo destina-se para orientar os administradores que são problemas relatados pelos utilizadores da experiência do registo combinado de resolução de problemas.

|     |
| --- |
| Registo de informações de segurança combinada para o Azure multi-factor Authentication e reposição de palavra-passe self-service do Azure Active Directory (Azure AD) é uma funcionalidade de pré-visualização pública do Azure AD. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="audit-logs"></a>Registos de auditoria

Os eventos registados para o registo combinado são na categoria de métodos de autenticação no Azure AD registos de auditoria.

![Eventos de registo que mostra de interface de registos de auditoria do Azure AD](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

A tabela seguinte apresenta uma lista de todos os eventos de auditoria gerados pelo registo combinado:

| Atividade | Estado | Razão | Descrição |
| --- | --- | --- | --- |
| Utilizador registado todas as informações de segurança necessárias | Êxito | Utilizador registado todas as informações de segurança necessário. | Este evento ocorre quando um utilizador foi concluída com êxito o registo.|
| Utilizador registado todas as informações de segurança necessárias | Falha | O utilizador cancelou o registo de informações de segurança. | Este evento ocorre quando um utilizador cancelar o Registro do modo de interrupção.|
| Informações de segurança do utilizador registado | Êxito | Utilizador registado *método*. | Este evento ocorre quando um usuário registra um método individual. *Método* pode ser Authenticator app, telefone, E-Mail, segurança perguntas, aplicação palavra-passe, telefone alternativo e assim por diante.| 
| Informações de segurança do utilizador revisto | Êxito | Utilizador analisado com êxito as informações de segurança. | Este evento ocorre quando um utilizador seleciona **parece bem** na página de revisão de informações de segurança.|
| Informações de segurança do utilizador revisto | Falha | Utilizador não foi possível analisar informações de segurança. | Este evento ocorre quando um utilizador seleciona **parece bem** nas informações de segurança reveja a página, mas algo falhar back-end.|
| Informações de segurança do utilizador eliminado | Êxito | Utilizador eliminado *método*. | Este evento ocorre quando um usuário exclui um método individual. *Método* pode ser Authenticator app, telefone, E-Mail, segurança perguntas, aplicação palavra-passe, telefone alternativo e assim por diante.|
| Informações de segurança do utilizador eliminado | Falha | Não foi possível eliminar o utilizador *método*. | Este evento ocorre quando um usuário tentar excluir um método, mas a tentativa falhar por algum motivo. *Método* pode ser Authenticator app, telefone, E-Mail, segurança perguntas, aplicação palavra-passe, telefone alternativo e assim por diante.|
| Informações de segurança de predefinição do utilizador foi alterado | Êxito | Utilizador alterou as informações de segurança predefinidas *método*. | Este evento ocorre quando um utilizador altera o método predefinido. *Método* podem ser notificação de aplicação de autenticador, um código da minha aplicação authenticator ou o token, chamada + X XXXXXXXXXX, texto, um código para + X XXXXXXXXX e assim por diante.|
| Informações de segurança de predefinição do utilizador foi alterado | Falha | Utilizador falha ao alterar as informações de segurança predefinidas *método*. | Este evento ocorre quando um usuário tentar altere o método predefinido, mas a tentativa falhar por algum motivo. *Método* podem ser notificação de aplicação de autenticador, um código da minha aplicação authenticator ou o token, chamada + X XXXXXXXXXX, texto, um código para + X XXXXXXXXX e assim por diante.|

## <a name="troubleshooting-interrupt-mode"></a>Modo de interrupção de resolução de problemas

| Sintoma | Passos de resolução de problemas |
| --- | --- |
| Não estou a ver os métodos que eu esperava ver. | 1. Verifique se o utilizador tem uma função de administrador do Azure AD. Se Sim, visualize as diferenças de política do administrador SSPR. <br> 2. Determine se o utilizador está a ser interrompido devido a imposição de registo de multi-factor Authentication ou a imposição de registo SSPR. Consulte a [fluxograma](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) em "Modos de registo combinado" para determinar quais métodos devem ser mostrados. <br> 3. Determine como recentemente a política de multi-factor Authentication ou SSPR foi alterada. Se a alteração recente, poderá demorar algum tempo para a política atualizada propagar.|

## <a name="troubleshooting-manage-mode"></a>Resolução de problemas a gerenciar o modo

| Sintoma | Passos de resolução de problemas |
| --- | --- |
| Não tenho a opção de adicionar um método específico. | 1. Determine se o método está ativado para multi-factor Authentication ou para SSPR. <br> 2. Se o método estiver ativado, guarde as políticas novamente e aguarde 1-2 horas antes de testar novamente. <br> 3. Se o método estiver ativado, certifique-se de que o utilizador não tiver configurado o número máximo desse método que eles têm permissão para configurar.|

## <a name="disable-combined-registration"></a>Desativar o registo combinado

Quando um utilizador regista um número de telefone e/ou a aplicação móvel no novo combinado a experiência, nossa carimbos de data / serviço um conjunto de sinalizadores (StrongAuthenticationMethods) para esses métodos em que o utilizador. Esta funcionalidade permite ao utilizador realizar o multi-factor Authentication com esses métodos sempre que a multi-factor Authentication é necessário.

Se um administrador ativa a pré-visualização, os utilizadores registem através da experiência nova e, em seguida, o administrador desative a pré-visualização, os utilizadores poderão inconscientemente estar registados multi-factor Authentication também.

Se um utilizador que tiver concluído o registo combinado vai para a página de registo de reposição (SSPR) de palavra-passe self-service atual em [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup), será pedido ao utilizador para efetuar a autenticação Multifator antes de poderem aceder ao nessa página. Este passo é esperado de um ponto de vista técnico, mas o há de novo para os utilizadores que foram anteriormente registados para SSPR apenas. Embora essa etapa adicional melhorar a postura de segurança do usuário, fornecendo outro nível de segurança, os administradores querem poderão reverter seus usuários para que eles já não são capazes de realizar o multi-factor Authentication.  

### <a name="how-to-roll-back-users"></a>Como reverte os utilizadores

Se, como administrador, pretende repor definições de multi-factor Authentication do utilizador, pode utilizar o script do PowerShell fornecido na secção seguinte. O script irá eliminar a propriedade StrongAuthenticationMethods para aplicação móvel de um utilizador e/ou número de telefone. Se executar esse script para os seus utilizadores, que precisam voltar a registar para multi-factor Authentication se precisarem. Recomendamos a reversão de teste com um ou dois utilizadores antes de reverter a todos os utilizadores afetados.

Os passos que se seguem irão ajudá-lo a reverter um utilizador ou grupo de utilizadores.

#### <a name="prerequisites"></a>Pré-requisitos

1. Instale os módulos do Azure AD PowerShell apropriados. Numa janela do PowerShell, execute estes comandos para instalar os módulos:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Guarde a lista de IDs de objeto de utilizador afectado para o seu computador como um arquivo de texto com o ID de um por linha. Tome nota da localização do ficheiro.
1. Guarde o seguinte script para o seu computador e tome nota da localização do script:

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

Numa janela do PowerShell, execute o seguinte comando, fornecendo as localizações de ficheiro de script e de utilizador. Introduza as credenciais de administrador global quando lhe for pedido. O script irá transmitir o resultado de cada operação de atualização de utilizador.

`<script location> -path <user file location>`

### <a name="disable-the-preview-experience"></a>Desativar a experiência de pré-visualização

Para desativar a experiência de pré-visualização para os seus utilizadores, conclua estes passos:

1. Inicie sessão no portal do Azure como um administrador do utilizador.
2. Aceda a **do Azure Active Directory** > **definições de utilizador** > **gerir as definições para as funcionalidades de pré-visualização do painel de acesso**.
3. Sob **os utilizadores podem utilizar funcionalidades de pré-visualização para registar e gerir informações de segurança**, defina o seletor para **None**e, em seguida, selecione **guardar**.

Os utilizadores já não serão pedidos para registar com a experiência de pré-visualização.

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre a pré-visualização pública do combinado de registo para reposição de palavra-passe self-service e o Azure multi-factor Authentication](concept-registration-mfa-sspr-combined.md)
