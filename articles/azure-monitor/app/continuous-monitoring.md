---
title: Monitorização contínua do seu oleoduto de libertação de DevOps com Azure Pipelines e Azure Application Insights | Microsoft Docs
description: Fornece instruções para configurar rapidamente a monitorização contínua com insights de aplicação
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: fd7cd6a107ed45adb60167a57661b60be5dc8212
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86517132"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Adicione monitorização contínua ao seu oleoduto de libertação

A Azure Pipelines integra-se com a Azure Application Insights para permitir uma monitorização contínua do seu oleoduto de libertação de DevOps durante todo o ciclo de vida de desenvolvimento de software. 

Com monitorização contínua, os oleodutos de libertação podem incorporar dados de monitorização da Application Insights e outros recursos Azure. Quando o gasoduto de desbloqueio detetar um alerta de Insights de Aplicação, o gasoduto pode portão ou retroceda a colocação até que o alerta seja resolvido. Se todos os controlos passarem, as implementações podem proceder automaticamente do teste até à produção, sem necessidade de intervenção manual. 

## <a name="configure-continuous-monitoring"></a>Configure a monitorização contínua

1. Em [Azure DevOps,](https://dev.azure.com)selecione uma organização e projeto.
   
1. No menu esquerdo da página do projeto, selecione  >  **Pipelines Releases**. 
   
1. Desça a seta ao lado **de New** e selecione Novo oleoduto **de lançamento**. Ou, se ainda não tiver um oleoduto, selecione **Novo oleoduto** na página que aparece.
   
1. No painel **de modelo Selecione um** painel de modelo, procure e selecione a **implementação do Serviço de Aplicações Azure com monitorização contínua** e, em seguida, selecione **Apply**. 

   ![Novo oleoduto Azure](media/continuous-monitoring/001.png)

1. Na caixa **fase 1,** selecione a hiperligação para **ver as tarefas do palco.**

   ![Ver tarefas de fase](media/continuous-monitoring/002.png)

1. No painel de configuração **fase 1,** complete os seguintes campos: 

    | Parâmetro        | Valor |
   | ------------- |:-----|
   | **Nome da fase**      | Forneça um nome artístico, ou deixe-o na **fase 1**. |
   | **Subscrição do Azure** | Desça e selecione a subscrição Azure ligada que pretende utilizar.|
   | **Tipo de aplicação** | Desça e selecione o seu tipo de aplicação. |
   | **Nome do Serviço de Aplicações** | Insira o nome do seu Serviço de Aplicações Azure. |
   | **Nome do Grupo de Recursos para Insights de Aplicações**    | Desça e selecione o grupo de recursos que pretende utilizar. |
   | **Nome de recurso Application Insights** | Desça e selecione o recurso Application Insights para o grupo de recursos selecionado.

1. Para guardar o gasoduto com definições de regra de alerta predefinidas, **selecione Guarde** na parte superior direita na janela Azure DevOps. Introduza um comentário descritivo e, em seguida, selecione **OK**.

## <a name="modify-alert-rules"></a>Modificar as regras de alerta

Fora da caixa, a implementação do Serviço de **Aplicações Azure com** o modelo de monitorização contínua tem quatro regras de alerta: **Disponibilidade,** **Pedidos Falhados,** **Tempo de resposta do Servidor** e **exceções do Servidor**. Pode adicionar mais regras ou alterar as definições de regras para atender às necessidades do seu nível de serviço. 

Para modificar as definições da regra de alerta:

No painel esquerdo da página do pipeline de lançamento, selecione **Configure Application Insights Alerts**.

As quatro regras de alerta predefinidos são criadas através de um script Inline:

```bash
$subscription = az account show --query "id";$subscription.Trim("`"");$resource="/subscriptions/$subscription/resourcegroups/"+"$(Parameters.AppInsightsResourceGroupName)"+"/providers/microsoft.insights/components/" + "$(Parameters.ApplicationInsightsResourceName)";
az monitor metrics alert create -n 'Availability_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'avg availabilityResults/availabilityPercentage < 99' --description "created from Azure DevOps";
az monitor metrics alert create -n 'FailedRequests_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'count requests/failed > 5' --description "created from Azure DevOps";
az monitor metrics alert create -n 'ServerResponseTime_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'avg requests/duration > 5' --description "created from Azure DevOps";
az monitor metrics alert create -n 'ServerExceptions_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'count exceptions/server > 5' --description "created from Azure DevOps";
```

Pode modificar o script e adicionar regras de alerta adicionais, modificar as condições de alerta ou remover regras de alerta que não fazem sentido para os seus fins de implantação.

## <a name="add-deployment-conditions"></a>Adicionar condições de implantação

Quando adiciona portões de implantação ao seu oleoduto de desbloqueio, um alerta que exceda os limiares definidos impede a promoção indesejada da libertação. Uma vez resolvido o alerta, a implantação pode proceder automaticamente.

Para adicionar portões de implantação:

1. Na página principal do gasoduto, em **Fases,** selecione as **condições de pré-implantação** ou o símbolo **das condições de pós-implantação,** dependendo do estágio que necessita de um portão de monitorização contínuo.
   
   ![Condições de pré-implantação](media/continuous-monitoring/004.png)
   
1. No painel de configuração das **condições de pré-implantação,** desace **as portas** para **ativar**.
   
1. Junto aos **portões de implantação**, selecione **Adicionar**.
   
1. Selecione **alertas de Monitor de Consulta Azure** a partir do menu suspenso. Esta opção permite-lhe aceder aos alertas Azure Monitor e Application Insights.
   
   ![Alertas de Monitor de Consulta Azure](media/continuous-monitoring/005.png)
   
1. Nas **opções de Avaliação**, insira os valores que pretende para configurações como **o tempo entre a reavaliação dos portões** e o tempo limite após o qual os **portões falham**. 

## <a name="view-release-logs"></a>Ver registos de lançamento

Pode ver o comportamento do portão de implantação e outros passos de desbloqueio nos registos de libertação. Para abrir os registos:

1. Selecione **Versões** do menu esquerdo da página do pipeline. 
   
1. Selecione qualquer libertação. 
   
1. Em **Fases**, selecione qualquer fase para ver um resumo de lançamento. 
   
1. Para visualizar registos, **selecione Ver registos** no resumo do lançamento, selecione a hiperligação **bem sucedida** ou **falhada** em qualquer fase ou paire sobre qualquer fase e selecione **Registos**. 
   
   ![Ver registos de lançamento](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os Gasodutos Azure, consulte a documentação da [Azure Pipelines](/azure/devops/pipelines).
