---
title: Início rápido-adicionar um dispositivo simulado ao Azure IoT Central
description: Este guia de início rápido mostra como criar um modelo de dispositivo e adicionar um dispositivo simulado ao seu aplicativo IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 46d325fd609363a5425fc16845552e75b575c231
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706702"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application-preview-features"></a>Início rápido: adicionar um dispositivo simulado ao seu aplicativo de IoT Central (recursos de visualização)

*Este artigo aplica-se a operadores, construtores e administradores.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Um modelo de dispositivo define os recursos de um dispositivo que se conecta ao seu aplicativo IoT Central. Os recursos incluem telemetria que o dispositivo envia, as propriedades do dispositivo e os comandos que um dispositivo responde. De um modelo de dispositivo, um construtor ou operador pode adicionar dispositivos reais e simulados a um aplicativo. Os dispositivos simulados são úteis para testar o comportamento do seu aplicativo IoT Central antes de conectar dispositivos reais.

Neste guia de início rápido, você cria um modelo de dispositivo de **sensor ambiental** e adiciona um dispositivo simulado. Um dispositivo de sensor ambiental:

* Envia telemetria como temperatura.
* Relata propriedades específicas do dispositivo, como o nível de brilho.
* Responde a comandos como ativar e desativar.
* Relata propriedades genéricas do dispositivo, como a versão do firmware e o número de série.

## <a name="prerequisites"></a>Pré-requisitos

Conclua o guia de início rápido [criar um aplicativo do Azure IOT central (recursos de visualização)](./quick-deploy-iot-central.md) para criar um aplicativo IOT central usando o modelo **aplicativo personalizado > Visualização de aplicativos** .

Você também precisa de uma cópia local do arquivo **EnvironmentalSensorInline. capabilitymodel. JSON** que contém o modelo de recurso de dispositivo de [plug and Play de IOT](../../iot-pnp/overview-iot-plug-and-play.md) . Você pode baixá-lo [aqui](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json). Clique com o botão direito do mouse na página e selecione **salvar como**.

Depois de baixar o arquivo, abra-o em um editor de texto e substitua as duas instâncias de `<YOUR_COMPANY_NAME_HERE>` pelo seu nome. Use somente os caracteres a-z, A-Z, 0-9 e sublinhado.

## <a name="create-a-template"></a>Criar um modelo

Como um construtor, você pode criar e editar modelos de dispositivo em seu aplicativo. Depois de publicar um modelo de dispositivo, você pode gerar um dispositivo simulado ou conectar dispositivos reais que implementam o modelo de dispositivo. Os dispositivos simulados permitem testar o comportamento do seu aplicativo antes de conectar um dispositivo real.

Para adicionar um novo modelo de dispositivo ao seu aplicativo, vá para a página **modelos de dispositivo** . Para fazer isso, selecione a guia **modelos de dispositivo** no painel esquerdo.

![Página modelos de dispositivo](./media/quick-create-pnp-device/devicedefinitions.png)

### <a name="add-a-device-capability-model"></a>Adicionar um modelo de capacidade de dispositivo

Há várias opções para criar um modelo de capacidade de dispositivo no IoT Central. Você pode optar por criar um modelo personalizado do zero, importar de um arquivo, selecionar no catálogo do dispositivo ou conectar um dispositivo IoT Plug and Play por meio de uma conexão do dispositivo-primeiro em que o modelo de capacidade do dispositivo foi publicado no repositório público. Neste tutorial, você importará um modelo de capacidade de dispositivo de um arquivo.

As etapas a seguir mostram como importar o modelo de capacidade para um dispositivo de **sensor ambiental** . Esses dispositivos enviam telemetria, como temperatura, para seu aplicativo:

1. Para adicionar um novo modelo de dispositivo, selecione **+** na página **modelos de dispositivo** .

1. Escolha **dispositivo IOT** na lista de modelos de dispositivo personalizados, selecione **Avançar: Personalizar**, selecione **Avançar: revisão**e, em seguida, selecione **criar**.

1. Insira **sensor ambiental** como o nome do seu modelo de dispositivo.

