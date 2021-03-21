---
title: Saiba quando um utilizador específico poderá aceder a uma aplicação
description: Como saber quando um utilizador criticamente importante pode aceder a uma aplicação que configura para o fornecimento de utilizadores com Azure AD
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/03/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 890c4a8a5aec3b15b150908c64bb114bd85a61a1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99256767"
---
# <a name="check-the-status-of-user-provisioning"></a>Verifique o estado do fornecimento do utilizador

O serviço de fornecimento AZure AD executa um ciclo inicial de fornecimento contra o sistema de origem e o sistema alvo, seguido de ciclos incrementais periódicos. Ao configurar o fornecimento de uma aplicação, pode verificar o estado atual do serviço de fornecimento e ver quando um utilizador poderá aceder a uma aplicação.

## <a name="view-the-provisioning-progress-bar"></a>Ver a barra de provisão do progresso

 Na página **de Provisionamento** de uma aplicação, pode ver o estado do serviço de prestação de Ad Azure. A secção **Estado Atual** na parte inferior da página mostra se um ciclo de provisionamento começou a aloque as contas dos utilizadores. Pode observar o progresso do ciclo, ver quantos utilizadores e grupos foram a provisionados e ver quantas funções são criadas.

Quando configura pela primeira vez o provisionamento automático, a secção **Estado De Corrente** na parte inferior da página mostra o estado do ciclo inicial de provisionamento. Esta secção atualiza cada vez que um ciclo incremental é executado. São apresentados os seguintes detalhes:
- O tipo de ciclo de provisionamento (inicial ou incremental) que está atualmente em execução ou foi concluído pela última vez.
- Uma barra de **progresso** que mostra a percentagem do ciclo de provisionamento que terminou. A percentagem reflete a contagem de páginas a provisionadas. Note que cada página pode conter vários utilizadores ou grupos, de modo que a percentagem não está diretamente correlacionada com o número de utilizadores, grupos ou funções a provisionadas.
- Um botão **Refresh** que pode utilizar para manter a vista atualizada.
- O número de **Utilizadores** e **Grupos** na loja de dados do conector. A contagem aumenta sempre que um objeto é adicionado ao âmbito de provisão. A contagem não diminuirá se um utilizador for eliminado ou eliminado duramente, uma vez que este não remove o objeto da loja de dados do conector. A contagem será recalculada a primeira sincronização depois do CDS [ser reposto](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta) 
- Um link **'Registos de auditoria' view,** que abre os registos de provisionamento Azure AD para mais informações sobre todas as operações executadas pelo serviço de fornecimento de utilizadores, incluindo o estado de provisionamento para cada utilizador (consulte a secção de [registos de provisionamento de utilização](#use-provisioning-logs-to-check-a-users-provisioning-status) abaixo).

Após a conclusão de um ciclo de provisionamento, a secção **estatística até à data** mostra o número cumulativo de utilizadores e grupos que foram a provisionados até à data, juntamente com a data de conclusão e duração do último ciclo. O **ID de atividade** identifica exclusivamente o ciclo de provisionamento mais recente. O **Job ID** é um identificador único para o trabalho de provisionamento, e é específico para a app no seu inquilino.

O progresso do provisionamento pode ser visualizado no portal Azure, no **separador de &gt; &gt; \[ \] &gt; provisionamento do nome de aplicação Azure Ative Directory Enterprise Apps.**

![Barra de progresso da página de provisionamento](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>Utilize registos de provisionamento para verificar o estado de provisionamento de um utilizador

Para ver o estado de provisionamento de um utilizador selecionado, consulte os [registos de Provisionamento (pré-visualização)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) em Azure AD. Todas as operações executadas pelo serviço de fornecimento de utilizadores são registadas nos registos de fornecimento de AZure AD. Isto inclui todas as operações de leitura e escrita feitas para os sistemas de origem e alvo, e os dados do utilizador que foram lidos ou escritos durante cada operação.

Pode aceder aos registos de provisionamento no portal Azure selecionando registos de provisionamento de aplicações empresariais do **Azure Ative** &gt;  &gt; **Directory (pré-visualização)** na secção **Atividade.** Pode pesquisar os dados de fornecimento com base no nome do utilizador ou do identificador no sistema de origem ou no sistema alvo. Para mais informações, consulte [registos de provisionamento (pré-visualização)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Os registos de provisionamento registam todas as operações realizadas pelo serviço de fornecimento, incluindo:

* Consulta Azure AD para utilizadores designados que estão em âmbito de provisão
* Consulta da aplicação-alvo para a existência desses utilizadores
* Comparando os objetos de utilizador entre o sistema
* Adicionar, atualizar ou desativar a conta de utilizador no sistema-alvo com base na comparação

Para obter mais informações sobre como ler os registos de provisionamento no portal Azure, consulte o [guia de informação sobre](check-status-user-account-provisioning.md)o provisionamento .

## <a name="how-long-will-it-take-to-provision-users"></a>Quanto tempo vai demorar a providenciar aos utilizadores?
Ao utilizar o fornecimento automático de utilizadores com uma aplicação, o Azure AD fornece e atualiza automaticamente as contas dos utilizadores numa aplicação baseada em coisas como o utilizador e a atribuição de [grupos](../manage-apps/assign-user-or-group-access-portal.md) num intervalo de tempo regularmente programado, normalmente a cada 40 minutos.

O tempo que um determinado utilizador leva a ser a provisionado depende principalmente se o seu trabalho de provisionamento está a executar um ciclo inicial ou um ciclo incremental.

- Para o **ciclo inicial**, o tempo de trabalho depende de muitos fatores, incluindo o número de utilizadores e grupos em possibilidade de provisão, e o número total de utilizadores e grupos no sistema de origem. A primeira sincronização entre a Azure AD e uma aplicação pode demorar entre 20 minutos e várias horas, dependendo do tamanho do diretório AZure AD e do número de utilizadores em possibilidade de provisionamento. Uma lista completa de fatores que afetam o desempenho do ciclo inicial são resumidos mais tarde nesta secção.

- Para **ciclos incrementais** após o ciclo inicial, os tempos de trabalho tendem a ser mais rápidos (por exemplo, dentro de 10 minutos), uma vez que o serviço de fornecimento armazena marcas de água que representam o estado de ambos os sistemas após o ciclo inicial, melhorando o desempenho das sincronizações subsequentes. O tempo de trabalho depende do número de alterações detetadas nesse ciclo de provisionamento. Se houver menos de 5.000 alterações de membros de utilizadores ou grupos, o trabalho pode terminar dentro de um único ciclo de provisionamento incremental. 

O quadro seguinte resume os tempos de sincronização para cenários comuns de provisionamento. Nestes cenários, o sistema de origem é Azure AD e o sistema alvo é uma aplicação SaaS. Os tempos de sincronização são derivados de uma análise estatística de trabalhos de sincronização para as aplicações SaaS ServiceNow, Workplace, Salesforce e G Suite.


| Configuração de âmbito | Utilizadores, grupos e membros no âmbito | Tempo de ciclo inicial | Tempo incremental do ciclo |
| -------- | -------- | -------- | -------- |
| Apenas utilizadores e grupos designados por sincronização |  < 1.000 |  < 30 minutos | < 30 minutos |
| Apenas utilizadores e grupos designados por sincronização |  1,000 - 10,000 | 142 - 708 minutos | < 30 minutos |
| Apenas utilizadores e grupos designados por sincronização |   10,000 - 100,000 | 1.170 - 2.340 minutos | < 30 minutos |
| Sincronia todos os utilizadores e grupos em Azure AD |  < 1.000 | < 30 minutos  | < 30 minutos |
| Sincronia todos os utilizadores e grupos em Azure AD |  1,000 - 10,000 | < 30 - 120 minutos | < 30 minutos |
| Sincronia todos os utilizadores e grupos em Azure AD |  10,000 - 100,000  | 713 - 1.425 minutos | < 30 minutos |
| Sync todos os utilizadores em Azure AD|  < 1.000  | < 30 minutos | < 30 minutos |
| Sync todos os utilizadores em Azure AD | 1,000 - 10,000  | 43 - 86 minutos | < 30 minutos |

Apenas para a configuração **Sync designado utilizador e grupos,** pode utilizar as seguintes fórmulas para determinar os tempos de **ciclo** mínimo e máximo esperados:

- Minutos mínimos = 0,01 x [Número de utilizadores, grupos e membros do grupo designados]
- Minutos máximos = 0,08 x [Número de utilizadores, grupos e membros do grupo designados]

Resumo dos fatores que influenciam o tempo que leva para completar um **ciclo inicial:**

- O número total de utilizadores e grupos em possibilidade de provisionamento.

- O número total de utilizadores, grupos e membros do grupo presentes no sistema de origem (Azure AD).

- Se os utilizadores em possibilidade de provisionamento são compatíveis com os utilizadores existentes na aplicação-alvo, ou se precisam de ser criados pela primeira vez. Os trabalhos de sincronização para os quais todos os utilizadores são *criados* pela primeira vez demoram cerca do dobro do tempo em que os trabalhos de sincronização para os quais todos os utilizadores são compatíveis com os utilizadores existentes.

- Número de erros nos [registos de provisionamento](check-status-user-account-provisioning.md). O desempenho é mais lento se houver muitos erros e o serviço de fornecimento entrou em estado de quarentena. 

- Limites de taxa de pedido e estrangulamento implementados pelo sistema-alvo. Alguns sistemas-alvo implementam limites de taxa de pedido e estrangulamento, que podem ter impacto no desempenho durante grandes operações de sincronização. Nestas condições, uma aplicação que recebe muitos pedidos demasiado rápido pode abrandar a sua taxa de resposta ou fechar a ligação. Para melhorar o desempenho, o conector precisa de se ajustar não enviando os pedidos da aplicação mais rapidamente do que a aplicação pode processá-los. Os conectores de provisionamento construídos pela Microsoft fazem este ajuste. 

- O número e tamanhos dos grupos designados. Sincronizar grupos atribuídos demora mais tempo do que sincronizar os utilizadores. Tanto o número como os tamanhos dos grupos atribuídos têm impacto no desempenho. Se uma aplicação tiver [mapeamentos ativados para sincronização de objetos](customize-application-attributes.md#editing-group-attribute-mappings)de grupo, propriedades de grupo como nomes de grupo e membros são sincronizadas para além dos utilizadores. Estas sincronizações adicionais demorarão mais do que sincronizar apenas objetos do utilizador.

- Se o desempenho se tornar um problema e estiver a tentar provisionar a maioria dos utilizadores e grupos no seu inquilino, utilize filtros de deteção. Os filtros de deteção permitem-lhe afinar os dados que o serviço de fornecimento extrai do Azure AD filtrando os utilizadores com base em valores específicos de atributos. Para obter mais informações sobre filtros de deteção, consulte [o provisionamento de aplicações baseados em Atributos com filtros de escoamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Passos seguintes
[Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](user-provisioning.md) (Automatizar o aprovisionamento e desaprovisionamento automático de utilizadores em aplicações SaaS no Azure Active Directory)