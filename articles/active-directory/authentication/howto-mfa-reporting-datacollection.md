---
title: Recolha de dados de utilizadores Azure AD MFA - Azure Ative Directory
description: Que informações são utilizadas para ajudar a autenticar os utilizadores pela Autenticação Multi-Factor AZURE AD?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2124ca9fae76d87a695a2c453f7b222e7a08794
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742798"
---
# <a name="azure-ad-multi-factor-authentication-user-data-collection"></a>Recolha de dados de autenticação multi-factor Azure AD

Este documento explica como encontrar informações do utilizador recolhidas pelo Azure Multi-Factor Authentication Server (MFA Server) e Azure AD MFA (cloud-based) no caso de pretender removê-la.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Informação recolhida

O MFA Server, a Extensão NPS e o Adaptador AD AD FS AD AD AD AD Azure Ad FS recolhem e armazenam as seguintes informações durante 90 dias.

Tentativas de autenticação (utilizadas para reportar e resolver problemas):

- Timestamp
- Nome de utilizador
- Nome Próprio
- Apelido
- Endereço de E-mail
- Grupo de Utilizadores
- Método de autenticação (chamada telefónica, mensagem de texto, aplicação móvel, ficha de OATH)
- Modo de chamada telefónica (Standard, PIN)
- Direção de mensagem de texto (sentido único, dois sentidos)
- Modo de mensagem de texto (OTP, OTP + PIN)
- Modo aplicativo móvel (Standard, PIN)
- Modo token OATH (Standard, PIN)
- Tipo de Autenticação
- Nome da Aplicação
- Código de país de chamada primária
- Número de telefone de chamada primária
- Extensão de chamada primária
- Chamada primária autenticada
- Resultado da chamada primária
- Código país de chamada de backup
- Número de telefone de chamada de reserva
- Extensão de chamada de backup
- Chamada de backup autenticada
- Resultado da chamada de backup
- Autenticado geral
- Resultado Global
- Resultados
- Autenticado
- Result
- Endereço IP iniciador
- Dispositivos
- Token do dispositivo
- Tipo de Dispositivo
- Versão de aplicativo móvel
- Versão do SO
- Result
- Verificação usada para notificação

Ativações (tentativas de ativar uma conta na aplicação móvel Microsoft Authenticator):
- Nome de utilizador
- Nome da Conta
- Timestamp
- Obtenha resultado do código de ativação
- Ativar o sucesso
- Ativar erro
- Resultado do estado de ativação
- Nome do dispositivo
- Tipo de Dispositivo
- Versão da Aplicação
- Ficha do OATH Ativada

Blocos (utilizados para determinar o estado bloqueado e para reportar):

- Bloqueio de tempo
- Bloco por Nome de Utilizador
- Nome de utilizador
- Código do País
- Número de Telefone
- Número de telefone formatado
- Extensão
- Extensão limpa
- Bloqueado
- Razão do Bloco
- Data de conclusão
- Razão de conclusão
- Bloqueio de Conta
- Alerta de Fraude
- Alerta de fraude não bloqueado
- Linguagem

Desvios (utilizados para reportar):

- Hora do bypass
- Bypass Segundos
- Bypass por Nome de Utilizador
- Nome de utilizador
- Código do País
- Número de Telefone
- Número de telefone formatado
- Extensão
- Extensão limpa
- Contornar a razão
- Data de conclusão
- Razão de conclusão
- Bypass usado

Alterações (utilizadas para sincronizar alterações do utilizador no MFA Server ou no AD AZure):

- Alteração da hora de alteração
- Nome de utilizador
- Novo Código do País
- Novo número de telefone
- Nova extensão
- Novo código de país de reserva
- Novo número de telefone de reserva
- Nova extensão de backup
- Novo PIN
- Alteração PIN Necessária
- Ficha de dispositivo antigo
- Novo token de dispositivo

## <a name="gather-data-from-mfa-server"></a>Recolher dados do MFA Server

Para a versão 8.0 ou superior do MFA Server, o seguinte processo permite aos administradores exportar todos os dados para os utilizadores:

