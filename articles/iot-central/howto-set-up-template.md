---
title: Configurar um modelo de dispositivo em um aplicativo de IoT Central do Azure | Microsoft Docs
description: Saiba como configurar um modelo de dispositivo com medidas, configurações, propriedades, regras e um painel.
author: viv-liu
ms.author: viviali
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ede7167d570c7bd2ba7e04c3a9a703555efb35cd
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698527"
---
# <a name="set-up-a-device-template"></a>Configurar um modelo de dispositivo

Um modelo de dispositivo é um plano gráfico que define as características e os comportamentos de um tipo de dispositivo que se conecta a um aplicativo de IoT Central do Azure.

Por exemplo, um construtor pode criar um modelo de dispositivo para um ventilador conectado com as seguintes características:

- Medição de telemetria de temperatura
- Medida de localização
- Medição do evento de erro do motor do ventilador
- Medição do estado operacional do ventilador
- Configuração de velocidade do ventilador
- Regras que enviam alertas
- Painel que fornece uma visão geral do dispositivo

A partir desse modelo de dispositivo, um operador pode criar e conectar dispositivos de ventilador reais com nomes como **fan-1** e **Fan-2**. Todos esses ventiladores têm medidas, configurações, propriedades, regras e um painel que os usuários do seu aplicativo podem monitorar e gerenciar.

> [!NOTE]
> Somente os criadores e os administradores podem criar, editar e excluir modelos de dispositivo. Qualquer usuário pode criar dispositivos na página de **Device Explorer** de modelos de dispositivo existentes.

## <a name="create-a-device-template"></a>Criar um modelo de dispositivo

1. Navegue até a página **modelos de dispositivo** .

2. Para criar um modelo, comece selecionando **+ novo**.

3. Para começar rapidamente, escolha entre os modelos predefinidos existentes. Caso contrário, selecione **personalizado**, insira um nome e clique em **criar** para criar seu próprio modelo do zero.

   ![Biblioteca de modelos de dispositivo](./media/howto-set-up-template/newtemplate.png)

4. Ao criar um modelo personalizado, você verá a página de **detalhes do dispositivo** para o novo modelo de dispositivo. IoT Central cria automaticamente um dispositivo simulado quando você cria um modelo de dispositivo. Um dispositivo simulado permite testar o comportamento do seu aplicativo antes de conectar um dispositivo real.

As seções a seguir descrevem cada uma das guias na página de **modelo do dispositivo** .

## <a name="measurements"></a>Medições

As medições são os dados provenientes do seu dispositivo. Você pode adicionar várias medidas ao modelo de dispositivo para corresponder aos recursos do seu dispositivo.

- As medições de telemetria são os pontos de dados numéricos que seu dispositivo coleta ao longo do tempo. Eles são representados como um fluxo contínuo. Um exemplo é temperatura.
- As medições de **evento** são dados pontuais que representam algo de significância no dispositivo. Um nível de severidade representa a importância de um evento. Um exemplo é um erro de motor do ventilador.
- As medidas de **estado** representam o estado do dispositivo ou seus componentes em um período de tempo. Por exemplo, um modo de ventilador pode ser definido como tendo o **funcionamento** e **parado** como os dois Estados possíveis.
- As medidas de **localização** são as coordenadas de longitude e latitude do dispositivo durante um período de tempo no. Por exemplo, um ventilador pode ser movido de um local para outro.

### <a name="create-a-telemetry-measurement"></a>Criar uma medição de telemetria

Para adicionar uma nova medição de telemetria, selecione **+ nova medida**, escolha telemetria como o tipo de medida e insira os detalhes no formulário.

> [!NOTE]
> Os nomes de campo no modelo de dispositivo devem corresponder aos nomes de propriedade no código de dispositivo correspondente para que a medição de telemetria seja exibida no aplicativo quando um dispositivo real estiver conectado. Faça o mesmo ao definir configurações, propriedades do dispositivo e comandos à medida que você continuar a definir o modelo de dispositivo nas seções a seguir.

Por exemplo, você pode adicionar uma nova medição de telemetria de temperatura:

| Nome a Apresentar        | Nome do Campo    |  Unidades    | Mín.   |Máx.|
| --------------------| ------------- |-----------|-------|---|
| Temperatura         | Temp          |  degC     |  0    |100|

