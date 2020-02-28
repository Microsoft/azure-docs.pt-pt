---
title: Conceber a sua implementação de Registos de Monitores Azure  Microsoft Docs
description: Este artigo descreve as considerações e recomendações para os clientes que se preparam para implantar um espaço de trabalho no Monitor Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/20/2019
ms.openlocfilehash: e493b07814821496f941a4b81402ba0b49acbede
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670462"
---
# <a name="designing-your-azure-monitor-logs-deployment"></a>Conceber a sua implementação de Registos de Monitores Azure

O Azure Monitor armazena dados de [registo](data-platform-logs.md) num espaço de trabalho do Log Analytics, que é um recurso Azure e um contentor onde os dados são recolhidos, agregados e servem como limite administrativo. Embora possa implementar um ou mais espaços de trabalho na sua subscrição Azure, existem várias considerações que deve compreender para garantir que a sua implementação inicial está a seguir as nossas diretrizes para lhe fornecer um custo eficaz, manejável e escalável implantação que satisfaz as suas organizações precisa.

Os dados de um espaço de trabalho são organizados em tabelas, cada uma das quais armazena diferentes tipos de dados e tem o seu próprio conjunto único de propriedades com base no recurso que gera os dados. A maioria das fontes de dados escreverá para as suas próprias tabelas num espaço de trabalho de Log Analytics.

![Modelo de dados do espaço de trabalho de exemplo](./media/design-logs-deployment/logs-data-model-01.png)

Um espaço de trabalho log Analytics fornece:

