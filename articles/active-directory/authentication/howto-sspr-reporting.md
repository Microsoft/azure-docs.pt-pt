---
title: Relatórios de redefinição de senha de autoatendimento-Azure Active Directory
description: Relatórios sobre eventos de redefinição de senha de autoatendimento do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3b05c45d218c0f85817b4b5023ac22401879f81
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847223"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Opções de relatório para o gerenciamento de senhas do Azure AD

Após a implantação, muitas organizações desejam saber como ou se a redefinição de senha de autoatendimento (SSPR) está realmente sendo usada. O recurso de relatório que o Azure Active Directory (Azure AD) fornece ajuda a responder perguntas usando relatórios predefinidos. Se você estiver licenciado adequadamente, também poderá criar consultas personalizadas.

![Relatórios sobre SSPR usando os logs de auditoria no Azure AD][Reporting]

As perguntas a seguir podem ser respondidas pelos relatórios que existem no [portal do Azure](https://portal.azure.com/):

> [!NOTE]
> Você deve ser [um administrador global](../users-groups-roles/directory-assign-admin-roles.md)e deve aceitar que esses dados sejam coletados em nome da sua organização. Para aceitar, você deve visitar a guia **relatório** ou os logs de auditoria pelo menos uma vez. Até lá, os dados não são coletados para sua organização.
>

* Quantas pessoas se registraram para redefinição de senha?
* Quem se registrou para redefinição de senha?
* Quais dados as pessoas estão registrando?
* Quantas pessoas redefiniram suas senhas nos últimos sete dias?
* Quais são os métodos mais comuns que os usuários ou administradores usam para redefinir suas senhas?
* Quais são os problemas comuns que os usuários ou administradores enfrentam ao tentar usar a redefinição de senha?
* Quais administradores estão redefinindo suas próprias senhas com frequência?
* Há alguma atividade suspeita acontecendo com a redefinição de senha?

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Como exibir relatórios de gerenciamento de senhas no portal do Azure

Na experiência de portal do Azure, melhoramos a maneira como você pode exibir a redefinição de senha e a atividade de registro de redefinição de senha. Use as etapas a seguir para localizar os eventos de redefinição de senha e registro de redefinição de senha:

1. Navegue para o [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no painel esquerdo.
3. Pesquise **Azure Active Directory** na lista de serviços e selecione-o.
4. Selecione **usuários** na seção Gerenciar.
5. Selecione **logs de auditoria** na folha **usuários** . Isso mostra todos os eventos de auditoria que ocorreram em relação a todos os usuários em seu diretório. Você pode filtrar essa exibição para ver todos os eventos relacionados à senha.
6. No menu de **filtro** na parte superior do painel, selecione a lista suspensa **serviço** e altere-a para o tipo de serviço de gerenciamento de **senha de autoatendimento** .
7. Opcionalmente, filtre ainda mais a lista escolhendo a **atividade** específica em que você está interessado.

### <a name="converged-registration-preview"></a>Registro convergido (versão prévia)

Se você estiver participando da visualização pública do registro convergido, as informações sobre a atividade do usuário nos logs de auditoria serão encontradas nos **métodos de autenticação**do serviço.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Descrição das colunas do relatório no portal do Azure

A lista a seguir explica cada uma das colunas do relatório no portal do Azure em detalhes:

* **Usuário**: o usuário que tentou uma operação de registro de redefinição de senha.
* **Função**: a função do usuário no diretório.
* **Data e hora**: a data e a hora da tentativa.
* **Dados registrados**: os dados de autenticação que o usuário forneceu durante o registro de redefinição de senha.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Descrição dos valores do relatório no portal do Azure

A tabela a seguir descreve os diferentes valores que você pode definir para cada coluna no portal do Azure:

| Column | Valores permitidos e seus significados |
| --- | --- |
| Dados registrados |**Email alternativo**: o usuário usou um email de autenticação ou email alternativo para autenticar.<p><p>**Telefone comercial**: o usuário usou um telefone comercial para se autenticar.<p>**Celular: o**usuário usou um telefone celular ou um telefone de autenticação para se autenticar.<p>**Perguntas de segurança**: as perguntas de segurança usadas pelo usuário para autenticação.<p>**Qualquer combinação dos métodos anteriores, por exemplo, email alternativo + telefone celular**: ocorre quando uma política de duas portas é especificada e mostra quais dois métodos o usuário usou para autenticar sua solicitação de redefinição de senha. |

## <a name="self-service-password-management-activity-types"></a>Tipos de atividade de gerenciamento de senhas de autoatendimento

Os seguintes tipos de atividade aparecem na categoria de evento de auditoria de **Gerenciamento de senhas de autoatendimento** :

* [Bloqueado da redefinição de senha de autoatendimento](#activity-type-blocked-from-self-service-password-reset): indica que um usuário tentou redefinir uma senha, usar uma porta específica ou validar um número de telefone mais de cinco vezes no total em 24 horas.
* [Alterar senha (autoatendimento)](#activity-type-change-password-self-service): indica que um usuário realizou uma alteração de senha voluntária ou forçada (devido à expiração).
* [Redefinir senha (por administrador)](#activity-type-reset-password-by-admin): indica que um administrador executou uma redefinição de senha em nome de um usuário da portal do Azure.
* [Redefinir senha (autoatendimento)](#activity-type-reset-password-self-service): indica que um usuário redefiniu sua senha com êxito no [portal de redefinição de senha do Azure ad](https://passwordreset.microsoftonline.com).
* [Progresso da atividade de fluxo de redefinição de senha de autoatendimento](#activity-type-self-serve-password-reset-flow-activity-progress): indica cada etapa específica que um usuário passa, como passar um portão de autenticação de redefinição de senha específico, como parte do processo de redefinição de senha.
* [Desbloquear conta de usuário (autoatendimento)](#activity-type-unlock-a-user-account-self-service)): indica que um usuário desbloqueou com êxito sua conta de Active Directory sem redefinir a senha no [portal de redefinição de senha do Azure ad](https://passwordreset.microsoftonline.com) usando o recurso Active Directory de desbloqueio de conta sem redefinição.
* [Usuário registrado para redefinição de senha de autoatendimento](#activity-type-user-registered-for-self-service-password-reset): indica que um usuário registrou todas as informações necessárias para poder redefinir sua senha de acordo com a política de redefinição de senha do locatário especificada no momento.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Tipo de atividade: bloqueado da redefinição de senha de autoatendimento

A lista a seguir explica essa atividade em detalhes:

* **Descrição da atividade**: indica que um usuário tentou redefinir uma senha, usar uma porta específica ou validar um número de telefone mais de cinco vezes no total em 24 horas.
* **Ator de atividade**: o usuário que foi limitado da execução de operações de redefinição adicionais. O usuário pode ser um usuário final ou um administrador.
* **Destino da atividade**: o usuário que foi limitado da execução de operações de redefinição adicionais. O usuário pode ser um usuário final ou um administrador.
* **Status da atividade**:
  * _Êxito_: indica que um usuário foi restringido de executar quaisquer redefinições adicionais, tentar quaisquer métodos de autenticação adicionais ou validar quaisquer números de telefone adicionais nas próximas 24 horas.
* **Motivo da falha do status da atividade**: não aplicável.

### <a name="activity-type-change-password-self-service"></a>Tipo de atividade: alterar senha (autoatendimento)

A lista a seguir explica essa atividade em detalhes:

* **Descrição da atividade**: indica que um usuário realizou uma alteração de senha voluntária ou forçada (devido à expiração).
* **Ator de atividade**: o usuário que alterou sua senha. O usuário pode ser um usuário final ou um administrador.
* **Destino da atividade**: o usuário que alterou sua senha. O usuário pode ser um usuário final ou um administrador.
* **Status da atividade**:
  * _Êxito_: indica que um usuário alterou sua senha com êxito.
  * _Falha_: indica que um usuário não pôde alterar sua senha. Você pode selecionar a linha para ver a categoria **motivo do status da atividade** para saber mais sobre por que a falha ocorreu.
* **Motivo da falha do status da atividade**:
  * _FuzzyPolicyViolationInvalidPassword_: o usuário selecionou uma senha que foi excluída automaticamente porque os recursos de detecção de senha banida da Microsoft descobriram que ela é muito comum ou especialmente fraca.

### <a name="activity-type-reset-password-by-admin"></a>Tipo de atividade: Redefinir senha (por administrador)

A lista a seguir explica essa atividade em detalhes:

* **Descrição da atividade**: indica que um administrador executou uma redefinição de senha em nome de um usuário da portal do Azure.
* **Ator de atividade**: o administrador que realizou a redefinição de senha em nome de outro usuário final ou administrador. Deve ser um administrador de senha, administrador de usuário ou administrador de assistência técnica.
* **Destino da atividade**: o usuário cuja senha foi redefinida. O usuário pode ser um usuário final ou um administrador diferente.
* **Status da atividade**:
  * _Êxito_: indica que um administrador redefiniu com êxito a senha de um usuário.
  * _Falha_: indica que um administrador não pôde alterar a senha de um usuário. Você pode selecionar a linha para ver a categoria **motivo do status da atividade** para saber mais sobre por que a falha ocorreu.

### <a name="activity-type-reset-password-self-service"></a>Tipo de atividade: Redefinir senha (autoatendimento)

A lista a seguir explica essa atividade em detalhes:

* **Descrição da atividade**: indica que um usuário redefiniu sua senha com êxito no [portal de redefinição de senha do Azure ad](https://passwordreset.microsoftonline.com).
* **Ator de atividade**: o usuário que redefiniu sua senha. O usuário pode ser um usuário final ou um administrador.
* **Destino da atividade**: o usuário que redefiniu sua senha. O usuário pode ser um usuário final ou um administrador.
* **Status da atividade**:
  * _Êxito_: indica que um usuário redefiniu sua própria senha com êxito.
  * _Falha_: indica que um usuário não pôde redefinir sua própria senha. Você pode selecionar a linha para ver a categoria **motivo do status da atividade** para saber mais sobre por que a falha ocorreu.
* **Motivo da falha do status da atividade**:
  * _FuzzyPolicyViolationInvalidPassword_: o administrador selecionou uma senha que foi excluída automaticamente porque os recursos de detecção de senha banida da Microsoft descobriram que ela era muito comum ou especialmente fraca.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Tipo de atividade: progresso da atividade do fluxo de redefinição de senha de autoatendimento

A lista a seguir explica essa atividade em detalhes:

* **Descrição da atividade**: indica cada etapa específica que um usuário passa (por exemplo, passando um portão de autenticação de redefinição de senha específico) como parte do processo de redefinição de senha.
* **Ator de atividade**: o usuário que realizou a parte do fluxo de redefinição de senha. O usuário pode ser um usuário final ou um administrador.
* **Destino da atividade**: o usuário que realizou a parte do fluxo de redefinição de senha. O usuário pode ser um usuário final ou um administrador.
* **Status da atividade**:
  * _Êxito_: indica que um usuário concluiu com êxito uma etapa específica do fluxo de redefinição de senha.
  * _Falha_: indica que uma etapa específica do fluxo de redefinição de senha falhou. Você pode selecionar a linha para ver a categoria **motivo do status da atividade** para saber mais sobre por que a falha ocorreu.
* **Motivos do status da atividade**: consulte a tabela a seguir para [todos os motivos de status de atividade de redefinição permitidos](#description-of-the-report-columns-in-the-azure-portal).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Tipo de atividade: desbloquear uma conta de usuário (autoatendimento)

A lista a seguir explica essa atividade em detalhes:

* **Descrição da atividade**: indica que um usuário desbloqueou com êxito sua conta de Active Directory sem redefinir a senha no [portal de redefinição de senha do Azure ad](https://passwordreset.microsoftonline.com) usando o recurso Active Directory de desbloqueio de conta sem redefinição.
* **Ator de atividade**: o usuário que desbloqueou sua conta sem redefinir a senha. O usuário pode ser um usuário final ou um administrador.
* **Destino da atividade**: o usuário que desbloqueou sua conta sem redefinir a senha. O usuário pode ser um usuário final ou um administrador.
* **Status de atividade permitidos**:
  * _Êxito_: indica que um usuário desbloqueou sua própria conta com êxito.
  * _Falha_: indica que um usuário não conseguiu desbloquear sua conta. Você pode selecionar a linha para ver a categoria **motivo do status da atividade** para saber mais sobre por que a falha ocorreu.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Tipo de atividade: usuário registrado para redefinição de senha de autoatendimento

A lista a seguir explica essa atividade em detalhes:

* **Descrição da atividade**: indica que um usuário registrou todas as informações necessárias para poder redefinir sua senha de acordo com a política de redefinição de senha do locatário especificada no momento. 
* **Ator de atividade**: o usuário que registrou para redefinição de senha. O usuário pode ser um usuário final ou um administrador.
* **Destino da atividade**: o usuário que registrou para redefinição de senha. O usuário pode ser um usuário final ou um administrador.
* **Status de atividade permitidos**:
  * _Êxito_: indica que um usuário se registrou com êxito para a redefinição de senha de acordo com a política atual. 
  * _Falha_: indica que um usuário não pôde se registrar para a redefinição de senha. Você pode selecionar a linha para ver a categoria **motivo do status da atividade** para saber mais sobre por que a falha ocorreu.

     >[!NOTE]
     >Falha não significa que um usuário não pode redefinir sua própria senha. Isso significa que eles não concluíram o processo de registro. Se houver dados não verificados em sua conta que estejam corretas, como um número de telefone que não é validado, mesmo que eles não tenham verificado esse número de telefone, eles ainda poderão usá-lo para redefinir a senha.

## <a name="next-steps"></a>Passos seguintes

* [Relatórios de uso e informações do SSPR e MFA](howto-authentication-methods-usage-insights.md)
* [Como posso concluir uma implementação com êxito da SSPR?](howto-sspr-deployment.md)
* [Reponha ou altere a palavra-passe](../user-help/active-directory-passwords-update-your-own-password.md).
* [Registe-se na reposição personalizada de palavras-passe](../user-help/active-directory-passwords-reset-register.md).
* [Tem alguma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](howto-sspr-authenticationdata.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política da SSPR?](concept-sspr-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](howto-sspr-writeback.md)
* [Quais são todas as opções na SSPR e o que significam?](concept-sspr-howitworks.md)
* [Acho que algo está quebrado. Como fazer solucionar problemas de SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Exemplo de logs de auditoria de atividade do SSPR no Azure AD"
