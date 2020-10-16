---
title: Adicione azulejos ao seu painel Azure IoT Central / Microsoft Docs
description: Como construtor, aprenda a configurar o painel de aplicações Azure IoT Central padrão com azulejos.
author: Haley-Rowland
ms.author: harowl
ms.date: 05/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: beeb771ea5053dd0ad867a7568aa64bbb2d0b4ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985275"
---
# <a name="configure-the-application-dashboard"></a>Configure o painel de aplicações

O **Dashboard** é a primeira página que vê quando se liga a uma aplicação IoT Central. Se criar a sua aplicação a partir de um dos modelos de [aplicação focados](./concepts-app-templates.md)na indústria, a sua aplicação tem um dashboard pré-definido para começar. Se criar a sua aplicação a partir de um modelo de [aplicação](./concepts-app-templates.md)personalizado, o seu dashboard mostra algumas dicas para começar.

> [!TIP]
> Os utilizadores podem [criar vários dashboards](howto-create-personal-dashboards.md) para além do painel de aplicações predefinido. Estes dashboards podem ser pessoais apenas para o utilizador, ou partilhados em todos os utilizadores da aplicação.  

## <a name="add-tiles"></a>Adicionar mosaico

A imagem que se segue mostra o painel de instrumentos numa aplicação criada a partir do modelo de **aplicação Personalizado.** Para personalizar o painel de instrumentos atual, **selecione Editar,** para adicionar um dashboard personalizado pessoal ou partilhado, selecione **New**:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png" alt-text="Painel de aplicações com base no modelo de aplicação personalizado":::

Depois de selecionar **Editar** ou **Novo,** o painel de instrumentos está no modo *de edição.* Pode utilizar as ferramentas no painel do painel do **painel do painel editar** para adicionar azulejos ao tablier e personalizar e remover os azulejos no próprio painel de instrumentos. Por exemplo, para adicionar um azulejo de **telemetria** para mostrar a temperatura atual reportada por um ou mais dispositivos:

1. No painel do **painel do painel do painel editar,** selecione um grupo de **dispositivos**.
1. Selecione um ou mais dispositivos no **dropdown dos Dispositivos** para mostrar no azulejo. Agora vê a telemetria, propriedades e comandos disponíveis dos dispositivos.
1. Selecione **Temperatura** na secção de telemetria e, em seguida, selecione **Adicionar azulejo**. O azulejo agora mostra no painel de instrumentos onde pode alterar a visualização, redimensionar o azulejo e configurá-lo:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/device-details.png" alt-text="Painel de aplicações com base no modelo de aplicação personalizado":::

Quando terminar de adicionar e personalizar azulejos no painel de instrumentos, **selecione Save**.

## <a name="customize-tiles"></a>Personalizar azulejos

