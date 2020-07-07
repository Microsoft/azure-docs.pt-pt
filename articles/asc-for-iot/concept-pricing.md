---
title: Custos associados e preços
description: Conheça os custos associados ao Azure Security Center para IoT e como controlá-los.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/27/2019
ms.author: mlottner
ms.openlocfilehash: fe117cf8d05ba3392b71858acf94d1fc88c1a527
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81311571"
---
# <a name="pricing-and-associated-costs"></a>Custos associados e preços

Este artigo explica o Azure Security Center para o modelo de preços IoT, resume todos os custos associados e explica como geri-los.

## <a name="pricing"></a>Preços

O modelo de preços do Azure Security Center for IoT é composto por duas partes, e é faturado uma vez que um Hub IoT está [ativado](quickstart-onboard-iot-hub.md) no Centro de Segurança Azure para IoT:

- Custo por dispositivo - capacidades de segurança incorporadas com base na análise de registos IoT Hub.

- Custo por mensagem - capacidades de segurança reforçadas com base em mensagens de segurança de dispositivos IoT Edge ou leaf.

Para mais informações, consulte [os preços do Security Center.](https://azure.microsoft.com/pricing/details/security-center/)

## <a name="associated-costs"></a>Custos associados

O Centro de Segurança Azure para ioT tem custos associados, que não fazem parte do preço direto:

- Custos de armazenamento do Log Analytics

Pode reduzir os custos associados excluindo certas funcionalidades de solução. Opte por alterar as suas definições.

Para alterar as suas definições:

1. Centro IoT aberto.

1. Em **Segurança,** clique **em Visão Geral**.

1. Clique em **Definições**.

O quadro seguinte fornece um resumo dos custos e implicações associados de cada opção.

|     | Utilização | Comentário |
| --- | --- | --- |
| **Armazenamento de Log Analytics** |  |
| Recomendação e alertas do dispositivo| Recomendação de segurança e alertas gerados pelo serviço | Não opcional |
| Dados de segurança brutos| Dados de segurança brutos de dispositivos IoT, recolhidos por agentes de segurança | Desativar _eventos de segurança de dispositivos crus_ |
|

>[!Important]
> Optar tem graves implicações para o Azure Security Center para a disponibilidade de recursos de segurança IoT.

| Opte por sair | Implicações |
| --- | --- |
| _Recolha de metadados duplos_ | Desativar [alertas personalizados](quickstart-create-custom-alerts.md) |
| | Recomendações manifestas ioT Edge desativadas |
| | Desativar recomendações e alertas baseados na identidade do dispositivo |
| _Armazenar eventos de segurança de dispositivos crus_ | Não estão disponíveis detalhes sobre as recomendações de base do dispositivo OS |
| | Não estão disponíveis detalhes sobre investigações de [alerta](concept-security-alerts.md) e [recomendações](concept-recommendations.md) |
|

## <a name="see-also"></a>Ver também

- Aceda aos seus [dados de segurança brutos](how-to-security-data-access.md)
- [Investigar um dispositivo](how-to-investigate-device.md)
- Compreender e explorar [recomendações de segurança](concept-recommendations.md)
- Compreender e explorar [alertas de segurança](concept-security-alerts.md)
