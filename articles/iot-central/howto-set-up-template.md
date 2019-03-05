---
title: Configurar um modelo de dispositivo num aplicativo do Azure IoT Central | Documentos da Microsoft
description: Saiba como configurar um modelo de dispositivo com medidas, configurações, propriedades, regras e um dashboard.
author: viv-liu
ms.author: viviali
ms.date: 10/26/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 99fce821c903a9638575eba9fe4a17d294bcbd60
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57310510"
---
# <a name="set-up-a-device-template"></a>Configurar um modelo de dispositivo

Um modelo de dispositivo é um esquema que define as características e comportamentos de um tipo de dispositivo que se liga a uma aplicação do Azure IoT Central.

Por exemplo, um construtor pode criar um modelo de dispositivo para um fã de ligado de IoT que tem:

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

1. Vá para o **Application Builder** página.

2. Para criar um modelo em branco, selecione **criar modelo de dispositivo**e, em seguida, selecione **personalizado**.

3. Introduza um nome (por exemplo, refrigerator (refrigerador)-1) para o novo modelo de dispositivo e selecione **criar**.

   ![Página de detalhes do dispositivo com "Refrigerador" como o nome do modelo](./media/howto-set-up-template/devicedetailspage.png)

4. Agora, está na **detalhes do dispositivo** página de um novo dispositivo simulado. Um dispositivo simulado é criado automaticamente quando cria um modelo de dispositivo. -Relatórios de dados e pode ser controlado como um dispositivo real.

Agora vamos examinar cada um dos separadores **detalhes do dispositivo** página.

## <a name="measurements"></a>Medições

Medidas são os dados provenientes do seu dispositivo. Pode adicionar várias medidas ao seu modelo de dispositivo de acordo com as capacidades do seu dispositivo.

- **Telemetria** medidas são os pontos de dados numéricos que recolhe o seu dispositivo ao longo do tempo. São representadas como um fluxo contínuo. Um exemplo é a temperatura.
- **Evento** medidas são os dados de ponto no tempo que representa algo de significância no dispositivo. Um nível de gravidade representa a importância de um evento. Um exemplo é um erro de motor de ventoinha.
- **Estado** medidas representam o estado do dispositivo ou de seus componentes durante um período de tempo. Por exemplo, um modo de ventoinha pode ser definido como tendo **operacional** e **parado** como dois Estados possíveis.

### <a name="create-a-telemetry-measurement"></a>Criar uma medida de telemetria
Para adicionar uma nova medida de telemetria, selecione **Editar modelo**e, em seguida, selecione a **+ nova medida** botão. Selecione **telemetria** como a medição escreva e introduza os detalhes o **telemetria criar** formulário.

> [!NOTE]
> Os nomes de campo no modelo do dispositivo tem de corresponder os nomes de propriedade no código de dispositivo correspondente para que a medição de telemetria a apresentar na aplicação quando está ligado um dispositivo real. Fazer o mesmo ao configurar as definições de propriedades do dispositivo e comandos enquanto continua a definir o modelo de dispositivo nas seções a seguir.

Por exemplo, pode adicionar uma nova medida de telemetria de temperatura:
| Nome a Apresentar        | Nome do Campo    |  Unidades    | Mín.   |Máx.|
| --------------------| ------------- |-----------|-------|---|
| Temperatura         | temp          |  degC     |  0    |100|

![Formulário de "Criar telemetria" com detalhes para medidas de temperatura](./media/howto-set-up-template/measurementsform.png)

Depois de selecionar **feito**, o **temperatura** medição aparece na lista de medidas. Em pouco tempo, pode ver a visualização dos dados de temperatura que gerou o dispositivo simulado. Quando cria um modelo de dispositivo, é gerado um dispositivo simulado do modelo que permite que teste o comportamento do seu aplicativo antes de um dispositivo físico/real está ligado.


> [!NOTE]
  O tipo de dados da medição de telemetria é uma vírgula flutuante ponto número.

