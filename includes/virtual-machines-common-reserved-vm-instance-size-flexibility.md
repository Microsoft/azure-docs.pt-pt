---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11-20-2018
ms.openlocfilehash: 790fb39c3d8964c053ffe1d7ee04418fcbc0913c
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857463"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Flexibilidade de tamanho de máquina virtual com instâncias de VM reservadas

Com uma instância de máquina virtual reservada otimizada para flexibilidade de tamanho de instância, a reserva comprada pode ser aplicada aos tamanhos de VMs (máquinas virtuais) no mesmo grupo de séries de tamanho. Por exemplo, se você comprar uma reserva para um tamanho de VM listado na tabela DSv2-Series, como Standard_DS5_v2, o desconto de reserva poderá ser aplicado aos outros quatro tamanhos listados na mesma tabela:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Mas esse desconto de reserva não se aplica a tamanhos de VMs listados em tabelas diferentes, como o que há na tabela de memória alta da série DSv2: Standard_DS11_v2, Standard_DS12_v2 e assim por diante.

Dentro do grupo de séries de tamanho, o número de VMs a que o desconto de reserva se aplica depende do tamanho da VM que você escolher ao comprar uma reserva. Ele também depende dos tamanhos das VMs que você tem em execução. A coluna de proporção listada nas tabelas a seguir compara a superfície relativa para cada tamanho de VM nesse grupo. Use o valor de taxa para calcular como o desconto de reserva se aplica às VMs que você está executando.

## <a name="examples"></a>Exemplos

Os exemplos a seguir usam os tamanhos e as proporções na tabela DSv2-Series.

 Você adquire uma instância de VM reservada com o tamanho Standard_DS4_v2 em que a taxa ou a superfície relativa em comparação com os outros tamanhos nessa série é 8.

- Cenário 1: Execute oito VMs de tamanho Standard_DS1_v2 com uma taxa de 1. Seu desconto de reserva se aplica a todas as oito dessas VMs.
- Cenário 2: Execute duas VMs de tamanho Standard_DS2_v2 com uma proporção de 2 cada. Também execute uma VM de tamanho Standard_DS3_v2 com uma proporção de 4. A superfície total é 2 + 2 + 4 = 8. Portanto, seu desconto de reserva se aplica a todas as três VMs.
- Cenário 3: Execute um Standard_DS5_v2 com uma taxa de 16. Seu desconto de reserva se aplica à metade do custo de computação da VM.

As seções a seguir mostram quais tamanhos estão no mesmo grupo de séries de tamanho quando você compra uma instância de VM reservada otimizada para flexibilidade de tamanho de instância.

## <a name="b-series"></a>Série B

| Size | Giro|
|---|---|
| Standard_B1s | 1 |
|Standard_B2s|4|

