---
title: Compreender a pré-visualização de custos do Centro de segurança do Azure para IoT | Documentos da Microsoft
description: Saiba mais sobre os custos associados com o Centro de segurança do Azure para IoT e como controlá-las.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 00666e27c9251248aa6ecff75d88908baabf71f3
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919710"
---
# <a name="pricing-and-associated-costs"></a>Custos associados e preços

> [!IMPORTANT]
> Centro de segurança do Azure para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica o Centro de segurança do Azure (ASC) para o modelo de preços de IoT, resume todos os custos associados e explica como geri-los.

## <a name="pricing"></a>Preços

O ASC para IoT, modelo de preços é composto por duas partes e é faturado uma vez que é um IoT Hub [ativada](quickstart-onboard-iot-hub.md) no ASC para IoT:

- Custo por dispositivo - funcionalidades de segurança incorporadas com base na análise de registos do IoT Hub.

- Custo por mensagem - recursos de segurança avançada com base em mensagens de segurança de dispositivos do IoT Edge ou folha.

  >[!Note]
  > Mensagens de segurança também serão cobrado o consumo de quota no IoT Hub.

Para obter mais informações, consulte [preços do Centro de segurança](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Custos associados

ASC para IoT tem dois tipos de custos associados, que não fazem parte dos preços direto:

- Consumo de quota do IoT Hub

- Os custos de armazenamento de análise de registo

Pode reduzir os custos associados ao desativar a determinados recursos, ao alterar as definições.

Para alterar suas configurações:

1. Abra o IoT Hub.

2. Sob **Security**, clique em **descrição geral**.

3. Clique em **definições**.

A tabela seguinte fornece um resumo dos custos associados e as implicações de cada opção.

|     | Utilização | Comentário |
| --- | --- | --- |
| **Consumo de quota do IoT Hub** |  |
| [Exportar dispositivos](https://docs.microsoft.com/azure/iot-hub/iot-hub-bulk-identity-mgmt#export-devices) tarefa (exportação de twin) | Uma vez por dia | Desativar _duplo coleção de metadados_ |
| **Armazenamento do log Analytics** |  |
| Recomendação de dispositivo e alertas| Recomendação de segurança e alertas gerados pelo serviço | Não é opcional |
| Dados de segurança não processados| Dados de segurança bruto dos dispositivos de IoT, recolhidos pelos agentes de segurança | Desativar _armazenar eventos de segurança de dispositivo sem formato_ |

>[!Important]
> Desativá-las tem sérias implicações para os recursos de segurança disponíveis.
  
| Opção ativa de não participação | Implicações |
| --- | --- |
| _Coleção de metadados de duplo_ | Desativar [alertas personalizados](quickstart-create-custom-alerts.md) |
| | Desativar as recomendações de manifestos do IoT Edge |
| | Desativar alertas e recomendações com base na identidade do dispositivo |
| _Eventos de segurança de dispositivo sem formato Store_ | Detalhes sobre as recomendações de linha de base de SO do dispositivo não estão disponíveis |
| | Detalhes sobre os [alerta](concept-security-alerts.md) e [recomendação](concept-recommendations.md) investigações não estão disponíveis |


## <a name="see-also"></a>Consulte também

- Acesso à sua [dados de segurança não processados](how-to-security-data-access.md)
- [Investigar um dispositivo](how-to-investigate-device.md)
- Compreender e explorar [recomendações de segurança](concept-recommendations.md)
- Compreender e explorar [alertas de segurança](concept-security-alerts.md)
