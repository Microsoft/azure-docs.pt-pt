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
ms.date: 05/31/2019
ms.author: mbullwin
ms.openlocfilehash: 6567d7f2ebaab5bd7b5bc8fb7b5a62970f169161
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476167"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Anotações em gráficos de métricas no Application Insights

Anotações no [Explorador de métricas](../../azure-monitor/app/metrics-explorer.md) gráficos mostram onde implementou uma nova compilação ou outro evento significativo. Eles facilitam ver se as suas alterações tinham qualquer impacto no desempenho da sua aplicação. Eles podem ser criados automaticamente pelos [sistema de compilação de serviços do Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/tasks/). Também pode criar anotações para sinalizar a qualquer evento que gosta, criando-los a partir do PowerShell.

> [!NOTE]
> Este artigo reflete o preterido **experiência de métricas clássicas**. Anotações só estão atualmente disponíveis na experiência do clássica e no  **[livros](../../azure-monitor/app/usage-workbooks.md)** . Para saber mais sobre a atual experiência de métricas, consulte [este artigo](../../azure-monitor/platform/metrics-charts.md).

![Exemplo de anotações](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-devops-services-build"></a>Anotações com serviços do Azure DevOps compilação da versão

Notas de versão são uma funcionalidade do serviço de Pipelines do Azure com base na cloud dos serviços de DevOps do Azure.

### <a name="install-the-annotations-extension-one-time"></a>Instalar a extensão de anotações (uma vez)
Para poder criar notas de versão, terá de instalar uma das muitas extensões de serviços de DevOps do Azure disponíveis no Visual Studio Marketplace.

1. Inicie sessão no seu [serviços do Azure DevOps](https://azure.microsoft.com/services/devops/) projeto.
2. No Visual Studio Marketplace [obter a extensão de notas de versão](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)e adicione-o à sua organização de serviços de DevOps do Azure.

![Selecione uma organização de DevOps do Azure e, em seguida, instale.](./media/annotations/1-install.png)

Apenas terá de fazer isto vez para a sua organização de serviços de DevOps do Azure. Notas de versão agora podem ser configuradas para qualquer projeto na sua organização.

### <a name="configure-release-annotations"></a>Configurar notas de versão

Tem de obter uma chave de API separada para cada modelo de versão dos serviços de DevOps do Azure.

1. Inicie sessão para o [portal do Microsoft Azure](https://portal.azure.com) e abra o recurso do Application Insights monitoriza a aplicação. (Ou [crie uma agora](../../azure-monitor/app/app-insights-overview.md), se ainda não tiver feito isso ainda.)
2. Abra o **acesso à API** separador e copie a **ID de informações da aplicação**.
   
    ![Em portal.azure.com, abra o recurso do Application Insights e escolha definições. Abra o acesso à API. Copie o ID da aplicação](./media/annotations/2-app-id.png)

4. Numa janela do browser separado, abra (ou crie) o modelo de liberação que gere as suas implementações de serviços de DevOps do Azure.
   
    Adicionar uma tarefa e selecione a tarefa de anotação de versão do Application Insights a partir do menu.

   ![Clique no sinal a tarefa de adicionar e selecione a anotação de versão do Application Insights. Cole o ID de informações da aplicação.](./media/annotations/3-add-task.png)

    Colar o **ID da aplicação** que copiou do separador de acesso à API.
   
    ![Cole o ID de informações da aplicação](./media/annotations/4-paste-app-id.png)

5. Na janela do Azure, crie uma nova chave de API e fazer uma cópia do mesmo.
   
    ![No separador de acesso à API na janela do Azure, clique em Criar chave de API.](./media/annotations/5-create-api-key.png)

    ![No separador de chave a criar API fornecer um comentário, verifique as anotações de escrita e clique em Gerar chave. Copie a nova chave.](./media/annotations/6-create-api-key.png)

6. Abra o separador de configuração do modelo de liberação.
   
    Criar uma definição de variável para `ApiKey`.
   
    Cole a chave de API para a definição de variável ApiKey.
   
    ![Na janela de serviços de DevOps do Azure, selecione o separador de variável e clique em Adicionar. Defina o nome para ApiKey e no valor, cole a chave que gerou e clique no ícone de bloqueio.](./media/annotations/7-paste-api-key.png)
1. Por fim, **guardar** pipeline de versões.


## <a name="view-annotations"></a>Ver anotações
Agora, sempre que utilizar o modelo de liberação para implementar uma nova versão, uma anotação será enviada para o Application Insights. As anotações aparecerá em gráficos no Explorador de métricas.

Clique em qualquer marcador de anotação (seta cinza claro) para abrir os detalhes sobre a versão, incluindo o requerente, ramo de controle de origem, versão de pipeline, o ambiente e muito mais.

![Clique em qualquer marcador de anotação de versão.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Criar anotações personalizadas do PowerShell
Também pode criar anotações a partir de qualquer processo que quiser (sem utilizar serviços de DevOps do Azure). 


1. Faça uma cópia local do [script do Powershell do GitHub](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

2. Obtenha o ID da aplicação e crie uma chave de API do separador de acesso à API.

3. Chame o script como este:

```PS

     .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }
```

É fácil modificar o script, por exemplo, para criar anotações para o passado.

## <a name="next-steps"></a>Passos Seguintes

* [Criar itens de trabalho](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automação com PowerShell](../../azure-monitor/app/powershell.md)
