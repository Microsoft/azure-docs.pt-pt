---
title: Adicione azulejos ao seu painel Central Azure IoT [ Microsoft Docs
description: Como construtor, aprenda a configurar o painel de aplicações central do Azure IoT com azulejos.
author: Haley-Rowland
ms.author: harowl
ms.date: 05/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 892bdcc08bd19b92c8b3d32d2954583f80005e87
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84022901"
---
# <a name="configure-the-application-dashboard"></a>Configure o painel de aplicações

O **Dashboard** é a primeira página que se vê quando se conecta a uma aplicação IoT Central. Se criar a sua aplicação a partir de um dos modelos de [aplicação focados](./concepts-app-templates.md)na indústria, a sua aplicação tem um dashboard pré-definido para iniciar. Se criar a sua aplicação a partir de um modelo de [aplicação](./concepts-app-templates.md)personalizado, o seu painel de instrumentos mostra algumas dicas para começar.

> [!TIP]
> Os utilizadores podem [criar vários dashboards](howto-create-personal-dashboards.md) para além do dashboard de aplicação predefinido. Estes dashboards podem ser pessoais apenas para o utilizador, ou partilhados em todos os utilizadores da aplicação.  

## <a name="add-tiles"></a>Adicionar mosaico

A imagem seguinte mostra o dashboard numa aplicação criada a partir do modelo de **aplicação Personalizado.** Para personalizar o painel de instrumentos atual, **selecione Editar,** para adicionar um dashboard pessoal ou partilhado personalizado, selecione **New**:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png" alt-text="Painel de instrumentos para aplicações com base no modelo de aplicação personalizado":::

Depois de selecionar **Editar** ou **Novo,** o painel de instrumentos encontra-se no modo *de edição.* Pode utilizar as ferramentas no painel **editdashboard** para adicionar azulejos ao painel de instrumentos e personalizar e remover azulejos no próprio painel de instrumentos. Por exemplo, adicionar um azulejo **telemetria** para mostrar a temperatura atual reportada por um ou mais dispositivos:

1. No painel editar painel de **painéis,** selecione um **grupo dispositivo**.
1. Selecione um ou mais dispositivos no dropdown dos **Dispositivos** para mostrar no azulejo. Agora vê a telemetria disponível, propriedades e comandos dos dispositivos.
1. Selecione **Temperatura** na secção de telemetria e, em seguida, selecione **Adicionar azulejos**. O azulejo agora mostra no painel de instrumentos onde pode alterar a visualização, redimensionar o azulejo e configurá-lo:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/device-details.png" alt-text="Adicione um azulejo de telemetria de temperatura ao tablier":::

Quando terminar de adicionar e personalizar azulejos no painel de instrumentos, selecione **Save**.

## <a name="customize-tiles"></a>Personalizar azulejos

Para personalizar um azulejo no painel de instrumentos, o painel deve estar em modo de edição. As opções de personalização disponíveis dependem do tipo de [azulejo:](#tile-types)

* O ícone da régua num azulejo permite-lhe alterar a visualização. As visualizações incluem gráficos de linha, valores conhecidos e mapas de calor.

* O ícone quadrado permite-lhe redimensionar o azulejo.

* O ícone do equipamento permite configurar a visualização. Por exemplo, para uma visualização de gráficos de linha pode escolher mostrar a lenda e os eixos, e escolher o intervalo de tempo para traçar.

## <a name="tile-types"></a>Tipos de azulejos

A tabela seguinte descreve os diferentes tipos de azulejos que pode adicionar a um painel de instrumentos:

| Mosaico             | Descrição |
| ---------------- | ----------- |
| Markdown         | Os azulejos de marcação são azulejos clicáveis que exibem um texto de título e descrição formatado com marcação. O URL pode ser uma ligação relativa a outra página da aplicação, ou um link absoluto para um site externo.|
| Imagem            | Os azulejos de imagem exibem uma imagem personalizada e podem ser clicáveis. O URL pode ser uma ligação relativa a outra página da aplicação, ou um link absoluto para um site externo.|
| Etiqueta            | Os azulejos de etiqueta exibem texto personalizado num painel de instrumentos. Pode escolher o tamanho do texto. Utilize um azulejo de etiqueta para adicionar informações relevantes ao painel de instrumentos tais descrições, detalhes de contacto ou ajuda.|
| Contagem            | Os azulejos de contagem exibem o número de dispositivos num grupo de dispositivos.|
| Mapa              | Os azulejos do mapa exibem a localização de um ou mais dispositivos num mapa. Também pode exibir até 100 pontos do histórico de localização de um dispositivo. Por exemplo, pode apresentar uma rota amostrada de onde um dispositivo esteve na última semana.|
| KPI              |  Os azulejos KPI apresentam valores agregados de telemetria para um ou mais dispositivos durante um período de tempo. Por exemplo, pode usá-lo para mostrar a temperatura máxima e a pressão alcançada saem de um ou mais dispositivos durante a última hora.|
| Gráfico de linhas       | Os azulejos da linha traçam um ou mais valores agregados de telemetria para um ou mais dispositivos durante um período de tempo. Por exemplo, pode apresentar um gráfico de linha para traçar a temperatura média e a pressão de um ou mais dispositivos durante a última hora.|
| Gráfico de barras        | Os azulejos da carta de barras traçam um ou mais valores agregados de telemetria para um ou mais dispositivos durante um período de tempo. Por exemplo, pode apresentar um gráfico de barras para mostrar a temperatura média e a pressão de um ou mais dispositivos durante a última hora.|
| Gráfico circular        | Os azulejos da carta de tarte apresentam um ou mais valores agregados de telemetria para um ou mais dispositivos durante um período de tempo.|
| Mapa de calor         | Os azulejos do mapa de calor exibem informações sobre um ou mais dispositivos, representados como cores.|
| Último Valor Conhecido | Os últimos azulejos de valor conhecidos exibem os mais recentes valores de telemetria para um ou mais dispositivos. Por exemplo, pode utilizar este azulejo para exibir os valores mais recentes de temperatura, pressão e humidade para um ou mais dispositivos. |
| História do Evento    | Os azulejos do Event History exibem os eventos para um dispositivo durante um período de tempo. Por exemplo, pode usá-lo para mostrar todos os eventos abertos e fechados da válvula para um ou mais dispositivos durante a última hora.|
| Propriedade         |  Os azulejos da propriedade exibem o valor atual para propriedades e propriedades em nuvem de um ou mais dispositivos. Por exemplo, pode utilizar este azulejo para exibir propriedades do dispositivo, como o fabricante ou a versão firmware para um dispositivo. |

Atualmente, pode adicionar até 10 dispositivos a azulejos que suportam vários dispositivos.

### <a name="customizing-visualizations"></a>Personalização de visualizações

Para azulejos que apresentem valores agregados, selecione o ícone de engrenagem ao lado do tipo de telemetria no painel **de gráficos Configure** para escolher a agregação. Pode escolher entre a média, soma, máxima, mínima e contagem.

Para gráficos de linhas, gráficos de barras e gráficos de tartes, você pode personalizar a cor dos diferentes valores de telemetria. Selecione o ícone da paleta ao lado da telemetria que pretende personalizar:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/color-customization.png" alt-text="Mude a cor de um valor de telemetria":::

Para azulejos que mostrem propriedades de cordas ou valores de telemetria, pode escolher como exibir o texto. Por exemplo, se o dispositivo armazenar um URL numa propriedade de cordas, pode exibi-lo como um link clicável. Se o URL referenciar uma imagem, pode rendera a imagem num último valor conhecido ou azulejo de propriedade. Para alterar a forma como uma corda se exibe, na configuração do azulejo selecione o ícone de engrenagem ao lado do tipo ou propriedade da telemetria:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/string-customization.png" alt-text="Chane como uma corda exibe em um azulejo":::

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu a configurar o seu painel de aplicações padrão Azure IoT Central, pode [aprender a criar um dashboard pessoal](howto-create-personal-dashboards.md).
