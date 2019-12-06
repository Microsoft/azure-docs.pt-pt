---
title: Coleta de dados de usuário do Azure MFA-Azure Active Directory
description: Quais informações são usadas para ajudar a autenticar usuários pela autenticação multifator do Azure?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bc0f0dbcd08df887b2484be6ca8c92a85962c1c
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848294"
---
# <a name="azure-multi-factor-authentication-user-data-collection"></a>Coleta de dados do usuário da autenticação multifator do Azure

Este documento explica como localizar informações de usuário coletadas pelo Azure Servidor de Autenticação Multifator (servidor MFA) e pelo Azure MFA (baseado em nuvem) no evento que você gostaria de removê-la.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Informações coletadas

O servidor MFA, a extensão NPS e o adaptador de AD FS do Azure MFA do Windows Server 2016 coletam e armazenam as seguintes informações por 90 dias.

Tentativas de autenticação (usadas para relatórios e solução de problemas):

- Carimbo de data/hora
- Nome de utilizador
- Nome Próprio
- Apelido
- Endereço de E-mail
- Grupo do Utilizador
- Método de autenticação (chamada telefônica, mensagem de texto, aplicativo móvel, token OATH)
- Modo de chamada telefônica (Standard, PIN)
- Direção da mensagem de texto (unidirecional, bidirecional)
- Modo de mensagem de texto (OTP, OTP + PIN)
- Modo de aplicativo móvel (padrão, PIN)
- Modo de token OATH (Standard, PIN)
- Tipo de Autenticação
- Nome da Aplicação
- Código do país da chamada principal
- Número de telefone de chamada principal
- Extensão de chamada primária
- Chamada primária autenticada
- Resultado da chamada principal
- Código do país da chamada de backup
- Número de telefone de chamada de backup
- Extensão de chamada de backup
- Chamada de backup autenticada
- Resultado da chamada de backup
- Geral autenticado
- Resultado geral
- Resultados
- Autenticado
- Resultado
- Iniciando endereço IP
- Dispositivos
- Token do dispositivo
- Tipo de Dispositivo
- Versão do aplicativo móvel
- Versão do SO
- Resultado
- Verificação usada para notificação

Ativações (tentam ativar uma conta no aplicativo móvel Microsoft Authenticator):
- Nome de utilizador
- Nome da Conta
- Carimbo de data/hora
- Obter resultado do código de ativação
- Êxito ao ativar
- Erro de ativação
- Resultado do status de ativação
- Nome do dispositivo
- Tipo de Dispositivo
- Versão da Aplicação
- Token OATH habilitado

Blocos (usados para determinar o estado bloqueado e para relatórios):

- Bloquear carimbo de data/hora
- Bloquear por nome de usuário
- Nome de utilizador
- Indicativo do País
- Número de Telefone
- Número de telefone formatado
- Extensão
- Limpar extensão
- Bloqueado
- Razão do Bloqueio
- Timestamp de conclusão
- Motivo da conclusão
- Bloqueio de Conta
- Alerta de fraude
- Alerta de fraude não bloqueado
- Linguagem

Bypasss (usados para relatórios):

- Ignorar carimbo de data/hora
- Segundos de Omissão
- Bypass por nome de usuário
- Nome de utilizador
- Indicativo do País
- Número de Telefone
- Número de telefone formatado
- Extensão
- Limpar extensão
- Motivo do bypass
- Timestamp de conclusão
- Motivo da conclusão
- Bypass usado

Alterações (usadas para sincronizar as alterações do usuário no servidor MFA ou no Azure AD):

- Alterar carimbo de data/hora
- Nome de utilizador
- Novo código do país
- Novo número de telefone
- Nova extensão
- Novo código de país de backup
- Novo número de telefone de backup
- Nova extensão de backup
- Novo PIN
- Necessário Alterar PIN
- Antigo token de dispositivo
- Novo token de dispositivo

## <a name="gather-data-from-mfa-server"></a>Coletar dados do servidor MFA

Para o servidor MFA versão 8,0 ou superior, o processo a seguir permite que os administradores exportem todos os dados para usuários:

