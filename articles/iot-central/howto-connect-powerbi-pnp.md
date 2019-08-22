---
title: Visualizar os dados do Azure IoT Central em um painel de Power BI | Microsoft Docs
description: Use a solução Power BI para o IoT Central do Azure para visualizar e analisar seus dados de IoT Central.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/09/2019
ms.topic: conceptual
ms.openlocfilehash: dcfabbfe5bf807e8820fa14b6b8234d276578dcc
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880075"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard-preview-features"></a>Visualizar e analisar seus dados de IoT Central do Azure em um painel de Power BI (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Este tópico se aplica aos administradores do.*

![Pipeline de solução Power BI](media/howto-connect-powerbi-pnp/iot-continuous-data-export.png)

Use a solução Power BI para o IoT Central do Azure para criar um painel de Power BI poderoso para monitorar o desempenho de seus dispositivos IoT. No painel do Power BI, você pode:
- Acompanhar a quantidade de dados que seus dispositivos estão enviando ao longo do tempo
- Comparar o volume de dados entre telemetria, Estados e eventos
- Identificar os dispositivos que estão relatando muitas medidas
- Observar as tendências históricas das medições do dispositivo
- Identificar dispositivos problemáticos que enviam muitos eventos críticos

Essa solução configura o pipeline que pega os dados em sua conta de armazenamento de BLOBs do Azure da [exportação de dados contínuas](howto-export-data-blob-storage-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json). Esses dados fluem para o Azure Functions, Azure Data Factory e o banco de dados SQL do Azure para processar e transformar o dado. A saída pode ser visualizada e analisada em um relatório Power BI que você pode baixar como um arquivo PBIX. Todos esses recursos são criados em sua assinatura do Azure, para que você possa personalizar cada componente para atender às suas necessidades.

## <a name="get-the-power-bi-solution-for-azure-iot-centralhttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>Obtenha a [solução de Power bi para IOT central do Azure](https://aka.ms/iotcentralpowerbisolutiontemplate) do Microsoft AppSource.

## <a name="prerequisites"></a>Pré-requisitos
A configuração da solução requer o seguinte:
- Acesso a uma assinatura do Azure
- Dados exportados usando a [exportação de dados contínuas](howto-export-data-blob-storage-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) do seu aplicativo IOT central. Recomendamos que você ative medidas, dispositivos e fluxos de modelo de dispositivo para obter o máximo do painel de Power BI.
- Power BI Desktop (versão mais recente)
- Power BI Pro (se você quiser compartilhar o painel com outras pessoas)

## <a name="reports"></a>Relatórios

Dois relatórios são gerados automaticamente. 

O primeiro relatório mostra uma exibição histórica das medidas relatadas pelos dispositivos e divide os diferentes tipos de medidas e dispositivos que enviaram o número mais alto de medições.

![Power BI página de relatório 1](media/howto-connect-powerbi-pnp/template-page1-hasdata.PNG)

O segundo relatório se aprofunda mais em eventos e mostra uma exibição histórica de erros e avisos relatados. Ele também mostra quais dispositivos estão relatando o maior número de eventos em todo o tempo, bem como eventos de erros e de aviso específicos.

![Página de relatório do Power BI 2](media/howto-connect-powerbi-pnp/template-page2-hasdata.PNG)

## <a name="architecture"></a>Arquitetura
Todos os recursos que foram criados podem ser acessados no portal do Azure. Tudo deve estar em um grupo de recursos.

![Exibição do portal do Azure do grupo de recursos](media/howto-connect-powerbi-pnp/azure-deployment.PNG)

As especificidades de cada recurso e como ela é usada são descritas abaixo.

### <a name="azure-functions"></a>Funções do Azure
O aplicativo de funções do Azure é disparado cada vez que um novo arquivo é gravado no armazenamento de BLOBs. As funções extraem os campos dentro de cada medição, dispositivos e arquivo de modelos de dispositivo e popula várias tabelas intermediárias do SQL a serem usadas pelo Azure Data Factory.

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory se conecta ao banco de dados SQL como um serviço vinculado. Ele executa atividades de procedimento armazenado que processam os dados e os armazenam nas tabelas de análise.

### <a name="azure-sql-database"></a>Base de Dados SQL do Azure
Essas tabelas são criadas automaticamente para popular os relatórios padrão. Explore esses esquemas no Power BI e você pode criar suas próprias visualizações sobre esses dados.

| Nome da tabela |
|------------|
|[Analytics]. Medidas|
|[Analytics]. Mensagens|
|[estágio]. Medidas|
|[Analytics]. Properties|
|[Analytics]. [PropertyDefinitions]|
|[analytics].[MeasurementDefinitions]|
|[Analytics]. Pseudodispositivos|
|[Analytics]. [DeviceTemplates]|
|[dbo]. Date|
|[dbo]. ChangeTracking|

## <a name="estimated-costs"></a>Custos estimados

Aqui está uma estimativa dos custos do Azure (Azure functions, Data Factory, Azure SQL) envolvida. Todos os preços estão em USD. Tenha em mente que os preços variam de acordo com a região, portanto, você deve sempre procurar os preços mais recentes dos serviços individuais para obter os preços reais.
Os padrões a seguir são definidos para você no modelo (você pode modificar qualquer um deles depois que as coisas forem configuradas):

- Azure Functions: Plano do serviço de aplicativo S1, $74.40/mês
- SQL do Azure S1, ~ $30/mês

Incentivamos você a se familiarizar com as várias opções de preços e ajustar as coisas de acordo com suas necessidades.

## <a name="resources"></a>Recursos

Visite AppSource para obter a [solução de Power bi para o IOT central do Azure](https://aka.ms/iotcentralpowerbisolutiontemplate).

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu a visualizar seus dados em Power BI, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Como gerenciar dispositivos](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)