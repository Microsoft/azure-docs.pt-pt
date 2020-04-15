---
title: Adicione azulejos ao seu painel de instrumentos [ Microsoft Docs
description: Como construtor, aprenda a configurar o painel de aplicações central do Azure IoT.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f75e5a28f7ec56750432e74ee48ba68491a5e481
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310290"
---
# <a name="configure-the-application-dashboard"></a>Configure o painel de aplicações

O **Dashboard** é a página que carrega quando os utilizadores que têm acesso à aplicação navegam para o URL da aplicação. Se criou a sua aplicação a partir de um dos Modelos de **Aplicação,** a sua aplicação terá um dashboard pré-definido para iniciar. Se criou a sua aplicação a partir do modelo de **aplicação** Legacy, o seu dashboard ficará em branco para começar.

> [!NOTE]
> Os utilizadores podem [criar vários dashboards](howto-create-personal-dashboards.md) para além do dashboard de aplicação predefinido. Estes dashboards podem ser pessoais apenas para o utilizador, ou partilhados em todos os utilizadores da aplicação. 

## <a name="add-tiles"></a>Adicionar mosaico

A imagem seguinte mostra o dashboard numa aplicação criada a partir do modelo de **aplicação personalizada.** Para personalizar o dashboard predefinido para a sua aplicação, selecione **Editar** no topo esquerdo da página.

> [!div class="mx-imgBorder"]
> ![Painel de instrumentos para aplicações com base no modelo "Sample Contoso"](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

Selecionar **Editar** abre o painel da biblioteca do painel de instrumentos. A biblioteca contém os azulejos e os primitivos do painel que pode usar para personalizar o painel de instrumentos.

> [!div class="mx-imgBorder"]
> ![Biblioteca do painel de instrumentos](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

Por exemplo, pode adicionar um azulejo **telemetria** para a temperatura atual do dispositivo. Para tal:
1. Selecione um **modelo de dispositivo**
1. Selecione uma Instância de **Dispositivo** para o dispositivo que pretende ver num azulejo do painel de instrumentos. Em seguida, você verá uma lista das propriedades do dispositivo que pode ser usada no azulejo.
1. Para criar o azulejo no painel de instrumentos, clique em **Temperatura** e arraste-o para a área do painel de instrumentos. Também pode clicar na caixa de verificação ao lado da **Temperatura** e clicar em **Combine**. A imagem seguinte mostra selecionando um modelo de dispositivo e instância de dispositivo, criando um azulejo de telemetria de temperatura no painel de instrumentos.
1. Selecione **Guardar** na parte superior esquerda para guardar o azulejo para o tablier.

> [!div class="mx-imgBorder"]
> ![Formulário "Configure Detalhes do Dispositivo" com detalhes para configurações e propriedades](media/howto-add-tiles-to-your-dashboard/device-details.png)

Agora, quando um operador vê o painel de aplicação predefinido, vê o novo azulejo com a **Temperatura** do dispositivo. Cada azulejo tem um gráfico pré-selecionado, gráfico, etc. que será exibido quando o azulejo for criado. No entanto, os utilizadores podem optar por editar e alterar esta visualização. 

> [!div class="mx-imgBorder"]
> ![Separador "Dashboard" com definições e propriedades visualizadas para o azulejo](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>Editar azulejos

Para editar um azulejo no painel de instrumentos, clique primeiro **Editar** na parte superior esquerda da página, que abrirá o modo de edição para o painel de instrumentos e todos os seus azulejos. 

> [!div class="mx-imgBorder"]
> ![Ecrã do painel de instrumentos com modo de edição ativado para um azulejo selecionado](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

Em seguida, clique no ícone **Gear** no canto superior direito do azulejo que deseja editar. Aqui pode editar aspetos do azulejo, incluindo o seu título, a sua visualização, agregação, etc.

> [!div class="mx-imgBorder"]
> ![Dropdown para definições de agregação de azulejos](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

Também pode alterar a visualização do gráfico clicando no ícone **Régua** no azulejo.

> [!div class="mx-imgBorder"]
> ![Dropdown para definições de visualização de azulejos](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>Tipos de azulejos

A tabela seguinte resume a utilização de azulejos em Azure IoT Central:
 
| Mosaico | Dashboard | Descrição
| ----------- | ------- | ------- |
| Conteúdo | Instrumentos de aplicação e conjunto de dispositivos |Os azulejos suportados por marcação são azulejos clicáveis que exibem texto de cabeça e descrição. Também pode utilizar este azulejo como um azulejo de ligação para permitir que um utilizador navegue para um URL relacionado com a sua aplicação.|
| Imagem | Instrumentos de aplicação e conjunto de dispositivos |Os azulejos de imagem exibem uma imagem personalizada e podem ser clicáveis. Utilize um azulejo de imagem para adicionar gráficos a um dashboard e opcionalmente permitir que um utilizador navegue num URL relevante para a sua aplicação.|
| Etiqueta | Dashboards de aplicação |Os azulejos de etiqueta exibem texto personalizado num painel de instrumentos. Pode escolher o tamanho do texto. Utilize um azulejo de etiqueta para adicionar informações relevantes ao painel de instrumentos tais descrições, detalhes de contacto ou ajuda.|
| Mapa | Instrumentos de aplicação e dispositivo |Os azulejos do mapa exibem a localização de um dispositivo num mapa. Também pode exibir até 100 pontos do histórico de localização de um dispositivo. Por exemplo, pode ser apresentada uma rota de exibição de onde um dispositivo esteve na última semana.|
| Gráfico de linha | Instrumentos de aplicação e dispositivo |Os azulejos da linha apresentam um gráfico de medição agregada para um dispositivo durante um período de tempo. Por exemplo, pode apresentar um gráfico de linha que mostre a temperatura média e a pressão de um dispositivo durante a última hora.|
| gráfico de barras | Instrumentos de aplicação e dispositivo |Os azulejos da tabela de barras apresentam um gráfico de medições agregadas para um dispositivo durante um período de tempo. Por exemplo, pode apresentar um gráfico de barras que mostre a temperatura média e a pressão de um dispositivo durante a última hora.|
| Gráfico de torta | Instrumentos de aplicação e conjunto de dispositivos |Os azulejos da carta de tarte apresentam um gráfico de medições agregadas para um dispositivo durante um período de tempo.|
| Mapa Térmico | Instrumentos de aplicação e conjunto de dispositivos |Os azulejos do Mapa de Calor exibem informações sobre o conjunto do dispositivo, representados como cores.|
| História do Evento | Instrumentos de aplicação e dispositivo |Os azulejos do Event History exibem os eventos para um dispositivo durante um período de tempo. Por exemplo, pode usá-lo para mostrar todas as alterações de temperatura de um dispositivo durante a última hora.|
| História do Estado | Instrumentos de aplicação e dispositivo |Os azulejos do estado apresentam os valores de medição por um período de tempo. Por exemplo, pode usá-lo para mostrar os valores de temperatura de um dispositivo durante a última hora.|
| KPI | Instrumentos de aplicação e dispositivo | Os azulejos KPI apresentam uma telemetria agregada ou medição de eventos por um período de tempo. Por exemplo, pode usá-lo para mostrar a temperatura máxima alcançada para um dispositivo durante a última hora.|
| Último Valor Conhecido | Instrumentos de aplicação e dispositivo |Os últimos azulejos de valor conhecidos apresentam o valor mais recente para uma medição de telemetria ou estado. Por exemplo, pode utilizar este azulejo para apresentar as medições mais recentes de temperatura, pressão e humidade para um dispositivo.|

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a configurar o seu painel de aplicações padrão Azure IoT Central, pode [aprender a criar um dashboard pessoal](howto-create-personal-dashboards.md).
