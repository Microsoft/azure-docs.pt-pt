---
title: Relatórios de acesso e uso para o Azure MFA-Azure Active Directory
description: Isso descreve como usar o recurso de autenticação multifator do Azure – relatórios.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed35abd5b9bfb8b9a74d598f1fa93d8f1a985bfb
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848277"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Relatórios na autenticação multifator do Azure

A autenticação multifator do Azure fornece vários relatórios que podem ser usados por você e sua organização acessível por meio do portal do Azure. A tabela a seguir lista os relatórios disponíveis:

| Relatório | Localização | Descrição |
|:--- |:--- |:--- |
| Histórico de utilizador bloqueado | O Azure AD > Security > MFA > bloquear/desbloquear usuários | Mostra o histórico de solicitações para bloquear ou desbloquear usuários. |
| Alertas de uso e fraude | Entradas de > do Azure AD | Fornece informações sobre o uso geral, Resumo do usuário e detalhes do usuário; bem como um histórico de alertas de fraude enviados durante o intervalo de datas especificado. |
| Uso para componentes locais | Relatório de atividade de > do Azure AD > Security > MFA | Fornece informações sobre o uso geral da MFA por meio da extensão do NPS, do ADFS e do servidor MFA. |
| Histórico de utilizador ignorado | O Azure AD > Security > MFA > bypass único | Fornece um histórico de solicitações para ignorar a autenticação multifator para um usuário. |
| Estado do servidor | Status do servidor > MFA > do Azure AD > Security | Exibe o status dos servidores de autenticação multifator associados à sua conta. |

