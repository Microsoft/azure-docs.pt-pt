---
title: Visualize os seus dados do Azure IoT Central no dashboard do Power BI | Documentos da Microsoft
description: Utilize a solução de BI de energia para o Azure IoT Central para visualizar e analisar os seus dados de IoT Central.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/15/2019
ms.topic: conceptual
ms.openlocfilehash: 322be1e13662d92a3cb0a805a9ccaacd05928f7d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60886818"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Visualize e analise os dados do Azure IoT Central no dashboard do Power BI

*Este tópico aplica-se aos administradores.*

![Pipeline de solução do Power BI](media/howto-connect-powerbi/iot-continuous-data-export.png)

Utilize a solução de BI de energia para o Azure IoT Central para criar um dashboard do Power BI poderoso para monitorizar o desempenho dos seus dispositivos IoT. No dashboard do Power BI, pode:
- Controlar a quantidade de dados estão a enviar seus dispositivos ao longo do tempo
- Comparar o volume de dados entre Estados, quantidades de telemetria e eventos
- Identificar dispositivos que estão a enviar relatórios inúmeras medições
- Observe as tendências históricas derivadas de medidas de dispositivo
- Identificar dispositivos problemáticos que enviar muitos eventos críticos

Esta solução configura o pipeline que utiliza os dados na sua conta de armazenamento de Blobs do Azure partir [a exportação contínua de dados](howto-export-data.md). Estes dados fluem através das funções do Azure, Azure Data Factory e SQL Database do Azure para processar e transformar os dados. A saída pode ser visualizada e analisada no relatório do Power BI que pode baixar como um ficheiro PBIX. Todos esses recursos são criados na sua subscrição do Azure, pelo que pode personalizar cada componente de acordo com as suas necessidades.

## <a name="get-the-power-bi-solution-for-azure-iot-centralhttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>Obter o [do Power BI de solução para o Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate) do Microsoft AppSource.

## <a name="prerequisites"></a>Pré-requisitos
Configurar a solução requer o seguinte:
- Acesso a uma subscrição do Azure
- Exportar dados com [a exportação contínua de dados](howto-export-data.md) da sua aplicação IoT Central. Recomendamos que ative medidas, dispositivos e fluxos de modelo do dispositivo para aproveitar ao máximo o dashboard do Power BI.
- Power BI Desktop (versão mais recente)
- Power BI Pro (se quiser partilhar o dashboard com outras pessoas)

## <a name="reports"></a>Relatórios

Dois relatórios são gerados automaticamente. 

O primeiro relatório mostra uma vista histórica das medidas de reportados pelos dispositivos e divide os diferentes tipos de medidas e dispositivos que enviaram o maior número de medidas.

![Página de relatório do Power BI 1](media/howto-connect-powerbi/template-page1-hasdata.PNG)

O segundo relatório se aprofunda-se nos eventos e mostra uma vista histórica dos erros e avisos relatados. Ela também mostra quais os dispositivos que comunicam o maior número de eventos todos juntos, bem como especificamente os eventos de erro e eventos de aviso.

![Página de relatório do Power BI 2](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="architecture"></a>Arquitetura
Todos os recursos que foram criados podem ser acedidos no portal do Azure. Tudo o que deve ser sob um único grupo de recursos.

![Vista de Portal do Azure do grupo de recursos](media/howto-connect-powerbi/azure-deployment.PNG)

As especificidades de cada recurso e como ela é usada é descrita abaixo.

### <a name="azure-functions"></a>Funções do Azure
A aplicação de função do Azure é acionada sempre que um novo ficheiro é escrito para o armazenamento de Blobs. As funções de extrair os campos dentro de cada medidas, dispositivos e o ficheiro de modelos de dispositivo e preenche a várias tabelas SQL intermediárias a ser utilizado pelo Azure Data Factory.

### <a name="azure-data-factory"></a>Azure Data Factory
O Azure Data Factory liga à base de dados SQL como um serviço ligado. Ele executa as atividades de procedimento armazenado que processam os dados e armazená-lo nas tabelas de análise.

### <a name="azure-sql-database"></a>Base de Dados SQL do Azure
Essas tabelas são criadas automaticamente para preencher os relatórios padrão. Explore esses esquemas no Power BI e pode criar suas próprias visualizações sobre estes dados.

| Nome da tabela |
|------------|
|[analytics].[Measurements]|
|[analytics]. [Mensagens]|
|[fase]. [Medidas]|
|[analytics]. [Propriedades]|
|[analytics].[PropertyDefinitions]|
|[analytics].[MeasurementDefinitions]|
|[analytics]. [Dispositivos]|
|[analytics].[DeviceTemplates]|
|[dbo].[date]|
|[dbo].[ChangeTracking]|

## <a name="estimated-costs"></a>Custos estimados

Aqui está uma estimativa dos custos do Azure (função do Azure, o Data Factory, Azure SQL) envolvidos. Todos os preços são em dólares americanos. Tenha em atenção que os preços variam consoante a região, para que sempre deve consultar os preços mais recentes dos serviços individuais para obter os preços reais.
As seguintes predefinições estão definidas para no modelo (pode modificar qualquer uma dessas após coisas mãos à obra):

- Funções do Azure: Plano do App Service S1, US $74.40/ mês
- S1 SQL do Azure, ~$30/month

Aconselhamo-lo a familiarizar-se com várias opções de preços e ajustar as coisas para se adequar às suas necessidades.

## <a name="resources"></a>Recursos

Visite o AppSource para obter o [Power BI solução do Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate).

## <a name="next-steps"></a>Passos Seguintes

Agora que sabe como visualizar os seus dados no Power BI, este é o passo seguinte sugerido:

> [!div class="nextstepaction"]
> [Como gerir dispositivos](howto-manage-devices.md)