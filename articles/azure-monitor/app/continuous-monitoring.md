---
title: Monitorização contínua do seu gasoduto de lançamento de DevOps com Pipelines Azure e Insights de Aplicação Azure [ Microsoft Docs
description: Fornece instruções para configurar rapidamente a monitorização contínua com insights de aplicação
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 0d47fb1eccdfcfc7b2719825575f06dc85e62452
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652757"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Adicione monitorização contínua ao seu gasoduto de libertação

A Azure Pipelines integra-se com a Azure Application Insights para permitir a monitorização contínua do seu pipeline de lançamento de DevOps ao longo do ciclo de vida de desenvolvimento de software. 

Com monitorização contínua, os gasodutos de libertação podem incorporar dados de monitorização a partir de Application Insights e outros recursos Azure. Quando o gasoduto de libertação detetar um alerta de Informação de Aplicação, o gasoduto pode portãor ou reverter a implantação até que o alerta seja resolvido. Se todos os controlos passarem, as implementações podem proceder automaticamente do teste até à produção, sem necessidade de intervenção manual. 

## <a name="configure-continuous-monitoring"></a>Configurar a monitorização contínua

1. Em [Azure DevOps,](https://dev.azure.com)selecione uma organização e projeto.
   
1. No menu esquerdo da página do projeto, selecione **Lançamentos** > de**Pipelines**. 
   
1. Deite a seta ao lado de **New** e selecione Novo oleoduto de **libertação**. Ou, se ainda não tiver um oleoduto, selecione **Novo oleoduto** na página que aparece.
   
1. No painel **de modeloS Selecione um** painel de modelos, procure e selecione a implementação do Serviço de **Aplicações Azure com monitorização contínua**, e, em seguida, selecione **Apply**. 

   ![Novo oleoduto Azure lança oleoduto](media/continuous-monitoring/001.png)

1. Na caixa **do Estágio 1,** selecione a hiperligação para **ver tarefas** de palco.

   ![Ver tarefas de palco](media/continuous-monitoring/002.png)

1. No painel de configuração **do estágio 1,** preencha os seguintes campos: 

    | Parâmetro        | Valor |
   | ------------- |:-----|
   | **Nome da fase**      | Forneça um nome artístico ou deixe-o no **estágio 1**. |
   | **Subscrição do Azure** | Desça e selecione a subscrição Azure ligada que pretende utilizar.|
   | **Tipo de aplicação** | Desça e selecione o seu tipo de aplicação. |
   | **Nome do serviço de aplicações** | Insira o nome do seu Serviço de Aplicações Azure. |
   | **Nome do Grupo de Recursos para Insights de Aplicação**    | Desça e selecione o grupo de recursos que pretende utilizar. |
   | **Nome de recurso De Insights de Aplicação** | Despete-se e selecione o recurso Application Insights para o grupo de recursos selecionado.

1. Para salvar o gasoduto com definições de regra de alerta predefinidos, selecione **Guardar** na parte superior direita na janela Azure DevOps. Introduza um comentário descritivo e, em seguida, selecione **OK**.

## <a name="modify-alert-rules"></a>Modificar regras de alerta

Fora da caixa, a implementação do Serviço de **Aplicações Azure com** modelo de monitorização contínua tem quatro regras de alerta: **Disponibilidade,** **pedidos falhados,** tempo de resposta do **servidor**e **exceções**ao Servidor . Pode adicionar mais regras ou alterar as definições de regra para atender às necessidades do seu nível de serviço. 

Para modificar as definições de regra de alerta:

No painel esquerdo da página do pipeline de lançamento, **selecione Configurar Alertas**de Insights de Aplicação .

As quatro regras de alerta padrão são criadas através de um script Inline:

```bash
$subscription = az account show --query "id";$subscription.Trim("`"");$resource="/subscriptions/$subscription/resourcegroups/"+"$(Parameters.AppInsightsResourceGroupName)"+"/providers/microsoft.insights/components/" + "$(Parameters.ApplicationInsightsResourceName)";
az monitor metrics alert create -n 'Availability_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'avg availabilityResults/availabilityPercentage < 99' --description "created from Azure DevOps";
az monitor metrics alert create -n 'FailedRequests_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'count requests/failed > 5' --description "created from Azure DevOps";
az monitor metrics alert create -n 'ServerResponseTime_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'avg requests/duration > 5' --description "created from Azure DevOps";
az monitor metrics alert create -n 'ServerExceptions_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'count exceptions/server > 5' --description "created from Azure DevOps";
```

Pode modificar o script e adicionar regras adicionais de alerta, modificar as condições de alerta ou remover regras de alerta que não façam sentido para os seus propósitos de implementação.

## <a name="add-deployment-conditions"></a>Adicionar condições de implantação

Ao adicionar portões de implantação ao seu gasoduto de libertação, um alerta que excede os limiares estabelecidos impede a promoção indesejada de lançamento. Uma vez que resolva o alerta, a implementação pode proceder automaticamente.

Para adicionar portões de implantação:

1. Na página principal do gasoduto, em **Fases**, selecione as **condições de pré-implantação** ou o símbolo das **condições de pós-implantação,** dependendo de qual fase necessita de um portão de monitorização contínuo.
   
   ![Condições de pré-implantação](media/continuous-monitoring/004.png)
   
1. No painel de configuração das **condições de pré-implantação,** coloque **as portas** **ativadas**.
   
1. Ao lado dos portões de **implantação,** selecione **Adicionar**.
   
1. Selecione **alertas do Query Azure Monitor** a partir do menu suspenso. Esta opção permite-lhe aceder tanto aos alertas do Azure Monitor como do Application Insights.
   
   ![Alertas de Monitor De Consulta Azure](media/continuous-monitoring/005.png)
   
1. Nas **opções de Avaliação,** introduza os valores que pretende para configurações como **o tempo entre a reavaliação dos portões** e o intervalo após o qual os **portões falham**. 

## <a name="view-release-logs"></a>Ver registos de lançamento

Pode ver o comportamento do portão de implantação e outros passos de libertação nos registos de libertação. Para abrir os registos:

1. Selecione **Lançamentos** a partir do menu esquerdo da página do pipeline. 
   
1. Selecione qualquer lançamento. 
   
1. Em **Fases,** selecione qualquer palco para ver um resumo de lançamento. 
   
1. Para visualizar registos, selecione **Registos de visualização** no resumo de lançamento, selecione a hiperligação **bem sucedida** ou **falhada** em qualquer fase, ou paire sobre qualquer fase e selecione **Registos**. 
   
   ![Ver registos de lançamento](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os oleodutos Azure, consulte a documentação dos [Gasodutos Azure.](https://docs.microsoft.com/azure/devops/pipelines)
