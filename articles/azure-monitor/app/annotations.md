---
title: Lançar anotações para Insights de Aplicações / Microsoft Docs
description: Adicione marcadores de implementação ou construção aos gráficos de exploradores de métricas em Application Insights.
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: 58f6603687838713fafbf4cd5cc3f100e22b7401
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993725"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Anotações em gráficos métricos em Insights de Aplicação

As anotações mostram onde implantaste uma nova construção, ou outros eventos significativos. As anotações facilitam a localização das alterações no desempenho da sua aplicação. Podem ser criados automaticamente pelo sistema de construção de [Gasodutos Azure.](/azure/devops/pipelines/tasks/) Também pode criar anotações para sinalizar qualquer evento que goste, criando-as a partir do PowerShell.

## <a name="release-annotations-with-azure-pipelines-build"></a>Liberar anotações com a construção de Gasodutos Azure

As anotações de lançamento são uma característica do serviço Azure Pipelines baseado na nuvem da Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Instale a extensão de Anotações (uma vez)

Para poder criar anotações de lançamento, terá de instalar uma das muitas extensões Azure DevOps disponíveis no Visual Studio Marketplace.

1. Inscreva-se no seu projeto [Azure DevOps.](https://azure.microsoft.com/services/devops/)
   
1. Na página de extensão de [anotações](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) do Mercado do Estúdio Visual, selecione a organização Azure DevOps e, em seguida, selecione **Instalar** para adicionar a extensão à sua organização Azure DevOps.
   
   ![Selecione uma organização Azure DevOps e, em seguida, selecione Instalar.](./media/annotations/1-install.png)
   
Só precisa de instalar a extensão uma vez para a sua organização Azure DevOps. Pode agora configurar anotações de lançamento para qualquer projeto da sua organização.

### <a name="configure-release-annotations"></a>Configurar anotações de libertação

Crie uma chave API separada para cada um dos seus modelos de libertação de Azure Pipelines.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e abra o recurso Application Insights que monitoriza a sua aplicação. Ou se não tiver um, [crie um novo recurso Application Insights](./app-insights-overview.md).
   
1. Abra o separador acesso a **API** e copie o **ID de Insights de Aplicação**.
   
   ![No Acesso API, copie o ID da aplicação.](./media/annotations/2-app-id.png)

1. Numa janela separada do navegador, abra ou crie o modelo de desbloqueio que gere as implementações dos Seus Pipelines Azure.
   
1. **Selecione Adicionar a tarefa** e, em seguida, selecione a tarefa de **anotação de desbloqueio de insights** de aplicação no menu.
   
   ![Selecione Adicionar Tarefa e selecione a anotação de desbloqueio de insights de aplicação.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > A tarefa de anotação de lançamento suporta atualmente apenas agentes baseados no Windows; não será executado em Linux, macOS, ou outros tipos de agentes.
   
1. No **ID da aplicação,** cole o ID de Insights de Aplicação que copiou do separador acesso da **API.**
   
   ![Cole o ID de Insights de Aplicação](./media/annotations/4-paste-app-id.png)
   
1. De volta à janela de acesso a **API** de Informações de Aplicações, selecione **Criar chave API**. 
   
   ![No separador API Access, selecione Criar Chave API.](./media/annotations/5-create-api-key.png)
   
1. Na janela da **chave Create API,** escreva uma descrição, selecione **anotações de escrita** e, em seguida, selecione **'Gerar tecla ' Geração'** Copie a nova chave.
   
   ![Na janela da chave Create API, escreva uma descrição, selecione anotações de escrita e, em seguida, selecione Gerar tecla.](./media/annotations/6-create-api-key.png)
   
1. Na janela do modelo de libertação, no separador **Variáveis,** **selecione Adicionar** para criar uma definição variável para a nova chave API.

1. In **Name**, insira `ApiKey` , e em **Valor**, cole a chave API que copiou a partir do separador **Acesso API.**
   
   ![No separador Azure DevOps Variables, selecione Adicionar, nomeie a variável ApiKey e cole a tecla API em Valor.](./media/annotations/7-paste-api-key.png)
   
1. **Selecione Guarde** na janela do modelo de libertação principal para guardar o modelo.


   > [!NOTE]
   > Os limites para as chaves API são descritos na documentação de [limites da taxa de API](https://dev.applicationinsights.io/documentation/Authorization/Rate-limits)REST .

## <a name="view-annotations"></a>Ver anotações


   > [!NOTE]
   > As anotações de libertação não estão atualmente disponíveis no painel métrica de Insights de Aplicação

Agora, sempre que utilizar o modelo de libertação para implementar uma nova versão, é enviada uma anotação para o Application Insights. As anotações podem ser vistas nos seguintes locais:

O painel de utilização onde também tem a capacidade de criar anotações de libertação manual:

![Screenshot do gráfico de barras com número de visitas de utilizador exibidas durante um período de horas. Anotações de libertação aparecem como marcas de verificação verdes acima do gráfico indicando o momento em que ocorreu uma libertação](./media/annotations/usage-pane.png)

Em qualquer consulta de livro baseada em log-based onde a visualização exibe tempo ao longo do eixo x.

![Screenshot do painel de livros com consulta baseada em registos de séries de tempo com anotações exibidas](./media/annotations/workbooks-annotations.png)

Para ativar anotações no seu livro, vá a **Definições Avançadas** e selecione **Mostrar anotações**.

![Screenshot do menu Definições Avançadas com as palavras mostram anotações realçadas com uma marca de verificação ao lado da definição para o ativar.](./media/annotations/workbook-show-annotations.png)

Selecione qualquer marcador de anotação para abrir detalhes sobre o lançamento, incluindo o solicitador, ramo de controlo de fonte, oleoduto de libertação e ambiente.

## <a name="create-custom-annotations-from-powershell"></a>Criar anotações personalizadas a partir de PowerShell
Pode utilizar o script [CreateReleaseAnnotation](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) PowerShell do GitHub para criar anotações a partir de qualquer processo que queira, sem utilizar Azure DevOps. 

1. Faça uma cópia local de [CreateReleaseAnnotation.ps1. ](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)
   
1. Utilize os passos no procedimento anterior para obter o ID do seu Application Insights e criar uma chave API a partir do separador De **Acesso API** de Informações de Aplicação.
   
1. Ligue para o script PowerShell com o seguinte código, substituindo os espaços reservados com ângulos pelos seus valores. São `-releaseProperties` opcionais. 
   
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

* [Criar itens de trabalho](./diagnostic-search.md#create-work-item)
* [Automatização com o PowerShell](./powershell.md)

