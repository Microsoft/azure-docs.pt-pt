---
title: Anotações de versão para Application Insights | Microsoft Docs
description: Adicione os marcadores de implantação ou de Build aos seus gráficos de métricas do Gerenciador em Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/01/2019
ms.openlocfilehash: f9ab1f1ef2b64c07a3b0c8ddf93d6ef4293517db
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73668121"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Anotações em gráficos de métricas no Application Insights

Anotações em gráficos de [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) mostram onde você implantou uma nova compilação ou outros eventos significativos. As anotações facilitam a visualização se as alterações tiveram algum efeito sobre o desempenho do aplicativo. Eles podem ser criados automaticamente pelo sistema [Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/tasks/) Build. Você também pode criar anotações para sinalizar qualquer evento que desejar, criando-as no PowerShell.

> [!NOTE]
> Este artigo reflete a experiência de **métricas clássicas**preteridas. As anotações estão atualmente disponíveis na experiência clássica e nas pastas de **[trabalho](../../azure-monitor/app/usage-workbooks.md)** . Para saber mais sobre a experiência atual de métricas, consulte [recursos avançados do Azure Metrics Explorer](../../azure-monitor/platform/metrics-charts.md).

![Exemplo de anotações](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Anotações de versão com Azure Pipelines Build

As anotações de versão são um recurso do serviço Azure Pipelines baseado em nuvem do Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Instalar a extensão de anotações (uma vez)
Para poder criar anotações de versão, você precisará instalar uma das muitas extensões do Azure DevOps disponíveis no Visual Studio Marketplace.

1. Entre no seu projeto [DevOps do Azure](https://azure.microsoft.com/services/devops/) .
   
1. Na página [extensão de anotações](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) de Visual Studio Marketplace versão, selecione sua organização do Azure DevOps e, em seguida, selecione **instalar** para adicionar a extensão à sua organização do DevOps do Azure.
   
   ![Selecione uma organização do Azure DevOps e, em seguida, selecione instalar.](./media/annotations/1-install.png)
   
Você só precisa instalar a extensão uma vez para sua organização DevOps do Azure. Agora você pode configurar anotações de versão para qualquer projeto em sua organização.

### <a name="configure-release-annotations"></a>Configurar anotações de versão

Crie uma chave de API separada para cada um dos modelos de versão de Azure Pipelines.

1. Entre no [portal do Azure](https://portal.azure.com) e abra o recurso Application insights que monitora seu aplicativo. Ou, se você não tiver uma, [crie um novo recurso de Application insights](../../azure-monitor/app/app-insights-overview.md).
   
1. Abra a guia **acesso à API** e copie a **ID de Application insights**.
   
   ![Em acesso à API, copie a ID do aplicativo.](./media/annotations/2-app-id.png)

1. Em uma janela separada do navegador, abra ou crie o modelo de versão que gerencia suas implantações de Azure Pipelines.
   
1. Selecione **Adicionar tarefa**e, em seguida, selecione a Application insights tarefa de **anotação de liberação** no menu.
   
   ![Selecione Adicionar tarefa e selecione Application Insights anotação de versão.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > A tarefa de anotação de versão atualmente dá suporte apenas a agentes baseados no Windows; Ele não será executado no Linux, no macOS ou em outros tipos de agentes.
   
1. Em **ID do aplicativo**, Cole a id de Application insights que você copiou da guia **acesso à API** .
   
   ![Cole a ID de Application Insights](./media/annotations/4-paste-app-id.png)
   
1. De volta à janela de **acesso Application insights API** , selecione **criar chave de API**. 
   
   ![Na guia acesso à API, selecione criar chave de API.](./media/annotations/5-create-api-key.png)
   
1. Na janela **criar chave de API** , digite uma descrição, selecione **anotações de gravação**e, em seguida, selecione **gerar chave**. Copie a nova chave.
   
   ![Na janela criar chave de API, digite uma descrição, selecione anotações de gravação e, em seguida, selecione gerar chave.](./media/annotations/6-create-api-key.png)
   
1. Na janela modelo de versão, na guia **variáveis** , selecione **Adicionar** para criar uma definição de variável para a nova chave de API.

1. Em **nome**, insira `ApiKey`e, em **valor**, Cole a chave de API que você copiou da guia **acesso à API** .
   
   ![Na guia variáveis do Azure DevOps, selecione Adicionar, nomeie a variável ApiKey e cole a chave de API em valor.](./media/annotations/7-paste-api-key.png)
   
1. Selecione **salvar** na janela principal do modelo de liberação para salvar o modelo.

## <a name="view-annotations"></a>Exibir anotações
Agora, sempre que você usar o modelo de versão para implantar uma nova versão, uma anotação será enviada para Application Insights. As anotações aparecem em gráficos em **Metrics Explorer**.

Selecione qualquer marcador de anotação (seta cinza-claro) para abrir detalhes sobre a versão, incluindo o solicitante, o Branch de controle do código-fonte, o pipeline de liberação e o ambiente.

![Selecione um marcador de anotação de versão.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Criar anotações personalizadas do PowerShell
Você pode usar o script do PowerShell do [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) do GitHub para criar anotações de qualquer processo que desejar, sem usar o DevOps do Azure. 

1. Faça uma cópia local de [CreateReleaseAnnotation. ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Use as etapas no procedimento anterior para obter sua ID de Application Insights e criar uma chave de API na guia **acesso à api** Application insights.
   
1. Chame o script do PowerShell com o código a seguir, substituindo os espaços reservados entre colchetes angulares por seus valores. As `-releaseProperties` são opcionais. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Você pode modificar o script, por exemplo, para criar anotações para o passado.

## <a name="next-steps"></a>Passos seguintes

* [Criar itens de trabalho](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automação com o PowerShell](../../azure-monitor/app/powershell.md)
