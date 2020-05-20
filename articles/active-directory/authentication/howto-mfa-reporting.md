---
title: Detalhes do evento de entrada para autenticação de multi-factors Azure - Diretório Ativo Azure
description: Saiba como visualizar a atividade de entrada para eventos de autenticação multi-factor Azure e mensagens de estado.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9bf76729c3b5844918659283a65eeb347c4237d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83639850"
---
# <a name="use-the-sign-ins-report-to-review-azure-multi-factor-authentication-events"></a>Utilize o relatório de inscrição para rever eventos de autenticação de multifactor do Azure

Para rever e compreender os eventos de autenticação multi-factor do Azure, pode utilizar o relatório de inscrições do Azure Ative Directory (Azure AD). Este relatório mostra detalhes de autenticação para eventos quando um utilizador é solicitado para autenticação multifactor, e se alguma política de Acesso Condicional estava em uso. Para obter informações detalhadas sobre o relatório de inscrição, consulte a visão geral dos relatórios de atividade de [entrada em Azure AD](../reports-monitoring/concept-sign-ins.md).

Este artigo mostra-lhe como ver o relatório de entrada de Anúncios Azure no portal Azure, e depois o módulo MSOnline V1 PowerShell.

## <a name="view-the-azure-ad-sign-ins-report"></a>Ver o relatório de inscrições da AD Azure

O relatório de entrada fornece-lhe informações sobre a utilização de aplicações geridas e atividades de entrada de utilizadores, que incluem informações sobre a utilização da autenticação multifactor (MFA). Os dados de MFA disponibilizam-lhe informações sobre como esta está a funcionar na sua organização. Permite-lhe responder a perguntas como as seguintes:

- O início de sessão foi submetido à MFA?
- Como é que o utilizador concluiu a MFA?
- Por que motivo é que o utilizador não conseguiu concluir a MFA?
- Quantos utilizadores são submetidos à MFA?
- Quantos utilizadores não conseguem concluir a submissão da MFA?
- Quais são os problemas mais comuns da MFA com que os utilizadores finais se deparam?

Para ver o relatório de atividade de entrada no [portal Azure,](https://portal.azure.com)preencha os seguintes passos. Também pode consultar dados através da [API reportando.](../reports-monitoring/concept-reporting-api.md)

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta com permissões *de administrador global.*
1. Procure e selecione **Azure Ative Directory,** em seguida, escolha **utilizadores** do menu do lado esquerdo.
1. No âmbito da *Atividade* do menu do lado esquerdo, selecione **Sign-ins**.
1. É apresentada uma lista de eventos de inscrição, incluindo o estado. Pode selecionar um evento para ver mais detalhes.

    Os *Detalhes de Autenticação* ou o separador *de Acesso Condicional* dos detalhes do evento mostram-lhe o código de estado ou qual a política que desencadeou o pedido do MFA.

    [![](media/howto-mfa-reporting/sign-in-report-cropped.png "Screenshot of example Azure Active Directory sign-ins report in the Azure portal")](media/howto-mfa-reporting/sign-in-report.png#lightbox)

Se disponível, a autenticação é mostrada, como mensagem de texto, notificação de aplicação Do Autenticador microsoft ou chamada telefónica.

Os seguintes detalhes são mostrados na janela *Autenticação Detalhes* para um caso de inscrição que mostre se o pedido de MFA foi satisfeito ou negado:

* Se a MFA for cumprida, esta coluna disponibiliza mais informações sobre como foi cumprida.
   * concluída na cloud
   * expirou devido a políticas configuradas no inquilino
   * registo concluído
   * cumprida por reclamação no token
   * cumprida por reclamação fornecida por fornecedor externo
   * cumprida por autenticação forte
   * ignorada porque o fluxo executado foi o fluxo de início de sessão de mediador do Windows
   * ignorada devido a palavra-passe de aplicação
   * ignorada devido a localização
   * ignorada devido a dispositivo registado
   * ignorada devido a dispositivo memorizado
   * concluída com êxito

* Se for recusada, a coluna mostrará o motivo da recusa.
   * autenticação em curso
   * tentativa de autenticação duplicada
   * código errado introduzido demasiadas vezes
   * autenticação inválida
   * código de verificação de aplicação móvel inválido
   * configuração incorreta
   * a chamada telefónica foi para o correio de voz
   * o formato do número de telefone é inválido
   * erro de serviço
   * incapaz de alcançar o telefone do utilizador
   * não é possível enviar a notificação de aplicação móvel para o dispositivo
   * não é possível enviar a notificação de aplicação móvel
   * o utilizador recusou a autenticação
   * o utilizador não respondeu à notificação de aplicação móvel
   * o utilizador não tem nenhum método de verificação registado
   * o utilizador introduziu um código incorreto
   * o utilizador introduziu um PIN incorreto
   * o utilizador desligou a chamada telefónica sem que a autenticação fosse bem sucedida
   * o utilizador está bloqueado
   * o utilizador nunca introduziu o código de verificação
   * o utilizador não foi encontrado
   * o código de verificação já foi utilizado uma vez

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>Relatórios PowerShell sobre utilizadores registados para MFA

Em primeiro lugar, certifique-se de que tem o [módulo MSOnline V1 PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) instalado.

Identifique os utilizadores que se registaram para o MFA utilizando o PowerShell que se segue. Este conjunto de comandos exclui utilizadores com deficiência, uma vez que estas contas não podem autenticar contra a AD Azure:

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods -ne $null -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Identifique os utilizadores que não se registaram no MFA utilizando o PowerShell que se segue. Este conjunto de comandos exclui utilizadores com deficiência, uma vez que estas contas não podem autenticar contra a AD Azure:

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods.Count -eq 0 -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Identificar utilizadores e métodos de saída registados:

```powershell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="downloaded-activity-reports-result-codes"></a>Códigos de resultados de relatórios de atividade descarregados

A tabela seguinte pode ajudar a resolver eventos usando a versão descarregada do relatório de atividade sada dos passos do portal anteriores ou comandos PowerShell. Estes códigos de resultados não aparecem diretamente no portal Azure.

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

## <a name="additional-mfa-reports"></a>Relatórios adicionais de MFA

As seguintes informações e relatórios adicionais estão disponíveis para eventos MFA, incluindo os do MFA Server:

| Relatório | Localização | Descrição |
|:--- |:--- |:--- |
| Histórico de utilizadores bloqueado | Azure AD > Segurança > MFA > bloquear/desbloquear utilizadores | Mostra o histórico de pedidos para bloquear ou desbloquear utilizadores. |
| Utilização de componentes no local | Relatório de atividade seletiva > > de segurança da AD Azure > | Fornece informações sobre o uso global do MFA Server através da extensão NPS, ADFS e MFA Server. |
| Histórico de utilizador ignorado | Azure AD > Security > MFA > bypass único | Fornece um histórico de pedidos do MFA Server para contornar o MFA para um utilizador. |
| Estado do servidor | Estado do servidor do > de segurança da AD Azure AD > M >FA | Apresenta o estado dos Servidores MFA associados à sua conta. |

## <a name="next-steps"></a>Passos seguintes

Este artigo forneceu uma visão geral do relatório de atividade de inscrição. Para obter informações mais detalhadas sobre o que este relatório contém e compreender os dados, consulte relatórios de atividade de [entrada em Azure AD](../reports-monitoring/concept-sign-ins.md).