Para personalizar um azulejo no tablier, o painel de instrumentos deve estar no modo de edição. As opções de personalização disponíveis dependem do [tipo de azulejo:](#tile-types)

* O ícone da régua num azulejo permite-lhe alterar a visualização. As visualizações incluem gráficos de linha, valores conhecidos e mapas de calor.

* O ícone quadrado permite-lhe redimensionar o azulejo.

* O ícone de engrenagem permite-lhe configurar a visualização. Por exemplo, para uma visualização de gráficos de linha, pode optar por mostrar a lenda e os eixos e escolher o intervalo de tempo para traçar.

## <a name="tile-types"></a>Tipos de azulejos

A tabela a seguir descreve os diferentes tipos de azulejos que pode adicionar a um dashboard:

| Mosaico             | Descrição |
| ---------------- | ----------- |
| Markdown         | Os azulejos de marcação são azulejos clicáveis que exibem um texto de posição e descrição formatado com marcação. O URL pode ser um link relativo para outra página na aplicação, ou um link absoluto para um site externo.|
| Imagem            | Os azulejos de imagem exibem uma imagem personalizada e podem ser clicáveis. O URL pode ser um link relativo para outra página na aplicação, ou um link absoluto para um site externo.|
| Etiqueta            | Os azulejos da etiqueta exibem texto personalizado num painel de instrumentos. Pode escolher o tamanho do texto. Utilize um azulejo de etiqueta para adicionar informações relevantes ao painel de instrumentos tais descrições, detalhes de contacto ou ajuda.|
| Contagem            | Os azulejos de contagem mostram o número de dispositivos num grupo de dispositivos.|
| Mapa              | Os azulejos do mapa exibem a localização de um ou mais dispositivos num mapa. Também pode exibir até 100 pontos do histórico de localização de um dispositivo. Por exemplo, pode apresentar a rota amostrada de onde um dispositivo esteve na última semana.|
| KPI              |  Os azulejos KPI apresentam valores de telemetria agregados para um ou mais dispositivos durante um período de tempo. Por exemplo, pode usá-lo para mostrar a temperatura máxima e a pressão alcançada para um ou mais dispositivos durante a última hora.|
| Gráfico de linhas       | Os azulejos de gráfico de linha traçam um ou mais valores de telemetria agregados para um ou mais dispositivos durante um período de tempo. Por exemplo, pode apresentar um gráfico de linha para traçar a temperatura média e a pressão de um ou mais dispositivos durante a última hora.|
| Gráfico de barras        | Os azulejos de gráfico de barras traçam um ou mais valores de telemetria agregados para um ou mais dispositivos durante um período de tempo. Por exemplo, pode apresentar um gráfico de barras para mostrar a temperatura média e a pressão de um ou mais dispositivos durante a última hora.|
| Gráfico circular        | Os azulejos da tabela de tortas apresentam um ou mais valores de telemetria agregados para um ou mais dispositivos durante um período de tempo.|
| Mapa térmico         | Os azulejos do mapa de calor exibem informações sobre um ou mais dispositivos, representados como cores.|
| Último valor conhecido | Os últimos azulejos de valor conhecidos exibem os valores mais recentes de telemetria para um ou mais dispositivos. Por exemplo, pode utilizar este azulejo para mostrar os valores de temperatura, pressão e humidade mais recentes para um ou mais dispositivos. |
| História do Evento    | Os azulejos de História do Evento exibem os eventos para um dispositivo durante um período de tempo. Por exemplo, pode usá-lo para mostrar todos os eventos abertos e fechados da válvula para um ou mais dispositivos durante a última hora.|
| Propriedade         |  Os azulejos de propriedade exibem o valor atual para propriedades e propriedades em nuvem de um ou mais dispositivos. Por exemplo, pode utilizar este azulejo para exibir propriedades do dispositivo, como o fabricante ou a versão firmware para um dispositivo. |

Atualmente, pode adicionar até 10 dispositivos a azulejos que suportam vários dispositivos.

### <a name="customizing-visualizations"></a>Personalização de visualizações

Para os azulejos que exibem valores agregados, selecione o ícone de engrenagem ao lado do tipo de telemetria no painel **de gráficos configurar** para escolher a agregação. Pode escolher entre a média, a soma, o máximo, o mínimo e a contagem.

Para gráficos de linha, gráficos de barras e gráficos de tortas, você pode personalizar a cor dos diferentes valores de telemetria. Selecione o ícone da paleta ao lado da telemetria que pretende personalizar:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/color-customization.png" alt-text="Painel de aplicações com base no modelo de aplicação personalizado":::

Para azulejos que apresentem propriedades de cordas ou valores de telemetria, pode escolher como exibir o texto. Por exemplo, se o dispositivo armazenar um URL numa propriedade de cadeia, pode exibi-lo como um link clicável. Se o URL referenciar uma imagem, pode tornar a imagem num último valor ou azulejo de propriedade conhecido. Para alterar a forma como uma corda aparece, na configuração do azulejo selecione o ícone de engrenagem ao lado do tipo de telemetria ou propriedade:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/string-customization.png" alt-text="Painel de aplicações com base no modelo de aplicação personalizado":::

Para **um KPI**numérico, **Último Valor Conhecido,** e azulejos **de propriedade** pode utilizar formatação condicional para personalizar a cor do azulejo com base no seu valor atual. Para adicionar formatação condicional, selecione **Configurar** no azulejo e, em seguida, selecione o ícone **de formatação condicional** ao lado do valor para personalizar:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-1.png" alt-text="Painel de aplicações com base no modelo de aplicação personalizado":::

Adicione as suas regras de formatação condicional:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-2.png" alt-text="Painel de aplicações com base no modelo de aplicação personalizado":::

A imagem que se segue mostra o efeito da regra de formatação condicional:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-3.png" alt-text="Painel de aplicações com base no modelo de aplicação personalizado":::

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a configurar o seu painel de aplicações Azure IoT Central, pode [aprender a criar um dashboard pessoal](howto-create-personal-dashboards.md).
