---
title: Projetando a sua implementação de registos Azure Monitor / Microsoft Docs
description: Este artigo descreve as considerações e recomendações para os clientes que se preparam para implantar um espaço de trabalho no Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/20/2019
ms.openlocfilehash: 6bdc7a087e60791ba3e3367aca3ea3a4500478ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91534204"
---
# <a name="designing-your-azure-monitor-logs-deployment"></a>Criar a implementação de Registos do Azure Monitor

O Azure Monitor armazena dados de [registo](data-platform-logs.md) num espaço de trabalho log Analytics, que é um recurso Azure e um recipiente onde os dados são recolhidos, agregados e serve como uma fronteira administrativa. Embora possa implantar um ou mais espaços de trabalho na sua subscrição do Azure, existem várias considerações que deve entender para garantir que a sua implementação inicial está a seguir as nossas diretrizes para lhe proporcionar uma implementação rentável, gerível e escalável que satisfaz as necessidades da sua organização.

Os dados num espaço de trabalho são organizados em tabelas, cada uma das quais armazena diferentes tipos de dados e tem o seu próprio conjunto único de propriedades com base no recurso que gera os dados. A maioria das fontes de dados escreverá para as suas próprias tabelas num espaço de trabalho log analytics.

![Modelo de dados do espaço de trabalho exemplo](./media/design-logs-deployment/logs-data-model-01.png)

Um espaço de trabalho Log Analytics fornece:

* Uma localização geográfica para armazenamento de dados.
* Isolamento de dados, concedendo a diferentes utilizadores direitos de acesso seguindo uma das nossas estratégias de design recomendadas.
* Possibilidade de configuração de configurações como [o nível de preços,](./manage-cost-storage.md#changing-pricing-tier) [retenção](./manage-cost-storage.md#change-the-data-retention-period)e [cobertura de dados](./manage-cost-storage.md#manage-your-maximum-daily-data-volume).

Os espaços de trabalho são hospedados em aglomerados físicos. Por padrão, o sistema está a criar e gerir estes clusters. Espera-se que os clientes que ingerem mais de 4TB/dia criem os seus próprios clusters dedicados para os seus espaços de trabalho - permite-lhes um melhor controlo e uma maior taxa de ingestão.

Este artigo fornece uma visão detalhada das considerações de design e migração, visão geral do controlo de acesso e uma compreensão das implementações de design que recomendamos para a sua organização de TI.



## <a name="important-considerations-for-an-access-control-strategy"></a>Considerações importantes para uma estratégia de controlo de acessos

A identificação do número de espaços de trabalho de que necessita é influenciada por um ou mais dos seguintes requisitos:

* É uma empresa global e precisa de dados de registo armazenados em regiões específicas por razões de soberania ou conformidade de dados.
* Estiver a utilizar o Azure e pretender evitar custos de transferência de dados de saída ao ter uma área de trabalho na mesma região dos recursos do Azure que gere.
* Você gere vários departamentos ou grupos empresariais, e você quer que cada um veja os seus próprios dados, mas não dados de outros. Além disso, não existe qualquer requisito de negócio para uma visão consolidada do departamento transversal ou do grupo empresarial.

As organizações de TI de hoje são modeladas seguindo um híbrido centralizado, descentralizado ou intermediário de ambas as estruturas. Como resultado, os seguintes modelos de implantação do espaço de trabalho têm sido comumente utilizados para mapear para uma destas estruturas organizacionais:

* **Centralizado**: Todos os registos são armazenados num espaço de trabalho central e administrados por uma única equipa, com o Azure Monitor a proporcionar acesso diferenciado por equipa. Neste cenário, é fácil de gerir, pesquisar através de recursos e registos de correlação cruzada. O espaço de trabalho pode crescer significativamente dependendo da quantidade de dados recolhidos a partir de múltiplos recursos na sua subscrição, com despesas administrativas adicionais para manter o controlo de acesso a diferentes utilizadores. Este modelo é conhecido como "hub and spoke".
* **Descentralizado**: Cada equipa tem o seu próprio espaço de trabalho criado num grupo de recursos que possuem e gerem, e os dados de registo são segregados por recurso. Neste cenário, o espaço de trabalho pode ser mantido seguro e o controlo de acesso é consistente com o acesso a recursos, mas é difícil cruzar registos. Os utilizadores que precisam de uma visão ampla de muitos recursos não podem analisar os dados de forma significativa.
* **Híbrido**: Os requisitos de conformidade da auditoria de segurança complicam ainda mais este cenário porque muitas organizações implementam ambos os modelos de implantação em paralelo. Isto geralmente resulta numa configuração complexa, cara e difícil de manter com lacunas na cobertura de registos.

Ao utilizar os agentes do Log Analytics para recolher dados, tem de compreender o seguinte para planear a implementação do seu agente:

* Para recolher dados de agentes do Windows, pode [configurar cada agente para reportar a um ou mais espaços de trabalho](./agent-windows.md), mesmo quando está a reportar a um grupo de gestão de Gestores de Operações do System Center. O agente windows pode reportar até quatro espaços de trabalho.
* O agente Linux não suporta multi-homing e só pode reportar-se a um único espaço de trabalho.

Se estiver a utilizar o Gestor de Operações do Centro de Sistema 2012 R2 ou mais tarde:

* Cada grupo de gestão de Gestores de Operações pode ser [ligado a apenas um espaço de trabalho.](./om-agents.md) 
* Os computadores Linux que reportam a um grupo de gestão devem ser configurados para reportar diretamente a um espaço de trabalho log analytics. Se os seus computadores Linux já estão a reportar diretamente a um espaço de trabalho e pretende monitorá-los com o Gestor de Operações, siga estes passos para [reportar a um grupo de gestão de Gestores de Operações.](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group) 
* Pode instalar o agente Do Windows do Log Analytics no computador Windows e informá-lo para ambos os Gestores de Operações integrados com um espaço de trabalho e um espaço de trabalho diferente.

## <a name="access-control-overview"></a>Descrição geral do controlo de acesso

Com o controlo de acesso baseado em funções (RBAC), pode conceder aos utilizadores e grupos apenas a quantidade de acesso de que precisam para trabalhar com dados de monitorização num espaço de trabalho. Isto permite-lhe alinhar-se com o modelo operativo da sua organização de TI utilizando um único espaço de trabalho para armazenar dados recolhidos ativados em todos os seus recursos. Por exemplo, concede acesso à sua equipa responsável pelos serviços de infraestrutura alojados em máquinas virtuais Azure (VMs), e como resultado terão acesso apenas aos registos gerados pelos VMs. Isto está a seguir o nosso novo modelo de registo de contexto de recursos. A base para este modelo é para cada registo de registo emitido por um recurso Azure, estando automaticamente associada a este recurso. Os registos são encaminhados para um espaço de trabalho central que respeita a deteção e o RBAC com base nos recursos.

Os dados a que um utilizador tem acesso são determinados por uma combinação de fatores listados na tabela seguinte. Cada um é descrito nas secções abaixo.

| Fator | Descrição |
|:---|:---|
| [Modo de acesso](#access-mode) | Método que o utilizador utiliza para aceder ao espaço de trabalho.  Define o âmbito dos dados disponíveis e o modo de controlo de acesso que é aplicado. |
| [Modo de controlo de acesso](#access-control-mode) | Definição no espaço de trabalho que define se as permissões são aplicadas no espaço de trabalho ou no nível de recursos. |
| [Permissões](manage-access.md) | Permissões aplicadas a indivíduos ou grupos de utilizadores para o espaço de trabalho ou recurso. Define quais os dados a que o utilizador terá acesso. |
| [Nível de tabela RBAC](manage-access.md#table-level-rbac) | Permissões granulares opcionais que se aplicam a todos os utilizadores, independentemente do seu modo de acesso ou modo de controlo de acesso. Define quais os tipos de dados a que um utilizador pode aceder. |

## <a name="access-mode"></a>Modo de acesso

O *modo de acesso* refere-se à forma como um utilizador acede a um espaço de trabalho do Log Analytics e define o âmbito dos dados a que pode aceder. 

Os utilizadores têm duas opções para aceder aos dados:

* **Contexto do espaço de**trabalho : Pode visualizar todos os registos no espaço de trabalho a que tem permissão. As consultas neste modo são procuradas em todos os dados em todas as tabelas do espaço de trabalho. Este é o modo de acesso utilizado quando os registos são acedidos ao espaço de trabalho como o âmbito, como quando seleciona **Logs** a partir do menu **Azure Monitor** no portal Azure.

    ![Log Analytics contexto do espaço de trabalho](./media/design-logs-deployment/query-from-workspace.png)

* **Contexto de recursos**: Ao aceder ao espaço de trabalho para um determinado recurso, grupo de recursos ou subscrição, como quando seleciona **Logs** a partir de um menu de recursos no portal Azure, pode visualizar registos apenas em todas as tabelas a que tenha acesso. As consultas neste modo são procuradas apenas por dados associados a esse recurso. Este modo também permite o RBAC granular.

    ![Log Analytics contexto a partir de recurso](./media/design-logs-deployment/query-from-resource.png)

    > [!NOTE]
    > Os registos só estão disponíveis para consultas de contexto de recursos se estiverem devidamente associados ao recurso relevante. Atualmente, os seguintes recursos têm limitações:
    > - Computadores fora de Azure
    > - Service Fabric
    > - Application Insights
    >
    > Pode testar se os registos estão devidamente associados ao seu recurso, executando uma consulta e inspecionando os registos em que está interessado. Se o ID de recurso correto estiver na propriedade [_ResourceId,](log-standard-properties.md#_resourceid) então os dados estão disponíveis para consultas centradas em recursos.

O Azure Monitor determina automaticamente o modo certo, dependendo do contexto a partir do qual realiza a pesquisa de registo. O âmbito é sempre apresentado na secção superior esquerda do Log Analytics.

### <a name="comparing-access-modes"></a>Comparar modos de acesso

A tabela a seguir resume os modos de acesso:

| Problema | Contexto do espaço de trabalho | Contexto de recursos |
|:---|:---|:---|
| Para quem é destinado cada modelo? | Administração central. Administradores que precisam de configurar a recolha de dados e utilizadores que precisam de acesso a uma grande variedade de recursos. Também atualmente necessário para os utilizadores que precisam de aceder a registos de recursos fora do Azure. | Equipas de candidatura. Administradores dos recursos da Azure a ser monitorizados. |
| O que um utilizador necessita para visualizar registos? | Permissões para o espaço de trabalho. Consulte **permissões do Espaço de Trabalho** em Gerir o acesso utilizando [permissões de espaço de trabalho.](manage-access.md#manage-access-using-workspace-permissions) | Leia o acesso ao recurso. Consulte **permissões de Recursos** em Gerir o acesso utilizando [permissões Azure](manage-access.md#manage-access-using-azure-permissions). As permissões podem ser herdadas (por exemplo, do grupo de recursos contendo) ou diretamente atribuídas ao recurso. A permissão para os registos do recurso será automaticamente atribuída. |
| Qual é o âmbito das permissões? | Espaço de trabalho. Os utilizadores com acesso ao espaço de trabalho podem consultar todos os registos no espaço de trabalho a partir de tabelas às que têm permissões. Ver [controlo de acesso à mesa](manage-access.md#table-level-rbac) | Recurso azul. O utilizador pode consultar registos de recursos específicos, grupos de recursos ou subscrição a que tenham acesso a partir de qualquer espaço de trabalho, mas não podem consultar registos de outros recursos. |
| Como podem os registos de acesso do utilizador? | <ul><li>Inicie **registos** a partir do menu **Azure Monitor.**</li></ul> <ul><li>Iniciar **registos** a partir de espaços de **trabalho log analytics**.</li></ul> <ul><li>Dos livros de [trabalho](../visualizations.md#workbooks)do Monitor Azure.</li></ul> | <ul><li>Inicie **registos** a partir do menu para o recurso Azure</li></ul> <ul><li>Inicie **registos** a partir do menu **Azure Monitor.**</li></ul> <ul><li>Iniciar **registos** a partir de espaços de **trabalho log analytics**.</li></ul> <ul><li>Dos livros de [trabalho](../visualizations.md#workbooks)do Monitor Azure.</li></ul> |

## <a name="access-control-mode"></a>Modo de controlo de acesso

O *modo de controlo access* é uma definição em cada espaço de trabalho que define como as permissões são determinadas para o espaço de trabalho.

* **Requer permissões de espaço de trabalho**: Este modo de controlo não permite o RBAC granular. Para que um utilizador aceda ao espaço de trabalho, deve ser-lhes concedidas permissões para o espaço de trabalho ou para mesas específicas.

    Se um utilizador aceder ao espaço de trabalho seguindo o modo de contexto de espaço de trabalho, tem acesso a todos os dados em qualquer tabela a que tenha acesso. Se um utilizador aceder ao espaço de trabalho seguindo o modo de contexto de recursos, tem acesso a apenas dados para esse recurso em qualquer tabela a que tenha acesso.

    Esta é a definição padrão para todos os espaços de trabalho criados antes de março de 2019.

* **Utilização de permissões de recursos ou espaço de trabalho**: Este modo de controlo permite o RBAC granular. Os utilizadores podem ter acesso a apenas dados associados aos recursos que podem ver, atribuindo a permissão do `read` Azure. 

    Quando um utilizador acede ao espaço de trabalho no modo de contexto de espaço de trabalho, aplicam-se permissões de espaço de trabalho. Quando um utilizador acede ao espaço de trabalho no modo de contexto de recursos, apenas as permissões de recursos são verificadas e as permissões do espaço de trabalho são ignoradas. Ativar o RBAC para um utilizador removendo-os das permissões do espaço de trabalho e permitindo que as suas permissões de recursos sejam reconhecidas.

    Esta é a definição padrão para todos os espaços de trabalho criados após março de 2019.

    > [!NOTE]
    > Se um utilizador tiver apenas permissões de recursos para o espaço de trabalho, só pode aceder ao espaço de trabalho utilizando o modo de contexto de recursos, assumindo que o modo de acesso ao espaço de trabalho está definido para **utilizar permissões de recursos ou espaço de trabalho.**

Para aprender a alterar o modo de controlo de acesso no portal, com o PowerShell ou utilizando um modelo de Gestor de Recursos, consulte o [modo de controlo de acesso Configure](manage-access.md#configure-access-control-mode).

## <a name="scale-and-ingestion-volume-rate-limit"></a>Limite de taxa de volume de escala e ingestão

O Azure Monitor é um serviço de dados de alta escala que serve milhares de clientes que enviam petabytes de dados todos os meses a um ritmo crescente. Os espaços de trabalho não são limitados no seu espaço de armazenamento e podem crescer a petabytes de dados. Não há necessidade de dividir espaços de trabalho devido à escala.

Para proteger e isolar os clientes do Azure Monitor e a sua infraestrutura de backend, existe um limite de taxa de ingestão padrão que é projetado para proteger contra picos e situações de inundações. O limite de taxa é de cerca de **6 GB/minuto** e foi concebido para permitir a ingestão normal. Para obter mais informações sobre a medição do limite de volume de ingestão, consulte [os limites de serviço do Monitor Azure](../service-limits.md#data-ingestion-volume-rate).

Os clientes que ingerem menos de 4TB/dia geralmente não cumprem estes limites. Os clientes que ingerem volumes mais elevados ou que tenham picos como parte das suas operações normais devem considerar a possibilidade de se deslocarem para [agrupamentos dedicados](../log-query/logs-dedicated-clusters.md) onde o limite da taxa de ingestão possa ser aumentado.

Quando o limite da taxa de ingestão é ativado ou chega a 80% do limiar, um evento é adicionado à tabela *Operação* no seu espaço de trabalho. Recomenda-se monitorizá-lo e criar um alerta. Consulte mais detalhes sobre [a taxa de volume de ingestão de dados](../service-limits.md#data-ingestion-volume-rate).


## <a name="recommendations"></a>Recomendações

![Exemplo de design de contexto de recursos](./media/design-logs-deployment/workspace-design-resource-context-01.png)

Este cenário abrange um único design de espaço de trabalho na subscrição da sua organização de TI que não é limitado pela soberania de dados ou conformidade regulamentar, ou precisa mapear para as regiões que os seus recursos são implantados dentro. Permite às equipas de segurança e administração de TI da sua organização a capacidade de alavancar a melhor integração com a gestão de acessos Azure e um controlo de acesso mais seguro.

Todos os recursos, soluções de monitorização e Insights como Application Insights e Azure Monitor para VMs, infraestruturas de apoio e aplicações mantidas pelas diferentes equipas estão configurados para encaminhar os seus dados de registo recolhidos para o espaço de trabalho partilhado centralizado da organização de TI. Aos utilizadores de cada equipa é-lhes concedido acesso a registos de recursos a que lhes foi dado acesso.

Uma vez implementada a sua arquitetura de espaço de trabalho, você pode impor isso em recursos Azure com [Azure Policy.](../../governance/policy/overview.md) Fornece uma forma de definir políticas e garantir o cumprimento dos seus recursos Azure para que enviem todos os seus registos de recursos para um determinado espaço de trabalho. Por exemplo, com máquinas virtuais Azure ou conjuntos de escala de máquinas virtuais, pode utilizar as políticas existentes que avaliam a conformidade do espaço de trabalho e os resultados dos relatórios, ou personalizar para remediar se não estiver em conformidade.  

## <a name="workspace-consolidation-migration-strategy"></a>Estratégia de migração de consolidação do espaço de trabalho

Para clientes que já implementaram vários espaços de trabalho e estão interessados em consolidar-se para o modelo de acesso ao contexto de recursos, recomendamos que você tome uma abordagem incremental para migrar para o modelo de acesso recomendado, e você não tenta alcançá-lo de forma rápida ou agressiva. Seguindo uma abordagem faseada do plano, migrar, validar e retirar-se seguindo uma linha temporal razoável ajudará a evitar incidentes não planeados ou impacto inesperado nas suas operações na nuvem. Se não tiver uma política de retenção de dados por razões de conformidade ou de negócio, precisa de avaliar o tempo adequado para reter dados no espaço de trabalho de que está a migrar durante o processo. Enquanto está a reconfigurar recursos para reportar ao espaço de trabalho partilhado, ainda pode analisar os dados no espaço de trabalho original, conforme necessário. Uma vez concluída a migração, se for governado a reter dados no espaço de trabalho original antes do final do período de retenção, não o elimine.

Enquanto planeia a sua migração para este modelo, considere o seguinte:

* Compreenda quais os regulamentos da indústria e as políticas internas relativas à conservação de dados que deve cumprir.
* Certifique-se de que as suas equipas de candidatura podem trabalhar dentro da funcionalidade de contexto de recursos existente.
* Identifique o acesso concedido aos recursos para as suas equipas de candidatura e teste num ambiente de desenvolvimento antes de implementar na produção.
* Configure o espaço de trabalho para permitir **permissões de utilização de recursos ou espaço de trabalho**.
* Remova a permissão das equipas de aplicação para ler e consultar o espaço de trabalho.
* Ativar e configurar quaisquer soluções de monitorização, Insights como o Azure Monitor para contentores e/ou Monitor Azure para VMs, a sua conta de Automação e soluções de gestão como a Update Management, Start/Stop VMs, etc., que foram implantadas no espaço de trabalho original.

## <a name="next-steps"></a>Passos seguintes

Para implementar as permissões e controlos de segurança recomendados neste guia, [reveja o acesso aos registos](manage-access.md).

