---
title: Anotações do Application Insights da versão | Documentos da Microsoft
description: Adicionar implementação ou criar marcadores para seus gráficos do Explorador de métricas no Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mbullwin
ms.openlocfilehash: e3ec202ba6126b150fb78c76591682f163018661
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604547"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Anotações em gráficos de métricas no Application Insights

Anotações no [Explorador de métricas](../../azure-monitor/app/metrics-explorer.md) gráficos mostram onde implementou uma nova compilação, ou outros eventos significativos. Anotações facilitam ver se as suas alterações tinham qualquer impacto no desempenho da sua aplicação. Eles podem ser criados automaticamente pelos [Pipelines do Azure](https://docs.microsoft.com/azure/devops/pipelines/tasks/) sistema de compilação. Também pode criar anotações para sinalizar a qualquer evento que gosta, criando-los a partir do PowerShell.

> [!NOTE]
> Este artigo reflete o preterido **experiência de métricas clássicas**. Anotações só estão atualmente disponíveis na experiência do clássica e no  **[livros](../../azure-monitor/app/usage-workbooks.md)** . Para saber mais sobre a experiência de métricas atual, veja [recursos avançados do Explorador de métricas do Azure](../../azure-monitor/platform/metrics-charts.md).

![Exemplo de anotações](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Anotações com Pipelines do Azure compilação da versão

Notas de versão são uma funcionalidade do serviço de Pipelines do Azure com base na cloud do Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Instalar a extensão de anotações (uma vez)
Para poder criar notas de versão, terá de instalar uma das muitas extensões de DevOps do Azure disponíveis no Visual Studio Marketplace.

1. Inicie sessão no seu [do Azure DevOps](https://azure.microsoft.com/services/devops/) projeto.
   
1. No Visual Studio Marketplace [extensão de notas de versão](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) página, selecione a sua organização de DevOps do Azure e, em seguida, selecione **instalar** para adicionar a extensão para a sua organização de DevOps do Azure.
   
   ![Selecione uma organização de DevOps do Azure e, em seguida, selecione instalar.](./media/annotations/1-install.png)
   
Apenas terá de instalar a extensão de uma vez para a sua organização de DevOps do Azure. Agora, pode configurar notas de versão para qualquer projeto na sua organização.

### <a name="configure-release-annotations"></a>Configurar notas de versão

Crie uma chave de API separada para cada um dos seus modelos de lançamento de Pipelines do Azure.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e abra o recurso do Application Insights monitoriza a aplicação. Ou se não tiver uma, [criar um novo recurso do Application Insights](../../azure-monitor/app/app-insights-overview.md).
   
1. Abra o **acesso à API** separador e copie a **ID de informações da aplicação**.
   
   ![Em acesso de API, copie o ID da aplicação.](./media/annotations/2-app-id.png)

1. Numa janela do browser separado, abrir ou criar o modelo de liberação que gere as suas implementações de Pipelines do Azure.
   
1. Selecione **adicionar tarefas**e, em seguida, selecione a **anotação de versão do Application Insights** tarefas no menu.
   
   ![Selecione a tarefa de adicionar e selecione a anotação de versão do Application Insights.](./media/annotations/3-add-task.png)
   
1. Sob **ID da aplicação**, cole o ID de informações da aplicação que copiou do **acesso à API** separador.
   
   ![Cole o ID de informações da aplicação](./media/annotations/4-paste-app-id.png)
   
1. Novamente no Application Insights **acesso à API** janela, selecione **criar chave de API**. 
   
   ![No separador de acesso à API, selecione Criar chave de API.](./media/annotations/5-create-api-key.png)
   
1. Na **criar chave de API** janela, escreva uma descrição, selecione **escrever anotações**e, em seguida, selecione **gerar chave**. Copie a nova chave.
   
   ![Na janela de chave de API de criar, escreva uma descrição, selecione as anotações de escrita e, em seguida, selecione a gerar chave.](./media/annotations/6-create-api-key.png)
   
1. Na janela de modelo de versão, sobre o **variáveis** separador, selecione **Add** para criar uma definição de variável para a nova chave de API.

1. Sob **Name**, introduza `ApiKey`e, em **valor**, cole a chave de API que copiou do **acesso à API** separador.
   
   ![No separador variáveis de DevOps do Azure, selecione Add, o nome da variável ApiKey e cole a chave de API em valor.](./media/annotations/7-paste-api-key.png)
   
1. Selecione **guardar** na janela de modelo de lançamento principal para guardar o modelo.

## <a name="view-annotations"></a>Ver anotações
Agora, sempre que utilizar o modelo de liberação para implementar uma nova versão, uma anotação é enviada para o Application Insights. As anotações são apresentados nos gráficos no **Explorador de métricas**.

Selecione qualquer marcador de anotação (seta a cinzenta claro) para abrir os detalhes sobre a versão, incluindo o requerente, o ramo de controle de origem, o pipeline de lançamento e o ambiente.

![Selecione um marcador de anotação de versão.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Criar anotações personalizadas do PowerShell
Pode utilizar o [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) script do PowerShell do GitHub para criar anotações a partir de qualquer processo que quiser, sem utilizar o Azure DevOps. 

1. Faça uma cópia local do [CreateReleaseAnnotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Utilize os passos do procedimento anterior para obter sua ID de informações da aplicação e criar uma chave de API do Application insights **acesso à API** separador.
   
1. Chame o script do PowerShell com o código seguinte, substituindo os marcadores de posição entre parênteses ângulo com os seus valores. O `-releaseProperties` são opcionais. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Pode modificar o script, por exemplo, para criar anotações para o passado.

## <a name="next-steps"></a>Passos Seguintes

* [Criar itens de trabalho](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automação com PowerShell](../../azure-monitor/app/powershell.md)
