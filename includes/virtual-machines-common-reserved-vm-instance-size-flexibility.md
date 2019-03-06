---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11-20-2018
ms.openlocfilehash: 6517aec56839295af30d251f4cb5d3bb9426282c
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57457833"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Flexibilidade de tamanho de máquina virtual com as instâncias de VM reservadas

Com uma instância de máquina virtual reservada que tem otimizada por exemplo, a flexibilidade de tamanho, pode aplicar a compra de reserva os tamanhos de máquinas virtuais (VMs) no mesmo grupo de séries de tamanho. Por exemplo, se comprar uma reserva para um tamanho de VM que está listado na tabela série DSv2, como Standard_DS5_v2, pode aplicar o desconto de reserva para os outros quatro tamanhos que se encontram na mesma tabela:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Mas o desconto de reserva não se aplica a tamanhos de VMs que estão listados nas tabelas diferentes, como o que é na tabela de memória de série DSv2: Standard_DS11_v2 Standard_DS12_v2 e assim por diante.

Dentro do grupo de séries de tamanho, o número de VMs, o desconto de reserva se aplica a depende o tamanho da VM que escolher quando adquire uma reserva. Depende também os tamanhos de VMs que tem em execução. A coluna de índice que está listada nas tabelas a seguir compara os requisitos de espaço relativo para cada tamanho VM nesse grupo. Utilize o valor rácio para calcular como o desconto de reserva se aplica a VMs ter em execução.

## <a name="examples"></a>Exemplos

Os exemplos seguintes utilizam os tamanhos e proporções na tabela de série DSv2.

 Comprar uma instância VM reservada com o tamanho Standard_DS4_v2 onde o rácio ou requisitos de espaço relativo em comparação comparada os outros tamanhos essa série são 8.

- Cenário 1: Executar oito Standard_DS1_v2 tamanho VMs com um rácio de 1. O desconto de reserva se aplica a todos os oito dessas VMS.
- Cenário 2: Executar dois Standard_DS2_v2 tamanho VMs com um rácio de 2 cada. Também executar um Standard_DS3_v2 tamanho VM com um rácio de 4. Os requisitos de espaço total é 2 + 2 + 4 = 8. Portanto, seu desconto de reserva se aplica a todas as três essas VMs.
- Cenário 3: Execute um Standard_DS5_v2 com uma proporção de 16. O desconto de reserva aplica-se ao custo de computação de metade nessa VM.

O seguir secções mostram os tamanhos estão no mesmo grupo de séries de tamanho quando adquire uma instância VM reservada otimizado, por exemplo, a flexibilidade de tamanho.

## <a name="b-series"></a>Série B

| Tamanho | Proporção|
|---|---|
| Standard_B1s | 1 |
|Standard_B2s|4|

