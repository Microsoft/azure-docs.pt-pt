---
title: Registo combinado de troubleshoot - Diretório Ativo Azure
description: Troubleshoot Azure AD Multi-Factor Authentication e auto-service password reset registo combinado
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c840df2c53554519f62a3d1d7a7d8b305187ffb
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450943"
---
# <a name="troubleshooting-combined-security-information-registration"></a>Registo combinado de informações de segurança de resolução de problemas

A informação neste artigo destina-se a orientar os administradores que estão a resolver problemas relatados pelos utilizadores da experiência de registo combinado.

## <a name="audit-logs"></a>Registos de auditoria

Os eventos registados para registo combinado estão na categoria Métodos de Autenticação nos registos de auditoria da AD Azure.

![Interface de registos de registos da Auditoria Azure AD mostrando eventos de registo](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

O quadro seguinte enumera todos os eventos de auditoria gerados pelo registo combinado:

| Atividade | Estado | Razão | Descrição |
| --- | --- | --- | --- |
| O utilizador registou todas as informações de segurança necessárias | Êxito | O utilizador registou todas as informações de segurança necessárias. | Este evento ocorre quando um utilizador tiver concluído com sucesso o registo.|
| O utilizador registou todas as informações de segurança necessárias | Falha | O utilizador cancelou o registo de informações de segurança. | Este evento ocorre quando um utilizador cancela o registo do modo de interrupção.|
| Informações de segurança registadas pelo utilizador | Êxito | *Método*registado pelo utilizador . | Este evento ocorre quando um utilizador regista um método individual. *O método* pode ser aplicação Authenticator, Telefone, E-mail, Questões de Segurança, Palavra-passe de aplicativo, telefone alternativo, e assim por diante.| 
| Informações de segurança revistas pelo utilizador | Êxito | O utilizador reviu com sucesso informações de segurança. | Este evento ocorre quando um utilizador seleciona **Fica bem** na página de revisão de informações de segurança.|
| Informações de segurança revistas pelo utilizador | Falha | O utilizador não rereviu as informações de segurança. | Este evento ocorre quando um utilizador seleciona **Fica bem** na página de revisão de informações de segurança, mas algo falha no backend.|
| Informações de segurança eliminadas pelo utilizador | Êxito | *Método*eliminado do utilizador . | Este evento ocorre quando um utilizador elimina um método individual. *O método* pode ser aplicação Authenticator, Telefone, E-mail, Questões de Segurança, Palavra-passe de aplicativo, telefone alternativo, e assim por diante.|
| Informações de segurança eliminadas pelo utilizador | Falha | O utilizador falhou em eliminar o *método*. | Este evento ocorre quando um utilizador tenta apagar um método, mas a tentativa falha por alguma razão. *O método* pode ser aplicação Authenticator, Telefone, E-mail, Questões de Segurança, Palavra-passe de aplicativo, telefone alternativo, e assim por diante.|
| O utilizador alterou as informações de segurança padrão | Êxito | O utilizador alterou a informação de segurança padrão para *o método*. | Este evento ocorre quando um utilizador altera o método predefinido. *O método* pode ser notificação de aplicação Authenticator, um código da minha aplicação ou token autenticador, Call +X XXXXXXXXXX, Texto um código para +X XXXXXXXXX, e assim por diante.|
| O utilizador alterou as informações de segurança padrão | Falha | O utilizador não alterou as informações de segurança predefinidas para *o método*. | Este evento ocorre quando um utilizador tenta alterar o método predefinido, mas a tentativa falha por alguma razão. *O método* pode ser notificação de aplicação Authenticator, um código da minha aplicação ou token autenticador, Call +X XXXXXXXXXX, Texto um código para +X XXXXXXXXX, e assim por diante.|

## <a name="troubleshooting-interrupt-mode"></a>Modo de interrupção de resolução de problemas

| Sintoma | Passos de resolução de problemas |
| --- | --- |
| Não estou a ver os métodos que esperava ver. | 1. Verifique se o utilizador tem uma função de administrador ad-ad. Se sim, veja as diferenças políticas de administração da SSPR. <br> 2. Determine se o utilizador está a ser interrompido devido à aplicação do registo de autenticação multifactor ou à aplicação do registo SSPR. Consulte o [fluxograma](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) em "Modos de registo combinados" para determinar quais os métodos a mostrar. <br> 3. Determine até que ponto recentemente a autenticação multi-factor ou a política de SSPR foi alterada. Se a mudança fosse recente, poderia levar algum tempo para que a política atualizada se propagasse.|

## <a name="troubleshooting-manage-mode"></a>Modo de gestão de resolução de problemas

| Sintoma | Passos de resolução de problemas |
| --- | --- |
| Não tenho a opção de adicionar um método em particular. | 1. Determine se o método está ativado para autenticação multi-factor ou para SSPR. <br> 2. Se o método estiver ativado, guarde novamente as políticas e aguarde 1-2 horas antes de voltar a testar. <br> 3. Se o método estiver ativado, certifique-se de que o utilizador ainda não criou o número máximo desse método que está autorizado a configurar.|

## <a name="disable-combined-registration"></a>Desativar o registo combinado

Quando um utilizador regista um número de telefone e/ou uma aplicação móvel na nova experiência combinada, o nosso serviço carimba um conjunto de bandeiras (StrongAuthenticationMethods) para esses métodos nesse utilizador. Esta funcionalidade permite ao utilizador efetuar a Autenticação Multi-Factor com esses métodos sempre que for necessária a autenticação multi-factor.

Se um administrador permitir a pré-visualização, os utilizadores registam-se através da nova experiência e, em seguida, o administrador desativa a pré-visualização, os utilizadores podem, sem saber, ser registados para autenticação multi-factor também.

Se um utilizador que tenha concluído o registo combinado for para a página de [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)registo de senha de autosserviço (SSPR) em , o utilizador será solicitado a realizar a Autenticação Multi-Factor antes de poder aceder a essa página. Este passo é esperado do ponto de vista técnico, mas é novo para utilizadores que estavam previamente registados apenas para SSPR. Embora este passo extra melhore a postura de segurança do utilizador, fornecendo outro nível de segurança, os administradores podem querer reverter os seus utilizadores para que não sejam mais capazes de realizar a Autenticação Multi-Factor.  

### <a name="how-to-roll-back-users"></a>Como reverter os utilizadores

Se, como administrador, pretender redefinir as definições de autenticação multi-factor de um utilizador, pode utilizar o script PowerShell fornecido na secção seguinte. O script irá limpar a propriedade StrongAuthenticationMethods para a aplicação móvel de um utilizador e/ou número de telefone. Se executar este script para os seus utilizadores, terão de se reregistar para autenticação multi-factor, se precisarem. Recomendamos testar o reversão com um ou dois utilizadores antes de relançar todos os utilizadores afetados.

Os passos que se seguem irão ajudá-lo a reverter um utilizador ou grupo de utilizadores.

#### <a name="prerequisites"></a>Pré-requisitos

1. Instale os módulos PowerShell Da AD AD apropriados. Numa janela PowerShell, execute estes comandos para instalar os módulos:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Guarde a lista de IDs de objetos de utilizador afetados para o seu computador como ficheiro de texto com um ID por linha. Tome nota da localização do ficheiro.
1. Guarde o seguinte guião para o seu computador e tome nota da localização do script:

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

#### <a name="rollback"></a>Recuo

Numa janela PowerShell, execute o seguinte comando, fornecendo as localizações do script e do ficheiro do utilizador. Introduza credenciais de administrador global quando solicitado. O script irá obter o resultado de cada operação de atualização do utilizador.

`<script location> -path <user file location>`

### <a name="disable-the-updated-experience"></a>Desativar a experiência atualizada

Para desativar a experiência atualizada para os seus utilizadores, complete estas etapas:

1. Inscreva-se no portal Azure como administrador de utilizador.
2. Aceda às > **definições** > do **utilizador do Diretório Ativo do Azure****Gerir as definições para visualizações**do painel de acesso .
3. Em **'Utilizadores' podem utilizar funcionalidades de pré-visualização para registar e gerir informações**de segurança, definir o seletor para **Nenhum**, e, em seguida, selecionar **Guardar**.

Os utilizadores deixarão de ser solicitados a registarem-se utilizando a experiência atualizada.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o registo combinado para reset de senha de autosserviço e autenticação de multifactor Azure](concept-registration-mfa-sspr-combined.md)
