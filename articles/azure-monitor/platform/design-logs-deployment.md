---
title: Criando sua implantação de logs de Azure Monitor | Microsoft Docs
description: Este artigo descreve as considerações e recomendações para os clientes se preparando para implantar um espaço de trabalho no Azure Monitor.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2019
ms.author: magoedte
ms.openlocfilehash: fa3c8b8cee0b8621a6a2800655f62a3d339f67c3
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211995"
---
# <a name="designing-your-azure-monitor-logs-deployment"></a>Criando sua implantação de logs de Azure Monitor

O Azure Monitor armazena dados de [log](data-platform-logs.md) em um espaço de trabalho log Analytics, que é um recurso do Azure e um contêiner em que os dados são coletados, agregados e servem como um limite administrativo. Embora você possa implantar um ou mais espaços de trabalho em sua assinatura do Azure, há várias considerações que você deve entender para garantir que a implantação inicial esteja seguindo nossas diretrizes para fornecer a você um custo eficaz, gerenciável e escalonável a implantação atende às necessidades de suas organizações.

Os dados em um espaço de trabalho são organizados em tabelas, cada um dos quais armazena diferentes tipos de dados e tem seu próprio conjunto exclusivo de propriedades com base no recurso que gera os dados. A maioria das fontes de dados será gravada em suas próprias tabelas em um espaço de trabalho Log Analytics.

![Exemplo de modelo de dados de espaço de trabalho](./media/design-logs-deployment/logs-data-model-01.png)

Um espaço de trabalho Log Analytics fornece:

* Uma localização geográfica para armazenamento de dados.
* Isolamento de dados concedendo direitos de acesso de usuários diferentes após uma das nossas estratégias de design recomendadas.
* Escopo para configuração de configurações, como [tipo de preço](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [retenção](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)e limitação de [dados](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap).

Este artigo fornece uma visão geral detalhada das considerações de design e migração, visão geral do controle de acesso e uma compreensão das implementações de design recomendadas para sua organização de ti.



## <a name="important-considerations-for-an-access-control-strategy"></a>Considerações importantes para uma estratégia de controle de acesso

Identificar o número de espaços de trabalho de que você precisa é influenciado por um ou mais dos seguintes requisitos:

* Você é uma empresa global e precisa de dados de log armazenados em regiões específicas para fins de conformidade ou de soberania de dados.
* Estiver a utilizar o Azure e pretender evitar custos de transferência de dados de saída ao ter uma área de trabalho na mesma região dos recursos do Azure que gere.
* Você gerencia vários departamentos ou grupos de negócios e deseja que cada um deles veja seus próprios dados, mas não dados de outros. Além disso, não há nenhum requisito comercial para uma exibição consolidada entre departamentos ou de grupos de negócios.

Hoje, as organizações de ti são modeladas seguindo um híbrido centralizado, descentralizado ou um entre as duas estruturas. Como resultado, os seguintes modelos de implantação de espaço de trabalho foram comumente usados para mapear para uma dessas estruturas organizacionais:

* **Centralizado**: Todos os logs são armazenados em um espaço de trabalho central e administrados por uma única equipe, com Azure Monitor fornecendo acesso diferenciado por equipe. Nesse cenário, é fácil de gerenciar, Pesquisar entre recursos e correlacionar logs de forma cruzada. O espaço de trabalho pode crescer significativamente dependendo da quantidade de dados coletados de vários recursos em sua assinatura, com sobrecarga administrativa adicional para manter o controle de acesso a diferentes usuários.
* **Descentralizado**: Cada equipe tem seu próprio espaço de trabalho criado em um grupo de recursos que ele possui e gerencia, e os dados de log são separados por recurso. Nesse cenário, o espaço de trabalho pode ser mantido seguro e o controle de acesso é consistente com o acesso aos recursos, mas é difícil correlacionar os logs. Os usuários que precisam de uma visão ampla de muitos recursos não podem analisar os dados de uma maneira significativa.
* **Híbrido**: Os requisitos de conformidade de auditoria de segurança complicam ainda mais esse cenário porque muitas organizações implementam ambos os modelos de implantação em paralelo. Isso normalmente resulta em uma configuração complexa, cara e difícil de manter com lacunas na cobertura de logs.

Ao usar os agentes de Log Analytics para coletar dados, você precisa entender o seguinte para planejar a implantação do agente:

* Para coletar dados de agentes do Windows, você pode [configurar cada agente para reportar para um ou mais espaços de trabalho](../../azure-monitor/platform/agent-windows.md), mesmo enquanto estiver relatando a um grupo de gerenciamento de System Center Operations Manager. O agente do Windows pode relatar até quatro espaços de trabalho.
* O agente do Linux não dá suporte a hospedagem múltipla e só pode relatar para um único espaço de trabalho.

Se você estiver usando o System Center Operations Manager 2012 R2 ou posterior:

* Cada grupo de gerenciamento de Operations Manager pode ser [conectado a apenas um espaço de trabalho](../platform/om-agents.md). 
* Os computadores Linux que relatam para um grupo de gerenciamento devem ser configurados para relatar diretamente a um espaço de trabalho Log Analytics. Se os computadores Linux já estiverem se reportando diretamente a um espaço de trabalho e você quiser monitorá-los com Operations Manager, siga estas etapas para [relatar a um grupo de gerenciamento de Operations Manager](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group). 
* Você pode instalar o Log Analytics agente do Windows no computador Windows e fazer com que ele se reporte a ambos Operations Manager integrados a um espaço de trabalho e a um espaço de trabalho diferente.

## <a name="access-control-overview"></a>Visão geral do controle de acesso

Com o RBAC (controle de acesso baseado em função), você pode conceder aos usuários e grupos apenas a quantidade de acesso de que eles precisam para trabalhar com dados de monitoramento em um espaço de trabalho. Isso permite que você se alinhe com o modelo operacional da organização de ti usando um único espaço de trabalho para armazenar os dados coletados habilitados em todos os seus recursos. Por exemplo, você concede acesso à sua equipe responsável pelos serviços de infraestrutura hospedados em VMs (máquinas virtuais) do Azure e, como resultado, eles terão acesso apenas aos logs gerados pelas VMs. Isso está seguindo nosso novo modelo de log de contexto de recurso. A base desse modelo é para cada registro de log emitido por um recurso do Azure, ele é automaticamente associado a esse recurso. Os logs são encaminhados para um espaço de trabalho central que respeita o escopo e o RBAC com base nos recursos.

Os dados aos quais um usuário tem acesso são determinados por uma combinação de fatores listados na tabela a seguir. Cada um é descrito nas seções a seguir.

| Fator | Descrição |
|:---|:---|
| [Modo de acesso](#access-mode) | Método usado pelo usuário para acessar o espaço de trabalho.  Define o escopo dos dados disponíveis e o modo de controle de acesso que é aplicado. |
| [Modo de controle de acesso](#access-control-mode) | Configuração no espaço de trabalho que define se as permissões são aplicadas no nível de espaço de trabalho ou de recurso. |
| [Permissões](manage-access.md) | Permissões aplicadas a indivíduo ou grupos de usuários para o espaço de trabalho ou recurso. Define a quais dados o usuário terá acesso. |
| [RBAC de nível de tabela](manage-access.md#table-level-rbac) | Permissões granulares opcionais que se aplicam a todos os usuários, independentemente do modo de acesso ou do modo de controle de acesso. Define quais tipos de dados um usuário pode acessar. |

## <a name="access-mode"></a>Modo de acesso

O *modo de acesso* refere-se a como um usuário acessa um espaço de trabalho log Analytics e define o escopo dos dados que eles podem acessar. 

Os usuários têm duas opções para acessar os dados:

* **Espaço de trabalho-contexto**: Você pode exibir todos os logs no espaço de trabalho para o qual tem permissão. As consultas nesse modo têm o escopo definido para todos os dados em todas as tabelas no espaço de trabalho. Esse é o modo de acesso usado quando os logs são acessados com o espaço de trabalho como o escopo, como quando você seleciona **logs** no menu **Azure monitor** na portal do Azure.

    ![Log Analytics contexto do espaço de trabalho](./media/design-logs-deployment/query-from-workspace.png)

* **Recurso-contexto**: Ao acessar o espaço de trabalho para um recurso específico, um grupo de recursos ou uma assinatura, como quando você seleciona **logs** em um menu de recursos na portal do Azure, você pode exibir logs somente para recursos em todas as tabelas às quais você tem acesso. As consultas neste modo têm o escopo para apenas os dados associados a esse recurso. Esse modo também permite o RBAC granular.

    ![Log Analytics contexto do recurso](./media/design-logs-deployment/query-from-resource.png)

    > [!NOTE]
    > Os logs estarão disponíveis para consultas de contexto de recurso somente se estiverem corretamente associados ao recurso relevante. Atualmente, os seguintes recursos têm limitações:
    > - Computadores fora do Azure
    > - Service Fabric
    > - Application Insights
    >
    > Você pode testar se os logs estão corretamente associados ao recurso executando uma consulta e inspecionando os registros nos quais você está interessado. Se a ID de recurso correta estiver na propriedade [_ResourceId](log-standard-properties.md#_resourceid) , os dados estarão disponíveis para consultas centradas em recursos.

Azure Monitor determina automaticamente o modo correto dependendo do contexto do qual você executa a pesquisa de log. O escopo é sempre apresentado na seção superior esquerda de Log Analytics.

### <a name="comparing-access-modes"></a>Comparando modos de acesso

A tabela a seguir resume os modos de acesso:

| | Espaço de trabalho-contexto | Recurso-contexto |
|:---|:---|:---|
| A quem cada modelo se destina? | Administração Central. Os administradores que precisam configurar a coleta de dados e os usuários que precisam de acesso a uma ampla variedade de recursos. Também exigido no momento para os usuários que precisam acessar os logs para recursos fora do Azure. | Equipes de aplicativos. Administradores de recursos do Azure sendo monitorados. |
| O que um usuário precisa para exibir os logs? | Permissões para o espaço de trabalho. Consulte **permissões de espaço de trabalho** em [gerenciar acesso usando permissões de espaço de trabalho](manage-access.md#manage-access-using-workspace-permissions). | Acesso de leitura ao recurso. Consulte **permissões de recurso** em [gerenciar o acesso usando as permissões do Azure](manage-access.md#manage-access-using-azure-permissions). As permissões podem ser herdadas (por exemplo, do grupo de recursos que a contém) ou diretamente atribuídas ao recurso. A permissão para os logs do recurso será atribuída automaticamente. |
| Qual é o escopo das permissões? | Espaço. Os usuários com acesso ao espaço de trabalho podem consultar todos os logs no espaço de trabalho de tabelas às quais eles têm permissões. Consulte [controle de acesso à tabela](manage-access.md#table-level-rbac) | Recurso do Azure. O usuário pode consultar logs de recursos específicos, grupos de recursos ou assinaturas aos quais eles têm acesso de qualquer espaço de trabalho, mas não podem consultar logs para outros recursos. |
| Como o usuário pode acessar os logs? | <ul><li>Inicie **os logs** no menu **Azure monitor** .</li></ul> <ul><li>Iniciar **logs** de **log Analytics espaços de trabalho**.</li></ul> <ul><li>De [pastas de trabalho](../visualizations.md#workbooks)do Azure monitor.</li></ul> | <ul><li>Iniciar **logs** no menu do recurso do Azure</li></ul> <ul><li>Inicie **os logs** no menu **Azure monitor** .</li></ul> <ul><li>Iniciar **logs** de **log Analytics espaços de trabalho**.</li></ul> <ul><li>De [pastas de trabalho](../visualizations.md#workbooks)do Azure monitor.</li></ul> |

## <a name="access-control-mode"></a>Modo de controlo de acesso

O *modo de controle de acesso* é uma configuração em cada espaço de trabalho que define como as permissões são determinadas para o espaço de trabalho.

* **Exigir permissões de espaço de trabalho**: Esse modo de controle não permite RBAC granular. Para que um usuário acesse o espaço de trabalho, ele deve receber permissões para o espaço de trabalho ou para tabelas específicas.

    Se um usuário acessar o espaço de trabalho após o modo de contexto de espaço de trabalho, ele terá acesso a todos os dados em qualquer tabela à qual tenha sido concedido acesso. Se um usuário acessar o espaço de trabalho após o modo de contexto de recurso, ele terá acesso a apenas dados para esse recurso em qualquer tabela à qual tenha sido concedido acesso.

    Essa é a configuração padrão para todos os espaços de trabalho criados antes de março de 2019.

* **Usar permissões de recurso ou espaço de trabalho**: Esse modo de controle permite RBAC granular. Os usuários podem receber acesso a apenas dados associados aos recursos que podem exibir atribuindo permissão do Azure `read` . 

    Quando um usuário acessa o espaço de trabalho no modo de contexto de espaço de trabalho, as permissões de espaço de trabalho se aplicam. Quando um usuário acessa o espaço de trabalho no modo de contexto de recurso, somente as permissões de recurso são verificadas e as permissões de espaço de trabalho são ignoradas. Habilite o RBAC para um usuário removendo-os das permissões do espaço de trabalho e permitindo que suas permissões de recurso sejam reconhecidas.

    Essa é a configuração padrão para todos os espaços de trabalho criados depois de março de 2019.

    > [!NOTE]
    > Se um usuário tiver apenas permissões de recurso para o espaço de trabalho, ele só poderá acessar o espaço de trabalho usando o modo de contexto de recurso, supondo que o modo de acesso do espaço de trabalho esteja definido para **usar permissões de recurso ou espaço de trabalho**.

Para saber como alterar o modo de controle de acesso no portal, com o PowerShell ou usando um modelo do Resource Manager, consulte [Configurar o modo de controle de acesso](manage-access.md#configure-access-control-mode).

## <a name="ingestion-volume-rate-limit"></a>Limite da taxa de volume de ingestão

Azure Monitor é um serviço de dados de alta escala que atende a milhares de clientes enviando terabytes de dados por mês em um ritmo crescente. O limite de taxa de ingestão padrão é definido como **500 MB/min** por espaço de trabalho. Se você enviar dados a uma taxa mais alta para um único espaço de trabalho, alguns dados serão descartados e um evento será enviado para a tabela de *operações* no seu espaço de trabalho a cada 6 horas, enquanto o limite continuará sendo excedido. Se o volume de ingestão continuar exceder o limite de taxa ou você estiver esperando contatá-lo em breve, poderá solicitar um aumento no espaço de trabalho abrindo uma solicitação de suporte.
 
Para ser notificado sobre esse evento em seu espaço de trabalho, crie uma [regra de alerta de log](alerts-log.md) usando a consulta a seguir com a base de lógica de alerta no número de resultados mais rígidos que zero.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


## <a name="recommendations"></a>Recomendações

![Exemplo de design de contexto de recurso](./media/design-logs-deployment/workspace-design-resource-context-01.png)

Este cenário aborda um design de espaço de trabalho único em sua assinatura de organizações de ti que não é restrita pela soberania de dados ou conformidade regulatória, ou que precisa mapear para as regiões em que os recursos são implantados. Ele permite que as equipes de segurança e administrador de ti da sua organização possam aproveitar a integração aprimorada com o gerenciamento de acesso do Azure e um controle de acesso mais seguro.

Todos os recursos, soluções de monitoramento e informações como Application Insights e Azure Monitor para VMs, a infraestrutura de suporte e os aplicativos mantidos pelas diferentes equipes são configurados para encaminhar seus dados de log coletados para as organizações de ti espaço de trabalho compartilhado centralizado. Os usuários em cada equipe recebem acesso a logs para recursos aos quais eles receberam acesso.

Depois de implantar a arquitetura do espaço de trabalho, você pode impor isso nos recursos do Azure com [Azure Policy](../../governance/policy/overview.md). Ele fornece uma maneira de definir políticas e garantir a conformidade com os recursos do Azure para que eles enviem todos os seus logs de diagnóstico para um espaço de trabalho específico. Por exemplo, com máquinas virtuais do Azure ou conjuntos de dimensionamento de máquinas virtuais, você pode usar políticas existentes que avaliam a conformidade do espaço de trabalho e os resultados do relatório, ou personalizar para corrigir se não estiver em conformidade.  

## <a name="workspace-consolidation-migration-strategy"></a>Estratégia de migração de consolidação de espaço de trabalho

Para os clientes que já implantaram vários espaços de trabalho e estão interessados em consolidar para o modelo de acesso de contexto de recurso, recomendamos que você use uma abordagem incremental para migrar para o modelo de acesso recomendado e não tente atingir isso de forma rápida ou agressiva. Seguir uma abordagem em fases para planejar, migrar, validar e desativar seguindo uma linha do tempo razoável ajudará a evitar incidentes não planejados ou um impacto inesperado em suas operações de nuvem. Se você não tiver uma política de retenção de dados para fins de conformidade ou de negócios, você precisará avaliar o tempo apropriado para manter os dados no espaço de trabalho que está migrando durante o processo. Enquanto você estiver reconfigurando os recursos para relatar para o espaço de trabalho compartilhado, você ainda poderá analisar os dados no espaço de trabalho original, conforme necessário. Quando a migração for concluída, se você for regido a manter os dados no espaço de trabalho original antes do final do período de retenção, não o exclua.

Ao planejar a migração para esse modelo, considere o seguinte:

* Entenda quais são os regulamentos do setor e as políticas internas relacionadas à retenção de dados que você deve obedecer.
* Verifique se as equipes de aplicativo podem trabalhar na funcionalidade de contexto de recurso existente.
* Identifique o acesso concedido aos recursos para suas equipes de aplicativo e teste em um ambiente de desenvolvimento antes de implementar em produção.
* Configure o espaço de trabalho para habilitar **as permissões usar recurso ou espaço de trabalho**.
* Remova a permissão de equipes de aplicativo para ler e consultar o espaço de trabalho.
* Habilite e configure qualquer solução de monitoramento, informações como Azure Monitor para contêineres e/ou Azure Monitor para VMs, suas contas de automação e soluções de gerenciamento, como Gerenciamento de Atualizações, iniciar/parar VMs, etc., que foram implantadas no original espaço.

## <a name="next-steps"></a>Passos seguintes

Para implementar as permissões de segurança e os controles recomendados neste guia, examine [gerenciar o acesso aos logs](manage-access.md).