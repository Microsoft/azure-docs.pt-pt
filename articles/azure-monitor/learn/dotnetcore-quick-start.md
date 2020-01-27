---
title: ASP.NET Core de início rápido-Azure Monitor Application Insights
description: Fornece instruções para configurar rapidamente um aplicativo Web ASP.NET Core para monitoramento com Azure Monitor Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.custom: mvc
ms.openlocfilehash: e05926d7a76f47cc3c95088fa2d8f1a6e385bbbc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75399023"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Iniciar a Monitorização de uma Aplicação Web ASP.NET Core

Com o Azure Application Insights, pode monitorizar facilmente a sua aplicação Web quanto à disponibilidade, ao desempenho e à utilização. Também pode identificar e diagnosticar erros rapidamente na sua aplicação sem ter de esperar que um utilizador os comunique. 

Este guia de início rápido orienta você na adição do SDK do Application Insights a um aplicativo Web ASP.NET Core existente. Para saber mais sobre como configurar Application Insights sem o Visual Studio, faça check-in deste [artigo](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

- [Instale o Visual Studio 2019](https://www.visualstudio.com/downloads/) com as seguintes cargas de trabalho:
  - Desenvolvimento ASP.NET e Web
  - Desenvolvimento do Azure
- [Instale o SDK .NET Core 2.0](https://www.microsoft.com/net/core)
- Irá precisar de uma subscrição do Azure e de uma aplicação Web .NET Core existente.

Se você não tiver um aplicativo Web ASP.NET Core, poderá usar nosso guia passo a passo para [criar um aplicativo ASP.NET Core e adicionar Application insights.](../../azure-monitor/app/asp-net-core.md)

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Ativar o Application Insights

O Application Insights pode recolher dados telemétricos de qualquer aplicação ligada à Internet, independentemente de estar a ser executado no local ou na cloud. Utilize os passos seguintes para começar a ver estes dados.

1. Selecione **Criar um recurso** > **Ferramentas de programador** > **Application Insights**.

   > [!NOTE]
   >Se esta for a primeira vez que você cria um recurso de Application Insights, você pode aprender mais visitando o documento [criar um Application insights recurso](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) .

    É apresentada uma caixa de configuração; utilize a tabela abaixo para preencher os campos de texto.

   | Definições        |  Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor Exclusivo Global | Nome que identifica a aplicação que está a monitorizar |
   | **Grupo de Recursos**     | myResourceGroup      | Nome do novo grupo de recursos para hospedar dados do App insights. Você pode criar um novo grupo de recursos ou usar um existente. |
   | **Localização** | E.U.A. Leste | Escolha uma localização perto de si ou perto do local onde a sua aplicação está alojada |

2. Clique em **Criar**.



## <a name="configure-app-insights-sdk"></a>Configurar o SDK do Application Insights

1. Abra o seu **projeto** da Aplicação Web ASP.NET Core no Visual Studio > Clique com o botão direito do rato em AppName no **Explorador de Soluções** > Selecione **Adicionar** > **Telemetria do Application Insights**.

    ![Adicionar Telemetria do Application Insights](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. Clique no **botão introdução**

3. Selecione sua conta e assinatura > selecione o **recurso existente** que você criou no portal do Azure > clique em **registrar**.

4. Selecione **projeto** > **gerenciar pacotes NuGet** > **origem do pacote: NuGet.org** > **Atualizar** os pacotes do SDK do Application insights para a versão estável mais recente.

5. Selecione **Depurar** > **Iniciar sem Depuração** (Ctrl + F5) para Iniciar a sua aplicação

    ![Menu de Descrição Geral do Application Insights](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> Os dados demoram entre três e cinco minutos a aparecer no portal. Se esta aplicação for uma aplicação de teste de tráfego reduzido, tenha em atenção que a maioria das métricas só é capturada quando existem operações ou pedidos ativos.

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar a monitorização no portal do Azure

1. Reabra a página de **visão geral** do Application Insights na portal do Azure selecionando **página inicial** e, em recursos recentes, selecione o recurso que você criou anteriormente para exibir detalhes sobre o aplicativo em execução no momento.

   ![Menu de Descrição Geral do Application Insights](./media/dotnetcore-quick-start/4overview.png)

2. Clique em **Mapa da Aplicação** para obter um esquema visual das relações de dependência entre os componentes da aplicação. Cada componente mostra KPIs, tais como carga, desempenho, falhas e alertas.

   ![Mapeamento de Aplicações](./media/dotnetcore-quick-start/5appmap.png)

3. Clique no ícone de **análise** de aplicativo ![ícone de mapa **de aplicativo](./media/dotnetcore-quick-start/006.png) exibir no Analytics**. Esta ação abre o **Application Insights Analytics**, que fornece uma linguagem de consulta avançada para analisar todos os dados recolhidos pelo Application Insights. Neste caso, é gerada uma consulta que compõe a contagem de pedidos como um gráfico. Pode escrever as suas próprias consultas para analisar outros dados.

   ![Gráfico de análise de pedidos de utilizador durante um período de tempo](./media/dotnetcore-quick-start/6analytics.png)

4. Volte para a página **visão geral** e examine os painéis de KPI.  Este dashboard fornece estatísticas sobre o estado de funcionamento da aplicação, incluindo o número de pedidos recebidos, a duração desses pedidos e quaisquer falhas que ocorram. 

   ![Gráficos de linha cronológica de Descrição Geral do Estado de Funcionamento](./media/dotnetcore-quick-start/7kpidashboards.png)

5. No lado esquerdo, clique em **métricas**. Use o Metrics Explorer para investigar a integridade e a utilização do recurso. Pode clicar em **Adicionar novo gráfico** criar vistas personalizadas adicionais ou selecionar **Editar** para modificar os tipos de gráficos existentes, a altura, a paleta de cores, os agrupamentos e as métricas. Por exemplo, você pode criar um gráfico que exibe o tempo médio de carregamento da página do navegador selecionando "tempo de carregamento de página do navegador" no menu suspenso métricas e "Méd" da agregação. Para saber mais sobre o Azure Metrics Explorer visite [a introdução ao azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md).

     ![Guia métricas: gráfico de tempo médio de carregamento da página do navegador](./media/dotnetcore-quick-start/8metrics.png)

## <a name="video"></a>Vídeo

- Vídeo passo a passo externo sobre como [configurar Application insights com o .NET Core e o Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) do zero.
- Vídeo passo a passo externo sobre como [configurar Application insights com o .NET Core e Visual Studio Code](https://youtu.be/ygGt84GDync) do zero.

## <a name="clean-up-resources"></a>Limpar recursos
Quando terminar o teste, você poderá excluir o grupo de recursos e todos os recursos relacionados. Para fazer isso, siga as etapas abaixo.

> [!NOTE]
> Se você usou um grupo de recursos existente, as instruções abaixo não funcionarão e você precisará apenas excluir o recurso de Application Insights individual. Tenha em mente sempre que você excluir um grupo de recursos, todos os recursos do underyling que são membros desse grupo serão excluídos.

1. No menu do lado esquerdo no portal do Azure, clique em **Grupos de recursos** e, em seguida, clique em **myResourceGroup**.
2. Na página do grupo de recursos, clique em **Eliminar**, escreva **myResourceGroup** na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Encontrar e diagnosticar exceções de runtime](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)
