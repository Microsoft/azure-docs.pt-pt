---
title: Configurar regras e ações no Azure IoT Central | Microsoft Docs
description: Este tutorial mostra-lhe, como construtor, como configurar regras baseadas em telemetria e ações na sua aplicação Azure IoT Central.
author: ankitscribbles
ms.author: ankitgup
ms.date: 01/28/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 096daa28b7548401adc857c3c6c8327ef1d1eb00
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769409"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central-new-ui-design"></a>Tutorial: Configurar regras e ações para o seu dispositivo no Azure IoT Central (design de nova interface do Usuário)

*Este artigo aplica-se a operadores, construtores e administradores.*

Neste tutorial, pode criar uma regra que envia um e-mail quando a temperatura do dispositivo de ar condicionado ligado excede 90&deg; F.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma regra baseada em telemetria
> * Adicionar uma ação

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, deve concluir o tutorial [Definir um novo tipo de dispositivo na sua aplicação](tutorial-define-device-type.md) para criar o modelo de dispositivo **Dispositivo de Ar Condicionado Ligado** com que irá trabalhar.

## <a name="create-a-telemetry-based-rule"></a>Criar uma regra baseada em telemetria

1. Para adicionar uma nova regra de acesso com base na telemetria ao seu aplicativo, no menu de navegação esquerdo, selecione **modelos de dispositivos**:

    ![Página de modelos de dispositivo](media/tutorial-configure-rules/templatespage1.png)

    Verá o **ligado ar-condicionado (1.0.0)** modelo de dispositivo que criou no tutorial anterior.

2. Para personalizar o modelo de dispositivo, selecione o **ligado ar-condicionado** modelo que criou no tutorial anterior.

3. Para adicionar uma regra baseada em telemetria no **regras** visualizar, selecione **regras**, selecione **+ nova regra**e, em seguida, selecione **telemetria**:

    ![Vista de regras](media/tutorial-configure-rules/newrule.png)

5. Para definir a sua regra, utilize as informações na tabela seguinte:

    | Definição                                      | Valor                             |
    | -------------------------------------------- | ------------------------------    |
    | Name                                         | Alerta de temperatura do ar condicionado |
    | Ativar regra em todos os dispositivos deste modelo | Ativado                                |
    | Condição                                    | A temperatura é superior a 90    |
    | Agregação                                  | Nenhuma                              |

    ![Condição da regra de temperatura](media/tutorial-configure-rules/temperaturerule.png)

    Em seguida, selecione **Guardar**.

## <a name="add-an-action"></a>Adicionar uma ação

Quando definir uma regra, também pode definir uma ação a executar quando são satisfeitas as condições de regra. Neste tutorial, vai criar uma regra com uma ação que envia uma notificação por e-mail.

1. Para adicionar um **ação**, primeiro **guardar** a regra e, em seguida, desloque-se para baixo no **configurar regra de telemetria** painel. Escolha o **+** junto a **ações**e, em seguida, escolha **E-Mail**:

    ![Ação de regra de temperatura](media/tutorial-configure-rules/addaction.png)

2. Para definir a sua ação, utilize as informações na tabela seguinte:

    | Definição   | Value                          |
    | --------- | ------------------------------ |
    | a        | O seu endereço de correio eletrónico             |
    | Notas     | A temperatura do ar condicionado excedeu o limite. |

    > [!NOTE]
    > Para receber uma notificação por e-mail, o endereço de e-mail deve ser um [ID de utilizador na aplicação](howto-administer.md), e esse utilizador deve ter sessão iniciada na aplicação, pelo menos, uma vez.

    ![Ação de temperatura](media/tutorial-configure-rules/temperatureaction.png)

3. Selecione **Guardar**. A regra está listada na **regras** página.

## <a name="test-the-rule"></a>Testar a regra

Pouco tempo depois de guardar a regra, esta é ativada. Quando forem cumpridas as condições definidas na regra, a aplicação envia uma mensagem para o endereço de e-mail que especificou na ação.

> [!NOTE]
> Depois de seu teste estiver concluído, desative a regra para parar de receber alertas na pasta a receber.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Criar uma regra baseada em telemetria
> * Adicionar uma ação

Agora que definiu uma regra baseada em limiar é a próxima etapa sugerida [personalizar vistas da operadora](tutorial-customize-operator.md).

Para saber mais sobre os diferentes tipos de regras no Azure IoT Central e como parametrizar a definição da regra, consulte:
* [Criar uma regra de telemetria e configurar as notificações](howto-create-telemetry-rules.md).
* [Criar uma regra de eventos e configurar as notificações](howto-create-event-rules.md).

<!-- Next tutorials in the sequence -->