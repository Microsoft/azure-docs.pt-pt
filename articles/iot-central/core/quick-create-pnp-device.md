---
title: Início rápido-adicionar um dispositivo simulado ao Azure IoT Central
description: Este guia de início rápido mostra como criar um modelo de dispositivo e adicionar um dispositivo simulado ao seu aplicativo IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/10/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: cb8f0c806435a6c623d14e50263844db7aac61bb
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027763"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Quickstart: Adicione um dispositivo simulado à sua aplicação IoT Central

*Este artigo aplica-se a operadores, construtores e administradores.*

Um modelo de dispositivo define os recursos de um dispositivo que se conecta ao seu aplicativo IoT Central. Os recursos incluem telemetria que o dispositivo envia, as propriedades do dispositivo e os comandos que um dispositivo responde. De um modelo de dispositivo, um construtor ou operador pode adicionar dispositivos reais e simulados a um aplicativo. Os dispositivos simulados são úteis para testar o comportamento do seu aplicativo IoT Central antes de conectar dispositivos reais.

Neste arranque rápido, adicione um modelo de dispositivo para uma placa *MXChip IoT DevKit* (DevKit) e crie um dispositivo simulado. Para completar este arranque rápido não precisa de um dispositivo real, trabalha com uma simulação do dispositivo. Um dispositivo DevKit:

* Envia telemetria como temperatura.
* Relata propriedades específicas do dispositivo, como o nível de brilho.
* Responde a comandos como ativar e desativar.
* Relata propriedades genéricas do dispositivo, como a versão do firmware e o número de série.

## <a name="prerequisites"></a>Pré-requisitos

Complete a [aplicação Create a Azure IoT Central](./quick-deploy-iot-central.md) para criar uma aplicação IoT Central utilizando o modelo de **aplicação Personalizado > Custom.**

## <a name="create-a-template"></a>Criar um modelo

Como construtor, pode criar e editar modelos de dispositivos na sua aplicação IoT Central. Depois de publicar um modelo de dispositivo, pode gerar dispositivo simulado ou ligar dispositivos reais a partir do modelo do dispositivo. Os dispositivos simulados permitem testar o comportamento do seu aplicativo antes de conectar um dispositivo real.

Para adicionar um novo modelo de dispositivo à sua aplicação, selecione o separador **Modelos** de Dispositivo no painel esquerdo.

![Página modelos de dispositivo](./media/quick-create-pnp-device/devicedefinitions.png)

Um modelo de dispositivo inclui um modelo de capacidade do dispositivo que define a telemetria que o dispositivo envia, propriedades do dispositivo e os comandos a que o dispositivo responde.

### <a name="add-a-device-capability-model"></a>Adicionar um modelo de capacidade de dispositivo

Existem várias opções para adicionar um modelo de capacidade de dispositivo à sua aplicação IoT Central. Pode criar um modelo de raiz, importar um modelo a partir de um ficheiro ou selecionar um dispositivo a partir do catálogo do dispositivo. A IoT Central também suporta uma abordagem *de primeiro dispositivo* onde importa automaticamente um modelo de um repositório quando um dispositivo se conecta pela primeira vez. Neste arranque rápido, escolhe um dispositivo do catálogo do dispositivo para importar o seu modelo de capacidade de dispositivo.

Os seguintes passos mostram como usar o catálogo do dispositivo para importar o modelo de capacidade para um dispositivo **MXChip IoT DevKit.** Esses dispositivos enviam telemetria, como temperatura, para seu aplicativo:

1. Para adicionar um novo modelo de dispositivo, selecione **+** na página **modelos de dispositivo** .

1. Na página do **modelo Select,** desloque-se para baixo até encontrar o azulejo **MXChip IoT DevKit.**

1. Selecione o azulejo **MXChip IoT DevKit** e, em seguida, selecione **Next: Customize**.

1. Na página **'Rever',** selecione **Criar**.

1. Após alguns segundos, pode ver o seu novo modelo de dispositivo:

    ![Modelo de dispositivo MXChip IoT DevKit](./media/quick-create-pnp-device/devkit-template.png)

    O modelo de capacidade MXChip IoT DevKit inclui interfaces como **mxchip_sensor,** **mxchip_settings**e **Informações do Dispositivo.** As interfaces definem as capacidades de um dispositivo MXChip IoT DevKit. Os recursos incluem a telemetria que um dispositivo envia, as propriedades que um dispositivo relata e os comandos aos quais um dispositivo responde.

### <a name="add-cloud-properties"></a>Adicionar propriedades de nuvem

Um modelo de dispositivo pode incluir propriedades de nuvem. As propriedades de nuvem existem somente no aplicativo IoT Central e nunca são enviadas ou recebidas de um dispositivo.

1. Selecione **Propriedades de nuvem** e **+ Adicionar Propriedade de nuvem**. Utilize as informações na tabela seguinte para adicionar duas propriedades na nuvem ao seu modelo de dispositivo:

    | Nome a Apresentar      | Tipo semântico | Esquema |
    | ----------------- | ------------- | ------ |
    | Data da Última Assistência | Nenhuma          | Date   |
    | Nome do cliente     | Nenhuma          | Cadeia |

