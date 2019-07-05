---
title: Como utilizar mosaicos no dashboard de aplicações do Azure IoT Central | Documentos da Microsoft
description: Como um construtor, saiba como utilizar mosaicos em dashboards de aplicações, dispositivos conjunto dashboards e dashboards do dispositivo.
author: v-krghan
ms.author: v-krghan
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 26322461e4f53bffff2fb182df6c45ed3b83c4bf
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503529"
---
# <a name="how-to-use-tiles"></a>Como utilizar mosaicos
Pode utilizar mosaicos para personalizar os dashboards de aplicações, dashboards de dispositivo, e dispositivos conjunto dashboards. Pode adicionar vários mosaicos a um dashboard e personalizar esses mosaicos para apresentar as informações relevantes ao aplicativo. Também pode redimensionar mosaicos e personalizar o esquema em qualquer dashboard. A captura de ecrã abaixo mostra o dashboard de aplicações com mosaicos diferentes.

![Dashboard de aplicações](media/howto-use-tiles/image1a.png)


A tabela seguinte resume a utilização de mosaicos no Azure IoT Central:

 
| mosaico | Dashboard | Descrição
| ----------- | ------- | ------- |
| Ligação | Aplicações e dispositivos conjunto dashboards |Mosaicos de ligação são mosaicos clicáveis que exibem texto do cabeçalho e descrição. Utilize um mosaico de ligação para permitir que um utilizador navegar para um URL que está relacionado com a sua aplicação. |
| Image | Aplicações e dispositivos conjunto dashboards |Mosaicos de imagem a apresentar uma imagem personalizada e podem ser clicáveis. Utilize um mosaico de imagem para adicionar elementos gráficos a um dashboard e, opcionalmente, ativar um utilizador navegar para um URL que é relevante para seu aplicativo.|
| Etiqueta | Dashboards de aplicações |Mosaicos de etiqueta exibem texto personalizado num dashboard. Pode escolher o tamanho do texto. Utilizar um mosaico de etiqueta para adicionar informações relevantes para o dashboard tal descrições, detalhes de contacto ou ajuda|
| Mapa | Aplicações e dispositivos conjunto dashboards |Mosaicos de mapa de apresentam a localização e o estado de um dispositivo num mapa. Por exemplo, pode exibir em que se um dispositivo e se a ventoinha está ativada.|
| Gráfico de linhas | Dashboards de aplicativos e dispositivos |Mosaicos de gráfico de linha apresentam um gráfico de medida de agregação para um dispositivo durante um período de tempo. Por exemplo, pode exibir um gráfico de linhas que mostra a temperatura e a pressão num dispositivo para a última hora|
| Gráfico de barras | Dashboards de aplicativos e dispositivos |Mosaicos de gráfico de barras apresentam um gráfico de medidas de agregação para um dispositivo durante um período de tempo. Por exemplo, pode exibir um gráfico de barras que mostra a temperatura e a pressão num dispositivo para a última hora |
| Histórico de eventos | Dashboards de aplicativos e dispositivos |Mosaico de histórico de eventos apresenta os eventos para um dispositivo durante um período de tempo. Por exemplo, pode utilizá-lo para mostrar que todas as alterações de temperatura ocorreram para um dispositivo durante a última hora. |
| Histórico de estado | Dashboards de aplicativos e dispositivos |Mosaico de histórico de estado apresenta os valores de medida, durante um período de tempo. Por exemplo, pode utilizá-lo para mostrar os valores de temperatura para um dispositivo durante a última hora.|
| KPI | Dashboards de aplicativos e dispositivos | Mosaico KPI apresenta uma medida de eventos de telemetria ou agregação durante um período de tempo. Por exemplo, pode utilizá-lo para mostrar a temperatura máxima atingida para um dispositivo durante a última hora.|
| Último valor conhecido | Dashboards de aplicativos e dispositivos |Mosaico do último valor conhecido apresenta o valor mais recente para a medição de telemetria ou estado. Por exemplo, pode utilizar este mosaico para apresentar as mais recentes medidas de temperatura, a pressão e a umidade médias para um dispositivo.|
| Grelha de conjunto de dispositivos | Aplicações e dispositivos conjunto dashboards | Dispositivos conjunto grelha apresenta informações sobre o conjunto de dispositivos. Utilize um mosaico de grade dispositivo definido para mostrar informações como nome, a localização e o modelo de todos os dispositivos no conjunto de dispositivos.|


Para saber mais sobre como configurar o dashboard na sua aplicação do Azure IoT Central, consulte [dashboard de aplicações de configurar](howto-configure-homepage.md).