### <a name="create-an-event-measurement"></a>Criar uma medida de eventos
Para adicionar uma nova medida de evento, selecione **Editar modelo**e, em seguida, selecione a **+ nova medida** botão. Selecione **evento** como a medição escreva e introduza os detalhes o **Create Event** formulário.

Forneça o **nome a apresentar**, **nome do campo**, e **gravidade** detalhes do evento. Pode escolher entre três níveis de gravidade disponíveis: **Erro**, **aviso**, e **informações**.  

Por exemplo, pode adicionar um novo **erro de Motor de ventoinha** eventos.

| Nome a Apresentar        | Nome do Campo    |  Gravidade Predefinida | 
| --------------------| ------------- |-----------|
| Erro de Motor da Ventoinha     | fanmotorerror |  Erro    | 

![Formulário de "Criar eventos" com detalhes para um evento de motor de ventoinha](./media/howto-set-up-template/eventmeasurementsform.png)

Depois de selecionar **feito**, o **erro de Motor de ventoinha** medição aparece na lista de medidas. Um operador pode ver a visualização dos dados do evento que o dispositivo está a enviar.

![Gráfico de medição de eventos](./media/howto-set-up-template/eventmeasurementschart.png)

Para ver mais detalhes sobre o evento, selecione o ícone de eventos no gráfico.

![Detalhes do evento de "Erro de Motor de ventoinha"](./media/howto-set-up-template/eventmeasurementsdetail.png)

> [!NOTE]
  O tipo de dados da medição de eventos é a cadeia de caracteres.

### <a name="create-a-state-measurement"></a>Criar uma medida de estado
Para adicionar uma nova medida de estado, selecione **Editar modelo**e, em seguida, selecione a **+ nova medida** botão. Selecione **estado** como a medição escreva e introduza os detalhes o **estado de criar** formulário.

Forneça os detalhes para **nome a apresentar**, **nome do campo**, e **valores** do Estado. Cada valor também pode ter um nome a apresentar que será utilizado quando o valor é apresentado em gráficos e tabelas.

Por exemplo, pode adicionar um novo **ventoinha modo** estado de que tem dois valores possíveis que o dispositivo pode enviar, **operacional** e **parado**.


| Nome a Apresentar | Nome do Campo    |  Valor 1   | Nome a Apresentar | Valor 2    |Nome a Apresentar  | 
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Modo da Ventoinha     | fanmode       |  1         | Em funcionamento    |     0      | Parada      |

![Formulário de "Estado de edição" com detalhes para o modo de ventoinha](./media/howto-set-up-template/statemeasurementsform.png)

Depois de selecionar **feito**, o **modo ventoinha** medição do estado é apresentado na lista de medidas. O operador pode ver a visualização dos dados de estado que o dispositivo está a enviar.

![Gráfico de estado de medida](./media/howto-set-up-template/statemeasurementschart.png)

Se o dispositivo envia demasiados pontos de dados num pequeno período de tempo, a medição de estado é apresentado com um elemento visual diferente, como mostrado na captura de ecrã seguinte. Se selecionar o gráfico, todos os pontos de dados dentro desse período de tempo são apresentados numa ordem cronológica. Também pode reduzir o intervalo de tempo para ver a medição plotada no gráfico.

> [!NOTE]
  O tipo de dados da medição de estado é a cadeia de caracteres.

## <a name="settings"></a>Definições

Definições de controlarem um dispositivo. Permitem que os operadores da sua aplicação para fornecer entradas para o dispositivo. Pode adicionar várias definições ao seu modelo de dispositivo que são apresentados como mosaicos no **definições** separador para operadores para utilizar. Pode adicionar vários tipos de definições: número, texto, data, alternar, lista de seleção e rótulo de seção. 

Elas podem estar em um de três Estados. O dispositivo comunica esses Estados.

- **Sincronizado**: O dispositivo foi alterado para refletir o valor de definição.

