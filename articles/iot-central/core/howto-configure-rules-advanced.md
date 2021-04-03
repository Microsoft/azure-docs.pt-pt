---
title: Utilize fluxos de trabalho para integrar a sua aplicação Azure IoT Central com outros serviços em nuvem | Microsoft Docs
description: Este artigo de como fazer mostra-lhe, como construtor, como configurar regras e ações que integram a sua aplicação IoT Central com outros serviços na nuvem. Para criar uma regra avançada, utilize um conector IoT Central em Aplicações de Automação de Energia ou Azure Logic.
author: dominicbetts
ms.author: dobett
ms.date: 05/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 257855b4f7b1fae56ed8d6a063acfb0588da9b6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92123325"
---
# <a name="use-workflows-to-integrate-your-azure-iot-central-application-with-other-cloud-services"></a>Utilize fluxos de trabalho para integrar a sua aplicação Azure IoT Central com outros serviços na nuvem

*Este artigo aplica-se aos construtores de soluções.*

Pode criar regras na IoT Central que desencadeiem ações, como o envio de um e-mail, em resposta a condições baseadas em telemetria, como a temperatura do dispositivo que exceda um limiar.

O conector Azure IoT Central V3 para Power Automamate e Azure Logic Apps permite criar regras mais avançadas para automatizar operações em IoT Central:

- Quando uma regra dispara na sua aplicação Azure IoT Central, pode desencadear um fluxo de trabalho em Power Automamate ou Azure Logic Apps. Estes fluxos de trabalho podem executar ações em outros serviços na nuvem, como o Microsoft 365 ou um serviço de terceiros.
- Um evento em outro serviço na nuvem, como o Microsoft 365, pode desencadear um fluxo de trabalho em Power Automamate ou Azure Logic Apps. Estes fluxos de trabalho podem executar ações ou obter dados da sua aplicação IoT Central.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste guia de como fazer, precisa de uma subscrição ativa do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

A configuração da solução requer uma aplicação IoT Central versão 3. Para saber como verificar a sua versão de aplicação, consulte [sobre a sua aplicação.](./howto-get-app-info.md) Para aprender a criar uma aplicação IoT Central, consulte [Criar uma aplicação Azure IoT Central](./quick-deploy-iot-central.md).

> [!NOTE]
> Se estiver a utilizar uma aplicação IoT Central versão 2, consulte [Construir fluxos de trabalho com o conector IoT Central em Azure Logic Apps](/previous-versions/azure/iot-central/core/howto-build-azure-logic-apps) no site de documentação das versões anteriores e utilizar o conector Azure IoT Central V2

## <a name="trigger-a-workflow-from-a-rule"></a>Desencadear um fluxo de trabalho a partir de uma regra

Antes de poder desencadear um fluxo de trabalho em Power Automamate ou Azure Logic Apps, precisa de uma regra na sua aplicação IoT Central. Para saber mais, consulte [as regras e ações do Configure na Azure IoT Central.](./howto-configure-rules.md)

Para adicionar o **Azure IoT Central V3 - pré-visualizar** o conector como gatilho no Power Automamate:

1. No Power Automamate, selecione **+ Criar,** selecione o **separador Personalizado.**
1. Procure *por IoT Central*, e selecione o conector **Azure IoT Central V3 - conector de pré-visualização.**
1. Na lista de gatilhos, selecione **Quando uma regra é disparada (pré-visualização)**.
1. No Passo em que **uma regra é disparada,** selecione a sua aplicação IoT Central e a regra que está a usar.

Para adicionar o **Azure IoT Central V3 - pré-visualizar** o conector como um gatilho em Azure Logic Apps:

1. No **Logic Apps Designer,** selecione o modelo **de Aplicação Lógica Em Branco.**
1. No designer, selecione o **separador Personalizado.**
1. Procure *por IoT Central*, e selecione o conector **Azure IoT Central V3 - conector de pré-visualização.**
1. Na lista de gatilhos, selecione **Quando uma regra é disparada (pré-visualização)**.
1. No Passo em que **uma regra é disparada,** selecione a sua aplicação IoT Central e a regra que está a usar.

