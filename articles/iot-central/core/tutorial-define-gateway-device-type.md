---
title: Tutorial - Defina um novo tipo de dispositivo de gateway em Azure IoT Central | Microsoft Docs
description: Este tutorial mostra-lhe, como construtor, como definir um novo tipo de dispositivo de gateway IoT na sua aplicação Azure IoT Central.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: e42e1791693342592e391a14422b1441c9cbbd31
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832390"
---
# <a name="tutorial---define-a-new-iot-gateway-device-type-in-your-azure-iot-central-application"></a>Tutorial - Defina um novo tipo de dispositivo de gateway IoT na sua aplicação Azure IoT Central

*Este artigo aplica-se a construtores de soluções e desenvolvedores de dispositivos.*

Este tutorial mostra-lhe, como construtor de soluções, como usar um modelo de dispositivo gateway para definir um dispositivo de gateway na sua aplicação IoT Central. Em seguida, configura vários dispositivos a jusante que se ligam à sua aplicação IoT Central através do dispositivo gateway. 

Neste tutorial, você cria um modelo de dispositivo de gateway **smart building.** Um dispositivo **smart building** gateway tem relações com outros dispositivos a jusante.

![Diagrama de relação entre dispositivo de gateway e dispositivos a jusante](./media/tutorial-define-gateway-device-type/gatewaypattern.png)

Além de permitir que os dispositivos a jusante comuniquem com a sua aplicação IoT Central, um dispositivo de gateway também pode:

* Envie a sua própria telemetria, como a temperatura.
* Responda às atualizações de propriedades escritas efetuadas por um operador. Por exemplo, um operador pode alterar o intervalo de envio de telemetria.
* Responda aos comandos, tais como reiniciar o dispositivo.

> [!div class="checklist"]
> Criar modelos de dispositivo a jusante Crie um modelo de dispositivo de gateway Publicar o modelo de dispositivo Criar os dispositivos simulados

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa criar [uma aplicação Azure IoT Central](./quick-deploy-iot-central.md).

## <a name="create-downstream-device-templates"></a>Criar modelos de dispositivo a jusante

Este tutorial utiliza modelos de dispositivo para um dispositivo **S1 Sensor** e um dispositivo **de sensor de ocupação RS40** para gerar dispositivos simulados a jusante.

Para criar um modelo de dispositivo para um dispositivo **S1 Sensor:**

1. No painel esquerdo, selecione **Modelos de Dispositivo .** Em seguida, selecione **+** para começar a adicionar o modelo.

1. Desloque-se para baixo até ver o azulejo do dispositivo **S1 Sensor.** Selecione o azulejo e, em seguida, **selecione Seguinte: Personalize**.

1. Na página **'Rever',** **selecione Criar** para adicionar o modelo do dispositivo à sua aplicação. 

Para criar um modelo de dispositivo para um dispositivo *** RS40 Sensor de Ocupação:**

1. No painel esquerdo, selecione **Modelos de Dispositivo .** Em seguida, selecione **+** para começar a adicionar o modelo.

1. Desloque-se para baixo até ver o azulejo do dispositivo ***RS40 Occupancy Sensor.** Selecione o azulejo e, em seguida, **selecione Seguinte: Personalize**.

1. Na página **'Rever',** **selecione Criar** para adicionar o modelo do dispositivo à sua aplicação. 

Tem agora modelos de dispositivo para os dois tipos de dispositivos a jusante:

![Modelos de dispositivos para dispositivos a jusante](./media/tutorial-define-gateway-device-type/downstream-device-types.png)


## <a name="create-a-gateway-device-template"></a>Criar um modelo de dispositivo de gateway

Neste tutorial cria-se um modelo de dispositivo para um dispositivo de gateway de raiz. Utilize este modelo mais tarde para criar um dispositivo de gateway simulado na sua aplicação.

Para adicionar um novo modelo de dispositivo gateway à sua aplicação:

1. No painel esquerdo, selecione **Modelos de Dispositivo .** Em seguida, selecione **+** para começar a adicionar o modelo.

1. Na página **do modelo Select,** selecione o **azulejo do dispositivo IoT** e, em seguida, selecione **Seguinte: Personalize**.

1. Na página do **dispositivo Personalizar,** selecione a caixa de verificação do **dispositivo Gateway.**

1. Na página **'Revisão',** **selecione Criar**. 

1. Introduza **o dispositivo de gateway smart building** como o nome do modelo.

1. Na página **'Criar um modelo de capacidade',** selecione o azulejo **personalizado.**

1. Selecione **+** para adicionar uma interface.  Escolha a interface padrão informação do **dispositivo.**

### <a name="add-relationships"></a>Adicionar relacionamentos

Em seguida, adicione relacionamentos aos modelos para os modelos de dispositivo a jusante:

1. No modelo do **dispositivo de gateway Smart Building,** selecione **Relacionamentos**.

1. Selecione **+ Adicionar relacionamento**. Introduza o **Sensor Ambiental** como o nome do visor e selecione **o Sensor S1** como alvo.

1. Selecione + Adicione novamente **a relação.** Introduza **o Sensor de Ocupação** como nome de visor e selecione o Sensor de Ocupação **RS40** como alvo.

1. Selecione **Guardar**.

![Modelo de dispositivo de gateway de porta de entrada de edifício inteligente, mostrando relacionamentos](./media/tutorial-define-gateway-device-type/relationships.png)

### <a name="add-cloud-properties"></a>Adicionar propriedades da cloud

Um modelo de dispositivo de gateway pode incluir propriedades na nuvem. As propriedades em nuvem só existem na aplicação IoT Central, e nunca são enviadas para, ou recebidas de um dispositivo.

Para adicionar propriedades de nuvem ao modelo de **dispositivo de gateway smart building.**

1. No modelo do **dispositivo de gateway Smart Building,** selecione propriedades **cloud**.

1.  Utilize as informações na tabela seguinte para adicionar duas propriedades de nuvem ao modelo do dispositivo gateway.

    | Nome a apresentar      | Tipo de semântica | Esquema |
    | ----------------- | ------------- | ------ |
    | Data da Última Assistência | Nenhum          | Data   |
    | Nome do Cliente     | Nenhum          | String |

2. Selecione **Guardar**.

### <a name="create-views"></a>Criar vistas

Como construtor, pode personalizar a aplicação para apresentar informações relevantes sobre o dispositivo sensor ambiental a um operador. As suas personalizações permitem ao operador gerir os dispositivos sensores ambientais ligados à aplicação. Pode criar dois tipos de vista para um operador utilizar para interagir com dispositivos:

* Formulários para visualizar e editar propriedades de dispositivos e nuvem.
* Dashboards para visualizar dispositivos.

Para gerar as vistas padrão para o modelo do **dispositivo de gateway Smart Building:**

1. No modelo do **dispositivo de gateway Smart Building,** selecione **Vistas**.

1. **Selecione Gerar azulejos predefinidos** e certifique-se de que todas as opções estão selecionadas.

1. **Selecione Gerer a visão padrão do painel de instrumentos**.

## <a name="publish-the-device-template"></a>Publicar o modelo de dispositivo

Antes de criar um dispositivo de gateway simulado ou ligar um verdadeiro dispositivo de gateway, tem de publicar o modelo do dispositivo.

Para publicar o modelo do dispositivo gateway:

1. Selecione o modelo do **dispositivo de gateway smart building** a partir da página de **modelos do dispositivo.**

2. Selecione **Publicar**.

3. Na caixa de diálogo **do modelo do dispositivo,** escolha **publicar**.

