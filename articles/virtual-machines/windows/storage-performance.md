---
title: Otimizar o desempenho nas máquinas virtuais da série Lsv2 do Azure – armazenamento | Microsoft Docs
description: Saiba como otimizar o desempenho da sua solução nas máquinas virtuais da série Lsv2.
services: virtual-machines-windows
author: laurenhughes
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: 5728afe8195a8f25e5aafcb815b0c61558b32547
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101791"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Otimizar o desempenho nas máquinas virtuais da série Lsv2

As máquinas virtuais da série Lsv2 dão suporte a uma variedade de cargas de trabalho que precisam de alta e/s e produtividade no armazenamento local em uma ampla gama de aplicativos e setores.  A Lsv2-Series é ideal para Big data, SQL, bancos de dados NoSQL, data warehousing e grandes bancos de dados transacionais, incluindo Cassandra, MongoDB, Cloudera e Redis.

O design das máquinas virtuais (VMs) da série Lsv2 maximiza o processador AMD EPYC™ 7551 para fornecer o melhor desempenho entre o processador, a memória, os dispositivos NVMe e as VMs. Além de maximizar o desempenho do hardware, as VMs da série Lsv2 são projetadas para funcionar com as necessidades dos sistemas operacionais Windows e Linux para melhorar o desempenho com o hardware e o software.

O ajuste do software e do hardware resultou na versão otimizada do [Windows Server 2019 datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), lançado no início de 2018 de dezembro para o Azure Marketplace, que dá suporte ao desempenho máximo nos dispositivos NVMe nas VMs da série Lsv2.

Este artigo fornece dicas e sugestões para garantir que suas cargas de trabalho e aplicativos alcancem o desempenho máximo projetado para as VMs. As informações nesta página serão atualizadas continuamente à medida que imagens otimizadas mais Lsv2 forem adicionadas ao Azure Marketplace.

## <a name="amd-eypc-chipset-architecture"></a>Arquitetura de chipset AMD EYPC™

As VMs da série Lsv2 usam processadores de servidor AMD EYPC™ com base na microarquitetura do Zen. A AMD desenvolveu a malha de infinitos (se) para EYPC™ como interconexão escalonável para seu modelo NUMA que poderia ser usado para comunicações no chip, no pacote e em vários pacotes. Em comparação com QPI (interconexão rápida de caminho) e UPI (interconexão de ultra caminhos) usados em processadores monolíticos de chips da Intel, a arquitetura de matriz pequena de vários NUMA do AMD pode trazer os benefícios de desempenho, bem como os desafios. O impacto real da largura de banda de memória e restrições de latência pode variar dependendo do tipo de cargas de trabalho em execução.

## <a name="tips-for-maximizing-performance"></a>Dicas para maximizar o desempenho

* O hardware que alimenta as VMs da série Lsv2 utiliza dispositivos NVMe com oito pares de fila de e/s (QP). Cada fila de e/s do dispositivo NVMe é, na verdade, um par: uma fila de envio e uma fila de conclusão. O driver NVMe é configurado para otimizar a utilização desses oito QPs de e/s distribuindo e/s em uma agenda round robin. Para obter o desempenho máximo, execute oito trabalhos por dispositivo para fazer a correspondência.

* Evite misturar comandos de administrador de NVMe (por exemplo, consulta de informações inteligentes de NVMe, etc.) com os comandos de e/s de NVMe durante as cargas de trabalho ativas. Os dispositivos Lsv2 NVMe são apoiados pela tecnologia do Hyper-V NVMe Direct, que alterna para o "modo lento" sempre que qualquer comando de administração do NVMe estiver pendente. Os usuários do Lsv2 podem ver uma queda significativa no desempenho de e/s do NVMe, se isso acontecer.

* Os usuários do Lsv2 não devem confiar nas informações NUMA do dispositivo (todas as 0) relatadas de dentro da VM para unidades de dados para decidir a afinidade NUMA para seus aplicativos. A maneira recomendada para melhor desempenho é distribuir cargas de trabalho entre CPUs, se possível. 

* A profundidade máxima de fila com suporte por par de filas de e/s para o dispositivo NVMe de VM Lsv2 é 1024 (vs. Limite do Amazon i3 QD 32). Os usuários do Lsv2 devem limitar suas cargas de trabalho de benchmark (sintéticas) para a profundidade da fila 1024 ou inferior para evitar o disparo de condições completas da fila, o que pode reduzir o desempenho.

## <a name="utilizing-local-nvme-storage"></a>Utilizando o armazenamento de NVMe local

