---
title: Saiba quando um utilizador específico poderá aceder a uma aplicação
description: Como descobrir quando um utilizador criticamente importante pode aceder a uma aplicação que configurapara o fornecimento de utilizadores com AD Azure
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48370e2806b70d550bce95ceff3857a79561f247
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79264144"
---
# <a name="check-the-status-of-user-provisioning"></a>Verifique o estado do fornecimento de utilizadores

O serviço de provisionamento da AD Azure executa um ciclo inicial de provisionamento contra o sistema de origem e o sistema de destino, seguido de ciclos incrementais periódicos. Quando configurar o fornecimento de uma aplicação, pode verificar o estado atual do serviço de provisionamento e ver quando é que um utilizador poderá aceder a uma aplicação.

## <a name="view-the-provisioning-progress-bar"></a>Ver a barra de provisão de progresso

 Na página de **Provisioning** para uma app, pode ver o estado do serviço de provisionamento de AD Azure. A secção **Estado Atual** na parte inferior da página mostra se um ciclo de provisionamento começou a fornecer contas de utilizador. Pode ver o progresso do ciclo, ver quantos utilizadores e grupos foram aprovisionados, e ver quantas funções são criadas.

Quando configura o fornecimento automático pela primeira vez, a secção **Estado** atual na parte inferior da página mostra o estado do ciclo inicial de provisionamento. Esta secção atualiza cada vez que um ciclo incremental corre. Os seguintes detalhes são mostrados:
- O tipo de ciclo de provisionamento (inicial ou incremental) que está atualmente em execução ou foi concluído pela última vez.
- Uma barra de **progresso** que mostra a percentagem do ciclo de provisionamento que está concluído. A percentagem reflete a contagem de páginas aprovisionadas. Note que cada página pode conter vários utilizadores ou grupos, pelo que a percentagem não se relaciona diretamente com o número de utilizadores, grupos ou funções aprovisionadas.
- Um botão **Refresh** que pode utilizar para manter a vista atualizada.
- O número de **Utilizadores** e **Grupos** na loja de dados do conector. A contagem aumenta sempre que um objeto é adicionado ao âmbito de provisionamento. A contagem não diminuirá se um utilizador for eliminado ou eliminado duramente, uma vez que este não remove o objeto da loja de dados do conector. A contagem será recatada a primeira sincronização após a [reset](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) do CDS 
- Um link **'Ver Registos de Auditoria',** que abre os registos de fornecimento de Anúncios Azure para mais detalhes sobre todas as operações executadas pelo serviço de provisionamento do utilizador, incluindo o estado de provisionamento para utilizadores individuais (ver a secção de registos de fornecimento de [utilização](#use-provisioning-logs-to-check-a-users-provisioning-status) abaixo).

Após a conclusão de um ciclo de provisionamento, a secção estatísticas até **à data** mostra o número acumulado de utilizadores e grupos que foram previstos até à data, juntamente com a data de conclusão e duração do último ciclo. O **ID de Atividade** identifica exclusivamente o ciclo de provisionamento mais recente. O **Job ID** é um identificador único para o trabalho de provisionamento, e é específico da app no seu inquilino.

O progresso do provisionamento pode ser visto no portal Azure, no ** &gt; separador &gt; \[\] &gt; ** de aplicação de aplicações de aplicação de aplicações de aplicações de aplicações de aplicação de aplicações de aplicações de diretório ativo azure.

![Barra de progresso da página de provisionamento](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>Utilize registos de fornecimento para verificar o estado de provisionamento de um utilizador

Para ver o estado de provisionamento de um utilizador selecionado, consulte os registos de [provisionamento (pré-visualização)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) em Azure AD. Todas as operações executadas pelo serviço de fornecimento de utilizadores são registadas nos registos de fornecimento de AD Azure. Isto inclui todas as operações de leitura e escrita efetuadas para os sistemas de origem e alvo, e os dados do utilizador que foram lidos ou escritos durante cada operação.

Pode aceder aos registos de fornecimento no portal Azure selecionando registos de provisionamento de &gt; **aplicações** &gt; de **diretório ativo azure** **(pré-visualização)** na secção **Atividade.** Pode pesquisar os dados de provisionamento com base no nome do utilizador ou no identificador no sistema de origem ou no sistema de destino. Para mais detalhes, consulte [os registos de provisionamento (pré-visualização)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Os registos de fornecimento registam todas as operações efetuadas pelo serviço de provisionamento, incluindo:

* Consulta de Azure AD para utilizadores designados que estão no âmbito do provisionamento
* Consulta da aplicação-alvo para a existência desses utilizadores
* Comparando os objetos do utilizador entre o sistema
* Adicionar, atualizar ou desativar a conta de utilizador no sistema-alvo com base na comparação

Para obter mais informações sobre como ler os registos de fornecimento no portal Azure, consulte o guia de informação de [provisionamento](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Quanto tempo demorará a fornecer utilizadores?
Ao utilizar o fornecimento automático de utilizadores com uma aplicação, o Azure AD disponibiliza e atualiza automaticamente as contas dos utilizadores numa aplicação baseada em coisas como a atribuição de [utilizador e grupo](../manage-apps/assign-user-or-group-access-portal.md) num intervalo de tempo regularmente programado, normalmente a cada 40 minutos.

O tempo que um determinado utilizador demora a ser provisionado depende principalmente do facto de o seu trabalho de provisionamento estar a executar um ciclo inicial ou um ciclo incremental.

- Para o **ciclo inicial,** o tempo de trabalho depende de muitos fatores, incluindo o número de utilizadores e grupos de âmbito de aplicação, e o número total de utilizadores e grupos no sistema de origem. A primeira sincronização entre a Azure AD e uma aplicação pode demorar entre 20 minutos e várias horas, dependendo do tamanho do diretório Azure AD e do número de utilizadores no âmbito do provisionamento. Uma lista completa de fatores que afetam o desempenho inicial do ciclo é resumida mais tarde nesta secção.

- Para **ciclos incrementais** após o ciclo inicial, os tempos de trabalho tendem a ser mais rápidos (por exemplo, no prazo de 10 minutos), uma vez que o serviço de provisionamento armazena marcas de água que representam o estado de ambos os sistemas após o ciclo inicial, melhorando o desempenho das sincronizações subsequentes. O tempo de trabalho depende do número de alterações detetadas nesse ciclo de provisionamento. Se houver menos de 5.000 alterações de membros do utilizador ou do grupo, o trabalho pode terminar num único ciclo de provisionamento incremental. 

A tabela que se segue resume os tempos de sincronização para cenários comuns de provisionamento. Nestes cenários, o sistema de origem é Azure AD e o sistema alvo é uma aplicação SaaS. Os tempos de sincronização são derivados de uma análise estatística de trabalhos sincronizados para as aplicações SaaS ServiceNow, Workplace, Salesforce e G Suite.


| Configuração de âmbito | Utilizadores, grupos e membros no âmbito | Tempo inicial do ciclo | Tempo de ciclo incremental |
| -------- | -------- | -------- | -------- |
| Sync apenas utilizadores e grupos atribuídos |  < 1.000 |  < 30 minutos | < 30 minutos |
| Sync apenas utilizadores e grupos atribuídos |  1,000 - 10,000 | 142 - 708 minutos | < 30 minutos |
| Sync apenas utilizadores e grupos atribuídos |   10,000 - 100,000 | 1.170 - 2.340 minutos | < 30 minutos |
| Sincronizar todos os utilizadores e grupos em Azure AD |  < 1.000 | < 30 minutos  | < 30 minutos |
| Sincronizar todos os utilizadores e grupos em Azure AD |  1,000 - 10,000 | < 30 - 120 minutos | < 30 minutos |
| Sincronizar todos os utilizadores e grupos em Azure AD |  10,000 - 100,000  | 713 - 1.425 minutos | < 30 minutos |
| Sincronizar todos os utilizadores em Azure AD|  < 1.000  | < 30 minutos | < 30 minutos |
| Sincronizar todos os utilizadores em Azure AD | 1,000 - 10,000  | 43 - 86 minutos | < 30 minutos |


Para a configuração Sync apenas para **utilizadores e grupos atribuídos,** pode utilizar as seguintes fórmulas para determinar os tempos de **ciclo mínimo** e máximo esperados:

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Resumo dos fatores que influenciam o tempo que leva para completar um **ciclo inicial:**

- O número total de utilizadores e grupos no âmbito do provisionamento.

- O número total de utilizadores, grupos e membros do grupo presentes no sistema de origem (Azure AD).

- Se os utilizadores no âmbito de fornecimento são compatíveis com os utilizadores existentes na aplicação-alvo, ou precisam de ser criados pela primeira vez. Os trabalhos de sincronização para os quais todos os utilizadores são criados pela primeira vez demoram cerca do *dobro do tempo* de sincronização para os quais todos os utilizadores são compatíveis com os utilizadores existentes.

- Número de erros nos registos de [provisionamento](check-status-user-account-provisioning.md). O desempenho é mais lento se houver muitos erros e o serviço de provisionamento entrou em estado de quarentena. 

- Limites de taxa de pedido e estrangulamento implementados pelo sistema-alvo. Alguns sistemas-alvo implementam limites de taxa de pedido e estrangulamento, que podem ter impacto no desempenho durante grandes operações de sincronização. Nestas condições, uma aplicação que recebe demasiados pedidos rapidamente pode abrandar a sua taxa de resposta ou fechar a ligação. Para melhorar o desempenho, o conector precisa de se ajustar não enviando os pedidos da aplicação mais rapidamente do que a aplicação pode processá-los. Os conectores de provisionamento construídos pela Microsoft fazem este ajuste. 

- O número e tamanhos dos grupos designados. Sincronizar grupos atribuídos demora mais tempo do que sincronizar os utilizadores. Tanto o número como os tamanhos dos grupos designados têm impacto no desempenho. Se uma aplicação tiver [mapeamentos habilitados para a sincronização de objetos](customize-application-attributes.md#editing-group-attribute-mappings)de grupo, propriedades de grupo, como nomes de grupo e membros, são sincronizadas para além dos utilizadores. Estas sincronizações adicionais demorarão mais tempo do que apenas sincronizar objetos de utilizador.

- Se o desempenho se tornar um problema e estiver a tentar fornecer a maioria dos utilizadores e grupos no seu inquilino, utilize filtros de digitalização. Os filtros de deteção permitem afinar os dados que o serviço de fornecimento extrai da AD Azure filtrando os utilizadores com base em valores específicos de atributos. Para obter mais informações sobre filtros de deteção, consulte o [fornecimento de aplicações baseado no Atributo com filtros](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)de deteção .

## <a name="next-steps"></a>Passos seguintes
[Automatizar o fornecimento e o desprovisionamento de utilizadores às aplicações SaaS com o Diretório Ativo azure](user-provisioning.md)
