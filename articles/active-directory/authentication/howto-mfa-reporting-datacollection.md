---
title: Recolha de dados de utilizadores do Azure MFA - Diretório Ativo Azure
description: Que informações são usadas para ajudar a autenticar utilizadores através da Autenticação Multi-Factor Azure?
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848294"
---
# <a name="azure-multi-factor-authentication-user-data-collection"></a>Recolha de dados de utilizadores de autenticação multi-factor Azure

Este documento explica como encontrar informações de utilizador recolhidas pelo Azure Multi-Factor Authentication Server (MFA Server) e pelo Azure MFA (baseado em Nuvem) caso queira removê-la.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Informação recolhida

MFA Server, a extensão NPS e o Adaptador Azure MFA FS do Windows Server 2016 recolhem e armazenam as seguintes informações durante 90 dias.

Tentativas de autenticação (utilizadas para reporte e resolução de problemas):

- Carimbo de data/hora
- Nome de utilizador
- Nome Próprio
- Apelido
- Endereço de E-mail
- Grupo de Utilizadores
- Método de Autenticação (Chamada telefónica, Mensagem de Texto, Aplicação Móvel, Token OATH)
- Modo de chamada telefónica (Standard, PIN)
- Direção da mensagem de texto (ida, dois sentidos)
- Modo mensagem de texto (OTP, OTP + PIN)
- Modo aplicativo móvel (Standard, PIN)
- Modo DE Token OATH (Standard, PIN)
- Tipo de Autenticação
- Nome da Aplicação
- Código do País de Chamada Primária
- Número de telefone de chamada primária
- Extensão de chamada primária
- Chamada Primária Autenticada
- Resultado da chamada primária
- Código de país de chamada de reserva
- Número de telefone de chamada de reserva
- Extensão de chamada de backup
- Chamada de backup autenticada
- Resultado da chamada de backup
- Autenticado geral
- Resultado Geral
- Resultados
- Autenticado
- Resultado
- Endereço IP de início
- Dispositivos
- Token dispositivo
- Tipo de Dispositivo
- Versão de aplicativo móvel
- Versão do SO
- Resultado
- Verificação de notificação

Ativações (tentativas de ativar uma conta na aplicação móvel Microsoft Authenticator):
- Nome de utilizador
- Nome da Conta
- Carimbo de data/hora
- Obter resultado do código de ativação
- Ativar o sucesso
- Ativar erro
- Resultado do estado de ativação
- Nome do dispositivo
- Tipo de Dispositivo
- Versão da Aplicação
- Ficha DE JURAMENTO Ativada

Blocos (utilizados para determinar o estado bloqueado e para reporte):

- Carimbo de tempo de bloco
- Bloquear por nome de utilizador
- Nome de utilizador
- Código do País
- Número de Telefone
- Número de telefone Formado
- Extensão
- Extensão limpa
- Bloqueado
- Razão do bloco
- Carimbo de tempo de conclusão
- Razão de conclusão
- Bloqueio de Conta
- Alerta de Fraude
- Alerta de fraude não bloqueado
- Idioma

Bypass (usado para reportar):

- Carimbo de tempo de bypass
- Segundos de bypass
- Bypass Por Username
- Nome de utilizador
- Código do País
- Número de Telefone
- Número de telefone Formado
- Extensão
- Extensão limpa
- Razão bypass
- Carimbo de tempo de conclusão
- Razão de conclusão
- Bypass Usado

Alterações (utilizadas para sincronizar alterações de utilizadores no MFA Server ou No AD Azure):

- Alterar o carimbo de tempo
- Nome de utilizador
- Novo Código do País
- Novo número de telefone
- Nova extensão
- Novo Código de País de Backup
- Novo número de telefone de backup
- Nova extensão de backup
- Novo PIN
- Mudança de PIN Necessária
- Ficha de dispositivo antigo
- Novo Token de Dispositivo

## <a name="gather-data-from-mfa-server"></a>Recolher dados do Servidor MFA

Para a versão 8.0 do MFA Server, o seguinte processo permite que os administradores exportem todos os dados para os utilizadores:

- Inicie sessão no seu Servidor MFA, navegue para o separador **Utilizadores,** selecione o utilizador em questão e clique no botão **Editar.** Tire as imagens (Alt-PrtScn) de cada separador para fornecer ao utilizador as definições atuais de MFA.
- A partir da linha de comando do Servidor MFA, execute o seguinte comando alterando o caminho de acordo com a sua instalação `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` para produzir um ficheiro formado JSON.
- Os administradores também podem usar a operação SDK GetUserGdpr do Serviço Web como uma opção para exportar todas as informações do serviço em nuvem MFA recolhidas para um determinado utilizador ou incorporar numa solução de reporte maior.
- Procure `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` e quaisquer\<cópias de segurança para " nome de utilizador>" (inclua as cotações na pesquisa) para encontrar todas as instâncias do registo do utilizador adicionadas ou alteradas.
   - Estes registos podem ser limitados (mas não eliminados) desverificando **"Alterações** de utilizador de registo" no servidor mfa UX, secção de registo, separador Ficheiros de Registo.
   - Se o syslog estiver configurado e **"Registar alterações** no utilizador" é verificado na secção MFA Server UX, Logging, syslog, então as entradas de registo podem ser recolhidas a partir do syslog.
- Outras ocorrências do nome de utilizador em MultiFactorAuthSvc.log e outros ficheiros de registo do Servidor MFA relativos a tentativas de autenticação são consideradas operacionais e duplicadas para as informações fornecidas através do MultiFactorAuthGdpr.exe exportação ou SDK do Serviço Web GetUserGdpr.

## <a name="delete-data-from-mfa-server"></a>Eliminar dados do Servidor MFA

A partir da linha de comando do Servidor MFA, execute o seguinte comando alterando o caminho de acordo com a sua instalação `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` para eliminar todas as informações do serviço em nuvem MFA recolhidas para este utilizador.

- Os dados incluídos na exportação são eliminados em tempo real, mas podem demorar até 30 dias para que os dados operacionais ou duplicados sejam totalmente removidos.
- Os administradores também podem utilizar a operação SDK DeleteUserGdpr do Serviço Web como uma opção para eliminar todas as informações do serviço de nuvem MFA recolhidas para um determinado utilizador ou incorporar numa solução de reporte maior.

## <a name="gather-data-from-nps-extension"></a>Recolher dados da extensão do NPS

Utilize o Portal de Privacidade da [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer um pedido de exportação.

- As informações do MFA estão incluídas na exportação, que pode levar horas ou dias para ser concluída.
- As ocorrências do nome de utilizador nos registos do evento AzureMfa/AuthN/AuthNOptCh, AzureMfa/AuthZAdminCh e AzureMfa/AuthZ/AuthZOptCh são consideradas operacionais e duplicadas das informações fornecidas na exportação.

## <a name="delete-data-from-nps-extension"></a>Eliminar dados da extensão nps

Utilize o Portal de Privacidade da [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer um pedido de conta Perto para eliminar todas as informações do serviço de nuvem MFA recolhidas para este utilizador.

- Pode levar até 30 dias para que os dados sejam totalmente removidos.

## <a name="gather-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Recolher dados do Adaptador FS Azure MFA AD 2016 Do Windows Server 2016

Utilize o Portal de Privacidade da [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer um pedido de exportação. 

- As informações do MFA estão incluídas na exportação, que pode levar horas ou dias para ser concluída.
- As ocorrências do nome de utilizador nos registos de eventos AD FS Tracing/Debug (se ativados) são consideradas operacionais e duplicadas às informações fornecidas na exportação.

## <a name="delete-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Eliminar dados do Adaptador FS Azure MFA AD 2016

Utilize o Portal de Privacidade da [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer um pedido de conta Perto para eliminar todas as informações do serviço de nuvem MFA recolhidas para este utilizador.

- Pode levar até 30 dias para que os dados sejam totalmente removidos.

## <a name="gather-data-for-azure-mfa"></a>Recolha de dados para o Azure MFA

Utilize o Portal de Privacidade da [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer um pedido de exportação.

- As informações do MFA estão incluídas na exportação, que pode levar horas ou dias para ser concluída.

## <a name="delete-data-for-azure-mfa"></a>Eliminar dados para O MFA Azure

Utilize o Portal de Privacidade da [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer um pedido de conta Perto para eliminar todas as informações do serviço de nuvem MFA recolhidas para este utilizador.

- Pode levar até 30 dias para que os dados sejam totalmente removidos.

## <a name="next-steps"></a>Passos seguintes

[Relatório do Servidor MFA](howto-mfa-reporting.md)