* Uma localização geográfica para armazenamento de dados.
* Isolamento de dados, concedendo aos diferentes utilizadores direitos de acesso seguindo uma das nossas estratégias de design recomendadas.
* Âmbito para configuração de configurações como nível de [preços,](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier) [retenção,](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) [e captação](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#manage-your-maximum-daily-data-volume)de dados .

Este artigo fornece uma visão detalhada das considerações de design e migração, visão geral do controlo de acesso e uma compreensão das implementações de design que recomendamos para a sua organização de TI.



## <a name="important-considerations-for-an-access-control-strategy"></a>Considerações importantes para uma estratégia de controlo de acesso

Identificar o número de espaços de trabalho de que necessita é influenciado por um ou mais dos seguintes requisitos:

* É uma empresa global e precisa de dados de registo armazenados em regiões específicas por razões de soberania ou conformidade de dados.
* Estiver a utilizar o Azure e pretender evitar custos de transferência de dados de saída ao ter uma área de trabalho na mesma região dos recursos do Azure que gere.
* Gere vários departamentos ou grupos empresariais, e quer que cada um veja os seus próprios dados, mas não dados de outros. Além disso, não existe qualquer exigência de negócio para uma visão consolidada do departamento transversal ou do grupo empresarial.

As organizações de TI são hoje modeladas seguindo um híbrido centralizado, descentralizado ou um híbrido intermédio de ambas as estruturas. Como resultado, os seguintes modelos de implantação do espaço de trabalho têm sido comumente usados para mapear uma destas estruturas organizacionais:

* **Centralizado**: Todos os registos são armazenados num espaço de trabalho central e administrados por uma única equipa, com o Monitor Azure a proporcionar acesso diferenciado por equipa. Neste cenário, é fácil gerir, pesquisar através de recursos e cruzar registos. O espaço de trabalho pode crescer significativamente dependendo da quantidade de dados recolhidos a partir de vários recursos na sua subscrição, com despesas administrativas adicionais para manter o controlo de acesso a diferentes utilizadores. Este modelo é conhecido como "hub e falado".
* **Descentralizado**: Cada equipa tem o seu próprio espaço de trabalho criado num grupo de recursos que possuem e gerem, e os dados de registo são segregados por recurso. Neste cenário, o espaço de trabalho pode ser mantido seguro e o controlo de acesso é consistente com o acesso de recursos, mas é difícil cruzar registos. Os utilizadores que precisam de uma visão ampla de muitos recursos não podem analisar os dados de forma significativa.
* **Híbrido**: Os requisitos de conformidade da auditoria de segurança complicam ainda mais este cenário porque muitas organizações implementam ambos os modelos de implementação em paralelo. Isto geralmente resulta numa configuração complexa, cara e difícil de manter com lacunas na cobertura de logs.

Ao utilizar os agentes Log Analytics para recolher dados, é necessário compreender o seguinte para planear a implementação do seu agente:

* Para recolher dados de agentes do Windows, pode [configurar cada agente para reportar a um ou mais espaços](../../azure-monitor/platform/agent-windows.md)de trabalho , mesmo quando está a reportar a um grupo de gestão de Gestão de Operações do System Center. O agente do Windows pode reportar até quatro espaços de trabalho.
* O agente Linux não suporta multi-homing e só pode reportar a um único espaço de trabalho.

Se estiver a utilizar o System Center Operations Manager 2012 R2 ou mais tarde:

* Cada grupo de gestão do Gestor de Operações pode ser [ligado a penas apenas um espaço de trabalho.](../platform/om-agents.md) 
* Os computadores Linux que reportem a um grupo de gestão devem ser configurados para reportar diretamente a um espaço de trabalho de Log Analytics. Se os seus computadores Linux já estão a reportar diretamente a um espaço de trabalho e pretende monitorizá-los com o Gestor de Operações, siga estes passos para reportar a um grupo de gestão do Gestor de [Operações](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group). 
* Pode instalar o agente Windows de Log Analytics no computador Windows e fazê-lo reportar tanto ao Gestor de Operações integrado com um espaço de trabalho e a um espaço de trabalho diferente.

## <a name="access-control-overview"></a>Descrição geral do controlo de acesso

Com o controlo de acesso baseado em papéis (RBAC), pode conceder aos utilizadores e grupos apenas a quantidade de acesso de que necessitam para trabalhar em monitorização de dados num espaço de trabalho. Isto permite-lhe alinhar-se com o seu modelo operativo da organização de TI utilizando um único espaço de trabalho para armazenar dados recolhidos ativados em todos os seus recursos. Por exemplo, você concede acesso à sua equipa responsável pelos serviços de infraestrutura hospedados em máquinas virtuais Azure (VMs), e como resultado eles terão acesso apenas aos registos gerados pelos VMs. Isto está a seguir o nosso novo modelo de registo de contexto de recursos. A base para este modelo é para cada registo de registo emitido por um recurso Azure, este é automaticamente associado a este recurso. Os registos são encaminhados para um espaço de trabalho central que respeita a deteção e o RBAC com base nos recursos.

Os dados a que um utilizador tem acesso são determinados por uma combinação de fatores que estão listados na tabela seguinte. Cada um é descrito nas secções abaixo.

| Fator | Descrição |
|:---|:---|
| [Modo de acesso](#access-mode) | Método que o utilizador utiliza para aceder ao espaço de trabalho.  Define o âmbito dos dados disponíveis e o modo de controlo de acesso que é aplicado. |
| [Modo de controlo de acesso](#access-control-mode) | Definição no espaço de trabalho que define se as permissões são aplicadas ao nível do espaço de trabalho ou do recurso. |
| [Permissões](manage-access.md) | Permissões aplicadas a indivíduos ou grupos de utilizadores para o espaço de trabalho ou recurso. Define a que dados o utilizador terá acesso. |
| [RBAC nível de mesa](manage-access.md#table-level-rbac) | Permissões granulares opcionais que se aplicam a todos os utilizadores, independentemente do seu modo de acesso ou do modo de controlo de acesso. Define quais os tipos de dados a que um utilizador pode aceder. |

## <a name="access-mode"></a>Modo de acesso

O modo de *acesso* refere-se à forma como um utilizador acede a um espaço de trabalho do Log Analytics e define o âmbito dos dados a que pode aceder. 

Os utilizadores têm duas opções para aceder aos dados:

* **Contexto espaço de trabalho:** Pode ver todos os registos no espaço de trabalho a que tem permissão. As consultas neste modo são consultadas a todos os dados em todas as tabelas do espaço de trabalho. Este é o modo de acesso utilizado quando os registos são acedidos com o espaço de trabalho como âmbito, como quando seleciona **Registos** do menu **Do Monitor Azure** no portal Azure.

    ![Log Analytics contexto do espaço de trabalho](./media/design-logs-deployment/query-from-workspace.png)

* **Contexto de recursos**: Quando acede ao espaço de trabalho para um determinado recurso, grupo de recursos ou subscrição, como quando seleciona **Registos** de um menu de recursos no portal Azure, pode ver registos apenas para recursos em todas as tabelas a que tem acesso. As consultas neste modo são reparadas apenas com dados associados a esse recurso. Este modo também permite rBAC granular.

    ![Log Analytics contexto a partir de recurso](./media/design-logs-deployment/query-from-resource.png)

    > [!NOTE]
    > Os registos só estão disponíveis para consultas de contexto de recursos se estiverem devidamente associados ao recurso relevante. Atualmente, os seguintes recursos têm limitações:
    > - Computadores fora de Azure
    > - Service Fabric
    > - Application Insights
    >
    > Pode testar se os registos estão devidamente associados ao seu recurso, executando uma consulta e inspecionando os registos em que está interessado. Se o ID de recurso correto estiver na [propriedade _ResourceId,](log-standard-properties.md#_resourceid) então os dados estão disponíveis para consultas centradas em recursos.

O Monitor Azure determina automaticamente o modo certo dependendo do contexto de que realiza a pesquisa de registo. O âmbito é sempre apresentado na secção superior-esquerda do Log Analytics.

### <a name="comparing-access-modes"></a>Comparar modos de acesso

A tabela que se segue resume os modos de acesso:

| | Contexto espaço de trabalho | Contexto de recursos |
|:---|:---|:---|
| Para quem é cada modelo destinado? | Administração central. Administradores que precisam de configurar a recolha de dados e utilizadores que precisam de acesso a uma grande variedade de recursos. Atualmente também é necessário para utilizadores que necessitem de aceder a registos de recursos fora do Azure. | Equipas de candidatura. Administradores de recursos Azure estão a ser monitorizados. |
| O que é que um utilizador precisa para ver os registos? | Permissões para o espaço de trabalho. Consulte **permissões do Espaço de Trabalho** no Acesso gerir utilizando [permissões no espaço de trabalho](manage-access.md#manage-access-using-workspace-permissions). | Leia o acesso ao recurso. Consulte **permissões de recursos** no acesso gerir utilizando [permissões Azure](manage-access.md#manage-access-using-azure-permissions). As permissões podem ser herdadas (como por exemplo, do grupo de recursos que contêm) ou diretamente atribuídas ao recurso. A permissão para os registos do recurso será automaticamente atribuída. |
| Qual é o âmbito das permissões? | Espaço de trabalho. Os utilizadores com acesso ao espaço de trabalho podem consultar todos os registos no espaço de trabalho a partir das tabelas a que têm permissões. Ver controlo de acesso à [mesa](manage-access.md#table-level-rbac) | Recurso azure. O utilizador pode consultar registos de recursos específicos, grupos de recursos ou subscrição a que têm acesso a partir de qualquer espaço de trabalho, mas não podem consultar registos de outros recursos. |
| Como pode o utilizador aceder aos registos? | <ul><li>Inicie **registos** do menu **Do Monitor Azure.**</li></ul> <ul><li>Inicie **registos** a partir de espaços de **trabalho log Analytics**.</li></ul> <ul><li>Dos livros de [trabalho](../visualizations.md#workbooks)do Monitor Azure.</li></ul> | <ul><li>Iniciar **Registos** a partir do menu para o recurso Azure</li></ul> <ul><li>Inicie **registos** do menu **Do Monitor Azure.**</li></ul> <ul><li>Inicie **registos** a partir de espaços de **trabalho log Analytics**.</li></ul> <ul><li>Dos livros de [trabalho](../visualizations.md#workbooks)do Monitor Azure.</li></ul> |

## <a name="access-control-mode"></a>Modo de controlo de acesso

O *modo de controlo de acesso* é uma definição em cada espaço de trabalho que define como as permissões são determinadas para o espaço de trabalho.

* **Requerer permissões no espaço**de trabalho : Este modo de controlo não permite rBAC granular. Para que um utilizador aceda ao espaço de trabalho, deve ser-lhes concedida permissão para o espaço de trabalho ou para tabelas específicas.

    Se um utilizador aceder ao espaço de trabalho seguindo o modo de contexto espaço de trabalho, tem acesso a todos os dados em qualquer tabela a que tenha tido acesso. Se um utilizador aceder ao espaço de trabalho seguindo o modo de contexto de recursos, tem acesso apenas a dados para esse recurso em qualquer tabela a que tenha tido acesso.

    Esta é a definição padrão para todos os espaços de trabalho criados antes de março de 2019.

* **Utilize permissões**de recursos ou espaço de trabalho : Este modo de controlo permite rBAC granular. Os utilizadores podem ter acesso apenas a dados associados aos recursos que possam visualizar atribuindo a permissão `read` Azure. 

    Quando um utilizador acede ao espaço de trabalho no modo de contexto espaço de trabalho, aplicam-se permissões no espaço de trabalho. Quando um utilizador acede ao espaço de trabalho no modo de contexto de recursos, apenas são verificadas permissões de recursos e as permissões do espaço de trabalho são ignoradas. Ative o RBAC para um utilizador, removendo-os das permissões do espaço de trabalho e permitindo que as suas permissões de recursos sejam reconhecidas.

    Esta é a definição padrão para todos os espaços de trabalho criados após março de 2019.

    > [!NOTE]
    > Se um utilizador tiver apenas permissões de recursos para o espaço de trabalho, só é possível aceder ao espaço de trabalho utilizando o modo de contexto de recursos, assumindo que o modo de acesso ao espaço de trabalho está definido para **utilizar permissões**de recursos ou espaço de trabalho.

Para aprender a alterar o modo de controlo de acesso no portal, com o PowerShell, ou utilizando um modelo de Gestor de Recursos, consulte o modo de controlo de [acesso Configure](manage-access.md#configure-access-control-mode).

## <a name="ingestion-volume-rate-limit"></a>Limite da taxa de volume de ingestão

O Azure Monitor é um serviço de dados de alta escala que serve milhares de clientes que enviam terabytes de dados todos os meses a um ritmo crescente. O limiar da taxa de ingestão padrão é fixado em **6 GB/min** por espaço de trabalho. Este é um valor aproximado, uma vez que o tamanho real pode variar entre os tipos de dados dependendo do comprimento do registo e da sua relação de compressão. Este limite não se aplica aos dados enviados por agentes ou pela API do [Coletor](data-collector-api.md)de Dados .

Se enviar dados a uma taxa mais elevada para um único espaço de trabalho, alguns dados são retirados e um evento é enviado para a tabela *Operação* no seu espaço de trabalho a cada 6 horas enquanto o limiar continua a ser ultrapassado. Se o seu volume de ingestão continuar a exceder o limite de taxa ou se estiver à espera de o atingir em breve, poderá solicitar um aumento para o seu espaço de trabalho abrindo um pedido de apoio.
 
Para ser notificado sobre tal evento no seu espaço de trabalho, crie uma regra de alerta de [registo](alerts-log.md) utilizando a seguinte consulta com base lógica de alerta no número de resultados do que zero.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


## <a name="recommendations"></a>Recomendações

![Exemplo de design de contexto de recursos](./media/design-logs-deployment/workspace-design-resource-context-01.png)

Este cenário abrange um único design de espaço de trabalho na subscrição das suas organizações de TI que não é limitado pela soberania de dados ou conformidade regulamentar, ou precisa de mapear para as regiões que os seus recursos estão implantados dentro. Permite às suas organizações segurança e administração de TI a capacidade de alavancar a melhor integração com a gestão de acesso seleções azure e um controlo de acesso mais seguro.

Todos os recursos, soluções de monitorização e Insights como Application Insights e Azure Monitor para VMs, infraestruturas de apoio e aplicações mantidas pelas diferentes equipas estão configuradas para encaminhar os seus dados de registo recolhidos para as organizações de TI espaço de trabalho partilhado centralizado. Os utilizadores de cada equipa têm acesso a registos de recursos a que lhes foi dado acesso.

Depois de ter implantado a sua arquitetura de espaço de trabalho, pode impor isso aos recursos do Azure com [a Política Azure.](../../governance/policy/overview.md) Fornece uma forma de definir políticas e garantir o cumprimento dos seus recursos Azure para que enviem todos os seus registos de recursos para um determinado espaço de trabalho. Por exemplo, com máquinas virtuais Azure ou conjuntos de escala de máquinas virtuais, pode utilizar as políticas existentes que avaliam a conformidade do espaço de trabalho e os resultados do relatório, ou personalizar para remediar se não for em conformidade.  

## <a name="workspace-consolidation-migration-strategy"></a>Estratégia de migração de consolidação do espaço de trabalho

Para os clientes que já implantaram vários espaços de trabalho e estão interessados em consolidar-se para o modelo de acesso ao contexto de recursos, recomendamos que tome uma abordagem incremental para migrar para o modelo de acesso recomendado, e não tente alcançar isso rápida ou agressivamente. Seguindo uma abordagem faseada do plano, migrar, validar e reformar-se seguindo uma linha temporal razoável ajudará a evitar quaisquer incidentes não planeados ou impacto inesperado nas suas operações na nuvem. Se não tiver uma política de retenção de dados por razões de conformidade ou negócio, terá de avaliar o tempo adequado para reter dados no espaço de trabalho de onde está a migrar durante o processo. Enquanto está a reconfigurar os recursos para reportar ao espaço de trabalho partilhado, ainda pode analisar os dados no espaço de trabalho original, se necessário. Uma vez concluída a migração, se for governado para reter dados no espaço de trabalho original antes do final do período de retenção, não os elimine.

Ao planear a sua migração para este modelo, considere o seguinte:

* Compreenda quais os regulamentos do setor e políticas internas relativas à retenção de dados que deve cumprir.
* Certifique-se de que as suas equipas de aplicação podem trabalhar dentro da funcionalidade de contexto de recursos existente.
* Identifique o acesso concedido aos recursos para as suas equipas de aplicação e teste num ambiente de desenvolvimento antes de implementar em produção.
* Configure o espaço de trabalho para ativar **o recurso de utilização ou as permissões do espaço**de trabalho.
* Remova a permissão das equipas de aplicação para ler e consultar o espaço de trabalho.
* Ative e configure quaisquer soluções de monitorização, insights como o Monitor Azure para contentores e/ou Monitor Azure para VMs, a sua conta de Automação ou soluções de gestão como A Gestão de Atualizações, Start/Stop VMs, etc., que foram implantados no espaço de trabalho original.

## <a name="next-steps"></a>Passos seguintes

Para implementar as permissões e controlos de segurança recomendados neste guia, [reveja a gestão do acesso aos registos](manage-access.md).
