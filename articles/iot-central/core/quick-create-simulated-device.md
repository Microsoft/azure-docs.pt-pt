---
title: Quickstart - Adicione um dispositivo simulado ao Azure IoT Central
description: Este quickstart mostra como criar um modelo de dispositivo e adicionar um dispositivo simulado à sua aplicação IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 0396c028c8c0e1a18baf037fed4efc21aad944f1
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715105"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Quickstart: Adicione um dispositivo simulado à sua aplicação IoT Central

Um modelo de dispositivo define as capacidades de um dispositivo que se conecta à sua aplicação IoT Central. As capacidades incluem telemetria que o dispositivo envia, propriedades do dispositivo, e os comandos a que um dispositivo responde. Utilizando um modelo de dispositivo, pode adicionar dispositivos reais e simulados a uma aplicação. Os dispositivos simulados são úteis para testar o comportamento da sua aplicação IoT Central antes de ligar dispositivos reais.

Neste arranque rápido, adicione um modelo de dispositivo para uma placa de desenvolvimento IoT Kit ESP32-Azure e crie um dispositivo simulado. Para completar este arranque rápido não precisa de um dispositivo real, trabalha com uma simulação do dispositivo. Um dispositivo ESP32:

* Envia telemetria como temperatura.
* Relata propriedades específicas do dispositivo, tais como a temperatura máxima desde que o dispositivo foi reiniciado.
* Responde a comandos como reiniciar.
* Relata propriedades genéricas do dispositivo, como a versão do firmware e o número de série.

## <a name="prerequisites"></a>Pré-requisitos

Complete o quickstart [da aplicação Create azure IoT Central](./quick-deploy-iot-central.md) para criar uma aplicação IoT Central utilizando o modelo **de aplicação Custom > Custom.**

## <a name="create-a-device-template"></a>Criar um modelo de dispositivo

Para adicionar um novo modelo de dispositivo à sua aplicação, selecione o **separador Modelos de Dispositivo** no painel esquerdo.

:::image type="content" source="media/quick-create-simulated-device/device-definitions.png" alt-text="Screenshot mostrando lista vazia de modelos de dispositivo":::

Um modelo de dispositivo inclui um modelo de dispositivo que define:

* A telemetria que o dispositivo envia.
* Propriedades do dispositivo.
* O comando a que o dispositivo responde.

### <a name="add-a-device-template"></a>Adicionar um modelo de dispositivo

Existem várias opções para adicionar um modelo de dispositivo à sua aplicação IoT Central. Pode criar um modelo de raiz, importar um modelo a partir de um ficheiro ou selecionar um dispositivo do catálogo do dispositivo. A IoT Central também suporta uma abordagem *de primeiro dispositivo* onde a aplicação importa automaticamente um modelo de um repositório quando um dispositivo real se conecta pela primeira vez.

Neste arranque rápido, você escolhe um dispositivo do catálogo do dispositivo para criar um modelo de dispositivo.

Os passos a seguir mostram-lhe como utilizar o catálogo do dispositivo para importar o modelo para um dispositivo **ESP32.** Estes dispositivos enviam telemetria, como temperatura, para a sua aplicação:

1. Para adicionar um novo modelo de dispositivo, selecione **+ Novo** na página **de modelos do dispositivo.**

1. Na página **de tipo Select,** desloque-se para baixo até encontrar o **azulejo do Kit IoT ESP32-Azure** na secção Utilizar uma secção **de modelo de dispositivo pré-configurada.**

1. Selecione o **azulejo do Kit IoT ESP32-Azure** e, em seguida, selecione **Seguinte: Revisão**.

1. Na página **'Revisão',** **selecione Criar**.

1. Após alguns segundos, pode ver o seu novo modelo de dispositivo:

    :::image type="content" source="media/quick-create-simulated-device/devkit-template.png" alt-text="Screenshot mostrando o modelo do dispositivo para dispositivo ESP32":::

    O nome do modelo é **Controlador de Sensores.** O modelo inclui componentes como **o Controlador de Sensores,** **SensorTemp** e **interface de informação do dispositivo.** Os componentes definem as capacidades de um dispositivo ESP32. As capacidades incluem a telemetria, propriedades e comandos.

### <a name="add-cloud-properties"></a>Adicionar propriedades da cloud

Um modelo de dispositivo pode incluir propriedades na nuvem. As propriedades em nuvem só existem na aplicação IoT Central e nunca são enviadas para, ou recebidas de um dispositivo. Para adicionar duas propriedades em nuvem:

1. Selecione **Cloud Properties** e, em seguida, **+ Adicionar propriedade em nuvem**. Utilize as informações na tabela seguinte para adicionar duas propriedades de nuvem ao modelo do seu dispositivo:

    | Nome a Apresentar      | Tipo semântico | Esquema |
    | ----------------- | ------------- | ------ |
    | Data da Última Assistência | Nenhum          | Data   |
    | Nome do Cliente     | Nenhum          | String |

1. **Selecione Guardar** para guardar as suas alterações:

    :::image type="content" source="media/quick-create-simulated-device/cloud-properties.png" alt-text="Screenshot mostrando duas propriedades de nuvem":::

