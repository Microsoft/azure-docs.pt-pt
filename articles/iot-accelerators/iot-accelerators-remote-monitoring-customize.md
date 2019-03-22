---
title: Personalizar a solução de monitorização remota da interface do Usuário - Azure | Documentos da Microsoft
description: Este artigo fornece informações sobre como pode acessar o código-fonte para o acelerador de solução de monitorização remota da interface do Usuário e fazer algumas personalizações.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.openlocfilehash: aed63e332375be4f8ed939cf162545c9f366f329
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317600"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Personalizar o acelerador de solução de monitorização remota

Este artigo fornece informações sobre como pode acessar o código-fonte e personalizar o acelerador de solução de monitorização remota da interface do Usuário.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Preparar um ambiente de desenvolvimento local para a interface do Usuário

O acelerador de solução de monitorização remota código de interface do Usuário é implementado usando a estrutura do react. js. Pode encontrar o código-fonte no [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) repositório do GitHub.

Para fazer alterações na interface do usuário, pode executar uma cópia do mesmo localmente. Para concluir as ações como a obtenção de telemetria, a cópia local liga-se para uma instância da solução.

Os passos seguintes descrevem o processo para configurar um ambiente local para o desenvolvimento de interface do Usuário:

1. Implementar um **básica** instância do acelerador de solução utilizando o **pcs** CLI. Tome nota do nome da sua implementação e as credenciais fornecidas para a máquina virtual. Para obter mais informações, consulte [implementar com a CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Para ativar o acesso SSH à máquina virtual que aloja os microsserviços na sua solução, utilize o portal do Azure ou o Azure Cloud Shell. Por exemplo:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Ative o acesso SSH apenas durante teste e desenvolvimento. Se ativar o SSH, [deve desabilitá-la assim que tiver terminado de usá-lo](../security/azure-security-network-security-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Utilize o portal do Azure ou o Azure Cloud Shell para encontrar o nome e endereço IP público da sua máquina virtual. Por exemplo:

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Utilize o SSH para ligar à máquina virtual. Utilize o endereço IP do passo anterior e as credenciais que forneceu quando executou **pcs** para implantar a solução. O `ssh` comando está disponível no Azure Cloud Shell.

1. Para permitir que a experiência do Usuário local ligar, execute os seguintes comandos na shell do bash na máquina virtual:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Depois de confirmar o comando for concluído e o web site é iniciado, pode desligar da máquina virtual.

1. Na sua cópia local do [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) repositório, editar o **. env** ficheiro para adicionar o URL da sua solução implementada:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. No prompt de comando, navegue para a sua cópia local do `azure-iot-pcs-remote-monitoring-webui` pasta.

1. Para instalar as bibliotecas necessárias e executar localmente a interface do Usuário, execute os seguintes comandos:

    ```cmd/sh
    npm install
    npm start
    ```

1. O comando anterior é executada a interface do Usuário localmente em http:\//localhost:3000 / dashboard. Pode editar o código, enquanto o site está em execução e vê-lo a atualizar dinamicamente.

## <a name="customize-the-layout"></a>Personalizar o esquema

Cada página na solução de monitorização remota é composta por um conjunto de controles, denominados *painéis* no código-fonte. O **Dashboard** página é constituída por cinco painéis: Descrição geral, mapa, alertas, telemetria e análise. Pode encontrar o código-fonte que define cada página e seus painéis na [pcs-remote-monitoring-serem](https://github.com/Azure/pcs-remote-monitoring-webui) repositório do GitHub. Por exemplo, o código que define a **Dashboard** página, o esquema e os painéis da página está localizado na [src/componentes/páginas/dashboard](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) pasta.

Uma vez que os painéis de gerir o seu próprio layout e o dimensionamento, pode facilmente modificar o layout de uma página. Efetue as seguintes alterações para o **PageContent** elemento no `src/components/pages/dashboard/dashboard.js` de ficheiros para:

* Troque as posições dos painéis de mapa e telemetria.
* Altere as larguras relativas dos painéis de mapa e análise.

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

![Esquema do painel de alteração](./media/iot-accelerators-remote-monitoring-customize/layout.png)

Também pode adicionar várias instâncias do mesmo painel ou várias versões se [duplicar e personalizar um painel](#duplicate-and-customize-an-existing-control). O exemplo seguinte mostra como adicionar duas instâncias do painel de telemetria. Para efetuar estas alterações, edite o `src/components/pages/dashboard/dashboard.js` ficheiro:

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

Em seguida, pode ver a telemetria de diferente em cada painel:

![Vários painéis de telemetria](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Duplicar e personalizar um controle existente

Os passos seguintes descrevem como duplicar um painel existente, modificá-lo e, em seguida, utilizar a versão modificada. Os passos utilizam o **alertas** painel como exemplo:

1. Na sua cópia local do repositório, fazer uma cópia do **alertas** pasta na `src/components/pages/dashboard/panels` pasta. Nomeie a nova cópia **cust_alerts**.

1. Na **alertsPanel.js** de ficheiros a **cust_alerts** pasta, edite o nome da classe a ser **CustAlertsPanel**:

    ```javascript
    export class CustAlertsPanel extends Component {
    ```

1. Adicione a seguinte linha para o `src/components/pages/dashboard/panels/index.js` ficheiro:

    ```javascript
    export * from './cust_alerts';
    ```

1. Substitua `alertsPanel` com `CustAlertsPanel` no `src/components/pages/dashboard/dashboard.js` ficheiro:

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

Agora tiver substituído o original **alertas** painel com uma cópia chamada **CustAlerts**. Esta cópia é o mesmo que o original. Agora pode modificar a cópia. Por exemplo, para alterar a coluna de ordenação no **alertas** painel:

1. Abra o ficheiro `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js`.

1. Modificar as definições da coluna, conforme mostrado no seguinte fragmento de código:

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

Captura de ecrã seguinte mostra a nova versão dos **alertas** painel:

![Painel de alertas atualizado](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Personalizar o gráfico de telemetria

Os ficheiros na `src/components/pages/dashboard/panels/telemtry` pasta definem o gráfico de telemetria sobre o **Dashboard** página. A interface do Usuário recupera a telemetria da solução de back-end no `src/services/telemetryService.js` ficheiro. Os passos seguintes mostram como alterar o período de tempo apresentado no gráfico de telemetria de 15 para 5 minutos:

1. Na `src/services/telemetryService.js` do ficheiro, localize a função chamada **getTelemetryByDeviceIdP15M**. Faça uma cópia desta função e modificar a cópia da seguinte forma:

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

1. Para usar esta nova função para preencher o gráfico de telemetria, abra o `src/components/pages/dashboard/dashboard.js` ficheiro. Localize a linha que inicializa o fluxo de telemetria e modificá-lo da seguinte forma:

    ```javascript
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

O gráfico de telemetria agora mostra os cinco minutos de dados de telemetria:

![Gráfico de telemetria com um dia](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Adicionar um novo KPI

O **Dashboard** página apresenta os KPIs no **Analytics** painel. Estes KPIs são calculados no `src/components/pages/dashboard/dashboard.js` ficheiro. Os KPIs são processados pelo `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` ficheiro. Os passos seguintes descrevem como calcular e processar um novo valor KPI no **Dashboard** página. O exemplo mostrado é adicionar uma nova alteração de percentagem de alertas de aviso KPI:

1. Abra o ficheiro `src/components/pages/dashboard/dashboard.js`. Modificar a **initialState** objeto para incluir um **warningAlertsChange** propriedade da seguinte forma:

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

1. Modificar a **currentAlertsStats** objeto para incluir **totalWarningCount** como uma propriedade de:

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

1. Incluir o novo **warningAlertsChange** KPI no fluxo de KPI:

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

1. Incluir o novo **warningAlertsChange** KPI nos dados de estado usados para processar a interface do Usuário:

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

1. Atualize os dados transmitidos para o painel de KPIs:

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

Agora concluiu as alterações no `src/components/pages/dashboard/dashboard.js` ficheiro. Os passos seguintes descrevem as alterações para fazer no `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` ficheiro para apresentar o novo KPI:

1. Modifique a seguinte linha de código para recuperar o novo valor KPI da seguinte forma:

    ```javascript
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Modificar a marcação para apresentar o novo valor KPI da seguinte forma:

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

O **Dashboard** página agora apresenta o novo valor KPI:

![Aviso de KPI](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Personalizar o mapa

Consulte a [mapa de personalizar](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) página no GitHub para obter detalhes sobre os componentes de mapa da solução.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Outras opções de personalização

Para modificar a camada de apresentação e as visualizações na solução de monitorização remota, pode editar o código. Repositórios do GitHub relevantes são:

* [Os microsserviços de configuração para soluções de IoT do Azure (.NET)](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [Os microsserviços de configuração para soluções de IoT do Azure (Java)](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [Monitorização da IU Web de remota de PCS de IoT do Azure](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu sobre os recursos disponíveis para o ajudar a personalizar a IU da web no solution accelerator monitorização remota. Para saber mais sobre como personalizar a interface do Usuário, consulte os artigos seguintes:

* [Adicionar uma página personalizada para monitorização remota solution accelerator da IU da web](iot-accelerators-remote-monitoring-customize-page.md)
* [Adicionar um serviço personalizado para monitorização remota solution accelerator da IU da web](iot-accelerators-remote-monitoring-customize-service.md)
* [Adicionar uma grade personalizada para monitorização remota solution accelerator da IU da web](iot-accelerators-remote-monitoring-customize-grid.md)
* [Adicionar uma lista de opções personalizada para monitorização remota solution accelerator da IU da web](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Adicionar um painel personalizado para o dashboard de monitorização remota solution accelerator da IU da web](iot-accelerators-remote-monitoring-customize-panel.md)

Para obter mais informações concetuais sobre o acelerador de solução de monitorização remota, consulte [arquitetura de monitorização remota](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Para obter mais informações sobre como personalizar os microsserviços de solução de monitorização remota, consulte [personalizar e Reimplementar um microsserviço](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->