Após a publicação de um modelo de dispositivo, é visível na página **dispositivos** e no operador. Num modelo de dispositivo publicado, não é possível editar um modelo de dispositivo sem criar uma nova versão. No entanto, pode fazer atualizações para propriedades na nuvem, personalizações e vistas, num modelo de dispositivo publicado. Estas atualizações não fazem com que uma nova versão seja criada. Depois de escoar quaisquer alterações, **selecione Publicar**  para empurrar essas alterações para fora para o seu operador.

## <a name="create-the-simulated-devices"></a>Criar os dispositivos simulados

Este tutorial utiliza dispositivos simulados a jusante e um dispositivo de gateway simulado.

Para criar um dispositivo de gateway simulado:

1. Na página **Dispositivos,** selecione o **dispositivo smart building gateway** na lista de modelos do dispositivo.

1. Selecione **+** para começar a adicionar um novo dispositivo.

1. Mantenha o **ID** e **o nome do dispositivo** gerados. Certifique-se de que o interruptor **simulado** está **ligado**. Selecione **Criar**.

Para criar um dispositivo simulado a jusante:

1. Na página **dispositivos,** selecione **o Sensor de Ocupação RS40** na lista de modelos do dispositivo.

1. Selecione **+** para começar a adicionar um novo dispositivo.

1. Mantenha o **ID** e **o nome do dispositivo** gerados. Certifique-se de que o interruptor **simulado** está **ligado**. Selecione **Criar**.

1. Na página **dispositivos,** selecione **S1 Sensor** na lista de modelos do dispositivo.

1. Selecione **+** para começar a adicionar um novo dispositivo.

1. Mantenha o **ID** e **o nome do dispositivo** gerados. Certifique-se de que o interruptor **simulado** está **ligado**. Selecione **Criar**.

![Dispositivos simulados na sua aplicação](./media/tutorial-define-gateway-device-type/simulated-devices.png)

### <a name="add-downstream-device-relationships-to-a-gateway-device"></a>Adicione as relações do dispositivo a jusante a um dispositivo de gateway

Agora que tem os dispositivos simulados na sua aplicação, pode criar as relações entre os dispositivos a jusante e o dispositivo gateway:

1. Na página **dispositivos,** selecione o **Sensor S1** na lista de modelos do dispositivo e, em seguida, selecione o seu dispositivo **simulado S1 Sensor.**

1. Selecione **Ligar ao gateway**.

1. No **'Ligar' a um** diálogo de gateway, selecione o modelo do **dispositivo de gateway Smart Building** e, em seguida, selecione a instância simulada que criou anteriormente.

1. Selecione **Join**.

1. Na página **dispositivos,** selecione o **Sensor de Ocupação RS40** na lista de modelos do dispositivo e, em seguida, selecione o seu dispositivo simulado **de Sensor de Ocupação RS40.**

1. Selecione **Ligar ao gateway**.

1. No **'Ligar' a um** diálogo de gateway, selecione o modelo do **dispositivo de gateway Smart Building** e, em seguida, selecione a instância simulada que criou anteriormente.

1. Selecione **Join**.

Ambos os seus dispositivos simulados a jusante estão agora ligados ao seu dispositivo de gateway simulado. Se navegar para a vista **dos Dispositivos a jusante** para o seu dispositivo gateway, pode ver os dispositivos a jusante relacionados:

![Vista de dispositivos a jusante](./media/tutorial-define-gateway-device-type/downstream-device-view.png)

Selecione um modelo de dispositivo de gateway e a instância do dispositivo gateway e **selecione 'Junte-se'.**

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

* Crie um novo gateway IoT como modelo de dispositivo.
* Criar propriedades em nuvem.
* Crie personalizações.
* Defina uma visualização para a telemetria do dispositivo.
* Adicione relacionamentos.
* Publique o modelo do seu dispositivo.

Em seguida, como desenvolvedor de dispositivos, você pode aprender a:

> [!div class="nextstepaction"]
> [Adicione um dispositivo Azure IoT Edge à sua aplicação Azure IoT Central](tutorial-add-edge-as-leaf-device.md)
