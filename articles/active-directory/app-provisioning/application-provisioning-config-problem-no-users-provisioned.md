---
title: Os utilizadores não estão a ser a provisionados na minha aplicação
description: Como resolver problemas comuns quando não vê utilizadores a aparecer numa Aplicação de Galeria AD AZure que configuraste para o fornecimento de utilizadores com Azure AD
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 21f16a0b9c8b3b603735520d5dcdbf61601e1480
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90706491"
---
# <a name="no-users-are-being-provisioned"></a>Não existem utilizadores a ser aprovisionados 
>[!NOTE]
>A partir de 04/16/2020, alteramos o comportamento dos utilizadores que atribuíram a função de acesso predefinido. Consulte a secção abaixo para mais detalhes. 
>
Depois de o provisionamento automático ter sido configurado para uma aplicação (incluindo verificar se as credenciais de aplicação fornecidas à Azure AD para se conectar à app são válidas), então os utilizadores e/ou grupos são fornecidos à aplicação. O provisionamento é determinado pelas seguintes coisas:

-   Quais utilizadores e grupos foram **designados** para a aplicação. Note que o provisionamento de grupos aninhados ou grupos Microsoft 365 não é suportado. Para obter mais informações sobre a atribuição, consulte [Atribuir um utilizador ou grupo a uma aplicação empresarial no Azure Ative Directory](../manage-apps/assign-user-or-group-access-portal.md).
-   Se os **mapeamentos de atributos** estão ou não ativados e configurados para sincronizar atributos válidos do AD AD do AZure para a aplicação. Para obter mais informações sobre mapeamentos de atributos, consulte [Personalizar o fornecimento de cartões de pedidos de produtos para aplicações saas no Diretório Ativo Azure](customize-application-attributes.md).
-   Se existe ou não um **filtro de deteção** presente que está a filtrar os utilizadores com base em valores específicos de atributos. Para obter mais informações sobre filtros de deteção, consulte [o provisionamento de aplicações baseados em Atributos com filtros de escoamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
  
  
Se observar que os utilizadores não estão a ser a provisionados, consulte os [registos de Provisionamento (pré-visualização)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) em Azure AD. Procure por entradas de registo para um utilizador específico.

Pode aceder aos registos de provisionamento no portal Azure selecionando registos de provisionamento de aplicações empresariais do **Azure Ative** &gt; **Enterprise Apps** &gt; **Directory (pré-visualização)** na secção **Atividade.** Pode pesquisar os dados de fornecimento com base no nome do utilizador ou do identificador no sistema de origem ou no sistema alvo. Para mais informações, consulte [registos de provisionamento (pré-visualização)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Os registos de provisionamento registam todas as operações realizadas pelo serviço de fornecimento, incluindo consulta da Azure AD para utilizadores designados que estão em âmbito de provisionamento, consultando a aplicação-alvo para a existência desses utilizadores, comparando os objetos de utilizador entre o sistema. Em seguida, adicione, atualize ou desative a conta de utilizador no sistema-alvo com base na comparação.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Áreas de Problema Geral com Provisionamento a considerar
Abaixo está uma lista das áreas de problema geral que você pode perfurar se você tem uma ideia de por onde começar.

- [Serviço de fornecimento não parece começar](#provisioning-service-does-not-appear-to-start)
- [Os registos de provisionamento dizem que os utilizadores são ignorados e não a provisionados, mesmo que sejam atribuídos](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Serviço de fornecimento não parece começar
Se definir o **Estado de Provisionamento** para estar **ligado na** secção de Provisionamento de Nome de Aplicação de Aplicações de Aplicação de Aplicações do **Diretório Ativo Azure &gt; &gt; \[ \] &gt; ** do portal Azure. No entanto, não são apresentados outros detalhes de estado nessa página após recargas subsequentes, é provável que o serviço esteja em execução, mas ainda não completou um ciclo inicial. Verifique os **registos de Provisionamento (pré-visualização)** acima descritos para determinar que operações o serviço está a realizar e se existem erros.

>[!NOTE]
>Um ciclo inicial pode demorar entre 20 minutos e várias horas, dependendo do tamanho do diretório AZure AD e do número de utilizadores em possibilidade de provisionamento. As sincronizações subsequentes após o ciclo inicial são mais rápidas, uma vez que o serviço de fornecimento armazena marcas de água que representam o estado de ambos os sistemas após o ciclo inicial. O ciclo inicial melhora o desempenho das sincronizações subsequentes.
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Os registos de provisionamento dizem que os utilizadores são ignorados e não a provisionados, mesmo que sejam atribuídos

Quando um utilizador aparece como "ignorado" nos registos de provisionamento, é importante rever o separador **Passos** do registo para determinar o motivo. Seguem-se razões e resoluções comuns:

- **Foi configurado um filtro de deteção** **que está a filtrar o utilizador com base num valor de atributo**. Para obter mais informações sobre filtros de deteção, consulte [os filtros de deteção](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
- **O utilizador "não tem efetivamente direito".** Se vir esta mensagem de erro específica, é porque existe um problema com o registo de atribuição do utilizador armazenado no Azure AD. Para corrigir este problema, desatribua o utilizador (ou grupo) da aplicação e recoloe-o novamente. Para obter mais informações sobre a atribuição, consulte [o acesso ao utilizador ou ao grupo](../manage-apps/assign-user-or-group-access-portal.md).
- **Falta um atributo necessário ou não é preenchido para um utilizador.** Uma coisa importante a considerar ao configurar o provisionamento é rever e configurar os mapeamentos de atributos e fluxos de trabalho que definem quais as propriedades do utilizador (ou grupo) que fluem do Azure AD para a aplicação. Esta configuração inclui a definição da "propriedade correspondente" que é usada para identificar e combinar exclusivamente utilizadores/grupos entre os dois sistemas. Para obter mais informações sobre este importante processo, consulte [Personalizar o fornecimento de cartões de pedidos de pedidos de SaaS no Diretório Ativo Azure](customize-application-attributes.md).
- **Atribuir mapeamentos para grupos:** Provisionamento do nome do grupo e detalhes do grupo, além dos membros, se apoiados em algumas candidaturas. Pode ativar ou desativar esta funcionalidade ativando ou desativando o **Mapeamento** de objetos de grupo indicados no **separador Provisioning.** Se os grupos de provisionamento estiverem habilitados, certifique-se de rever os mapeamentos de atributos para garantir que um campo adequado está a ser utilizado para o "ID correspondente". O ID correspondente pode ser o nome de exibição ou pseudónimo de e-mail. O grupo e os seus membros não são a provisionados se a propriedade correspondente estiver vazia ou não povoada para um grupo em Azure AD.
## <a name="provisioning-users-assigned-to-the-default-access-role"></a>Provisionamento de utilizadores atribuídos à função de acesso predefinido
A função predefinida numa aplicação da galeria é chamada de função de "acesso predefinido". Historicamente, os utilizadores destacados para esta função não são a provisionados e são marcados como ignorados nos [registos de provisionamento](../reports-monitoring/concept-provisioning-logs.md) por "não terem direito eficazmente". 

**Comportamento para configurações de provisionamento criadas após 04/16/2020:** Os utilizadores atribuídos à função de acesso predefinido serão avaliados da mesma forma que todas as outras funções. Um utilizador que lhe seja atribuído o acesso predefinido não será ignorado como "não tem direito efetivo". 

**Comportamento para configurações de provisionamento criadas antes de 04/16/2020:** Nos próximos 3 meses, o comportamento continuará como é hoje. Os utilizadores com a função de acesso predefinido serão ignorados como não têm direito eficaz. Depois de julho de 2020, o comportamento será uniforme para todas as aplicações. Não vamos ignorar o provisionamento dos utilizadores com a função de acesso predefinido devido a "não ter direito eficazmente". Esta alteração será feita pela Microsoft, sem necessidade de ação do cliente. Se quiser garantir que estes utilizadores continuam a ser ignorados, mesmo após esta alteração, por favor aplique os filtros de deteção apropriados ou não desatribua o utilizador da aplicação para garantir que estão fora de alcance.  

Para perguntas sobre estas mudanças, por favor, contacte provisioningfeedback@microsoft.com
## <a name="next-steps"></a>Passos seguintes

[Azure AD Connect sync: Understanding Declarative Provisioning](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)