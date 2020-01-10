---
title: Criar uma solução de gerenciamento no Azure | Microsoft Docs
description: As soluções de gerenciamento incluem cenários de gerenciamento empacotados no Azure que os clientes podem adicionar ao seu espaço de trabalho de Log Analytics.  Este artigo fornece detalhes sobre como você pode criar soluções de gerenciamento para serem usadas em seu próprio ambiente ou disponibilizadas para seus clientes.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8c08dc5091e7ab0eec5d4e6dd455e6adb3caa35f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75402052"
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Projetar e criar uma solução de gerenciamento no Azure (versão prévia)
> [!NOTE]
> Esta é uma documentação preliminar para criar soluções de gerenciamento no Azure que estão atualmente em versão prévia. Qualquer esquema descrito abaixo está sujeito a alterações.

As [soluções de gerenciamento]( solutions.md) fornecem cenários de gerenciamento empacotados que os clientes podem adicionar ao seu espaço de trabalho de log Analytics.  Este artigo apresenta um processo básico para projetar e criar uma solução de gerenciamento adequada para os requisitos mais comuns.  Se você for novo na criação de soluções de gerenciamento, poderá usar esse processo como um ponto de partida e, em seguida, aproveitar os conceitos para soluções mais complexas à medida que suas necessidades evoluem.

## <a name="what-is-a-management-solution"></a>O que é uma solução de gerenciamento?

As soluções de gerenciamento contêm recursos do Azure que trabalham em conjunto para alcançar um cenário de gerenciamento específico.  Eles são implementados como [modelos de gerenciamento de recursos](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) que contêm detalhes de como instalar e configurar seus recursos contidos quando a solução é instalada.

A estratégia básica é iniciar sua solução de gerenciamento criando componentes individuais em seu ambiente do Azure.  Depois que a funcionalidade estiver funcionando corretamente, você poderá começar a compactá-las em um [arquivo de solução de gerenciamento]( solutions-solution-file.md). 


## <a name="design-your-solution"></a>Conceba a sua solução
O padrão mais comum para uma solução de gerenciamento é mostrado no diagrama a seguir.  Os diferentes componentes neste padrão são discutidos no abaixo.

![Visão geral da solução de gerenciamento](media/solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Origens de dados
A primeira etapa na criação de uma solução é determinar os dados que você precisa do repositório Log Analytics.  Esses dados podem ser coletados por uma [fonte de dados](../../azure-monitor/platform/agent-data-sources.md) ou [outra solução]( solutions.md), ou sua solução pode precisar fornecer o processo para coletá-lo.

Há várias maneiras pelas quais as fontes de dados podem ser coletadas no repositório Log Analytics, conforme descrito em [fontes de dados no log Analytics](../../azure-monitor/platform/agent-data-sources.md).  Isso inclui eventos no log de eventos do Windows ou gerados pelo syslog, além de contadores de desempenho para clientes Windows e Linux.  Você também pode coletar dados dos recursos do Azure coletados pelo Azure Monitor.  

Se você precisar de dados que não estão acessíveis por meio de qualquer uma das fontes de dados disponíveis, poderá usar a [API do coletor de dados http](../../azure-monitor/platform/data-collector-api.md) , que permite gravar dados no repositório de log Analytics de qualquer cliente que possa chamar uma API REST.  O meio mais comum de coleta de dados personalizada em uma solução de gerenciamento é criar um [runbook na automação do Azure](../../automation/automation-runbook-types.md) que coleta os dados necessários do Azure ou recursos externos e usa a API do coletor de dados para gravar no repositório.  

### <a name="log-searches"></a>Pesquisas de log
[Pesquisas de log](../../azure-monitor/log-query/log-query-overview.md) são usadas para extrair e analisar dados no repositório log Analytics.  Eles são usados por exibições e alertas, além de permitir que o usuário execute a análise ad hoc de dados no repositório.  

Você deve definir todas as consultas que considerar que serão úteis para o usuário, mesmo que não sejam usadas por exibições ou alertas.  Eles estarão disponíveis para eles como pesquisas salvas no portal, e você também pode incluí-los em uma [lista de visualização de consultas](../../azure-monitor/platform/view-designer-parts.md#list-of-queries-part) no modo de exibição personalizado.

### <a name="alerts"></a>Alertas
[Alertas no log Analytics](../../azure-monitor/platform/alerts-overview.md) identificam problemas por meio de [pesquisas de log](#log-searches) em relação aos dados no repositório.  Eles notificam o usuário ou executam automaticamente uma ação em resposta. Você deve identificar diferentes condições de alerta para seu aplicativo e incluir as regras de alerta correspondentes em seu arquivo de solução.

Se o problema puder ser corrigido com um processo automatizado, você normalmente criará um runbook na automação do Azure para executar essa correção.  A maioria dos serviços do Azure pode ser gerenciada com [cmdlets](/powershell/azure/overview) que o runbook aproveitaria para executar essa funcionalidade.

Se sua solução exigir a funcionalidade externa em resposta a um alerta, você poderá usar uma [resposta de webhook](../../azure-monitor/platform/alerts-metric.md).  Isso permite que você chame um serviço Web externo enviando informações do alerta.

### <a name="views"></a>Vistas
Exibições em Log Analytics são usadas para visualizar dados do repositório de Log Analytics.  Em geral, cada solução contém uma única exibição com um [bloco](../../azure-monitor/platform/view-designer-tiles.md) que é exibido no painel principal do usuário.  O modo de exibição pode conter qualquer número de [partes de visualização](../../azure-monitor/platform/view-designer-parts.md) para fornecer diferentes visualizações dos dados coletados ao usuário.

Você [cria exibições personalizadas usando o designer de exibição](../../azure-monitor/platform/view-designer.md) , que pode ser exportado posteriormente para inclusão em seu arquivo de solução.  


## <a name="create-solution-file"></a>Criar arquivo de solução
Depois de configurar e testar os componentes que serão parte de sua solução, você pode [criar o arquivo de solução]( solutions-solution-file.md).  Você implementará os componentes da solução em um [modelo do Resource Manager](../../azure-resource-manager/templates/template-syntax.md) que inclui um [recurso de solução]( solutions-solution-file.md#solution-resource) com relações com os outros recursos no arquivo.  


## <a name="test-your-solution"></a>Testar sua solução
Enquanto estiver desenvolvendo sua solução, você precisará instalá-la e testá-la em seu espaço de trabalho.  Você pode fazer isso usando qualquer um dos métodos disponíveis para [testar e instalar modelos do Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="publish-your-solution"></a>Publicar sua solução
Depois de concluir e testar sua solução, você poderá disponibilizá-la para os clientes por meio das seguintes fontes.

- **Modelos de início rápido do Azure**.  Os [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/) são um conjunto de modelos do Resource Manager contribuídos pela Comunidade por meio do github.  Você pode disponibilizar sua solução seguindo as informações no [Guia de contribuição](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE).
- **Azure Marketplace**.  O [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) permite que você distribua e venda sua solução para outros desenvolvedores, ISVs e profissionais de ti.  Você pode aprender a publicar sua solução no Azure Marketplace em [como publicar e gerenciar uma oferta no Azure Marketplace](../../marketplace/marketplace-publishers-guide.md).



## <a name="next-steps"></a>Passos seguintes
* Saiba como [criar um arquivo de solução]( solutions-solution-file.md) para sua solução de gerenciamento.
* Conheça os detalhes da [criação de modelos de Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).
* Pesquise [modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates) para obter exemplos de modelos diferentes do Resource Manager.
