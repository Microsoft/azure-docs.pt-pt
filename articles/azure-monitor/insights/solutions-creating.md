---
title: Construir uma solução de gestão no Azure Microsoft Docs
description: As soluções de gestão incluem cenários de gestão embalados no Azure que os clientes podem adicionar ao seu espaço de trabalho Log Analytics.  Este artigo fornece detalhes sobre como pode criar soluções de gestão para ser usado no seu próprio ambiente ou disponibilizado aos seus clientes.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f1605597c7716ba6a896c7ecdae968f07d66027b
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663220"
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Conceber e construir uma solução de gestão em Azure (Pré-visualização)
> [!NOTE]
> Esta é uma documentação preliminar para a criação de soluções de gestão no Azure que estão atualmente em pré-visualização. Qualquer esquema descrito abaixo está sujeito a alterações.

[As soluções de gestão]( solutions.md) proporcionam cenários de gestão embalados que os clientes podem adicionar ao seu espaço de trabalho Log Analytics.  Este artigo apresenta um processo básico para conceber e construir uma solução de gestão adequada para os requisitos mais comuns.  Se você é novo em soluções de gestão de construção, então você pode usar este processo como um ponto de partida e, em seguida, alavancar os conceitos para soluções mais complexas à medida que as suas necessidades evoluem.

## <a name="what-is-a-management-solution"></a>O que é uma solução de gestão?

As soluções de gestão contêm recursos Azure que trabalham em conjunto para alcançar um determinado cenário de gestão.  São implementados como modelos de [Gestão](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) de Recursos que contêm detalhes de como instalar e configurar os seus recursos contidos quando a solução é instalada.

A estratégia básica é iniciar a sua solução de gestão construindo os componentes individuais no seu ambiente Azure.  Uma vez que tenha a funcionalidade a funcionar corretamente, pode começar a embalá-las num ficheiro de solução de [gestão.]( solutions-solution-file.md) 


## <a name="design-your-solution"></a>Desenhe a sua solução
O padrão mais comum para uma solução de gestão é mostrado no diagrama seguinte.  Os diferentes componentes deste padrão são discutidos no seguinte.

