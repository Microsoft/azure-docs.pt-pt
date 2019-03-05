---
title: Configurar um modelo de dispositivo num aplicativo do Azure IoT Central | Documentos da Microsoft
description: Saiba como configurar um modelo de dispositivo com medidas, configurações, propriedades, regras e um dashboard.
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 80c98d151c1b42b0c6106d4fa1af921bbb5ef55f
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57308402"
---
# <a name="set-up-a-device-template-new-ui-design"></a>Configurar um modelo de dispositivo (design de nova interface do Usuário)

Um modelo de dispositivo é um esquema que define as características e comportamentos de um tipo de dispositivo que se liga a uma aplicação do Azure IoT Central.

Por exemplo, um construtor pode criar um modelo de dispositivo para um fã de ligado de IoT com o r:

- Medida de telemetria de temperatura

- Medida de eventos de erro motor ventoinha

- Ventoinha medição do Estado de funcionamento

- Definição de velocidade de ventoinha

- Propriedade de localização

- Regras que enviar alertas

- Dashboard que lhe dá uma visão geral do dispositivo

Partir deste modelo de dispositivo, pode criar e ligar os dispositivos de ventoinha real com nomes como um operador **ventoinha-1** e **ventoinha-2**. Todos estes fãs tem medidas, configurações, propriedades, regras e um dashboard que os utilizadores da sua aplicação podem monitorizar e gerir.

> [!NOTE]
> Apenas os construtores e os administradores podem criar, editar e eliminar modelos de dispositivos. Qualquer utilizador pode criar dispositivos sobre o **Device Explorer** página a partir de modelos de dispositivos existentes.

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="create-a-device-template"></a>Criar um modelo de dispositivo

1. Navegue para o **modelos de dispositivos** página.

2. Para criar um modelo em branco, selecione **+** e, em seguida, introduza um nome, tal como **refrigerador** ao seu novo modelo de dispositivo. Em seguida, selecione **criar**:

   ![Página de detalhes do dispositivo com "Refrigerador" como o nome do modelo](./media/howto-set-up-template-experimental/devicedetailspage.png)

4. Agora, está na **detalhes do dispositivo** página para um novo modelo de dispositivo. IoT Central cria automaticamente um dispositivo simulado ao criar um modelo de dispositivo. Um dispositivo simulado permite testar o comportamento do seu aplicativo antes de ligar um dispositivo real.

As secções seguintes descrevem cada um dos separadores no **modelo de dispositivo** página.

## <a name="measurements"></a>Medições

Medidas são os dados provenientes do seu dispositivo. Pode adicionar várias medidas ao seu modelo de dispositivo de acordo com as capacidades do seu dispositivo.

- **Telemetria** medidas são os pontos de dados numéricos que recolhe o seu dispositivo ao longo do tempo. São representadas como um fluxo contínuo. Um exemplo é a temperatura.
- **Evento** medidas são os dados de ponto no tempo que representa algo de significância no dispositivo. Um nível de gravidade representa a importância de um evento. Um exemplo é um erro de motor de ventoinha.
- **Estado** medidas representam o estado do dispositivo ou de seus componentes durante um período de tempo. Por exemplo, um modo de ventoinha pode ser definido como tendo **operacional** e **parado** como dois Estados possíveis.

### <a name="create-a-telemetry-measurement"></a>Criar uma medida de telemetria

Para adicionar uma nova medida de telemetria, selecione **+ nova medida**, escolha **telemetria** como a medida de tipo e introduza os detalhes no formulário.

> [!NOTE]
> Os nomes de campo no modelo do dispositivo tem de corresponder os nomes de propriedade no código de dispositivo correspondente para que a medição de telemetria a apresentar na aplicação quando está ligado um dispositivo real. Fazer o mesmo ao configurar as definições de propriedades do dispositivo e comandos enquanto continua a definir o modelo de dispositivo nas seções a seguir.

Por exemplo, pode adicionar uma nova medida de telemetria de temperatura:

| Nome a Apresentar        | Nome do Campo    |  Unidades    | Mín.   |Máx.|
| --------------------| ------------- |-----------|-------|---|
| Temperatura         | temp          |  degC     |  0    |100|

![Formulário de "Criar telemetria" com detalhes para medidas de temperatura](./media/howto-set-up-template-experimental/measurementsform.png)

Depois de selecionar **salvar**, o **temperatura** medição aparece na lista de medidas. Em pouco tempo, verá a visualização dos dados de temperatura do dispositivo simulado.

> [!NOTE]
> O tipo de dados da medição de telemetria é uma vírgula flutuante ponto número.