- **Pendente**: O dispositivo atualmente está mudando para o valor de definição.

- **Erro**: O dispositivo devolveu um erro.

Por exemplo, pode adicionar uma nova definição de velocidade de ventoinha selecionando **Editar modelo** e introduzir no novo **número** definição:

| Nome a Apresentar  | Nome do Campo    |  Unidades  | Casas decimais |Inicial|
| --------------| ------------- |---------| ---------|---- |
| Ventoinha velocidade     | fanSpeed      | RPM     | 2        | 0   |

![Formulário de "Configurar o número" com detalhes para as definições de velocidade](./media/howto-set-up-template/settingsform.png)

Depois de selecionar **salvar**, o **ventoinha velocidade** definição é apresentada como um mosaico e está pronta para ser utilizada para alterar a velocidade de fã do dispositivo.

Depois de criar um mosaico, selecione **feito** na parte do canto superior direito da tela. Depois do dispositivo real é ligado à aplicação, o valor da definição será alterado para sincronizado.

![Separador "Definições" com a opção "Modo de Design" para o mosaico](./media/howto-set-up-template/settingstile.png)

## <a name="properties"></a>Propriedades

Propriedades são os metadados do dispositivo que está associada do dispositivo, como a localização do dispositivo e o número de série. Pode adicionar várias propriedades para o modelo de dispositivo que são apresentados como mosaicos no **propriedades** separador. Pode adicionar vários tipos de propriedades: número, texto, data, alternar, propriedade do dispositivo, etiqueta e localização. Um operador pode especificar os valores de propriedades quando criam um dispositivo e podem editar estes valores em qualquer altura. No entanto, as propriedades dos dispositivos são só de leitura são enviadas do dispositivo para a aplicação e não podem ser alteradas pelo operador. Quando o dispositivo real é ligado, o mosaico de propriedade do dispositivo será atualizado na aplicação. 

Existem duas categorias de propriedades:

- **Dispositivo** propriedades que o dispositivo comunica-se para a aplicação do Centro de IoT. Estes são os valores de só de leitura comunicados pelo dispositivo e serão atualizadas na aplicação quando o dispositivo real está ligado. 
- **Aplicação** propriedades que são armazenadas puramente no aplicativo e podem ser editadas pela operadora de rede. O dispositivo não reconhece as propriedades da aplicação.

Por exemplo, pode adicionar localização do dispositivo como uma nova **texto** propriedade (uma propriedade de aplicativo) ao selecionar **Editar modelo** e inserindo a nova propriedade:

| Nome a Apresentar  | Nome do Campo | Reduzir Espaços Iniciais  | Reduzir Espaços Seguintes  | Maiúsculas e minúsculas| Comprimento Mínimo | Comprimento Máximo |
| --------------| -----------|---------| ---------|---- |----|----|
| Localização      | loc        | Desativado     |  Desativado     | Misto  | 0 | 100|

![Formulário de "Configurar o texto" na guia "Propriedades"](./media/howto-set-up-template/propertiesform.png)

Depois de selecionar **guardar**, localização do dispositivo é apresentado como um mosaico:

![Mosaico de localização](./media/howto-set-up-template/propertiestile.png)

Depois de criar o mosaico, pode alterar o valor de propriedade do aplicativo. Primeiro, selecione **feito** na parte canto superior direito da tela.

### <a name="create-a-location-property-through-azure-maps"></a>Criar uma propriedade de localização por meio do Azure Maps
Pode dar contexto geográfico aos seus dados de localização no Azure IoT Central e mapear qualquer coordenadas de latitude e longitude de uma morada. Ou pode simplesmente latitude e longitude coordenadas do mapa. Mapas do Azure permite que esta capacidade no Centro de IoT.

Pode adicionar dois tipos de propriedades de localização:
- **Localização como uma propriedade de aplicativo**, que é armazenado puramente no aplicativo. O dispositivo não reconhece as propriedades da aplicação.
- **Localização como uma propriedade de dispositivo**, que o dispositivo de relatórios para a aplicação.

