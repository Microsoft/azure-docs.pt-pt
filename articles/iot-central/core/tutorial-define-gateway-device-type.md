---
title: Defina um novo tipo de dispositivo gateway em Azure IoT Central [ Central De entrada de gateways] Microsoft Docs
description: Este tutorial mostra-lhe, como construtor, como definir um novo tipo de dispositivo ioT gateway na sua aplicação Azure IoT Central.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 8020abf3f8ab153d0143ff50d837ebcfbf5bdfba
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77423747"
---
# <a name="define-a-new-iot-gateway-device-type-in-your-azure-iot-central-application"></a>Defina um novo tipo de dispositivo de gateway IoT na sua aplicação Azure IoT Central

Este tutorial mostra-lhe, como construtor, como usar um modelo de dispositivo de gateway para definir um dispositivo de gateway na sua aplicação IoT Central. Em seguida, configura vários dispositivos a jusante que se ligam à sua aplicação IoT Central através do dispositivo gateway. 

Neste tutorial, você cria um modelo de dispositivo de gateway **Smart Building.** Um dispositivo de gateway **Smart Building** tem relações com outros dispositivos a jusante.

![Diagrama de relação entre dispositivo de gateway e dispositivos a jusante](./media/tutorial-define-gateway-device-type/gatewaypattern.png)

Além de permitir que os dispositivos a jusante se comuniquem com a sua aplicação IoT Central, um dispositivo gateway também pode:

* Envie a sua própria telemetria, como a temperatura.
* Responda a atualizações de propriedade reempreensíveis feitas por um operador. Por exemplo, um operador pode alterar o intervalo de envio de telemetria.
* Responda a comandos, tais como reiniciar o dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, é necessário [criar uma aplicação Azure IoT Central.](./quick-deploy-iot-central.md)

## <a name="create-downstream-device-templates"></a>Criar modelos de dispositivo a jusante

Este tutorial utiliza modelos de dispositivos para um dispositivo **Sensor S1** e um dispositivo **Sensor de Ocupação RS40** para gerar dispositivos a jusante simulados.

Para criar um modelo de dispositivo para um dispositivo **S1 Sensor:**

1. No painel esquerdo, selecione **Modelos de dispositivo**. Em seguida, selecione **+** para começar a adicionar o modelo.

1. Desloque-se para baixo até poder ver o azulejo do dispositivo **Sensor S1.** Selecione o azulejo e, em seguida, selecione **Seguinte: Personalizar**.

1. Na página **'Rever',** selecione **Criar** para adicionar o modelo do dispositivo à sua aplicação. 

Para criar um modelo de dispositivo para um dispositivo sensor de**ocupação RS40:**

1. No painel esquerdo, selecione **Modelos de dispositivo**. Em seguida, selecione **+** para começar a adicionar o modelo.

1. Desloque-se para baixo até poder ver o azulejo do dispositivo *** RS40 Ocupação Sensor.** Selecione o azulejo e, em seguida, selecione **Seguinte: Personalizar**.

1. Na página **'Rever',** selecione **Criar** para adicionar o modelo do dispositivo à sua aplicação. 

Agora tem modelos de dispositivo para os dois tipos de dispositivos a jusante:

![Modelos de dispositivos para dispositivos a jusante](./media/tutorial-define-gateway-device-type/downstream-device-types.png)


## <a name="create-a-gateway-device-template"></a>Criar um modelo de dispositivo de gateway

Neste tutorial cria-se um modelo de dispositivo para um dispositivo de gateway de raiz. Usa este modelo mais tarde para criar um dispositivo de gateway simulado na sua aplicação.

Para adicionar um novo modelo de dispositivo de gateway à sua aplicação:

1. No painel esquerdo, selecione **Modelos de dispositivo**. Em seguida, selecione **+** para começar a adicionar o modelo.

1. Na página do **modelo Select,** selecione o azulejo **do Dispositivo IoT** e, em seguida, selecione **Seguinte: Personalizar**.

1. Na página personalizar o **dispositivo,** selecione a caixa de verificação do **dispositivo Gateway.**

1. Na página **'Rever',** selecione **Criar**. 

1. Introduza o **dispositivo de gateway smart building** como o nome do modelo.

1. Na página Criar um modelo de **capacidade,** selecione o azulejo **Personalizado.**

1. Selecione **+** para adicionar uma interface.  Escolha a interface padrão de Informação do **Dispositivo.**

### <a name="add-relationships"></a>Adicionar relacionamentos

Em seguida, adicione relações aos modelos para os modelos do dispositivo a jusante:

1. No modelo de dispositivo de **gateway Smart Building,** selecione **Relationships**.

1. Selecione **+ Adicionar relacionamento**. Introduza o **Sensor Ambiental** como nome de exibição e selecione **O Sensor S1** como alvo.

1. Selecione **+ Adicione** a relação novamente. Introduza o **Sensor de Ocupação** como nome de exibição e selecione o Sensor de Ocupação **RS40** como alvo.

1. Selecione **Guardar**.

![Modelo de dispositivo de gateway de construção inteligente, mostrando relacionamentos](./media/tutorial-define-gateway-device-type/relationships.png)

### <a name="add-cloud-properties"></a>Adicione propriedades da nuvem

Um modelo de dispositivo de gateway pode incluir propriedades na nuvem. As propriedades da nuvem só existem na aplicação IoT Central, e nunca são enviadas para, ou recebidas de um dispositivo.

Para adicionar propriedades em nuvem ao modelo de dispositivo de **gateway Smart Building.**

1. No modelo de dispositivo de **gateway Smart Building,** selecione **propriedades cloud**.