:::image type="content" source="./media/howto-configure-rules-advanced/triggers.png" alt-text="Encontre o Azure IoT Central - pré-visualizar o conector e escolha o gatilho":::

Pode agora adicionar mais passos ao seu fluxo de trabalho para construir o seu cenário de integração.

## <a name="run-an-action"></a>Executar uma ação

Você pode executar ações em uma aplicação IoT Central a partir de power automamate e Azure Logic Apps fluxos de trabalho. Em primeiro lugar, crie o seu fluxo de trabalho e utilize um conector para definir um gatilho para iniciar o fluxo de trabalho. Em seguida, utilize o **Azure IoT Central V3 - pré-visualizar** o conector como ação.

Para adicionar o **Azure IoT Central V3 - pré-visualizar** o conector como uma ação no Power Automamate:

1. No Power Automamate, no painel **de ação Escolha,** selecione o **separador Personalizado.**
1. Procure *por IoT Central* e selecione o **conector Azure IoT Central V3 - conector de pré-visualização.**
1. Na lista de ações, selecione a ação IoT Central que pretende utilizar.
1. No passo de ação, complete a configuração para a ação que escolheu. Em seguida, selecione **Guardar**.

Para adicionar o conector **Azure IoT Central V3- pré-visualização** como uma ação em Azure Logic Apps:

1. No **Logic Apps Designer,** no painel **de ação Escolha,** selecione o **separador Personalizado.**
1. Procure *por IoT Central* e selecione o conector **de pré-visualização Azure IoT Central V3.**
1. Na lista de ações, selecione a ação IoT Central que pretende utilizar.
1. No passo de ação, complete a configuração para a ação que escolheu. Em seguida, selecione **Guardar**.

:::image type="content" source="./media/howto-configure-rules-advanced/actions.png" alt-text="Encontre o conector Azure IoT Central V3 e escolha uma ação":::

## <a name="list-of-actions"></a>Lista de ações

A lista a seguir mostra todas as ações IoT Central disponíveis no **Azure IoT Central V3 - conector de pré-visualização** e suas opções de configuração. Muitos dos campos podem ter conteúdo gerado dinamicamente. Por exemplo, um passo anterior poderia determinar o ID do dispositivo em que o passo atual funciona.

### <a name="create-or-update-a-device"></a>Criar ou atualizar um dispositivo

Utilize esta ação para criar ou atualizar um dispositivo na sua aplicação IoT Central.

| Campo | Descrição |
| ----- | ----------- |
| Aplicação | Escolha na sua lista de aplicações IoT Central. |
| Dispositivo | O ID único do dispositivo para criar ou atualizar. |
| Aprovado | Escolha se o dispositivo foi aprovado para ligar à IoT Central. |
| Descrição do Dispositivo | Uma descrição detalhada do dispositivo. |
| Nome do Dispositivo | O nome de exibição do dispositivo. |
| Modelo de dispositivo | Escolha entre a lista de modelos de dispositivo na sua aplicação IoT Central. |
| Simulado | Escolha se o dispositivo é simulado. |

### <a name="delete-a-device"></a>Eliminar um dispositivo

Utilize esta ação para eliminar um dispositivo da sua aplicação IoT Central.

| Campo | Descrição |
| ----- | ----------- |
| Aplicação | Escolha na sua lista de aplicações IoT Central. |
| Dispositivo | A identificação única do dispositivo para apagar. |

### <a name="execute-a-device-command"></a>Executar um comando de dispositivo

Utilize esta ação para executar um comando definido numa das interfaces do dispositivo.

| Campo | Descrição |
| ----- | ----------- |
| Aplicação | Escolha na sua lista de aplicações IoT Central. |
| Dispositivo | A identificação única do dispositivo para apagar. |
| Componente do dispositivo | A interface no modelo do dispositivo que contém o comando. |
| Comando do Dispositivo | Escolha um dos comandos na interface selecionada. |
| Modelo de dispositivo | Escolha entre a lista de modelos de dispositivo na sua aplicação IoT Central. |
| Pedido de pedido de dispositivo Payload | Se o comando necessitar de uma carga útil de pedido, adicione-o aqui.  |

