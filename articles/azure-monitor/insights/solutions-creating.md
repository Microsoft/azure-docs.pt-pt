---
title: Criar uma solução de gestão no Azure | Documentos da Microsoft
description: Soluções de gestão incluem cenários de gestão em pacote no Azure que os clientes podem adicionar a sua área de trabalho do Log Analytics.  Este artigo fornece detalhes sobre como pode criar soluções de gestão para ser usado em seu próprio ambiente ou disponibilizada aos seus clientes.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ef1af4d3d27bc098341a4de716e293557baa946a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761392"
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Conceber e criar uma solução de gestão no Azure (pré-visualização)
> [!NOTE]
> Esta é a documentação preliminar para a criação de soluções de gestão no Azure que estão atualmente em pré-visualização. Qualquer esquema descrita abaixo está sujeitas a alterações.

[Soluções de gestão]( solutions.md) fornecer cenários de pacote de gestão que os clientes podem adicionar a sua área de trabalho do Log Analytics.  Este artigo apresenta um processo básico para projetar e criar uma solução de gestão que é adequada para os requisitos mais comuns.  Se estiver a criar soluções de gestão, em seguida, pode utilizar este processo como um ponto de partida e, em seguida, tirar partido dos conceitos para soluções mais complexas, à medida que os seus requisitos evoluem.

## <a name="what-is-a-management-solution"></a>O que é uma solução de gestão?

Soluções de gestão contêm recursos do Azure que funcionam em conjunto para obter um cenário de gestão específico.  Eles são implementados como [modelos de gestão de recursos](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) que contêm detalhes sobre como instalar e configurar os recursos contidos quando a solução é instalada.

A estratégia básica é começar a sua solução de gestão com a criação de componentes individuais no seu ambiente do Azure.  Assim que tiver a funcionalidade funcionar corretamente, pode começar a empacotá-los para um [arquivo da solução de gestão]( solutions-solution-file.md). 


## <a name="design-your-solution"></a>Conceba a sua solução
O padrão mais comuns para uma solução de gestão é mostrado no diagrama seguinte.  Os diferentes componentes neste padrão são apresentados a seguir.

![Descrição geral da solução de gestão](media/solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Origens de dados
A primeira etapa na criação de uma solução é determinar os dados que necessita a partir do repositório do Log Analytics.  Estes dados podem ser recolhidos por um [origem de dados](../../azure-monitor/platform/agent-data-sources.md) ou [outra solução]( solutions.md), ou a sua solução talvez seja necessário fornecer o processo de recolha-lo.

Existem várias maneiras de origens de dados que podem ser recolhidas no repositório do Log Analytics, conforme descrito em [origens de dados no Log Analytics](../../azure-monitor/platform/agent-data-sources.md).  Isto inclui eventos no registo de eventos do Windows ou gerado pelo Syslog, além de contadores de desempenho, para clientes Windows e Linux.  Também pode recolher dados dos recursos do Azure recolhidos pelo Monitor do Azure.  

Se necessitar de dados que não são acessíveis através de qualquer uma das origens de dados disponíveis, em seguida, pode utilizar o [HTTP Data Collector API](../../azure-monitor/platform/data-collector-api.md) que permite-lhe escrever dados para o repositório do Log Analytics a partir de qualquer cliente que pode chamar uma API REST.  Os meios mais comuns de recolha de dados personalizados numa solução de gestão de consiste em criar uma [runbook na automatização do Azure](../../automation/automation-runbook-types.md) que recolhe os dados necessários a partir dos recursos do Azure ou externos e utiliza a API de Recoletor de dados para escrever o repositório.  

### <a name="log-searches"></a>Pesquisas de registos
[Pesquisas de registos](../../azure-monitor/log-query/log-query-overview.md) são usados para extrair e analisar dados no repositório do Log Analytics.  São utilizados pelas vistas e alertas, além de permitir que o usuário execute análise ad hoc de dados no repositório.  

Deve definir todas as consultas que acredita que serão úteis para os utilizadores, mesmo que eles não são utilizados por quaisquer vistas ou alertas.  Estes vão estar disponíveis a eles como pesquisas guardadas no portal e também pode incluir os mesmos num [parte de visualização de lista de consultas](../../azure-monitor/platform/view-designer-parts.md#list-of-queries-part) na vista personalizada.

### <a name="alerts"></a>Alertas
[Alertas do Log Analytics](../../azure-monitor/platform/alerts-overview.md) identificar problemas através de [pesquisas de registos](#log-searches) relativamente aos dados no repositório.  Eles ou notificar o utilizador ou executam automaticamente uma ação em resposta. Deve identificar diferentes condições de alerta para a sua aplicação e incluir regras de alerta correspondentes no seu ficheiro de solução.

Se o problema potencialmente pode ser corrigido com um processo automatizado, normalmente, irá criar um runbook na automatização do Azure para realizar essa correção.  Os serviços mais do Azure podem ser geridos com [cmdlets](/powershell/azure/overview) que o runbook poderia aproveitar para fazer essa funcionalidade.

Se sua solução requer funcionalidade externa em resposta a um alerta, em seguida, pode utilizar um [resposta do webhook](../../azure-monitor/platform/alerts-metric.md).  Isto permite-lhe chamar um serviço web externo enviar informações a partir do alerta.

### <a name="views"></a>Vistas
Vistas no Log Analytics são utilizadas para visualizar dados do repositório do Log Analytics.  Cada solução normalmente irá conter uma única vista com um [mosaico](../../azure-monitor/platform/view-designer-tiles.md) que é apresentado no dashboard principal do usuário.  A vista pode conter qualquer número de [partes de visualização](../../azure-monitor/platform/view-designer-parts.md) para fornecer visualizações diferentes dos dados recolhidos para o usuário.

[Criar vistas personalizadas com o estruturador de vista](../../azure-monitor/platform/view-designer.md) que mais tarde, pode exportar para inclusão no seu ficheiro de solução.  


## <a name="create-solution-file"></a>Criar ficheiro de solução
Depois de ter configurado e testado os componentes que farão parte da sua solução, pode [criar o arquivo da solução]( solutions-solution-file.md).  Irá implementar os componentes da solução num [modelo do Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) que inclua um [recursos de solução]( solutions-solution-file.md#solution-resource) com relações aos outros recursos no arquivo.  


## <a name="test-your-solution"></a>Testar a sua solução
Enquanto estiver a desenvolver sua solução, terá de instalar e testá-lo na sua área de trabalho.  Pode fazê-lo usando qualquer um dos métodos disponíveis para [testar e instalar os modelos do Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="publish-your-solution"></a>Publicar a sua solução
Depois de concluída e testada sua solução, pode disponibilizá-lo para os clientes através de qualquer uma das seguintes origens.

- **Modelos de início rápido do Azure**.  [Modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/) é um conjunto de modelos do Resource Manager contribuído pela Comunidade através do GitHub.  É possível disponibilizar sua solução por seguintes informações na [guia de contribuições](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE).
- **O Azure Marketplace**.  O [do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) permite distribuir e vender a sua solução para outros desenvolvedores, ISVs e os profissionais de TI.  Pode aprender a publicar a sua solução no Azure Marketplace em [sobre como publicar e gerir uma oferta no Azure Marketplace](../../marketplace/marketplace-publishers-guide.md).



## <a name="next-steps"></a>Passos Seguintes
* Saiba como [criar um arquivo de solução]( solutions-solution-file.md) para a sua solução de gestão.
* Saiba os detalhes de [modelos Authoring Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).
* Pesquisa [modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates) exemplos dos diferentes modelos do Resource Manager.
