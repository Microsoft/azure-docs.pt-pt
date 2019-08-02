---
title: Personalizar a interface do usuário da solução de monitoramento remoto – Azure | Microsoft Docs
description: Este artigo fornece informações sobre como você pode acessar o código-fonte para a interface do usuário do acelerador de solução de monitoramento remoto e fazer algumas personalizações.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.openlocfilehash: eb3d5fea68b5b1b6e648943cb3dbaab5857e9e07
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608001"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Personalizar o acelerador de solução de monitoramento remoto

Este artigo fornece informações sobre como você pode acessar o código-fonte e personalizar a interface do usuário do acelerador de solução de monitoramento remoto.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Preparar um ambiente de desenvolvimento local para a interface do usuário

O código da interface do usuário do acelerador de solução de monitoramento remoto é implementado usando a estrutura reajam. js. Você pode encontrar o código-fonte no repositório GitHub [Azure-IOT-PCs-Remote-Monitoring-WebUI](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) .

Para fazer alterações na interface do usuário, você pode executar uma cópia dela localmente. Para concluir ações como recuperar a telemetria, a cópia local se conecta a uma instância implantada da solução.

As etapas a seguir descrevem o processo para configurar um ambiente local para o desenvolvimento da interface do usuário:

1. Implante uma instância **básica** do Solution Accelerator usando a CLI do **PCs** . Anote o nome da sua implantação e as credenciais fornecidas para a máquina virtual. Para obter mais informações, consulte [implantar usando a CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Para habilitar o acesso SSH à máquina virtual que hospeda os microserviços em sua solução, use o portal do Azure ou o Azure Cloud Shell. Por exemplo:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Habilite somente o acesso SSH durante o teste e o desenvolvimento. Se você habilitar o SSH, [desabilite-o assim que terminar de usá-lo](../security/fundamentals/network-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Use o portal do Azure ou o Azure Cloud Shell para localizar o nome e o endereço IP público de sua máquina virtual. Por exemplo:

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Use o SSH para se conectar à sua máquina virtual. Use o endereço IP da etapa anterior e as credenciais fornecidas quando você executou os **computadores** para implantar a solução. O `ssh` comando está disponível no Azure cloud Shell.

1. Para permitir que o UX local se conecte, execute os seguintes comandos no shell bash na máquina virtual:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Depois de ver o comando ser concluído e o site for iniciado, você poderá desconectar-se da máquina virtual.

1. Na cópia local do repositório [Azure-IOT-PCs-Remote-Monitoring-WebUI](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) , edite o arquivo **. env** para adicionar a URL da solução implantada:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. Em um prompt de comando, navegue até a cópia local da `azure-iot-pcs-remote-monitoring-webui` pasta.

1. Para instalar as bibliotecas necessárias e executar a interface do usuário localmente, execute os seguintes comandos:

    ```cmd/sh
    npm install
    npm start
    ```

1. O comando anterior executa a interface do usuário localmente em\/http:/localhost: 3000/Dashboard. Você pode editar o código enquanto o site está em execução e vê-lo atualizado dinamicamente.

## <a name="customize-the-layout"></a>Personalizar o layout

Cada página na solução de monitoramento remoto é composta de um conjunto de controles, chamados de *painéis* no código-fonte. A página do **painel** é composta de cinco painéis: Visão geral, mapa, alertas, telemetria e análise. Você pode encontrar o código-fonte que define cada página e seus painéis no repositório do GitHub [PCs-Remote-Monitoring-WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) . Por exemplo, o código que define a página do **painel** , seu layout e os painéis na página está localizado na pasta [src/Components/Pages/Dashboard](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) .

Como os painéis gerenciam seu próprio layout e dimensionamento, você pode facilmente modificar o layout de uma página. Faça as seguintes alterações no elemento **PageContent** no `src/components/pages/dashboard/dashboard.js` arquivo para:

* Troque as posições dos painéis mapa e telemetria.
* Altere as larguras relativas dos painéis mapa e análise.

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-6">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

![Alterar layout do painel](./media/iot-accelerators-remote-monitoring-customize/layout.png)

Você também pode adicionar várias instâncias do mesmo painel, ou várias versões, se você [duplicar e personalizar um painel](#duplicate-and-customize-an-existing-control). O exemplo a seguir mostra como adicionar duas instâncias do painel telemetria. Para fazer essas alterações, edite `src/components/pages/dashboard/dashboard.js` o arquivo:

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

Em seguida, você pode exibir a telemetria diferente em cada painel:

![Vários painéis de telemetria](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Duplicar e personalizar um controle existente

As etapas a seguir descrevem como duplicar um painel existente, modificá-lo e, em seguida, usar a versão modificada. As etapas usam o painel **alertas** como um exemplo:

1. Na cópia local do repositório, faça uma cópia da pasta **alertas** na `src/components/pages/dashboard/panels` pasta. Nomeie a nova cópia **cust_alerts**.

1. No arquivo **alertsPanel. js** na pasta **cust_alerts** , edite o nome da classe a ser **CustAlertsPanel**:

    ```javascript
    export class CustAlertsPanel extends Component {
    ```

1. Adicione a seguinte linha ao `src/components/pages/dashboard/panels/index.js` arquivo:

    ```javascript
    export * from './cust_alerts';
    ```

1. Substituir `alertsPanel` por `CustAlertsPanel` no`src/components/pages/dashboard/dashboard.js` arquivo:

    ```javascript
    import {
      OverviewPanel,
      CustAlertsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlertsPanel
        alerts={currentActivealertsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

Agora, você substituiu o painel **alertas** originais por uma cópia chamada **CustAlerts**. Essa cópia é igual à original. Agora você pode modificar a cópia. Por exemplo, para alterar a ordenação de coluna no painel **alertas** :

1. Abra o ficheiro `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js`.

1. Modifique as definições de coluna conforme mostrado no seguinte trecho de código:

    ```javascript
    this.columnDefs = [
      rulesColumnDefs.severity,
      {
        headerName: 'rules.grid.count',
        field: 'count'
      },
      {
        ...rulesColumnDefs.ruleName,
        minWidth: 200
      },
      rulesColumnDefs.explore
    ];
    ```

A captura de tela a seguir mostra a nova versão do painel **alertas** :

![painel de alertas atualizado](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Personalizar o gráfico de telemetria

Os arquivos na `src/components/pages/dashboard/panels/telemtry` pasta definem o gráfico de telemetria na página **painel** . A interface do usuário recupera a telemetria do back-end `src/services/telemetryService.js` da solução no arquivo. As etapas a seguir mostram como alterar o período de tempo exibido no gráfico de telemetria de 15 para 5 minutos:

1. No arquivo, localize a função chamada **getTelemetryByDeviceIdP15M.** `src/services/telemetryService.js` Faça uma cópia dessa função e modifique a cópia da seguinte maneira:

    ```javascript
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. Para usar essa nova função para popular o gráfico de telemetria, `src/components/pages/dashboard/dashboard.js` Abra o arquivo. Localize a linha que inicializa o fluxo de telemetria e modifique-a da seguinte maneira:

    ```javascript
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

O gráfico de telemetria agora mostra os cinco minutos de dados de telemetria:

![Gráfico de telemetria mostrando um dia](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Adicionar um novo KPI

A página **painel** exibe KPIs no painel **análise** . Esses KPIs são calculados no `src/components/pages/dashboard/dashboard.js` arquivo. Os KPIs são renderizados pelo `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` arquivo. As etapas a seguir descrevem como calcular e renderizar um novo valor de KPI na página **painel** . O exemplo mostrado é adicionar uma nova alteração percentual no KPI alertas de aviso:

1. Abra o ficheiro `src/components/pages/dashboard/dashboard.js`. Modifique o objeto inicialstate para incluir uma propriedade **warningAlertsChange** da seguinte maneira:

    ```javascript
    const initialState = {
      ...

      // Analytics data
      analyticsVersion: 0,
      currentActiveAlerts: [],
      topAlerts: [],
      alertsPerDeviceId: {},
      criticalAlertsChange: 0,
      warningAlertsChange: 0,
      analyticsIsPending: true,
      analyticsError: null

      ...
    };
    ```

1. Modifique o objeto **currentAlertsStats** para incluir **totalWarningCount** como uma propriedade:

    ```javascript
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alertsPerDeviceId: updatedAlertsPerDeviceId
    };
    ```

1. Calcule o novo KPI. Localize o cálculo para a contagem de alertas críticos. Duplique o código e modifique a cópia da seguinte maneira:

    ```javascript
    // ================== Warning Alerts Count - START
    const currentWarningAlerts = currentAlertsStats.totalWarningCount;
    const previousWarningAlerts = previousAlerts.reduce(
      (cnt, { severity }) => severity === Config.ruleSeverity.warning ? cnt + 1 : cnt,
      0
    );
    const warningAlertsChange = ((currentWarningAlerts - previousWarningAlerts) / currentWarningAlerts * 100).toFixed(2);
    // ================== Warning Alerts Count - END
    ```

1. Inclua o novo KPI **warningAlertsChange** no fluxo do KPI:

    ```javascript
    return ({
      analyticsIsPending: false,
      analyticsVersion: this.state.analyticsVersion + 1,

      // Analytics data
      currentActiveAlerts,
      topAlerts,
      criticalAlertsChange,
      warningAlertsChange,
      alertsPerDeviceId: currentAlertsStats.alertsPerDeviceId,

      ...
    });
    ```

1. Inclua o novo KPI **warningAlertsChange** nos dados de estado usados para renderizar a interface do usuário:

    ```javascript
    const {
      ...

      analyticsVersion,
      currentActiveAlerts,
      topAlerts,
      alertsPerDeviceId,
      criticalAlertsChange,
      warningAlertsChange,
      analyticsIsPending,
      analyticsError,

      ...
    } = this.state;
    ```

1. Atualize os dados passados para o painel KPIs:

    ```javascript
    <AnalyticsPanel
      timeSeriesExplorerUrl={timeSeriesParamUrl}
      topAlerts={topAlertsWithName}
      alertsPerDeviceId={alertsPerDeviceType}
      criticalAlertsChange={criticalAlertsChange}
      warningAlertsChange={warningAlertsChange}
      isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || analyticsError}
      theme={theme}
      colors={chartColorObjects}
      t={t} />
    ```

Agora você concluiu as alterações no `src/components/pages/dashboard/dashboard.js` arquivo. As etapas a seguir descrevem as alterações a serem feitas `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` no arquivo para exibir o novo KPI:

1. Modifique a seguinte linha de código para recuperar o novo valor de KPI da seguinte maneira:

    ```javascript
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Modifique a marcação para exibir o novo valor de KPI da seguinte maneira:

    ```javascript
    <div className="analytics-cell">
      <div className="analytics-header">{t('dashboard.panels.analytics.criticalAlerts')}</div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(criticalAlertsChange) ? criticalAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(warningAlertsChange) ? warningAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

A página **painel** agora exibe o novo valor de KPI:

![KPI de aviso](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Personalizar o mapa

Consulte a página [Personalizar mapa](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) no GitHub para obter detalhes dos componentes do mapa na solução.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Outras opções de personalização

Para modificar ainda mais a camada de apresentação e visualizações na solução de monitoramento remoto, você pode editar o código. Os repositórios relevantes do GitHub são:

* [O microserviço de configuração para soluções de IoT do Azure (.NET)](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [O microserviço de configuração para soluções de IoT do Azure (Java)](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [Interface do usuário da Web de monitoramento remoto de PCS IoT do Azure](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre os recursos disponíveis para ajudá-lo a personalizar a interface do usuário da Web no acelerador de solução de monitoramento remoto. Para saber mais sobre como personalizar a interface do usuário, confira os seguintes artigos:

* [Adicionar uma página personalizada à interface do usuário Web do acelerador de solução de monitoramento remoto](iot-accelerators-remote-monitoring-customize-page.md)
* [Adicionar um serviço personalizado à interface do usuário Web do acelerador de solução de monitoramento remoto](iot-accelerators-remote-monitoring-customize-service.md)
* [Adicionar uma grade Personalizada à interface do usuário Web do acelerador de solução de monitoramento remoto](iot-accelerators-remote-monitoring-customize-grid.md)
* [Adicionar um submenu personalizado à interface do usuário Web do acelerador de solução de monitoramento remoto](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Adicionar um painel personalizado ao painel na interface do usuário da Web de monitoramento remoto do Solution Accelerator](iot-accelerators-remote-monitoring-customize-panel.md)

Para obter mais informações conceituais sobre o acelerador de solução de monitoramento remoto, consulte [arquitetura de monitoramento remoto](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Para obter mais informações sobre como personalizar os microserviços da solução de monitoramento remoto, consulte [Personalizar e reimplantar um microserviço](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->