#### <a name="add-location-as-an-application-property"></a>Adicionar localização como uma propriedade de aplicação 
Pode criar uma propriedade de localização como uma propriedade de aplicação com o Azure Maps na sua aplicação IoT Central. Por exemplo, pode adicionar o endereço de instalação de dispositivo. 

1. Sobre o **propriedades** separador, selecione **Editar modelo**.

   ![Guia "Propriedades" com o modo de design ativado](./media/howto-set-up-template/locationcloudproperty1.png)

2. Na biblioteca, selecione **localização**.
3. Configurar **nome a apresentar**, **nome do campo**e (opcionalmente) **valor inicial** para a localização. 

    | Nome a Apresentar  | Nome do Campo | Valor Inicial |
    | --------------| -----------|---------| 
    | Endereço de instalação | insta_address | Microsoft, 1 Microsoft Way, Redmond, WA 98052   |

   ![Formulário de "Configurar a localização" com detalhes para a localização](./media/howto-set-up-template/locationcloudproperty2.png)

   Existem dois formatos com suporte para adicionar uma localização:
   - **Localização como um endereço**
   - **Localização como coordenadas** 

4. Selecione **salvar** e **feito**. Agora, um operador pode atualizar o valor de localização sob a forma de campo de localização. 

#### <a name="add-location-as-a-device-property"></a>Adicionar localização como uma propriedade do dispositivo 

Pode criar uma propriedade de localização como uma propriedade de dispositivo que o dispositivo comunica. Por exemplo, se quiser controlar a localização do dispositivo:

1. Sobre o **propriedades** separador, selecione **Editar modelo**.

   ![Guia "Propriedades" com o modo de design ativado](./media/howto-set-up-template/locationdeviceproperty1.png)

2. Selecione **propriedade do dispositivo** da biblioteca.
3. Configurar o nome a apresentar e o nome de campo e selecione **localização** como o tipo de dados. 

    | Nome a Apresentar  | Nome do Campo | Tipo de Dados |
    | --------------| -----------|-----------| 
    | Localização do dispositivo | deviceLoc| localização  |

   > [!NOTE]
   > Os nomes de campo tem de corresponder aos nomes de propriedade no código de dispositivo correspondente

   ![Formulário de "Configurar propriedades do dispositivo" com detalhes para a localização](./media/howto-set-up-template/locationdeviceproperty2.png)