1. Escolha **Importar modelo de funcionalidade** para criar um novo modelo de capacidade de dispositivo a partir de um arquivo JSON. Navegue até a pasta em que você salvou o arquivo **EnvironmentalSensorInline. capabilitymodel. JSON** em seu computador local. Selecione o arquivo **EnvironmentalSensorInline. capabilitymodel. JSON** e, em seguida, selecione **abrir**. O modelo de recurso do sensor de ambiente inclui as interfaces do **sensor ambiental** e de **informações do dispositivo** :

    ![Modelo de capacidade de dispositivo do sensor ambiental](./media/quick-create-pnp-device/newdevicecapabilitymodel.png)

    Essas interfaces definem os recursos de um dispositivo de **sensor ambiental** . Os recursos incluem a telemetria que um dispositivo envia, as propriedades que um dispositivo relata e os comandos aos quais um dispositivo responde.

### <a name="add-cloud-properties"></a>Adicionar propriedades de nuvem

Um modelo de dispositivo pode incluir propriedades de nuvem. As propriedades de nuvem existem somente no aplicativo IoT Central e nunca são enviadas ou recebidas de um dispositivo.

1. Selecione **Propriedades de nuvem** e **+ Adicionar Propriedade de nuvem**. Use as informações na tabela a seguir para adicionar uma propriedade de nuvem ao modelo de dispositivo.

    | Nome a Apresentar      | Tipo semântico | Esquema |
    | ----------------- | ------------- | ------ |
    | Data da Última Assistência | Nenhuma          | Date   |
    | Nome do cliente     | Nenhuma          | String |

1. Selecione **salvar** para salvar as alterações:

    ![Propriedades de nuvem](media/quick-create-pnp-device/cloudproperties.png)

## <a name="create-views"></a>Criar exibições

Como um construtor, você pode personalizar o aplicativo para exibir informações relevantes sobre o dispositivo do sensor ambiental para um operador. Suas personalizações permitem que o operador gerencie os dispositivos do sensor ambiental conectados ao aplicativo. Você pode criar dois tipos de modos de exibição para que um operador Use para interagir com dispositivos:

* Formulários para exibir e editar propriedades de dispositivo e de nuvem.
* Painéis para visualizar dispositivos.

### <a name="generate-default-views"></a>Gerar exibições padrão

Gerar exibições padrão é uma maneira rápida de começar a visualizar as informações importantes do dispositivo. Você pode ter até três exibições padrão geradas para seu modelo de dispositivo:

* A exibição **comandos** permite que o operador despache comandos para seu dispositivo.
* O modo de exibição de **visão geral** usa gráficos e métricas para exibir a telemetria do dispositivo.
* A exibição **sobre** exibe as propriedades do dispositivo.

Selecione **exibições** e, em seguida, **gerar exibições padrão**.

### <a name="configure-a-view-to-visualize-devices"></a>Configurar uma exibição para visualizar dispositivos

Um painel de dispositivos permite que um operador visualize um dispositivo usando gráficos e métricas. Pode definir as informações que são apresentadas num dashboard do dispositivo como construtor. Você pode definir vários painéis para dispositivos. Para criar um painel para visualizar a telemetria do sensor ambiental, selecione **exibições** e, em seguida, **visualizando o dispositivo**:

1. Todas as propriedades do dispositivo, propriedades de nuvem, telemetria e opções estáticas são listadas em **Propriedades**. Você pode arrastar e soltar qualquer um desses itens para a exibição. Arraste a propriedade **nível de brilho** para a exibição. Você pode configurar o bloco usando o ícone de engrenagem.

1. Para adicionar um gráfico que plota telemetria, selecione **umidade** e **temperatura**e, em seguida, selecione **combinar**. Para exibir esse gráfico em um formato diferente, como um gráfico de pizza ou um gráfico de barras, selecione o botão **alterar visualização** na parte superior do bloco.

1. Selecione **salvar** para salvar sua exibição:

Você pode adicionar mais blocos que mostram outras propriedades ou valores de telemetria. Você também pode adicionar texto estático, links e imagens. Para mover ou redimensionar um bloco no painel, mova o ponteiro do mouse sobre o bloco e arraste o bloco para um novo local ou redimensione-o.

### <a name="add-a-device-form"></a>Adicionar um formulário de dispositivo

Um formulário de dispositivo permite que um operador Edite propriedades de dispositivo gravável e propriedades de nuvem. Como um construtor, você pode definir vários formulários e escolher quais propriedades de dispositivo e de nuvem mostrar em cada formulário. Você também pode exibir propriedades de dispositivo somente leitura em um formulário.

Para criar um formulário para exibir e editar as propriedades do sensor ambiental:

1. Navegue até **exibições** no modelo de **sensor ambiental** . Selecione o bloco **dispositivo de edição e dados de nuvem** para adicionar uma nova exibição.

1. Insira o nome do formulário **Propriedades do sensor ambiental**.

1. Arraste as propriedades de nuvem **nome do cliente** e **última data de serviço** até a seção existente no formulário.

1. Selecione o **nível de brilho** e as propriedades do dispositivo de **estado do dispositivo** . Em seguida, selecione **Adicionar seção**. Edite o título da seção para ser **Propriedades do sensor**. Selecione **Aplicar**.

1. Selecione o **modelo do dispositivo**, a **versão do software**, o **fabricante**e as propriedades do dispositivo do **fabricante do processador** . Em seguida, selecione **Adicionar seção**. Edite o título da seção para ser **Propriedades do dispositivo**. Selecione **Aplicar**.

1. Selecione **salvar** para salvar sua exibição.

## <a name="publish-device-template"></a>Publicar modelo de dispositivo

Para poder criar um sensor ambiental simulado ou conectar um sensor ambiental real, você precisa publicar o modelo do dispositivo.

Para publicar um modelo de dispositivo:

1. Vá para o modelo de dispositivo na página **modelos de dispositivo** .

1. Selecione **Publicar**.

1. Na caixa de diálogo **publicar um modelo de dispositivo** , escolha **publicar**:

    ![Modelo publicado](media/quick-create-pnp-device/publishedmodel.png)

Depois que um modelo de dispositivo é publicado, ele fica visível na página **dispositivos** e no operador. Em um modelo de dispositivo publicado, você não pode editar um modelo de capacidade de dispositivo sem criar uma nova versão. No entanto, você pode fazer atualizações nas propriedades de nuvem, personalizações e exibições, em um modelo de dispositivo publicado sem controle de versão. Depois de fazer alterações, selecione **publicar** para enviar por push essas alterações para o operador.

## <a name="add-a-simulated-device"></a>Adicionar um dispositivo simulado

Para adicionar um dispositivo simulado ao seu aplicativo, use o modelo de dispositivo do **sensor de ambiente** que você criou.

1. Para adicionar um novo dispositivo como um operador, escolha **dispositivos** no painel esquerdo. A guia **dispositivos** mostra **todos os dispositivos** e o modelo de dispositivo do **sensor ambiental** . Selecione **sensor ambiental**.

1. Para adicionar um dispositivo de sensor de ambiente simulado, selecione **+ novo**. Use a **ID de dispositivo** sugerida ou insira sua própria **ID de dispositivo**em minúsculas. Você também pode inserir um nome para o novo dispositivo. Alterne a alternância **simulada** para **ativado** e, em seguida, selecione **criar**.

    ![Dispositivo de simulação](./media/quick-create-pnp-device/simulated-device.png)

Agora você pode interagir com as exibições que foram criadas pelo construtor para o modelo de dispositivo usando dados simulados.

## <a name="use-a-simulated-device-to-improve-views"></a>Usar um dispositivo simulado para melhorar as exibições

Depois de criar um novo dispositivo simulado, o construtor pode usar este dispositivo para continuar a melhorar e criar as exibições para o modelo de dispositivo.

1. Escolha **modelos de dispositivo** no painel esquerdo e selecione o modelo de **sensor ambiental** .

1. Selecione qualquer uma das exibições que você deseja editar ou crie uma nova exibição. Clique em **Configurar visualização do dispositivo**e **selecione de um dispositivo em execução**. Aqui você pode escolher entre não ter nenhum dispositivo de visualização, usando um dispositivo real que pode ser configurado para teste ou de um dispositivo existente que você adicionou ao IoT Central.

1. Escolha o dispositivo simulado na lista. Em seguida, selecione **aplicar**. Agora você pode ver o mesmo dispositivo simulado em seus modos de exibição de modelo de dispositivo experiência de criação. Essa exibição é útil para gráficos e outras visualizações.

    ![Configurar o dispositivo de visualização](./media/quick-create-pnp-device/configure-preview.png)

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a criar um modelo de dispositivo de **sensor de ambiente** e a adicionar um dispositivo simulado ao seu aplicativo.

Para saber mais sobre como monitorar dispositivos conectados ao seu aplicativo, vá para o início rápido:

> [!div class="nextstepaction"]
> [Configurar regras e ações](./quick-configure-rules.md)
