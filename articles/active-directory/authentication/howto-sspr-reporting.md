---
title: Relatórios - Azure Active Directory de reposição de palavra-passe self-service
description: Eventos de redefinição de relatórios sobre a palavra-passe self-service do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53140c7b02dd657036b76db0dd137bd770d97f4d
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369535"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Opções de relatórios para gestão de palavras-passe do Azure AD

Após a implementação, muitas organizações querem saber como realmente está a ser utilizado ou se de reposição de palavra-passe self-service (SSPR). A funcionalidade de relatórios do Azure Active Directory (Azure AD) fornece ajuda a responder a perguntas através de relatórios criados previamente. Se está corretamente licenciado, também pode criar consultas personalizadas.

![Relatórios na SSPR usando a auditoria de registos no Azure AD][Reporting]

As perguntas seguintes podem ser respondidas pelos relatórios que existem nos [portal do Azure](https://portal.azure.com/):

> [!NOTE]
> Tem de ser [um administrador global](../users-groups-roles/directory-assign-admin-roles.md), e deve optar ativamente por estes dados para ser reunidas em nome da sua organização. Para optar, tem de visitar o **relatórios** separador ou a auditoria de registos, pelo menos, uma vez. Até lá, os dados não são recolhidos para a sua organização.
>

* Como muitas pessoas têm registado na reposição de palavra-passe?
* Quem tiver registado na reposição de palavra-passe?
* Os dados que são as pessoas se registrar?
* Como muitas pessoas reponham as palavras-passe nos últimos sete dias?
* Quais são os métodos mais comuns que os utilizadores ou os administradores utilizam para repor as palavras-passe?
* O que são problemas comuns que os usuários ou administradores face ao tentar utilizar a reposição de palavra-passe?
* O que os administradores são a repor a palavra-passe com frequência?
* Existe alguma atividade suspeita acontecendo com a reposição de palavra-passe?

## <a name="power-bi-content-pack"></a>Pacote de conteúdos do Power BI

Se for um utilizador do Power BI, há um pacote de conteúdos para o Azure AD, que inclui a criação de relatórios fácil de usar para SSPR. Para obter mais informações sobre como utilizar e implementar o pacote de conteúdos, consulte [como utilizar o pacote de conteúdos do Azure Active Directory Power BI](../reports-monitoring/howto-power-bi-content-pack.md). Com o pacote de conteúdos, pode criar seus próprios dashboards e partilhá-los com outras pessoas na sua organização.

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Como visualizar relatórios de gestão de palavra-passe no portal do Azure

A experiência do portal do Azure, melhorámos a maneira que pode ver a reposição de palavra-passe e a atividade de registo de reposição de palavra-passe. Utilize os seguintes passos para localizar a palavra-passe de reposição e eventos de registo de reposição de palavra-passe:

1. Navegue para o [portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no painel esquerdo.
3. Procure **do Azure Active Directory** na lista de serviços e selecioná-lo.
4. Selecionar **Utilizadores e grupos**.
5. Selecione **registos de auditoria** partir do **utilizadores e grupos** menu. Isso mostra todos os eventos de auditoria que ocorreram em relação a todos os utilizadores no seu diretório. Pode filtrar esta vista para ver todos os eventos relacionados com a palavra-passe.
6. Para filtrar esta vista para ver apenas os eventos relacionados à reposição de palavra-passe, selecione o **filtro** botão na parte superior do painel.
7. Do **filtro** menu, selecione a **categoria** pendente lista e altere-o para o **gestão de palavras-passe Self-Service** tipo de categoria.
8. Opcionalmente, continuar a filtrar a lista ao escolher as específicas **atividade** que está interessado.

### <a name="converged-registration-preview"></a>Registo convergido (pré-visualização)

Se estão a participar na pré-visualização pública do Registro convergida, irão encontrar informações sobre a atividade do utilizador nos registos de auditoria na categoria **métodos de autenticação**.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Descrição das colunas do relatório no portal do Azure

A lista a seguir explica cada uma das colunas de relatório no portal do Azure em detalhe:

* **Utilizador**: Operação de registo de reposição do utilizador que tentou uma palavra-passe.
* **Função**: A função do utilizador no diretório.
* **Data e hora**: A data e hora da tentativa.
* **Dados registados**: Registo de reposição de dados de autenticação que o utilizador fornecido durante a palavra-passe.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Descrição dos valores de relatório no portal do Azure

A tabela seguinte descreve os valores de diferentes que são que pode definir para cada coluna no portal do Azure:

| Coluna | Valores permitidos e seus significados |
| --- | --- |
| Dados registados |**E-mail alternativo**: O utilizador utilizou um correio eletrónico alternativo ou e-mail de autenticação para autenticar.<p><p>**Telefone do escritório**: O utilizador utilizou um telefone do escritório para autenticar.<p>**Número de telemóvel**: O utilizador utilizou um telemóvel ou telefone de autenticação para autenticar.<p>**Perguntas de segurança**: O utilizador utilizou perguntas de segurança para autenticar.<p>**Qualquer combinação dos métodos anteriores, por exemplo, se alternam e-mail + o número de telemóvel**: Ocorre quando uma política de porta de dois for especificada e mostra os dois métodos, o utilizador utilizado para autenticação de pedido de reposição de palavra-passe. |

## <a name="self-service-password-management-activity-types"></a>Tipos de atividades de gestão de palavra-passe Self-Service

Os seguintes tipos de atividade são apresentados no **gestão de palavras-passe Self-Service** categoria de eventos de auditoria:

* [Bloqueado de reposição de palavra-passe self-service](#activity-type-blocked-from-self-service-password-reset): Indica que um utilizador tentou repor uma palavra-passe, utilize uma porta específica ou validar um número de telefone mais do que cinco vezes total em 24 horas.
* [Alterar palavra-passe (personalizada)](#activity-type-change-password-self-service): Indica que um utilizador efetuar um voluntário ou forçado (devido à expiração) alteração de palavra-passe.
* [Repor palavra-passe (por administrador)](#activity-type-reset-password-by-admin): Indica que um administrador efetuar uma reposição em nome de um utilizador a partir do portal do Azure de palavra-passe.
* [Repor palavra-passe (personalizada)](#activity-type-reset-password-self-service): Indica que um utilizador com êxito de repor a palavra-passe do [portal de reposição de palavras-passe do Azure AD](https://passwordreset.microsoftonline.com).
* [Progresso da atividade de fluxo de reposição de palavra-passe self-service](#activity-type-self-serve-password-reset-flow-activity-progress): Indica a cada passo que um usuário avança nos, como passar uma palavra-passe específica de reposição de porta de autenticação, como o processo de reposição da parte da palavra-passe.
* [Desbloquear conta de utilizador (personalizado)](#activity-type-unlock-a-user-account-self-service)): Indica que um utilizador com êxito conseguiu desbloquear a conta do Active Directory sem repor a palavra-passe do [portal de reposição de palavras-passe do Azure AD](https://passwordreset.microsoftonline.com) ao utilizar a funcionalidade do Active Directory da conta de desbloquear sem repor.
* [Utilizador registado na reposição de palavra-passe self-service](#activity-type-user-registered-for-self-service-password-reset): Indica que um utilizador registou todas as informações necessárias para repor a palavra-passe em conformidade com a política de reposição de palavra-passe do inquilino atualmente especificado.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Tipo de atividade: Bloqueado da reposição de palavras-passe personalizada

A lista a seguir explica esta atividade detalhadamente:

* **Descrição de atividade**: Indica que um utilizador tentou repor uma palavra-passe, utilize uma porta específica ou validar um número de telefone mais do que cinco vezes total em 24 horas.
* **Ator atividade**: O utilizador que foi limitado de executar adicionais a operações de reposição. O utilizador pode ser um utilizador final ou um administrador.
* **Destino de atividade**: O utilizador que foi limitado de executar adicionais a operações de reposição. O utilizador pode ser um utilizador final ou um administrador.
* **Estado da atividade**:
  * _Success_: Indica que um utilizador foi limitado de executar qualquer reposições adicionais, tentar qualquer método de autenticação adicional ou validar qualquer números de telefone adicionais para as próximas 24 horas.
* **Razão de falha do Estado de atividade**: Não aplicável.

### <a name="activity-type-change-password-self-service"></a>Tipo de atividade: Alterar palavra-passe (personalizada)

A lista a seguir explica esta atividade detalhadamente:

* **Descrição de atividade**: Indica que um utilizador efetuar um voluntário ou forçado (devido à expiração) alteração de palavra-passe.
* **Ator atividade**: O utilizador que foi alterado a palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Destino de atividade**: O utilizador que foi alterado a palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Estados de atividade**:
  * _Success_: Indica que um utilizador alterado com êxito a palavra-passe.
  * _Falha de_: Indica que um utilizador não foi possível alterar a palavra-passe. Pode selecionar a linha para ver os **razão do Estado de atividade** categoria para saber mais sobre por que motivo a falha ocorreu.
* **Razão de falha do Estado de atividade**:
  * _FuzzyPolicyViolationInvalidPassword_: O utilizador selecionou uma palavra-passe que foi banida automaticamente porque as capacidades de deteção de palavra-passe banidas da Microsoft não encontrado-lo para ser demasiado comum ou especialmente fraco.

### <a name="activity-type-reset-password-by-admin"></a>Tipo de atividade: Repor palavra-passe (por administrador)

A lista a seguir explica esta atividade detalhadamente:

* **Descrição de atividade**: Indica que um administrador efetuar uma reposição em nome de um utilizador a partir do portal do Azure de palavra-passe.
* **Ator atividade**: O administrador que efetuou a reposição em nome de outro utilizador final ou administrador de palavra-passe. Tem de ser seja um administrador global, administrador de palavras-passe, utilizador administrador ou administrador de suporte técnico.
* **Destino de atividade**: O utilizador cuja palavras-passe foi reposta. O utilizador pode ser um utilizador final ou um administrador diferente.
* **Estados de atividade**:
  * _Success_: Indica que um administrador com êxito de reposição da palavra-passe de um utilizador.
  * _Falha de_: Indica a falha de um administrador alterar a palavra-passe de um utilizador. Pode selecionar a linha para ver os **razão do Estado de atividade** categoria para saber mais sobre por que motivo a falha ocorreu.

### <a name="activity-type-reset-password-self-service"></a>Tipo de atividade: Repor palavra-passe (personalizada)

A lista a seguir explica esta atividade detalhadamente:

* **Descrição de atividade**: Indica que um utilizador com êxito de repor a palavra-passe do [portal de reposição de palavras-passe do Azure AD](https://passwordreset.microsoftonline.com).
* **Ator atividade**: O utilizador que reponha a palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Destino de atividade**: O utilizador que reponha a palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Estados de atividade**:
  * _Success_: Indica que um utilizador reposta com êxito a sua própria palavra-passe.
  * _Falha de_: Indica que um utilizador falha ao repor a sua própria palavra-passe. Pode selecionar a linha para ver os **razão do Estado de atividade** categoria para saber mais sobre por que motivo a falha ocorreu.
* **Razão de falha do Estado de atividade**:
  * _FuzzyPolicyViolationInvalidPassword_: O administrador selecionado uma palavra-passe que foi banida automaticamente porque as capacidades de deteção de palavra-passe banidas da Microsoft não encontrado-lo para ser demasiado comum ou especialmente fraco.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Tipo de atividade: Progresso da atividade de fluxo de reposição de palavra-passe de personalizada

A lista a seguir explica esta atividade detalhadamente:

* **Descrição de atividade**: Indica a cada passo que um usuário avança nos (por exemplo, passar uma palavra-passe específica de reposição de porta de autenticação) como o processo de reposição da parte da palavra-passe.
* **Ator atividade**: Fluxo de reposição de utilizador que efetuou a parte da palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Destino de atividade**: Fluxo de reposição de utilizador que efetuou a parte da palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Estados de atividade**:
  * _Success_: Indica que um utilizador concluída com êxito um passo específico do fluxo de reposição de palavra-passe.
  * _Falha de_: Indica que um passo específico da palavra-passe reposta fluxo falhou. Pode selecionar a linha para ver os **razão do Estado de atividade** categoria para saber mais sobre por que motivo a falha ocorreu.
* **Motivos de estado de atividade**:   Consulte a tabela seguinte para [todos os reposição permitido atividade Estado motivos](#description-of-the-report-columns-in-the-azure-portal).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Tipo de atividade: Desbloquear uma conta de utilizador (personalizado)

A lista a seguir explica esta atividade detalhadamente:

* **Descrição de atividade**: Indica que um utilizador com êxito conseguiu desbloquear a conta do Active Directory sem repor a palavra-passe do [portal de reposição de palavras-passe do Azure AD](https://passwordreset.microsoftonline.com) ao utilizar a funcionalidade do Active Directory da conta de desbloquear sem repor.
* **Ator atividade**: O utilizador que conseguiu desbloquear a sua conta sem repor a palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Destino de atividade**: O utilizador que conseguiu desbloquear a sua conta sem repor a palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Permitido Estados de atividade**:
  * _Success_: Indica que um utilizador desbloqueado com êxito a sua própria conta.
  * _Falha de_: Indica que um utilizador não conseguiu desbloquear a conta. Pode selecionar a linha para ver os **razão do Estado de atividade** categoria para saber mais sobre por que motivo a falha ocorreu.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Tipo de atividade: Utilizador registado na reposição de palavra-passe personalizada

A lista a seguir explica esta atividade detalhadamente:

* **Descrição de atividade**: Indica que um utilizador registou todas as informações necessárias para repor a palavra-passe em conformidade com a política de reposição de palavra-passe do inquilino atualmente especificado. 
* **Ator atividade**: O utilizador que registado na reposição de palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Destino de atividade**: O utilizador que registado na reposição de palavra-passe. O utilizador pode ser um utilizador final ou um administrador.
* **Permitido Estados de atividade**:
  * _Success_: Indica que um utilizador registado com êxito para a palavra-passe de reposição de acordo com a política atual. 
  * _Falha de_: Indica que um utilizador não conseguiu registar na reposição de palavra-passe. Pode selecionar a linha para ver os **razão do Estado de atividade** categoria para saber mais sobre por que motivo a falha ocorreu.

     >[!NOTE]
     >Falha não significa que um utilizador for não é possível repor a sua própria palavra-passe. Isso significa que eles não concluir o processo de registo. Se houver dados não verificados na sua conta está correta, por exemplo, um número de telefone que não é validado, mesmo que eles não têm a verificar este número de telefone, ainda pode utilizá-lo para repor a palavra-passe.

## <a name="next-steps"></a>Passos Seguintes

* [Como posso concluir uma implementação com êxito da SSPR?](howto-sspr-deployment.md)
* [Reponha ou altere a palavra-passe](../user-help/active-directory-passwords-update-your-own-password.md).
* [Registe-se na reposição personalizada de palavras-passe](../user-help/active-directory-passwords-reset-register.md).
* [Tem alguma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](howto-sspr-authenticationdata.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política da SSPR?](concept-sspr-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](howto-sspr-writeback.md)
* [Quais são todas as opções na SSPR e o que significam?](concept-sspr-howitworks.md)
* [Creio que algo está a funcionar incorretamente. Como posso resolver problemas da SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Registos de exemplo de auditoria de atividade da SSPR no Azure AD"
