---
title: Relatórios de redefinição de senha de autosserviço - Diretório Ativo Azure
description: Reportagem sobre eventos de redefinição de senha de autosserviço da AD Azure
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/01/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 348082ad96a5efa4d8f866c3675044edf7b6f8a8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652160"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Reporting options for Azure AD password management (Opções para os relatórios da gestão de palavras-passe do Azure AD)

Após a implantação, muitas organizações querem saber como ou se o reset de senha de autosserviço (SSPR) está realmente a ser usado. A funcionalidade de reporte que o Azure Ative Directory (Azure AD) fornece ajuda-o a responder a perguntas utilizando relatórios pré-construídos. Se você estiver devidamente licenciado, você também pode criar consultas personalizadas.

![Relatórios sobre SSPR utilizando os registos de auditoria em Azure AD][Reporting]

As seguintes perguntas podem ser respondidas pelos relatórios existentes no [portal Azure:](https://portal.azure.com/)

> [!NOTE]
> Você deve ser [um administrador global](../users-groups-roles/directory-assign-admin-roles.md), e você deve optar por que estes dados sejam recolhidos em nome da sua organização. Para optar, deve visitar o separador **Relatório** ou os registos de auditoria pelo menos uma vez. Até lá, os dados não são recolhidos para a sua organização.
>

* Quantas pessoas se registaram para reset de senha?
* Quem se registou para reset de senha?
* Que dados estão a registar as pessoas?
* Quantas pessoas redefiniram as suas palavras-passe nos últimos sete dias?
* Quais são os métodos mais comuns que os utilizadores ou administradores usam para redefinir as suas palavras-passe?
* Quais são os problemas comuns que os utilizadores ou administradores enfrentam quando tentam utilizar o reset da palavra-passe?
* Que administradores estão a repor as suas próprias palavras-passe frequentemente?
* Há alguma atividade suspeita a decorrer com o reset da palavra-passe?

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>How to view password management reports in the Azure portal (Como ver os relatórios de gestão de palavras-passe no portal do Azure)

Na experiência do portal Azure, melhorámos a forma como pode ver a redefinição de palavra-passe e a redefinição da atividade de registo de passwords. Utilize os seguintes passos para encontrar os eventos de reset da palavra-passe e redefinir a palavra-passe:

1. Navegue pelo [portal Azure.](https://portal.azure.com)
2. Selecione **Todos os serviços** no painel esquerdo.
3. Procure o **Azure Ative Directory** na lista de serviços e selecione-o.
4. Selecione **Utilizadores** da secção Gerir.
5. Selecione **Registos** de Auditoria da lâmina **dos Utilizadores.** Isto mostra-lhe todos os eventos de auditoria que ocorreram contra todos os utilizadores do seu diretório. Pode filtrar esta vista para ver todos os eventos relacionados com a palavra-passe.
6. A partir do menu **Filter** na parte superior do painel, selecione a lista de drop-down do **Serviço** e mude-a para o tipo de serviço de **Gestão de Passwords self-service.**
7. Opcionalmente, filtrar ainda mais a lista escolhendo a **Atividade** específica que lhe interessa.

### <a name="converged-registration-preview"></a>Inscrição convergente (pré-visualização)

Se estiver a participar na pré-visualização pública do registo convergente, as informações relativas à atividade do utilizador nos registos de auditoria serão encontradas ao abrigo dos Métodos de**Autenticação**de **Segurança** > .

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Descrição das colunas de relatório no portal Azure

A seguinte lista explica em pormenor cada uma das colunas de relatório no portal Azure:

* **Utilizador**: O utilizador que tentou uma operação de registo de redefinição de palavra-passe.
* **Função**: O papel do utilizador no diretório.
* **Data e Hora**: A data e a hora da tentativa.
* **Dados Registados**: Os dados de autenticação que o utilizador forneceu durante o registo de redefinição da palavra-passe.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Descrição dos valores do relatório no portal Azure

A tabela a seguir descreve os diferentes valores que é que pode definir para cada coluna no portal Azure:

| Coluna | Valores permitidos e seus significados |
| --- | --- |
| Dados registados |**Email alternativo**: O utilizador usou um e-mail alternativo ou e-mail de autenticação para autenticar.<p><p>**Telefone do escritório**: O utilizador usou um telefone de escritório para autenticar.<p>**Telemóvel**: O utilizador usou um telemóvel ou telefone de autenticação para autenticar.<p>**Questões de segurança**: O utilizador usou questões de segurança para autenticar.<p>**Qualquer combinação dos métodos anteriores, por exemplo, e-mail alternativo + telemóvel**: Ocorre quando uma política de dois portas é especificada e mostra quais os dois métodos utilizados pelo utilizador para autenticar o seu pedido de reset de palavra-passe. |

## <a name="self-service-password-management-activity-types"></a>Tipos de atividade de gestão de passwords self-service

Os seguintes tipos de atividade aparecem na categoria de evento de auditoria de gestão de **passwords self-service:**

* [Bloqueado do reset da palavra-passe self-service](#activity-type-blocked-from-self-service-password-reset): Indica que um utilizador tentou redefinir uma palavra-passe, utilizar um portão específico ou validar um número de telefone mais de cinco vezes em 24 horas.
* [Alterar a palavra-passe (self-service)](#activity-type-change-password-self-service): Indica que um utilizador realizou uma alteração voluntária ou forçada (devido à caducidade).
* [Redefinir a palavra-passe (por administrador)](#activity-type-reset-password-by-admin): Indica que um administrador realizou uma redefinição de senha em nome de um utilizador do portal Azure.
* [Redefinir a palavra-passe (self-service)](#activity-type-reset-password-self-service): Indica que um utilizador redefiniu com sucesso a sua palavra-passe do portal de reset da [palavra-passe Azure AD](https://passwordreset.microsoftonline.com).
* Progresso da atividade de fluxo de [palavra-passe de autosserviço](#activity-type-self-serve-password-reset-flow-activity-progress): Indica cada passo específico que um utilizador procede, como passar um portão específico de autenticação de reset de palavra-passe, como parte do processo de reset da palavra-passe.
* Desbloquear a conta de [utilizador (self-service)](#activity-type-unlock-a-user-account-self-service)): Indica que um utilizador desbloqueou com sucesso a sua conta De Diretório Ativo sem redefinir a sua palavra-passe a partir do portal de [redefinição de palavra-passe Azure AD](https://passwordreset.microsoftonline.com) utilizando a funcionalidade de diretório Ativo de desbloqueio da conta sem reset.
* [Utilizador registado para reposição de palavra-passe de autosserviço](#activity-type-user-registered-for-self-service-password-reset): Indica que um utilizador registou todas as informações necessárias para poder redefinir a sua palavra-passe de acordo com a política de redefinição de palavra-passe do arrendatário atualmente especificada.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Tipo de atividade: Bloqueado do reset de senha de autosserviço

A seguinte lista explica esta atividade em detalhe:

* **Descrição da atividade**: Indica que um utilizador tentou redefinir uma palavra-passe, utilizar um portão específico ou validar um número de telefone mais de cinco vezes em 24 horas.
* **Ator**de atividade : O utilizador que foi impedido de realizar operações adicionais de reset. O utilizador pode ser um utilizador final ou um administrador.
* **Objetivo de atividade**: O utilizador que foi impedido de realizar operações adicionais de reset. O utilizador pode ser um utilizador final ou um administrador.
* **Estado da atividade:**
  * _Sucesso_: Indica que um utilizador foi impedido de realizar quaisquer resets adicionais, de tentar quaisquer métodos de autenticação adicionais ou de validar quaisquer números de telefone adicionais para as próximas 24 horas.
* **Razão de falha no estado**de atividade : Não aplicável.

### <a name="activity-type-change-password-self-service"></a>Tipo de atividade: Alterar a palavra-passe (self-service)

A seguinte lista explica esta atividade em detalhe:

* **Descrição da atividade**: Indica que um utilizador realizou uma alteração voluntária ou forçada (devido à caducidade).
* **Ator de atividade**: O utilizador que mudou a sua palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Alvo de atividade**: O utilizador que alterou a sua palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Estados de atividade:**
  * _Sucesso_: Indica que um utilizador alterou com sucesso a sua palavra-passe.
  * _Falha_: Indica que um utilizador não alterou a sua palavra-passe. Pode selecionar a linha para ver a categoria **de razão** de estado da Atividade para saber mais sobre o porquê da falha ocorrer.
* **Razão de falha do estado**da atividade:
  * _FuzzyPolicyViolationInvalidPassword_: O utilizador selecionou uma palavra-passe que foi automaticamente proibida porque as capacidades de deteção de passwords proibidas da Microsoft a charam demasiado comum ou especialmente fraca.

### <a name="activity-type-reset-password-by-admin"></a>Tipo de atividade: Reset password (por administrador)

A seguinte lista explica esta atividade em detalhe:

* **Descrição da atividade**: Indica que um administrador realizou uma redefinição de senha em nome de um utilizador do portal Azure.
* **Ator de atividade**: O administrador que realizou a palavra-passe reset em nome de outro utilizador final ou administrador. Deve ser um administrador de palavra-passe, administrador de utilizador ou administrador de helpdesk.
* **Alvo de atividade**: O utilizador cuja palavra-passe foi redefinida. O utilizador pode ser um utilizador final ou um administrador diferente.
* **Estados de atividade:**
  * _Sucesso_: Indica que um administrador redefine com sucesso a palavra-passe de um utilizador.
  * _Falha_: Indica que um administrador não alterou a palavra-passe de um utilizador. Pode selecionar a linha para ver a categoria **de razão** de estado da Atividade para saber mais sobre o porquê da falha ocorrer.

### <a name="activity-type-reset-password-self-service"></a>Tipo de atividade: Reset password (self-service)

A seguinte lista explica esta atividade em detalhe:

* **Descrição da atividade**: Indica que um utilizador redefiniu com sucesso a sua palavra-passe do portal de reset de [palavra-passe Azure AD](https://passwordreset.microsoftonline.com).
* **Ator de atividade**: O utilizador que redefiniu a sua palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Alvo de atividade**: O utilizador que redefinir a sua palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Estados de atividade:**
  * _Sucesso_: Indica que um utilizador redefine com sucesso a sua própria palavra-passe.
  * _Falha_: Indica que um utilizador não redefiniu a sua própria palavra-passe. Pode selecionar a linha para ver a categoria **de razão** de estado da Atividade para saber mais sobre o porquê da falha ocorrer.
* **Razão de falha do estado**da atividade:
  * _FuzzyPolicyViolationInvalidPassword_: O administrador selecionou uma palavra-passe que foi automaticamente proibida porque as capacidades de deteção de passwords proibidas da Microsoft a charam demasiado comum ou especialmente fraca.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Tipo de atividade: Auto-servir o progresso da atividade de fluxo de password

A seguinte lista explica esta atividade em detalhe:

* **Descrição da atividade**: Indica cada passo específico que um utilizador procede (como passar por um portão específico de autenticação de palavra-passe) como parte do processo de reset da palavra-passe.
* **Ator**de atividade : O utilizador que realizou parte do fluxo de reset da palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Objetivo de atividade**: O utilizador que realizou parte do fluxo de reset da palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Estados de atividade:**
  * _Sucesso_: Indica que um utilizador completou com sucesso um passo específico do fluxo de reset da palavra-passe.
  * _Falha_: Indica que falhou um passo específico do fluxo de reset da palavra-passe. Pode selecionar a linha para ver a categoria **de razão** de estado da Atividade para saber mais sobre o porquê da falha ocorrer.
* **Razões de estado**de atividade : Consulte o quadro seguinte para [todos os motivos admissíveis de estado](#description-of-the-report-columns-in-the-azure-portal)de atividade de reset .

### <a name="activity-type-unlock-a-user-account-self-service"></a>Tipo de atividade: Desbloquear uma conta de utilizador (self-service)

A seguinte lista explica esta atividade em detalhe:

* **Descrição da atividade**: Indica que um utilizador desbloqueou com sucesso a sua conta De Diretório Ativo sem redefinir a sua palavra-passe do portal de [redefinição da palavra-passe Azure AD](https://passwordreset.microsoftonline.com) utilizando a funcionalidade de diretório Ativo de desbloqueio da conta sem reset.
* **Ator de atividade**: O utilizador que desbloqueou a sua conta sem redefinir a sua palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Alvo de atividade**: O utilizador que desbloqueou a sua conta sem reajustar a sua palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Estados de atividade permitidos:**
  * _Sucesso_: Indica que um utilizador desbloqueou com sucesso a sua própria conta.
  * _Falha_: Indica que um utilizador não desbloqueou a sua conta. Pode selecionar a linha para ver a categoria **de razão** de estado da Atividade para saber mais sobre o porquê da falha ocorrer.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Tipo de atividade: Utilizador registado para reset de palavra-passe self-service

A seguinte lista explica esta atividade em detalhe:

* **Descrição da atividade**: Indica que um utilizador registou todas as informações necessárias para poder redefinir a sua palavra-passe de acordo com a política de redefinição de palavra-passe do arrendatário atualmente especificada. 
* **Ator de atividade**: O utilizador que se registou para reset de palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Alvo de atividade**: O utilizador que se registou para reset de palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Estados de atividade permitidos:**
  * _Sucesso_: Indica que um utilizador registou com sucesso a reposição de palavra-passe de acordo com a política atual. 
  * _Falha_: Indica que um utilizador não se registou para reset de palavra-passe. Pode selecionar a linha para ver a categoria **de razão** de estado da Atividade para saber mais sobre o porquê da falha ocorrer.

     >[!NOTE]
     >Falha não significa que um utilizador não seja capaz de redefinir a sua própria palavra-passe. Significa que não terminaram o processo de registo. Se houver dados não verificados na sua conta que estejam corretos, como um número de telefone que não seja validado, mesmo que não tenham verificado este número de telefone, ainda podem usá-lo para redefinir a sua palavra-passe.

## <a name="next-steps"></a>Passos seguintes

* [SSPR e Relatórios de Informação sobre o Uso de SSPR e MFA](howto-authentication-methods-usage-insights.md)
* [Como posso concluir uma implementação com êxito da SSPR?](howto-sspr-deployment.md)
* [Reponha ou altere a palavra-passe](../user-help/active-directory-passwords-update-your-own-password.md).
* [Registe-se para reset de palavra-passe de autosserviço](../user-help/active-directory-passwords-reset-register.md).
* [Tem alguma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](howto-sspr-authenticationdata.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política da SSPR?](concept-sspr-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](howto-sspr-writeback.md)
* [Quais são todas as opções na SSPR e o que significam?](concept-sspr-howitworks.md)
* [Acho que algo está partido. Como posso resolver problemas com a SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Exemplo de registos de auditoria de atividade sspr em Azure AD"
