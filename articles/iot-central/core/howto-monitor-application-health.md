---
title: Monitorize a saúde de uma aplicação Azure IoT Central | Microsoft Docs
description: Como operador ou administrador, monitorize a saúde geral dos dispositivos ligados à sua aplicação IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 01/27/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: d0e59f73dd9b62b528c3d86d315b613312df7773
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100577046"
---
# <a name="monitor-the-overall-health-of-an-iot-central-application"></a>Monitorizar a saúde geral de uma aplicação IoT Central

> [!NOTE]
> As métricas só estão disponíveis para aplicações IoT Central da versão 3. Para saber como verificar a sua versão de aplicação, consulte [sobre a sua aplicação.](./howto-get-app-info.md)

*Este artigo aplica-se aos operadores e administradores.*

Neste artigo, aprende-se a utilizar o conjunto de métricas fornecidas pela IoT Central para avaliar a saúde dos dispositivos ligados à sua aplicação IoT Central e a saúde das suas exportações de dados em execução.

As métricas são ativadas por padrão para a sua aplicação IoT Central e acede-las a partir do [portal Azure](https://portal.azure.com/). A [plataforma de dados Azure Monitor expõe estas métricas](../../azure-monitor/essentials/data-platform-metrics.md) e fornece várias formas de interagir com elas. Por exemplo, pode utilizar gráficos no portal Azure, uma API REST ou consultas no PowerShell ou no Azure CLI.

### <a name="trial-applications"></a>Pedidos de ensaio

As aplicações que usam o plano de teste gratuito não têm uma subscrição Azure associada e por isso não suportam métricas do Azure Monitor. Pode [converter uma aplicação num plano de preços padrão](./howto-view-bill.md#move-from-free-to-standard-pricing-plan) e ter acesso a estas métricas.

## <a name="view-metrics-in-the-azure-portal"></a>Ver métricas no portal Azure

Os seguintes passos pressupõem que tem uma [aplicação IoT Central](./quick-deploy-iot-central.md) com [alguns dispositivos conectados](./tutorial-connect-device.md) ou uma exportação de dados em [execução.](howto-export-data.md)

Para ver as métricas IoT Central no portal:

1. Navegue para o seu recurso de aplicação IoT Central no portal. Por padrão, os recursos da IoT Central estão localizados num grupo de recursos chamado **IOTC**.
1. Para criar um gráfico a partir das métricas da sua aplicação, selecione **Métricas** na secção **de Monitorização.**

![Métricas Azure](media/howto-monitor-application-health/metrics.png)

### <a name="azure-portal-permissions"></a>Permissões do portal Azure

O acesso às métricas no portal Azure é gerido pelo [controlo de acesso baseado em funções Azure.](../../role-based-access-control/overview.md) Utilize o portal Azure para adicionar os utilizadores à aplicação/grupo/subscrição IoT Central para lhes conceder acesso. Deve adicionar um utilizador no portal mesmo que já tenham sido adicionados à aplicação IoT Central. Utilize [funções incorporadas Azure](../../role-based-access-control/built-in-roles.md) para um controlo de acesso com grãos mais finos.

## <a name="iot-central-metrics"></a>Métricas IoT Central

Para obter uma lista das métricas que estão atualmente disponíveis para a IoT Central, consulte [métricas suportadas com o Azure Monitor](../../azure-monitor/essentials/metrics-supported.md#microsoftiotcentraliotapps).

### <a name="metrics-and-invoices"></a>Métricas e faturas

As métricas podem diferir dos números indicados na sua fatura Azure IoT Central. Esta situação ocorre por uma série de razões, tais como:

- Os [planos de preços padrão](https://azure.microsoft.com/pricing/details/iot-central/) da IoT Central incluem dois dispositivos e quotas de mensagens variadas gratuitamente. Enquanto os itens gratuitos são excluídos da faturação, ainda são contados nas métricas.

- IoT Central autogere um ID do dispositivo de teste para cada modelo de dispositivo na aplicação. Este ID do dispositivo é visível na página do **dispositivo de teste Manage** para um modelo de dispositivo. Os construtores de soluções podem optar por [validar os seus modelos de dispositivo antes](./overview-iot-central.md#create-device-templates) de os publicar, gerando código que utiliza estes IDs do dispositivo de teste. Embora estes dispositivos estejam excluídos da faturação, ainda são contados nas métricas.

- Embora as métricas possam mostrar um subconjunto de comunicação dispositivo-nuvem, toda a comunicação entre o dispositivo e a nuvem [conta como uma mensagem para a faturação](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar modelos de aplicação, o próximo passo sugerido é aprender a [gerir o IoT Central a partir do portal Azure.](howto-manage-iot-central-from-portal.md)