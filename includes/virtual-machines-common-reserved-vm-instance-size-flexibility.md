---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11-20-2018
ms.openlocfilehash: a98a941477fc83a104b55ed91f457c5a48f90d59
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029846"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Flexibilidade de tamanho de máquina virtual com instâncias de VM reservadas

Com uma instância de máquina virtual reservada que é otimizada para flexibilidade de tamanho de instância, a reserva que você compra pode se aplicar a tamanhos de máquinas virtuais (VMs) no mesmo grupo de flexibilidade de tamanho de instância. Por exemplo, se você comprar uma reserva para um tamanho de VM listado na série DSv2, como Standard_DS5_v2, o desconto de reserva poderá ser aplicado aos outros quatro tamanhos listados no mesmo grupo de flexibilidade de tamanho de instância:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Mas esse desconto de reserva não se aplica a tamanhos de VMs que estão listados em diferentes grupos de flexibilidade de tamanho de instância, como SKUs na memória alta da série DSv2: Standard_DS11_v2, Standard_DS12_v2 e assim por diante.

Dentro do grupo de flexibilidade do tamanho da instância, o número de VMs a que o desconto de reserva se aplica depende do tamanho da VM que você escolher ao comprar uma reserva. Ele também depende dos tamanhos das VMs que você tem em execução. A coluna ratio compara a superfície relativa para cada tamanho de VM nesse grupo de flexibilidade de tamanho de instância. Use o valor de taxa para calcular como o desconto de reserva se aplica às VMs que você está executando.

## <a name="examples"></a>Exemplos

Os exemplos a seguir usam os tamanhos e as proporções na tabela DSv2-Series.

Você adquire uma instância de VM reservada com o tamanho Standard_DS4_v2 em que a taxa ou a superfície relativa em comparação com os outros tamanhos nessa série é 8.

- Cenário 1: Execute oito VMs de tamanho Standard_DS1_v2 com uma taxa de 1. Seu desconto de reserva se aplica a todas as oito dessas VMs.
- Cenário 2: Execute duas VMs de tamanho Standard_DS2_v2 com uma proporção de 2 cada. Também execute uma VM de tamanho Standard_DS3_v2 com uma proporção de 4. A superfície total é 2 + 2 + 4 = 8. Portanto, seu desconto de reserva se aplica a todas as três VMs.
- Cenário 3: Execute um Standard_DS5_v2 com uma taxa de 16. Seu desconto de reserva se aplica à metade do custo de computação da VM.

As seções a seguir mostram quais tamanhos estão no mesmo grupo de séries de tamanho quando você compra uma instância de VM reservada otimizada para flexibilidade de tamanho de instância.

## <a name="instance-size-flexibility-ratio-for-vms"></a>Taxa de flexibilidade de tamanho de instância para VMs 

O CSV abaixo tem os grupos de flexibilidade de tamanho de instância, ArmSkuName e as proporções.  

[Taxas de flexibilidade de tamanho de instância](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

Manteremos a URL do arquivo e o esquema fixo para que você possa consumir esse arquivo programaticamente. Os dados também estarão disponíveis por meio da API em breve.