![Formulário "criar telemetria" com detalhes para medição de temperatura](./media/howto-set-up-template/measurementsform.png)

Depois de selecionar **salvar**, a medição de **temperatura** aparecerá na lista de medições. Em breve, você verá a visualização dos dados de temperatura do dispositivo simulado.

Ao exibir a telemetria, você pode escolher entre as seguintes opções de agregação: Média, mínimo, máximo, soma e contagem. A **média** é selecionada como a agregação padrão no gráfico.

> [!NOTE]
> O tipo de dados da medição de telemetria é um número de ponto flutuante.

### <a name="create-an-event-measurement"></a>Criar uma medição de evento

Para adicionar uma nova medição de evento, selecione **+ nova medição** e selecione **evento** como o tipo de medida. Insira os detalhes no formulário **criar evento** .

Forneça o **nome de exibição**, o **nome do campo**e os detalhes de **severidade** para o evento. Você pode escolher entre os três níveis de severidade disponíveis: **Erro**, **aviso**e **informações**.

Por exemplo, você pode adicionar um novo evento de **erro do motor do ventilador** .

| Nome a Apresentar        | Nome do Campo    |  Gravidade Predefinida |
| --------------------| ------------- |-----------|
| Erro de Motor da Ventoinha     | fanmotorerror |  Erro    |

![Formulário "criar evento" com detalhes de um evento de motor de ventilador](./media/howto-set-up-template/eventmeasurementsform.png)

Depois de selecionar **salvar**, a medição de **erro do motor do ventilador** aparecerá na lista de medições. Em breve, você verá a visualização dos dados do evento do dispositivo simulado.

Para exibir mais detalhes sobre um evento, selecione o ícone de evento no gráfico:

![Detalhes do evento "erro do motor do ventilador"](./media/howto-set-up-template/eventmeasurementsdetail.png)

> [!NOTE]
> O tipo de dados da medição do evento é String.

### <a name="create-a-state-measurement"></a>Criar uma medição de estado

Para adicionar uma nova medida de estado, selecione o botão de **medida + nova** e selecione **estado** como o tipo de medida. Insira os detalhes no formulário de **criação de estado** .

Forneça os detalhes para **nome de exibição**, **nome do campo**e **valores** do estado. Cada valor também pode ter um nome de exibição que será usado quando o valor for exibido em gráficos e tabelas.

Por exemplo, você pode adicionar um novo estado de **modo de ventilador** que tem dois valores possíveis que o dispositivo pode enviar, **operar** e **parar**.

| Nome a Apresentar | Nome do Campo    |  Valor 1   | Nome a Apresentar | Valor 2    |Nome a Apresentar  |
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Modo da Ventoinha     | fanmode       |  1         | Em funcionamento    |     0      | Parada      |

![Formulário "Editar estado" com detalhes para o modo de ventilador](./media/howto-set-up-template/statemeasurementsform.png)

Depois de selecionar **salvar**, a medida de estado do **modo de ventilador** aparecerá na lista de medições. Em breve, você verá a visualização dos dados de estado do dispositivo simulado.

Se o dispositivo enviar muitos pontos de dados em uma pequena duração, a medida de estado aparecerá com um visual diferente. Selecione o gráfico para exibir todos os pontos de dados dentro desse período de tempo em ordem cronológica. Você também pode restringir o intervalo de tempo para ver a medida plotada no gráfico.

> [!NOTE]
> O tipo de dados da medida de estado é cadeia de caracteres.

### <a name="create-a-location-measurement"></a>Criar uma medição de local

Para adicionar uma nova medida de local, selecione **+ nova medida**, escolha **local** como o tipo de medida e insira os detalhes no formulário **criar medição** .

Por exemplo, você pode adicionar uma nova medida de telemetria de local:

| Nome a Apresentar        | Nome do Campo    |
| --------------------| ------------- |
| Local do ativo      |  assetloc     |

![Formulário "criar local" com detalhes para a medida de localização](./media/howto-set-up-template/locationmeasurementsform.png)

Depois de selecionar **salvar**, a medida de **localização** aparece na lista de medições. Em breve, você verá a visualização dos dados do local do dispositivo simulado.

Ao exibir o local, você pode escolher entre as seguintes opções: o local mais recente e o histórico de localização. O **histórico de localização** só é aplicado no intervalo de tempo selecionado.

