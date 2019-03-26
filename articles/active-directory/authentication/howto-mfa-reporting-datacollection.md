---
title: Recolha de Azure dados de utilizador do multi-factor Authentication - Azure Active Directory
description: As informações que são utilizadas para autenticar os utilizadores ao Azure multi-factor Authentication?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2b8d68cc348ce8e157c7d58424eaebb06940335
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436673"
---
# <a name="azure-multi-factor-authentication-user-data-collection"></a>Recolha de dados de utilizador multi-factor Authentication do Azure

Este documento explica como encontrar informações de utilizador recolhidas pelo servidor do Azure multi-factor Authentication (MFA Server) e o MFA do Azure (com base na Cloud), no caso de gostaria de removê-lo.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Informações recolhidas

Servidor MFA, a extensão de NPS e o Windows Server 2016 do Azure MFA adaptador AD FS recolher e armazenam as seguintes informações para 90 dias.

Tentativas de autenticação (que é utilizadas para relatórios e resolução de problemas):

- Carimbo de data/hora
- Nome de utilizador
- Nome Próprio
- Apelido
- Endereço de E-mail
- Grupo de Utilizadores
- Método de autenticação (chamada telefónica, texto mensagem, aplicação móvel, OATH Token)
- Modo de chamada telefónica (padrão, PIN)
- Direção de mensagem de texto (unidirecional, bidirecional)
- Modo de mensagem de texto (OTP, OTP + PIN)
- Modo de aplicação móvel (padrão, PIN)
- Modo de Token OATH (padrão, PIN)
- Tipo de Autenticação
- Nome da Aplicação
- Indicativo de país de chamada primário
- Número de telefone principal chamada
- Extensão de chamada primário
- Principal chamada autenticada
- Resultado da chamada primário
- Indicativo de país de chamada de cópia de segurança
- Número de telefone de chamada de cópia de segurança
- Extensão de chamada de cópia de segurança
- Cópia de segurança chamada autenticada
- Resultado da chamada de cópia de segurança
- Em geral autenticado
- Resultado geral
- Resultados
- Autenticados
- Resultado
- Iniciar o endereço IP
- Dispositivos
- Token do dispositivo
- Tipo de Dispositivo
- Versão de aplicação móvel
- Versão do SO
- Resultado
- Verificação utilizada para notificação

Ativações (tentativas para ativar uma conta na aplicação móvel Microsoft Authenticator):
- Nome de utilizador
- Nome da Conta
- Carimbo de data/hora
- Obter o resultado do código de ativação
- Ativar o sucesso
- Ativar erro
- Resultado de estado de ativação
- Nome do dispositivo
- Tipo de Dispositivo
- Versão da Aplicação
- Token OATH ativada

Blocos (utilizado para determinar o estado de bloqueado e por comunicarem as):

- Bloquear Timestamp
- Bloco por nome de utilizador
- Nome de utilizador
- Código do País
- Número de Telefone
- Número de telefone formatado
- Extensão
- Limpar a extensão
- Bloqueado
- Motivo do Bloqueio
- Timestamp de conclusão
- Motivo de conclusão
- Bloqueio de Conta
- Alerta de Fraude
- Alerta de fraude não bloqueada
- Idioma

Omissões (utilizadas para os relatórios):

- Timestamp omissão
- Segundos de Omissão
- Ignorar por nome de utilizador
- Nome de utilizador
- Código do País
- Número de Telefone
- Número de telefone formatado
- Extensão
- Limpar a extensão
- Motivo da Omissão
- Timestamp de conclusão
- Motivo de conclusão
- Ignorar utilizado

Alterações (usadas para sincronizar as alterações do utilizador para o servidor MFA ou AAD):

- Alterar Timestamp
- Nome de utilizador
- Novo Código de País
- Novo Número de Telefone
- Nova Extensão
- Novo código de país alternativo
- Novo número de telefone alternativo
- Nova extensão de cópia de segurança
- Novo PIN
- Exigir Alteração do PIN
- Token de dispositivo antigo
- Novo Token de Dispositivo

## <a name="gather-data-from-mfa-server"></a>Recolher dados a partir do servidor de MFA

Para o servidor MFA versão 8.0 ou superior, o seguinte processo permite aos administradores exportar todos os dados para os utilizadores:

