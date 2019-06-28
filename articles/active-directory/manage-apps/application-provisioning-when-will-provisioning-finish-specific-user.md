---
title: Saiba de que quando um utilizador específico poderá aceder a uma aplicação | Documentos da Microsoft
description: Como saber quando um usuário extremamente importante ser capaz de aceder a uma aplicação que configurou para o aprovisionamento de utilizadores com o Azure AD
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
ms.date: 06/12/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fd6b70e7a4542a4cad2ee95fa280ddf8fbe6553
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310044"
---
# <a name="check-the-status-of-user-provisioning"></a>Verificar o estado de aprovisionamento de utilizadores

O serviço de aprovisionamento do AD do Azure é executado um ciclo de aprovisionamento inicial contra o sistema de origem e o sistema de destino, seguido de ciclos de incrementais periódicos. Quando configurar o aprovisionamento para uma aplicação, pode verificar o estado atual do serviço de aprovisionamento e ver quando um utilizador poderá aceder a uma aplicação.

## <a name="view-the-provisioning-progress-bar-preview"></a>Ver o aprovisionamento de progresso de barra (pré-visualização)

 Sobre o **aprovisionamento** página para uma aplicação, pode ver o estado do Azure AD do serviço de aprovisionamento. O **Status atual** secção da parte inferior da página mostra se um ciclo de aprovisionamento iniciou o aprovisionamento de contas de utilizador. Pode ver o progresso do ciclo de, veja quantos utilizadores e grupos foram aprovisionados e ver quantas funções são criadas.