O armazenamento local no disco de 1,92 TB NVMe em todas as VMs Lsv2 é efêmero. Durante uma reinicialização padrão bem-sucedida da VM, os dados no disco da NVMe local serão mantidos. Os dados não serão persistidos no NVMe se a VM for reimplantada, desalocada ou excluída. Os dados não serão mantidos se outro problema fizer a VM, ou o hardware em que está sendo executado, se tornarem não íntegros. Quando isso acontece, todos os dados no host antigo são apagados com segurança.

Também haverá casos em que a VM precisa ser movida para um computador host diferente, por exemplo, durante uma operação de manutenção planejada. As operações de manutenção planejada e algumas falhas de hardware podem ser antecipadas com [eventos agendados](scheduled-events.md). Eventos Agendados deve ser usado para permanecer atualizado em qualquer operação prevista de manutenção e recuperação.

No caso de um evento de manutenção planejada exigir que a VM seja recriada em um novo host com discos locais vazios, os dados precisarão ser ressincronizados (novamente, com todos os dados no host antigo sendo apagados com segurança). Isso ocorre porque as VMs da série Lsv2 atualmente não dão suporte à migração dinâmica no disco NVMe local.

Há dois modos de manutenção planejada.

### <a name="standard-vm-customer-controlled-maintenance"></a>Manutenção controlada pelo cliente da VM padrão

- A VM é movida para um host atualizado durante uma janela de 30 dias.
- Lsv2 dados de armazenamento local podem ser perdidos; portanto, é recomendável fazer backup de dados antes do evento.

### <a name="automatic-maintenance"></a>Manutenção automática

- Ocorre se o cliente não executar a manutenção controlada pelo cliente ou no caso de procedimentos de emergência, como um evento de dia zero de segurança.
- Destina-se a preservar os dados do cliente, mas há um pequeno risco de um congelamento ou reinicialização da VM.
- Lsv2 dados de armazenamento local podem ser perdidos; portanto, é recomendável fazer backup de dados antes do evento.

Para qualquer evento de serviço futuro, use o processo de manutenção controlado para selecionar um horário mais conveniente para a atualização. Antes do evento, você pode fazer backup de seus dados no armazenamento Premium. Após a conclusão do evento de manutenção, você pode retornar seus dados para o armazenamento de NVMe local de VMs Lsv2 atualizadas.

Os cenários que mantêm dados em discos NVMe locais incluem:

- A VM está em execução e íntegra.
- A VM é reinicializada no local (por você ou pelo Azure).
- A VM está pausada (parada sem desalocação).
- A maioria das operações de serviço de manutenção planejada.

Os cenários que apagam dados com segurança para proteger o cliente incluem:

- A VM é reimplantada, parada (desalocada) ou excluída (por você).
- A VM se torna não íntegra e tem de reparar o serviço para outro nó devido a um problema de hardware.
- Um pequeno número de operações de manutenção de manutenção planejada que exige que a VM seja realocada para outro host para manutenção.

Para saber mais sobre as opções de backup de dados no armazenamento local, consulte [backup e recuperação de desastre para discos IaaS do Azure](backup-and-disaster-recovery-for-azure-iaas-disks.md).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

* **Como fazer iniciar a implantação de VMs da série Lsv2?**  
   Assim como qualquer outra VM, use o [portal](quick-create-portal.md), [CLI do Azure](quick-create-cli.md)ou [PowerShell](quick-create-powershell.md) para criar uma VM.

* **Uma única falha de disco de NVMe fará com que todas as VMs no host falhem?**  
   Se uma falha de disco for detectada no nó de hardware, o hardware estará em um estado de falha. Quando isso ocorre, todas as VMs no nó são automaticamente desalocadas e movidas para um nó íntegro. Para VMs da série Lsv2, isso significa que os dados do cliente no nó com falha também serão apagados com segurança e precisarão ser recriados pelo cliente no novo nó. Conforme observado, antes que a migração ao vivo se torne disponível no Lsv2, os dados no nó com falha serão movidos proativamente com as VMs à medida que forem transferidas para outro nó.

* **É necessário fazer ajustes de sondagem no Windows no Windows Server 2012 ou no Windows Server 2016?**  
   A sondagem do NVMe só está disponível no Windows Server 2019 no Azure.  

* **Posso voltar para um modelo de ISR (rotina de serviço de interrupção) tradicional?**  
   As VMs da série Lsv2 são otimizadas para sondagem de NVMe. As atualizações são fornecidas continuamente para melhorar o desempenho de sondagem.

* **Posso ajustar as configurações de sondagem no Windows Server 2019?**  
   As configurações de sondagem não são ajustáveis pelo usuário.
   
## <a name="next-steps"></a>Passos seguintes

* Consulte as especificações para todas as [VMs otimizadas para desempenho de armazenamento](sizes-storage.md) no Azure
