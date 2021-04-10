---
title: Otimizar o desempenho em máquinas virtuais da série Azure Lsv2 - Armazenamento
description: Aprenda a otimizar o desempenho para a sua solução nas máquinas virtuais da série Lsv2 usando um exemplo Linux.
services: virtual-machines-linux
author: laurenhughes
ms.service: virtual-machines
ms-subservice: vm-sizes-storage
ms.collection: linux
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/05/2019
ms.author: joelpell
ms.openlocfilehash: 99349654bb01f368a2a3a84c4ecc01f248b25175
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552767"
---
# <a name="optimize-performance-on-the-lsv2-series-linux-virtual-machines"></a>Otimizar o desempenho nas máquinas virtuais Lsv2 da série Linux

As máquinas virtuais da série Lsv2 suportam uma variedade de cargas de trabalho que necessitam de alta E/S e produção no armazenamento local através de uma vasta gama de aplicações e indústrias.  A série Lsv2 é ideal para big data, SQL, bases de dados NoSQL, armazenamento de dados e grandes bases de dados transacionais, incluindo Cassandra, MongoDB, Cloudera e Redis.

O design das Máquinas Virtuais (VMs) da série Lsv2 maximiza o processador AMD EPYC™ 7551 para proporcionar o melhor desempenho entre o processador, a memória, os dispositivos NVMe e os VMs. Trabalhando com parceiros no Linux, várias construções estão disponíveis Azure Marketplace que estão otimizadas para o desempenho da série Lsv2 e atualmente incluem:

- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 8.0
- Debian 9
- Debian 10

Este artigo fornece dicas e sugestões para garantir que as suas cargas de trabalho e aplicações obtenham o máximo desempenho projetado nos VMs. As informações nesta página serão continuamente atualizadas à medida que mais imagens otimizadas do LSV2 forem adicionadas ao Mercado Azure.

## <a name="amd-eypc-chipset-architecture"></a>AMD EYPC™ arquitetura chipset

Os VMs da série Lsv2 utilizam processadores de servidores AMD EYPC™ com base na microarquite Zen. A AMD desenvolveu o Tecido Infinito (IF) para o EYPC™ como interligação escalável para o seu modelo NUMA que poderia ser usado para comunicações on-die, on-package e multi-pacotes. Em comparação com o QPI (Quick-Path Interconnect) e o UPI (Ultra-Path Interconnect) usados em processadores modernos de monolíticos da Intel, a arquitetura de tão pequeno-die da AMD pode trazer tanto benefícios de desempenho como desafios. O impacto real da largura de banda da memória e das restrições de latência podem variar dependendo do tipo de carga de trabalho em execução.

## <a name="tips-to-maximize-performance"></a>Dicas para maximizar o desempenho

* Se estiver a carregar um Linux GuestOS personalizado para a sua carga de trabalho, note que o Networking Acelerado será **desligado** por padrão. Se pretender ativar o Networking Acelerado, ative-o no momento da criação de VM para melhor desempenho.

* O hardware que alimenta os VMs da série Lsv2 utiliza dispositivos NVMe com oito pares de filas de E/O (QP)s. Cada fila de I/O do dispositivo NVMe é na verdade um par: uma fila de submissão e uma fila de conclusão. O controlador NVMe está configurado para otimizar a utilização destes oito QPs de I/O distribuindo I/O's num horário redondo. Para obter o desempenho máximo, executar oito empregos por dispositivo para combinar.

* Evite misturar comandos de administração NVMe (por exemplo, consulta de informações NVMe SMART, etc.) com comandos NVMe I/O durante cargas de trabalho ativas. Os dispositivos Lsv2 NVMe são apoiados pela tecnologia Hyper-V NVMe Direct, que se transforma em "modo lento" sempre que quaisquer comandos de administração NVMe estiverem pendentes. Os utilizadores de Lsv2 podem ver uma queda dramática no desempenho do NVMe I/O se isso acontecer.

* Os utilizadores de Lsv2 não devem confiar nas informações do dispositivo NUMA (todos 0) reportadas a partir do VM para que os dados conduzam a decidir a afinidade NUMA pelas suas apps. A forma recomendada para um melhor desempenho é espalhar cargas de trabalho através de CPUs, se possível.

* A profundidade máxima suportada da fila por suporte de suporte para o dispositivo Lsv2 VM NVMe é de 1024 (vs. Amazon i3 QD 32 limite). Os utilizadores de Lsv2 devem limitar as suas cargas de trabalho de benchmarking (sintéticas) à profundidade da fila 1024 ou inferior para evitar desencadear condições de fila completas, o que pode reduzir o desempenho.

## <a name="utilizing-local-nvme-storage"></a>Utilização de armazenamento local de NVMe

O armazenamento local no disco 1.92 TB NVMe em todos os Lsv2 VMs é efémero. Durante um reboot padrão bem sucedido do VM, os dados do disco NVMe local persistirão. Os dados não persistirão no NVMe se o VM for redistribuído, deslocado ou eliminado. Os dados não persistirão se outro problema fizer com que o VM, ou o hardware em que está a funcionar, se torne insalubre. Quando isto acontece, qualquer dado sobre o antigo hospedeiro é apagado de forma segura.

