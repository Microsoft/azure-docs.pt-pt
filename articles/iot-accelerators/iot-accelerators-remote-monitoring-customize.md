---
title: Personalize a solução de monitorização remota UI - Azure [ Microsoft Docs
description: Este artigo fornece informações sobre como pode aceder ao código fonte para o acelerador de soluções de Monitorização Remota UI e fazer algumas personalizações.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.openlocfilehash: eb3d5fea68b5b1b6e648943cb3dbaab5857e9e07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68608001"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Personalize o acelerador de soluções de monitorização remota

Este artigo fornece informações sobre como pode aceder ao código fonte e personalizar o acelerador de soluções de Monitorização Remota UI.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Prepare um ambiente de desenvolvimento local para a UI

O código UI da solução de monitorização remota é implementado utilizando a estrutura React.js. Pode encontrar o código fonte no [repositório azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) GitHub.

Para fazer alterações na UI, pode executar uma cópia local. Para completar ações como a recuperação da telemetria, a cópia local liga-se a um exemplo implantado da solução.

Os seguintes passos delineiam o processo para criar um ambiente local para o desenvolvimento da UI:

1. Implemente uma instância **básica** do acelerador de solução utilizando o **CLI pcs.** Tome nota do nome da sua implantação e das credenciais que forneceu para a máquina virtual. Para mais informações, consulte [A utilização do CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Para permitir o acesso ao SSH à máquina virtual que acolhe os microserviços na sua solução, utilize o portal Azure ou a Azure Cloud Shell. Por exemplo:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Só permite o acesso sSH durante o teste e desenvolvimento. Se ativar o SSH, [deve desativá-lo assim que terminar de o utilizar](../security/fundamentals/network-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Utilize o portal Azure ou a Azure Cloud Shell para encontrar o nome e endereço IP público da sua máquina virtual. Por exemplo:

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Utilize o SSH para se ligar à sua máquina virtual. Utilize o endereço IP do passo anterior e as credenciais que forneceu quando executou **os PCs** para implementar a solução. O `ssh` comando está disponível na Casca de Nuvem Azure.

1. Para permitir que o UX local se conecte, execute os seguintes comandos na concha da batida na máquina virtual:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Depois de ver o comando completo e o site começar, pode desligar-se da máquina virtual.

1. Na sua cópia local do [repositório azure-iot-pcs-remote-monitoring-webui,](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) edite o ficheiro **.env** para adicionar o URL da sua solução implementada:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. Num pedido de comando, navegue `azure-iot-pcs-remote-monitoring-webui` para a sua cópia local da pasta.

1. Para instalar as bibliotecas necessárias e executar a UI localmente, executar os seguintes comandos:

    ```cmd/sh
    npm install
    npm start
    ```

1. O comando anterior executa a\/UI localmente em http: /localhost:3000/dashboard. Pode editar o código enquanto o site está em execução e vê-lo atualizar dinamicamente.

## <a name="customize-the-layout"></a>Personalize o layout

Cada página da solução de Monitorização Remota é composta por um conjunto de *controlos, referidos como painéis* no código fonte. A página **do Dashboard** é composta por cinco painéis: Visão geral, mapa, alertas, telemetria e Análise. Pode encontrar o código fonte que define cada página e os seus painéis no repositório [pcs-remote-monitoring-webui](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub. Por exemplo, o código que define a página **do Dashboard,** o seu layout e os painéis na página estão localizados no [src/componentes/páginas/pasta do painel.](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard)

Como os painéis gerem o seu próprio layout e dimensionamento, você pode facilmente modificar o layout de uma página. Faça as seguintes alterações ao `src/components/pages/dashboard/dashboard.js` elemento **PageContent** no ficheiro para:

* Troque as posições dos painéis de mapas e telemetria.
* Mude as larguras relativas do mapa e dos painéis de análise.

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

Também pode adicionar várias instâncias do mesmo painel, ou várias versões se [duplicar e personalizar um painel.](#duplicate-and-customize-an-existing-control) O exemplo que se segue mostra como adicionar duas instâncias do painel de telemetria. Para efazer estas `src/components/pages/dashboard/dashboard.js` alterações, edite o ficheiro:

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

Em seguida, pode ver uma telemetria diferente em cada painel:

![Múltiplos painéis de telemetria](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Duplicar e personalizar um controlo existente

Os seguintes passos descrevem como duplicar um painel existente, modificá-lo e, em seguida, utilizar a versão modificada. Os passos utilizam o painel de **alertas** como exemplo:

1. Na sua cópia local do repositório, faça uma cópia `src/components/pages/dashboard/panels` da pasta de **alertas** na pasta. Diga o nome da nova cópia **cust_alerts.**

1. No ficheiro **alertsPanel.js** na pasta **cust_alerts,** editar o nome da classe para ser **CustAlertsPanel:**

    ```javascript
    export class CustAlertsPanel extends Component {
    ```

1. Adicione a seguinte `src/components/pages/dashboard/panels/index.js` linha ao ficheiro:

    ```javascript
    export * from './cust_alerts';
    ```

1. `alertsPanel` Substitua-o `src/components/pages/dashboard/dashboard.js` no `CustAlertsPanel` ficheiro:

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

Substituiu o painel de **alertas originais** por uma cópia chamada **CustAlerts.** Esta cópia é a mesma que a original. Agora pode modificar a cópia. Por exemplo, para alterar a encomenda da coluna no painel de **alertas:**

1. Abra o ficheiro `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js`.

1. Modificar as definições da coluna, tal como mostrado no seguinte código:

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

![painel alertaatualizado](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Personalize o gráfico de telemetria

Os ficheiros `src/components/pages/dashboard/panels/telemtry` da pasta definem o gráfico de telemetria na página **do Dashboard.** A UI recupera a telemetria da solução traseira do `src/services/telemetryService.js` ficheiro. Os seguintes passos mostram-lhe como alterar o período de tempo apresentado na tabela de telemetria de 15 a 5 minutos:

1. No `src/services/telemetryService.js` ficheiro, localize a função chamada **getTelemettryByDeviceIdP15M**. Faça uma cópia desta função e modifique a cópia da seguinte forma:

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

1. Para utilizar esta nova função para preencher `src/components/pages/dashboard/dashboard.js` o gráfico de telemetria, abra o ficheiro. Localize a linha que inicializa o fluxo de telemetria e modifique-a da seguinte forma:

    ```javascript
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

O gráfico de telemetria mostra agora os cinco minutos de dados de telemetria:

![Gráfico de telemetria mostrando um dia](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Adicione um novo KPI

A página **do Dashboard** apresenta KPIs no painel **Analytics.** Estes KP's são `src/components/pages/dashboard/dashboard.js` calculados no ficheiro. Os KP's são `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` prestados pelo ficheiro. Os seguintes passos descrevem como calcular e renderizar um novo valor KPI na página **do Dashboard.** O exemplo mostrado é adicionar uma nova variação percentual nos alertas de alerta KPI:

1. Abra o ficheiro `src/components/pages/dashboard/dashboard.js`. Modifique o objeto **inicial do Estado** para incluir uma propriedade **warningAlertsChange** da seguinte forma:

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

1. Modificar o objeto **actualAlertsStats** para incluir **totalWarningCount** como uma propriedade:

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

1. Incluir o novo **avisoAlertsChange** KPI nos dados estatais utilizados para renderizar a UI:

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

1. Atualizar os dados transmitidos ao painel kPIs:

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

Já terminou as alterações `src/components/pages/dashboard/dashboard.js` no ficheiro. Os seguintes passos descrevem `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` as alterações a fazer no ficheiro para exibir o novo KPI:

1. Modificar a seguinte linha de código para recuperar o novo valor KPI da seguinte forma:

    ```javascript
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Modifique a marcação para exibir o novo valor KPI da seguinte forma:

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

A página **do Dashboard** apresenta agora o novo valor KPI:

![KPI aviso](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Personalize o mapa

Consulte a página de [mapa Personalizar](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) no GitHub para obter detalhes dos componentes do mapa na solução.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Outras opções de personalização

Para modificar ainda mais a camada de apresentação e visualização na solução de Monitorização Remota, pode editar o código. Os repositórios gitHub relevantes são:

* [O microserviço de configuração para Soluções Azure IoT (.NET)](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [O microserviço de configuração para Soluções Azure IoT (Java)](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [UI de monitorização remota do PCS Azure IoT](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre os recursos disponíveis para ajudá-lo a personalizar a UI web no acelerador de soluções de Monitorização Remota. Para saber mais sobre personalizar a UI, consulte os seguintes artigos:

* [Adicione uma página personalizada ao uI web de solução de monitorização remota](iot-accelerators-remote-monitoring-customize-page.md)
* [Adicione um serviço personalizado ao uI web de solução de monitorização remota](iot-accelerators-remote-monitoring-customize-service.md)
* [Adicione uma grelha personalizada ao uI web de solução de monitorização remota](iot-accelerators-remote-monitoring-customize-grid.md)
* [Adicione um flyout personalizado ao UI web de solução de monitorização remota](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Adicione um painel personalizado ao painel no UI web de solução de monitorização remota](iot-accelerators-remote-monitoring-customize-panel.md)

Para obter informações mais conceptuais sobre o acelerador de soluções de monitorização remota, consulte [a arquitetura de Monitorização Remota](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Para obter mais informações sobre a personalização dos microserviços da solução de monitorização remota, consulte [Personalizar e reutilizar um microserviço](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->
