---
title: Utilizar análise de alterações de aplicações no Monitor Azure para encontrar problemas com aplicações web Microsoft Docs
description: Utilize a Análise de Alterações de Aplicações no Monitor Azure para resolver problemas de aplicações em sites ao vivo no Serviço de Aplicações Azure.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: c287a2315f2b2319a6873ce84ee0e4e48bec8444
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836818"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Utilizar análise de alterações de aplicações (pré-visualização) no Monitor Azure

Quando ocorre um problema ou paragem do site ao vivo, determinar rapidamente a causa da raiz é fundamental. As soluções de monitorização padrão podem alertá-lo para um problema. Podem até indicar qual o componente que está a falhar. Mas este alerta nem sempre explicará imediatamente a causa da falha. Sabes que o teu site funcionou há cinco minutos, e agora está avariado. O que mudou nos últimos cinco minutos? Esta é a pergunta que a Análise de Alterações de Aplicações foi concebida para responder no Monitor Azure.

Com base na potência do Gráfico de [Recursos Azure,](https://docs.microsoft.com/azure/governance/resource-graph/overview)a Change Analysis fornece informações sobre as alterações da aplicação azure para aumentar a observabilidade e reduzir o MTTR (tempo médio para reparar).

> [!IMPORTANT]
> A Análise de Mudança está atualmente em pré-visualização. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço. Esta versão não é recomendada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas ou ter capacidades limitadas. Para mais informações, consulte [os termos suplementares de utilização para as pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Descrição geral

A Change Analysis deteta vários tipos de alterações, desde a camada de infraestrutura até à implementação da aplicação. É um fornecedor de recursos Azure de nível de subscrição que verifica as mudanças de recursos na subscrição. A Change Analysis fornece dados para várias ferramentas de diagnóstico para ajudar os utilizadores a entender quais as mudanças que podem ter causado problemas.

O diagrama seguinte ilustra a arquitetura da Análise de Mudança:

![Diagrama de arquitetura de como a Change Analysis obtém dados de mudança e fornece-os às ferramentas do cliente](./media/change-analysis/overview.png)

## <a name="data-sources"></a>Origens de dados

Consultas de análise de alterações de aplicações para o Gestor de Recursos Azure rastrearam propriedades, configurações proxidas e alterações na aplicação web no hóspede. Além disso, o serviço rastreia as alterações de dependência dos recursos para diagnosticar e monitorizar uma aplicação de ponta a ponta.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Gestor de Recursos Azure rastreou as mudanças de propriedades

Utilizando o [Azure Resource Graph,](https://docs.microsoft.com/azure/governance/resource-graph/overview)a Change Analysis fornece um registo histórico de como os recursos Azure que acolhem a sua aplicação mudaram ao longo do tempo. Configurações rastreadas, tais como identidades geridas, upgrade do Os da Plataforma e nomes de anfitriões podem ser detetados.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Gestor de Recursos Azure proxiu alterações de definição

As definições como a regra de configuração IP, as definições de TLS e as versões de extensão ainda não estão disponíveis no Gráfico de Recursos Do Azure, por isso as consultas de Análise de Alteração e calcula estas alterações de forma segura para fornecer mais detalhes sobre o que mudou na aplicação.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Alterações na implementação e configuração da aplicação web (alterações no hóspede)

A Change Analysis capta o estado de implantação e configuração de uma aplicação a cada 4 horas. Pode detetar, por exemplo, alterações nas variáveis ambientais de aplicação. A ferramenta calcula as diferenças e apresenta o que mudou. Ao contrário das alterações do Gestor de Recursos, as informações de alteração de mudança de código podem não estar disponíveis imediatamente na ferramenta. Para ver as últimas alterações na Análise de Mudança, selecione **Refresh**.

![Screenshot do botão "Scan changes now"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Alterações de dependência

As alterações às dependências de recursos também podem causar problemas numa aplicação web. Por exemplo, se uma aplicação web ligar para uma cache Redis, o Redis cache SKU pode afetar o desempenho da aplicação web. Para detetar alterações nas dependências, a Change Analysis verifica o registo dNS da aplicação web. Desta forma, identifica alterações em todos os componentes da aplicação que podem causar problemas.
Atualmente, apoiam-se as seguintes dependências:
- Aplicações Web
- Storage do Azure
- SQL do Azure

## <a name="application-change-analysis-service"></a>Serviço de análise de alterações de aplicações

Os cálculos e agregados do serviço de análise de alterações de aplicações alteram os dados das fontes de dados acima mencionadas. Fornece um conjunto de análises para que os utilizadores naveguem facilmente através de todas as alterações de recursos e identifiquem quais as mudanças relevantes no contexto de resolução de problemas ou monitorização.
O fornecedor de recursos "Microsoft.ChangeAnalysis" precisa de ser registado com uma subscrição para o Gestor de Recursos Azure, rastreadas propriedades e configurações de configurações proxidas que alteram dados para estarem disponíveis. À medida que entra na ferramenta de diagnóstico e resolução de problemas da Web App ou se levanta o separador 'Change Analysis' autónomo, este fornecedor de recursos é automaticamente registado. Não possui qualquer desempenho ou implementação de custos para a sua subscrição. Quando activaa a Change Analysis para aplicações web (ou ativar a ferramenta de problemas diagnosticar e resolver), terá um impacto de desempenho negligenciável na aplicação web e nenhum custo de faturação.
Para alterações na aplicação web no hóspede, é necessária uma ativação separada para digitalizar ficheiros de código dentro de uma aplicação web. Para mais informações, consulte a [Análise de Alteração no Diagnóstico e resolva](https://docs.microsoft.com/azure/azure-monitor/app/change-analysis#application-change-analysis-in-the-diagnose-and-solve-problems-tool) a secção de ferramentas de problemas mais tarde neste artigo para obter mais detalhes.

## <a name="visualizations-for-application-change-analysis"></a>Visualizações para análise de alterações de aplicações

### <a name="standalone-ui"></a>UI autónomo

No Monitor Azure, existe um painel autónomo para a Análise de Alterações para ver todas as mudanças com insights sobre dependências e recursos de aplicações.

Procure a Change Analysis na barra de pesquisa no portal Azure para lançar a experiência.

![Screenshot de pesquisa de Change Analysis no portal Azure](./media/change-analysis/search-change-analysis.png)

Todos os recursos sob uma subscrição selecionada são apresentados com alterações das últimas 24 horas. Para otimizar o desempenho da carga da página, o serviço está a apresentar 10 recursos de cada vez. Clique nas páginas seguintes para ver mais recursos. Estamos a trabalhar para eliminar esta limitação.

![Screenshot da lâmina de análise de mudança no portal Azure](./media/change-analysis/change-analysis-standalone-blade.png)

Clicando num recurso para ver todas as suas alterações. Se necessário, desperte-a numa alteração para ver os detalhes e insights de mudança formatadoj.

![Screenshot dos detalhes da mudança](./media/change-analysis/change-details.png)

Para qualquer feedback, utilize o botão changeanalysisteam@microsoft.comde feedback de envio na lâmina ou no e-mail .

![Screenshot do botão de feedback na lâmina de análise de mudança](./media/change-analysis/change-analysis-feedback.png)

### <a name="web-app-diagnose-and-solve-problems"></a>Diagnóstico e Resolução de Problemas de Aplicação Web

No Monitor Azure, a Change Analysis também está integrada no autosserviço Diagnosticar e resolver a experiência de **problemas.** Aceda a esta experiência a partir da página **de visão geral** da sua aplicação App Service.

![Screenshot do botão "Visão geral" e do botão "Diagnosticar e resolver problemas"](./media/change-analysis/change-analysis.png)

### <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Análise de alterações de aplicação na ferramenta diagnosticar e resolver problemas

Application Change Analysis é um detetor autónomo na Web App diagnosticar e resolver ferramentas de problemas. Também é agregado em Falhas de **Aplicação** e **detetores web app down**. Ao entrar na ferramenta Diagnosticar e Resolver Problemas, o fornecedor de recursos **Microsoft.ChangeAnalysis** será automaticamente registado. Siga estas instruções para permitir o rastreio de alterações na aplicação web no hóspede.

1. Selecione **Disponibilidade e Desempenho.**

    ![Screenshot das opções de resolução de problemas "Disponibilidade e Desempenho"](./media/change-analysis/availability-and-performance.png)

2. Selecione **Alterações de Aplicação**. A funcionalidade também está disponível em Falhas de **Aplicação.**

   ![Screenshot do botão "Application Crashes"](./media/change-analysis/application-changes.png)

3. Para ativar a Análise de Alterações, **selecione 'Activar', agora**.

   ![Screenshot das opções de "Application Crashes"](./media/change-analysis/enable-changeanalysis.png)

4. Ligue a **Análise de Mudança** e selecione **Guardar**. A ferramenta exibe todas as aplicações web ao abrigo de um plano de Serviço de Aplicações. Pode utilizar o interruptor de nível de plano para ligar a Análise de Mudança para todas as aplicações web ao abrigo de um plano.

    ![Screenshot da interface de utilizador "Enable Change Analysis"](./media/change-analysis/change-analysis-on.png)

5. Para aceder à Análise de Alterações, selecione **Diagnosticar e resolver problemas** > **Disponibilidade e**Falhas de > **Aplicação**de Desempenho . Verá um gráfico que resume o tipo de mudanças ao longo do tempo juntamente com detalhes sobre essas alterações. Por predefinição, as alterações nas últimas 24 horas são apresentadas para ajudar com problemas imediatos.

     ![Screenshot da visão difusa de mudança](./media/change-analysis/change-view.png)

### <a name="enable-change-analysis-at-scale"></a>Ativar a Análise de Alterações à escala

Se a sua subscrição incluir numerosas aplicações web, permitir o serviço ao nível da aplicação web seria ineficiente. Execute o seguinte script para ativar todas as aplicações web na sua subscrição.

Pré-requisitos:

- Módulo PowerShell Az. Siga as instruções no [Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0)

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

### <a name="virtual-machine-diagnose-and-solve-problems"></a>Diagnóstico e Resolução de Problemas de Máquina Virtual

Vá para diagnosticar e resolver problemas para uma máquina virtual.  Vá a Ferramentas de **Resolução de Problemas,** navegue pela página e selecione **Analisar alterações recentes** para visualizar alterações na Máquina Virtual.

![Screenshot do VM Diagnosticar e Resolver Problemas](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Screenshot do VM Diagnosticar e Resolver Problemas](./media/change-analysis/analyze-recent-changes.png)

## <a name="next-steps"></a>Passos seguintes

- Ativar insights de aplicação para [aplicações de serviços de aplicações Azure](azure-web-apps.md).
- Ativar insights de aplicação para as aplicações de [escala virtual IIS azure VM e Azure](azure-vm-vmss-apps.md).
- Saiba mais sobre o Gráfico de [Recursos Azure,](https://docs.microsoft.com/azure/governance/resource-graph/overview)que ajuda a power Change Analysis.