## <a name="view-mfa-reports"></a>Exibir relatórios de MFA

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **segurança** > **MFA**.
3. Selecione o relatório que você deseja exibir.

   ![Relatório de status do servidor do MFA no portal do Azure](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Relatório de entradas do Azure AD

Com o **relatório de atividade** de entradas no [portal do Azure](https://portal.azure.com), você pode obter as informações necessárias para determinar o funcionamento do seu ambiente.

O relatório de entradas pode fornecer informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário, que incluem informações sobre o uso da autenticação multifator (MFA). Os dados de MFA disponibilizam-lhe informações sobre como esta está a funcionar na sua organização. Permite-lhe responder a perguntas como:

- O início de sessão foi submetido à MFA?
- Como é que o utilizador concluiu a MFA?
- Por que motivo é que o utilizador não conseguiu concluir a MFA?
- Quantos utilizadores são submetidos à MFA?
- Quantos utilizadores não conseguem concluir a submissão da MFA?
- Quais são os problemas mais comuns da MFA com que os utilizadores finais se deparam?

Esses dados estão disponíveis por meio do [portal do Azure](https://portal.azure.com) e da [API de relatório](../reports-monitoring/concept-reporting-api.md).

![Relatório de entradas do Azure AD no portal do Azure](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Estrutura de relatório de entradas

Os relatórios de atividades de início de sessão para a MFA dão-lhe acesso às informações seguintes:

**MFA necessária:** se a MFA é necessária para o início de sessão ou não. A MFA pode ser necessária devido a MFA por usuário, acesso condicional ou outros motivos. Os valores possíveis são **Sim** ou **não**.

**Resultado da MFA:** obter mais informações sobre se a MFA foi cumprida ou recusada:

- Se a MFA for cumprida, esta coluna disponibiliza mais informações sobre como foi cumprida.
   - Multi-Factor Authentication do Azure
      - concluída na cloud
      - expirou devido a políticas configuradas no inquilino
      - registo concluído
      - cumprida por reclamação no token
      - cumprida por reclamação fornecida por fornecedor externo
      - cumprida por autenticação forte
      - ignorada porque o fluxo executado foi o fluxo de início de sessão de mediador do Windows
      - ignorada devido a palavra-passe de aplicação
      - ignorada devido a localização
      - ignorada devido a dispositivo registado
      - ignorada devido a dispositivo memorizado
      - concluída com êxito
   - Redirecionado para fornecedor externo para autenticação multifator

- Se for recusada, a coluna mostrará o motivo da recusa.
   - Multi-Factor Authentication do Azure recusada;
      - autenticação em curso
      - tentativa de autenticação duplicada
      - código errado introduzido demasiadas vezes
      - autenticação inválida
      - código de verificação de aplicação móvel inválido
      - configuração incorreta
      - a chamada telefónica foi para o correio de voz
      - o formato do número de telefone é inválido
      - erro de serviço
      - não é possível comunicar com o telefone do utilizador
      - não é possível enviar a notificação de aplicação móvel para o dispositivo
      - não é possível enviar a notificação de aplicação móvel
      - o utilizador recusou a autenticação
      - o utilizador não respondeu à notificação de aplicação móvel
      - o utilizador não tem nenhum método de verificação registado
      - o utilizador introduziu um código incorreto
      - o utilizador introduziu um PIN incorreto
      - o utilizador desligou a chamada telefónica sem que a autenticação fosse bem sucedida
      - o utilizador está bloqueado
      - o utilizador nunca introduziu o código de verificação
      - o utilizador não foi encontrado
      - o código de verificação já foi utilizado uma vez

**Método de autenticação da MFA:** o método de autenticação que o utilizador utilizou para concluir a MFA. Os valores possíveis incluem:

- Mensagem de texto
- Notificação de aplicação móvel
- Chamada telefónica (telefone de autenticação)
- Código de verificação de aplicação móvel
- Chamada telefónica (telefone do escritório)
- Chamada telefónica (telefone de autenticação alternativo)

**Detalhe de autenticação da MFA:** versão limpa do número de telefone, por exemplo, + X XXXXXXXX64.

**Acesso condicional** Encontre informações sobre políticas de acesso condicional que afetaram a tentativa de entrada, incluindo:

- Nome da política
- Controles de concessão
- Controles de sessão
- Resultado

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>Relatórios do PowerShell sobre usuários registrados para MFA

Primeiro, verifique se você tem o [módulo PowerShell do MSOnline v1](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) instalado.

Identifique os usuários que se registraram para MFA usando o PowerShell a seguir.

```Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identifique os usuários que não se registraram para MFA usando o PowerShell a seguir.

```Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="possible-results-in-activity-reports"></a>Resultados possíveis em relatórios de atividades

A tabela a seguir pode ser usada para solucionar problemas de autenticação multifator usando a versão baixada do relatório de atividade de autenticação multifator. Eles não aparecerão diretamente no portal do Azure.

| Resultado da chamada | Descrição | Descrição ampla |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PIN Introduzido | O usuário inseriu um PIN.  Se a autenticação for bem-sucedida, elas entraram no PIN correto.  Se a autenticação for negada, elas inseriram um PIN incorreto ou o usuário será definido como modo padrão. |
| SUCCESS_NO_PIN | Somente # inserido | Se o usuário estiver definido como modo PIN e a autenticação for negada, isso significa que o usuário não inseriu seu PIN e apenas inseriu #.  Se o usuário estiver definido como modo padrão e a autenticação for bem sucedido, isso significa que o usuário inseriu apenas #, que é a coisa correta a fazer no modo padrão. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Não Premido Após a Entrada | O usuário não enviou nenhum dígito DTMF, pois # não foi inserido.  Outros dígitos inseridos não são enviados, a menos que # seja inserido indicando a conclusão da entrada. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Nenhuma Entrada de Telefone - Tempo Limite Excedido | A chamada foi respondida, mas não havia resposta.  Isso normalmente indica que a chamada foi selecionada pela caixa postal. |
| SUCCESS_PIN_EXPIRED | PIN Expirado e Não Alterado | O PIN do usuário expirou e foi solicitado a alterá-lo, mas a alteração do PIN não foi concluída com êxito. |
| SUCCESS_USED_CACHE | Cache Utilizada | A autenticação foi bem-sucedida sem uma chamada de autenticação multifator desde que uma autenticação bem-sucedida anterior para o mesmo nome de usuário ocorreu dentro do período de tempo de cache configurado. |
| SUCCESS_BYPASSED_AUTH | Autenticação Ignorada | Êxito na autenticação usando um bypass único iniciado para o usuário.  Consulte o relatório histórico de usuário desviado para obter mais detalhes sobre o bypass. |
| SUCCESS_USED_IP_BASED_CACHE | Cache baseado em IP usado | A autenticação foi bem-sucedida sem uma chamada de autenticação multifator desde uma autenticação bem-sucedida anterior para o mesmo nome de usuário, tipo de autenticação, nome do aplicativo e IP ocorrido dentro do período de tempo de cache configurado. |
| SUCCESS_USED_APP_BASED_CACHE | Cache baseado em aplicativo usado | A autenticação foi bem-sucedida sem uma chamada de autenticação multifator desde uma autenticação bem-sucedida anterior para o mesmo nome de usuário, tipo de autenticação e nome do aplicativo dentro do período de tempo de cache configurado. |
| SUCCESS_INVALID_INPUT | Entrada de Telefone Inválido | A resposta enviada do telefone não é válida.  Isso pode ser de uma máquina de fax ou modem, ou o usuário pode ter inserido * como parte do PIN. |
| SUCCESS_USER_BLOCKED | Utilizador Está Bloqueado | O número de telefone do usuário está bloqueado.  Um número bloqueado pode ser iniciado pelo usuário durante uma chamada de autenticação ou por um administrador usando o portal do Azure. <br> Observação: um número bloqueado também é um subproduto de um alerta de fraude. |
| SUCCESS_SMS_AUTHENTICATED | Mensagem de Texto Autenticada | Para a mensagem de teste de duas vias, o usuário respondeu corretamente com sua OTP (senha de uso único) ou OTP + PIN. |
| SUCCESS_SMS_SENT | Mensagem de Texto Enviada | Para mensagem de texto, a mensagem de texto que contém a senha de uso único (OTP) foi enviada com êxito.  O usuário vai inserir a OTP ou OTP + PIN no aplicativo para concluir a autenticação. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Aplicação Móvel Autenticada | O usuário foi autenticado com êxito por meio do aplicativo móvel. |
| SUCCESS_OATH_CODE_PENDING | Código OATH Pendente | O usuário foi solicitado a fornecer seu código OATH, mas não respondeu. |
| SUCCESS_OATH_CODE_VERIFIED | Código OATH Verificado | O usuário inseriu um código OATH válido quando solicitado. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Código OATH de Contingência Verificado | O usuário teve a autenticação negada usando o método de autenticação multifator primário e forneceu um código OATH válido para fallback. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Respostas às Perguntas de Segurança de Contingência | O usuário teve a autenticação negada usando seu método de autenticação multifator primário e respondeu suas perguntas de segurança corretamente para fallback. |
| FAILED_PHONE_BUSY | Autenticação Já Em Curso | A autenticação multifator já está processando uma autenticação para esse usuário.  Isso geralmente é causado por clientes RADIUS que enviam várias solicitações de autenticação durante o mesmo logon. |
| CONFIG_ISSUE | Telefone Inacessível | A chamada foi tentada, mas não pôde ser colocada ou não foi respondida.  Isso inclui sinal de ocupado, sinal de fast Busy (desconectado), três tons (número não mais em serviço), tempo limite atingido durante o toque, etc. |
| FAILED_INVALID_PHONENUMBER | Formato de Número de Telefone Inválido | O número de telefone tem um formato inválido.  Os números de telefone devem ser numéricos e devem ter 10 dígitos para o código do país + 1 (Estados Unidos & Canadá). |
| FAILED_USER_HUNGUP_ON_US | O Utilizador Desligou o Telefone | O usuário respondeu ao telefone, mas, em seguida, travou sem pressionar nenhum botão. |
| FAILED_INVALID_EXTENSION | Extensão Inválida | A extensão contém caracteres inválidos.  Somente dígitos, vírgulas, * e # são permitidos.  Um prefixo @ também pode ser usado. |
| FAILED_FRAUD_CODE_ENTERED | Código Fraudulento Introduzido | O usuário optou por relatar fraude durante a chamada, resultando em uma autenticação negada e um número de telefone bloqueado.| 
| FAILED_SERVER_ERROR | Não é Possível Efetuar a Chamada | O serviço de autenticação multifator não pôde fazer a chamada. |
| FAILED_SMS_NOT_SENT | Não Foi Possível Enviar a Mensagem de Texto | Não foi possível enviar a mensagem de texto.  A autenticação foi negada. |
| FAILED_SMS_OTP_INCORRECT | Mensagem de Texto OTP Incorreta | O usuário inseriu uma senha de uso único (OTP) incorreta da mensagem de texto recebida.  A autenticação foi negada. |
| FAILED_SMS_OTP_PIN_INCORRECT | Mensagem de Texto OTP + PIN Incorreta | O usuário inseriu uma senha de uso único (OTP) incorreta e/ou um PIN de usuário incorreto.  A autenticação foi negada. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Máximo de tentativas de OTP de mensagem de texto excedido | O usuário excedeu o número máximo de tentativas de OTP (senha de uso único). |
| FAILED_PHONE_APP_DENIED | Aplicação Móvel Negada | O usuário negou a autenticação no aplicativo móvel pressionando o botão negar. |
| FAILED_PHONE_APP_INVALID_PIN | PIN de Aplicação Móvel Inválido | O usuário inseriu um PIN inválido ao autenticar no aplicativo móvel. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | PIN de Aplicação Móvel Não Alterado | O usuário não concluiu com êxito uma alteração de PIN necessária no aplicativo móvel. |
| FAILED_FRAUD_REPORTED | Fraude Reportada | O usuário relatou fraude no aplicativo móvel. |
| FAILED_PHONE_APP_NO_RESPONSE | Aplicação Móvel Não Responde | O usuário não respondeu à solicitação de autenticação do aplicativo móvel. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Todos os Dispositivos de Aplicações Móveis Bloqueados | Os dispositivos de aplicativo móvel para este usuário não estão mais respondendo a notificações e foram bloqueados. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Falha de Notificação de Aplicação Móvel | Ocorreu uma falha ao tentar enviar uma notificação para o aplicativo móvel no dispositivo do usuário. |
| FAILED_PHONE_APP_INVALID_RESULT | Resultado de Aplicação Móvel Inválido | O aplicativo móvel retornou um resultado inválido. |
| FAILED_OATH_CODE_INCORRECT | Código OATH Incorreto | O usuário inseriu um código OATH incorreto.  A autenticação foi negada. |
| FAILED_OATH_CODE_PIN_INCORRECT | Código OATH + PIN incorreto | O usuário inseriu um código OATH incorreto e/ou um PIN de usuário incorreto.  A autenticação foi negada. |
| FAILED_OATH_CODE_DUPLICATE | Código OATH Duplicado | O usuário inseriu um código OATH que foi usado anteriormente.  A autenticação foi negada. |
| FAILED_OATH_CODE_OLD | Código OATH desatualizado | O usuário inseriu um código OATH que precede um código OATH que foi usado anteriormente.  A autenticação foi negada. |
| FAILED_OATH_TOKEN_TIMEOUT | Tempo limite do resultado do código OATH | O usuário demorou muito para inserir o código OATH e a tentativa de autenticação multifator já tinha atingido o tempo limite. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Tempo limite do resultado de perguntas de segurança | O usuário demorou muito para inserir a resposta a perguntas de segurança e a tentativa de autenticação multifator já tinha atingido o tempo limite. |
| FAILED_AUTH_RESULT_TIMEOUT | Tempo limite de resultado de autenticação | O usuário demorou muito para concluir a tentativa de autenticação multifator. |
| FAILED_AUTHENTICATION_THROTTLED | Autenticação limitada | A tentativa de autenticação multifator foi limitada pelo serviço. |

## <a name="next-steps"></a>Passos seguintes

* [Relatórios de uso e informações do SSPR e MFA](howto-authentication-methods-usage-insights.md)
* [Para usuários](../user-help/multi-factor-authentication-end-user.md)
* [Onde implantar](concept-mfa-whichversion.md)
