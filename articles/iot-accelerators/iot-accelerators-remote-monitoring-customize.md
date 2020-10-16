---
title: Personalizar a solução de Monitorização Remota UI - Azure / Microsoft Docs
description: Este artigo fornece informações sobre como pode aceder ao código fonte para o UI acelerador de solução de monitorização remota e fazer algumas personalizações.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 2789ed642979616a4491a61d146d8468552ec2e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318471"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Personalize o acelerador de solução de monitorização remota

Este artigo fornece informações sobre como pode aceder ao código fonte e personalizar a UI do acelerador de solução de monitorização remota.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Preparar um ambiente de desenvolvimento local para a UI

O código UI do acelerador de solução de monitorização remota é implementado utilizando a estrutura React.js. Pode encontrar o código fonte no [repositório azure-iot-pcs-monitor-remote-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) GitHub.

Para fazer alterações na UI, pode executar uma cópia localmente. Para completar ações como a recuperação da telemetria, a cópia local conecta-se a uma instância implementada da solução.

Os seguintes passos descrevem o processo de criação de um ambiente local para o desenvolvimento da UI:

1. Implementar uma instância **básica** do acelerador de solução utilizando o **CLI dos computadores.** Tome nota do nome da sua implantação e das credenciais que forneceu para a máquina virtual. Para obter mais informações, consulte [implementar utilizando o CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Para permitir o acesso do SSH à máquina virtual que acolhe os microserviços na sua solução, utilize o portal Azure ou o Azure Cloud Shell. Por exemplo:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Apenas permitir o acesso ao SSH durante o teste e o desenvolvimento. Se ativar o SSH, [deve desativá-lo assim que terminar de o utilizar.](../security/fundamentals/network-best-practices.md#disable-rdpssh-access-to-virtual-machines)

1. Utilize o portal Azure ou o Azure Cloud Shell para encontrar o nome e o endereço IP público da sua máquina virtual. Por exemplo:

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Utilize o SSH para ligar à sua máquina virtual. Utilize o endereço IP a partir do passo anterior e as credenciais que forneceu quando executou **pcs** para implementar a solução. O `ssh` comando está disponível na Azure Cloud Shell.

1. Para permitir a ligação do UX local, corram os seguintes comandos na casca de choque na máquina virtual:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Depois de ver o comando completo e o web site começar, pode desligar-se da máquina virtual.

1. Na sua cópia local do [repositório azure-iot-pcs-monitoring-webui,](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) edite o ficheiro **.env** para adicionar o URL da sua solução implementada:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. A um pedido de comando, navegue para a sua cópia local da `azure-iot-pcs-remote-monitoring-webui` pasta.

1. Para instalar as bibliotecas necessárias e executar a UI localmente, executar os seguintes comandos:

    ```cmd/sh
    npm install
    npm start
    ```

1. O comando anterior executa a UI localmente em http: \/ /localhost:3000/dashboard. Pode editar o código enquanto o site está em execução e vê-lo atualizado dinamicamente.

## <a name="customize-the-layout"></a>Personalize o layout

Cada página na solução de Monitorização Remota é composta por um conjunto de *controlos, referidos* como painéis no código fonte. A página **dashboard** é composta por cinco painéis: Visão geral, Mapa, Alertas, Telemetria e Analytics. Pode encontrar o código fonte que define cada página e os seus painéis no [repositório gitHub de monitorização remota do pcs-remote-monitoring-webui.](https://github.com/Azure/pcs-remote-monitoring-webui) Por exemplo, o código que define a página **do Dashboard,** o seu layout e os painéis na página estão localizados na pasta [src/componentes/páginas/dashboard.](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard)

Como os painéis gerem o seu próprio layout e dimensionamento, você pode facilmente modificar o layout de uma página. Faça as seguintes alterações ao elemento **PageContent** no `src/components/pages/dashboard/dashboard.js` ficheiro para:

* Troque as posições do mapa e dos painéis de telemetria.
* Altere as larguras relativas do mapa e dos painéis de análise.

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

![Alterar o layout do painel](./media/iot-accelerators-remote-monitoring-customize/layout.png)

Também pode adicionar várias instâncias do mesmo painel, ou várias versões se [duplicar e personalizar um painel](#duplicate-and-customize-an-existing-control). O exemplo a seguir mostra como adicionar duas instâncias do painel de telemetria. Para esporar estas alterações, edite o `src/components/pages/dashboard/dashboard.js` ficheiro:

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

Em seguida, pode ver diferentes telemetrias em cada painel:

![Vários painéis de telemetria](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Duplicar e personalizar um controlo existente

Os passos a seguir descrevem como duplicar um painel existente, modificá-lo e, em seguida, utilizar a versão modificada. Os passos utilizam o painel **de alertas** como exemplo:

1. Na sua cópia local do repositório, faça uma cópia da pasta **de alertas** na `src/components/pages/dashboard/panels` pasta. Diga o nome da nova cópia **cust_alerts**.

1. No ficheiro **alertsPanel.js** na pasta **cust_alerts,** edite o nome da classe para ser **CustAlertsPanel:**

    ```javascript
    export class CustAlertsPanel extends Component {
    ```

1. Adicione a seguinte linha ao `src/components/pages/dashboard/panels/index.js` ficheiro:

    ```javascript
    export * from './cust_alerts';
    ```

1. `alertsPanel` `CustAlertsPanel` Substitua-o no `src/components/pages/dashboard/dashboard.js` ficheiro:

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

Substituiu agora o painel de **alertas originais** por uma cópia chamada **CustAlerts.** Esta cópia é a mesma que a original. Agora pode modificar a cópia. Por exemplo, para alterar a ordem da coluna no painel **de alertas:**

1. Abra o ficheiro `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js`.

1. Modificar as definições da coluna tal como mostrado no seguinte corte de código:

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

A imagem que se segue mostra a nova versão do painel de **alertas:**

![painel de alertas atualizado](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Personalize o gráfico de telemetria

Os ficheiros da `src/components/pages/dashboard/panels/telemtry` pasta definem o gráfico de telemetria na página **do Painel de Instrumentos.** A UI recupera a telemetria a partir da extremidade traseira da solução no `src/services/telemetryService.js` ficheiro. Os seguintes passos mostram-lhe como alterar o período de tempo exibido na tabela de telemetria de 15 para 5 minutos:

1. No `src/services/telemetryService.js` ficheiro, localize a função chamada **getTelemetryByDeviceIdP15M**. Faça uma cópia desta função e modifique a cópia da seguinte forma:

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

1. Para utilizar esta nova função para povoar o gráfico de telemetria, abra o `src/components/pages/dashboard/dashboard.js` ficheiro. Localize a linha que inicializa o fluxo de telemetria e modifique-a da seguinte forma:

    ```javascript
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

O gráfico de telemetria mostra agora os cinco minutos de dados de telemetria:

![Gráfico de telemetria mostrando um dia](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Adicione um novo KPI

A página **dashboard** apresenta KPI's no painel **Analytics.** Estes KPI's são calculados no `src/components/pages/dashboard/dashboard.js` ficheiro. Os KPI's são renderizados pelo `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` ficheiro. Os seguintes passos descrevem como calcular e renderizar um novo valor KPI na página **dashboard.** O exemplo mostrado é adicionar uma nova variação percentual nos alertas de aviso KPI:

1. Abra o ficheiro `src/components/pages/dashboard/dashboard.js`. Modifique o objeto **InicialState** para incluir uma propriedade **de AdvertênciaAlertsChange** da seguinte forma:

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

1. Modifique o objeto **atual DeertsStats** para incluir **o TotalWarningCount** como uma propriedade:

    ```javascript
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alertsPerDeviceId: updatedAlertsPerDeviceId
    };
    ```

1. Calcule o novo KPI. Encontre o cálculo para a contagem de alertas críticos. Duplicar o código e modificar a cópia da seguinte forma:

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

1. Inclua o novo **avisoAlertsChange** KPI no fluxo KPI:

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

1. Inclua o novo **avisoAlertsChange** KPI nos dados estatais utilizados para tornar a UI:

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

1. Atualizar os dados transmitidos ao painel KPI's:

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

Já terminou as alterações no `src/components/pages/dashboard/dashboard.js` ficheiro. Os seguintes passos descrevem as alterações a fazer no `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` ficheiro para exibir o novo KPI:

1. Modificar a seguinte linha de código para recuperar o novo valor KPI da seguinte forma:

    ```javascript
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Modifique a marcação para mostrar o novo valor KPI da seguinte forma:

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

A página **dashboard** apresenta agora o novo valor KPI:

![Aviso KPI](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Personalize o mapa

Consulte a página do [mapa Personalizar](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) no GitHub para obter detalhes sobre os componentes do mapa na solução.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Outras opções de personalização

Para modificar ainda mais a camada de apresentação e visualizações na solução de Monitorização Remota, pode editar o código. Os repositórios gitHub relevantes são:

* [O microserviço de configuração para Azure IoT Solutions (.NET)](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [O microserviço de configuração para Azure IoT Solutions (Java)](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [Azure IoT PCS Monitorização Remota Web UI](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre os recursos disponíveis para ajudá-lo a personalizar a UI web no acelerador de solução de Monitorização Remota. Para saber mais sobre a personalização da UI, consulte os seguintes artigos:

* [Adicione uma página personalizada à UI do acelerador de solução de monitorização remota](iot-accelerators-remote-monitoring-customize-page.md)
* [Adicione um serviço personalizado à UI do acelerador de solução de monitorização remota](iot-accelerators-remote-monitoring-customize-service.md)
* [Adicione uma grelha personalizada à UI do acelerador de solução de monitorização remota](iot-accelerators-remote-monitoring-customize-grid.md)
* [Adicione um flyout personalizado à UI do acelerador de solução de monitorização remota](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Adicione um painel personalizado ao painel no painel de instrumentos na UI do acelerador de solução de monitorização remota](iot-accelerators-remote-monitoring-customize-panel.md)

Para obter mais informações conceptuais sobre o acelerador de solução de monitorização remota, consulte [a arquitetura de monitorização remota](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Para obter mais informações sobre a personalização dos microserviços de solução de monitorização remota, consulte [Personalizar e redistribuir um microserviço.](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->