### <a name="create-an-event-measurement"></a>Criar uma medida de eventos

Para adicionar uma nova medida de evento, selecione **+ nova medida** e selecione **eventos** como o tipo de medição. Introduza os detalhes o **Create Event** formulário.

Forneça o **nome a apresentar**, **nome do campo**, e **gravidade** detalhes do evento. Pode escolher entre três níveis de gravidade disponíveis: **Erro**, **aviso**, e **informações**.

Por exemplo, pode adicionar um novo **erro de Motor de ventoinha** eventos.

| Nome a Apresentar        | Nome do Campo    |  Gravidade Predefinida |
| --------------------| ------------- |-----------|
| Erro de Motor da Ventoinha     | fanmotorerror |  Erro    |

![Formulário de "Criar eventos" com detalhes para um evento de motor de ventoinha](./media/howto-set-up-template-experimental/eventmeasurementsform.png)

Depois de selecionar **salvar**, o **ventoinha erro de Motor** medição aparece na lista de medidas. Em pouco tempo, verá a visualização dos dados do evento do dispositivo simulado.

Para ver mais detalhes sobre um evento, selecione o ícone de eventos no gráfico:

![Detalhes do evento de "Erro de Motor de ventoinha"](./media/howto-set-up-template-experimental/eventmeasurementsdetail.png)

> [!NOTE]
> O tipo de dados da medição de eventos é a cadeia de caracteres.

### <a name="create-a-state-measurement"></a>Criar uma medida de estado

Para adicionar uma nova medida de estado, selecione o **+ nova medida** e selecione **estado** como o tipo de medição. Introduza os detalhes o **estado de criar** formulário.

Forneça os detalhes para **nome a apresentar**, **nome do campo**, e **valores** do Estado. Cada valor também pode ter um nome a apresentar que será utilizado quando o valor é apresentado em gráficos e tabelas.

Por exemplo, pode adicionar um novo **ventoinha modo** estado de que tem dois valores possíveis que o dispositivo pode enviar, **operacional** e **parado**.

| Nome a Apresentar | Nome do Campo    |  Valor 1   | Nome a Apresentar | Valor 2    |Nome a Apresentar  | 
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Modo da Ventoinha     | fanmode       |  1         | Em funcionamento    |     0      | Parada      |

![Formulário de "Estado de edição" com detalhes para o modo de ventoinha](./media/howto-set-up-template-experimental/statemeasurementsform.png)

Depois de selecionar **salvar**, o **modo ventoinha** medição do estado é apresentado na lista de medidas. Ver uns breves instantes, a visualização dos dados de estado do dispositivo simulado.

Se o dispositivo envia demasiados pontos de dados num pequeno período de tempo, a medição de estado é apresentado com um elemento visual diferente. Selecione o gráfico para ver todos os pontos de dados dentro desse período de tempo por ordem cronológica. Também pode reduzir o intervalo de tempo para ver a medição plotada no gráfico.

> [!NOTE]
> O tipo de dados da medição de estado é a cadeia de caracteres.

## <a name="settings"></a>Definições

Definições de controlarem um dispositivo. Eles permitem aos operadores forneça entradas para o dispositivo. Pode adicionar várias definições ao seu modelo de dispositivo que são apresentados como mosaicos no **definições** separador para operadores para utilizar. Pode adicionar vários tipos de definições: número, texto, data, alternar, lista de seleção e rótulo de seção.

Elas podem estar em um de três Estados. O dispositivo comunica esses Estados.

- **Sincronizado**: O dispositivo foi alterado para refletir o valor de definição.

- **Pendente**: O dispositivo atualmente está mudando para o valor de definição.

- **Erro**: O dispositivo devolveu um erro.

Por exemplo, pode adicionar uma nova definição de velocidade de ventoinha selecionando **configurações** e introduzir no novo **número** definição:

| Nome a Apresentar  | Nome do Campo    |  Unidades  | Casas decimais |Inicial|
| --------------| ------------- |---------| ---------|---- |
| Ventoinha velocidade     | fanSpeed      | RPM     | 2        | 0   |

![Formulário de "Configurar o número" com detalhes para as definições de velocidade](./media/howto-set-up-template-experimental/settingsform.png)

Depois de selecionar **salvar**, o **ventoinha velocidade** definição aparece como um mosaico. Um operador pode utilizar a definição no **Device Explorer** página para alterar a velocidade de fã do dispositivo.

## <a name="properties"></a>Propriedades