![Visão geral da solução de gestão](media/solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Origens de dados
O primeiro passo para a conceção de uma solução é determinar os dados que necessita do repositório Log Analytics.  Estes dados podem ser recolhidos por uma fonte de [dados](../../azure-monitor/platform/agent-data-sources.md) ou [outra solução,]( solutions.md)ou a sua solução poderá ter de fornecer o processo para os recolher.

Existem várias formas de recolher fontes de dados no repositório Log Analytics, conforme descrito em [fontes](../../azure-monitor/platform/agent-data-sources.md)de dados no Log Analytics .  Isto inclui eventos no Windows Event Log ou gerados pela Syslog, além de contadores de desempenho tanto para clientes Windows como Linux.  Também pode recolher dados dos recursos azure recolhidos pelo Azure Monitor.  

Se necessitar de dados que não sejam acessíveis através de qualquer uma das fontes de dados disponíveis, então pode utilizar a [API](../../azure-monitor/platform/data-collector-api.md) http Data Collector que lhe permite escrever dados para o repositório Log Analytics de qualquer cliente que possa chamar uma API REST.  O meio mais comum de recolha de dados personalizados numa solução de gestão é criar um livro de [execução na Automação Azure](../../automation/automation-runbook-types.md) que recolhe os dados necessários do Azure ou de recursos externos e utiliza a API de Recolha de Dados para escrever ao repositório.  

### <a name="log-searches"></a>Pesquisas de log
[As pesquisas](../../azure-monitor/log-query/log-query-overview.md) de registo são usadas para extrair e analisar dados no repositório Log Analytics.  São utilizados por pontos de vista e alertas, além de permitir em que o utilizador efetue uma análise ad hoc dos dados no repositório.  

Deve definir quaisquer dúvidas que considere úteis para o utilizador, mesmo que não sejam utilizadas por quaisquer visualizações ou alertas.  Estes estarão disponíveis para eles como Saved Searchs no portal, e você também pode incluí-los em uma [parte de visualização de Consultas](../../azure-monitor/platform/view-designer-parts.md#list-of-queries-part) na sua vista personalizada.

### <a name="alerts"></a>Alertas
[Os alertas no Log Analytics](../../azure-monitor/platform/alerts-overview.md) identificam problemas através de pesquisas de [registo](#log-searches) contra os dados no repositório.  Ou notificam o utilizador ou executam automaticamente uma ação em resposta. Deve identificar diferentes condições de alerta para a sua aplicação e incluir as regras de alerta correspondentes no seu ficheiro de solução.

Se o problema pode potencialmente ser corrigido com um processo automatizado, então você normalmente vai criar um livro de execução na Automação Azure para realizar esta reparação.  A maioria dos serviços Azure pode ser gerido com [cmdlets](/powershell/azure/overview) que o livro de execução aproveitaria para executar tal funcionalidade.

Se a sua solução necessitar de funcionalidade externa em resposta a um alerta, então pode utilizar uma [resposta webhook](../../azure-monitor/platform/alerts-metric.md).  Isto permite-lhe ligar para um serviço web externo enviando informações a partir do alerta.

### <a name="views"></a>Vistas
As vistas em Log Analytics são usadas para visualizar dados do repositório Log Analytics.  Cada solução normalmente contém uma única vista com um [azulejo](../../azure-monitor/platform/view-designer-tiles.md) que é exibido no painel principal do utilizador.  A vista pode conter qualquer número de peças de [visualização](../../azure-monitor/platform/view-designer-parts.md) para fornecer diferentes visualizações dos dados recolhidos ao utilizador.

Cria [vistas personalizadas utilizando o View Designer](../../azure-monitor/platform/view-designer.md) que mais tarde poderá exportar para inclusão no seu ficheiro de solução.  


## <a name="create-solution-file"></a>Criar ficheiro de solução
Depois de configurar e testar os componentes que farão parte da sua solução, pode criar o seu ficheiro de [solução]( solutions-solution-file.md).  Implementará os componentes da solução num [modelo de Gestor](../../azure-resource-manager/templates/template-syntax.md) de Recursos que inclui um recurso de [solução]( solutions-solution-file.md#solution-resource) com relações com os outros recursos do ficheiro.  


## <a name="test-your-solution"></a>Teste a sua solução
Enquanto está a desenvolver a sua solução, terá de a instalar e testar no seu espaço de trabalho.  Pode fazê-lo utilizando qualquer um dos métodos disponíveis para [testar e instalar modelos](../../azure-resource-manager/templates/deploy-powershell.md)de Gestor de Recursos .

## <a name="publish-your-solution"></a>Publique a sua solução
Depois de concluído e testado a sua solução, pode disponibilizá-la aos clientes através das seguintes fontes.

- **Modelos Azure Quickstart**.  [Os modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/) são um conjunto de modelos de Gestor de Recursos contribuídos pela comunidade através do GitHub.  Pode disponibilizar a sua solução seguindo informações no guia de [contribuições.](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE)
- **Mercado Azure.**  O [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) permite-lhe distribuir e vender a sua solução a outros desenvolvedores, ISVs e profissionais de TI.  Pode aprender a publicar a sua solução para o Azure Marketplace em [Como publicar e gerir uma oferta no Azure Marketplace.](../../marketplace/marketplace-publishers-guide.md)



## <a name="next-steps"></a>Passos seguintes
* Aprenda a criar um ficheiro de [solução]( solutions-solution-file.md) para a sua solução de gestão.
* Conheça os detalhes dos modelos de Gestor de [Recursos Do Azure.](../../azure-resource-manager/templates/template-syntax.md)
* Procure [modelos Azure Quickstart](https://azure.microsoft.com/documentation/templates) para amostras de diferentes modelos de Gestor de Recursos.
