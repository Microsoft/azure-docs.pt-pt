---
title: Descubra quando um usuário específico poderá acessar um aplicativo
description: Como descobrir quando um usuário extremamente importante pode acessar um aplicativo que você configurou para o provisionamento de usuários com o Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13ef4abd303768c181c4bccf08338ad788330482
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77067061"
---
# <a name="check-the-status-of-user-provisioning"></a>Verificar o status do provisionamento do usuário

O serviço de provisionamento do Azure AD executa um ciclo de provisionamento inicial no sistema de origem e no sistema de destino, seguido por ciclos incrementais periódicos. Ao configurar o provisionamento para um aplicativo, você pode verificar o status atual do serviço de provisionamento e ver quando um usuário poderá acessar um aplicativo.

## <a name="view-the-provisioning-progress-bar"></a>Exibir a barra de progresso do provisionamento

 Na página de **Provisioning** para uma app, pode ver o estado do serviço de provisionamento de AD Azure. A secção **Estado Atual** na parte inferior da página mostra se um ciclo de provisionamento começou a fornecer contas de utilizador. Você pode ver o progresso do ciclo, ver quantos usuários e grupos foram provisionados e ver quantas funções são criadas.

Quando configura o fornecimento automático pela primeira vez, a secção **Estado** atual na parte inferior da página mostra o estado do ciclo inicial de provisionamento. Esta seção é atualizada toda vez que um ciclo incremental é executado. Os seguintes detalhes são mostrados:
- O tipo de ciclo de provisionamento (inicial ou incremental) que está em execução no momento ou foi concluído pela última vez.
- Uma barra de **progresso** que mostra a percentagem do ciclo de provisionamento que está concluído. A porcentagem reflete a contagem de páginas provisionada. Observe que cada página pode conter vários usuários ou grupos, portanto, o percentual não se correlaciona diretamente com o número de usuários, grupos ou funções provisionados.
- Um botão **Refresh** que pode utilizar para manter a vista atualizada.
- O número de **Utilizadores** e **Grupos** na loja de dados do conector. A contagem aumenta sempre que um objeto é adicionado ao escopo do provisionamento. A contagem não será desativada se um usuário for excluído de forma reversível ou excluído com hardware, pois isso não remove o objeto do armazenamento de dados do conector. A contagem será recatada a primeira sincronização após a [reset](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) do CDS 
- Um link **'Ver Registos de Auditoria',** que abre os registos de fornecimento de Anúncios Azure para mais detalhes sobre todas as operações executadas pelo serviço de provisionamento do utilizador, incluindo o estado de provisionamento para utilizadores individuais (ver a secção de registos de fornecimento de [utilização](#use-provisioning-logs-to-check-a-users-provisioning-status) abaixo).

Após a conclusão de um ciclo de provisionamento, a secção estatísticas até **à data** mostra o número acumulado de utilizadores e grupos que foram previstos até à data, juntamente com a data de conclusão e duração do último ciclo. O **ID de Atividade** identifica exclusivamente o ciclo de provisionamento mais recente. O **Job ID** é um identificador único para o trabalho de provisionamento, e é específico da app no seu inquilino.

O progresso do provisionamento pode ser visto no portal Azure, no **Azure Ative Directory &gt; Enterprise Apps &gt; \[nome** de aplicação\] &gt; do separador Provisioning.

![Barra de progresso da página de provisionamento](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>Usar logs de provisionamento para verificar o status de provisionamento de um usuário

Para ver o estado de provisionamento de um utilizador selecionado, consulte os registos de [provisionamento (pré-visualização)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) em Azure AD. Todas as operações executadas pelo serviço de provisionamento de usuário são registradas nos logs de provisionamento do Azure AD. Isso inclui todas as operações de leitura e gravação feitas nos sistemas de origem e de destino, bem como os dados de usuário que foram lidos ou gravados durante cada operação.

Pode aceder aos registos de fornecimento no portal Azure selecionando o **Azure Ative Directory** &gt; **Enterprise Apps** &gt; **Provisioning (pré-visualização)** na secção **Atividade.** Você pode pesquisar os dados de provisionamento com base no nome do usuário ou no identificador no sistema de origem ou no sistema de destino. Para mais detalhes, consulte [os registos de provisionamento (pré-visualização)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Os logs de provisionamento registram todas as operações executadas pelo serviço de provisionamento, incluindo:

* Consultando o Azure AD para usuários atribuídos que estão no escopo para provisionamento
* Consultando o aplicativo de destino para a existência desses usuários
* Comparando os objetos de usuário entre o sistema
* Adicionando, atualizando ou desabilitando a conta de usuário no sistema de destino com base na comparação

Para obter mais informações sobre como ler os registos de fornecimento no portal Azure, consulte o guia de informação de [provisionamento](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Quanto tempo será necessário para provisionar os usuários?
Ao utilizar o fornecimento automático de utilizadores com uma aplicação, o Azure AD disponibiliza e atualiza automaticamente as contas dos utilizadores numa aplicação baseada em coisas como a atribuição de [utilizador e grupo](../manage-apps/assign-user-or-group-access-portal.md) num intervalo de tempo regularmente programado, normalmente a cada 40 minutos.

O tempo necessário para que um determinado usuário seja provisionado depende principalmente de se o seu trabalho de provisionamento está executando um ciclo inicial ou um ciclo incremental.

- Para o **ciclo inicial,** o tempo de trabalho depende de muitos fatores, incluindo o número de utilizadores e grupos de âmbito de aplicação, e o número total de utilizadores e grupos no sistema de origem. A primeira sincronização entre o Azure AD e um aplicativo pode levar de 20 minutos a várias horas, dependendo do tamanho do diretório do Azure AD e do número de usuários no escopo para provisionamento. Uma lista abrangente de fatores que afetam o desempenho inicial do ciclo é resumida mais adiante nesta seção.

- Para **ciclos incrementais** após o ciclo inicial, os tempos de trabalho tendem a ser mais rápidos (por exemplo, no prazo de 10 minutos), uma vez que o serviço de provisionamento armazena marcas de água que representam o estado de ambos os sistemas após o ciclo inicial, melhorando o desempenho das sincronizações subsequentes. A hora do trabalho depende do número de alterações detectadas no ciclo de provisionamento. Se houver menos de 5.000 alterações de associação de usuário ou de grupo, o trabalho poderá ser concluído em um único ciclo de provisionamento incremental. 

A tabela a seguir resume os tempos de sincronização para cenários de provisionamento comuns. Nesses cenários, o sistema de origem é o Azure AD e o sistema de destino é um aplicativo SaaS. Os tempos de sincronização derivam de uma análise estatística de trabalhos de sincronização para os aplicativos SaaS ServiceNow, Workplace, Salesforce e G Suite.


| Configuração de escopo | Usuários, grupos e membros no escopo | Tempo de ciclo inicial | Tempo de ciclo incremental |
| -------- | -------- | -------- | -------- |
| Sincronizar somente usuários e grupos atribuídos |  < 1.000 |  < 30 minutos | < 30 minutos |
| Sincronizar somente usuários e grupos atribuídos |  1,000 - 10,000 | 142-708 minutos | < 30 minutos |
| Sincronizar somente usuários e grupos atribuídos |   10.000-100.000 | 1\.170-2.340 minutos | < 30 minutos |
| Sincronizar todos os usuários e grupos no Azure AD |  < 1.000 | < 30 minutos  | < 30 minutos |
| Sincronizar todos os usuários e grupos no Azure AD |  1,000 - 10,000 | < de 30-120 minutos | < 30 minutos |
| Sincronizar todos os usuários e grupos no Azure AD |  10.000-100.000  | 713-1.425 minutos | < 30 minutos |
| Sincronizar todos os usuários no Azure AD|  < 1.000  | < 30 minutos | < 30 minutos |
| Sincronizar todos os usuários no Azure AD | 1,000 - 10,000  | 43-86 minutos | < 30 minutos |


Para a configuração Sync apenas para **utilizadores e grupos atribuídos,** pode utilizar as seguintes fórmulas para determinar os tempos de **ciclo mínimo** e máximo esperados:

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Resumo dos fatores que influenciam o tempo que leva para completar um **ciclo inicial:**

- O número total de usuários e grupos no escopo para provisionamento.

- O número total de usuários, grupos e membros do grupo presentes no sistema de origem (Azure AD).

- Se os usuários no escopo para provisionamento são correspondidos aos usuários existentes no aplicativo de destino ou precisam ser criados pela primeira vez. Os trabalhos de sincronização para os quais todos os utilizadores são criados pela primeira vez demoram cerca do *dobro do tempo* de sincronização para os quais todos os utilizadores são compatíveis com os utilizadores existentes.

- Número de erros nos registos de [provisionamento](check-status-user-account-provisioning.md). O desempenho é mais lento se houver muitos erros e o serviço de provisionamento tiver entrado em um estado de quarentena. 

- Limites de taxa de solicitação e limitação implementados pelo sistema de destino. Alguns sistemas de destino implementam limites de taxa de solicitação e limitação, o que pode afetar o desempenho durante grandes operações de sincronização. Sob essas condições, um aplicativo que recebe muitas solicitações muito rápidas pode reduzir sua taxa de resposta ou fechar a conexão. Para melhorar o desempenho, o conector precisa ser ajustado por não enviar solicitações de aplicativo mais rápido do que o aplicativo pode processá-las. Os conectores de provisionamento criados pela Microsoft fazem esse ajuste. 

- O número e os tamanhos de grupos atribuídos. A sincronização de grupos atribuídos leva mais tempo do que a sincronização de usuários. O número e os tamanhos dos grupos atribuídos impactam o desempenho. Se uma aplicação tiver [mapeamentos habilitados para a sincronização de objetos](customize-application-attributes.md#editing-group-attribute-mappings)de grupo, propriedades de grupo, como nomes de grupo e membros, são sincronizadas para além dos utilizadores. Essas sincronizações adicionais levarão mais tempo do que apenas sincronizar objetos de usuário.

- Se o desempenho se tornar um problema e você estiver tentando provisionar a maioria dos usuários e grupos em seu locatário, use filtros de escopo. Os filtros de escopo permitem ajustar os dados que o serviço de provisionamento extrai do Azure AD filtrando os usuários com base em valores de atributo específicos. Para obter mais informações sobre filtros de deteção, consulte o [fornecimento de aplicações baseado no Atributo com filtros](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)de deteção .

## <a name="next-steps"></a>Passos seguintes
[Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](user-provisioning.md) (Automatizar o aprovisionamento e desaprovisionamento automático de utilizadores em aplicações SaaS no Azure Active Directory)