Propriedades são metadados associada ao dispositivo, tal como a localização do dispositivo e o número de série. Adicionar várias propriedades ao seu modelo de dispositivo que são apresentados como mosaicos no **propriedades** separador. Uma propriedade pode ter um tipo como o número, texto, data, alternar, propriedade do dispositivo, etiqueta ou localização. Um operador pode especificar os valores de propriedades quando criam um dispositivo e podem editar estes valores em qualquer altura. Propriedades do dispositivo são só de leitura e são enviadas do dispositivo para a aplicação. Um operador não é possível alterar propriedades do dispositivo. Quando se liga um dispositivo real, o mosaico de propriedade do dispositivo é de atualizações no aplicativo.

Existem duas categorias de propriedades:

- _Propriedades do dispositivo_ que o dispositivo comunica-se para a aplicação do Centro de IoT. Propriedades do dispositivo são só de leitura valores comunicados pelo dispositivo e são atualizadas no aplicativo quando estiver ligado um dispositivo real.
- _Propriedades da aplicação_ que são armazenados na aplicação e pode ser editada pela operadora de rede. O dispositivo não reconhece as propriedades da aplicação.

Por exemplo, pode adicionar a última data de serviço para o dispositivo como uma nova **data** propriedade (uma propriedade de aplicação) no **propriedades** separador:

| Nome a Apresentar  | Nome do Campo | Valor Inicial   |
| --------------| -----------|-----------------|
| Última assistência      | lastServiced        | 01/29/2019     |

![Formulário de "Configurar Serviced último" na guia "Propriedades"](./media/howto-set-up-template-experimental/propertiesform.png)

Depois de selecionar **guardar**, a última servidos data para o dispositivo é apresentado como um mosaico.

Depois de criar o mosaico, pode alterar o valor da propriedade no aplicativo a **Device Explorer**.

### <a name="create-a-location-property-through-azure-maps"></a>Criar uma propriedade de localização por meio do Azure Maps

Pode dar contexto geográfico aos seus dados de localização no Azure IoT Central e mapear qualquer coordenadas de latitude e longitude de uma morada. Ou pode mapear a latitude e longitude coordenadas. Mapas do Azure permite que esta capacidade no Centro de IoT.

Pode adicionar dois tipos de propriedades de localização:

- **Localização como uma propriedade de aplicativo**, que é armazenado no aplicativo. O dispositivo não reconhece as propriedades da aplicação.
- **Localização como uma propriedade de dispositivo**, que o dispositivo de relatórios para a aplicação.

#### <a name="add-location-as-an-application-property"></a>Adicionar localização como uma propriedade de aplicação

Pode criar uma propriedade de localização como uma propriedade de aplicação com o Azure Maps na sua aplicação IoT Central. Por exemplo, pode adicionar o endereço de instalação de dispositivo:

1. Navegue para o **propriedades** separador.

2. Na biblioteca, selecione **localização**.

3. Configurar **nome a apresentar**, **nome do campo**e (opcionalmente) **valor inicial** para a localização.

    | Nome a Apresentar  | Nome do Campo | Valor Inicial |
    | --------------| -----------|---------| 
    | Endereço de instalação | installAddress | Microsoft, 1 Microsoft Way, Redmond, WA 98052   |

   ![Formulário de "Configurar a localização" com detalhes para a localização](./media/howto-set-up-template-experimental/locationcloudproperty2.png)

   Existem dois formatos com suporte para adicionar uma localização:
   - **Localização como um endereço**
   - **Localização como coordenadas**

4. Selecione **Guardar**. Um operador pode atualizar o valor de localização dos **Device Explorer**.

#### <a name="add-location-as-a-device-property"></a>Adicionar localização como uma propriedade do dispositivo

Pode criar uma propriedade de localização como uma propriedade de dispositivo que o dispositivo comunica. Por exemplo, se quiser controlar a localização do dispositivo:

1. Navegue para o **propriedades** separador.

2. Selecione **propriedade do dispositivo** da biblioteca.

3. Configurar o nome a apresentar e o nome de campo e selecione **localização** como o tipo de dados:

    | Nome a Apresentar  | Nome do Campo | Tipo de Dados |
    | --------------| -----------|-----------|
    | Localização do dispositivo | deviceLocation | localização  |

   > [!NOTE]
   > Os nomes de campo tem de corresponder aos nomes de propriedade no código de dispositivo correspondente

   ![Formulário de "Configurar propriedades do dispositivo" com detalhes para a localização](./media/howto-set-up-template-experimental/locationdeviceproperty2.png)

