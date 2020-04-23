---
title: Relatórios de acesso e utilização para Azure MFA - Diretório Ativo Azure
description: Isto descreve como usar a funcionalidade de autenticação multi-factor Azure - relatórios.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/30/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8665e58dc84a2ea9b5f2ed70a41bbdd0aa3aa9b
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82099160"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Relatórios na autenticação multi-factor azure

A Autenticação Multi-Factor Azure fornece vários relatórios que podem ser utilizados por si e pela sua organização acessíveis através do portal Azure. A tabela seguinte enumera os relatórios disponíveis:

| Relatório | Localização | Descrição |
|:--- |:--- |:--- |
| Histórico de utilizadores bloqueado | Azure AD > Segurança > MFA > bloquear/desbloquear utilizadores | Mostra o histórico de pedidos para bloquear ou desbloquear utilizadores. |
| Alertas de utilização e fraude | Azure AD > Sign-ins | Fornece informações sobre a utilização global, resumo do utilizador e detalhes do utilizador; bem como um histórico de alertas de fraude apresentados durante o intervalo de data especificado. |
| Utilização de componentes no local | Relatório de atividade seletiva > > de segurança da AD Azure > | Fornece informações sobre o uso global para MFA através da extensão NPS, ADFS e servidor MFA. |
| Histórico de utilizador ignorado | Azure AD > Security > MFA > bypass único | Fornece um histórico de pedidos para contornar a autenticação multi-factor para um utilizador. |
| Estado do servidor | Estado do servidor do > de segurança da AD Azure AD > M >FA | Apresenta o estado dos Servidores de Autenticação Multi-Factor associados à sua conta. |

