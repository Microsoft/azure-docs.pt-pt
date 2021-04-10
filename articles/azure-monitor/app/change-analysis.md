---
title: Use a análise de mudança de aplicação no Azure Monitor para encontrar problemas de aplicações web | Microsoft Docs
description: Utilize a Análise de Alteração de Aplicações no Azure Monitor para resolver problemas de aplicações em sites ao vivo no Azure App Service.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: 43ece2cb0f5cb9428d8d73f769018e9fe2408ab8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655826"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Utilizar a Análise de Alteração de Aplicações (pré-visualização) no Monitor Azure

Quando ocorre um problema ou interrupção do site ao vivo, determinar rapidamente a causa da raiz é fundamental. As soluções de monitorização padrão podem alertá-lo para um problema. Podem até indicar que componente está a falhar. Mas este alerta nem sempre explica imediatamente a causa do fracasso. Sabes que o teu site funcionou há cinco minutos, e agora está avariado. O que mudou nos últimos cinco minutos? Esta é a questão que a Análise de Alteração de Aplicações foi concebida para responder no Azure Monitor.

Com base na potência do [Azure Resource Graph,](../../governance/resource-graph/overview.md)a Change Analysis fornece insights sobre as alterações da sua aplicação Azure para aumentar a observabilidade e reduzir o MTTR (tempo médio de reparação).

> [!IMPORTANT]
> A Análise de Alterações está atualmente em pré-visualização. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço. Esta versão não é recomendada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas ou podem ter capacidades restritas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Descrição Geral

A Análise de Alterações deteta vários tipos de alterações, desde a camada de infraestrutura até à implementação da aplicação. É um fornecedor de recursos Azure de nível de subscrição que verifica as alterações de recursos na subscrição. A Change Analysis fornece dados para várias ferramentas de diagnóstico para ajudar os utilizadores a entender que mudanças podem ter causado problemas.

O seguinte diagrama ilustra a arquitetura da Análise de Mudanças:

![Diagrama de arquitetura de como a Change Analysis obtém dados de mudança e fornece-os às ferramentas do cliente](./media/change-analysis/overview.png)

## <a name="supported-resource-types"></a>Tipos de recursos suportados

O serviço application Change Analysis suporta alterações no nível de propriedade de recursos em todos os tipos de recursos Azure, incluindo recursos comuns como:
- Máquina Virtual
- Conjuntos de dimensionamento de máquinas virtuais
- Serviço de Aplicações
- Serviço Azure Kubernetes
- Função do Azure
- Recursos de rede: Grupo de Segurança de Rede, Rede Virtual, Gateway de Aplicação, etc.
- Serviços de dados: Armazenamento, SQL, Redis Cache, Cosmos DB, etc.

## <a name="data-sources"></a>Origens de dados

As consultas de análise de alterações de aplicações para O Gestor de Recursos Azure rastrearam propriedades, configurações proxied e alterações de aplicações web no hóspede. Além disso, o serviço rastreia as alterações de dependência de recursos para diagnosticar e monitorizar uma aplicação de ponta a ponta.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Azure Resource Manager rastreou as mudanças de propriedades

Utilizando [o Azure Resource Graph](../../governance/resource-graph/overview.md), a Change Analysis fornece um registo histórico de como os recursos Azure que acolhem a sua aplicação mudaram ao longo do tempo. Podem ser detetadas definições rastreadas como identidades geridas, upgrade do Sistema operativo da Plataforma e hostnames.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure Resource Manager proxied alterações de definição

As definições como a regra de configuração IP, as definições de TLS e as versões de extensão ainda não estão disponíveis no Gráfico de Recursos Azure, pelo que a Alteração de Análise consulta e calcula estas alterações de forma segura para fornecer mais detalhes sobre o que foi alterado na aplicação.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Alterações na implementação e configuração de aplicações web (alterações no hóspede)

A Análise de Alteração captura o estado de implantação e configuração de uma aplicação a cada 4 horas. Pode detetar, por exemplo, alterações nas variáveis do ambiente de aplicação. A ferramenta calcula as diferenças e apresenta o que mudou. Ao contrário das alterações do Gestor de Recursos, as informações de alteração de implementação de código podem não estar disponíveis imediatamente na ferramenta. Para ver as últimas alterações na Análise de Alterações, **selecione Refresh**.

![Screenshot do botão "Scan changes now"](./media/change-analysis/scan-changes.png)

