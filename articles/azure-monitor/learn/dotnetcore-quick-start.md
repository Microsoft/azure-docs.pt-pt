---
title: Quickstart ASP.NET Core - Azure Monitor Application Insights
description: Fornece instruções para configurar rapidamente uma aplicação web ASP.NET Core para monitorização com insights de aplicação do Monitor Azure
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.custom: mvc
ms.openlocfilehash: ef46b86186d1f5e26360de891b3a090ab0ece66b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78894820"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Iniciar a Monitorização de uma Aplicação Web ASP.NET Core

Com o Azure Application Insights, pode monitorizar facilmente a sua aplicação Web quanto à disponibilidade, ao desempenho e à utilização. Também pode identificar e diagnosticar erros rapidamente na sua aplicação sem ter de esperar que um utilizador os comunique. 

Este quickstart guia-o através da adição do SDK de Insights de Aplicação a uma aplicação web ASP.NET existente. Para aprender sobre configurar Insights de Aplicação sem Estúdio Visual check-out este [artigo](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

- [Instale o Estúdio Visual 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com as seguintes cargas de trabalho:
  - Desenvolvimento ASP.NET e Web
  - Desenvolvimento do Azure
- [Instale o SDK .NET Core 2.0](https://dotnet.microsoft.com/download)
- Irá precisar de uma subscrição do Azure e de uma aplicação Web .NET Core existente.

Se não tiver uma aplicação web ASP.NET Core, pode usar o nosso guia passo a passo para criar uma aplicação ASP.NET Core e adicionar Insights de [Aplicação.](../../azure-monitor/app/asp-net-core.md)

Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Ativar o Application Insights

O Application Insights pode recolher dados telemétricos de qualquer aplicação ligada à Internet, independentemente de estar a ser executado no local ou na cloud. Utilize os passos seguintes para começar a ver estes dados.

1. Selecione **Criar um programador de recursos** > **Ferramentas** > **De aplicação Insights**.

   > [!NOTE]
   >Se for a primeira vez que cria um recurso de Insights de Aplicação, pode aprender mais visitando o [Create a Application Insights Resource](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) doc.

    É apresentada uma caixa de configuração; utilize a tabela abaixo para preencher os campos de texto.

   | Definições        |  Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor Exclusivo Global | Nome que identifica a aplicação que está a monitorizar |
   | **Grupo de Recursos**     | myResourceGroup      | Nome para o novo grupo de recursos para hospedar dados da App Insights. Pode criar um novo grupo de recursos ou utilizar um existente. |
   | **Localização** | E.U.A. Leste | Escolha uma localização perto de si ou perto do local onde a sua aplicação está alojada |

2. Clique em **Criar**.



## <a name="configure-app-insights-sdk"></a>Configurar o SDK do Application Insights

1. Abra o seu **projeto** da Aplicação Web ASP.NET Core no Visual Studio > Clique com o botão direito do rato em AppName no **Explorador de Soluções** > Selecione **Adicionar** > **Telemetria do Application Insights**.

    ![Adicionar Telemetria do Application Insights](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. Clique no botão **Iniciar**

3. Selecione a sua conta e > de subscrição Selecione o **recurso existente** que criou no portal Azure > Click **Register**.

4. Selecione **Project** > **Manage NuGet Packages** > **Package source: nuget.org** > **Update** the Application Insights SDK packages to the latest stable release.

5. Selecione **Debug** > **Start sem Debugging** (Ctrl+F5) para lançar a sua aplicação

    ![Menu de Descrição Geral do Application Insights](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> Os dados demoram entre três e cinco minutos a aparecer no portal. Se esta aplicação for uma aplicação de teste de tráfego reduzido, tenha em atenção que a maioria das métricas só é capturada quando existem operações ou pedidos ativos.

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar a monitorização no portal do Azure

1. Reabra a página de **visão geral** da Aplicação Insights no portal Azure selecionando **Home** e sob recursos recentes selecione o recurso que criou anteriormente, para ver detalhes sobre a sua aplicação atualmente em execução.

   ![Menu de Descrição Geral do Application Insights](./media/dotnetcore-quick-start/4overview.png)

2. Clique em **Mapa da aplicação** para obter um esquema visual das relações de dependência entre os componentes da aplicação. Cada componente mostra KPIs, tais como carga, desempenho, falhas e alertas.

   ![Mapeamento de Aplicações](./media/dotnetcore-quick-start/5appmap.png)

3. Clique no](./media/dotnetcore-quick-start/006.png) ícone ![do ícone app **Analytics** Ícone **Ícone Visualização no Analytics**. Esta ação abre o **Application Insights Analytics**, que fornece uma linguagem de consulta avançada para analisar todos os dados recolhidos pelo Application Insights. Neste caso, é gerada uma consulta que compõe a contagem de pedidos como um gráfico. Pode escrever as suas próprias consultas para analisar outros dados.

   ![Gráfico de análise de pedidos de utilizador durante um período de tempo](./media/dotnetcore-quick-start/6analytics.png)

4. Volte à página **de visão geral** e examine os Dashboards KPI.  Este dashboard fornece estatísticas sobre o estado de funcionamento da aplicação, incluindo o número de pedidos recebidos, a duração desses pedidos e quaisquer falhas que ocorram. 

   ![Gráficos de linha cronológica de Descrição Geral do Estado de Funcionamento](./media/dotnetcore-quick-start/7kpidashboards.png)

5. No clique esquerdo em **Métricas**. Utilize o explorador de métricas para investigar a saúde e utilização do seu recurso. Pode clicar em **Adicionar novo gráfico** criar vistas personalizadas adicionais ou selecionar **Editar** para modificar os tipos de gráficos existentes, a altura, a paleta de cores, os agrupamentos e as métricas. Por exemplo, pode fazer um gráfico que exibe o tempo médio de carga da página do navegador, escolhendo "Tempo de carga da página do navegador" a partir das métricas que descem e "Avg" da agregação. Para saber mais sobre a visita do Azure Metrics Explorer [Começar com o Azure Metrics Explorer.](../../azure-monitor/platform/metrics-getting-started.md)

     ![Separador de métricas: Gráfico médio de tempo de carregamento da página do navegador](./media/dotnetcore-quick-start/8metrics.png)

## <a name="video"></a>Vídeo

- Vídeo externo passo a passo sobre configurar Insights de [Aplicação com .NET Core e Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) do zero.
- Vídeo externo passo a passo sobre configurar Insights de [aplicação com .NET Core e Visual Studio Code](https://youtu.be/ygGt84GDync) do zero.

## <a name="clean-up-resources"></a>Limpar recursos
Quando terminar os testes, pode eliminar o grupo de recursos e todos os recursos relacionados. Para isso siga os passos abaixo.

> [!NOTE]
> Se utilizou um grupo de recursos existente, as instruções abaixo não funcionarão e terá de eliminar o recurso Individual Application Insights. Tenha em mente que sempre que eliminar um grupo de recursos, todos os recursos que são membros desse grupo serão eliminados.

1. No menu do lado esquerdo no portal do Azure, clique em **Grupos de recursos** e, em seguida, clique em **myResourceGroup**.
2. Na página do grupo de recursos, clique em **Eliminar**, escreva **myResourceGroup** na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Encontrar e diagnosticar exceções de runtime](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)
