---
title: Limitação e compreender as quotas do IoT Hub do Azure | Documentos da Microsoft
description: Guia do programador - descrição das quotas que se aplicam ao IoT Hub e o comportamento esperado de limitação.
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/05/2018
ms.openlocfilehash: 31d3c404d22c9b8ad66c2d5d1adf34c38cc4a682
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010859"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Referência - IoT Hub quotas e limitação

## <a name="quotas-and-throttling"></a>Quotas e limitação

Cada subscrição do Azure pode ter no máximo, 50 hubs IoT e no máximo, 1 hub gratuito.

Cada hub IoT é aprovisionado com um determinado número de unidades num escalão específico. O escalão e o número de unidades de determinam a quota diária máxima de mensagens que pode enviar. O tamanho da mensagem utilizado para calcular que a quota diária é 0,5 KB para um hub de escalão gratuito e de 4KB para todos os outros escalões. Para obter mais informações, consulte [preços do Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

A camada também determina os limites de limitação que o IoT Hub impõe em todas as operações.

## <a name="operation-throttles"></a>Limitações de operação

Limitações de operação são as limitações de taxa que são aplicadas em intervalos de minutos e foram concebidas para evitar abusos. IoT Hub tenta evitar a devolver erros sempre que possível, mas começa retornando `429 ThrottlingException` se a limitação é violada durante muito tempo.

Em qualquer momento, pode aumentar as quotas ou limites de limitação do aumento do número de unidades aprovisionados num IoT hub.

A tabela seguinte mostra os imposto limitadores. Valores referem-se para um hub individual.

| Limitação | Gratuito, B1 e S1 | B2 e S2 | B3 e S3 | 
| -------- | ------- | ------- | ------- |
| Operações de registo de identidade (criar, obter, listar, atualizar e eliminar) | 1.67/SEC/Unit (100/min/unidade) | 1.67/SEC/Unit (100/min/unidade) | 83.33/SEC/Unit (5000/min/unidade) |
| Novas ligações de dispositivo (este limite aplica-se para a taxa à qual _novas ligações_ são estabelecidas, não o número total de ligações) | Mais de 100/seg ou 12/seg/unidade <br/> Por exemplo, duas unidades de S1: 2\*12 = 24 novas conexões por segundo, mas tem, pelo menos, 100 novas ligações/seg em suas unidades. Com nove unidades de S1, tem ligações novas 108/seg (9\*12) em suas unidades. | nova ligações/seg/unidade de 120 | 6000 novas ligações/seg/unidade |
| Envios do dispositivo para a cloud | Mais de 100/seg ou 12/seg/unidade <br/> Por exemplo, duas unidades de S1: 2\*12 = 24/seg, mas ter, pelo menos, 100/seg em suas unidades. Com nove unidades de S1, tem 108/seg (9\*12) em suas unidades. | 120/seg/unidade | 6000/seg/unidade |
| Na cloud para o dispositivo envia<sup>1</sup> | 1.67/SEC/Unit (100/min/unidade) | 1.67/SEC/Unit (100/min/unidade) | 83.33/SEC/Unit (5000/min/unidade) |
| Na cloud para o dispositivo recebe<sup>1</sup> <br/> (apenas quando dispositivo utiliza HTTPS)| 16.67/SEC/Unit (1000/min/unidade) | 16.67/SEC/Unit (1000/min/unidade) | 833.33/SEC/Unit (50000/min/unidade) |
| Carregamento de ficheiros | ficheiro 1.67 carregamento notificações/seg/unidade (100/min/unidade) | ficheiro 1.67 carregamento notificações/seg/unidade (100/min/unidade) | ficheiro 83.33 carregamento notificações/seg/unidade (5000/min/unidade) |
| Métodos diretos<sup>1</sup> | 160KB/seg/unidade<sup>2</sup> | 480KB/seg/unidade<sup>2</sup> | 24MB/sec/unit<sup>2</sup> | 
| Duplo (dispositivo e módulo) leituras<sup>1</sup> | 10/seg | Mais de 10 por segundo ou 1/seg/unidade | 50/seg/unidade |
| Duplo atualizações (dispositivo e módulo)<sup>1</sup> | 10/seg | Mais de 10 por segundo ou 1/seg/unidade | 50/seg/unidade |
| Tarefas de operações<sup>1,3</sup> <br/> (criar, atualizar, listar, eliminar) | 1.67/SEC/Unit (100/min/unidade) | 1.67/SEC/Unit (100/min/unidade) | 83.33/SEC/Unit (5000/min/unidade) |
| Tarefas de operações de dispositivo<sup>1</sup> <br/> (atualizar duplo, invocar o método direto) | 10/seg | Mais de 10 por segundo ou 1/seg/unidade | 50/seg/unidade |
| Configurações e implementações de borda<sup>1</sup> <br/> (criar, atualizar, listar, eliminar) | 0.33/SEC/Unit (20/min/unidade) | 0.33/SEC/Unit (20/min/unidade) | 0.33/SEC/Unit (20/min/unidade) |
| Taxa de inicialização de fluxo de dispositivo<sup>4</sup> | 5 novos fluxos/seg | 5 novos fluxos/seg | 5 novos fluxos/seg |
| Número máximo de fluxos de dispositivos conectados simultaneamente<sup>4</sup> | 50 | 50 | 50 |
| Transferência de dados de fluxo de dispositivo máximo<sup>4</sup> (agregar volume por dia) | 300 MB | 300 MB | 300 MB |


<sup>1</sup>esta funcionalidade não está disponível no escalão básico do IoT Hub. Para obter mais informações, consulte [como escolher o IoT Hub certo](iot-hub-scaling.md). <br/><sup>2</sup>limitação de tamanho do medidor é de 8 KB. <br/><sup>3</sup>só pode ter uma tarefa de importação/exportação de dispositivos ativos por vez. <br/><sup>4</sup>só estão disponíveis para S1, S2, S3, F1 do SKU e fluxos de dispositivo do IoT Hub.

O *ligações de dispositivos* limitação rege a taxa a que seja possível estabelecer novas ligações de dispositivo com um hub IoT. O *ligações de dispositivos* limitação não controlam o número máximo de dispositivos ligados em simultâneo. O *ligações de dispositivos* limitação de taxa depende do número de unidades que sejam aprovisionados para o hub IoT.

Por exemplo, se comprar uma única unidade de S1, obterá uma limitação de 100 conexões por segundo. Por conseguinte, para ligar a 100 000 dispositivos, demora, pelo menos, 1 000 segundos (aproximadamente 16 minutos). No entanto, pode ter quantos dispositivos ligados em simultâneo que tenha dispositivos registados no registo de identidade.

Para uma visão detalhada do IoT Hub na limitação do comportamento, consulte a mensagem de blogue [IoT Hub, limitação e](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/).

> [!IMPORTANT]
> Operações de registo de identidade destinam-se a utilização de tempo de execução em cenários de aprovisionamento e gestão de dispositivos. Ler ou atualizar um grande número de identidades de dispositivos é suportada através dos [importar e exportar tarefas](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).
> 
> 

## <a name="other-limits"></a>Outros limites

IoT Hub impõe outros limites operacionais:

| Operação | Limite |
| --------- | ----- |
| URIs de carregamento de ficheiros | 10000 SAS URIs podem ficar fora de uma conta de armazenamento de uma só vez. <br/> 10 SAS URIs/dispositivo podem ficar fora de uma só vez. |
| Tarefas<sup>1</sup> | Histórico de tarefas é mantido, cópia de segurança para 30 dias <br/> Máximo de tarefas simultâneas é 1 (para gratuito) e S1, 5 (para S2), 10 (para S3). |
| Pontos finais adicionais | Pago SKU hubs podem ter 10 pontos de extremidade adicionais. Os hubs de SKU gratuitos podem ter um ponto de extremidade adicional. |
| Regras de roteamento de mensagem | Pago SKU hubs podem ter regras de encaminhamento de 100. Os hubs de SKU gratuitos podem ter cinco regras de encaminhamento. |
| Mensagens do dispositivo para a cloud | 256 KB de tamanho máximo de mensagens |
| Mensagens cloud-para-dispositivo<sup>1</sup> | 64 KB de tamanho da mensagem máxima. Máximo de mensagens para entrega pendentes é 50. |
| Método direto<sup>1</sup> | Tamanho de payload do método direto máximo é 128 KB. |
| Configurações de dispositivos automático<sup>1</sup> | configurações de 100 por hub SKU paga. configurações de 20 por hub SKU gratuito. |
| Implementações de borda automática<sup>1</sup> | 20 módulos por implementação. 100 implementações por hub SKU paga. 20 implementações por hub SKU gratuito. |
| Twins<sup>1</sup> | Tamanho máximo por secção duplo (etiquetas, as propriedades pretendidas, as propriedades comunicadas) é de 8 KB |

<sup>1</sup>esta funcionalidade não está disponível no escalão básico do IoT Hub. Para obter mais informações, consulte [como escolher o IoT Hub certo](iot-hub-scaling.md).

> [!NOTE]
> Atualmente, o número máximo de dispositivos, que pode ligar a um hub IoT individual é 1.000.000. Se deseja aumentar este limite, contacte [Support da Microsoft](https://azure.microsoft.com/support/options/).

## <a name="latency"></a>Latência
IoT Hub se esforça para fornecer a baixa latência para todas as operações. No entanto, devido a condições de rede e outros fatores imprevisíveis não pode garantir uma latência máxima. Ao conceber a sua solução, deve:

* Evite fazer suposições sobre a latência máxima de qualquer operação do IoT Hub.
* Aprovisione o seu hub IoT na região do Azure mais próxima dos seus dispositivos.
* Considere utilizar o Azure IoT Edge para executar operações de sensíveis à latência no dispositivo ou num gateway perto do dispositivo.

Várias unidades do IoT Hub afetam a limitação conforme descrito anteriormente, mas não fornece quaisquer garantias ou um benefícios de latência adicional.

Se vir inesperados aumentos de latência da operação, contacte [Support da Microsoft](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Passos Seguintes

Outros tópicos de referência neste guia de programador do IoT Hub incluem:

* [Pontos finais do Hub IoT](iot-hub-devguide-endpoints.md)