O tipo de dados da medida de localização é um objeto que contém a longitude, a latitude e uma altitude opcional. O trecho a seguir mostra a estrutura JavaScript:

```javascript
assetloc: {
  lon: floating point number,
  lat: floating point number,
  alt?: floating point number
}
```

Quando o dispositivo real estiver conectado, o local que você adicionou como uma medida será atualizado com o valor enviado pelo dispositivo. Depois de configurar sua medição de local, você pode [Adicionar um mapa para visualizar o local no painel do dispositivo](#add-a-location-measurement-in-the-dashboard).

## <a name="settings"></a>Definições

Configurações controlam um dispositivo. Eles permitem que os operadores forneçam entradas para o dispositivo. Você pode adicionar várias configurações ao modelo de dispositivo que aparecem como blocos na guia **configurações** para que os operadores usem. Você pode adicionar vários tipos de configurações: número, texto, data, alternância e rótulo de seção.

As configurações podem estar em um dos três Estados. O dispositivo relata esses Estados.

- **Sincronizado**: O dispositivo foi alterado para refletir o valor da configuração.

- **Pendente**: No momento, o dispositivo está sendo alterado para o valor da configuração.

- **Erro**: O dispositivo retornou um erro.

Por exemplo, você pode adicionar uma nova configuração de velocidade do ventilador selecionando **configurações** e inserindo na nova configuração de **número** :

| Nome a Apresentar  | Nome do Campo    |  Unidades  | Decimais |Inicial|
| --------------| ------------- |---------| ---------|---- |
| Velocidade do ventilador     | fanSpeed      | RPM     | 2        | 0   |

![Formulário "configurar número" com detalhes para as configurações de velocidade](./media/howto-set-up-template/settingsform.png)

Depois de selecionar **salvar**, a configuração de **velocidade do ventilador** aparece como um bloco. Um operador pode usar a configuração na página de **Device Explorer** para alterar a velocidade do ventilador do dispositivo.

## <a name="properties"></a>properties

As propriedades são metadados associados ao dispositivo, como um local do dispositivo fixo e um número de série. Adicione várias propriedades ao modelo de dispositivo que aparecem como blocos na guia **Propriedades** . Uma propriedade tem um tipo como número, texto, data, alternância, Propriedade do dispositivo, rótulo ou um local fixo. Um operador especifica os valores para propriedades quando eles criam um dispositivo e podem editar esses valores a qualquer momento. As propriedades do dispositivo são somente leitura e enviadas do dispositivo para o aplicativo. Um operador não pode alterar as propriedades do dispositivo. Quando um dispositivo real se conecta, o bloco de propriedades do dispositivo é atualizado no aplicativo.

Existem duas categorias de propriedades:

- _Propriedades do dispositivo_ que o dispositivo relata para o aplicativo IOT central. As propriedades do dispositivo são valores somente leitura relatados pelo dispositivo e são atualizadas no aplicativo quando um dispositivo real é conectado.
- _As propriedades do aplicativo_ que são armazenadas no aplicativo e podem ser editadas pelo operador. As propriedades do aplicativo são armazenadas apenas no aplicativo e nunca são vistas por um dispositivo.

Por exemplo, você pode adicionar a última data de serviço para o dispositivo como uma nova propriedade de **Data** (uma propriedade de aplicativo) na guia **Propriedades** :

| Nome a Apresentar  | Nome do Campo | Valor Inicial   |
| --------------| -----------|-----------------|
| Última assistência      | lastServiced        | 01/29/2019     |

![Formulário "configurar o último serviço" na guia "Propriedades"](./media/howto-set-up-template/propertiesform.png)

Depois de selecionar **salvar**, a última data de serviço do dispositivo aparecerá como um bloco.

Depois de criar o bloco, você pode alterar o valor da Propriedade do aplicativo no **Device Explorer**.

### <a name="create-a-location-property"></a>Criar uma propriedade de local

Você pode fornecer um contexto geográfico aos dados de localização no Azure IoT Central e mapear quaisquer coordenadas de latitude e longitude ou um endereço de rua. O mapas do Azure habilita esse recurso no IoT Central.

Você pode adicionar dois tipos de propriedades de local:

- **Local como uma propriedade de aplicativo**, que é armazenada no aplicativo. As propriedades do aplicativo são armazenadas apenas no aplicativo e nunca são vistas por um dispositivo.
- **Local como uma propriedade de dispositivo**, que o dispositivo relata para o aplicativo. Esse tipo de propriedade é melhor usado para um local estático.

> [!NOTE]
> O local como uma propriedade não registra um histórico. Se o histórico for desejado, use uma medida de local.

#### <a name="add-location-as-an-application-property"></a>Adicionar local como uma propriedade de aplicativo

Você pode criar uma propriedade de local como uma propriedade de aplicativo usando mapas do Azure em seu aplicativo IoT Central. Por exemplo, você pode adicionar o endereço de instalação do dispositivo:

1. Navegue até a guia **Propriedades** .

2. Na biblioteca, selecione **local**.

3. Configure o **nome de exibição**, o nome do **campo**e (opcionalmente) o **valor inicial** para o local.

    | Nome a Apresentar  | Nome do Campo | Valor Inicial |
    | --------------| -----------|---------|
    | Endereço de instalação | installAddress | Microsoft, 1 Microsoft Way, Redmond, WA 98052   |

   ![Formulário "configurar local" com detalhes para o local](./media/howto-set-up-template/locationcloudproperty2.png)

   Há dois formatos com suporte para adicionar um local:
   - **Local como um endereço**
   - **Local como coordenadas**

4. Selecione **Guardar**. Um operador pode atualizar o valor de local no **Device Explorer**.

#### <a name="add-location-as-a-device-property"></a>Adicionar local como uma propriedade de dispositivo

Você pode criar uma propriedade de local como uma propriedade de dispositivo que o dispositivo relata. Por exemplo, se você quiser acompanhar o local do dispositivo:

1. Navegue até a guia **Propriedades** .

2. Selecione a **Propriedade do dispositivo** na biblioteca.

3. Configure o nome de exibição e o nome do campo e selecione **local** como o tipo de dados:

    | Nome a Apresentar  | Nome do Campo | Tipo de Dados |
    | --------------| -----------|-----------|
    | Localização do dispositivo | deviceLocation | location  |

   > [!NOTE]
   > Os nomes de campo devem corresponder aos nomes de propriedade no código de dispositivo correspondente

   ![Formulário "configurar propriedades do dispositivo" com detalhes para o local](./media/howto-set-up-template/locationdeviceproperty2.png)

Quando o dispositivo real estiver conectado, o local que você adicionou como uma propriedade de dispositivo será atualizado com o valor enviado pelo dispositivo. Depois de configurar sua propriedade Location, você pode [Adicionar um mapa para visualizar o local no painel do dispositivo](#add-a-location-property-in-the-dashboard).

## <a name="commands"></a>Comandos

Os comandos são usados para gerenciar remotamente um dispositivo. Eles permitem que os operadores executem comandos no dispositivo. Você pode adicionar vários comandos ao modelo de dispositivo que aparecem como blocos na guia **comandos** para que os operadores usem. Como o construtor do dispositivo, você tem a flexibilidade de definir comandos de acordo com suas necessidades.

Como um comando é diferente de uma configuração?

- **Configuração**: Uma configuração é uma configuração que você deseja aplicar a um dispositivo. Você deseja que o dispositivo persista essa configuração até que você a altere. Por exemplo, você deseja definir a temperatura de seu freezer e quer essa configuração mesmo quando o freezer é reiniciado.

- **Comando**: Você usa comandos para executar instantaneamente um comando no dispositivo remotamente do IoT Central. Se um dispositivo não estiver conectado, o comando expirará e falhará. Por exemplo, você deseja reiniciar um dispositivo.

Por exemplo, você pode adicionar um novo comando de **eco** selecionando a guia **comandos** , selecionando **+ novo comando**e inserindo os detalhes do novo comando:

| Nome a Apresentar  | Nome do Campo | Tempo Limite Predefinido | Tipo de Dados |
| --------------| -----------|---------------- | --------- |
| Comando Echo  | echo       |  30             | text      |

![Formulário "Configurar comando" com detalhes para Echo](./media/howto-set-up-template/commandsecho1.png)

Depois de selecionar **salvar**, o comando **Echo** aparece como um bloco e está pronto para ser usado no **Device Explorer** quando o dispositivo real se conecta. Os nomes de campo do comando devem corresponder aos nomes de propriedade no código de dispositivo correspondente para que os comandos sejam executados com êxito.

[Aqui está o link para o código de dispositivo C de exemplo.](https://github.com/Azure/iot-central-firmware/blob/ad40358906aeb8f2040a822ba5292df866692c16/MXCHIP/mxchip_advanced/src/AzureIOTClient.cpp#L34)

## <a name="rules"></a>Regras

As regras permitem que os operadores monitorem dispositivos quase em tempo real. As regras automaticamente chamam ações como enviar um email quando a regra é disparada. Um tipo de regra está disponível hoje:

- **Regra**de telemetria, que é disparada quando a telemetria do dispositivo selecionado cruza um limite especificado. [Saiba mais sobre as regras](howto-create-telemetry-rules.md)de telemetria.

## <a name="dashboard"></a>Dashboard

O painel é onde um operador vai para ver informações sobre um dispositivo. Como um construtor, você adiciona blocos a esta página para ajudar os operadores a entender como o dispositivo está se comportando. Você pode adicionar muitos tipos de blocos de painel, como imagem, gráfico de linhas, gráfico de barras, KPI (indicador chave de desempenho), configurações e propriedades e rótulo.

Por exemplo, você pode adicionar um bloco **configurações e propriedades** para mostrar uma seleção dos valores atuais de configurações e propriedades selecionando a guia **painel** e o bloco da biblioteca:

![Formulário "configurar detalhes do dispositivo" com detalhes para configurações e propriedades](./media/howto-set-up-template/dashboardsettingsandpropertiesform1.png)

Agora, quando um operador exibir o painel no **Device Explorer**, ele poderá ver o bloco.

### <a name="add-a-location-measurement-in-the-dashboard"></a>Adicionar uma medida de local no painel

Se você configurou uma medida de local, poderá visualizar o local com um mapa no painel do seu dispositivo. Para as medidas de localização, você tem a opção de plotar o histórico de localização.

1. Navegue até a guia **painel** .

1. No painel do dispositivo, selecione **mapa** na biblioteca.

1. Dê um título ao mapa. O exemplo a seguir tem o título **local atual do dispositivo**. Em seguida, escolha a medida local que você configurou anteriormente na guia **medidas** . No exemplo a seguir, a medida **local do ativo** é selecionada:

   ![Formulário "configurar mapa" com detalhes de título e propriedades](./media/howto-set-up-template/locationcloudproperty5map.png)

1. Selecione **Guardar**. O bloco mapa agora exibe o local que você selecionou.

Você pode redimensionar o bloco mapa. Quando um operador exibe o painel no **Device Explorer**, todos os blocos de painel que você configurou, incluindo um mapa de local, são visíveis.

### <a name="add-a-location-property-in-the-dashboard"></a>Adicionar uma propriedade local no painel

Se você configurou uma propriedade Location, poderá visualizar o local com um mapa no painel do seu dispositivo.

1. Navegue até a guia **painel** .

1. No painel do dispositivo, selecione **mapa** na biblioteca.

1. Dê um título ao mapa. O exemplo a seguir tem o título **local atual do dispositivo**. Em seguida, escolha a propriedade local que você configurou anteriormente na guia **Propriedades** . No exemplo a seguir, a medição do **local do dispositivo** é selecionada:

   ![Configurar o formulário de mapa com detalhes para título e propriedades](./media/howto-set-up-template/locationcloudproperty6map.png)

1. Selecione **Guardar**. O bloco mapa agora exibe o local que você selecionou.

Você pode redimensionar o bloco mapa. Quando um operador exibe o painel no **Device Explorer**, todos os blocos de painel que você configurou, incluindo um mapa de local, são visíveis.

Para saber mais sobre como usar blocos no Azure IoT Central, confira [usar blocos do Dashboard](howto-use-tiles.md).

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu como configurar um modelo de dispositivo em seu aplicativo de IoT Central do Azure, você pode:

- [Criar uma nova versão de modelo de dispositivo](howto-version-device-template.md)
- [Conectar um dispositivo MXChip IoT DevKit ao aplicativo IoT Central do Azure](howto-connect-devkit.md)
- [Conectar um aplicativo cliente genérico ao seu aplicativo de IoT Central do Azure (Node. js)](howto-connect-nodejs.md)