- Inicie sessão no seu Servidor MFA, navegue no **separador Utilizadores,** selecione o utilizador em questão e clique no botão **Editar.** Tire as imagens (Alt-PrtScn) de cada separador para fornecer ao utilizador as suas definições de MFA atuais.
- A partir da linha de comando do MFA Server, executar o seguinte comando alterando o caminho de acordo com a sua instalação `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` para produzir um ficheiro formatado JSON.
- Os administradores também podem usar a operação SDK GetUserGdpr do Serviço Web como uma opção para exportar todas as informações de serviços de nuvem MFA recolhidas para um determinado utilizador ou incorporar numa solução de reporte maior.
- Procure `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` e quaisquer backups para \<username> " " (inclua as citações na pesquisa) para encontrar todas as instâncias do registo do utilizador sendo adicionadas ou alteradas.
   - Estes registos podem ser limitados (mas não eliminados) desmarcando **"Registar alterações do utilizador"** no MFA Server UX, secção de registo, separador Ficheiros de Registo.
   - Se o syslog estiver configurado e **"Registar alterações do utilizador"** é verificado no MFA Server UX, secção de registo, separador Syslog, então as entradas de registo podem ser recolhidas a partir de syslog.
- Outras ocorrências do nome de utilizador no MultiFactorAuthSvc.log e outros ficheiros de registo do MFA Server relativos a tentativas de autenticação são considerados operacionais e duplicados às informações fornecidas através MultiFactorAuthGdpr.exe exportação ou serviço web SDK GetUserGdpr.

## <a name="delete-data-from-mfa-server"></a>Eliminar dados do MFA Server

A partir da linha de comando do MFA Server, executar o seguinte comando alterando o caminho de acordo com a sua instalação `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` para eliminar todas as informações de serviço de nuvem MFA recolhidas para este utilizador.

- Os dados incluídos na exportação são eliminados em tempo real, mas pode levar até 30 dias para que os dados operacionais ou duplicados sejam totalmente removidos.
- Os administradores também podem usar a operação SDK DeleteUserGdpr do Serviço Web como uma opção para eliminar todas as informações de serviço de nuvem MFA recolhidas para um determinado utilizador ou incorporar numa solução de reporte maior.

## <a name="gather-data-from-nps-extension"></a>Recolher dados da extensão do NPS

Utilize o [Portal de Privacidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) da Microsoft para fazer um pedido de exportação.

- As informações sobre o MFA estão incluídas na exportação, que pode demorar horas ou dias a ser concluída.
- As ocorrências do nome de utilizador nos registos de eventos AzureMfa/AuthN/AuthNOptCh, AzureMfa/AuthZ/AuthZAdminCh e AzureMfa/AuthZ/AuthZOptCh são consideradas operacionais e duplicativas às informações fornecidas na exportação.

## <a name="delete-data-from-nps-extension"></a>Eliminar dados da extensão de NPS

Utilize o [Portal de Privacidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) da Microsoft para fazer um pedido de Conta Perto para eliminar todas as informações do serviço de cloud MFA recolhidas para este utilizador.

- Pode levar até 30 dias para que os dados sejam totalmente removidos.

## <a name="gather-data-from-windows-server-2016-azure-ad-mfa-ad-fs-adapter"></a>Recolha dados do Adaptador AD FS do Windows Server 2016 Azure AD MFA

Utilize o [Portal de Privacidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) da Microsoft para fazer um pedido de exportação. 

- As informações sobre o MFA estão incluídas na exportação, que pode demorar horas ou dias a ser concluída.
- As ocorrências do nome de utilizador nos registos de eventos AD FS Tracing/Debug (se ativados) são consideradas operacionais e duplicadas às informações fornecidas na exportação.

## <a name="delete-data-from-windows-server-2016-azure-ad-mfa-ad-fs-adapter"></a>Eliminar dados do Adaptador AD FS do Windows Server 2016 Azure AD MFA

Utilize o [Portal de Privacidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) da Microsoft para fazer um pedido de Conta Perto para eliminar todas as informações do serviço de cloud MFA recolhidas para este utilizador.

- Pode levar até 30 dias para que os dados sejam totalmente removidos.

## <a name="gather-data-for-azure-ad-mfa"></a>Recolha de dados para Azure AD MFA

Utilize o [Portal de Privacidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) da Microsoft para fazer um pedido de exportação.

- As informações sobre o MFA estão incluídas na exportação, que pode demorar horas ou dias a ser concluída.

## <a name="delete-data-for-azure-ad-mfa"></a>Eliminar dados para Azure AD MFA

Utilize o [Portal de Privacidade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) da Microsoft para fazer um pedido de Conta Perto para eliminar todas as informações do serviço de cloud MFA recolhidas para este utilizador.

- Pode levar até 30 dias para que os dados sejam totalmente removidos.

## <a name="next-steps"></a>Passos seguintes

[Relatório do Servidor MFA](howto-mfa-reporting.md)
