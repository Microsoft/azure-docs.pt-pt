---
title: Detalhes do evento de inscrição para autenticação multi-factor Azure AD - Diretório Ativo Azure
description: Saiba como ver a atividade de entrada para eventos de autenticação multi-factor Azure AD e mensagens de estado.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/15/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e2a02ae7bd89e99dc2eee013394a1f85139c1c00
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742781"
---
# <a name="use-the-sign-ins-report-to-review-azure-ad-multi-factor-authentication-events"></a>Utilize o relatório de inscrições para rever os eventos de autenticação multi-factor Azure AD

Para rever e compreender os eventos de autenticação multi-factor Azure AD, pode utilizar o relatório de inscrição do Azure Ative Directory (Azure AD). Este relatório mostra detalhes de autenticação para eventos quando um utilizador é solicitado para a autenticação de vários fatores, e se alguma política de Acesso Condicional estiver em uso. Para obter informações detalhadas sobre o relatório de inscrições, consulte a [visão geral dos relatórios de atividade de inscrição no Azure AD](../reports-monitoring/concept-sign-ins.md).

Este artigo mostra-lhe como ver o relatório de ins de sação Azure AD no portal Azure e, em seguida, o módulo MSOnline V1 PowerShell.

## <a name="view-the-azure-ad-sign-ins-report"></a>Ver o relatório de inscrições da Azure AD

O relatório de inscrições fornece-lhe informações sobre o uso de aplicações geridas e atividades de inscrição do utilizador, que inclui informações sobre a utilização de autenticação multi-fatores (MFA). Os dados de MFA disponibilizam-lhe informações sobre como esta está a funcionar na sua organização. Permite-lhe responder a perguntas como:

- O início de sessão foi submetido à MFA?
- Como é que o utilizador concluiu a MFA?
- Por que motivo é que o utilizador não conseguiu concluir a MFA?
- Quantos utilizadores são submetidos à MFA?
- Quantos utilizadores não conseguem concluir a submissão da MFA?
- Quais são os problemas mais comuns da MFA com que os utilizadores finais se deparam?