Para obter mais informações, consulte [tamanhos de máquinas de virtuais burstable de série B](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="b-series-high-memory"></a>Alto de memória de série B

| Tamanho | Proporção|
|---|---|
| Standard_B1ms |1|
|Standard_B2ms|4|
|Standard_B4ms|8|
|Standard_B8ms|16|

Para obter mais informações, consulte [tamanhos de máquinas de virtuais burstable de série B](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="d-series"></a>Série D

| Tamanho | Proporção|
|---|---|
| Standard_D1|1|
|Standard_D2|2|
|Standard_D3|4|
|Standard_D4|8|

Para obter mais informações, consulte [gerações anteriores de tamanhos de máquina virtual](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="d-series-high-memory"></a>Alto de memória de série D

| Tamanho | Proporção|
|---|---|
| Standard_D11|1|
|Standard_D12|2|
|Standard_D13|4|
|Standard_D14|8|

Para obter mais informações, consulte [gerações anteriores de tamanhos de máquina virtual](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series"></a>Série DS

| Tamanho | Proporção|
|---|---|
|Standard_DS1|1|
|Standard_DS2|2|
|Standard_DS3|4|
|Standard_DS4|8|

Para obter mais informações, consulte [gerações anteriores de tamanhos de máquina virtual](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series-high-memory"></a>Memória de série DS elevada

| Tamanho | Proporção|
|---|---|
|Standard_DS11|1|
|Standard_DS12|2|
|Standard_DS13|4|
|Standard_DS14|8|

Para obter mais informações, consulte [gerações anteriores de tamanhos de máquina virtual](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series"></a>Série DSv2

| Tamanho | Proporção|
|---|---|
|Standard_DS1_v2|1|
|Standard_DS2_v2|2|
|Standard_DS3_v2|4|
|Standard_DS4_v2|8|
|Standard_DS5_v2|16|

Para obter mais informações, consulte [tamanhos de máquinas virtuais de fins gerais](../articles/virtual-machines/windows/sizes-general.md#dv2-series).

## <a name="dsv2-series-high-memory"></a>Memória de série DSv2 elevada

| Tamanho | Proporção|
|---|---|
|Standard_DS11_v2|1|
|Standard_DS11-1_v2|1|
|Standard_DS12_v2|2|
|Standard_DS12-1_v2|2|
|Standard_DS12-2_v2|2|
|Standard_DS13_v2|4|
|Standard_DS13-2_v2|4|
|Standard_DS13-4_v2|4|
|Standard_DS14_v2|8|
|Standard_DS14-4_v2|8|
|Standard_DS14-8_v2|8|
|Standard_DS15_v2|10|

Para obter mais informações, consulte [tamanhos de máquinas virtuais com otimização de memória](../articles/virtual-machines/windows/sizes-memory.md#dsv2-series-11-15).

## <a name="dsv3-series"></a>Série DSv3

| Tamanho | Proporção|
|---|---|
|Standard_D2s_v3|1|
|Standard_D4s_v3|2|
|Standard_D8s_v3|4|
|Standard_D16s_v3|8|
|Standard_D32s_v3|16|
|Standard_D64s_v3|32|

Para obter mais informações, consulte [tamanhos de máquinas virtuais de fins gerais](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-sup1sup).

## <a name="dv2-series"></a>Série Dv2

| Tamanho | Proporção|
|---|---|
|Standard_D1_v2|1|
|Standard_D2_v2|2|
|Standard_D3_v2|4|
|Standard_D4_v2|8|
|Standard_D5_v2|16|

Para obter mais informações, consulte [tamanhos de máquinas virtuais de fins gerais](../articles/virtual-machines/windows/sizes-general.md#dv2-series).

## <a name="dv2-series-high-memory"></a>Alto de memória de série Dv2

| Tamanho | Proporção|
|---|---|
|Standard_D11_v2|1|
|Standard_D12_v2|2|
|Standard_D13_v2|4|
|Standard_D14_v2|8|
|Standard_D15_v2|10|

Para obter mais informações, consulte [tamanhos de máquinas virtuais com otimização de memória](../articles/virtual-machines/windows/sizes-memory.md#dv2-series-11-15).

## <a name="dv3-series"></a>Série Dv3

| Tamanho | Proporção|
|---|---|
| Standard_D2_v3|1|
|Standard_D4_v3|2|
|Standard_D8_v3|4|
|Standard_D16_v3|8|
|Standard_D32_v3|16|
|Standard_D64_v3|32|

Para obter mais informações, consulte [tamanhos de máquinas virtuais de fins gerais](../articles/virtual-machines/windows/sizes-general.md#dv3-series-sup1sup).

## <a name="esv3-series"></a>Série ESv3

| Tamanho | Proporção|
|---|---|
|Standard_E2s_v3|1|
|Standard_E4s_v3|2|
|Standard_E4-2s_v3|2|
|Standard_E8s_v3|4|
|Standard_E8-2s_v3|4|
|Standard_E8-4s_v3|4|
|Standard_E16s_v3|8|
|Standard_E16-4s_v3|8|
|Standard_E16-8s_v3|8|
|Standard_E20s_v3|10|
|Standard_E32s_v3|16|
|Standard_E32-8s_v3|16|
|Standard_E32-16s_v3|16|
|Standard_E64s_v3|28.8|
|Standard_E64-16s_v3|28.8|
|Standard_E64-32s_v3|28.8|

Para obter mais informações, consulte [tamanhos de máquinas virtuais com otimização de memória](../articles/virtual-machines/windows/sizes-memory.md#esv3-series).

## <a name="ev3-series"></a>Série Ev3

| Tamanho | Proporção|
|---|---|
| Standard_E2_v3|1|
|Standard_E4_v3|2|
|Standard_E8_v3|4|
|Standard_E16_v3|8|
|Standard_E20_v3|10|
|Standard_E32_v3|16|
|Standard_E64_v3|32|

Para obter mais informações, consulte [tamanhos de máquinas virtuais com otimização de memória](../articles/virtual-machines/windows/sizes-memory.md#ev3-series).

## <a name="f-series"></a>Série F

| Tamanho | Proporção|
|---|---|
| Standard_F1|1|
|Standard_F2|2|
|Standard_F4|4|
|Standard_F8|8|
Standard_F16|16|

Para obter mais informações, consulte [tamanhos de máquinas virtuais otimizadas para computação](../articles/virtual-machines/windows/sizes-compute.md#f-series).

## <a name="fs-series"></a>FS-series

| Tamanho | Proporção|
|---|---|
| Standard_F1s|1|
|Standard_F2s|2|
|Standard_F4s|4|
|Standard_F8s|8|
|Standard_F16s|16|

Para obter mais informações, consulte [tamanhos de máquinas virtuais otimizadas para computação](../articles/virtual-machines/windows/sizes-compute.md#fs-series-sup1sup).

## <a name="fsv2-series"></a>Série Fsv2

| Tamanho | Proporção|
|---|---|
|Standard_F2s_v2|1|
|Standard_F4s_v2|2|
|Standard_F8s_v2|4|
|Standard_F16s_v2|8|
|Standard_F32s_v2|16|
|Standard_F64s_v2|32|
|Standard_F72s_v2|36|

Para obter mais informações, consulte [tamanhos de máquinas virtuais otimizadas para computação](../articles/virtual-machines/windows/sizes-compute.md#fsv2-series-sup1sup).

## <a name="h-series"></a>Série H

| Tamanho | Proporção|
|---|---|
| Standard_H8|1|
|Standard_H16|2|

Para obter mais informações, consulte [tamanhos de VM de computação de alto desempenho](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="h-series-high-memory"></a>Alto de memória de série H

| Tamanho | Proporção|
|---|---|
| Standard_H8m|1|
|Standard_H16m|2|

Para obter mais informações, consulte [tamanhos de VM de computação de alto desempenho](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="ls-series"></a>Série Ls

| Tamanho | Proporção|
|---|---|
| Standard_L4s|1|
|Standard_L8s|2|
|Standard_L16s|4|
|Standard_L32s|8|

Para obter mais informações, consulte [tamanhos de máquinas virtuais otimizadas para armazenamento](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="m-series"></a>Série M

| Tamanho | Proporção|
|---|---|
| Standard_M64s|1|
|Standard_M128s|2|

Para obter mais informações, consulte [tamanhos de máquinas virtuais com otimização de memória](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional"></a>Fracionária de série M

| Tamanho | Proporção|
|---|---|
| Standard_M16s|1|
|Standard_M32s|2|

Para obter mais informações, consulte [tamanhos de máquinas virtuais com otimização de memória](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-high-memory"></a>Memória elevada fracionária de série M

| Tamanho | Proporção|
|---|---|
|Standard_M8ms|1|
|Standard_M8 2ms|1|
|Standard_M8 4ms|1|
|Standard_M16ms|2|
|Standard_M16-4ms|2|
|Standard_M16-8ms|2|
|Standard_M32ms|4|
|Standard_M32-8ms|4|
|Standard_M32-16ms|4|

Para obter mais informações, consulte [tamanhos de máquinas virtuais com otimização de memória](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-large"></a>Fracionária de série M grandes

| Tamanho | Proporção|
|---|---|
| Standard_M32ls|1|
|Standard_M64ls|2|

Para obter mais informações, consulte [tamanhos de máquinas virtuais com otimização de memória](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-high-memory"></a>Alto de memória de série M

| Tamanho | Proporção|
|---|---|
| Standard_M64ms|1|
|Standard_M64-16ms|1|
|Standard_M64-32ms|1|
|Standard_M128ms|2|
|Standard_M128 32ms|2|
|Standard_M128-64ms|2|

Para obter mais informações, consulte [tamanhos de máquinas virtuais com otimização de memória](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="nc-series"></a>Série NC

| Tamanho | Proporção|
|---|---|
| Standard_NC6|1|
|Standard_NC12|2|
|Standard_NC24|4|

Para obter mais informações, consulte [tamanhos de máquinas virtuais com otimização de GPU](../articles/virtual-machines/windows/sizes-gpu.md).

## <a name="ncv2-series"></a>Série NCv2

| Tamanho | Proporção|
|---|---|
| Standard_NC6s_v2|1|
|Standard_NC12s_v2|2|
|Standard_NC24s_v2|4|

Para obter mais informações, consulte [tamanhos de máquinas virtuais com otimização de GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).

## <a name="ncv3-series"></a>Série NCv3

| Tamanho | Proporção|
|---|---|
| Standard_NC6s_v3|1|
|Standard_NC12s_v3|2|
|Standard_NC24s_v3|4|

Para obter mais informações, consulte [tamanhos de máquinas virtuais com otimização de GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series).

## <a name="nd-series"></a>Série ND

| Tamanho | Proporção|
|---|---|
| Standard_ND6s|1|
|Standard_ND12s|2|
|Standard_ND24s|4|

Para obter mais informações, consulte [tamanhos de máquinas virtuais com otimização de GPU](../articles/virtual-machines/windows//sizes-gpu.md#nd-series).

## <a name="nv-series"></a>Série NV

| Tamanho | Proporção|
|---|---|
| Standard_NV6|1|
|Standard_NV12|2|
|Standard_NV24|4|

Para obter mais informações, consulte [tamanhos de máquinas virtuais com otimização de GPU](../articles/virtual-machines/windows//sizes-gpu.md#nv-series).


