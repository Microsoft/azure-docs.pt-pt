---
title: Relatórios de acesso e utilização do MFA do Azure - Azure Active Directory
description: Descreve como utilizar a funcionalidade de multi-factor Authentication - relatórios.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988e8982b6f06fb1210330c5cafdb696892794fe
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235520"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Relatórios no Azure multi-factor Authentication

O Azure multi-factor Authentication fornece diversos relatórios que podem ser utilizados por e sua organização acessível através do portal do Azure. A tabela seguinte lista os relatórios disponíveis:

| Relatório | Location | Descrição |
|:--- |:--- |:--- |
| Histórico de utilizador bloqueado | O Azure AD > servidor MFA > bloquear/desbloquear utilizadores | Mostra o histórico de pedidos para bloquear ou desbloquear utilizadores. |
| Alertas de fraude e de utilização | O Azure AD > inícios de sessão | Fornece informações sobre a utilização global, resumo por utilizador e detalhes de utilizador como um histórico de alertas de fraude apresentados durante o intervalo de datas especificado. |
| Utilização de componentes no local | O Azure AD > servidor MFA > relatório de atividade | Fornece informações sobre a utilização global da MFA através da extensão NPS, AD FS e o servidor MFA. |
| Histórico de utilizador ignorado | O Azure AD > servidor MFA > omissão de uso individual | Fornece um histórico de pedidos para ignorar o multi-factor Authentication para um utilizador. |
| Estado do servidor | O Azure AD > servidor MFA > Estado do servidor | Apresenta que o estado dos servidores multi-factor Authentication associado à sua conta. |