Depois do dispositivo real é ligado, a localização que adicionou como uma propriedade do dispositivo será atualizada com o valor enviado pelo dispositivo. O endereço de instalação, a localização que adicionou como uma propriedade de aplicativo, é um mosaico editável. Agora que configurou a sua propriedade de localização, pode [adicionar um mapa para visualizar a localização no dashboard do dispositivo](#add-an-azure-maps-location-in-the-dashboard).

## <a name="commands"></a>Comandos

Comandos são utilizados para gerir remotamente um dispositivo. Permitem que os operadores da sua aplicação para executar os comandos instantaneamente no dispositivo. Pode adicionar vários comandos ao seu modelo de dispositivo que são apresentados como mosaicos no **comandos** separador para operadores para utilizar. Como o construtor do dispositivo, tem a flexibilidade de definir comandos, de acordo com os seus requisitos.

Como é que um comando diferente a partir de uma definição? 

* **Definição**: Uma definição é uma configuração que pretende aplicar a um dispositivo e quiser que o dispositivo para manter essa configuração até que alterá-lo. Por exemplo, se pretender definir a temperatura de sua freezer e pretende que a configuração, mesmo quando o freezer reinicia. 

* **Comando**: Utilizar comandos instantaneamente executar um comando no dispositivo remotamente a partir de IoT Central. Se um dispositivo não está ligado, o comando exceder o tempo limite e falha. Por exemplo, que pretende reiniciar um dispositivo.  


Por exemplo, pode adicionar um novo **eco** comando selecionando **Editar modelo**, em seguida, selecionar **+ novo comando**e inserindo o comando novo:

| Nome a Apresentar  | Nome do Campo | Tempo Limite Predefinido | Tipo de Dados |
| --------------| -----------|---------------- | --------- | 
| Comando Echo  | echo       |  30             | texto      |

![Formulário de "Configurar o comando" com detalhes para eco](./media/howto-set-up-template/commandsecho.png)

Depois de selecionar **salvar** e **feito**, o **eco** comando aparece como um mosaico e está pronto para ser usado para retornar o dispositivo assim que o seu dispositivo real está ligado. Os nomes de campos do comando de têm de corresponder os nomes de propriedade no código de dispositivo correspondente na ordem de comandos para ser executada com êxito.


## <a name="rules"></a>Regras

Regras de permitem aos operadores monitorizar dispositivos em tempo real. Regras automaticamente invocam ações como enviar um e-mail quando a regra é acionada. Um tipo de regra está disponível hoje:

- **Regra de telemetria**, que é acionado quando a telemetria do dispositivo selecionado ultrapassar um limiar especificado. [Saiba mais sobre regras de telemetria](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Dashboard

O dashboard é onde um operador pode ir para ver informações sobre um dispositivo. Como um construtor, pode adicionar mosaicos nesta página para o ajudar a compreender como o dispositivo está se comportando de operadores. Pode adicionar vários mosaicos do dashboard ao modelo do dispositivo. Pode adicionar vários tipos de mosaicos do dashboard, como a imagem, gráfico de linhas, gráfico de barras, indicador chave de desempenho (KPI), definições e propriedades e da etiqueta.

Por exemplo, pode adicionar um **definições e propriedades** mosaico para mostrar uma seleção dos valores atuais das definições e propriedades selecionando **Editar modelo** e o mosaico da biblioteca:

![Formulário de "Configurar os detalhes do dispositivo" com detalhes para as definições e propriedades](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

Agora quando um operador visualiza o dashboard, pode ver este mosaico que apresenta as propriedades e configurações do dispositivo:

![Separador "Dashboard" com as definições apresentadas e propriedades para o mosaico](./media/howto-set-up-template/dashboardtile.png)

### <a name="add-an-azure-maps-location-in-the-dashboard"></a>Adicionar uma localização do Azure Maps no dashboard

Se tiver configurado uma propriedade de localização anteriormente em [criar uma propriedade de localização por meio do Azure Maps](#create-a-location-property-through-azure-maps), é possível visualizar a localização, utilizando um mapa no dashboard do dispositivo.

1. Sobre o **Dashboard** separador, selecione **Editar modelo**.

   ![Separador "Dashboard" com o modo de design ativado](./media/howto-set-up-template/locationcloudproperty4map.png)

2. No dashboard do dispositivo, selecione **mapa** da biblioteca. 
3. Forneça um título. O exemplo a seguir tem o título de localização de instalação, em seguida, selecione a propriedade de localização que configurou anteriormente no separador de propriedades. No exemplo abaixo, **endereço de instalação** está selecionada.

   ![Formulário de "Configurar o mapeamento de" com detalhes para o título e propriedades](./media/howto-set-up-template/locationcloudproperty5map.png)

4. Selecione **Guardar**. O mosaico do mapa apresenta agora a localização que selecionou. 

   ![Mosaico do mapa com a localização selecionada](./media/howto-set-up-template/locationcloudproperty6map.png) 

Pode redimensionar o mapa para seu tamanho desejado. Agora quando um operador visualiza o dashboard, pode ver todos os mosaicos de dashboard que foram configurados, incluindo um mapa de localização.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como configurar um modelo de dispositivo na sua aplicação do Azure IoT Central, pode:

> [!div class="nextstepaction"]
> [Criar uma nova versão de modelo do dispositivo](howto-version-devicetemplate.md)
