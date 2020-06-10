---
title: Visualize os seus dados Azure IoT Central num dashboard Power BI / Microsoft Docs
description: Utilize a Solução Power BI para Azure IoT Central para visualizar e analisar os seus dados IoT Central.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/4/2019
ms.topic: conceptual
ms.openlocfilehash: a484ab2553aeefbbe8c03ae639bdd667e4d9cb8e
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84661195"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Visualize e analise os seus dados Azure IoT Central num painel power BI

*Este tópico aplica-se aos administradores e desenvolvedores de soluções.*

:::image type="content" source="media/howto-connect-powerbi/iot-continuous-data-export.png" alt-text="Pipeline de solução de BI de potência":::

Utilize a Solução Power BI para Azure IoT Central para criar um poderoso painel power BI para monitorizar o desempenho dos seus dispositivos IoT. No seu painel Power BI, pode:

- Acompanhe a quantidade de dados que os seus dispositivos estão a enviar ao longo do tempo
- Comparar volumes de dados entre diferentes fluxos de telemetria
- Filtrar para baixo para os dados enviados por dispositivos específicos
- Ver os dados mais recentes da telemetria numa tabela

Esta solução configura um pipeline que lê dados da sua conta de armazenamento [Desporte Desporte Azure](howto-export-data-blob-storage.md) Blob. O oleoduto utiliza funções Azure, Azure Data Factory e Azure SQL Database para processar e transformar os dados. pode visualizar e analisar os dados num relatório Power BI que descarrega como um ficheiro PBIX. Todos os recursos são criados na sua subscrição Azure, para que possa personalizar cada componente de acordo com as suas necessidades.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste guia de como fazer, precisa de uma subscrição ativa do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

A configuração da solução requer os seguintes recursos:

- IoT Central. Para saber mais, consulte [Criar uma aplicação Azure IoT Central](./quick-deploy-iot-central.md).
- Exportação contínua de dados configurado para exportar telemetria, dispositivos e modelos de dispositivo para o armazenamento de Azure Blob. Para saber mais, consulte [Como exportar dados de IoT para destinos em Azure.](howto-export-data.md)
  - Certifique-se de que apenas a sua aplicação IoT Central está a exportar dados para o recipiente blob.
  - Os seus [dispositivos devem enviar mensagens codificadas JSON](../../iot-hub/iot-hub-devguide-messages-d2c.md). Os dispositivos devem especificar `contentType:application/JSON` e ou ou ou nas propriedades do sistema de `contentEncoding:utf-8` `contentEncoding:utf-16` `contentEncoding:utf-32` mensagens.
- Power BI Desktop (versão mais recente). Ver [downloads de Power BI](https://powerbi.microsoft.com/downloads/).
- Power BI Pro (se quiser partilhar o painel com outros).

## <a name="install"></a>Instalar

Para configurar o pipeline, navegue para a solução Power BI para a página [Azure IoT Central](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) no site da **Microsoft AppSource.** **Selecione Obter agora**, e siga as instruções.

Quando abrir o ficheiro PBIX, certifique-se de que lê e segue as instruções na página de identificação. Estas instruções descrevem como ligar o seu relatório à sua base de dados SQL.

## <a name="report"></a>Relatório

O ficheiro PBIX contém os dispositivos e o relatório **de telemetria** mostra uma visão histórica da telemetria que foi enviada por dispositivos. Fornece uma desagregação dos diferentes tipos de telemetria, e também mostra a telemetria mais recente enviada por dispositivos.

:::image type="content" source="media/howto-connect-powerbi/report.png" alt-text="Power BI Devices e Relatório de Telemetria":::

## <a name="pipeline-resources"></a>Recursos de gasodutos

Você pode aceder a todos os recursos Azure que compõem o oleoduto no portal Azure. Todos os recursos estão no grupo de recursos que criaste quando criaste o oleoduto.

:::image type="content" source="media/howto-connect-powerbi/azure-deployment.png" alt-text="Vista do portal Azure do grupo de recursos":::

A lista que se segue descreve o papel de cada recurso no gasoduto:

### <a name="azure-functions"></a>Funções do Azure

A aplicação Azure Function dispara cada vez que a IoT Central escreve um novo ficheiro para o armazenamento blob. As funções extraem dados da telemetria, dispositivos e modelos de dispositivos para povoar as tabelas SQL intermédias que a Azure Data Factory utiliza.

### <a name="azure-data-factory"></a>Azure Data Factory

A Azure Data Factory conecta-se à base de dados SQL como um serviço ligado. Executa procedimentos armazenados para processar os dados e armazená-lo nas tabelas de análise.

A Azure Data Factory funciona a cada 15 minutos para transformar o último lote de dados para carregar nas tabelas SQL (que é o número mínimo atual para o Gatilho da **janela ).**

### <a name="azure-sql-database"></a>Base de Dados SQL do Azure

A Azure Data Factory gera um conjunto de tabelas de análise para Power BI. Você pode explorar estes esquemas no Power BI e usá-los para construir suas próprias visualizações.

## <a name="estimated-costs"></a>Custos estimados

A solução Power BI para a página [Central Azure IoT](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) no site da Microsoft AppSource inclui um link para um estimador de custos para os recursos que implementa.

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu a visualizar os seus dados no Power BI, o próximo passo sugerido é aprender [a gerir dispositivos.](howto-manage-devices.md)