Haverá também casos em que o VM precisa de ser transferido para uma máquina hospedeira diferente, por exemplo, durante uma operação de manutenção planeada. As operações de manutenção planeadas e algumas falhas de hardware podem ser antecipadas com [Eventos Agendados.](scheduled-events.md) Os Eventos Agendados devem ser utilizados para se manterem atualizados em quaisquer operações de manutenção e recuperação previstas.

No caso de um evento de manutenção planeado exigir que o VM seja recriado num novo hospedeiro com discos locais vazios, os dados terão de ser ressincronizados (mais uma vez, com quaisquer dados sobre o antigo hospedeiro a serem apagados de forma segura). Isto ocorre porque os VMs da série Lsv2 não suportam atualmente a migração ao vivo no disco NVMe local.

Existem dois modos de manutenção planeada.

### <a name="standard-vm-customer-controlled-maintenance"></a>Manutenção padrão controlada pelo cliente VM

- O VM é movido para um hospedeiro atualizado durante uma janela de 30 dias.
- Os dados de armazenamento locais Lsv2 podem ser perdidos, pelo que é recomendado o backup de dados antes do evento.

### <a name="automatic-maintenance"></a>Manutenção automática

- Ocorre se o cliente não executar a manutenção controlada pelo cliente, ou em caso de procedimentos de emergência, como um evento de segurança zero-dia.
- Destina-se a preservar os dados dos clientes, mas existe um pequeno risco de congelamento ou reinicialização de VM.
- Os dados de armazenamento locais Lsv2 podem ser perdidos, pelo que é recomendado o backup de dados antes do evento.

Para quaisquer eventos de serviço que se avizinham, utilize o processo de manutenção controlado para selecionar um tempo mais conveniente para a atualização. Antes do evento, pode fazer o back up dos seus dados no armazenamento premium. Após o evento de manutenção concluído, pode devolver os seus dados ao armazenamento NVMe local de Lsv2 VMs.

Os cenários que mantêm dados nos discos NVMe locais incluem:

- O VM está a funcionar e saudável.
- O VM é reiniciado no lugar (por si ou por Azure).
- O VM é pausado (parado sem desatribuição).
- A maioria das operações de manutenção planeadas.

Os cenários que apagam de forma segura os dados para proteger o cliente incluem:

- O VM é redistribuído, parado (desatribuído) ou eliminado (por si).
- O VM torna-se insalubre e tem de servir a cicatrização a outro nó devido a um problema de hardware.
- Um pequeno número das operações de manutenção planeadas que exigem que o VM seja realojado para outro hospedeiro para manutenção.

Para saber mais sobre as opções de backup de dados no armazenamento local, consulte [backup e recuperação de desastres para discos Azure IaaS](../backup-and-disaster-recovery-for-azure-iaas-disks.md).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

* **Como começo a implantar VMs da série Lsv2?**  
   Tal como qualquer outro VM, utilize o [Portal](quick-create-portal.md), [Azure CLI](quick-create-cli.md)ou [PowerShell](quick-create-powershell.md) para criar um VM.

* **Uma única falha no disco NVMe fará com que todos os VMs do hospedeiro falhem?**  
   Se for detetada uma falha no disco no nó de hardware, o hardware encontra-se num estado de falha. Quando isto ocorre, todos os VMs no nó são automaticamente desatribuídos e transferidos para um nó saudável. Para os VMs da série Lsv2, isto significa que os dados do cliente sobre o nó de falha também são apagados de forma segura e terão de ser recriados pelo cliente no novo nó. Como notado, antes da migração ao vivo ficar disponível em Lsv2, os dados sobre o nó de falha serão movidos proactivamente com os VMs à medida que são transferidos para outro nó.

* **Preciso de fazer ajustamentos para rq_affinity para o desempenho?**  
   A regulação rq_affinity é um pequeno ajuste quando se utiliza as operações máximas de entrada/saída máximas por segundo (IOPS). Uma vez que tudo o resto está funcionando bem, então tente definir rq_affinity para 0 para ver se faz diferença.

* **Preciso de alterar as definições de blk_mq?**  
   RHEL/CentOS 7.x utiliza automaticamente blk-mq para os dispositivos NVMe. Não são necessárias alterações de configuração ou configurações. A definição scsi_mod.use_blk_mq destina-se apenas a SCSI e foi utilizada durante a pré-visualização Lsv2 porque os dispositivos NVMe eram visíveis nos VMs convidados como dispositivos SCSI. Atualmente, os dispositivos NVMe são visíveis como dispositivos NVMe, pelo que a definição de blk-mq SCSI é irrelevante.

* **Preciso de mudar de "fio"?**  
   Para obter o máximo IOPS com uma ferramenta de medição de desempenho como 'fio' nos tamanhos L64v2 e L80v2 VM, de definido "rq_affinity" a 0 em cada dispositivo NVMe.  Por exemplo, esta linha de comando definirá "rq_affinity" a zero para todos os 10 dispositivos NVMe num L80v2 VM:

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   Note também que o melhor desempenho é obtido quando a E/S é feita diretamente para cada um dos dispositivos NVMe crus sem divisórias, sem sistemas de ficheiros, sem RAID 0 config, etc. Antes de iniciar uma sessão de testes, certifique-se de que a configuração está num estado fresco/limpo conhecido, funcionando `blkdiscard` em cada um dos dispositivos NVMe.
   
## <a name="next-steps"></a>Passos seguintes

* Consulte as especificações de todos os [VMs otimizados para o desempenho do armazenamento](../sizes-storage.md) no Azure