- Inicie sessão no seu servidor de MFA, navegue para o **usuários** separador, selecione o utilizador em questão e clique nas **editar** botão. Tire capturas de ecrã (Alt-PrtScn) de cada separador para fornecer ao usuário suas definições atuais do MFA.
- Na linha de comando do servidor MFA, execute o seguinte comando alterar o caminho, de acordo com a instalação `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` produzir um JSON arquivo formatado.
- Os administradores também podem usar a operação de Web Service SDK GetUserGdpr como uma opção para exportar todas as informações de serviço do cloud MFA coletadas para um determinado usuário ou incorporar uma solução de relatórios maior.
- Pesquisa `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` e quaisquer cópias de segurança para "\<nome de utilizador >" (inclua as aspas na pesquisa) para localizar todas as instâncias do registo de utilizador que está a ser adicionadas ou alteradas.
   - Podem ser limitados (mas não eliminados) estes registos ao desmarcar a opção **"Alterações do utilizador de início de sessão"** na UX de servidor MFA, a secção de registo, separador de ficheiros de registo.
   - Se estiver configurado syslog, e **"Alterações do utilizador de início de sessão"** estiver marcada na UX de servidor MFA, a secção de registo, o separador Syslog, em seguida, as entradas de registo podem ser recolhidas de syslog em vez disso.
- Ficheiros pertencentes ao tentativas são consideradas operacional e duplicadas para as informações fornecidas através de exportação MultiFactorAuthGdpr.exe ou o SDK do serviço Web de autenticação de registo de outras ocorrências do nome de utilizador no multifactorauthsvc. log e outro servidor de MFA GetUserGdpr.

## <a name="delete-data-from-mfa-server"></a>Eliminar dados do servidor MFA

Na linha de comando do servidor MFA, execute o seguinte comando alterar o caminho, de acordo com a instalação `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` para eliminar todos os MFA na cloud service informações recolhidas para este utilizador.

- Dados incluídos na exportação serão eliminados em tempo real, mas poderá demorar até 30 dias de dados operacionais ou duplicados a ser completamente removido.
- Os administradores também podem usar a operação de Web Service SDK DeleteUserGdpr como uma opção para eliminar todas as informações de serviço do cloud MFA coletadas para um determinado usuário ou incorporar uma solução de relatórios maior.

## <a name="gather-data-from-nps-extension"></a>Recolher dados a partir da extensão NPS

Utilize o [Portal de privacidade do Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer um pedido de exportação.

- Informações de MFA estão incluídas na exportação, o que poderá demorar horas ou dias para ser concluído.
- Ocorrências do nome de utilizador no AzureMfa/AuthN/AuthNOptCh, AzureMfa/AuthZ/AuthZAdminCh e registos de eventos de AzureMfa/AuthZ/AuthZOptCh são consideradas operacional e duplicadas para as informações fornecidas na exportação.

## <a name="delete-data-from-nps-extension"></a>Eliminar dados da extensão NPS

Utilize o [Portal de privacidade do Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer um pedido para a conta fechar eliminar todos os MFA na cloud service informações recolhidas para este utilizador.

- Pode demorar até 30 dias de dados a ser completamente removido.

## <a name="gather-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Recolher dados a partir do Windows Server 2016 do Azure MFA adaptador AD FS

Utilize o [Portal de privacidade do Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer um pedido de exportação. 

- Informações de MFA estão incluídas na exportação, o que poderá demorar horas ou dias para ser concluído.
- Ocorrências do nome de utilizador nos logs de eventos de rastreamento/depuração do AD FS (se ativada) são consideradas operacional e duplicadas para as informações fornecidas na exportação.

## <a name="delete-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Eliminar dados do Windows Server 2016 do Azure MFA adaptador AD FS

Utilize o [Portal de privacidade do Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer um pedido para a conta fechar eliminar todos os MFA na cloud service informações recolhidas para este utilizador.

- Pode demorar até 30 dias de dados a ser completamente removido.

## <a name="gather-data-for-azure-mfa"></a>Recolher dados para a MFA do Azure

Utilize o [Portal de privacidade do Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer um pedido de exportação.

- Informações de MFA estão incluídas na exportação, o que poderá demorar horas ou dias para ser concluído.

## <a name="delete-data-for-azure-mfa"></a>Eliminar dados de MFA do Azure

Utilize o [Portal de privacidade do Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) para fazer um pedido para a conta fechar eliminar todos os MFA na cloud service informações recolhidas para este utilizador.

- Pode demorar até 30 dias de dados a ser completamente removido.

## <a name="next-steps"></a>Passos Seguintes

[Relatórios do servidor MFA](howto-mfa-reporting.md)