Quando configura pela primeira vez o aprovisionamento automático, o **Status atual** seção na parte inferior da página mostra o estado do ciclo do provisionamento inicial. Esta secção atualiza sempre que um ciclo incremental é executada. São mostrados os seguintes detalhes:
- O tipo de aprovisionamento ciclo (inicial ou incremental) que está atualmente em execução ou pela última vez foi concluído.
- R **barra de progresso** que mostra a percentagem de ciclo de aprovisionamento que foi concluída. A percentagem reflete a contagem de páginas aprovisionado. Tenha em atenção que cada página pode conter vários utilizadores ou grupos, para que a percentagem diretamente não está correlacionado com o número de utilizadores, grupos ou funções aprovisionadas.
- R **atualizar** botão pode utilizar para manter o view atualizado.
- O número de **usuários** e **grupos** aprovisionado e o número de funções criadas. Durante o ciclo inicial, o **utilizadores** número contagens de cópia de segurança por 1 quando um utilizador é criado ou atualizado, e ele contagens para baixo em 1 quando um utilizador é eliminado. Durante um ciclo incremental, o utilizador atualizações não afetam o **utilizadores** contagem; as alterações no número de apenas quando os utilizadores são criados ou eliminados.
- Uma **ver registos de auditoria** ligação, que abre a auditoria do Azure AD registos para obter detalhes sobre todas as operações executadas pelo serviço de aprovisionamento do utilizador, incluindo o provisionamento de estado para utilizadores individuais (consulte a [deregistosdeauditoriadeuso](#use-audit-logs-to-check-a-users-provisioning-status)secção abaixo).

Depois de um ciclo de aprovisionamento está concluído, o **estatísticas até à data** secção mostra os números cumulativos de utilizadores e grupos que foram aprovisionados até à data, juntamente com a data de conclusão e a duração do ciclo do último. O **ID de atividade** identifica exclusivamente o ciclo de aprovisionamento mais recente. O **ID da tarefa** é um identificador exclusivo para a tarefa de aprovisionamento e é específica para a aplicação no seu inquilino.

O progresso de aprovisionamento pode visto no portal do Azure, no **do Azure Active Directory &gt; aplicações empresariais &gt; \[nome da aplicação\] &gt; aprovisionamento** separador.

![Barra de progresso do aprovisionamento página](media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-audit-logs-to-check-a-users-provisioning-status"></a>Registos de auditoria de utilização para verificar o estado de aprovisionamento de um utilizador

Para ver o estado de aprovisionamento de um utilizador selecionado, consulte os registos de auditoria no Azure AD. Todas as operações a executar o serviço de aprovisionamento de utilizador são registadas no Azure AD registos de auditoria. Isso inclui todas as operações feitas para os sistemas de origem e de destino e os dados de utilizador que foi lidos ou gravados durante cada operação de leitura e escrita.

Os registos de auditoria de aprovisionamento podem ser acedidos no portal do Azure, no **do Azure Active Directory &gt; aplicações empresariais &gt; \[nome da aplicação\] &gt; registos de auditoria** separador. Filtrar os registos a **aprovisionamento da conta** categoria para ver apenas os eventos de aprovisionamento para essa aplicação. Pode procurar utilizadores com base no "ID correspondente" que foi configurado para os mesmos nos mapeamentos de atributos. 

Por exemplo, se tiver configurado o "nome principal de utilizador" ou "endereço de e-mail" como o atributo correspondente no lado do Azure AD e o utilizador está a ser aprovisionamento não tem um valor de "audrey@contoso.com", em seguida, procurar os registos de auditoria para "audrey@contoso.com" e, em seguida, reveja as entradas devolvido.

Os registos de auditoria aprovisionamento registram todas as operações executadas pelo serviço de aprovisionamento, incluindo:

* Consultar o Azure AD para os utilizadores atribuídos, que se encontrem no âmbito de aprovisionamento
* Consultar a aplicação de destino para a existência desses utilizadores
* Comparar os objetos de utilizador entre o sistema
* Adicionar, atualizar ou desabilitar a conta de utilizador no sistema de destino com base na comparação

Para obter mais informações sobre como ler os registos de auditoria no portal do Azure, consulte a [guia de relatórios de aprovisionamento](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Quanto tempo necessário para aprovisionar os utilizadores?
Ao utilizar o aprovisionamento automático de utilizadores com uma aplicação, do Azure AD Aprovisiona e automaticamente atualizações de contas de utilizador numa aplicação com base nas coisas como [atribuição de utilizador e grupo](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) regularmente agendada no tempo de intervalo, normalmente cada 10 minutos.

O tempo necessário para um determinado usuário a ser aprovisionado depende principalmente se a tarefa de aprovisionamento está a executar uma sincronização inicial ou uma sincronização incremental.

- Para **inicial sincronizações**, o tempo de trabalho depende de vários fatores, incluindo o número de utilizadores e grupos no âmbito de aprovisionamento e o número total de utilizadores e de grupo no sistema de origem. A primeira sincronização entre o Azure AD e uma aplicação pode demorar entre 20 minutos a várias horas, dependendo do tamanho do diretório do Azure AD e o número de utilizadores no âmbito de aprovisionamento. Uma lista abrangente dos fatores que afetam o desempenho da sincronização inicial são resumidas posteriormente nesta secção.

- Para **sincronizações incrementais** após a sincronização inicial, as horas de trabalho tendem a ser mais rápida (por exemplo, dentro de 10 minutos), como o serviço de aprovisionamento armazena as marcas d'água que representam o estado dos dois sistemas após a sincronização inicial, melhorando o desempenho de sincronizações subsequentes. O tempo de trabalho depende o número de alterações detetado nesse ciclo de aprovisionamento. Se existirem menos de 5.000 utilizador ou alterações de associação de grupo, pode concluir a tarefa dentro de um ciclo de aprovisionamento incremental único. 

A tabela seguinte resume as horas de sincronização para cenários comuns de aprovisionamento. Nestes cenários, o sistema de origem é o Azure AD e o sistema de destino é uma aplicação SaaS. Os tempos de sincronização são derivados de uma análise estatística dos trabalhos de sincronização para as aplicações de SaaS, ServiceNow, o local de trabalho, o Salesforce e o G Suite.


| Configuração de âmbito | Os utilizadores, grupos e membros no âmbito | Tempo de sincronização inicial | Hora de sincronização incremental |
| -------- | -------- | -------- | -------- |
| Sincronizar os utilizadores atribuídos e apenas a grupos |  < 1,000 |  < a 30 minutos | < a 30 minutos |
| Sincronizar os utilizadores atribuídos e apenas a grupos |  1\.000 - 10.000 | 142 - minutos 708 | < a 30 minutos |
| Sincronizar os utilizadores atribuídos e apenas a grupos |   10,000 - 100,000 | 1,170 - minutos 2,340 | < a 30 minutos |
| Sincronizar todos os utilizadores e grupos no Azure AD |  < 1,000 | < a 30 minutos  | < a 30 minutos |
| Sincronizar todos os utilizadores e grupos no Azure AD |  1\.000 - 10.000 | < 120 de 30 minutos | < a 30 minutos |
| Sincronizar todos os utilizadores e grupos no Azure AD |  10,000 - 100,000  | 713 - minutos 1,425 | < a 30 minutos |
| Sincronizar todos os utilizadores no Azure AD|  < 1,000  | < a 30 minutos | < a 30 minutos |
| Sincronizar todos os utilizadores no Azure AD | 1\.000 - 10.000  | 43 - minutos de 86 | < a 30 minutos |


Para a configuração **sincronização atribuído apenas grupos de utilizadores e**, pode utilizar as seguintes fórmulas para determinar a aproximado mínimo e máximo esperado **inicial sincronização** vezes:

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Resumo dos fatores que influenciam o tempo que demora a concluir uma **inicial sincronização**:

- O número total de utilizadores e grupos no âmbito de aprovisionamento.

- O número total de utilizadores, grupos e membros do grupo presentes no sistema de origem (Azure AD).

- Se os utilizadores no âmbito de aprovisionamento são correspondidos com os utilizadores existentes no aplicativo de destino, ou tem de ser criado pela primeira vez. Tarefas de sincronização para o qual todos os utilizadores são criados pela primeira vez demorar cerca de *duas vezes desde* como sincronizar tarefas para o qual todos os utilizadores são correspondidos com os utilizadores existentes.

- Número de erros no [registos de auditoria](check-status-user-account-provisioning.md). O desempenho é mais lento, se existirem muitos erros e o serviço de aprovisionamento tornou-se num Estado de quarentena.    

- Limites de velocidade e a limitação implementada pelo sistema de destino do pedido. Alguns sistemas de destino implementam limites de velocidade do pedido e limitação, que pode afetar o desempenho durante as operações de sincronização de grandes dimensões. Nestas condições, uma aplicação que recebe demasiados pedidos demasiado rápidos poderá atrasar a respetiva taxa de resposta ou fechar a ligação. Para melhorar o desempenho, o conector tem de ajustar por não enviar os pedidos de aplicações mais rapidamente do que a aplicação pode processá-los. Aprovisionamento conectores criados pela Microsoft fazer esta alteração. 

- O número e os tamanhos dos grupos atribuídos. A sincronizar grupos atribuídos exige mais tempo a sincronizar os utilizadores. O número e os tamanhos dos grupos atribuídos afetam o desempenho. Se tiver um aplicativo [mapeamentos ativados para sincronização de objetos de grupo](customize-application-attributes.md#editing-group-attribute-mappings), propriedades de grupo, tais como nomes de grupo e as associações são sincronizadas, além de usuários. Estes sincronizações adicionais irão demorar mais tempo do que apenas a sincronizar objetos de utilizador.

## <a name="next-steps"></a>Passos Seguintes
[Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) (Automatizar o aprovisionamento e desaprovisionamento automático de utilizadores em aplicações SaaS no Azure Active Directory)
