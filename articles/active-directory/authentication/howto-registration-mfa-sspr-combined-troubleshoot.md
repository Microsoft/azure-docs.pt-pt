---
title: Registo combinado de resolução de problemas - Azure Ative Directory
description: Resolução de problemas Azure AD Multi-Factor Autenticação e autosserviço de autosserviço reset registo combinado
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: db87887fc2b51c7cb8cb300eb8e711d3ae9b6ac8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98610794"
---
# <a name="troubleshooting-combined-security-information-registration"></a>Resolução de problemas combinado de registo de informações de segurança

As informações deste artigo destinam-se a orientar os administradores que estão a resolver problemas relatados pelos utilizadores da experiência de registo combinado.

## <a name="audit-logs"></a>Registos de auditoria

Os eventos registados para registo combinado estão no serviço Métodos de Autenticação nos registos de auditoria Azure AD.

![Interface de registos de auditoria Azure AD mostrando eventos de registo](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

O quadro que se segue lista todos os eventos de auditoria gerados pelo registo combinado:

| Atividade | Estado | Razão | Description |
| --- | --- | --- | --- |
| Utilizador registou todas as informações de segurança necessárias | Com êxito | O utilizador registou todas as informações de segurança necessárias. | Este evento ocorre quando um utilizador completou o registo com sucesso.|
| Utilizador registou todas as informações de segurança necessárias | Falha | O utilizador cancelou o registo de informações de segurança. | Este evento ocorre quando um utilizador cancela o registo do modo de interrupção.|
| Informação de segurança registada pelo utilizador | Com êxito | Método *registado* pelo utilizador . | Este evento ocorre quando um utilizador regista um método individual. *O método* pode ser app Autenticador, Telefone, E-mail, Questões de Segurança, Senha de aplicação, telefone alternativo, e assim por diante.| 
| Informação de segurança revista pelo utilizador | Com êxito | O utilizador reviu com sucesso as informações de segurança. | Este evento ocorre quando um utilizador seleciona **Fica bem** na página de revisão de informações de segurança.|
| Informação de segurança revista pelo utilizador | Falha | O utilizador não reviu as informações de segurança. | Este evento ocorre quando um utilizador seleciona **Fica bem** na página de revisão de informações de segurança, mas algo falha no backend.|
| Informação de segurança eliminada pelo utilizador | Com êxito | Método eliminado pelo *utilizador.* | Este evento ocorre quando um utilizador elimina um método individual. *O método* pode ser app Autenticador, Telefone, E-mail, Questões de Segurança, Senha de aplicação, telefone alternativo, e assim por diante.|
| Informação de segurança eliminada pelo utilizador | Falha | O utilizador não conseguiu eliminar o *método.* | Este evento ocorre quando um utilizador tenta apagar um método, mas a tentativa falha por alguma razão. *O método* pode ser app Autenticador, Telefone, E-mail, Questões de Segurança, Senha de aplicação, telefone alternativo, e assim por diante.|
| O utilizador alterou as informações de segurança predefinidos | Com êxito | O utilizador alterou a informação de segurança predefinida para *o método*. | Este evento ocorre quando um utilizador altera o método predefinido. *O método* pode ser notificação de aplicação autenticador, um código da minha app autenticadora ou token, Call +X XXXXXXXXX, Texto de um código para +X XXXXXXX, e assim por diante.|
| O utilizador alterou as informações de segurança predefinidos | Falha | O utilizador não alterou a informação de segurança predefinida para *o método*. | Este evento ocorre quando um utilizador tenta alterar o método padrão, mas a tentativa falha por alguma razão. *O método* pode ser notificação de aplicação autenticador, um código da minha app autenticadora ou token, Call +X XXXXXXXXX, Texto de um código para +X XXXXXXX, e assim por diante.|

## <a name="troubleshooting-interrupt-mode"></a>Modo de interrupção de resolução de problemas

| Sintoma | Passos de resolução de problemas |
| --- | --- |
| Não estou a ver os métodos que esperava ver. | 1. Verifique se o utilizador tem uma função de administração Ad Ad Azure. Se sim, veja as diferenças políticas de administração da SSPR. <br> 2. Determinar se o utilizador está a ser interrompido devido à aplicação do registo de autenticação multi-factor ou à aplicação do registo SSPR. Consulte o [fluxograma](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) em "Modos de registo combinados" para determinar quais os métodos a apresentar. <br> 3. Determinar com que forma a política de autenticação multi-factor ou SSPR foi alterada recentemente. Se a mudança foi recente, pode levar algum tempo para que a política atualizada se propague.|

## <a name="troubleshooting-manage-mode"></a>Modo de gestão de resolução de problemas

| Sintoma | Passos de resolução de problemas |
| --- | --- |
| Não tenho a opção de adicionar um método específico. | 1. Determinar se o método está ativado para autenticação multi-factor ou para SSPR. <br> 2. Se o método estiver ativado, guarde novamente as políticas e aguarde 1-2 horas antes de voltar a testar. <br> 3. Se o método estiver ativado, certifique-se de que o utilizador ainda não estabeleceu o número máximo desse método que lhes é permitido configurar.|

## <a name="disable-combined-registration"></a>Desativar o registo combinado

Quando um utilizador regista um número de telefone e/ou aplicação móvel na nova experiência combinada, o nosso serviço marca um conjunto de bandeiras (StrongAuthenticationMethods) para esses métodos nesse utilizador. Esta funcionalidade permite ao utilizador realizar a autenticação multi-factor com esses métodos sempre que for necessária a autenticação multi-factor.

Se um administrador permitir a pré-visualização, os utilizadores registam-se através da nova experiência e, em seguida, o administrador desativa a pré-visualização, os utilizadores poderão, sem saber, ser registados para a Autenticação Multi-Factor também.

Se um utilizador que tenha concluído o registo combinado for para a atual página de registo de redefinição de senha de autosserviço (SSPR), [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) o utilizador será solicitado a realizar a Autenticação Multi-Factor antes de poder aceder a essa página. Este passo é esperado do ponto de vista técnico, mas é novo para utilizadores que estavam previamente registados apenas para SSPR. Embora este passo extra melhore a postura de segurança do utilizador, fornecendo outro nível de segurança, os administradores podem querer reverter os seus utilizadores para que não possam continuar a realizar a Autenticação Multi-Factor.  

### <a name="how-to-roll-back-users"></a>Como reverter os utilizadores

Se, como administrador, pretender reiniciar as definições de autenticação multi-factor de um utilizador, pode utilizar o script PowerShell fornecido na secção seguinte. O script limpará a propriedade StrongAuthenticationMethods para a aplicação móvel de um utilizador e/ou número de telefone. Se executar este script para os seus utilizadores, eles terão de voltar a registar-se para autenticação multi-factor, caso precisem. Recomendamos testar o revés com um ou dois utilizadores antes de reverter todos os utilizadores afetados.

Os passos que se seguem ajudarão a reverter um utilizador ou grupo de utilizadores.

#### <a name="prerequisites"></a>Pré-requisitos

1. Instale os módulos Azure AD PowerShell apropriados. Numa janela PowerShell, execute estes comandos para instalar os módulos:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Guarde a lista de IDs de objetos de utilizador afetados para o seu computador como um ficheiro de texto com um ID por linha. Tome nota da localização do ficheiro.
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

Numa janela PowerShell, execute o seguinte comando, fornecendo as localizações do script e do ficheiro do utilizador. Insira credenciais de administrador global quando solicitado. O script irá obter o resultado de cada operação de atualização do utilizador.

`<script location> -path <user file location>`

### <a name="disable-the-updated-experience"></a>Desativar a experiência atualizada

Para desativar a experiência atualizada para os seus utilizadores, complete estes passos:

1. Inscreva-se no portal Azure como administrador de utilizador.
2. Vá às definições do Utilizador **do Diretor Ativo Azure**  >    >  **Gerir as definições para funcionalidades de pré-visualização do painel** de acesso .
3. Em **Utilizadores podem utilizar funcionalidades de pré-visualização para registar e gerir informações de segurança**, definir o seletor para **Nenhum**, e, em seguida, selecionar **Guardar**.

Os utilizadores deixarão de ser solicitados a registarem-se utilizando a experiência atualizada.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o registo combinado para reset de senha de autosserviço e autenticação multi-factor AZure AD](concept-registration-mfa-sspr-combined.md)
