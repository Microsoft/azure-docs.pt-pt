---
title: Flexibilidade do tamanho da máquina virtual -Azure Reservado VM Instances
description: Saiba a que série de tamanho um desconto de reserva se aplica quando você por uma instância VM reservada.
author: yashesvi
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/02/2021
ms.author: yashesvi
ms.openlocfilehash: e6e96ff41503751741c8afc2229d66fd596e0806
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525369"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Flexibilidade de tamanho da máquina virtual com o Reserved VM Instances

Ao comprar uma Instância VM Reservada, pode optar por otimizar, por exemplo, a flexibilidade do tamanho ou a prioridade de capacidade. Para obter mais informações sobre a configuração ou alteração da definição de otimização para instâncias VM reservadas, consulte [alterar a definição de otimização para instâncias VM reservadas](../cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).

Com uma caixa de máquina virtual reservada que é otimizada, por exemplo, flexibilidade de tamanho, a reserva que você compra pode aplicar-se aos tamanhos de máquinas virtuais (VMs) no mesmo grupo de flexibilidade de tamanho de exemplo. Por exemplo, se comprar uma reserva para um tamanho VM listado no Série DSv2, como Standard_DS3_v2, o desconto de reserva pode aplicar-se aos outros tamanhos listados nesse mesmo grupo de flexibilidade de tamanhos:

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

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte [o que são Reservas Azure.](../cost-management-billing/reservations/save-compute-costs-reservations.md)
