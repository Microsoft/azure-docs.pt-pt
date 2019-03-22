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
ms.openlocfilehash: 7dd26bbb06310439aae065349c14c6df1c1c12e6
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316937"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Relatórios no Azure multi-factor Authentication

O Azure multi-factor Authentication fornece diversos relatórios que podem ser utilizados por e sua organização acessível através do portal do Azure. A tabela seguinte lista os relatórios disponíveis:

| Relatório | Localização | Descrição |
|:--- |:--- |:--- |
| Histórico de Utilizador Bloqueado | O Azure AD > servidor MFA > bloquear/desbloquear utilizadores | Mostra o histórico de pedidos para bloquear ou desbloquear utilizadores. |
| Alertas de fraude e de utilização | O Azure AD > inícios de sessão | Fornece informações sobre a utilização global, resumo por utilizador e detalhes de utilizador como um histórico de alertas de fraude apresentados durante o intervalo de datas especificado. |
| Utilização de componentes no local | O Azure AD > servidor MFA > relatório de atividade | Fornece informações sobre a utilização global da MFA através da extensão NPS, AD FS e o servidor MFA. |
| Histórico de Utilizador Ignorado | O Azure AD > servidor MFA > omissão de uso individual | Fornece um histórico de pedidos para ignorar o multi-factor Authentication para um utilizador. |
| Estado do servidor | O Azure AD > servidor MFA > Estado do servidor | Apresenta que o estado dos servidores multi-factor Authentication associado à sua conta. |

## <a name="view-mfa-reports"></a>Ver relatórios de MFA

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No lado esquerdo, selecione **do Azure Active Directory** > **servidor MFA**.
3. Selecione o relatório que pretende ver.

   <center>
   
   ![Cloud](./media/howto-mfa-reporting/report.png)</center>

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

![Nuvem](./media/howto-mfa-reporting/sign-in-report.png)

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

## <a name="powershell-reporting"></a>Relatórios do PowerShell

Identifique os utilizadores que foram registrados para MFA com o PowerShell que se segue.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identificar os utilizadores que não se registou para a MFA com o PowerShell que se segue.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>Passos Seguintes

* [Para os utilizadores](../user-help/multi-factor-authentication-end-user.md)
* [Onde pretende implementar](concept-mfa-whichversion.md)