1.  Utilize as informações na tabela seguinte para adicionar duas propriedades na nuvem ao seu modelo de dispositivo de gateway.

    | Nome a apresentar      | Tipo semântico | Esquema |
    | ----------------- | ------------- | ------ |
    | Data da Última Assistência | Nenhuma          | Data   |
    | Nome do cliente     | Nenhuma          | Cadeia |

2. Selecione **Guardar**.

### <a name="create-views"></a>Criar vistas

Como construtor, pode personalizar a aplicação para apresentar informações relevantes sobre o dispositivo de sensor ambiental a um operador. As suas personalizações permitem ao operador gerir os dispositivos de sensores ambientais ligados à aplicação. Pode criar dois tipos de pontos de vista para um operador utilizar para interagir com dispositivos:

* Formulários para visualizar e editar dispositivo e propriedades em nuvem.
* Dashboards para visualizar dispositivos.

Para gerar as vistas padrão para o modelo de dispositivo de **gateway Smart Building:**

1. No modelo de dispositivo de **gateway Smart Building,** selecione **Views**.

1. Selecione **Generate versões predefinidas** e certifique-se de que todas as opções são selecionadas.

1. **Selecione Gere a visão padrão do dashboard**.

## <a name="publish-the-device-template"></a>Publique o modelo do dispositivo

Antes de poder criar um dispositivo de gateway simulado ou ligar um verdadeiro dispositivo de gateway, tem de publicar o modelo do seu dispositivo.

Para publicar o modelo do dispositivo gateway:

1. Selecione o modelo de dispositivo de **gateway smart building** na página de modelos do **dispositivo.**

2. Selecione **Publicar**.

3. Na caixa de diálogo **'Modelo** de Publicação', escolha **Publicar**.

Depois de publicado um modelo de dispositivo, é visível na página **dispositivos** e no operador. Num modelo de dispositivo publicado, não é possível editar um modelo de capacidade de dispositivo sem criar uma nova versão. No entanto, pode fazer atualizações para propriedades na nuvem, personalizações e visualizações, num modelo de dispositivo publicado. Estas atualizações não fazem com que seja criada uma nova versão. Depois de então fazer quaisquer alterações, selecione **Publicar** para empurrar essas alterações para o seu operador.

## <a name="create-the-simulated-devices"></a>Criar os dispositivos simulados

Este tutorial utiliza dispositivos a jusante simulados e um dispositivo de gateway simulado.

Para criar um dispositivo de gateway simulado:

1. Na página **dispositivos,** selecione o **dispositivo de gateway Smart Building** na lista de modelos do dispositivo.

1. Selecione **+** para começar a adicionar um novo dispositivo.

1. Mantenha o **id do dispositivo** gerado e o nome do **dispositivo**. Certifique-se de que o interruptor **simulado** está **ligado**. Selecione **Criar**.

Para criar um dispositivo a jusante simulado:

1. Na página **dispositivos,** selecione **Sensor de Ocupação RS40** na lista de modelos do dispositivo.

1. Selecione **+** para começar a adicionar um novo dispositivo.

1. Mantenha o **id do dispositivo** gerado e o nome do **dispositivo**. Certifique-se de que o interruptor **simulado** está **ligado**. Selecione **Criar**.

1. Na página **Dispositivos,** selecione **Sensor S1** na lista de modelos do dispositivo.

1. Selecione **+** para começar a adicionar um novo dispositivo.

1. Mantenha o **id do dispositivo** gerado e o nome do **dispositivo**. Certifique-se de que o interruptor **simulado** está **ligado**. Selecione **Criar**.

![Dispositivos simulados na sua aplicação](./media/tutorial-define-gateway-device-type/simulated-devices.png)

### <a name="add-downstream-device-relationships-to-a-gateway-device"></a>Adicione as relações do dispositivo a jusante a um dispositivo de gateway

Agora que tem os dispositivos simulados na sua aplicação, pode criar as relações entre os dispositivos a jusante e o dispositivo gateway:

1. Na página **dispositivos,** selecione **Sensor S1** na lista de modelos do dispositivo e, em seguida, selecione o seu dispositivo s1 **sensor** simulado.

1. Selecione **Ligar ao gateway**.

1. No **Connect a um** diálogo de gateway, selecione o modelo de dispositivo de **gateway Smart Building** e, em seguida, selecione a instância simulada que criou anteriormente.

1. Selecione **Juntar**.

1. Na página **dispositivos,** selecione sensor de **ocupação RS40** na lista de modelos do dispositivo e, em seguida, selecione o seu dispositivo de sensor de **ocupação RS40** simulado.

1. Selecione **Ligar ao gateway**.

1. No **Connect a um** diálogo de gateway, selecione o modelo de dispositivo de **gateway Smart Building** e, em seguida, selecione a instância simulada que criou anteriormente.

1. Selecione **Juntar**.

Ambos os seus dispositivos a jusante simulados estão agora ligados ao seu dispositivo de gateway simulado. Se navegar para a vista **de Dispositivos Downstream** para o seu dispositivo gateway, pode ver os dispositivos a jusante relacionados:

![Visão de dispositivos a jusante](./media/tutorial-define-gateway-device-type/downstream-device-view.png)

Selecione um modelo de dispositivo de gateway e uma instância do dispositivo gateway e selecione **Juntar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

* Crie um novo portal IoT como modelo de dispositivo.
* Criar propriedades em nuvem.
* Criar personalizações.
* Defina uma visualização para a telemetria do dispositivo.
* Adicione relacionamentos.
* Publique o modelo do seu dispositivo.

Em seguida, pode:

> [!div class="nextstepaction"]
> [Ligar um dispositivo](tutorial-connect-pnp-device.md)