Depois do dispositivo real é ligado, a localização que adicionou como uma propriedade do dispositivo é atualizada com o valor enviado pelo dispositivo. Agora que configurou a sua propriedade de localização, pode [adicionar um mapa para visualizar a localização no dashboard do dispositivo](#add-an-azure-maps-location-in-the-dashboard).

## <a name="commands"></a>Comandos

Comandos são utilizados para gerir remotamente um dispositivo. Elas permitem operadores executar comandos no dispositivo. Pode adicionar vários comandos ao seu modelo de dispositivo que são apresentados como mosaicos no **comandos** separador para operadores para utilizar. Como o construtor do dispositivo, tem a flexibilidade de definir comandos, de acordo com os seus requisitos.

Como é que um comando diferente a partir de uma definição?

* **Definição**: Uma configuração é uma configuração que pretende aplicar a um dispositivo. Pretende que o dispositivo para manter essa configuração até que alterá-lo. Por exemplo, se pretender definir a temperatura de sua freezer e pretende que a configuração, mesmo quando o freezer reinicia.

* **Comando**: Utilizar comandos instantaneamente executar um comando no dispositivo remotamente a partir de IoT Central. Se um dispositivo não está ligado, o comando exceder o tempo limite e falha. Por exemplo, que pretende reiniciar um dispositivo.

Por exemplo, pode adicionar um novo **eco** comando ao selecionar o **comandos** separador, em seguida, selecionando **+ novo comando**e introduzir os novos detalhes do comando:

| Nome a Apresentar  | Nome do Campo | Tempo Limite Predefinido | Tipo de Dados |
| --------------| -----------|---------------- | --------- |
| Comando Echo  | echo       |  30             | texto      |

![Formulário de "Configurar o comando" com detalhes para eco](./media/howto-set-up-template-experimental/commandsecho.png)

Depois de selecionar **salvar**, o **eco** comando aparece como um mosaico e está pronto para ser utilizada a partir do **Device Explorer** quando se liga o seu dispositivo real. Os nomes de campos do comando de têm de corresponder os nomes de propriedade no código de dispositivo correspondente na ordem de comandos para ser executada com êxito.

## <a name="rules"></a>Regras

Regras de permitem aos operadores monitorizar dispositivos em tempo real. Regras automaticamente invocam ações como enviar um e-mail quando a regra é acionada. Um tipo de regra está disponível hoje:

- **Regra de telemetria**, que é acionado quando a telemetria do dispositivo selecionado ultrapassar um limiar especificado. [Saiba mais sobre regras de telemetria](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="dashboard"></a>Dashboard

O dashboard é onde um operador pode ir para ver informações sobre um dispositivo. Como um construtor, pode adicionar mosaicos nesta página para o ajudar a compreender como o dispositivo está se comportando de operadores. Pode adicionar vários mosaicos do dashboard ao modelo do dispositivo. Pode adicionar vários tipos de mosaicos do dashboard, como a imagem, gráfico de linhas, gráfico de barras, indicador chave de desempenho (KPI), definições e propriedades e da etiqueta.

Por exemplo, pode adicionar um **definições e propriedades** mosaico para mostrar uma seleção dos valores atuais das definições e propriedades, selecionando o **Dashboard** guia e o mosaico da biblioteca:

![Formulário de "Configurar os detalhes do dispositivo" com detalhes para as definições e propriedades](./media/howto-set-up-template-experimental/dashboardsettingsandpropertiesform.png)

Agora quando um operador visualiza o dashboard na **Device Explorer**, eles podem ver o mosaico.

### <a name="add-an-azure-maps-location-in-the-dashboard"></a>Adicionar uma localização do Azure Maps no dashboard

Se tiver configurado uma propriedade de localização, é possível visualizar a localização, utilizando um mapa no dashboard do dispositivo.

1. Navegue para o **Dashboard** separador.

1. No dashboard do dispositivo, selecione **mapa** da biblioteca.

1. Atribua um título de mapa. O exemplo seguinte tem o título **localização de instalação**. Em seguida, selecione a propriedade de localização que configurou anteriormente o **propriedades** separador. No exemplo a seguir **endereço de instalação** está selecionada.

   ![Formulário de "Configurar o mapeamento de" com detalhes para o título e propriedades](./media/howto-set-up-template-experimental/locationcloudproperty5map.png)

4. Selecione **Guardar**. O mosaico do mapa apresenta agora a localização que selecionou.

Pode redimensionar o mapa para seu tamanho desejado. Agora quando um operador visualiza o dashboard na **Device Explorer**, mosaicos do dashboard do que tiver configurado, incluindo um mapa de localização estão visíveis.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como configurar um modelo de dispositivo na sua aplicação do Azure IoT Central, pode:

> [!div class="nextstepaction"]
> [Criar uma nova versão de modelo do dispositivo](howto-version-devicetemplate.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
