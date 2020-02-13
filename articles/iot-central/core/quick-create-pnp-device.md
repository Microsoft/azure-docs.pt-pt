---
title: Quickstart - Adicione um dispositivo simulado à Central Azure IoT
description: Este quickstart mostra como criar um modelo de dispositivo e adicionar um dispositivo simulado à sua aplicação IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 489bf81388c1bb889756d54957e791282054f0f7
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169628"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Quickstart: Adicione um dispositivo simulado à sua aplicação IoT Central

*Este artigo aplica-se a operadores, construtores e administradores.*

Um modelo de dispositivo define as capacidades de um dispositivo que se conecta à sua aplicação IoT Central. As capacidades incluem telemetria que o dispositivo envia, propriedades do dispositivo, e os comandos a que um dispositivo responde. A partir de um modelo de dispositivo, um construtor ou operador pode adicionar dispositivos reais e simulados a uma aplicação. Os dispositivos simulados são úteis para testar o comportamento da sua aplicação IoT Central antes de ligar dispositivos reais.

Neste arranque rápido, adicione um modelo de dispositivo para uma placa *MXChip IoT DevKit* (DevKit) e crie um dispositivo simulado. Para completar este arranque rápido não precisa de um dispositivo real, trabalha com uma simulação do dispositivo. Um dispositivo DevKit:

* Envia telemetria como a temperatura.
* Relata propriedades específicas do dispositivo, tais como o nível de luminosidade.
* Responde a comandos como ligar e desligar.
* Relata propriedades genéricas do dispositivo, tais como versão firmware e número de série.

## <a name="prerequisites"></a>Pré-requisitos

Complete a [aplicação Create a Azure IoT Central](./quick-deploy-iot-central.md) para criar uma aplicação IoT Central utilizando o modelo de **aplicação Personalizado > Custom.**

## <a name="create-a-template"></a>Criar um modelo

Como construtor, pode criar e editar modelos de dispositivos na sua aplicação IoT Central. Depois de publicar um modelo de dispositivo, pode gerar dispositivo simulado ou ligar dispositivos reais a partir do modelo do dispositivo. Os dispositivos simulados permitem testar o comportamento da sua aplicação antes de ligar um dispositivo real.

Para adicionar um novo modelo de dispositivo à sua aplicação, selecione o separador **Modelos** de Dispositivo no painel esquerdo.

![Página de modelos de dispositivo](./media/quick-create-pnp-device/devicedefinitions.png)

Um modelo de dispositivo inclui um modelo de capacidade do dispositivo que define a telemetria que o dispositivo envia, propriedades do dispositivo e os comandos a que o dispositivo responde.

### <a name="add-a-device-capability-model"></a>Adicione um modelo de capacidade do dispositivo

Existem várias opções para adicionar um modelo de capacidade de dispositivo à sua aplicação IoT Central. Pode criar um modelo de raiz, importar um modelo a partir de um ficheiro ou selecionar um dispositivo a partir do catálogo do dispositivo. A IoT Central também suporta uma abordagem *de primeiro dispositivo* onde importa automaticamente um modelo de um repositório quando um dispositivo se conecta pela primeira vez. Neste arranque rápido, escolhe um dispositivo do catálogo do dispositivo para importar o seu modelo de capacidade de dispositivo.

Os seguintes passos mostram como usar o catálogo do dispositivo para importar o modelo de capacidade para um dispositivo **MXChip IoT DevKit.** Estes dispositivos enviam telemetria, como temperatura, para a sua aplicação:

1. Para adicionar um novo modelo de dispositivo, selecione **+** na página de modelos do **Dispositivo.**

1. Na página do **modelo Select,** desloque-se para baixo até encontrar o azulejo **MXChip IoT DevKit.**

1. Selecione o azulejo **MXChip IoT DevKit** e, em seguida, selecione **Next: Customize**.

1. Na página **'Rever',** selecione **Criar**.

1. Após alguns segundos, pode ver o seu novo modelo de dispositivo:

    ![Modelo de dispositivo MXChip IoT DevKit](./media/quick-create-pnp-device/devkit-template.png)

    O modelo de capacidade MXChip IoT DevKit inclui interfaces como **mxchip_sensor,** **mxchip_settings**e **Informações do Dispositivo.** As interfaces definem as capacidades de um dispositivo MXChip IoT DevKit. As capacidades incluem a telemetria que um dispositivo envia, as propriedades que um dispositivo reporta, e os comandos a que um dispositivo responde.

### <a name="add-cloud-properties"></a>Adicione propriedades da nuvem

Um modelo de dispositivo pode incluir propriedades na nuvem. As propriedades da nuvem só existem na aplicação IoT Central e nunca são enviadas para, ou recebidas de um dispositivo.

1. Selecione **Cloud Properties** **e,** em seguida, + Adicione a propriedade em nuvem . Utilize as informações na tabela seguinte para adicionar duas propriedades na nuvem ao seu modelo de dispositivo:

    | Nome a Apresentar      | Tipo semântico | Esquema |
    | ----------------- | ------------- | ------ |
    | Data da Última Assistência | Nenhuma          | Data   |
    | Nome do cliente     | Nenhuma          | Cadeia |

1. Selecione **Guardar** para guardar as alterações:

    ![Propriedades em nuvem](media/quick-create-pnp-device/cloudproperties.png)

## <a name="views"></a>Vistas

Como construtor, pode personalizar a aplicação para apresentar informações relevantes sobre o dispositivo a um operador. As suas personalizações permitem ao operador gerir os dispositivos ligados à aplicação. Pode criar dois tipos de pontos de vista para um operador utilizar para interagir com dispositivos:

* Formulários para visualizar e editar dispositivo e propriedades em nuvem.
* Dashboards para visualizar dispositivos, incluindo a telemetria que enviam.

### <a name="default-views"></a>Vistas padrão

As vistas padrão são uma forma rápida de começar a visualizar as informações importantes do dispositivo. Pode ter até três visualizações predefinidas geradas para o seu modelo de dispositivo:

* A vista **Comandos** permite ao seu operador enviar comandos para o seu dispositivo.
* A visão **geral** utiliza gráficos e métricas para exibir a telemetria do dispositivo.
* A visão **Sobre** exibe propriedades do dispositivo.

Selecione o nó **views** no modelo do dispositivo. Você pode ver que IoT Central gerou uma **visão geral** e uma visão **sobre** para você quando você adicionou o modelo.

Para adicionar um novo formulário **de dispositivo Manage** que um operador pode usar para gerir o dispositivo:

1. Selecione o nó **Views** e, em seguida, selecione o **dispositivo de edição e** o azulejo de dados em nuvem para adicionar uma nova vista.

1. Altere o nome do formulário para Gerir o **dispositivo**.

1. Selecione as propriedades da cloud nome **do cliente** e **da última data** de serviço e a propriedade **Fan Speed.** Em seguida, **selecione a secção Adicionar**:

    ![Criar nova forma](media/quick-create-pnp-device/new-form.png)

1. Selecione **Guardar** para guardar o seu novo formulário.

## <a name="publish-device-template"></a>Publicar modelo de dispositivo

Antes de poder criar um dispositivo simulado ou ligar um dispositivo real, tem de publicar o modelo do seu dispositivo. Embora a IoT Central tenha publicado o modelo quando o criou pela primeira vez, tem de publicar a versão atualizada.

Para publicar um modelo de dispositivo:

1. Vá para o modelo do seu dispositivo a partir da página modelos de **dispositivo.**

1. **Selecione Publicar:**

    ![Modelo publicado](media/quick-create-pnp-device/publishedmodel.png)

1. No modelo publicar este modelo de dispositivo para o diálogo **da aplicação,** selecione **Publicar**. 

Depois de publicar um modelo de dispositivo, é visível na página **dispositivos.** Num modelo de dispositivo publicado, não é possível editar um modelo de capacidade de dispositivo sem criar uma nova versão. No entanto, pode fazer atualizações para propriedades na nuvem, personalizações e visualizações, num modelo de dispositivo publicado sem versão. Depois de então fazer quaisquer alterações, selecione **Publicar** para empurrar essas alterações para o seu operador.

## <a name="add-a-simulated-device"></a>Adicionar um dispositivo simulado

Para adicionar um dispositivo simulado à sua aplicação, utiliza o modelo de dispositivo **MXChip IoT DevKit** que criou.

1. Para adicionar um novo dispositivo como operador, escolha **dispositivos** no painel esquerdo. O separador **Dispositivos** mostra **todos os dispositivos** e o modelo de dispositivo **MXChip IoT DevKit.** Selecione **MXChip IoT DevKit**.

1. Para adicionar um dispositivo DevKit simulado, selecione **+** . Utilize o **ID** do dispositivo sugerido ou introduza o seu próprio **ID do dispositivo**minúsculo . Também pode introduzir um nome para o seu novo dispositivo. Certifique-se de que o toggle **simulado** está **ligado** e, em seguida, selecione **Criar**.

    ![Dispositivo simulado](./media/quick-create-pnp-device/simulated-device.png)

Agora pode interagir com as vistas que foram criadas pelo construtor para o modelo do dispositivo usando dados simulados:

1. Selecione o seu dispositivo simulado na página **dispositivos.**

1. A visão **geral** mostra um enredo da telemetria simulada:

    ![Vista geral](./media/quick-create-pnp-device/simulated-telemetry.png)

1. A vista **Sobre** mostra valores de propriedade, incluindo as propriedades em nuvem que você adicionou à vista.

1. A visão **comandos** permite-lhe executar comandos, tais como **piscar** no dispositivo.

1. A vista **'Gerir dispositivos'** é o formulário que criou para o operador gerir o dispositivo.

## <a name="use-a-simulated-device-to-improve-views"></a>Utilize um dispositivo simulado para melhorar as vistas

Depois de criar um novo dispositivo simulado, o construtor pode usar este dispositivo para continuar a melhorar e basear-se nas vistas para o modelo do dispositivo.

1. Escolha **os modelos do dispositivo** no painel esquerdo e selecione o modelo **MXChip IoT DevKit.**

1. Selecione qualquer uma das vistas que gostaria de editar ou crie uma nova vista. Selecione o dispositivo de **pré-visualização Configure**e, em seguida, **selecione a partir de um dispositivo**de execução . Aqui pode optar por não ter nenhum dispositivo de pré-visualização, um dispositivo real configurado para testes ou um dispositivo existente que adicionou à IoT Central.

1. Escolha o seu dispositivo simulado na lista. Em seguida, selecione **Aplicar**. Agora pode ver o mesmo dispositivo simulado no seu modelo de dispositivo visualiza ndo experiência de construção. Esta vista é útil para gráficos e outras visualizações.

    ![Configurar dispositivo de pré-visualização](./media/quick-create-pnp-device/configure-preview.png)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar um modelo de dispositivo **MXChip IoT DevKit** e adicionar um dispositivo simulado à sua aplicação.

Para saber mais sobre dispositivos de monitorização ligados à sua aplicação, continue a iniciar rapidamente:

> [!div class="nextstepaction"]
> [Configurar regras e ações](./quick-configure-rules.md)