## <a name="view-mfa-reports"></a>Ver relatórios de MFA

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No lado esquerdo, selecione **do Azure Active Directory** > **servidor MFA**.
3. Selecione o relatório que pretende ver.

   ![Relatório de estado do servidor MFA server no portal do Azure](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Relatório de inícios de sessão do Azure AD

Com o **relatório de atividade de inícios de sessão** no [portal do Azure](https://portal.azure.com), pode obter as informações necessárias para determinar o estado de funcionamento do ambiente.

O relatório de inícios de sessão pode fornecer informações sobre a utilização de aplicações geridas e início de sessão atividades do utilizador, que inclui informações sobre a utilização da autenticação multifator (MFA). Os dados de MFA disponibilizam-lhe informações sobre como esta está a funcionar na sua organização. Permite-lhe responder a perguntas como:

- O início de sessão foi submetido à MFA?
- Como é que o utilizador concluiu a MFA?
- Por que motivo é que o utilizador não conseguiu concluir a MFA?
- Quantos utilizadores são submetidos à MFA?
- Quantos utilizadores não conseguem concluir a submissão da MFA?
- Quais são os problemas mais comuns da MFA com que os utilizadores finais se deparam?

Estes dados estão disponíveis através da [portal do Azure](https://portal.azure.com) e o [API de relatórios](../reports-monitoring/concept-reporting-api.md).

![Relatório de inícios de sessão do AD do Azure no portal do Azure](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Estrutura de relatório de inícios de sessão

Os relatórios de atividades de início de sessão para a MFA dão-lhe acesso às informações seguintes:

**MFA necessária:** Se MFA é necessária para o início de sessão ou não. A MFA pode ser necessário devido à MFA por utilizador, o acesso condicional ou outros motivos. Os valores possíveis são **Sim** ou **não**.

**Resultado da MFA:** Obter mais informações sobre se o MFA foi cumprida ou negado:

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

**Método de autenticação de MFA:** O método de autenticação, o utilizador utilizado para concluir a MFA. Os valores possíveis incluem:

- Mensagem de texto
- Notificação de aplicação móvel
- Chamada telefónica (telefone de autenticação)
- Código de verificação de aplicação móvel
- Chamada telefónica (telefone do escritório)
- Chamada telefónica (telefone de autenticação alternativo)

**Detalhe de autenticação de MFA:** Limpa a versão do número de telefone, por exemplo: + X XXXXXXXX64.

**Acesso condicional** encontrar informações sobre as políticas de acesso condicional que afetados a tentativa de início de sessão, incluindo:

- Nome da política
- Controlos de concessão
- Controlos de sessão
- Resultado

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>PowerShell relatórios sobre os utilizadores registado para MFA

Em primeiro lugar, certifique-se de que tem o [módulo do PowerShell do MSOnline V1](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) instalado.

Identifique os utilizadores que foram registrados para MFA com o PowerShell que se segue.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identificar os utilizadores que não se registou para a MFA com o PowerShell que se segue.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="possible-results-in-activity-reports"></a>Possíveis resultados nos relatórios de atividade

A tabela seguinte pode ser utilizada para resolver problemas de autenticação multifator com a versão transferida do relatório de atividade de autenticação multifator. Não serão apresentados diretamente no portal do Azure.

| Resultado da chamada | Descrição | Descrição geral |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PIN introduzido | O utilizador introduziu um PIN.  Se a autenticação tiver êxito, em seguida, eles introduziu o PIN correto.  Se authentication for recusada, em seguida, eles introduziu um PIN incorreto ou o utilizador está definido para o modo padrão. |
| SUCCESS_NO_PIN | Apenas os # introduzido | Se o utilizador está definido para o modo PIN e a autenticação é negada, isso significa que o utilizador não introduzir o PIN e introduziu apenas #.  Se o utilizador está definido para o modo padrão e a autenticação é bem-sucedida isso significa que o utilizador introduzir apenas o # que é a correta coisa a fazer no modo padrão. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Não premido após introdução | O utilizador não enviou os dígitos DTMF, uma vez que o # não foi introduzido.  Outros dígitos introduzidos não são enviados, a menos que # é introduzido, que indica a conclusão da entrada. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Sem introdução no telemóvel - excedeu o limite de tempo | A chamada foi atendida, mas não houve resposta.  Normalmente, isto indica que a chamada foi captada por correio de voz. |
| SUCCESS_PIN_EXPIRED | PIN expirado e não alterado | O PIN do utilizador expirou e foram-lhes pedido que alterá-la, mas a alteração do PIN não foi concluída com êxito. |
| SUCCESS_USED_CACHE | Cache utilizada | Autenticação com êxito sem uma chamada de multi-factor Authentication, uma vez que uma autenticação bem-sucedida anterior para o mesmo nome de utilizador ocorreu durante o período de tempo de cache configurada. |
| SUCCESS_BYPASSED_AUTH | Autenticação ignorada | Autenticação com êxito usando uma omissão de uso individual iniciada para o utilizador.  Consulte o relatório de histórico de utilizador ignorado para obter mais detalhes sobre a omissão. |
| SUCCESS_USED_IP_BASED_CACHE | Cache utilizada baseado em IP | Autenticação com êxito sem uma chamada de multi-factor Authentication desde uma autenticação bem-sucedida anterior para o mesmo nome de utilizador, o tipo de autenticação, o nome da aplicação e IP ocorreu durante o período de tempo de cache configurada. |
| SUCCESS_USED_APP_BASED_CACHE | Cache utilizada com base na aplicação | Autenticação com êxito sem uma chamada de multi-factor Authentication desde uma autenticação bem-sucedida anterior para o mesmo nome de utilizador, o tipo de autenticação e o nome da aplicação durante o período de tempo de cache configurada. |
| SUCCESS_INVALID_INPUT | Entrada de telefone inválido | A resposta enviada do telefone não é válida.  Isto pode ser de uma máquina de fax ou modem ou o utilizador pode introduzir * como parte do seu PIN. |
| SUCCESS_USER_BLOCKED | O utilizador está bloqueado | Número de telefone do utilizador está bloqueado.  Um número bloqueado pode ser iniciado pelo utilizador durante uma chamada de autenticação ou por um administrador no portal do Azure. <br> NOTA:  Um número bloqueado também é um subproduto de um alerta de fraude. |
| SUCCESS_SMS_AUTHENTICATED | Mensagem de texto autenticada | Por mensagem de teste bidirecional, o utilizador corretamente respondeu com o código de acesso Monouso (OTP) ou o OTP + o PIN. |
| SUCCESS_SMS_SENT | Mensagem de texto enviada | Mensagem de texto, a mensagem de texto que contém o código Monouso (OTP) foi enviada com êxito.  O utilizador irá introduzir o OTP ou OTP + PIN no aplicativo para concluir a autenticação. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Aplicação móvel autenticada | O utilizador autenticado com êxito através da aplicação móvel. |
| SUCCESS_OATH_CODE_PENDING | Código OATH pendente | O utilizador foi-lhe pedido para o respetivo código OATH, mas não respondeu. |
| SUCCESS_OATH_CODE_VERIFIED | Código OATH verificado | O utilizador introduziu um código OATH válido quando lhe for pedido. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Código OATH de contingência validado | O utilizador foi negado autenticação usando o método de multi-factor Authentication principal e, em seguida, fornecido um código OATH válido para contingência. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Respostas para suas perguntas de segurança de contingência | O utilizador foi negado a autenticação utilizando o método de multi-factor Authentication principal e, em seguida, respostas para suas perguntas de segurança corretamente para contingência. |
| FAILED_PHONE_BUSY | Autenticação já em curso | Autenticação Multifator já está a processar uma autenticação para este utilizador.  Isso normalmente é causado por clientes RADIUS que enviam várias solicitações de autenticação durante o mesmo início de sessão. |
| CONFIG_ISSUE | Telefone inacessível | Chamada foi tentada, mas qualquer um pode não ser realizada ou não foi respondida.  Isto inclui o sinal de ocupado, sinal de ocupado rápido (desligado), chamamos-tons (número já não está no serviço), excedido ao toque, etc. |
| FAILED_INVALID_PHONENUMBER | Formato de número de telefone inválido | O número de telefone tem um formato inválido.  Números de telefone tem de ser numéricos e devem ter 10 dígitos para o indicativo do país + 1 (Estados Unidos e Canadá). |
| FAILED_USER_HUNGUP_ON_US | O utilizador desligou o telefone | O utilizador respostas para o telefone, mas, em seguida, desligou sem pressionar qualquer botões. |
| FAILED_INVALID_EXTENSION | Extensão inválida | A extensão contém carateres inválidos.  Apenas dígitos, vírgulas, *, # é permitida.  Um prefixo @ também podem ser utilizadas. |
| FAILED_FRAUD_CODE_ENTERED | Código fraudulento introduzido | O utilizador optou por reportar fraude durante a chamada resulta numa autenticação negada e um número de telefone bloqueado.| 
| FAILED_SERVER_ERROR | Não é possível efetuar a chamada | O serviço de multi-factor Authentication não foi possível colocar a chamada. |
| FAILED_SMS_NOT_SENT | Não foi possível enviar a mensagem de texto | Não foi possível enviar a mensagem de texto.  A autenticação é negada. |
| FAILED_SMS_OTP_INCORRECT | Mensagem de texto OTP incorreta | O utilizador introduziu um código de acesso incorreto Monouso (OTP) da mensagem de texto que receberam.  A autenticação é negada. |
| FAILED_SMS_OTP_PIN_INCORRECT | Texto da mensagem OTP + PIN incorreto | O utilizador introduziu um código de acesso incorreto Monouso (OTP) e/ou um PIN de utilizador incorretas.  A autenticação é negada. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Foi excedido o texto máximo de tentativas OTP de mensagens | O utilizador excedeu o número máximo de tentativas de código Monouso (OTP). |
| FAILED_PHONE_APP_DENIED | Aplicação móvel negada | Negado ao utilizador a autenticação na aplicação móvel, premindo o botão de negação. |
| FAILED_PHONE_APP_INVALID_PIN | PIN de aplicação móvel inválido | O utilizador introduziu um PIN inválido durante a autenticação na aplicação móvel. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | Não foi alterado de PIN da aplicação móvel | O utilizador não foi concluída com êxito uma alteração do PIN necessária na aplicação móvel. |
| FAILED_FRAUD_REPORTED | Fraude reportada | O utilizador reportada fraude na aplicação móvel. |
| FAILED_PHONE_APP_NO_RESPONSE | Aplicação móvel sem resposta | O utilizador não respondeu ao pedido de autenticação de aplicação móvel. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Móvel de aplicação de todos os dispositivos bloqueados | Os dispositivos de aplicação móvel para este utilizador já não estão a responder às notificações e foram bloqueados. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Falha de notificação de aplicação móvel | Ocorreu uma falha durante a tentativa de enviar uma notificação na aplicação móvel no dispositivo do utilizador. |
| FAILED_PHONE_APP_INVALID_RESULT | Resultado da aplicação móvel inválido | A aplicação móvel devolveu um resultado inválido. |
| FAILED_OATH_CODE_INCORRECT | Código OATH incorreto | O utilizador introduziu um código OATH incorreto.  A autenticação é negada. |
| FAILED_OATH_CODE_PIN_INCORRECT | Código OATH + PIN incorreto | O utilizador introduziu um código OATH incorreto e/ou um PIN de utilizador incorretas.  A autenticação é negada. |
| FAILED_OATH_CODE_DUPLICATE | Código OATH duplicado | O utilizador introduziu um código OATH que foi utilizado anteriormente.  A autenticação é negada. |
| FAILED_OATH_CODE_OLD | Código OATH desatualizado | O utilizador introduziu um código OATH que precede um código OATH que foi utilizado anteriormente.  A autenticação é negada. |
| FAILED_OATH_TOKEN_TIMEOUT | Tempo limite do resultado de código OATH | O utilizador demorou demasiado tempo a introduzir o código OATH e a tentativa de multi-factor Authentication já tinha ultrapassou o tempo limite. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Tempo limite de resultado de perguntas de segurança | O utilizador demorou demasiado tempo a introduzir a responder a perguntas de segurança e a tentativa de multi-factor Authentication já tinha ultrapassou o tempo limite. |
| FAILED_AUTH_RESULT_TIMEOUT | Tempo limite do resultado de autenticação | O utilizador demorou demasiado tempo a concluir a tentativa de multi-factor Authentication. |
| FAILED_AUTHENTICATION_THROTTLED | Autenticação limitada | A tentativa de multi-factor Authentication foi limitada pelo serviço. |

## <a name="next-steps"></a>Passos Seguintes

* [Para os utilizadores](../user-help/multi-factor-authentication-end-user.md)
* [Onde pretende implementar](concept-mfa-whichversion.md)
