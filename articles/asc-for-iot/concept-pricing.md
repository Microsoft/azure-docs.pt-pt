---
title: Compreender o Centro de Segurança Azure para os custos de IoT Microsoft Docs
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
ms.openlocfilehash: dc9dcbfd00b5205fa5c66e334b30c76d549d8a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71348517"
---
# <a name="pricing-and-associated-costs"></a>Custos associados e preços

Este artigo explica o modelo de preços do Azure Security Center para o IoT, resume todos os custos associados e explica como geri-los.

## <a name="pricing"></a>Preços

O modelo de preços azure Security Center for IoT é composto por duas partes, e é faturado uma vez que um IoT Hub é [ativado](quickstart-onboard-iot-hub.md) no Azure Security Center for IoT:

- Custo por dispositivo - capacidades de segurança incorporadas com base na análise de registos do IoT Hub.

- Custo por mensagem - capacidades de segurança melhoradas com base em mensagens de segurança de dispositivos IoT Edge ou leaf.


Para mais informações, consulte os preços do [Centro de Segurança.](https://azure.microsoft.com/pricing/details/security-center/)

## <a name="associated-costs"></a>Custos associados

O Azure Security Center for IoT tem custos associados, que não fazem parte do preço direto:


- Custos de armazenamento de Log Analytics

Pode reduzir os custos associados optando por não ter determinadas funcionalidades de solução. Opte por alterar as definições.

Para alterar as suas definições:

1. Open IoT Hub.

2. Under **Security,** clique em **Visão Geral**.

3. Clique em **Definições**.

O quadro seguinte apresenta um resumo dos custos associados e das implicações de cada opção.

|     | Utilização | Comentário |
| --- | --- | --- |
| **Armazenamento de Log Analytics** |  |
| Recomendação e alertas do dispositivo| Recomendação de segurança e alertas gerados pelo serviço | Não opcional |
| Dados de segurança bruta| Dados de segurança brutos de dispositivos IoT, recolhidos por agentes de segurança | Desativar _armazenar eventos de segurança de dispositivos brutos_ |
|

>[!Important]
> Optar por sair tem graves implicações para o Azure Security Center para a disponibilidade de funcionalidades de segurança IoT. 
  
| Opte por sair | Implicações |
| --- | --- |
| _Recolha de metadados gémeos_ | [Desativar alertas personalizados](quickstart-create-custom-alerts.md) |
| | Desativar recomendações manifestas ioT Edge |
| | Desativar recomendações e alertas baseados na identidade do dispositivo |
| _Armazenar eventos de segurança de dispositivos brutos_ | Não estão disponíveis detalhes sobre as recomendações de base do dispositivo OS |
| | Não estão disponíveis detalhes sobre [investigações](concept-security-alerts.md) de alerta e [recomendação](concept-recommendations.md) |
|


## <a name="see-also"></a>Consulte também

- Aceda aos seus [dados de segurança bruta](how-to-security-data-access.md)
- [Investigar um dispositivo](how-to-investigate-device.md)
- Compreender e explorar [recomendações](concept-recommendations.md) de segurança
- Compreender e explorar [alertas](concept-security-alerts.md) de segurança
