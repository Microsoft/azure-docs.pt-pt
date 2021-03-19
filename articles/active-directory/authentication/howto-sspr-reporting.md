---
title: Relatórios de reset de palavra-passe de autosserviço - Azure Ative Directory
description: Reportagem sobre eventos de redefinição de senha de autosserviço Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/01/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad1b8318e261c7dfef7fc125716736087a84bdc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579183"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Reporting options for Azure AD password management (Opções para os relatórios da gestão de palavras-passe do Azure AD)

Após a implementação, muitas organizações querem saber como ou se a palavra-passe de autosserviço está realmente a ser usada. A funcionalidade de reporte que o Azure Ative Directory (Azure AD) fornece ajuda-o a responder a perguntas utilizando relatórios pré-construídos. Se você estiver devidamente licenciado, também pode criar consultas personalizadas.

![Reportagem sobre sSPR usando os registos de auditoria em Azure AD][Reporting]

As seguintes perguntas podem ser respondidas pelos relatórios existentes no [portal Azure:](https://portal.azure.com/)

> [!NOTE]
> Você deve ser [um administrador global](../roles/permissions-reference.md), e você deve optar por estes dados para ser recolhido em nome da sua organização. Para optar, deve visitar o **separador Reportar** ou os registos de auditoria pelo menos uma vez. Até lá, os dados não são recolhidos para a sua organização.
>

* Quantas pessoas se registaram para reposição de passwords?
* Quem se registou para reposição de password?
* Que dados estão a registar as pessoas?
* Quantas pessoas repuserem as suas palavras-passe nos últimos sete dias?
* Quais são os métodos mais comuns que os utilizadores ou administradores usam para redefinir as suas palavras-passe?
* Quais são os problemas comuns que os utilizadores ou administradores enfrentam quando tentam utilizar o reset da palavra-passe?
* Que administradores estão a repor as suas próprias palavras-passe com frequência?
* Há alguma atividade suspeita a acontecer com a redefinição da palavra-passe?

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>How to view password management reports in the Azure portal (Como ver os relatórios de gestão de palavras-passe no portal do Azure)

Na experiência do portal Azure, melhorámos a forma como pode ver a atividade de reset de password e de redefinição de password. Utilize os seguintes passos para encontrar os eventos de registo de reset da palavra-passe e da palavra-passe:

1. Navegue pelo [portal Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no painel esquerdo.
3. Procure **o Azure Ative Directory** na lista de serviços e selecione-o.
4. Selecione **Utilizadores** da secção Gerir.
5. Selecione **Registos** de Auditoria da lâmina **do Utilizadores.** Isto mostra-lhe todos os eventos de auditoria que ocorreram contra todos os utilizadores do seu diretório. Pode filtrar esta vista para ver todos os eventos relacionados com a palavra-passe.
6. A partir do menu **Filtro** na parte superior do painel, selecione a lista de drop-down **do Serviço** e altere-a para o tipo de serviço **de Gestão de Passwords self-service.**
7. Opcionalmente, filtrar ainda mais a lista escolhendo a **Atividade** específica que lhe interessa.

### <a name="combined-registration"></a>Registo combinado

Se tiver ativado o [registo combinado,](./concept-registration-mfa-sspr-combined.md)serão encontradas informações sobre a atividade do utilizador nos registos de auditoria nos  >  **Métodos de Autenticação de** Segurança .

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Descrição das colunas de relatório no portal Azure

A seguinte lista explica em pormenor cada uma das colunas de relatório no portal Azure:

* **Utilizador**: O utilizador que tentou uma operação de registo de redefinição de password.
* **Função**: O papel do utilizador no diretório.
* **Data e hora**: A data e a hora da tentativa.
* **Dados Registados**: Os dados de autenticação que o utilizador forneceu durante o registo de redefinição de password.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Descrição dos valores do relatório no portal Azure

A tabela a seguir descreve os diferentes valores que são definidos para cada coluna no portal Azure:

| Coluna | Valores permitidos e seus significados |
| --- | --- |
| Dados registados |**Email alternativo**: O utilizador usou um e-mail alternativo ou um e-mail de autenticação para autenticar.<p><p>**Telefone de escritório**: O utilizador usou um telefone de escritório para autenticar.<p>**Telemóvel**: O utilizador usou um telemóvel ou um telemóvel de autenticação para autenticar.<p>**Questões de segurança**: O utilizador utilizou perguntas de segurança para autenticar.<p>**Qualquer combinação dos métodos anteriores, por exemplo, e-mail alternativo + telemóvel**: Ocorre quando uma política de dois portões é especificada e mostra quais os dois métodos utilizados pelo utilizador para autenticar o seu pedido de reset de palavra-passe. |

## <a name="self-service-password-management-activity-types"></a>Self-Service tipos de atividade de Gestão de Passwords

Os seguintes tipos de atividade aparecem na categoria de eventos de auditoria **de Autosserviço Password Management:**

* [Bloqueado do reset da palavra-passe de autosserviço](#activity-type-blocked-from-self-service-password-reset): Indica que um utilizador tentou redefinir uma palavra-passe, utilizar um portão específico ou validar um número de telefone mais de cinco vezes em 24 horas.
* [Alterar palavra-passe (self-service)](#activity-type-change-password-self-service): Indica que um utilizador realizou uma alteração voluntária ou forçada (devido à expiração).
* [Redefinir a palavra-passe (por administração)](#activity-type-reset-password-by-admin): Indica que um administrador efetuou uma redefinição de palavra-passe em nome de um utilizador a partir do portal Azure.
* [Redefinir a palavra-passe (self-service)](#activity-type-reset-password-self-service): Indica que um utilizador reinicie com sucesso a sua palavra-passe a partir do portal de reset da [palavra-passe AD Azure](https://passwordreset.microsoftonline.com).
* [Autosserviço redefinir o progresso da atividade de fluxo de redefinição](#activity-type-self-serve-password-reset-flow-activity-progress): Indica cada passo específico através do qual um utilizador procede, como por exemplo, passar um portão de autenticação de redefinição de palavra-passe específico, como parte do processo de reset da palavra-passe.
* [Desbloquear a conta de utilizador (self-service)](#activity-type-unlock-a-user-account-self-service)): Indica que um utilizador desbloqueou com sucesso a sua conta ative Directory sem redefinir a sua palavra-passe a partir do portal de reset da [palavra-passe AD Azure](https://passwordreset.microsoftonline.com) utilizando a função ative directory de desbloqueio de conta sem reset.
* [Utilizador registado para redefinição da palavra-passe de autosserviço](#activity-type-user-registered-for-self-service-password-reset): Indica que um utilizador registou todas as informações necessárias para poder redefinir a sua palavra-passe de acordo com a política de reset da palavra-passe do inquilino atualmente especificada.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Tipo de atividade: Bloqueado do reset da palavra-passe de autosserviço

A seguinte lista explica esta atividade em detalhe:

* **Descrição da atividade**: Indica que um utilizador tentou redefinir uma palavra-passe, utilizar um portão específico ou validar um número de telefone mais de cinco vezes em 24 horas.
* **Ator de atividade**: O utilizador que foi impedido de realizar operações adicionais de reset. O utilizador pode ser um utilizador final ou um administrador.
* **Objetivo de atividade**: O utilizador que foi impedido de realizar operações adicionais de reset. O utilizador pode ser um utilizador final ou um administrador.
* **Estado da atividade:**
  * _Sucesso_: Indica que um utilizador foi impedido de realizar quaisquer resets adicionais, de tentar quaisquer métodos de autenticação adicionais ou de validar quaisquer números de telefone adicionais durante as próximas 24 horas.
* **Razão de falha de estado da atividade**: Não aplicável.

### <a name="activity-type-change-password-self-service"></a>Tipo de atividade: Alterar palavra-passe (self-service)

A seguinte lista explica esta atividade em detalhe:

* **Descrição da atividade**: Indica que um utilizador realizou uma alteração voluntária ou forçada (devido à expiração).
* **Ator de atividade**: O utilizador que mudou a sua palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Alvo de atividade**: O utilizador que alterou a sua palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Estados de atividade:**
  * _Sucesso_: Indica que um utilizador alterou com sucesso a sua palavra-passe.
  * _Falha_: Indica que um utilizador não alterou a sua palavra-passe. Pode selecionar a linha para ver a categoria **de razão de estado** de atividade para saber mais sobre o porquê da falha ter ocorrido.
* **Razão de falha do estado da atividade:**
  * _FuzzyPolicyViolationInvalidPassword_: O utilizador selecionou uma palavra-passe que foi automaticamente proibida porque as capacidades de Deteção de Passwords proibidas pela Microsoft acharam que era demasiado comum ou especialmente fraca.

### <a name="activity-type-reset-password-by-admin"></a>Tipo de atividade: Redefinir a palavra-passe (por administração)

A seguinte lista explica esta atividade em detalhe:

* **Descrição da atividade**: Indica que um administrador efetuou uma redefinição de palavra-passe em nome de um utilizador a partir do portal Azure.
* **Ator de atividade**: O administrador que executou a palavra-passe reiniciou em nome de outro utilizador ou administrador final. Deve ser um administrador de senha, administrador de utilizador ou administrador de helpdesk.
* **Alvo de atividade**: O utilizador cuja palavra-passe foi reposta. O utilizador pode ser um utilizador final ou um administrador diferente.
* **Estados de atividade:**
  * _Sucesso_: Indica que um administrador reinicia com sucesso a palavra-passe de um utilizador.
  * _Falha_: Indica que um administrador não alterou a palavra-passe de um utilizador. Pode selecionar a linha para ver a categoria **de razão de estado** de atividade para saber mais sobre o porquê da falha ter ocorrido.

### <a name="activity-type-reset-password-self-service"></a>Tipo de atividade: Redefinir a palavra-passe (self-service)

A seguinte lista explica esta atividade em detalhe:

* **Descrição da atividade**: Indica que um utilizador reiniciou com sucesso a sua palavra-passe a partir do portal de reposição da [palavra-passe AD Azure](https://passwordreset.microsoftonline.com).
* **Ator de atividade**: O utilizador que repôs a sua palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Alvo de atividade**: O utilizador que repôs a sua palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Estados de atividade:**
  * _Sucesso_: Indica que um utilizador reinicia com sucesso a sua própria palavra-passe.
  * _Falha_: Indica que um utilizador não repôs a sua própria palavra-passe. Pode selecionar a linha para ver a categoria **de razão de estado** de atividade para saber mais sobre o porquê da falha ter ocorrido.
* **Razão de falha do estado da atividade:**
  * _FuzzyPolicyViolationInvalidPassword_: O administrador selecionou uma palavra-passe que foi automaticamente proibida porque as capacidades de Deteção de Passwords proibidas pela Microsoft acharam que era demasiado comum ou especialmente fraca.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Tipo de atividade: Auto-servir redefinição da atividade de fluxo

A seguinte lista explica esta atividade em detalhe:

* **Descrição da atividade**: Indica cada passo específico através do qual um utilizador procede (como por exemplo, passar um portão de autenticação de redefinição de senha específico) como parte do processo de reset da palavra-passe.
* **Ator de atividade**: O utilizador que executou parte do fluxo de reset da palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Alvo de atividade**: O utilizador que executou parte do fluxo de reset da palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Estados de atividade:**
  * _Sucesso_: Indica que um utilizador completou com sucesso um passo específico do fluxo de reset da palavra-passe.
  * _Falha_: Indica que um passo específico do fluxo de reset da palavra-passe falhou. Pode selecionar a linha para ver a categoria **de razão de estado** de atividade para saber mais sobre o porquê da falha ter ocorrido.
* **Razões de estado da atividade**: Consulte o quadro seguinte para todos os [motivos de situação de reposição admissível](#description-of-the-report-columns-in-the-azure-portal).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Tipo de atividade: Desbloquear uma conta de utilizador (self-service)

A seguinte lista explica esta atividade em detalhe:

* **Descrição da atividade**: Indica que um utilizador desbloqueou com sucesso a sua conta de Diretório Ativo sem redefinir a sua palavra-passe a partir do portal de reset da [palavra-passe AD Azure](https://passwordreset.microsoftonline.com) utilizando a função ative directory do desbloqueio de conta sem reset.
* **Ator de atividade**: O utilizador que desbloqueou a sua conta sem redefinir a sua palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Alvo de atividade**: O utilizador que desbloqueou a sua conta sem redefinir a sua palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Estatutos de atividade permitidos:**
  * _Sucesso_: Indica que um utilizador desbloqueou com sucesso a sua própria conta.
  * _Falha_: Indica que um utilizador não desbloqueou a sua conta. Pode selecionar a linha para ver a categoria **de razão de estado** de atividade para saber mais sobre o porquê da falha ter ocorrido.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Tipo de atividade: Utilizador registado para reset da palavra-passe de autosserviço

A seguinte lista explica esta atividade em detalhe:

* **Descrição da atividade**: Indica que um utilizador registou todas as informações necessárias para poder redefinir a sua palavra-passe de acordo com a política de reposição da palavra-passe do inquilino atualmente especificada. 
* **Ator de atividade**: O utilizador que se registou para reposição da palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Alvo de atividade**: O utilizador que se registou para reposição da palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Estatutos de atividade permitidos:**
  * _Sucesso_: Indica que um utilizador registou com sucesso a reposição da palavra-passe de acordo com a política atual. 
  * _Falha_: Indica que um utilizador não se registou para reiniciar a palavra-passe. Pode selecionar a linha para ver a categoria **de razão de estado** de atividade para saber mais sobre o porquê da falha ter ocorrido.

     >[!NOTE]
     >Falha não significa que um utilizador seja incapaz de redefinir a sua própria palavra-passe. Significa que não terminaram o processo de registo. Se houver dados não verificados na sua conta que estejam corretos, como um número de telefone que não seja validado, mesmo que não tenham verificado este número de telefone, ainda podem usá-lo para redefinir a sua palavra-passe.

## <a name="next-steps"></a>Passos seguintes

* [Utilização e informação sobre informações sobre o uso e informações sobre o uso da SSPR e do MFA](./howto-authentication-methods-activity.md)
* [Como posso concluir uma implementação com êxito da SSPR?](howto-sspr-deployment.md)
* [Reponha ou altere a palavra-passe](../user-help/active-directory-passwords-update-your-own-password.md).
* [Registe-se para reiniciar a palavra-passe de autosserviço](../user-help/active-directory-passwords-reset-register.md).
* [Tem alguma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](howto-sspr-authenticationdata.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política da SSPR?](concept-sspr-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](./tutorial-enable-sspr-writeback.md)
* [Quais são todas as opções na SSPR e o que significam?](concept-sspr-howitworks.md)
* [Acho que algo está partido. Como é que resolvo a SSPR?](./troubleshoot-sspr.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Exemplo de registos de auditoria de atividade sSPR em Azure AD"