Atualmente, todos os ficheiros baseados em texto na raiz do site **wwwroot** com as seguintes extensões são suportados:
- *.config
- *.xml
- *.json
- *.gema
- *.yml
- *.txt
- *.ini
- *.env

### <a name="dependency-changes"></a>Alterações de dependência

As alterações nas dependências de recursos também podem causar problemas num recurso. Por exemplo, se uma aplicação web ligar para uma cache Redis, o SKU de cache Redis pode afetar o desempenho da aplicação web. Outro exemplo é que se a porta 22 foi fechada num Grupo de Segurança de Rede de Máquinas Virtuais, causará erros de conectividade.

#### <a name="web-app-diagnose-and-solve-problems-navigator-preview"></a>Web App diagnosticar e resolver problemas navegador (Pré-visualização)

Para detetar alterações nas dependências, a Change Analysis verifica o registo dns da aplicação web. Desta forma, identifica alterações em todos os componentes da aplicação que podem causar problemas.
Atualmente, as seguintes dependências são suportadas no **Diagnóstico de Aplicações Web e resolvem problemas | Navegador (Pré-visualização):**

- Aplicações Web
- Storage do Azure
- SQL do Azure

#### <a name="related-resources"></a>Recursos relacionados

A Análise de Alteração de Aplicação deteta recursos relacionados. Exemplos comuns são Network Security Group, Virtual Network, Application Gateway e Load Balancer relacionados com uma Máquina Virtual.
Os recursos de rede são geralmente aprovisionados automaticamente no mesmo grupo de recursos que os recursos que o utilizam, pelo que a filtragem das alterações por grupo de recursos mostrará todas as alterações para a Máquina Virtual e recursos de rede relacionados.

![Screenshot das alterações de Rede](./media/change-analysis/network-changes.png)

## <a name="application-change-analysis-service-enablement"></a>Ativação do serviço de análise de alterações de aplicações

O serviço de Análise de Alterações de Aplicação calcula e agrega os dados das fontes de dados acima mencionadas. Fornece um conjunto de análises para que os utilizadores naveguem facilmente através de todas as alterações de recursos e identifiquem qual a mudança relevante no contexto de resolução de problemas ou monitorização.
O fornecedor de recursos "Microsoft.ChangeAnalysis" precisa de ser registado com uma subscrição para o Gestor de Recursos Azure que rastreou propriedades e definições proxied alterar dados para estar disponível. Ao introduzir a ferramenta de diagnóstico e resolução de problemas da Web App ou apresentar o separador Change Analysis autónomo, este fornecedor de recursos é automaticamente registado.
Para alterações na aplicação web no hóspede, é necessária uma ativação separada para digitalizar ficheiros de código dentro de uma aplicação web. Para obter mais informações, consulte a Análise de Alterações na secção [de Ferramentas de Diagnóstico e resolva problemas](change-analysis-visualizations.md#application-change-analysis-in-the-diagnose-and-solve-problems-tool) mais tarde neste artigo para obter mais detalhes.

## <a name="cost"></a>Custo
Application Change Analysis é um serviço gratuito - não incorre em qualquer custo de faturação para subscrições com o mesmo ativado. O serviço também não tem qualquer impacto de desempenho para digitalizar as alterações das propriedades do Azure Resource. Quando ativa a Análise de Alterações para aplicações web em aplicativos no hóspede (ou ativar a ferramenta de diagnóstico e resolver problemas), terá um impacto de desempenho negligenciável na aplicação web e nenhum custo de faturação.


## <a name="enable-change-analysis-at-scale-for-web-app-in-guest-file-and-environment-variable-changes"></a>Ativar a Análise de Alterações à escala para alterações variáveis de ficheiros e ambiente da Web App

Se a sua subscrição incluir numerosas aplicações web, ativar o serviço ao nível da aplicação web seria ineficiente. Execute o seguinte script para ativar todas as aplicações web na sua subscrição.

Pré-requisitos:

- Módulo PowerShell Az. Siga as instruções na [Instalação do módulo Azure PowerShell](/powershell/azure/install-az-ps)

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

- Saiba [mais sobre visualizações na Análise de Mudança](change-analysis-visualizations.md)
- Saiba como [resolver problemas na Análise de Mudanças](change-analysis-troubleshoot.md)
- Ativar insights de aplicações para [aplicações Azure App Services](azure-web-apps.md).
- Ativar os Insights de Aplicações para [Azure VM e Azure série de máquinas virtuais definir aplicações hospedadas no IIS](azure-vm-vmss-apps.md).
