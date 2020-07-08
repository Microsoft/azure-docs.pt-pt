---
title: Use a análise de mudança de aplicação no Azure Monitor para encontrar problemas com a web-app Microsoft Docs
description: Utilize a Análise de Alteração de Aplicações no Azure Monitor para resolver problemas de aplicações em sites ao vivo no Azure App Service.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: c287a2315f2b2319a6873ce84ee0e4e48bec8444
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82836818"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Utilizar a Análise de Alteração de Aplicações (pré-visualização) no Monitor Azure

Quando ocorre um problema ou interrupção do site ao vivo, determinar rapidamente a causa da raiz é fundamental. As soluções de monitorização padrão podem alertá-lo para um problema. Podem até indicar que componente está a falhar. Mas este alerta nem sempre explica imediatamente a causa do fracasso. Sabes que o teu site funcionou há cinco minutos, e agora está avariado. O que mudou nos últimos cinco minutos? Esta é a questão que a Análise de Alteração de Aplicações foi concebida para responder no Azure Monitor.

Com base na potência do [Azure Resource Graph,](https://docs.microsoft.com/azure/governance/resource-graph/overview)a Change Analysis fornece insights sobre as alterações da sua aplicação Azure para aumentar a observabilidade e reduzir o MTTR (tempo médio de reparação).

> [!IMPORTANT]
> A Análise de Alterações está atualmente em pré-visualização. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço. Esta versão não é recomendada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas ou podem ter capacidades restritas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Descrição geral

A Análise de Alterações deteta vários tipos de alterações, desde a camada de infraestrutura até à implementação da aplicação. É um fornecedor de recursos Azure de nível de subscrição que verifica as alterações de recursos na subscrição. A Change Analysis fornece dados para várias ferramentas de diagnóstico para ajudar os utilizadores a entender que mudanças podem ter causado problemas.

O seguinte diagrama ilustra a arquitetura da Análise de Mudanças:

![Diagrama de arquitetura de como a Change Analysis obtém dados de mudança e fornece-os às ferramentas do cliente](./media/change-analysis/overview.png)

## <a name="data-sources"></a>Origens de dados

As consultas de análise de alterações de aplicações para O Gestor de Recursos Azure rastrearam propriedades, configurações proxied e alterações de aplicações web no hóspede. Além disso, o serviço rastreia as alterações de dependência de recursos para diagnosticar e monitorizar uma aplicação de ponta a ponta.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Azure Resource Manager rastreou as mudanças de propriedades

Utilizando [o Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), a Change Analysis fornece um registo histórico de como os recursos Azure que acolhem a sua aplicação mudaram ao longo do tempo. Podem ser detetadas definições rastreadas como identidades geridas, upgrade do Sistema operativo da Plataforma e hostnames.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure Resource Manager proxied alterações de definição

As definições como a regra de configuração IP, as definições de TLS e as versões de extensão ainda não estão disponíveis no Gráfico de Recursos Azure, pelo que a Alteração de Análise consulta e calcula estas alterações de forma segura para fornecer mais detalhes sobre o que foi alterado na aplicação.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Alterações na implementação e configuração de aplicações web (alterações no hóspede)

A Análise de Alteração captura o estado de implantação e configuração de uma aplicação a cada 4 horas. Pode detetar, por exemplo, alterações nas variáveis do ambiente de aplicação. A ferramenta calcula as diferenças e apresenta o que mudou. Ao contrário das alterações do Gestor de Recursos, as informações de alteração de implementação de código podem não estar disponíveis imediatamente na ferramenta. Para ver as últimas alterações na Análise de Alterações, **selecione Refresh**.

![Screenshot do botão "Scan changes now"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Alterações de dependência

As alterações nas dependências de recursos também podem causar problemas numa aplicação web. Por exemplo, se uma aplicação web ligar para uma cache Redis, o SKU de cache Redis pode afetar o desempenho da aplicação web. Para detetar alterações nas dependências, a Change Analysis verifica o registo dns da aplicação web. Desta forma, identifica alterações em todos os componentes da aplicação que podem causar problemas.
Atualmente são suportadas as seguintes dependências:
- Aplicações Web
- Storage do Azure
- SQL do Azure

## <a name="application-change-analysis-service"></a>Serviço de Análise de Alterações de Aplicações

O serviço de Análise de Alterações de Aplicação calcula e agrega os dados das fontes de dados acima mencionadas. Fornece um conjunto de análises para que os utilizadores naveguem facilmente através de todas as alterações de recursos e identifiquem qual a mudança relevante no contexto de resolução de problemas ou monitorização.
O fornecedor de recursos "Microsoft.ChangeAnalysis" precisa de ser registado com uma subscrição para o Gestor de Recursos Azure que rastreou propriedades e definições proxied alterar dados para estar disponível. Ao introduzir a ferramenta de diagnóstico e resolução de problemas da Web App ou apresentar o separador Change Analysis autónomo, este fornecedor de recursos é automaticamente registado. Não tem qualquer desempenho ou implementações de custos para a sua subscrição. Quando ativa a Análise de Alterações para aplicações web (ou ativar a ferramenta de problemas de diagnóstico e resolução), terá um impacto de desempenho negligenciável na aplicação web e sem custo de faturação.
Para alterações na aplicação web no hóspede, é necessária uma ativação separada para digitalizar ficheiros de código dentro de uma aplicação web. Para obter mais informações, consulte a Análise de Alterações na secção [de Ferramentas de Diagnóstico e resolva problemas](https://docs.microsoft.com/azure/azure-monitor/app/change-analysis#application-change-analysis-in-the-diagnose-and-solve-problems-tool) mais tarde neste artigo para obter mais detalhes.

## <a name="visualizations-for-application-change-analysis"></a>Visualizações para análise de mudança de aplicação

### <a name="standalone-ui"></a>UI autónomo

No Azure Monitor, existe um painel autónomo para a Análise de Alterações para visualizar todas as alterações com insights sobre dependências de aplicações e recursos.

Procure a Análise de Alterações na barra de pesquisa no portal Azure para lançar a experiência.

![Screenshot de pesquisa de Análise de Mudança no portal Azure](./media/change-analysis/search-change-analysis.png)

Todos os recursos sob uma subscrição selecionada são apresentados com alterações das últimas 24 horas. Para otimizar para o desempenho da carga da página, o serviço apresenta 10 recursos de cada vez. Clique nas próximas páginas para ver mais recursos. Estamos a trabalhar para eliminar esta limitação.

![Screenshot da lâmina de análise de mudança no portal Azure](./media/change-analysis/change-analysis-standalone-blade.png)

Clicando num recurso para ver todas as suas alterações. Se necessário, faça uma alteração para ver detalhes e insights de alteração formatados json.

![Screenshot de detalhes de mudança](./media/change-analysis/change-details.png)

Para obter qualquer feedback, utilize o botão de feedback de envio na lâmina ou e-mail changeanalysisteam@microsoft.com .

![Screenshot do botão de feedback na lâmina change analysis](./media/change-analysis/change-analysis-feedback.png)

### <a name="web-app-diagnose-and-solve-problems"></a>Diagnóstico e resolução de problemas de aplicações web

No Azure Monitor, a Change Analysis também é integrada no autosserviço Diagnosticar e resolver a experiência de **problemas.** Aceda a esta experiência a partir da página **geral** da sua aplicação Serviço de Aplicações.

![Screenshot do botão "Visão Geral" e do botão "Diagnosticar e resolver problemas"](./media/change-analysis/change-analysis.png)

### <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Análise de alteração de aplicação na ferramenta diagnosticar e resolver problemas

Application Change Analysis é um detetor autónomo na Web App diagnosticar e resolver ferramentas de problemas. Também é agregado em **detetores de aplicações e** web **app down.** Ao introduzir a ferramenta Diagnose and Solve Problems, o fornecedor de recursos **Microsoft.ChangeAnalysis** será automaticamente registado. Siga estas instruções para permitir o rastreio de alterações na web no hóspede.

1. Selecione **Disponibilidade e Performance**.

    ![Screenshot das opções de resolução de problemas "Disponibilidade e desempenho"](./media/change-analysis/availability-and-performance.png)

2. Selecione **Alterações de Aplicação**. A funcionalidade também está disponível em **Application Crashes.**

   ![Screenshot do botão "Application Crashes"](./media/change-analysis/application-changes.png)

3. Para ativar a Análise de Alterações, selecione **Ative já**.

   ![Screenshot das opções "Application Crashes"](./media/change-analysis/enable-changeanalysis.png)

4. Ligue a **Análise de Alterações** e selecione **Guardar**. A ferramenta exibe todas as aplicações web ao abrigo de um plano de Serviço de Aplicações. Pode utilizar o interruptor de nível de plano para ativar a Change Analysis para todas as aplicações web ao abrigo de um plano.

    ![Screenshot da interface de utilizador "Enable Change Analysis"](./media/change-analysis/change-analysis-on.png)

5. Para aceder à Análise de Alterações, selecione **Diagnosticar e resolver problemas**Disponibilidade e Falhas de  >  **Availability and Performance**  >  **Aplicação de**Desempenho. Você verá um gráfico que resume o tipo de mudanças ao longo do tempo, juntamente com detalhes sobre essas alterações. Por padrão, as alterações nas últimas 24 horas são apresentadas para ajudar com problemas imediatos.

     ![Screenshot da visão de mudança diff](./media/change-analysis/change-view.png)

### <a name="enable-change-analysis-at-scale"></a>Ativar a análise de mudança à escala

Se a sua subscrição incluir numerosas aplicações web, ativar o serviço ao nível da aplicação web seria ineficiente. Execute o seguinte script para ativar todas as aplicações web na sua subscrição.

Pré-requisitos:

- Módulo PowerShell Az. Siga as instruções na [Instalação do módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0)

Execute o seguintes script:

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"

# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```

### <a name="virtual-machine-diagnose-and-solve-problems"></a>Diagnóstico e resolução de máquinas virtuais

Vá para a ferramenta Diagnosticar e Resolver Problemas para uma Máquina Virtual.  Vá a **Ferramentas de Resolução de Problemas,** navegue pela página e selecione **Analise as alterações recentes** para ver as alterações na Máquina Virtual.

![Screenshot do VM Diagnosticar e Resolver Problemas](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Screenshot do VM Diagnosticar e Resolver Problemas](./media/change-analysis/analyze-recent-changes.png)

## <a name="next-steps"></a>Próximos passos

- Ativar insights de aplicações para [aplicações Azure App Services](azure-web-apps.md).
- Ativar os Insights de Aplicações para [Azure VM e Azure série de máquinas virtuais definir aplicações hospedadas no IIS](azure-vm-vmss-apps.md).
- Saiba mais sobre [o Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), que ajuda a power Change Analysis.
