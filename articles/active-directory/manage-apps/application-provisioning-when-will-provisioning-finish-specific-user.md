---
title: Descubra quando um usuário específico poderá acessar um aplicativo | Microsoft Docs
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
ms.openlocfilehash: 7d3b334df8cd24a1d8ca88c8ac2e3117bdd24d8b
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057772"
---
# <a name="check-the-status-of-user-provisioning"></a>Verificar o status do provisionamento do usuário

O serviço de provisionamento do Azure AD executa um ciclo de provisionamento inicial no sistema de origem e no sistema de destino, seguido por ciclos incrementais periódicos. Ao configurar o provisionamento para um aplicativo, você pode verificar o status atual do serviço de provisionamento e ver quando um usuário poderá acessar um aplicativo.

## <a name="view-the-provisioning-progress-bar"></a>Exibir a barra de progresso do provisionamento

 Na página de **provisionamento** de um aplicativo, você pode exibir o status do serviço de provisionamento do Azure AD. A seção **status atual** na parte inferior da página mostra se um ciclo de provisionamento começou a provisionar contas de usuário. Você pode ver o progresso do ciclo, ver quantos usuários e grupos foram provisionados e ver quantas funções são criadas.

Quando você configura o provisionamento automático pela primeira vez, a seção **status atual** na parte inferior da página mostra o status do ciclo de provisionamento inicial. Esta seção é atualizada toda vez que um ciclo incremental é executado. Os seguintes detalhes são mostrados:
- O tipo de ciclo de provisionamento (inicial ou incremental) que está em execução no momento ou foi concluído pela última vez.
- Uma **barra de progresso** que mostra a porcentagem do ciclo de provisionamento que foi concluída. A porcentagem reflete a contagem de páginas provisionada. Observe que cada página pode conter vários usuários ou grupos, portanto, o percentual não se correlaciona diretamente com o número de usuários, grupos ou funções provisionados.
- Um botão de **atualização** que você pode usar para manter a exibição atualizada.
- O número de **usuários** e **grupos** provisionados e o número de funções criadas. Durante o ciclo inicial, o número de **usuários** conta em 1 quando um usuário é criado ou atualizado e é contado por 1 quando um usuário é excluído. Durante um ciclo incremental, as atualizações do usuário não afetam a contagem de **usuários** ; o número é alterado somente quando os usuários são criados ou excluídos.
- Um link **Exibir logs de auditoria** , que abre os logs de provisionamento do Azure ad para obter detalhes sobre todas as operações executadas pelo serviço de provisionamento de usuário, incluindo o status de provisionamento para usuários individuais (consulte a seção [usar logs de provisionamento](#use-provisioning-logs-to-check-a-users-provisioning-status) abaixo).

Após a conclusão de um ciclo de provisionamento, a seção **estatísticas até a data** mostra os números cumulativos de usuários e grupos que foram provisionados até a data, junto com a data de conclusão e a duração do último ciclo. A **ID da atividade** identifica exclusivamente o ciclo de provisionamento mais recente. A **ID do trabalho** é um identificador exclusivo para o trabalho de provisionamento e é específica para o aplicativo em seu locatário.

O progresso do provisionamento pode ser exibido na portal do Azure, na guia **provisionamento de &gt; nome\] &gt; &gt; de aplicativo do \[Azure Active Directory Enterprise apps** .

![Barra de progresso da página de provisionamento](media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>Usar logs de provisionamento para verificar o status de provisionamento de um usuário

Para ver o status de provisionamento de um usuário selecionado, consulte os [logs de provisionamento (versão prévia)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) no Azure AD. Todas as operações executadas pelo serviço de provisionamento de usuário são registradas nos logs de provisionamento do Azure AD. Isso inclui todas as operações de leitura e gravação feitas nos sistemas de origem e de destino, bem como os dados de usuário que foram lidos ou gravados durante cada operação.

Você pode acessar os logs de provisionamento no portal do Azure selecionando **Azure Active Directory** &gt; **aplicativos** &gt; empresariais **logs de provisionamento (versão prévia)** na seção **atividade** . Você pode pesquisar os dados de provisionamento com base no nome do usuário ou no identificador no sistema de origem ou no sistema de destino. Para obter detalhes, consulte [Provisionando logs (versão prévia)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Os logs de provisionamento registram todas as operações executadas pelo serviço de provisionamento, incluindo:

* Consultando o Azure AD para usuários atribuídos que estão no escopo para provisionamento
* Consultando o aplicativo de destino para a existência desses usuários
* Comparando os objetos de usuário entre o sistema
* Adicionando, atualizando ou desabilitando a conta de usuário no sistema de destino com base na comparação

Para obter mais informações sobre como ler os logs de provisionamento no portal do Azure, consulte o guia de [relatórios de provisionamento](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Quanto tempo será necessário para provisionar os usuários?
Ao usar o provisionamento automático de usuário com um aplicativo, o Azure AD provisiona e atualiza automaticamente as contas de usuário em um aplicativo com base em coisas como [atribuição de usuário e grupo](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) em um intervalo de tempo agendado regularmente, normalmente a cada 40 minutos.

O tempo necessário para que um determinado usuário seja provisionado depende principalmente de se o seu trabalho de provisionamento está executando um ciclo inicial ou um ciclo incremental.

- Para o **ciclo inicial**, o tempo de trabalho depende de muitos fatores, incluindo o número de usuários e grupos no escopo para provisionamento e o número total de usuários e grupo no sistema de origem. A primeira sincronização entre o Azure AD e um aplicativo pode levar de 20 minutos a várias horas, dependendo do tamanho do diretório do Azure AD e do número de usuários no escopo para provisionamento. Uma lista abrangente de fatores que afetam o desempenho inicial do ciclo é resumida mais adiante nesta seção.

- Para **ciclos incrementais** após o ciclo inicial, os tempos de trabalho tendem a ser mais rápidos (por exemplo, em 10 minutos), pois o serviço de provisionamento armazena marcas d' água que representam o estado de ambos os sistemas após o ciclo inicial, melhorando o desempenho do subseqüente sincronizações. A hora do trabalho depende do número de alterações detectadas no ciclo de provisionamento. Se houver menos de 5.000 alterações de associação de usuário ou de grupo, o trabalho poderá ser concluído em um único ciclo de provisionamento incremental. 

A tabela a seguir resume os tempos de sincronização para cenários de provisionamento comuns. Nesses cenários, o sistema de origem é o Azure AD e o sistema de destino é um aplicativo SaaS. Os tempos de sincronização derivam de uma análise estatística de trabalhos de sincronização para os aplicativos SaaS ServiceNow, Workplace, Salesforce e G Suite.


| Configuração de escopo | Usuários, grupos e membros no escopo | Tempo de ciclo inicial | Tempo de ciclo incremental |
| -------- | -------- | -------- | -------- |
| Sincronizar somente usuários e grupos atribuídos |  < 1.000 |  < 30 minutos | < 30 minutos |
| Sincronizar somente usuários e grupos atribuídos |  1\.000 - 10.000 | 142-708 minutos | < 30 minutos |
| Sincronizar somente usuários e grupos atribuídos |   10.000-100.000 | 1\.170-2.340 minutos | < 30 minutos |
| Sincronizar todos os usuários e grupos no Azure AD |  < 1.000 | < 30 minutos  | < 30 minutos |
| Sincronizar todos os usuários e grupos no Azure AD |  1\.000 - 10.000 | < de 30-120 minutos | < 30 minutos |
| Sincronizar todos os usuários e grupos no Azure AD |  10.000-100.000  | 713-1.425 minutos | < 30 minutos |
| Sincronizar todos os usuários no Azure AD|  < 1.000  | < 30 minutos | < 30 minutos |
| Sincronizar todos os usuários no Azure AD | 1\.000 - 10.000  | 43-86 minutos | < 30 minutos |


Para a sincronização de configuração, **somente usuários e grupos atribuídos**, você pode usar as fórmulas a seguir para determinar os tempos de **ciclo inicial** mínimo e máximo aproximado:

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Resumo dos fatores que influenciam o tempo necessário para concluir um **ciclo inicial**:

- O número total de usuários e grupos no escopo para provisionamento.

- O número total de usuários, grupos e membros do grupo presentes no sistema de origem (Azure AD).

- Se os usuários no escopo para provisionamento são correspondidos aos usuários existentes no aplicativo de destino ou precisam ser criados pela primeira vez. Os trabalhos de sincronização para os quais todos os usuários são criados pela primeira vez levam aproximadamente *duas vezes mais* tempo que os trabalhos de sincronização para os quais todos os usuários têm correspondência com os usuários existentes.

- Número de erros nos [logs de provisionamento](check-status-user-account-provisioning.md). O desempenho é mais lento se houver muitos erros e o serviço de provisionamento tiver entrado em um estado de quarentena. 

- Limites de taxa de solicitação e limitação implementados pelo sistema de destino. Alguns sistemas de destino implementam limites de taxa de solicitação e limitação, o que pode afetar o desempenho durante grandes operações de sincronização. Sob essas condições, um aplicativo que recebe muitas solicitações muito rápidas pode reduzir sua taxa de resposta ou fechar a conexão. Para melhorar o desempenho, o conector precisa ser ajustado por não enviar solicitações de aplicativo mais rápido do que o aplicativo pode processá-las. Os conectores de provisionamento criados pela Microsoft fazem esse ajuste. 

- O número e os tamanhos de grupos atribuídos. A sincronização de grupos atribuídos leva mais tempo do que a sincronização de usuários. O número e os tamanhos dos grupos atribuídos impactam o desempenho. Se um aplicativo tiver [mapeamentos habilitados para sincronização de objeto de grupo](customize-application-attributes.md#editing-group-attribute-mappings), as propriedades de grupo, como nomes de grupo e associações, serão sincronizadas além dos usuários. Essas sincronizações adicionais levarão mais tempo do que apenas sincronizar objetos de usuário.

## <a name="next-steps"></a>Passos seguintes
[Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) (Automatizar o aprovisionamento e desaprovisionamento automático de utilizadores em aplicações SaaS no Azure Active Directory)