## <a name="view-mfa-reports"></a>Ver relatórios de MFA

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Ative Directory** > **Security** > **MFA**.
3. Selecione o relatório que pretende ver.

   ![Relatório de estado do servidor do Servidor MFA no portal Azure](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Relatório de inscrições da AD Azure

Com o relatório de atividade sintetizador no [portal Azure,](https://portal.azure.com)pode obter a informação necessária para determinar como está o seu ambiente. **sign-ins activity report**

O relatório de entrada pode fornecer-lhe informações sobre a utilização de aplicações geridas e atividades de entrada de utilizadores, que incluem informações sobre a utilização da autenticação multifactor (MFA). Os dados de MFA disponibilizam-lhe informações sobre como esta está a funcionar na sua organização. Permite-lhe responder a perguntas como:

- O início de sessão foi submetido à MFA?
- Como é que o utilizador concluiu a MFA?
- Por que motivo é que o utilizador não conseguiu concluir a MFA?
- Quantos utilizadores são submetidos à MFA?
- Quantos utilizadores não conseguem concluir a submissão da MFA?
- Quais são os problemas mais comuns da MFA com que os utilizadores finais se deparam?

Estes dados estão disponíveis através do [portal Azure](https://portal.azure.com) e da [API reportando.](../reports-monitoring/concept-reporting-api.md)

![Relatório de inscrições da AD Azure no portal Azure](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Estrutura de relatório de insadiário

Os relatórios de atividades de início de sessão para a MFA dão-lhe acesso às informações seguintes:

**MFA necessária:** se a MFA é necessária para o início de sessão ou não. O MFA pode ser exigido devido a MFA por utilizador, Acesso Condicional ou outras razões. Os valores possíveis são **Sim** ou **Não.**

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
      - incapaz de alcançar o telefone do utilizador
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

**Método de autenticação da MFA:** o método de autenticação que o utilizador utilizou para concluir a MFA. Valores possíveis incluem:

- Mensagem de texto
- Notificação de aplicação móvel
- Chamada telefónica (telefone de autenticação)
- Código de verificação de aplicação móvel
- Chamada telefónica (telefone do escritório)
- Chamada telefónica (telefone de autenticação alternativo)

**Detalhe de autenticação da MFA:** versão limpa do número de telefone, por exemplo, + X XXXXXXXX64.

**Acesso Condicional** Encontre informações sobre as políticas de acesso condicional que afetaram a tentativa de inscrição, incluindo:

- Nome da política
- Controlos de subvenções
- Controlos de sessão
- Resultado

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>Relatórios PowerShell sobre utilizadores registados para MFA

Em primeiro lugar, certifique-se de que tem o [módulo MSOnline V1 PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) instalado.

Identifique os utilizadores que se registaram para o MFA utilizando o PowerShell que se segue. Este conjunto de comandos exclui utilizadores com deficiência, uma vez que estas contas não podem autenticar contra a AD Azure.

```powershell
Get-MsolUser -All | Where-Object {$._StrongAuthenticationMethods -ne $null -and $._BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Identifique os utilizadores que não se registaram no MFA utilizando o PowerShell que se segue. Este conjunto de comandos exclui utilizadores com deficiência, uma vez que estas contas não podem autenticar contra a AD Azure.

```powershell
Get-MsolUser -All | Where-Object {$._StrongAuthenticationMethods.Count -eq 0 -and $._BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Identificar utilizadores e métodos de saída registados. 

```powershell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="possible-results-in-activity-reports"></a>Possíveis resultados em relatórios de atividade

A tabela a seguir pode ser utilizada para resolver problemas de autenticação de vários fatores utilizando a versão descarregada do relatório de atividade de autenticação de vários fatores. Não aparecerão diretamente no portal Azure.

| Resultado da chamada | Descrição | Descrição ampla |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PIN Introduzido | O utilizador introduziu um PIN. Se a autenticação tiver sido bem sucedida, entraram no PIN correto. Se a autenticação for negada, introduziu um PIN incorreto ou o utilizador está definido para o modo Standard. |
| SUCCESS_NO_PIN | Apenas # Entrou | Se o utilizador estiver definido para o modo PIN e a autenticação for negada, isto significa que o utilizador não introduziu o seu PIN e apenas introduziu #.  Se o utilizador estiver definido para o modo Standard e a autenticação for bem sucedida, isto significa que o utilizador apenas entrou # o que é a coisa certa a fazer no modo Standard. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Não pressionado após a entrada | O utilizador não enviou nenhum dígito dTMF uma vez que # não foi introduzido. Outros dígitos introduzidos não são enviados a menos que # seja introduzido indicando a conclusão da entrada. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Sem entrada de telefone - Timed out | A chamada foi atendida, mas não houve resposta. Isto normalmente indica que a chamada foi captada por correio de voz. |
| SUCCESS_PIN_EXPIRED | PIN expirado e não alterado | O PIN do utilizador está caducado e foi-lhes solicitado que o alterasse, mas a alteração PIN não foi concluída com sucesso. |
| SUCCESS_USED_CACHE | Cache usado | A autenticação foi bem sucedida sem uma chamada de autenticação multi-factor, uma vez que uma autenticação anterior e bem sucedida para o mesmo nome de utilizador ocorreu dentro do prazo de cache configurado. |
| SUCCESS_BYPASSED_AUTH | Auth ignorado | A autenticação conseguiu utilizar um Bypass One-Time iniciado para o utilizador. Consulte o Relatório de História do Utilizador ignorado para obter mais detalhes sobre o bypass. |
| SUCCESS_USED_IP_BASED_CACHE | Cache usado baseado em IP | A autenticação foi bem sucedida sem uma chamada de autenticação multi-factor, uma vez que uma autenticação anterior e bem sucedida para o mesmo nome de utilizador, tipo de autenticação, nome da aplicação e IP ocorreu dentro do prazo de cache configurado. |
| SUCCESS_USED_APP_BASED_CACHE | Cache baseado em aplicativos | A autenticação foi bem sucedida sem uma chamada de autenticação multi-factor desde que uma autenticação anterior e bem sucedida para o mesmo nome de utilizador, tipo de autenticação e nome de aplicação dentro do prazo de cache configurado. |
| SUCCESS_INVALID_INPUT | Entrada de telefone inválida | A resposta enviada pelo telefone não é válida. Isto pode ser de um fax ou modem ou o utilizador pode ter entrado * como parte do seu PIN. |
| SUCCESS_USER_BLOCKED | O utilizador está bloqueado | O número de telefone do utilizador está bloqueado. Um número bloqueado pode ser iniciado pelo utilizador durante uma chamada de autenticação ou por um administrador que utilize o portal Azure. <br> NOTA: Um número bloqueado é também um subproduto de um Alerta de Fraude. |
| SUCCESS_SMS_AUTHENTICATED | Mensagem de texto autenticada | Para uma mensagem de teste bidirecional, o utilizador respondeu corretamente com a sua senha única (OTP) ou OTP + PIN. |
| SUCCESS_SMS_SENT | Mensagem de texto enviada | Para a Mensagem de Texto, a mensagem de texto contendo a senha única (OTP) foi enviada com sucesso. O utilizador introduzirá o OTP ou OTP + PIN na aplicação para completar a autenticação. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Aplicação móvel autenticada | O utilizador autenticou com sucesso através da aplicação móvel. |
| SUCCESS_OATH_CODE_PENDING | Código OATH pendente | O utilizador foi solicitado para o seu código OATH, mas não respondeu. |
| SUCCESS_OATH_CODE_VERIFIED | Código OATH Verificado | O utilizador introduziu um código OATH válido quando solicitado. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Código DO JURAMENTO de recuo verificado | O utilizador foi negado autenticação utilizando o seu método primário de autenticação multi-factor e, em seguida, forneceu um código OATH válido para recuo. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Perguntas de Segurança de Fallback Respondidas | O utilizador foi negado autenticação utilizando o seu método primário de autenticação multi-factor e, em seguida, respondeu corretamente às suas questões de segurança para recuo. |
| FAILED_PHONE_BUSY | Auth já em progresso | A Autenticação Multi-Factor já está a processar uma autenticação para este utilizador. Isto é frequentemente causado por clientes RADIUS que enviam múltiplos pedidos de autenticação durante o mesmo sign-on. |
| CONFIG_ISSUE | Telefone Incontactável | A chamada foi tentada, mas ou não podia ser colocada ou não foi respondida. Isto inclui sinal ocupado, sinal de rápida atarefada (desligado), três tons (número já não em serviço), cronometrado durante o toque, etc. |
| FAILED_INVALID_PHONENUMBER | Formato de número de telefone inválido | O número de telefone tem um formato inválido. Os números de telefone devem ser numéricos e devem ser de 10 dígitos para o código do país +1 (Estados Unidos & Canadá). |
| FAILED_USER_HUNGUP_ON_US | Utilizador pendurou o telefone | O utilizador atendeu o telefone, mas depois desligou sem carregar em botões. |
| FAILED_INVALID_EXTENSION | Extensão inválida | A extensão contém caracteres inválidos. Apenas dígitos, vírgulas, *, e # são permitidos. Um prefixo @ também pode ser usado. |
| FAILED_FRAUD_CODE_ENTERED | Código de Fraude Introduzido | O utilizador optou por denunciar fraude durante a chamada, resultando numa autenticação negada e num número de telefone bloqueado.| 
| FAILED_SERVER_ERROR | Incapaz de colocar chamada | O serviço de autenticação multi-factor não pôde fazer a chamada. |
| FAILED_SMS_NOT_SENT | Mensagem de texto não podia ser enviada | A mensagem de texto não pôde ser enviada. A autenticação é negada. |
| FAILED_SMS_OTP_INCORRECT | Mensagem de texto OTP Incorreta | O utilizador introduziu uma senha única incorreta (OTP) a partir da mensagem de texto que recebeu. A autenticação é negada. |
| FAILED_SMS_OTP_PIN_INCORRECT | Mensagem de texto OTP + PIN Incorreto | O utilizador introduziu uma senha única incorreta (OTP) e/ou um PIN de utilizador incorreto. A autenticação é negada. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Tentativas de Mensagem de Texto Máxima SmT | O utilizador ultrapassou o número máximo de tentativas de senha única (OTP). |
| FAILED_PHONE_APP_DENIED | Aplicação móvel negada | O utilizador negou a autenticação na aplicação móvel premindo o botão Deny. |
| FAILED_PHONE_APP_INVALID_PIN | Pin inválido da aplicação móvel | O utilizador introduziu um PIN inválido ao autenticar na aplicação móvel. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | PIN de aplicativo móvel não alterado | O utilizador não concluiu com sucesso uma alteração PIN necessária na aplicação móvel. |
| FAILED_FRAUD_REPORTED | Fraude reportada | O utilizador relatou fraude na aplicação móvel. |
| FAILED_PHONE_APP_NO_RESPONSE | Aplicação móvel sem resposta | O utilizador não respondeu ao pedido de autenticação da aplicação móvel. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Aplicativo móvel todos os dispositivos bloqueados | Os dispositivos de aplicação móvel para este utilizador já não respondem às notificações e foram bloqueados. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Notificação de aplicativo móvel falhou | Uma falha ocorreu ao tentar enviar uma notificação para a aplicação móvel no dispositivo do utilizador. |
| FAILED_PHONE_APP_INVALID_RESULT | Resultado inválido da aplicação móvel | A aplicação móvel devolveu um resultado inválido. |
| FAILED_OATH_CODE_INCORRECT | Código OATH Incorreto | O utilizador introduziu um código OATH incorreto.  A autenticação é negada. |
| FAILED_OATH_CODE_PIN_INCORRECT | Código OATH + PIN Incorreto | O utilizador introduziu um código OATH incorreto e/ou um PIN de utilizador incorreto.  A autenticação é negada. |
| FAILED_OATH_CODE_DUPLICATE | Duplicar código DO JURAMENTO | O utilizador introduziu um código OATH que foi previamente utilizado.  A autenticação é negada. |
| FAILED_OATH_CODE_OLD | Código OATH Desatualizado | O utilizador introduziu um código OATH que precede um código OATH que foi previamente utilizado.  A autenticação é negada. |
| FAILED_OATH_TOKEN_TIMEOUT | Tempo de tempo de saída do resultado do código do JURAMENTO | O utilizador demorou demasiado tempo a introduzir o código OATH e a tentativa de autenticação multi-factor já tinha esgotado. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Tempo de tempo de perguntas de segurança | O utilizador demorou demasiado tempo a introduzir resposta a questões de segurança e a tentativa de autenticação multi-factor já tinha esgotado. |
| FAILED_AUTH_RESULT_TIMEOUT | Timeout do resultado de Auth | O utilizador demorou muito tempo a completar a tentativa de autenticação multi-factor. |
| FAILED_AUTHENTICATION_THROTTLED | Autenticação Acelerada | A tentativa de autenticação multi-factor foi acelerada pelo serviço. |

## <a name="next-steps"></a>Passos seguintes

* [SSPR e Relatórios de Informação sobre o Uso de SSPR e MFA](howto-authentication-methods-usage-insights.md)
* [Para utilizadores](../user-help/multi-factor-authentication-end-user.md)
* [Onde implantar](concept-mfa-whichversion.md)