Para visualizar o relatório de atividades de inscrição no [portal Azure,](https://portal.azure.com)complete os seguintes passos. Também pode consultar dados utilizando a [API de reporte.](../reports-monitoring/concept-reporting-api.md)

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta com permissões *de administrador global.*
1. Procure e selecione **Azure Ative Directory,** em seguida, escolha **Utilizadores** no menu do lado esquerdo.
1. Em *Atividade* a partir do menu do lado esquerdo, selecione **Iniciar s-ins**.
1. É apresentada uma lista de eventos de inscrição, incluindo o estado. Pode selecionar um evento para ver mais detalhes.

    O *separador De Dados* de Autenticação ou *de Acesso Condicional* dos dados do evento mostra-lhe o código de estado ou qual a política que desencadeou o pedido de MFA.

    [![Screenshot do exemplo Azure Ative Directory relatório de ins- ins no portal Azure](media/howto-mfa-reporting/sign-in-report-cropped.png)](media/howto-mfa-reporting/sign-in-report.png#lightbox)

Se disponível, a autenticação é mostrada, como mensagem de texto, notificação de aplicação do Microsoft Authenticator ou chamada telefónica.

Os seguintes detalhes são mostrados na janela Detalhes de *Autenticação* para um evento de entrada que mostra se o pedido de MFA foi satisfeito ou negado:

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
   * incapaz de chegar ao telefone do utilizador
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

Em primeiro lugar, certifique-se de que tem o [módulo MSOnline V1 PowerShell](/powershell/azure/active-directory/overview?view=azureadps-1.0) instalado.

Identifique os utilizadores que se registaram para MFA usando o PowerShell que se segue. Este conjunto de comandos exclui os utilizadores com deficiência, uma vez que estas contas não podem autenticar-se contra a Azure AD:

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods -ne $null -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Identifique os utilizadores que não se registaram para MFA utilizando o PowerShell que se segue. Este conjunto de comandos exclui os utilizadores com deficiência, uma vez que estas contas não podem autenticar-se contra a Azure AD:

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods.Count -eq 0 -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Identificar utilizadores e métodos de saída registados:

```powershell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="downloaded-activity-reports-result-codes"></a>Relatórios de atividades descarregados códigos de resultados

A tabela a seguir pode ajudar a resolver eventos utilizando a versão descarregada do relatório de atividades a partir dos passos do portal anterior ou comandos PowerShell. Estes códigos de resultados não aparecem diretamente no portal Azure.

| Resultado de chamada | Description | Descrição ampla |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PIN introduzido | O utilizador introduziu um PIN.  Se a autenticação tiver sido bem sucedida, entraram no PIN correto.  Se a autenticação for negada, inseriram um PIN incorreto ou o utilizador está definido para o modo Standard. |
| SUCCESS_NO_PIN | Apenas # Inscrito | Se o utilizador estiver definido para o modo PIN e a autenticação for negada, isto significa que o utilizador não introduziu o pin e só entrou em #.  Se o utilizador estiver definido para o modo Standard e a autenticação tiver sucesso, isto significa que o utilizador só entrou # que é a coisa certa a fazer no modo Standard. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Não pressionado após a entrada | O utilizador não enviou nenhum dígito DTMF uma vez que # não foi introduzido.  Outros dígitos introduzidos não são enviados a menos que # seja introduzido indicando a conclusão da entrada. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Sem entrada de telefone - out cronometrado | A chamada foi atendida, mas não houve resposta.  Isto normalmente indica que a chamada foi captada por correio de voz. |
| SUCCESS_PIN_EXPIRED | PIN expirado e não alterado | O PIN do utilizador expirou e foi solicitado a alterá-lo, mas a alteração PIN não foi concluída com sucesso. |
| SUCCESS_USED_CACHE | Cache usado | A autenticação foi conseguida sem uma chamada de autenticação multi-factor, uma vez que ocorreu uma autenticação com sucesso anterior para o mesmo nome de utilizador dentro do prazo de cache configurado. |
| SUCCESS_BYPASSED_AUTH | Auth contornado | A autenticação conseguiu utilizar um Bypass One-Time iniciado para o utilizador.  Consulte o Relatório de Histórico do Utilizador Contornado para obter mais detalhes sobre o bypass. |
| SUCCESS_USED_IP_BASED_CACHE | Cache usado baseado em IP | A autenticação foi conseguida sem uma chamada de autenticação multi-factor, uma vez que uma autenticação com sucesso anterior para o mesmo nome de utilizador, tipo de autenticação, nome de aplicação e IP ocorreu dentro do prazo de cache configurado. |
| SUCCESS_USED_APP_BASED_CACHE | Cache usado baseado em aplicativos | A autenticação foi conseguida sem uma chamada de autenticação multi-factor, uma vez que uma autenticação com sucesso anterior para o mesmo nome de utilizador, tipo de autenticação e nome de aplicação dentro do prazo de cache configurado. |
| SUCCESS_INVALID_INPUT | Entrada de telefone inválida | A resposta enviada do telefone não é válida.  Isto pode ser de um fax ou modem ou o utilizador pode ter introduzido * como parte do pin. |
| SUCCESS_USER_BLOCKED | O utilizador está bloqueado | O número de telefone do utilizador está bloqueado.  Um número bloqueado pode ser iniciado pelo utilizador durante uma chamada de autenticação ou por um administrador utilizando o portal Azure. <br> NOTA: Um número bloqueado é também um subproduto de um Alerta de Fraude. |
| SUCCESS_SMS_AUTHENTICATED | Mensagem de texto autenticada | Para uma mensagem de teste bidirecional, o utilizador respondeu corretamente com a sua senha única (OTP) ou OTP + PIN. |
| SUCCESS_SMS_SENT | Mensagem de texto enviada | Para mensagem de texto, a mensagem de texto contendo a senha única (OTP) foi enviada com sucesso.  O utilizador introduzirá o OTP ou OTP + PIN na aplicação para completar a autenticação. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Aplicativo Móvel Autenticado | O utilizador foi autenticado com sucesso através da aplicação móvel. |
| SUCCESS_OATH_CODE_PENDING | Código do OATH pendente | O utilizador foi solicitado para o seu código de OATH, mas não respondeu. |
| SUCCESS_OATH_CODE_VERIFIED | Código do OATH Verificado | O utilizador introduziu um código OATH válido quando solicitado. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Código de OATH de retorno verificado | Foi negada a autenticação do utilizador utilizando o seu método primário de autenticação multi-factor e, em seguida, forneceu um código OATH válido para recuo. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Perguntas de segurança de recuo respondidas | Foi negada a autenticação do utilizador utilizando o seu método primário de autenticação multi-factor e, em seguida, respondeu corretamente às suas questões de segurança para recuo. |
| FAILED_PHONE_BUSY | Auth já em curso | A Autenticação Multi-Factor já está a processar uma autenticação para este utilizador.  Isto é frequentemente causado por clientes RADIUS que enviam vários pedidos de autenticação durante a mesma entrada. |
| CONFIG_ISSUE | Telefone Inacessível | A chamada foi tentada, mas ou não pôde ser colocada ou não foi respondida.  Isto inclui sinal ocupado, sinal de ocupado rápido (desligado), tri-tons (número já não em serviço), tempo para fora durante o toque, etc. |
| FAILED_INVALID_PHONENUMBER | Formato de número de telefone inválido | O número de telefone tem um formato inválido.  Os números de telefone devem ser numéricos e devem ser de 10 dígitos para o código de país +1 (Estados Unidos & Canadá). |
| FAILED_USER_HUNGUP_ON_US | Utilizador desligou o telefone | O utilizador atendeu o telefone, mas desligou-o sem carregar em nenhum botão. |
| FAILED_INVALID_EXTENSION | Extensão inválida | A extensão contém caracteres inválidos.  Apenas dígitos, vírgulas, *, e # são permitidos.  Um prefixo @ também pode ser usado. |
| FAILED_FRAUD_CODE_ENTERED | Código de Fraude introduzido | O utilizador decidiu denunciar fraude durante a chamada, resultando numa autenticação negada e num número de telefone bloqueado.| 
| FAILED_SERVER_ERROR | Incapaz de colocar chamada | O serviço de autenticação multi-factor não foi capaz de fazer a chamada. |
| FAILED_SMS_NOT_SENT | Mensagem de texto não podia ser enviada | A mensagem de texto não pôde ser enviada.  A autenticação é negada. |
| FAILED_SMS_OTP_INCORRECT | OTP de mensagem de texto incorreto | O utilizador introduziu uma senha única (OTP) incorreta a partir da mensagem de texto que recebeu.  A autenticação é negada. |
| FAILED_SMS_OTP_PIN_INCORRECT | OTP de mensagem de texto + PIN Incorrect | O utilizador introduziu uma senha única incorreta (OTP) e/ou um PIN do utilizador incorreto.  A autenticação é negada. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Tentativas de OTP de mensagem máxima excedidas | O utilizador excedeu o número máximo de tentativas de senha única (OTP). |
| FAILED_PHONE_APP_DENIED | Aplicação móvel negada | O utilizador negou a autenticação na aplicação móvel ao premir o botão Deny. |
| FAILED_PHONE_APP_INVALID_PIN | APP Móvel INVálido PIN | O utilizador introduziu um PIN inválido ao autenticar na aplicação móvel. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | PIN de aplicativo móvel não alterado | O utilizador não completou com sucesso uma alteração PIN necessária na aplicação móvel. |
| FAILED_FRAUD_REPORTED | Fraude Reportada | O utilizador relatou fraude na aplicação móvel. |
| FAILED_PHONE_APP_NO_RESPONSE | Aplicação móvel Sem Resposta | O utilizador não respondeu ao pedido de autenticação da aplicação móvel. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Aplicação móvel Todos os dispositivos bloqueados | Os dispositivos de aplicação móvel para este utilizador já não respondem às notificações e foram bloqueados. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Notificação de aplicativo móvel falhou | Ocorreu uma falha ao tentar enviar uma notificação para a aplicação móvel no dispositivo do utilizador. |
| FAILED_PHONE_APP_INVALID_RESULT | Resultado inválido da aplicação móvel | A aplicação móvel devolveu um resultado inválido. |
| FAILED_OATH_CODE_INCORRECT | Código OATH Incorreto | O utilizador introduziu um código OATH incorreto.  A autenticação é negada. |
| FAILED_OATH_CODE_PIN_INCORRECT | Código do Juramento + PIN Incorrect | O utilizador introduziu um código OATH incorreto e/ou um PIN do utilizador incorreto.  A autenticação é negada. |
| FAILED_OATH_CODE_DUPLICATE | Código de OATH duplicado | O utilizador introduziu um código OATH que foi previamente utilizado.  A autenticação é negada. |
| FAILED_OATH_CODE_OLD | Código do OATH fora de data | O utilizador introduziu um código OATH que antecede um código OATH que foi previamente utilizado.  A autenticação é negada. |
| FAILED_OATH_TOKEN_TIMEOUT | Tempo limite de resultados do código do OATH | O utilizador demorou muito tempo a introduzir o código OATH e a tentativa de autenticação multi-factor já tinha terminado. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Perguntas de segurança Resultado Tempo limite | O utilizador demorou muito tempo a responder a questões de segurança e a tentativa de autenticação multi-factor já tinha terminado. |
| FAILED_AUTH_RESULT_TIMEOUT | Intervalo de tempo do resultado de Auth | O utilizador demorou demasiado tempo a concluir a tentativa de autenticação multi-factor. |
| FAILED_AUTHENTICATION_THROTTLED | Autenticação Acelerada | A tentativa de autenticação multi-factor foi estrangulada pelo serviço. |

## <a name="additional-mfa-reports"></a>Relatórios adicionais do MFA

Estão disponíveis as seguintes informações e relatórios adicionais para eventos MFA, incluindo os do MFA Server:

| Relatório | Localização | Description |
|:--- |:--- |:--- |
| Histórico de utilizadores bloqueados | Azure AD > Security > MFA > utilizadores de bloco/desbloqueio | Mostra o histórico de pedidos para bloquear ou desbloquear utilizadores. |
| Utilização de componentes no local | Relatório de atividade > de segurança > Azure AD > > MFA | Fornece informações sobre o uso geral para o MFA Server através da extensão NPS, ADFS e MFA Server. |
| Histórico de utilizadores contornados | Azure AD > Segurança > MFA > bypass único | Fornece um histórico de pedidos do MFA Server para contornar o MFA para um utilizador. |
| Estado do servidor | Azure AD > Segurança > Estado do Servidor > MFA | Apresenta o estado dos Servidores MFA associados à sua conta. |

## <a name="next-steps"></a>Passos seguintes

Este artigo forneceu uma visão geral do relatório de atividades de inscrição. Para obter informações mais detalhadas sobre o que este relatório contém e compreender os dados, consulte [relatórios de atividade de inscrição no Azure AD](../reports-monitoring/concept-sign-ins.md).