- Faça logon no servidor MFA, navegue até a guia **usuários** , selecione o usuário em questão e clique no botão **Editar** . Faça capturas de tela (Alt-PrtScn) de cada guia para fornecer ao usuário suas configurações atuais de MFA.
- Na linha de comando do servidor MFA, execute o comando a seguir alterando o caminho de acordo com o `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` de instalação para produzir um arquivo formatado em JSON.
- Os administradores também podem usar a operação GetUserGdpr do SDK do serviço Web como uma opção para exportar todas as informações do serviço de nuvem MFA coletadas para um determinado usuário ou incorporá-las a uma solução de relatório maior.
- Pesquise `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` e quaisquer backups de "\<nome de usuário >" (inclua as aspas na pesquisa) para localizar todas as instâncias do registro de usuário que estão sendo adicionadas ou alteradas.
   - Esses registros podem ser limitados (mas não eliminados) ao desmarcar **"registrar alterações de usuário"** no UX do servidor MFA, na seção log, na guia arquivos de log.
   - Se o syslog estiver configurado e **"registrar alterações de usuário"** estiver marcada na UX do servidor MFA, na seção log, na guia syslog, as entradas de log poderão ser coletadas do syslog.
- Outras ocorrências do nome de usuário no MultiFactorAuthSvc. log e outros arquivos de log do servidor MFA referentes às tentativas de autenticação são consideradas operacionais e duplicadas para as informações fornecidas usando o SDK de exportação ou serviço Web do MultiFactorAuthGdpr. exe GetUserGdpr.

## <a name="delete-data-from-mfa-server"></a>Excluir dados do servidor MFA

Na linha de comando do servidor MFA, execute o seguinte comando alterando o caminho de acordo com o `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` de instalação para excluir todas as informações do serviço de nuvem MFA coletadas para esse usuário.

- Os dados incluídos na exportação são excluídos em tempo real, mas pode levar até 30 dias para que os dados operacionais ou duplicados sejam totalmente removidos.
- Os administradores também podem usar a operação DeleteUserGdpr do SDK do serviço Web como uma opção para excluir todas as informações do serviço de nuvem MFA coletadas para um determinado usuário ou incorporá-las a uma solução de relatório maior.

## <a name="gather-data-from-nps-extension"></a>Coletar dados da extensão do NPS

Use o [portal de privacidade da Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer uma solicitação de exportação.

- As informações de MFA são incluídas na exportação, o que pode levar horas ou dias para ser concluído.
- As ocorrências do nome de usuário nos logs de eventos AzureMfa/Authn/AuthNOptCh, AzureMfa/AuthZ/AuthZAdminCh e AzureMfa/AuthZ/AuthZOptCh são consideradas operacionais e duplicadas para as informações fornecidas na exportação.

## <a name="delete-data-from-nps-extension"></a>Excluir dados da extensão do NPS

Use o [portal de privacidade da Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer uma solicitação de conta fechar para excluir todas as informações de serviço de nuvem MFA coletadas para este usuário.

- Pode levar até 30 dias para que os dados sejam totalmente removidos.

## <a name="gather-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Coletar dados do adaptador de AD FS do Azure MFA do Windows Server 2016

Use o [portal de privacidade da Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer uma solicitação de exportação. 

- As informações de MFA são incluídas na exportação, o que pode levar horas ou dias para ser concluído.
- As ocorrências do nome de usuário nos logs de eventos de rastreamento/depuração de AD FS (se habilitado) são consideradas operacionais e duplicadas para as informações fornecidas na exportação.

## <a name="delete-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Excluir dados do adaptador de AD FS do Azure MFA do Windows Server 2016

Use o [portal de privacidade da Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer uma solicitação de conta fechar para excluir todas as informações de serviço de nuvem MFA coletadas para este usuário.

- Pode levar até 30 dias para que os dados sejam totalmente removidos.

## <a name="gather-data-for-azure-mfa"></a>Coletar dados para o Azure MFA

Use o [portal de privacidade da Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer uma solicitação de exportação.

- As informações de MFA são incluídas na exportação, o que pode levar horas ou dias para ser concluído.

## <a name="delete-data-for-azure-mfa"></a>Excluir dados para o Azure MFA

Use o [portal de privacidade da Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer uma solicitação de conta fechar para excluir todas as informações de serviço de nuvem MFA coletadas para este usuário.

- Pode levar até 30 dias para que os dados sejam totalmente removidos.

## <a name="next-steps"></a>Passos seguintes

[Relatórios do servidor MFA](howto-mfa-reporting.md)
