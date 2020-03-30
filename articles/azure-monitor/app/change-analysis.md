---
title: Utilizar análise de alterações de aplicações no Monitor Azure para encontrar problemas com aplicações web Microsoft Docs
description: Utilize a Análise de Alterações de Aplicações no Monitor Azure para resolver problemas de aplicações em sites ao vivo no Serviço de Aplicações Azure.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: 036b8c084bdfdc11c02274758c550c76bdc7b1e7
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348738"
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

Atualmente a Change Analysis está integrada no Diagnóstico e resolve a experiência de **problemas** na aplicação web do App Service, bem como disponível como um separador autónomo no portal Azure.
Consulte as alterações de *Visualização de todos os recursos na* secção Azure para aceder à lâmina de análise de alterações e à secção de *funcionalidades de Análise de Alterações para as Aplicações Web* para a sua utilização no portal da Web App mais tarde neste artigo.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Gestor de Recursos Azure rastreou as mudanças de propriedades

Utilizando o [Azure Resource Graph,](https://docs.microsoft.com/azure/governance/resource-graph/overview)a Change Analysis fornece um registo histórico de como os recursos Azure que acolhem a sua aplicação mudaram ao longo do tempo. Configurações rastreadas, tais como identidades geridas, upgrade do Os da Plataforma e nomes de anfitriões podem ser detetados.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Gestor de Recursos Azure proxiu alterações de definição
As definições como a regra de configuração IP, as definições de TLS e as versões de extensão ainda não estão disponíveis em ARG, por isso as consultas de Análise de Alteração e calcula estas alterações de forma segura para fornecer mais detalhes sobre o que mudou na aplicação. Esta informação ainda não está disponível no Azure Resource Graph, mas estará disponível em breve.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Alterações na implementação e configuração da aplicação web (alterações no hóspede)

A Change Analysis capta o estado de implantação e configuração de uma aplicação a cada 4 horas. Pode detetar, por exemplo, alterações nas variáveis ambientais de aplicação. A ferramenta calcula as diferenças e apresenta o que mudou. Ao contrário das alterações do Gestor de Recursos, as informações de alteração de mudança de código podem não estar disponíveis imediatamente na ferramenta. Para ver as últimas alterações na Análise de Alterações, selecione **alterações de digitalização agora**.

![Screenshot do botão "Scan changes now"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Alterações de dependência

As alterações às dependências de recursos também podem causar problemas numa aplicação web. Por exemplo, se uma aplicação web ligar para uma cache Redis, o Redis cache SKU pode afetar o desempenho da aplicação web. Para detetar alterações nas dependências, a Change Analysis verifica o registo dNS da aplicação web. Desta forma, identifica alterações em todos os componentes da aplicação que podem causar problemas.
Atualmente, apoiam-se as seguintes dependências:
- Aplicações Web
- Storage do Azure
- SQL do Azure

### <a name="enablement"></a>Enablement
O fornecedor de recursos "Microsoft.ChangeAnalysis" precisa de ser registado com uma subscrição para o Gestor de Recursos Azure, rastreadas propriedades e configurações de configurações proxidas que alteram dados para estarem disponíveis. À medida que entra na ferramenta de diagnóstico e resolução de problemas da Web App ou se levanta o separador 'Change Analysis' autónomo, este fornecedor de recursos é automaticamente registado. Não possui qualquer desempenho e implementação de custos para a sua subscrição. Quando ativar a Change Analysis para aplicações web (ou ativar na ferramenta diagnosticar e resolver problemas), terá um impacto de desempenho negligenciável na aplicação web e nenhum custo de faturação.
Para alterações na aplicação web no hóspede, é necessária uma ativação separada para digitalizar ficheiros de código dentro de uma aplicação web. Para mais informações, consulte [a Enable Change Analysis no Diagnóstico e resolva](https://docs.microsoft.com/azure/azure-monitor/app/change-analysis#enable-change-analysis-in-the-diagnose-and-solve-problems-tool) os problemas da secção de ferramentas mais tarde neste artigo para obter mais detalhes.


## <a name="viewing-changes-for-all-resources-in-azure"></a>Ver alterações para todos os recursos em Azure
No Monitor Azure, existe uma lâmina autónoma para a Change Analysis para ver todas as mudanças com insights e recursos de dependência de aplicações.

Procure a Análise de Mudança na barra de pesquisa no portal Azure para lançar a lâmina.

![Screenshot de pesquisa de Change Analysis no portal Azure](./media/change-analysis/search-change-analysis.png)

Selecione Grupo de Recursos e recursos para começar a visualizar alterações.

![Screenshot da lâmina de análise de mudança no portal Azure](./media/change-analysis/change-analysis-standalone-blade.png)

Pode ver Insights e recursos de dependências relacionadas que acolhem a sua aplicação. Esta visão foi concebida para ser centrada na aplicação para os desenvolvedores resolverem problemas.

Os recursos atualmente apoiados incluem:
- Máquinas Virtuais
- Conjunto de escala de máquina virtual
- Recursos de Networking Azure
- Aplicação web com rastreio de ficheiros no hóspede e alterações de variáveis ambientais

Para qualquer feedback, utilize o botão changeanalysisteam@microsoft.comde feedback de envio na lâmina ou no e-mail .

![Screenshot do botão de feedback na lâmina de análise de mudança](./media/change-analysis/change-analysis-feedback.png)

## <a name="change-analysis-for-the-web-apps-feature"></a>Alterar análise para a funcionalidade Aplicações Web

No Monitor Azure, a Change Analysis também está integrada no autosserviço Diagnosticar e resolver a experiência de **problemas.** Aceda a esta experiência a partir da página **de visão geral** da sua aplicação App Service.

![Screenshot do botão "Visão geral" e do botão "Diagnosticar e resolver problemas"](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Ativar a Análise de Mudança na ferramenta diagnosticar e resolver problemas

1. Selecione **Disponibilidade e Desempenho.**

    ![Screenshot das opções de resolução de problemas "Disponibilidade e Desempenho"](./media/change-analysis/availability-and-performance.png)

1. Selecione **Alterações de Aplicação**. Não que a funcionalidade também esteja disponível em Falhas de **Aplicação.**

   ![Screenshot do botão "Application Crashes"](./media/change-analysis/application-changes.png)

1. Para ativar a Análise de Alterações, **selecione 'Activar', agora**.

   ![Screenshot das opções de "Application Crashes"](./media/change-analysis/enable-changeanalysis.png)

1. Ligue a **Análise de Mudança** e selecione **Guardar**. A ferramenta exibe todas as aplicações web ao abrigo de um plano de Serviço de Aplicações. Pode utilizar o interruptor de nível de plano para ligar a Análise de Mudança para todas as aplicações web ao abrigo de um plano.

    ![Screenshot da interface de utilizador "Enable Change Analysis"](./media/change-analysis/change-analysis-on.png)


1. Para aceder à Análise de Alterações, selecione **Diagnosticar e resolver problemas** > **Disponibilidade e**Falhas de > **Aplicação**de Desempenho . Verá um gráfico que resume o tipo de mudanças ao longo do tempo juntamente com detalhes sobre essas alterações. Por predefinição, as alterações nas últimas 24 horas são apresentadas para ajudar com problemas imediatos.

     ![Screenshot da visão difusa de mudança](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Ativar a Análise de Alterações à escala

Se a sua subscrição incluir numerosas aplicações web, permitir o serviço ao nível da aplicação web seria ineficiente. Execute o seguinte script para ativar todas as aplicações web na sua subscrição.

Pré-requisitos:
* Módulo PowerShell Az. Siga as instruções no [Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0)

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



## <a name="next-steps"></a>Passos seguintes

- Ativar insights de aplicação para [aplicações de serviços de aplicações Azure](azure-web-apps.md).
- Ativar insights de aplicação para as aplicações de [escala virtual IIS azure VM e Azure](azure-vm-vmss-apps.md).
- Saiba mais sobre o Gráfico de [Recursos Azure,](https://docs.microsoft.com/azure/governance/resource-graph/overview)que ajuda a power Change Analysis.
