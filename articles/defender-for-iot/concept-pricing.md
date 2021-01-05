---
title: Custos associados e preços
description: Conheça os custos associados ao Defender para ioT e como controlá-los.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/08/2020
ms.author: shhazam
ms.openlocfilehash: a97bcbf5ba47289a2e68b0eaa587ea39d7fb705a
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832357"
---
# <a name="pricing-and-associated-costs"></a>Custos associados e preços

Este artigo explica o modelo de preços IoT do Defender, resume todos os custos associados e explica como geri-los.

## <a name="pricing"></a>Preços

O modelo de preços Defender for IoT é composto por duas partes, e é faturado uma vez que um Hub IoT está [ativado](quickstart-onboard-iot-hub.md) no Defender para IoT:

- Custo por dispositivo - capacidades de segurança incorporadas com base na análise de registos IoT Hub.

- Custo por mensagem - capacidades de segurança reforçadas com base em mensagens de segurança de dispositivos IoT Edge ou leaf.

Para mais informações, consulte [os preços do Security Center.](https://azure.microsoft.com/pricing/details/security-center/)

## <a name="associated-costs"></a>Custos associados

O Defender para a IoT tem custos associados, que não fazem parte do preço direto:

- Custos de armazenamento do Log Analytics

Pode reduzir os custos associados excluindo certas funcionalidades de solução. Opte por alterar as suas definições.

Para alterar as suas definições:

1. Centro IoT aberto.

1. Em **Segurança**, clique em **Definições**.

1. Clique em **Recolha de Dados.**

O quadro seguinte fornece um resumo dos custos e implicações associados de cada opção.

| Opção | Utilização | Comentário |
| --- | --- | --- |
| **Armazenamento de Log Analytics** |  |
| Recomendação e alertas do dispositivo| Recomendação de segurança e alertas gerados pelo serviço | Não opcional |
| Dados de segurança brutos| Dados de segurança brutos de dispositivos IoT, recolhidos por agentes de segurança | Desativar _eventos de segurança de dispositivos crus_ |
|

>[!Important]
> Optar por sair tem graves implicações para o Defender para a disponibilidade de recursos de segurança IoT.

| Opte por sair | Implicações |
| --- | --- |
| _Recolha de metadados duplos_ | Desativar [alertas personalizados](quickstart-create-custom-alerts.md) |
| | Recomendações manifestas ioT Edge desativadas |
| | Desativar recomendações e alertas baseados na identidade do dispositivo |
| _Armazenar eventos de segurança de dispositivos crus_ | Não estão disponíveis detalhes sobre as recomendações de base do dispositivo OS |
| | Não estão disponíveis detalhes sobre investigações de [alerta](concept-security-alerts.md) e [recomendações](concept-recommendations.md) |
|

## <a name="see-also"></a>Veja também

- Aceda aos seus [dados de segurança brutos](how-to-security-data-access.md)
- [Investigar um dispositivo](how-to-investigate-device.md)
- Compreender e explorar [recomendações de segurança](concept-recommendations.md)
- Compreender e explorar [alertas de segurança](concept-security-alerts.md)
