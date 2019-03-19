---
title: Monitorizar o desempenho de serviços de aplicações do Azure | Documentos da Microsoft
description: Desempenho de aplicações, monitorização dos serviços de aplicações do Azure. Crie gráficos de tempos de carregamento e resposta, informações de dependências e defina alertas relativos ao desempenho.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: application-insights
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: mbullwin
ms.openlocfilehash: 92a7c1a45655f8804aa1f81b1a77ebf7cd5197e8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122170"
---
# <a name="monitor-azure-app-service-performance"></a>Monitorizar o desempenho do serviço de aplicações do Azure
Na [portal do Azure](https://portal.azure.com) pode configurar a monitorização de desempenho de aplicações para as suas aplicações web, móveis back-ends e aplicações API no [App Service do Azure](../../app-service/overview.md). O [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) instrui a sua aplicação a enviar telemetria sobre as atividades para o serviço Application Insights, onde são armazenadas e analisadas. Aí, podem ser utilizados gráficos de métricas e ferramentas de pesquisa para ajudar a diagnosticar problemas, melhorar o desempenho e avaliar a utilização.

## <a name="runtime-or-build-time"></a>Hora de tempo de execução ou compilação
Para configurar a monitorização, pode instrumentar a aplicação de uma de duas formas:

* **Tempo de execução** -pode selecionar uma extensão de monitorização, quando o serviço de aplicações já está em direto. Não é necessário recompilar ou reinstalar a aplicação. Obtém um conjunto padrão de pacotes que monitorizam os tempos de resposta, as taxas de êxito, as exceções, as dependências e assim sucessivamente.

* **Tempo de compilação** - pode instalar um pacote na sua aplicação durante a programação. Esta opção é mais versátil. Para além dos mesmos pacotes padrão, pode escrever código para personalizar a telemetria ou enviar a sua própria telemetria. Pode registar atividades específicas ou eventos, de acordo com a semântica do domínio da aplicação. Isso também dá-lhe a capacidade de testar a versão mais recente do SDK do Application Insights, como pode optar por avaliar o beta SDKs, ao passo que a monitorização de tempo de execução está restringida para a versão estável mais recente.

## <a name="runtime-instrumentation-with-application-insights"></a>Instrumentação de tempo de execução com o Application Insights
Se estiver atualmente a executar um serviço de aplicações no Azure, já tem alguma monitorização: taxas de pedidos e de erros por predefinição. Adicione o Application Insights para obter mais, como tempos de resposta, monitorização de chamadas para dependências, deteção inteligente e de acesso para a poderosa linguagem de consulta de Kusto. 

1. **Selecione o Application Insights** no painel de controlo do Azure para o serviço de aplicações.

    ![Em definições, escolha Application Insights](./media/azure-web-apps/settings-app-insights.png)

   * Opte por criar um novo recurso, a menos que já configurou a um recurso do Application Insights para esta aplicação. 

     > [!NOTE]
     > Quando clica em **OK** para criar o novo recurso, será solicitado a **aplicar definições de monitorização**. Selecionando **continuar** irá ligar o seu novo recurso do Application Insights ao seu serviço de aplicações, fazendo assim, será também **acionar um reinício do serviço de aplicações**. 

     ![Instrumente a sua aplicação Web](./media/azure-web-apps/create-resource.png)

2. Depois de especificar o recurso a utilizar, pode escolher como pretende que o application insights para recolher dados por plataforma para a sua aplicação. Monitorização de aplicações do ASP.NET é por padrão com dois níveis diferentes de coleção.

    ![Escolher opções por plataforma](./media/azure-web-apps/choose-options-new.png)

   * .NET **conjunto básico** nível oferece funcionalidades essenciais de APM de instância única.
    
   * .NET **recomendado coleção** nível:
       * Adiciona as tendências de utilização de CPU, memória e e/s.
       * Correlaciona microsserviços em limites de pedido/dependência.
       * Recolhe as tendências de utilização e permite a correlação de resultados de disponibilidade para transações.
       * Recolhe as exceções não processadas pelo processo de host.
       * Melhora a precisão de métricas APM sob carga, quando a amostragem é usada.
    
     .NET core oferece **recomendado coleção** ou **desativado** para .NET Core 2.0 e 2.1.

3. **Instrumentar o seu serviço de aplicações** após instalação do Application Insights.

   **Ativar a monitorização do lado do cliente** para a telemetria de utilizador e vista de página.

    (Isto é ativado por predefinição para aplicações de .NET Core com **recomendado coleção**, independentemente de se a 'APPINSIGHTS_JAVASCRIPT_ENABLED' de definição de aplicação está presente. Suporte baseado em granular da interface do Usuário para a desativação da monitorização do lado do cliente não está atualmente disponível para .NET Core.)
    
   * Selecione Definições > Definições da Aplicação
   * Em Definições da Aplicação, adicione um par de chaves-valores novo:

     Chave: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valor:`true`
   * **Guarde** as definições e **reinicie** a aplicação.

4. Explorar dados de monitorização da sua aplicação, selecionando **configurações** > **Application Insights** > **ver mais no Application Insights**.

Mais tarde, pode criar a aplicação com o Application Insights, se pretender.

*Como posso remover o Application Insights ou mudar para enviar para outro recurso?*

* No Azure, abra o painel de controlo de aplicação web e, em definições, abra **Application Insights**. Pode desativar o Application Insights clicando **desativar** na parte superior, ou selecione um novo recurso no **alterar seu recurso** secção.

## <a name="build-the-app-with-application-insights"></a>Compilar a aplicação com o Application Insights
O Application Insights pode proporcionar telemetria mais detalhada, mediante a instalação de um SDK na sua aplicação. Em particular, pode recolher registos de rastreio, [escrever telemetria personalizada](../../azure-monitor/app/api-custom-events-metrics.md)e obter relatórios de exceções mais detalhados.

1. **No Visual Studio** (2013 update 2 ou posterior), configure o Application Insights para o seu projeto.

    Com o botão direito no projeto web e selecione **adicionar > Application Insights** ou **projeto** > **Application Insights**  >  **Configurar o Application Insights**.

    ![Clique com o botão direito do rato no projeto e escolha Adicionar ou Configurar o Application Insights](./media/azure-web-apps/03-add.png)

    Se lhe for pedido para iniciar sessão, utilize as credenciais da sua conta do Azure.

    A operação tem dois efeitos:

   1. Cria um recurso do Application Insights no Azure, onde telemetria é armazenada, analisada e apresentada.
   2. Adiciona o pacote NuGet do Application Insights ao seu código (se ainda não estiver lá) e configura-o para enviar a telemetria para o recurso do Azure.
2. Execute a aplicação no seu computador de programação (F5) para **testar a telemetria**.
3. **Publique a aplicação** no Azure, da forma habitual. 

*Como posso mudar para enviar para outro recurso do Application Insights?*

* No Visual Studio, clique com botão direito no projeto, escolha **configurar o Application Insights**e escolha o recurso que pretende. Terá a opção de criar um recurso novo. Recompile e reimplemente.

## <a name="automate-monitoring"></a>Automatizar a monitorização

Para ativar a recolha de telemetria com o Application Insights apenas as definições de aplicação tem de ser definido:

   ![Definições de aplicação do serviço de aplicações com as definições disponíveis do Application Insights](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definições de definições de aplicação

|Nome da definição de aplicação |  Definição | Value |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Extensão principal, que controla a monitorização do tempo de execução. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Em default funcionalidades em modo apenas, essencial estão ativadas e assim garantir um desempenho ideal. | `default` ou `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Controla se o motor de reescrita de binário `InstrumentationEngine` será ativado. Esta definição tem implicações de desempenho e tem impacto sobre a hora de início/inicialização a frio. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Controles se o texto de tabela SQL e no Azure serão capturados, juntamente com as chamadas de dependência. Aviso de desempenho: Isto requer a `InstrumentationEngine`. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Definições de aplicação de serviço de aplicações com o Azure Resource Manager

Podem ser geridas e configuradas com as definições da aplicação para os serviços aplicacionais [modelos Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Este método pode ser utilizado durante a implantação de novos recursos do serviço de aplicações com a automatização do Azure Resource Manager ou para modificar as definições dos recursos existentes.

A estrutura básica das definições de aplicação JSON para um serviço de aplicações está ilustrado abaixo:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]

```

Para obter um exemplo de um Gestor de recursos do Azure modelo com definições de aplicação configurado para o Application Insights isso [modelo](https://github.com/Andrew-MSFT/BasicImageGallery) pode ser útil, especificamente a partir da secção [linha 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatize a criação de um recurso do Application Insights e uma ligação para o serviço de aplicações criada recentemente.

Para criar um modelo Azure Resource Manager com todas as configurações de Application Insights padrão configuradas, iniciar o processo como se fosse criar uma nova aplicação Web com o Application Insights ativado.

Selecione **opções de automatização**

   ![Menu de criação de aplicações do serviço de aplicações web](./media/azure-web-apps/create-web-app.png)

Isso gera o modelo mais recente do Azure Resource Manager com todas as definições necessárias configuradas.

  ![Modelo de aplicação do serviço de aplicações web](./media/azure-web-apps/arm-template.png)

> [!NOTE]
> O modelo irá gerar definições da aplicação no modo "predefinido". Esse modo está desempenho otimizado, embora pode modificar o modelo para ativar qualquer que seja funcionalidades que preferir.

## <a name="more-telemetry"></a>Mais telemetria

* [Web page load data](../../azure-monitor/app/javascript.md) (Dados de carregamento de páginas Web)
* [Custom telemetry](../../azure-monitor/app/api-custom-events-metrics.md) (Telemetria personalizada)

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="do-i-still-need-to-go-to-extensions---add---application-insights-extension-for-new-app-service-apps"></a>Eu ainda preciso ir para extensões – Adicionar - extensão do Application Insights para novas aplicações de serviço de aplicações?

Não, não precisa de adicionar a extensão manualmente. Ativar o Application Insights através do painel Definições, irá adicionar todas as definições de aplicação necessárias para ativar a monitorização. Isso é agora possível, porque os ficheiros que foi adicionados anteriormente pela extensão do estão agora [pré-instalado](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) como parte da imagem do serviço de aplicações. Os ficheiros estão localizados no `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent`.

### <a name="if-runtime-and-build-time-monitoring-are-both-enabled-do-i-end-up-with-duplicate-data"></a>Se o tempo de execução e monitoramento de tempo de compilação estiverem ativadas, terminou com dados duplicados?

Não, por predefinição se for detetada a monitorização de tempo de compilação via a extensão de monitorização de tempo de execução irá parar o envio de dados e apenas o tempo de compilação configuração de monitorização será cumprido. O processo de determinar se pretende desativar a monitorização do tempo de execução baseia-se a deteção de qualquer um desses três arquivos:

* Microsoft.ApplicationInsights dll
* Dll de Microsoft.ASP.NET.TelemetryCorrelation
* Dll de System.Diagnostics.DiagnosticSource

É importante ter em mente que muitas versões do Visual Studio, alguns ou todos estes ficheiros são adicionados por padrão para os arquivos de modelo do ASP.NET e ASP.NET Core Visual Studio. Se o projeto foi criado com base em um dos modelos, mesmo se explicitamente ainda não ativou o Application Insights, a presença da dependência do arquivo impediria a monitorização de tempo de execução a partir de a ativar.

### <a name="appinsightsjavascriptenabled-causes-incomplete-html-response-in-net-core-web-applications"></a>APPINSIGHTS_JAVASCRIPT_ENABLED faz com que a resposta HTML incompleta em aplicativos de web do NET CORE.

Ativar Javascript por meio de serviços de aplicação pode fazer com que as respostas de html para serão cortadas.

* Solução 1: definir a definição de aplicação APPINSIGHTS_JAVASCRIPT_ENABLED como false ou removê-la completamente e reiniciar
* Solução 2: adicionar o sdk por meio do código e remover a extensão (depurador Profiler e o instantâneo não funcionarão com esta configuração)

Para acompanhar este problema, aceda a [extensão do Azure, fazendo com que a resposta HTML incompleta](https://github.com/Microsoft/ApplicationInsights-Home/issues/277).

Para .NET Core seguem-se atualmente **nepodporuje**:

* Implementação independente.
* Aplicações visando o .NET Framework.
* Aplicações de .NET core 2.2.

> [!NOTE]
> .NET core 2.0 e 2.1 do .NET Core são suportadas. Quando é adicionado suporte de .NET Core 2.2 neste artigo será atualizado.

## <a name="next-steps"></a>Passos Seguintes
* [Run the profiler on your live app](../../azure-monitor/app/profiler.md) (Executar o gerador de perfis na sua aplicação publicada).
* [Funções do Azure](https://github.com/christopheranderson/azure-functions-app-insights-sample) - monitorize Funções do Azure com o Application Insights
* [Ativar os diagnósticos do Azure](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md) para serem enviados para o Application Insights.
* [Monitorizar as métricas de estado de funcionamento de serviço](../../azure-monitor/platform/data-collection.md) para se certificar de que o serviço está disponível e a responder.
* [Receber notificações de alertas](../../azure-monitor/platform/alerts-overview.md) sempre que ocorrem eventos operacionais ou quando as métricas ultrapassam um determinado limiar.
* Utilizar o [Application Insights para aplicações JavaScript e páginas Web](../../azure-monitor/app/javascript.md) para obter telemetria de clientes a partir dos browsers que visitam as páginas Web.
* [Configurar os Testes Web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) para ser alertado se o seu site estiver em baixo.