Para obter mais informações, consulte [tamanhos de máquina virtual expansível da série B](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="b-series-high-memory"></a>Memória alta da série B

| Size | Giro|
|---|---|
| Standard_B1ms |1|
|Standard_B2ms|4|
|Standard_B4ms|8|
|Standard_B8ms|16|

Para obter mais informações, consulte [tamanhos de máquina virtual expansível da série B](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="d-series"></a>Série D

| Size | Giro|
|---|---|
| Standard_D1|1|
|Standard_D2|2|
|Standard_D3|4|
|Standard_D4|8|

Para obter mais informações, consulte [gerações anteriores de tamanhos de máquina virtual](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="d-series-high-memory"></a>Memória alta da série D

| Size | Giro|
|---|---|
| Standard_D11|1|
|Standard_D12|2|
|Standard_D13|4|
|Standard_D14|8|

Para obter mais informações, consulte [gerações anteriores de tamanhos de máquina virtual](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series"></a>Série DS

| Size | Giro|
|---|---|
|Standard_DS1|1|
|Standard_DS2|2|
|Standard_DS3|4|
|Standard_DS4|8|

Para obter mais informações, consulte [gerações anteriores de tamanhos de máquina virtual](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series-high-memory"></a>Memória alta da série DS

| Size | Giro|
|---|---|
|Standard_DS11|1|
|Standard_DS12|2|
|Standard_DS13|4|
|Standard_DS14|8|

Para obter mais informações, consulte [gerações anteriores de tamanhos de máquina virtual](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series"></a>Série DSv2

| Size | Giro|
|---|---|
|Standard_DS1_v2|1|
|Standard_DS2_v2|2|
|Standard_DS3_v2|4|
|Standard_DS4_v2|8|
|Standard_DS5_v2|16|

Para obter mais informações, consulte [gerações anteriores de tamanhos de máquina virtual](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series-high-memory"></a>Memória alta da série DSv2

| Size | Giro|
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

Para obter mais informações, consulte [gerações anteriores de tamanhos de máquina virtual](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-high-memory-isolated-series"></a>Série isolada de memória alta DSv2

| Size | Giro|
|---|---|
|Standard_DS15i_v2|1|

Para obter mais informações, consulte [gerações anteriores de tamanhos de máquina virtual](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv3-series"></a>Série DSv3

| Size | Giro|
|---|---|
|Standard_D2s_v3|1|
|Standard_D4s_v3|2|
|Standard_D8s_v3|4|
|Standard_D16s_v3|8|
|Standard_D32s_v3|16|
|Standard_D64s_v3|32|

Para obter mais informações, consulte [tamanhos de máquina virtual de uso geral](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-1).

## <a name="dv2-series"></a>Série Dv2

| Size | Giro|
|---|---|
|Standard_D1_v2|1|
|Standard_D2_v2|2|
|Standard_D3_v2|4|
|Standard_D4_v2|8|
|Standard_D5_v2|16|

Para obter mais informações, consulte [gerações anteriores de tamanhos de máquina virtual](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dv2-series-high-memory"></a>Memória alta da série Dv2

| Size | Giro|
|---|---|
|Standard_D11_v2|1|
|Standard_D12_v2|2|
|Standard_D13_v2|4|
|Standard_D14_v2|8|
|Standard_D15_v2|10|

Para obter mais informações, consulte [gerações anteriores de tamanhos de máquina virtual](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dv2--high-memory-isolated-series"></a>Série isolada de memória alta Dv2

| Size | Giro|
|---|---|
|Standard_D15i_v2|1|

Para obter mais informações, consulte [gerações anteriores de tamanhos de máquina virtual](../articles/virtual-machines/windows/sizes-previous-gen.md).


## <a name="dv3-series"></a>Série Dv3

| Size | Giro|
|---|---|
| Standard_D2_v3|1|
|Standard_D4_v3|2|
|Standard_D8_v3|4|
|Standard_D16_v3|8|
|Standard_D32_v3|16|
|Standard_D64_v3|32|

Para obter mais informações, consulte [tamanhos de máquina virtual de uso geral](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1).

## <a name="esv3-series"></a>Série ESv3

| Size | Giro|
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
|Standard_E64s_v3|28,8|
|Standard_E64-16s_v3|28,8|
|Standard_E64-32s_v3|28,8|

Para obter mais informações, consulte [tamanhos de máquina virtual com otimização de memória](../articles/virtual-machines/windows/sizes-memory.md#esv3-series).

## <a name="esv3-series-isolated-series"></a>Série isolada da série ESv3

| Size | Giro|
|---|---|
|Standard_E64is_v3|1|

## <a name="ev3-series"></a>Série Ev3

| Size | Giro|
|---|---|
|Standard_E2_v3|1|
|Standard_E4_v3|2|
|Standard_E8_v3|4|
|Standard_E16_v3|8|
|Standard_E20_v3|10|
|Standard_E32_v3|16|
|Standard_E64_v3|32|

Para obter mais informações, consulte [tamanhos de máquina virtual com otimização de memória](../articles/virtual-machines/windows/sizes-memory.md#ev3-series).

## <a name="ev3-series-isolated-series"></a>Série isolada da série Ev3

| Size | Giro|
|---|---|
|Standard_E64i_v3|1|

Para obter mais informações, consulte [tamanhos de máquina virtual com otimização de memória](../articles/virtual-machines/windows/sizes-memory.md#ev3-series).

## <a name="f-series"></a>Série F

| Size | Giro|
|---|---|
| Standard_F1|1|
|Standard_F2|2|
|Standard_F4|4|
|Standard_F8|8|
Standard_F16|16|

Para obter mais informações, consulte [gerações anteriores de tamanhos de máquina virtual](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="fs-series"></a>Série FS

| Size | Giro|
|---|---|
| Standard_F1s|1|
|Standard_F2s|2|
|Standard_F4s|4|
|Standard_F8s|8|
|Standard_F16s|16|

Para obter mais informações, consulte [gerações anteriores de tamanhos de máquina virtual](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="fsv2-series"></a>Série Fsv2

| Size | Giro|
|---|---|
|Standard_F2s_v2|1|
|Standard_F4s_v2|2|
|Standard_F8s_v2|4|
|Standard_F16s_v2|8|
|Standard_F32s_v2|16|
|Standard_F64s_v2|32|
|Standard_F72s_v2|36|

Para obter mais informações, consulte [tamanhos de máquinas virtuais otimizadas para computação](../articles/virtual-machines/windows/sizes-compute.md#fsv2-series-1).

## <a name="h-series"></a>Série H

| Size | Giro|
|---|---|
| Standard_H8|1|
|Standard_H16|2|

Para obter mais informações, consulte [tamanhos de VM de computação de alto desempenho](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="h-series-high-memory"></a>Memória alta da série H

| Size | Giro|
|---|---|
| Standard_H8m|1|
|Standard_H16m|2|

Para obter mais informações, consulte [tamanhos de VM de computação de alto desempenho](../articles/virtual-machines/windows/sizes-hpc.md).


## <a name="hcs-series"></a>Série HCS

| Size | Giro|
|---|---|
|Standard_HC44rs|1|

Para obter mais informações, consulte [tamanhos de VM de computação de alto desempenho](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="hbs-series"></a>Série HBS

| Size | Giro|
|---|---|
|Standard_HB60rs|1|

Para obter mais informações, consulte [tamanhos de VM de computação de alto desempenho](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="hbs-series"></a>Série HBS

| Size | Giro|
|---|---|
|Standard_HB60rs|1|

Para obter mais informações, consulte [tamanhos de VM de computação de alto desempenho](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="h-series-low-latency-series"></a>Série H de baixa latência – séries

| Size | Giro|
|---|---|
|Standard_H16r|1|

Para obter mais informações, consulte [tamanhos de máquina virtual com otimização de armazenamento](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="h-series-high-memory-low-latency-series"></a>Série H de alta latência de memória insuficiente

| Size | Giro|
|---|---|
|Standard_H16mr|1|

Para obter mais informações, consulte [tamanhos de máquina virtual com otimização de armazenamento](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="h-series"></a>Série H

| Size | Giro|
|---|---|
|Standard_H8|1|
|Standard_H16|2|

Para obter mais informações, consulte [tamanhos de máquina virtual com otimização de armazenamento](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="lsv2-series-series"></a>Série série LSv2

| Size | Giro|
|---|---|
|Standard_L8s_v2|1|
|Standard_L16s_v2|2|
|Standard_L32s_v2|4|
|Standard_L48s_v2|6|
|Standard_L64s_v2|8|
|Standard_L80s_v2|10|
|Standard_L96s_v2|12|

Para obter mais informações, consulte [tamanhos de máquina virtual com otimização de armazenamento](../articles/virtual-machines/windows//sizes-storage.md#lsv2-series).

## <a name="m-series"></a>Série M

| Size | Giro|
|---|---|
| Standard_M64s|1|
|Standard_M128s|2|

Para obter mais informações, consulte [tamanhos de máquina virtual com otimização de memória](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional"></a>Fração da série M

| Size | Giro|
|---|---|
| Standard_M16s|1|
|Standard_M32s|2|

Para obter mais informações, consulte [tamanhos de máquina virtual com otimização de memória](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-high-memory"></a>Memória de alta fracionária da série M

| Size | Giro|
|---|---|
|Standard_M8ms|1|
|Standard_M8-2 MS|1|
|Standard_M8-4ms|1|
|Standard_M16ms|2|
|Standard_M16-4ms|2|
|Standard_M16-8ms|2|
|Standard_M32ms|4|
|Standard_M32-8ms|4|
|Standard_M32-16ms|4|

Para obter mais informações, consulte [tamanhos de máquina virtual com otimização de memória](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-large"></a>Fração da série M grande

| Size | Giro|
|---|---|
|Standard_M32ls|1|
|Standard_M64ls|2|

Para obter mais informações, consulte [tamanhos de máquina virtual com otimização de memória](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-high-memory"></a>Memória alta da série M

| Size | Giro|
|---|---|
|Standard_M64ms|1|
|Standard_M64-16ms|1|
|Standard_M64-32ms Standard|1|
|Standard_M128ms|2|
|Standard_M128-32ms Standard|2|
|Standard_M128-64Ms Standard|2|

Para obter mais informações, consulte [tamanhos de máquina virtual com otimização de memória](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="ms-series-fractional-tiny"></a>Pequeno Fracional da Série MS

| Size | Giro|
|---|---|
|Standard_M32ls|1|

Para obter mais informações, consulte [tamanhos de máquina virtual com otimização de memória](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="msv2-series-high-memory-series"></a>Série MSv2 de memória alta

| Size | Giro|
|---|---|
|Standard_M208ms_v2|1|
|Standard_M416ms_v2|2|

Para obter mais informações, consulte [tamanhos de máquina virtual com otimização de memória](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="msv2-series"></a>Série MSv2

| Size | Giro|
|---|---|
|Standard_M208s_v2|1|
|Standard_M416s_v2|2|

Para obter mais informações, consulte [tamanhos de máquina virtual com otimização de memória](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="nc-series"></a>Série NC

| Size | Giro|
|---|---|
|Standard_NC6|1|
|Standard_NC12|2|
|Standard_NC24|4|

Para obter mais informações, consulte [tamanhos de máquina virtual com otimização de GPU](../articles/virtual-machines/windows/sizes-gpu.md).

## <a name="ncv2-series"></a>Série NCv2

| Size | Giro|
|---|---|
| Standard_NC6s_v2|1|
|Standard_NC12s_v2|2|
|Standard_NC24s_v2|4|

Para obter mais informações, consulte [tamanhos de máquina virtual com otimização de GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).

## <a name="ncv3-series"></a>Série NCv3

| Size | Giro|
|---|---|
| Standard_NC6s_v3|1|
|Standard_NC12s_v3|2|
|Standard_NC24s_v3|4|

Para obter mais informações, consulte [tamanhos de máquina virtual com otimização de GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series).

## <a name="nd-series"></a>Série ND

| Size | Giro|
|---|---|
| Standard_ND6s|1|
|Standard_ND12s|2|
|Standard_ND24s|4|

Para obter mais informações, consulte [tamanhos de máquina virtual com otimização de GPU](../articles/virtual-machines/windows//sizes-gpu.md#nd-series).

## <a name="nv-series"></a>Série NV

| Size | Giro|
|---|---|
| Standard_NV6|1|
|Standard_NV12|2|
|Standard_NV24|4|

## <a name="nvsv3-series"></a>Série NVSv3

| Size | Giro|
|---|---|
|Standard_NV12s_v3|1|
|Standard_NV24s_v3|2|
|Standard_NV48s_v3|4|

## <a name="nds-series-low-latency-series"></a>Série NDS-séries de baixa latência

| Size | Giro|
|---|---|
|Standard_ND24rs|1|

## <a name="ncsv3-series-low-latency-series"></a>Série NCSv3 de baixa latência – séries

| Size | Giro|
|---|---|
|Standard_NC24rs_v3|1|

Para obter mais informações, consulte [tamanhos de máquina virtual com otimização de GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series).

## <a name="ncsv2-series-low-latency-series"></a>Série NCSv2 de baixa latência – séries

| Size | Giro|
|---|---|
|Standard_NC24rs_v2|1|

Para obter mais informações, consulte [tamanhos de máquina virtual com otimização de GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).

## <a name="nc-series-low-latency-series"></a>Série NC de baixa latência-séries

| Size | Giro|
|---|---|
|Standard_NC24r|1|

Para obter mais informações, consulte [tamanhos de máquina virtual com otimização de GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).





