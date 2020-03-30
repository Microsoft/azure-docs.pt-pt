---
title: Lançar anotações para Insights de Aplicação / Microsoft Docs
description: Adicione marcadores de implantação ou construção aos seus gráficos de exploradores de métricas em Insights de Aplicação.
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: e0e2a106b276110e13b3c68889e4d1d349ba73a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666518"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Anotações em gráficos métricos em Insights de Aplicação

As anotações nos gráficos do [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) mostram onde implementou uma nova construção, ou outros eventos significativos. As anotações facilitam a ver se as suas alterações tiveram algum efeito no desempenho da sua aplicação. Podem ser automaticamente criados pelo sistema de construção de [Gasodutos Azure.](https://docs.microsoft.com/azure/devops/pipelines/tasks/) Também pode criar anotações para sinalizar qualquer evento que queira, criando-as a partir do PowerShell.

> [!NOTE]
> Este artigo reflete a experiência de **métricas clássicas**depreciadas. Anotações só estão atualmente disponíveis na experiência clássica e nos **[livros de trabalho.](../../azure-monitor/app/usage-workbooks.md)** Para saber mais sobre a experiência das métricas atuais, consulte [as características avançadas do Azure Metrics Explorer.](../../azure-monitor/platform/metrics-charts.md)

![Exemplo de anotações](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Lançar anotações com a construção de Pipelines Azure

As anotações de lançamento são uma característica do serviço azure pipelines baseado em nuvem da Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Instale a extensão de Anotações (uma vez)
Para poder criar anotações de lançamento, terá de instalar uma das muitas extensões Azure DevOps disponíveis no Visual Studio Marketplace.

1. Inscreva-se no seu projeto [Azure DevOps.](https://azure.microsoft.com/services/devops/)
   
1. Na página de extensão de [anotações](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) de lançamento do Visual Studio Marketplace, selecione a sua organização Azure DevOps e, em seguida, selecione **Instalar** para adicionar a extensão à sua organização Azure DevOps.
   
   ![Selecione uma organização Azure DevOps e, em seguida, selecione Instalar.](./media/annotations/1-install.png)
   
Só precisa de instalar a extensão uma vez para a sua organização Azure DevOps. Agora pode configurar anotações de lançamento para qualquer projeto da sua organização.

### <a name="configure-release-annotations"></a>Configure anotações de lançamento

Crie uma chave API separada para cada um dos seus modelos de lançamento de Pipelines Azure.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e abra o recurso Application Insights que monitoriza a sua aplicação. Ou se não tiver um, [crie um novo recurso Application Insights.](../../azure-monitor/app/app-insights-overview.md)
   
1. Abra o separador **API Access** e copie o ID de Insights de **Aplicação**.
   
   ![No acesso da API, copie o ID da aplicação.](./media/annotations/2-app-id.png)

1. Numa janela separada do navegador, abra ou crie o modelo de lançamento que gere as implementações dos seus Pipelines Azure.
   
1. Selecione **Adicionar tarefa**e, em seguida, selecione a tarefa de **anotação** de lançamento de informações de aplicação do menu.
   
   ![Selecione Adicionar Tarefa e selecione Anotação de Lançamento de Insights de Aplicação.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > A tarefa de Anotação de Lançamento suporta atualmente apenas agentes baseados no Windows; não funcionará em Linux, macOS ou outros tipos de agentes.
   
1. Em **ID de aplicação,** colhe o ID de Insights de Aplicação copiado do separador **API Access.**
   
   ![Colar o ID de Insights de Aplicação](./media/annotations/4-paste-app-id.png)
   
1. De volta à janela de **acesso API** Insights da aplicação, selecione **Create API Key**. 
   
   ![No separador API Access, selecione Create API Key.](./media/annotations/5-create-api-key.png)
   
1. Na janela da **chave Create API,** escreva uma descrição, selecione **Anotações de Escrever,** e, em seguida, selecione **a tecla Generate**. Copie a nova chave.
   
   ![Na janela da chave Create API, escreva uma descrição, selecione Escrever anotações e, em seguida, selecione a chave Generate.](./media/annotations/6-create-api-key.png)
   
1. Na janela do modelo de lançamento, no separador **Variáveis,** selecione **Adicionar** para criar uma definição variável para a nova tecla API.

1. Em **Nome,** `ApiKey`introduza , e em **valor**, cola a chave API que copiou do separador **API Access.**
   
   ![No separador DevOps Azure, selecione Adicionar, nomear a variável ApiKey e colar a chave API em Valor.](./media/annotations/7-paste-api-key.png)
   
1. Selecione **Guardar** na janela do modelo de desbloqueio principal para salvar o modelo.

## <a name="view-annotations"></a>Ver anotações
Agora, sempre que utilizar o modelo de lançamento para implementar um novo lançamento, uma anotação é enviada para Application Insights. As anotações aparecem em gráficos no **Metrics Explorer.**

Selecione qualquer marcador de anotação (seta cinzenta clara) para abrir detalhes sobre a libertação, incluindo o solicitador, o ramo de controlo de fonte, o gasoduto de libertação e o ambiente.

![Selecione um marcador de anotação de libertação.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Criar anotações personalizadas a partir do PowerShell
Pode utilizar o script [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) PowerShell do GitHub para criar anotações a partir de qualquer processo que goste, sem utilizar O Azure DevOps. 

1. Faça uma cópia local de [CreateReleaseAnnotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Utilize os passos no procedimento anterior para obter o id de Insights de Aplicação e crie uma chave API a partir do separador **De Acesso API** Insights da aplicação.
   
1. Ligue para o script PowerShell com o seguinte código, substituindo os espaços reservados com suportes de ângulo com os seus valores. São `-releaseProperties` opcionais. 
   
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

## <a name="next-steps"></a>Passos seguintes

* [Criar itens de trabalho](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automatização com o PowerShell](../../azure-monitor/app/powershell.md)