1. Selecione **salvar** para salvar as alterações:

    ![Propriedades de nuvem](media/quick-create-pnp-device/cloudproperties.png)

## <a name="views"></a>Vistas

Como construtor, pode personalizar a aplicação para apresentar informações relevantes sobre o dispositivo a um operador. As suas personalizações permitem ao operador gerir os dispositivos ligados à aplicação. Você pode criar dois tipos de modos de exibição para que um operador Use para interagir com dispositivos:

* Formulários para exibir e editar propriedades de dispositivo e de nuvem.
* Dashboards para visualizar dispositivos, incluindo a telemetria que enviam.

### <a name="default-views"></a>Vistas padrão

As vistas padrão são uma forma rápida de começar a visualizar as informações importantes do dispositivo. Você pode ter até três exibições padrão geradas para seu modelo de dispositivo:

* A exibição **comandos** permite que o operador despache comandos para seu dispositivo.
* O modo de exibição de **visão geral** usa gráficos e métricas para exibir a telemetria do dispositivo.
* A exibição **sobre** exibe as propriedades do dispositivo.

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

1. Vá para o modelo de dispositivo na página **modelos de dispositivo** .

1. **Selecione Publicar:**

    ![Modelo publicado](media/quick-create-pnp-device/publishedmodel.png)

1. No modelo publicar este modelo de dispositivo para o diálogo **da aplicação,** selecione **Publicar**. 

Depois de publicar um modelo de dispositivo, é visível na página **dispositivos.** Em um modelo de dispositivo publicado, você não pode editar um modelo de capacidade de dispositivo sem criar uma nova versão. No entanto, você pode fazer atualizações nas propriedades de nuvem, personalizações e exibições, em um modelo de dispositivo publicado sem controle de versão. Depois de fazer alterações, selecione **publicar** para enviar por push essas alterações para o operador.

## <a name="add-a-simulated-device"></a>Adicionar um dispositivo simulado

Para adicionar um dispositivo simulado à sua aplicação, utiliza o modelo de dispositivo **MXChip IoT DevKit** que criou.

1. Para adicionar um novo dispositivo como um operador, escolha **dispositivos** no painel esquerdo. O separador **Dispositivos** mostra **todos os dispositivos** e o modelo de dispositivo **MXChip IoT DevKit.** Selecione **MXChip IoT DevKit**.

1. Para adicionar um dispositivo DevKit simulado, selecione **+** . Use a **ID de dispositivo** sugerida ou insira sua própria **ID de dispositivo**em minúsculas. Você também pode inserir um nome para o novo dispositivo. Certifique-se de que o alternância **simulado** está **ligado** e, em seguida, selecione **Criar**.

    ![Dispositivo de simulação](./media/quick-create-pnp-device/simulated-device.png)

Agora pode interagir com as vistas que foram criadas pelo construtor para o modelo do dispositivo usando dados simulados:

1. Selecione o seu dispositivo simulado na página **dispositivos.**

1. A visão **geral** mostra um enredo da telemetria simulada:

    ![Vista geral](./media/quick-create-pnp-device/simulated-telemetry.png)

1. A vista **Sobre** mostra valores de propriedade, incluindo as propriedades em nuvem que você adicionou à vista.

1. A visão **comandos** permite-lhe executar comandos, tais como **piscar** no dispositivo.

1. A vista **'Gerir dispositivos'** é o formulário que criou para o operador gerir o dispositivo.

## <a name="use-a-simulated-device-to-improve-views"></a>Usar um dispositivo simulado para melhorar as exibições

Depois de criar um novo dispositivo simulado, o construtor pode usar este dispositivo para continuar a melhorar e criar as exibições para o modelo de dispositivo.

1. Escolha **os modelos do dispositivo** no painel esquerdo e selecione o modelo **MXChip IoT DevKit.**

1. Selecione qualquer uma das exibições que você deseja editar ou crie uma nova exibição. Selecione o dispositivo de **pré-visualização Configure**e, em seguida, **selecione a partir de um dispositivo**de execução . Aqui pode optar por não ter nenhum dispositivo de pré-visualização, um dispositivo real configurado para testes ou um dispositivo existente que adicionou à IoT Central.

1. Escolha o dispositivo simulado na lista. Em seguida, selecione **Aplicar**. Agora você pode ver o mesmo dispositivo simulado em seus modos de exibição de modelo de dispositivo experiência de criação. Essa exibição é útil para gráficos e outras visualizações.

    ![Configurar o dispositivo de visualização](./media/quick-create-pnp-device/configure-preview.png)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar um modelo de dispositivo **MXChip IoT DevKit** e adicionar um dispositivo simulado à sua aplicação.

Para saber mais sobre como monitorar dispositivos conectados ao seu aplicativo, vá para o início rápido:

> [!div class="nextstepaction"]
> [Configurar regras e ações](./quick-configure-rules.md)