## <a name="views"></a>Vistas

Pode personalizar a aplicação para exibir informações relevantes sobre o dispositivo. As personalizações permitem que outros gerem os dispositivos ligados à aplicação. Pode criar dois tipos de vistas para interagir com dispositivos:

* Formulários para visualizar e editar propriedades de dispositivos e nuvem.
* Dashboards para visualizar dispositivos, incluindo a telemetria que enviam.

### <a name="default-views"></a>Vistas padrão

As vistas padrão são uma forma rápida de começar a visualizar as informações importantes do dispositivo. Pode ter até três vistas padrão geradas para o seu modelo de dispositivo:

* A visualização **de Comandos** permite-lhe enviar comandos para o seu dispositivo.
* A **vista geral** utiliza gráficos e métricas para exibir telemetria do dispositivo.
* A vista **Sobre** exibe propriedades do dispositivo.

Selecione o nó **Vistas** no modelo do dispositivo. Pode ver que o IoT Central gerou uma **visão geral**, uma vista **Sobre** e uma vista **de Dados Brutos** para si quando adicionou o modelo.

Para adicionar um novo formulário para gerir o dispositivo:

1. Selecione o nó **Vistas** e, em seguida, selecione o **dispositivo de edição e** o azulejo de dados em nuvem para adicionar uma nova vista.

1. Altere o nome do formulário para **Gerir o dispositivo**.

1. Selecione as propriedades de nuvem **de nome do cliente** e última data de **serviço** e a propriedade **Temperatura Alvo.** Em seguida, **selecione Secção adicionar**:

    :::image type="content" source="media/quick-create-simulated-device/new-form.png" alt-text="Screenshot mostrando novo formulário adicionado ao modelo do dispositivo":::

1. **Selecione Guardar** para guardar o seu novo formulário.

## <a name="publish-device-template"></a>Publique o modelo do dispositivo

Antes de criar um dispositivo simulado, ou ligar um dispositivo real, tem de publicar o modelo do dispositivo. Embora a IoT Central tenha publicado o modelo quando o criou pela primeira vez, tem de publicar a versão atualizada.

Para publicar um modelo de dispositivo:

1. Navegue para o modelo do dispositivo **do controlador** de sensor a partir da página **de modelos do dispositivo.**

1. Selecione **Publicar** na barra de comando no topo da página.

1. No diálogo que aparece, **selecione Publicar**.

Depois de publicar um modelo de dispositivo, é visível na página **dispositivos.** Num modelo de dispositivo publicado, não é possível editar um modelo de dispositivo sem criar uma nova versão. No entanto, pode modificar propriedades em nuvem, personalizações e vistas num modelo de dispositivo publicado sem ver versão. Depois de efetuar quaisquer alterações, **selecione Publicar**  para empurrar essas alterações para dispositivos reais e simulados para usar.

## <a name="add-a-simulated-device"></a>Adicionar um dispositivo simulado

Para adicionar um dispositivo simulado à sua aplicação, utilize o modelo de dispositivo **ESP32** que criou.

1. Para adicionar um novo dispositivo, escolha **dispositivos** no painel esquerdo. O separador **Dispositivos** mostra **todos os dispositivos** e o modelo do dispositivo **do controlador de sensores** para o dispositivo ESP32. Selecione **o controlador de sensores**.

1. Para adicionar um dispositivo DevKit simulado, selecione **+ Novo**. Utilize o **ID do dispositivo** sugerido ou introduza o seu próprio. Um ID do dispositivo pode conter letras, números e `-` o caráter. Também pode introduzir um nome para o seu novo dispositivo. Certifique-se de que **este dispositivo** está definido para **Sim** e, em seguida, selecione **Criar**.

    :::image type="content" source="media/quick-create-simulated-device/simulated-device.png" alt-text="Screenshot que mostra o dispositivo simulado do Controlador de Sensor":::

Agora pode interagir com as vistas que criaram anteriormente usando dados simulados:

1. Selecione o seu dispositivo simulado na página **dispositivos**

    * A **vista geral** mostra um enredo da telemetria simulada:

        :::image type="content" source="media/quick-create-simulated-device/simulated-telemetry.png" alt-text="Screenshot mostrando página geral para dispositivo simulado":::

    * A vista **Sobre** mostra valores de propriedade.

    * A visualização **de Comandos** permite executar comandos, tais como **reiniciar** o dispositivo.

    * A vista **de dispositivos Manage** é a forma que criou para gerir o dispositivo.

    * A **visão de dados raw** permite-lhe ver os valores brutos de telemetria e propriedade enviados pelo dispositivo. Esta vista é útil para depurar dispositivos.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar um modelo de dispositivo **do Controlador de Sensor** para um dispositivo ESP32 e adicionou um dispositivo simulado à sua aplicação.

Para saber mais sobre dispositivos de monitorização ligados à sua aplicação, continue a iniciar rapidamente:

> [!div class="nextstepaction"]
> [Configurar regras e ações](./quick-configure-rules.md)
