---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02-18-2020
ms.openlocfilehash: aebe8f05bd1a3607da6d1153c87490891fc6b299
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77471465"
---
Ao comprar uma Instância VM Reservada, pode optar por otimizar, por exemplo, a flexibilidade do tamanho ou a prioridade de capacidade. Para obter mais informações sobre a configuração ou alteração da definição de otimização para instâncias VM reservadas, consulte [alterar a definição de otimização para instâncias VM reservadas](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).

Com uma caixa de máquina virtual reservada que é otimizada, por exemplo, flexibilidade de tamanho, a reserva que você compra pode aplicar-se aos tamanhos de máquinas virtuais (VMs) no mesmo grupo de flexibilidade de tamanho de exemplo. Por exemplo, se comprar uma reserva para um tamanho VM listado no Série DSv2, como Standard_DS5_v2, o desconto de reserva pode aplicar-se aos outros quatro tamanhos listados nesse mesmo grupo de flexibilidade de tamanho:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Mas esse desconto de reserva não se aplica aos tamanhos de VMs que estão listados em diferentes grupos de flexibilidade de tamanho de exemplo, como SKUs em DSv2 Series High Memory: Standard_DS11_v2, Standard_DS12_v2, e assim por diante.

Dentro do grupo de flexibilidade de tamanho de exemplo, o número de VMs que o desconto de reserva se aplica depende do tamanho VM que você escolhe quando você compra uma reserva. Também depende dos tamanhos dos VMs que tem em execução. A coluna de rácio compara a pegada relativa para cada tamanho VM nesse grupo de flexibilidade de tamanho. Utilize o valor da relação para calcular como o desconto de reserva se aplica aos VMs que tem em execução.

## <a name="examples"></a>Exemplos

Os exemplos a seguir utilizam os tamanhos e rácios na tabela da série DSv2.

Você compra uma instância VM reservada com o tamanho Standard_DS4_v2 onde o rácio ou a pegada relativa em comparação com os outros tamanhos dessa série é 8.

- Cenário 1: Executar oito VMs de tamanho Standard_DS1_v2 com um rácio de 1. O seu desconto de reserva aplica-se a todos os oito VMs.
- Cenário 2: Executar dois VMs de tamanho Standard_DS2_v2 com uma razão de 2 cada. Também executar um VM de tamanho Standard_DS3_v2 com uma proporção de 4. A pegada total é de 2+2+4=8. Assim, o seu desconto de reserva aplica-se a todos os três VMs.
- Cenário 3: Executar um Standard_DS5_v2 com uma razão de 16. O seu desconto de reserva aplica-se a metade do custo de cálculo da VM.

As secções seguintes mostram quais os tamanhos do grupo da série do mesmo tamanho quando você compra um exemplo VM reservado otimizado, por exemplo, flexibilidade de tamanho.

## <a name="instance-size-flexibility-ratio-for-vms"></a>Rácio de flexibilidade de tamanho de instância para VMs 

CSV abaixo tem os grupos de flexibilidade de tamanho de instância, ArmSkuName e os rácios.  

[Rácios de flexibilidade de tamanho de instância](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

Manteremos o URL de ficheiro e o esquema corrigidos para que possa consumir este ficheiro programáticamente. Os dados também estarão disponíveis através da API em breve.