> [!NOTE]
> Não pode escolher um componente do dispositivo até escolher um modelo de dispositivo.

### <a name="get-a-device-by-id"></a>Obter um dispositivo por ID

Utilize esta ação para recuperar os detalhes do dispositivo.

| Campo | Descrição |
| ----- | ----------- |
| Aplicação | Escolha na sua lista de aplicações IoT Central. |
| Dispositivo | A identificação única do dispositivo para apagar. |

Pode utilizar os detalhes devolvidos nas expressões dinâmicas noutras ações. Os detalhes do dispositivo devolvidos incluem: **Aprovado,** **corpo,** **descrição do dispositivo,** **nome do dispositivo,** **modelo do dispositivo,** **aprovisionado** e **simulado**.

### <a name="get-device-cloud-properties"></a>Obtenha propriedades na nuvem de dispositivo

Utilize esta ação para recuperar os valores de propriedade na nuvem para um dispositivo específico.

| Campo | Descrição |
| ----- | ----------- |
| Aplicação | Escolha na sua lista de aplicações IoT Central. |
| Dispositivo | A identificação única do dispositivo para apagar. |
| Modelo de dispositivo | Escolha entre a lista de modelos de dispositivo na sua aplicação IoT Central. |

Pode utilizar os valores de propriedade em nuvem devolvidos nas expressões dinâmicas noutras ações.

### <a name="get-device-properties"></a>Obtenha propriedades do dispositivo

Utilize esta ação para recuperar os valores de propriedade para um dispositivo específico.

| Campo | Descrição |
| ----- | ----------- |
| Aplicação | Escolha na sua lista de aplicações IoT Central. |
| Dispositivo | A identificação única do dispositivo para apagar. |
| Modelo de dispositivo | Escolha entre a lista de modelos de dispositivo na sua aplicação IoT Central. |

Pode utilizar os valores de propriedade devolvidos nas expressões dinâmicas noutras ações.

### <a name="get-device-telemetry-value"></a>Obtenha o valor da telemetria do dispositivo

Utilize esta ação para recuperar os valores de telemetria para um dispositivo específico.

| Campo | Descrição |
| ----- | ----------- |
| Aplicação | Escolha na sua lista de aplicações IoT Central. |
| Dispositivo | A identificação única do dispositivo para apagar. |
| Modelo de dispositivo | Escolha entre a lista de modelos de dispositivo na sua aplicação IoT Central. |

Pode utilizar os valores de telemetria devolvidos nas expressões dinâmicas noutras ações.

### <a name="update-device-cloud-properties"></a>Atualizar propriedades na nuvem do dispositivo

Utilize esta ação para atualizar os valores de propriedade na nuvem para um dispositivo específico.

| Campo | Descrição |
| ----- | ----------- |
| Aplicação | Escolha na sua lista de aplicações IoT Central. |
| Dispositivo | A identificação única do dispositivo para apagar. |
| Modelo de dispositivo | Escolha entre a lista de modelos de dispositivo na sua aplicação IoT Central. |
| Propriedades da cloud | Depois de escolher um modelo de dispositivo, é adicionado um campo para cada propriedade na nuvem definida no modelo. |

### <a name="update-device-properties"></a>Atualizar as propriedades do dispositivo

Utilize esta ação para atualizar valores de propriedades escritas para um dispositivo específico.

| Campo | Descrição |
| ----- | ----------- |
| Aplicação | Escolha na sua lista de aplicações IoT Central. |
| Dispositivo | A identificação única do dispositivo para apagar. |
| Modelo de dispositivo | Escolha entre a lista de modelos de dispositivo na sua aplicação IoT Central. |
| Propriedades escritas | Depois de escolher um modelo de dispositivo, é adicionado um campo para cada propriedade escrita definida no modelo. |

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a criar uma regra avançada na sua aplicação Azure IoT Central, pode aprender a analisar dados do [dispositivo na sua aplicação Azure IoT Central](howto-create-analytics.md)