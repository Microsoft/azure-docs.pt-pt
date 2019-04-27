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
ms.date: 11/08/2018
ms.author: mbullwin
ms.openlocfilehash: 652591fc4539e6f19c0606c1502609a823327f2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60794705"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Anotações em gráficos de métricas no Application Insights

Anotações no [Explorador de métricas](../../azure-monitor/app/metrics-explorer.md) gráficos mostram onde implementou uma nova compilação ou outro evento significativo. Eles facilitam ver se as suas alterações tinham qualquer impacto no desempenho da sua aplicação. Eles podem ser criados automaticamente pelos [sistema de compilação de serviços do Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/tasks/). Também pode criar anotações para sinalizar a qualquer evento que gosta, criando-los a partir do PowerShell.

> [!NOTE]
> Este artigo reflete o preterido **experiência de métricas clássicas**. Anotações só estão atualmente disponíveis na experiência do clássica e no  **[livros](../../azure-monitor/app/usage-workbooks.md)**. Para saber mais sobre a atual experiência de métricas, consulte [este artigo](../../azure-monitor/platform/metrics-charts.md).

![Exemplo de anotações com visível correlação com o tempo de resposta do servidor](./media/annotations/00.png)

## <a name="release-annotations-with-azure-devops-services-build"></a>Anotações com serviços do Azure DevOps compilação da versão

Notas de versão são uma funcionalidade do serviço de Pipelines do Azure com base na cloud dos serviços de DevOps do Azure. 

### <a name="install-the-annotations-extension-one-time"></a>Instalar a extensão de anotações (uma vez)
Para poder criar notas de versão, terá de instalar uma das muitas extensões de serviços de DevOps do Azure disponíveis no Visual Studio Marketplace.

1. Inicie sessão no seu [serviços do Azure DevOps](https://visualstudio.microsoft.com/vso/) projeto.
2. No Visual Studio Marketplace [obter a extensão de notas de versão](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)e adicione-o à sua organização de serviços de DevOps do Azure.

![AT principais direita da página web dos serviços de DevOps do Azure, Marketplace aberto. Selecionar serviços de DevOps do Azure e, em seguida, em Pipelines do Azure, selecione ver mais.](./media/annotations/10.png)

Apenas terá de fazer isto vez para a sua organização de serviços de DevOps do Azure. Notas de versão agora podem ser configuradas para qualquer projeto na sua organização. 

### <a name="configure-release-annotations"></a>Configurar notas de versão

Tem de obter uma chave de API separada para cada modelo de versão dos serviços de DevOps do Azure.

1. Inicie sessão para o [Portal do Microsoft Azure](https://portal.azure.com) e abra o recurso do Application Insights monitoriza a aplicação. (Ou [crie uma agora](../../azure-monitor/app/app-insights-overview.md), se ainda não tiver feito isso ainda.)
2. Open **acesso à API**, **Id de informações da aplicação**.
   
    ![Em portal.azure.com, abra o recurso do Application Insights e escolha definições. Abra o acesso à API. Copie o ID da aplicação](./media/annotations/20.png)

4. Numa janela do browser separado, abra (ou crie) o modelo de liberação que gere as suas implementações de serviços de DevOps do Azure. 
   
    Adicionar uma tarefa e selecione a tarefa de anotação de versão do Application Insights a partir do menu.
   
    Colar o **Id da aplicação** que copiou a partir do painel de acesso à API.
   
    ![Nos serviços de DevOps do Azure, abra o lançamento, em seguida, selecione um pipeline de lançamento e selecione Edit. Clique em Adicionar tarefas e selecione a anotação de versão do Application Insights. Cole o ID de informações da aplicação.](./media/annotations/30.png)
4. Definir o **APIKey** campo a uma variável `$(ApiKey)`.

5. Na janela do Azure, crie uma nova chave de API e fazer uma cópia do mesmo.
   
    ![No painel de acesso à API a janela do Azure, clique em Criar chave de API. Fornecer um comentário, verifique as anotações de escrita e clique em Gerar chave. Copie a nova chave.](./media/annotations/40.png)

6. Abra o separador de configuração do modelo de liberação.
   
    Criar uma definição de variável para `ApiKey`.
   
    Cole a chave de API para a definição de variável ApiKey.
   
    ![Na janela de serviços de DevOps do Azure, selecione o separador de configuração e clique em Adicionar variável. Defina o nome para ApiKey e no valor, cole a chave que acabou de gerar e clique no ícone de bloqueio.](./media/annotations/50.png)
7. Por fim, **guardar** pipeline de versões.


## <a name="view-annotations"></a>Ver anotações
Agora, sempre que utilizar o modelo de liberação para implementar uma nova versão, uma anotação será enviada para o Application Insights. As anotações aparecerá em gráficos no Explorador de métricas.

Clique em qualquer marcador de anotação para abrir os detalhes sobre a versão, incluindo o requerente, ramo de controle de origem, versão de pipeline, o ambiente e muito mais.

![Clique em qualquer marcador de anotação de versão.](./media/annotations/60.png)

## <a name="create-custom-annotations-from-powershell"></a>Criar anotações personalizadas do PowerShell
Também pode criar anotações a partir de qualquer processo que quiser (sem utilizar serviços de DevOps do Azure). 


1. Faça uma cópia local do [script do Powershell do GitHub](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

2. Obtenha o ID da aplicação e criar uma chave de API a partir do painel de acesso à API.

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
