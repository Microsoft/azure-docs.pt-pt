---
title: Série NP - Azure Virtual Machines
description: Especificações para os VMs da série NP.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: aa67a858d0396badc25a625b23dc2f2fdf1bdff9
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551378"
---
# <a name="np-series"></a>Série NP 
As máquinas virtuais da série NP são alimentadas por [Xilinx U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) FPGAs para acelerar cargas de trabalho, incluindo inferência de machine learning, transcoding de vídeo e pesquisa de bases de dados & análise. Os VMs da série NP também são alimentados por CPUs Intel Xeon 8171M (Skylake) com toda a velocidade do relógio turbo core de 3,2 GHz.

[Armazenamento Premium](premium-storage-performance.md): Suportado<br>
[Caching premium armazenamento](premium-storage-performance.md): Suportado<br>
[Migração ao Vivo](maintenance-and-updates.md): Não Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Não suportado<br>
Suporte de geração VM: Geração 1<br>
[Rede Acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): Suportado<br>
[Discos EFÉMEROS :](ephemeral-os-disks.md)Não Suportados <br>
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | FPGA | Memória FPGA: GiB | Discos de dados máximos | Max NICs/Largura de banda esperada (MBps) | 
|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


##  <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Q:** Que versão do Vitis devo usar? 

**A:** Xilinx recomenda [Vitis 2020.2](https://www.xilinx.com/products/design-tools/vitis/vitis-platform.html)


**Q:** Preciso de usar VMS NP para desenvolver a minha solução? 

**A:** Não, pode desenvolver-se no local e deslocar-se para a nuvem! Por favor, certifique-se de seguir a documentação do atestado para implementar em VMS NP. 

**Q:** Que versão do XRT devo usar?

**A:** xrt_202020.2.8.832 

**Q:** Qual é a plataforma de implantação do alvo?

**A:** Utilize as seguintes plataformas.
- xilinx-u250-gen3x16-xdma-platform-2.1-3_all
- xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1 

**Q:** Que plataforma devo apontar para o desenvolvimento?

**A:** xilinx-u250-gen3x16-xdma-2.1-202010-1-dev_1-2954688_all 

**Q:** Quais são os sistemas operativos suportados? 

**A:** A Xilinx e a Microsoft validaram o Ubuntu 18.04 LTS e o CentOS 7.8.

 A Xilinx criou as seguintes imagens de mercado para simplificar a implantação destes VMs. 

[Xilinx Alveo U250 Deployment VM – Ubuntu18.04](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_ubuntu1804_032321)

[Xilinx Alveo U250 Implantação VM – CentOS7.8](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_centos78_032321)

**Q:** Posso implementar os meus VMs Ubuntu/CentOS e instalar a Plataforma alvo XRT/Deployment? 

**R:** Sim.

**Q:** Se eu implementar o meu próprio Ubuntu18.04 VM, quais são os pacotes e passos necessários?

**A:** Utilize kernel 4.1X por [documentação Xilinx XRT](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf)
       
Instale as seguintes embalagens.
- xrt_202020.2.8.832_18.04-amd64-xrt.deb
       
- xrt_202020.2.8.832_18.04-amd64-azure.deb
       
- xilinx-u250-gen3x16-xdma-platform-2.1-3_all_18,04.deb.tar.gz
       
- xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1_all.deb  

**Q:** Em Ubuntu, depois de reiniciar o meu VM não consigo encontrar o meu FPGA(s): 

**A:** Verifique se o seu núcleo não foi atualizado (uname -a). Em caso afirmativo, por favor, desclasse para o núcleo 4.1X. 

**Q:** Se eu implementar o meu próprio CentOS7.8 VM, então quais são os pacotes e passos necessários?

**A:** Use a versão Kernel: 3.10.0-1160.15.2.el7.x86_64

 Instale as seguintes embalagens.
   
 - xrt_202020.2.8.832_7.4.1708-x86_64-xrt.rpm 
      
 - xrt_202020.2.8.832_7.4.1708-x86_64-azure.rpm 
     
 - xilinx-u250-gen3x16-xdma-platform-2.1-3.noarch.rpm.tar.gz 
      
 - xilinx-u250-gen3x16-xdma-valide-2.1-3005608.1.noarch.rpm  

**Q:** Ao executar xbutil validado no CentOS, recebo este aviso: "AVISO: A versão Kernel 3.10.0-1160.15.2.el7.x86_64 não é oficialmente suportado. 4.18.0-193 é a versão mais recente suportada." 

**A:** Isto pode ser ignorado com segurança. 

**Q:** Quais são as diferenças entre OnPrem e NP VMs em relação ao XRT? 

**A:** No Azure, a plataforma XDMA 2.1 suporta apenas funcionalidades de retenção de dados Host_Mem(SB) e DDR. 

Para ativar Host_Mem(SB) (1Gb RAM): sudo xbutil host_mem --ativar --tamanho 1g 

Para desativar Host_Mem(SB): sudo xbutil host_mem --desativar 

**Q:** Posso executar comandos xbmgmt? 

**A:** Não, nos VMs Azure não existe suporte de gestão diretamente da Azure VM. 

 **Q:** Preciso carregar um PLP? 

**A:** Não, o PLP é carregado automaticamente para si, por isso não há necessidade de carregar através de comandos xbmgmt. 

 
**Q:** O Azure suporta diferentes PLPs? 

**A:** Não neste momento. Apoiamos apenas o PLP fornecido nos pacotes da plataforma de implantação. 

**Q:** Como posso consultar a informação plp? 

**A:** Precisa executar consulta xbutil e olhar para a porção inferior. 

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Otimizada para GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
