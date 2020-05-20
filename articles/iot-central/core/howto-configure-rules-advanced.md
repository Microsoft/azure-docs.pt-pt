---
title: Utilize fluxos de trabalho para integrar a sua aplicação Azure IoT Central com outros serviços na nuvem [ Microsoft Docs
description: Este artigo de como fazer mostra-lhe, como construtor, como configurar regras e ações que integram a sua aplicação IoT Central com outros serviços na nuvem. Para criar uma regra avançada, utilize um conector IoT Central em Aplicações Power Automate ou Azure Logic.
author: dominicbetts
ms.author: dobett
ms.date: 05/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: e2018f4d6f8e0813892a43c66975961356333bff
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665006"
---
# <a name="use-workflows-to-integrate-your-azure-iot-central-application-with-other-cloud-services"></a>Utilize fluxos de trabalho para integrar a sua aplicação Azure IoT Central com outros serviços na nuvem

*Este artigo aplica-se aos construtores de soluções.*

Pode criar regras na IoT Central que desencadeiem ações, como o envio de um e-mail, em resposta a condições baseadas em telemetria, como a temperatura do dispositivo que exceda um limiar.

O conector IoT Central para Aplicações Power Automate e Azure Logic permite criar regras mais avançadas para automatizar operações na IoT Central:

- Quando uma regra dispara na sua aplicação Azure IoT Central, pode desencadear um fluxo de trabalho em Power Automate ou Azure Logic Apps. Estes fluxos de trabalho podem executar ações em outros serviços na nuvem, como o Office 365, ou um serviço de terceiros.
- Um evento em outro serviço na nuvem, como o Office 365, pode desencadear um fluxo de trabalho em Power Automate ou Azure Logic Apps. Estes fluxos de trabalho podem executar ações ou recuperar dados da sua aplicação IoT Central.

## <a name="trigger-a-workflow-from-a-rule"></a>Desencadear um fluxo de trabalho de uma regra

Antes de poder desencadear um fluxo de trabalho em Power Automate ou Azure Logic Apps, precisa de uma regra na sua aplicação IoT Central. Para saber mais, consulte as regras e ações da [Configuração na Central Azure IoT.](./howto-configure-rules.md)

Para adicionar o **Azure IoT Central - pré-visualizar** o conector como um gatilho no Power Automate:

1. Em Power Automate, selecione **+ Criar**, selecione o separador **Personalizado.**
1. Procure *ioT Central*, e selecione o conector **Azure IoT Central - pré-visualização.**
1. Na lista de gatilhos, selecione **Quando uma regra for acionada (pré-visualização)**.
1. No **passo Quando uma regra é disparada,** selecione a sua aplicação IoT Central e a regra que está a usar.

Para adicionar o **Azure IoT Central - conector de pré-visualização** como um gatilho em Aplicações Lógicas Azure:

1. Em **Logic Apps Designer,** selecione o modelo de **aplicação lógica em branco.**
1. No designer, selecione o separador **Custom.**
1. Procure *ioT Central*, e selecione o conector **Azure IoT Central - pré-visualização.**
1. Na lista de gatilhos, selecione **Quando uma regra for acionada (pré-visualização)**.
1. No **passo Quando uma regra é disparada,** selecione a sua aplicação IoT Central e a regra que está a usar.

:::image type="content" source="./media/howto-configure-rules-advanced/triggers.png" alt-text="Encontre o Azure IoT Central - conector de pré-visualização e escolha o gatilho":::

Agora pode adicionar mais passos ao seu fluxo de trabalho para construir o seu cenário de integração.

## <a name="run-an-action"></a>Executar uma ação

Você pode executar ações em uma aplicação IoT Central a partir de power automate e azure logic apps fluxos de trabalho. Em primeiro lugar, crie o seu fluxo de trabalho e utilize um conector para definir um gatilho para iniciar o fluxo de trabalho. Em seguida, utilize o **Azure IoT Central - pré-visualizar** o conector como uma ação.

Para adicionar o **Azure IoT Central - pré-visualizar** o conector como uma ação no Power Automate:

1. No Power Automate, no painel de ação Escolha um painel de **ação,** selecione o separador **Custom.**
1. Procure *ioT Central* e selecione o conector **Azure IoT Central - pré-visualização.**
1. Na lista de ações, selecione a ação IoT Central que pretende utilizar.
1. No passo de ação, complete a configuração para a ação que escolheu. Em seguida, selecione **Guardar**.

Para adicionar o **Azure IoT Central - conector de pré-visualização** como uma ação em Aplicações Lógicas Azure:

1. Na **Logic Apps Designer,** no Painel escolha um painel de **ação,** selecione o separador **Custom.**
1. Procure *ioT Central*, e selecione o conector **Azure IoT Central - pré-visualização.**
1. Na lista de ações, selecione a ação IoT Central que pretende utilizar.
1. No passo de ação, complete a configuração para a ação que escolheu. Em seguida, selecione **Guardar**.

:::image type="content" source="./media/howto-configure-rules-advanced/actions.png" alt-text="Encontre o Azure IoT Central - pré-visualizar o conector e escolha uma ação":::

## <a name="list-of-actions"></a>Lista de ações

A lista que se segue mostra todas as ações disponíveis do IoT Central no **Azure IoT Central - conector de pré-visualização** e suas opções de configuração. Muitos dos campos podem ter conteúdo gerado dinamicamente. Por exemplo, um passo anterior poderia determinar o ID do dispositivo em que o passo atual atua.

### <a name="create-or-update-a-device"></a>Criar ou atualizar um dispositivo

Utilize esta ação para criar ou atualizar um dispositivo na sua aplicação IoT Central.

| Campo | Descrição |
| ----- | ----------- |
| Aplicação | Escolha entre a sua lista de aplicações IoT Central. |
| Dispositivo | A identificação única do dispositivo para criar ou atualizar. |
| Aprovado | Escolha se o dispositivo foi aprovado para ligar à IoT Central. |
| Descrição do Dispositivo | Uma descrição detalhada do dispositivo. |
| Nome do Dispositivo | O nome de exibição do dispositivo. |
| Modelo de dispositivo | Escolha entre a lista de modelos de dispositivos na sua aplicação IoT Central. |
| Simulado | Escolha se o dispositivo é simulado. |

### <a name="delete-a-device"></a>Eliminar um dispositivo

Utilize esta ação para eliminar um dispositivo da sua aplicação IoT Central.

| Campo | Descrição |
| ----- | ----------- |
| Aplicação | Escolha entre a sua lista de aplicações IoT Central. |
| Dispositivo | A identificação única do dispositivo para apagar. |

### <a name="execute-a-device-command"></a>Executar um comando de dispositivo

Utilize esta ação para executar um comando definido numa das interfaces do dispositivo.

| Campo | Descrição |
| ----- | ----------- |
| Aplicação | Escolha entre a sua lista de aplicações IoT Central. |
| Dispositivo | A identificação única do dispositivo para apagar. |
| Componente do dispositivo | A interface no modelo do dispositivo que contém o comando. |
| Comando do Dispositivo | Escolha um dos comandos na interface selecionada. |
| Modelo de dispositivo | Escolha entre a lista de modelos de dispositivos na sua aplicação IoT Central. |
| Carga útil do pedido de comando do dispositivo | Se o comando necessitar de uma carga útil de pedido, adicione-o aqui.  |

> [!NOTE]
> Não pode escolher um componente do dispositivo até ter escolhido um modelo de dispositivo.

### <a name="get-a-device-by-id"></a>Obter um dispositivo por ID

Utilize esta ação para recuperar os detalhes do dispositivo.

| Campo | Descrição |
| ----- | ----------- |
| Aplicação | Escolha entre a sua lista de aplicações IoT Central. |
| Dispositivo | A identificação única do dispositivo para apagar. |

Pode utilizar os detalhes devolvidos nas expressões dinâmicas de outras ações. Os detalhes do dispositivo devolvidos incluem: **Aprovado,** **corpo,** **Descrição do dispositivo,** nome do **dispositivo,** modelo de **dispositivo,** **provisionado**e **simulado**.

### <a name="get-device-cloud-properties"></a>Obtenha propriedades na nuvem do dispositivo

Utilize esta ação para recuperar os valores da propriedade da nuvem para um dispositivo específico.

| Campo | Descrição |
| ----- | ----------- |
| Aplicação | Escolha entre a sua lista de aplicações IoT Central. |
| Dispositivo | A identificação única do dispositivo para apagar. |
| Modelo de dispositivo | Escolha entre a lista de modelos de dispositivos na sua aplicação IoT Central. |

Você pode usar os valores de propriedade em nuvem devolvida nas expressões dinâmicas em outras ações.

### <a name="get-device-properties"></a>Obtenha propriedades do dispositivo

Utilize esta ação para recuperar os valores da propriedade para um dispositivo específico.

| Campo | Descrição |
| ----- | ----------- |
| Aplicação | Escolha entre a sua lista de aplicações IoT Central. |
| Dispositivo | A identificação única do dispositivo para apagar. |
| Modelo de dispositivo | Escolha entre a lista de modelos de dispositivos na sua aplicação IoT Central. |

Pode utilizar os valores de propriedade devolvidos nas expressões dinâmicas em outras ações.

### <a name="get-device-telemetry-value"></a>Obter valor de telemetria do dispositivo

Utilize esta ação para recuperar os valores de telemetria para um dispositivo específico.

| Campo | Descrição |
| ----- | ----------- |
| Aplicação | Escolha entre a sua lista de aplicações IoT Central. |
| Dispositivo | A identificação única do dispositivo para apagar. |
| Modelo de dispositivo | Escolha entre a lista de modelos de dispositivos na sua aplicação IoT Central. |

Pode utilizar os valores de telemetria devolvidos nas expressões dinâmicas de outras ações.

### <a name="update-device-cloud-properties"></a>Atualizar propriedades da nuvem do dispositivo

Utilize esta ação para atualizar os valores de propriedade na nuvem para um dispositivo específico.

| Campo | Descrição |
| ----- | ----------- |
| Aplicação | Escolha entre a sua lista de aplicações IoT Central. |
| Dispositivo | A identificação única do dispositivo para apagar. |
| Modelo de dispositivo | Escolha entre a lista de modelos de dispositivos na sua aplicação IoT Central. |
| Propriedades em nuvem | Depois de escolher um modelo de dispositivo, é adicionado um campo para cada propriedade em nuvem definida no modelo. |

### <a name="update-device-properties"></a>Atualizar propriedades do dispositivo

Utilize esta ação para atualizar valores de propriedade reemível para um dispositivo específico.

| Campo | Descrição |
| ----- | ----------- |
| Aplicação | Escolha entre a sua lista de aplicações IoT Central. |
| Dispositivo | A identificação única do dispositivo para apagar. |
| Modelo de dispositivo | Escolha entre a lista de modelos de dispositivos na sua aplicação IoT Central. |
| Propriedades repreensíveis | Depois de escolher um modelo de dispositivo, é adicionado um campo para cada propriedade writeable definida no modelo. |

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a criar uma regra avançada na sua aplicação Azure IoT Central, pode aprender a [analisar dados do dispositivo na sua aplicação Azure IoT Central](howto-create-analytics.md)
