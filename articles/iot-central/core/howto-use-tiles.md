---
title: Como usar blocos no painel do aplicativo IoT Central do Azure | Microsoft Docs
description: Como um construtor, saiba como usar blocos em painéis de aplicativos, painéis de conjunto de dispositivos e painéis de dispositivos.
author: v-krghan
ms.author: v-krghan
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0803258c362279049a49a7eee00e7a4763621672
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952631"
---
# <a name="how-to-use-tiles"></a>Como usar blocos
Você pode usar blocos para personalizar painéis de aplicativo, painéis de dispositivos e painéis de conjunto de dispositivos. Você pode adicionar vários blocos a um painel e personalizar esses blocos para mostrar informações relevantes ao seu aplicativo. Você também pode redimensionar blocos e personalizar o layout em qualquer painel. A captura de tela abaixo mostra o painel do aplicativo com blocos diferentes.

![Dashboard de aplicações](media/howto-use-tiles/image1a.png)


A tabela a seguir resume o uso de blocos no Azure IoT Central:

 
| Bloco | Dashboard | Descrição
| ----------- | ------- | ------- |
| Ligação | Painéis de conjunto de aplicativos e dispositivos |Os blocos de link são blocos clicáveis que exibem o texto de título e descrição. Use um bloco de link para permitir que um usuário navegue para uma URL relacionada ao seu aplicativo. |
| Imagem | Painéis de conjunto de aplicativos e dispositivos |Os blocos de imagem exibem uma imagem personalizada e podem ser clicáveis. Use um bloco de imagem para adicionar elementos gráficos a um painel e, opcionalmente, permitir que um usuário navegue até uma URL relevante para seu aplicativo.|
| Label | Painéis de aplicativo |Blocos de rótulo exibem texto personalizado em um painel. Você pode escolher o tamanho do texto. Use um bloco de rótulo para adicionar informações relevantes ao painel, tais descrições, detalhes de contato ou ajuda|
| Mapa | Painéis de conjunto de aplicativos e dispositivos |Os blocos de mapa exibem o local e o estado de um dispositivo em um mapa. Por exemplo, você pode exibir onde está um dispositivo e se o ventilador está ligado.|
| Gráfico de linhas | Painéis de aplicativo e dispositivo |Os blocos de gráfico de linhas exibem um gráfico de medida de agregação para um dispositivo por um período de tempo. Por exemplo, você pode exibir um gráfico de linhas que mostra a temperatura média e a pressão em um dispositivo na última hora|
| Gráfico de barras | Painéis de aplicativo e dispositivo |Os blocos de gráfico de barras exibem um gráfico de medidas agregadas para um dispositivo por um período de tempo. Por exemplo, você pode exibir um gráfico de barras que mostra a temperatura média e a pressão em um dispositivo na última hora |
| Histórico de eventos | Painéis de aplicativo e dispositivo |Bloco do histórico de eventos exibe os eventos de um dispositivo durante um período de tempo. Por exemplo, você pode usá-lo para mostrar todas as alterações de temperatura ocorridas para um dispositivo durante a última hora. |
| Histórico de estado | Painéis de aplicativo e dispositivo |Bloco do histórico de estado exibe os valores de medida para um período de tempo. Por exemplo, você pode usá-lo para mostrar os valores de temperatura de um dispositivo durante a última hora.|
| KPI | Painéis de aplicativo e dispositivo | Bloco de KPI exibe uma telemetria agregada ou medida de evento para um período de tempo. Por exemplo, você pode usá-lo para mostrar a temperatura máxima atingida para um dispositivo durante a última hora.|
| Último valor conhecido | Painéis de aplicativo e dispositivo |O último bloco de valor conhecido exibe o valor mais recente para uma medição de telemetria ou estado. Por exemplo, você pode usar esse bloco para exibir as medidas mais recentes de temperatura, pressão e umidade de um dispositivo.|
| Grade de conjunto de dispositivos | Painéis de conjunto de aplicativos e dispositivos | A grade conjunto de dispositivos exibe informações sobre o conjunto de dispositivos. Use um bloco de grade de conjunto de dispositivos para mostrar informações como nome, local e modelo de todos os dispositivos no conjunto de dispositivos.|


Para saber mais sobre como configurar o painel em seu aplicativo de IoT Central do Azure, confira [Adicionar blocos ao seu painel](./howto-add-tiles-to-your-dashboard.md).
