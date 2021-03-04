---
title: Série Msv2 (Pré-visualização) - Azure Virtual Machines
description: Especificações para os VMs da série Msv2.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: 986b02ee1127bc929ce34518226424ba06d24b89
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101550"
---
# <a name="msv2-and-mdsv2-series-medium-memory-preview"></a>Msv2 e Mdsv2 série Memória Média (Pré-visualização)


> [!IMPORTANT]
> Junte-se à pré-visualização preenchendo o formulário em **https://aka.ms/Mv2MedMemoryPreview** .  

O Msv2 e mdsv2 Série VM de memória média apresenta intel® Xeon® processador Platinum 8280 (Lago cascade) com uma frequência base de base de 2,7 GHz e 4,0 GHz de frequência monomo turbo. Com estes VMs, os clientes conseguem uma maior flexibilidade com o disco local e opções sem disco. Os clientes também têm acesso a um conjunto de novos tamanhos de VM isolados com mais CPU e memória que vão até 192 vCPU com 4 TiB de memória. 


Os VMs da série Msv2 e Mdsv2 são apenas de geração 2 e suportam um subconjunto de Imagens suportadas pela geração 2. Veja abaixo a lista completa de imagens suportadas para as séries Msv2 e Mdsv2.  

- Windows Server 2019 ou mais tarde
- SUSE Linux Enterprise Server 12 SP4 e mais tarde ou SUSE Linux Enterprise Server 15 SP1 e mais tarde
- Red Hat Enterprise Linux 7.6, 7.7, 8.1 ou mais tarde 
- Oracle Enterprise Linux 7.7 ou mais tarde

Para obter mais informações sobre máquinas virtuais geração 2, consulte [Suporte para a geração 2 VMs em Azure.](./generation-2.md)



[Armazenamento Premium](premium-storage-performance.md): Suportado<br>
[Caching premium armazenamento](premium-storage-performance.md): Suportado<br>
[Migração ao Vivo](maintenance-and-updates.md): Não Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Não suportado<br>
[Suporte vm geração](generation-2.md): Geração 2<br>
[Write Accelerator](./how-to-enable-write-accelerator.md): Suportado<br>
[Rede Acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): Suportado<br>
[Discos EFÉMEROS :](ephemeral-os-disks.md)Não Suportados <br>
<br>
 
## <a name="msv2-medium-memory-diskless"></a>Msv2 Memória Média Sem Dissi de Memória 

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Produção de disco não-abacatado por maxilar: IOPS/MBps | NICs máximos | Largura de banda de rede esperada (Mbps) | 
|---|---|---|---|---|---|---|---|
| Standard_M32ms_v2 | 32 | 875 | 0 | 32 |  20000/500 | 8 | 8000 | 
| Standard_M64s_v2 | 64 | 1024 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M64ms_v2 | 64 | 1792 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M128s_v2 | 128 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M128ms_v2 | 128 | 3892 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192is_v2 | 192 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192ims_v2 | 192 | 4096 | 0 | 64 | 80000/2000 | 8 | 30000 | 

## <a name="mdsv2-medium-memory-with-disk"></a>Memória média Mdsv2 com Disco  

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Disco de dados max | Produção de armazenamento em cache máximo e temporário: IOPS / MBps | Produção de disco não-abacatado por maxilar: IOPS/MBps | NICs máximos | Largura de banda de rede esperada (Mbps) | 
|---|---|---|---|---|---|---|---|---|
| Standard_M32dms_v2 | 32 | 875 | 1024 | 32 | 40000/400 | 20000/500 | 8 | 8000 | 
| Standard_M64ds_v2 | 64 | 1024 | 2048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M64dms_v2 | 64 | 1792 | 2048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M128ds_v2 | 128 | 2048 | 4096 | 64 |160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M128dms_v2 | 128 | 3892 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M192ids_v2 | 192 | 2048 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M192idms_v2 | 192 | 4096 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Outros tamanhos e informações

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Otimizada para GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

Calculadora de preços: [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
