---
title: Monitorize a saúde de uma aplicação Azure IoT Central / Microsoft Docs
description: Como operador ou administrador, monitorize a saúde geral dos dispositivos ligados à sua aplicação IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 664819b209aeb09093ce8711456b86ff4d3e8949
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84249579"
---
# <a name="monitor-the-overall-health-of-the-devices-connected-to-an-iot-central-application"></a>Monitorize a saúde geral dos dispositivos ligados a uma aplicação IoT Central

*Este artigo aplica-se aos operadores e administradores.*

Neste artigo, aprende-se a utilizar o conjunto de métricas fornecidas pela IoT Central para avaliar a saúde geral dos dispositivos ligados à sua aplicação IoT Central.

As métricas são ativadas por padrão para a sua aplicação IoT Central e acede-las a partir do [portal Azure](https://portal.azure.com/). A [plataforma de dados Azure Monitor expõe estas métricas](../../azure-monitor/platform/data-platform-metrics.md) e fornece várias formas de interagir com elas. Por exemplo, pode utilizar gráficos no portal Azure, uma API REST ou consultas no PowerShell ou no Azure CLI.

### <a name="trial-applications"></a>Pedidos de ensaio

As aplicações que usam o plano de teste gratuito não têm uma subscrição Azure associada e por isso não suportam métricas do Azure Monitor. Pode [converter uma aplicação num plano de preços padrão](./howto-view-bill.md#move-from-free-to-standard-pricing-plan) e ter acesso a estas métricas.

## <a name="view-metrics-in-the-azure-portal"></a>Ver métricas no portal Azure

Os seguintes passos pressupõem que tem uma [aplicação IoT Central](./quick-deploy-iot-central.md) com [alguns dispositivos conectados](./tutorial-connect-device-nodejs.md).

Para ver as métricas IoT Central no portal:

1. Navegue para o seu recurso de aplicação IoT Central no portal. Por padrão, os recursos da IoT Central estão localizados num grupo de recursos chamado **IOTC**.
1. Para criar um gráfico a partir das métricas da sua aplicação, selecione **Métricas** na secção **de Monitorização.**

### <a name="azure-portal-permissions"></a>Permissões do portal Azure

O acesso às métricas no portal Azure é gerido pelo [controlo de acesso baseado em funções Azure.](../../role-based-access-control/overview.md) Utilize o portal Azure para adicionar os utilizadores à aplicação/grupo/subscrição IoT Central para lhes conceder acesso. Deve adicionar um utilizador no portal mesmo que já tenham sido adicionados à aplicação IoT Central. Utilize [funções incorporadas Azure](../../role-based-access-control/built-in-roles.md) para um controlo de acesso com grãos mais finos.

## <a name="iot-central-metrics"></a>Métricas IoT Central

A tabela a seguir descreve as métricas que estão atualmente disponíveis para a IoT Central:

| Metric | Nome de exibição métrica | Unidade | Tipo de agregação | Descrição |
|--------|---------------------|------|------------------|-------------|
| connectedDeviceCount         | Total de dispositivos conectados                              | Contagem  | Total             | Número de dispositivos ligados à IoT Central                               |
| c2d.property.read.success    | Propriedade de dispositivo de sucesso lê da IoT Central    | Contagem  | Total             | A contagem de todas as leituras bem sucedidas iniciadas da IoT Central    |
| c2d.property.read.failure    | Falha na propriedade do dispositivo lê-se da IoT Central        | Contagem  | Total             | A contagem de todas as leituras de propriedade falhada iniciadas a partir da IoT Central        |
| d2c.property.read.success    | Bem sucedida propriedade do dispositivo lê de dispositivos        | Contagem  | Total             | A contagem de todas as leituras bem sucedidas iniciadas a partir de dispositivos        |
| d2c.property.read.failure    | Falha na propriedade do dispositivo lê-se a partir de dispositivos            | Contagem  | Total             | A contagem de todas as leituras de propriedade falhada iniciadas a partir de dispositivos            |
| c2d.property.update.success  | Atualizações de propriedade de dispositivos bem-sucedidos da IoT Central  | Contagem  | Total             | A contagem de todas as atualizações de propriedade bem sucedidas iniciadas a partir da IoT Central  |
| c2d.property.update.failure  | Atualizações de propriedade de dispositivos falhados da IoT Central      | Contagem  | Total             | A contagem de todas as atualizações de propriedade falhadas iniciadas a partir da IoT Central      |
| d2c.property.update.success  | Atualizações de propriedade de dispositivos bem-sucedidos a partir de dispositivos      | Contagem  | Total             | A contagem de todas as atualizações de propriedade bem sucedidas iniciadas a partir de dispositivos      |
| d2c.property.update.failure  | Falhas nas atualizações de propriedade do dispositivo a partir de dispositivos          | Contagem  | Total             | A contagem de todas as atualizações de propriedade falhadas iniciadas a partir de dispositivos          |

### <a name="metrics-and-invoices"></a>Métricas e faturas

As métricas podem diferir dos números indicados na sua fatura Azure IoT Central. Esta situação ocorre por uma série de razões, tais como:

- Os [planos de preços padrão](https://azure.microsoft.com/pricing/details/iot-central/) da IoT Central incluem dois dispositivos e quotas de mensagens variadas gratuitamente. Enquanto os itens gratuitos são excluídos da faturação, ainda são contados nas métricas.

- IoT Central autogere um ID do dispositivo de teste para cada modelo de dispositivo na aplicação. Este ID do dispositivo é visível na página do **dispositivo de teste Manage** para um modelo de dispositivo. Os construtores de soluções podem optar por [validar os seus modelos de dispositivo antes](./overview-iot-central.md#create-device-templates) de os publicar, gerando código que utiliza estes IDs do dispositivo de teste. Embora estes dispositivos estejam excluídos da faturação, ainda são contados nas métricas.

- Embora as métricas possam mostrar um subconjunto de comunicação dispositivo-nuvem, toda a comunicação entre o dispositivo e a nuvem [conta como uma mensagem para a faturação](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu a usar modelos de aplicação, o próximo passo sugerido é aprender a [gerir o IoT Central a partir do portal Azure](howto-manage-iot-central-from-portal.md)
