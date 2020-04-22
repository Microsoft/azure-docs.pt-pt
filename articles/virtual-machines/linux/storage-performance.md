---
title: Otimizar o desempenho em máquinas virtuais da série Azure Lsv2 - Armazenamento
description: Aprenda a otimizar o desempenho da sua solução nas máquinas virtuais da série Lsv2.
services: virtual-machines-linux
author: laurenhughes
ms.service: virtual-machines-linux
ms-subservice: sizes
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/05/2019
ms.author: joelpell
ms.openlocfilehash: 7a0d5e29097bc9a672e142fcffb0ebe879fe2475
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757682"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Otimizar o desempenho nas máquinas virtuais da série Lsv2

As máquinas virtuais da série Lsv2 suportam uma variedade de cargas de trabalho que precisam de alto I/O e de entrada no armazenamento local através de uma vasta gama de aplicações e indústrias.  A série Lsv2 é ideal para bases de dados Big Data, SQL, NoSQL, armazenamento de dados e grandes bases de dados transacionais, incluindo Cassandra, MongoDB, Cloudera e Redis.

O design das Máquinas Virtuais da série Lsv2 (VMs) maximiza o processador AMD EPYC™ 7551 para proporcionar o melhor desempenho entre o processador, memória, dispositivos NVMe e VMs. Trabalhando com parceiros no Linux, várias construções estão disponíveis Azure Marketplace que estão otimizadas para o desempenho da série Lsv2 e atualmente incluem:

- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 8.0
- Debiano 9
- Debian 10

Este artigo fornece dicas e sugestões para garantir que as suas cargas de trabalho e aplicações atinjam o máximo desempenho projetado para os VMs. As informações nesta página serão continuamente atualizadas à medida que mais imagens otimizadas de Lsv2 forem adicionadas ao Azure Marketplace.

## <a name="amd-eypc-chipset-architecture"></a>Arquitetura chipset amd EYPC™

Os VMs da série Lsv2 utilizam processadores de servidorES ™ AMD EYPC com base na microarquitetura Zen. A AMD desenvolveu o Infinity Fabric (IF) para a EYPC™ como interligação escalável para o seu modelo NUMA que poderia ser usado para comunicações on-die, on-package e multi-package. Em comparação com o QPI (Quick-Path Interconnect) e UPI (Ultra-Path Interconnect) utilizados em processadores monolíticos modernos da Intel, a arquitetura de pequenas-die da AMD pode trazer benefícios de desempenho, bem como desafios. O impacto real da largura de banda da memória e dos constrangimentos de latência podem variar dependendo do tipo de carga de trabalho em funcionamento.

## <a name="tips-to-maximize-performance"></a>Dicas para maximizar o desempenho

* Se estiver a carregar um Linux GuestOS personalizado para a sua carga de trabalho, note que a Rede Acelerada será **OFF** por defeito. Se pretender ativar o Networking Acelerado, ative-o no momento da criação de VM para um melhor desempenho.

* O hardware que alimenta os VMs da série Lsv2 utiliza dispositivos NVMe com oito Pares de Fila De I/O (QP)s. Cada fila de i/O do dispositivo NVMe é na verdade um par: uma fila de submissão e uma fila de conclusão. O condutor da NVMe está configurado para otimizar a utilização destes oito QPs de I/O distribuindo I/O's em um horário de robin redondo. Para obter o desempenho máximo, executar oito empregos por dispositivo para combinar.

* Evite misturar comandos de administração NVMe (por exemplo, consulta de informação NVMe SMART, etc.) com comandos NVMe I/O durante cargas de trabalho ativas. Os dispositivos Lsv2 NVMe são apoiados pela tecnologia Hyper-V NVMe Direct, que muda para "modo lento" sempre que estiverem pendentes quaisquer comandos de administração NVMe. Os utilizadores de Lsv2 podem ver uma queda dramática no desempenho do NVMe I/O se isso acontecer.

* Os utilizadores de Lsv2 não devem confiar nas informações do dispositivo NUM (todos 0) reportados a partir do VM para unidades de dados para decidir a afinidade DA NAS para as suas apps. A forma recomendada de um melhor desempenho é espalhar cargas de trabalho por CPUs, se possível.

* A profundidade máxima de fila suportada por par de fila sv/O para o dispositivo Lsv2 VM NVMe é 1024 (vs. Amazon i3 QD 32 limite). Os utilizadores de Lsv2 devem limitar as suas cargas de trabalho de benchmarking (sintéticos) à profundidade da fila 1024 ou inferior para evitar o desencadeamento de condições completas da fila, o que pode reduzir o desempenho.

## <a name="utilizing-local-nvme-storage"></a>Utilização do armazenamento local nVMe

O armazenamento local no disco NVMe de 1.92 TB em todos os VMs Lsv2 é efémero. Durante um reboot padrão bem sucedido do VM, os dados do disco NVMe local persistirão. Os dados não persistirão no NVMe se o VM for redistribuído, desafetado ou eliminado. Os dados não persistirão se outro problema fizer com que o VM, ou o hardware em que está a funcionar, se torne insalubre. Quando isto acontece, quaisquer dados sobre o antigo hospedeiro são apagados de forma segura.

Haverá também casos em que o VM precisa de ser transferido para uma máquina hospedeira diferente, por exemplo, durante uma operação de manutenção planeada. As operações de manutenção planeadas e algumas falhas de hardware podem ser antecipadas com [eventos agendados.](scheduled-events.md) Os Eventos Agendados devem ser utilizados para se manterem atualizados em quaisquer operações de manutenção e recuperação previstas.

No caso de um evento de manutenção planeado exigir que o VM seja recriado num novo hospedeiro com discos locais vazios, os dados terão de ser resincronizados (mais uma vez, com quaisquer dados sobre o antigo hospedeiro a serem apagados de forma segura). Isto ocorre porque os VMs da série Lsv2 não suportam atualmente a migração ao vivo no disco NVMe local.

Existem dois modos de manutenção planeada.

### <a name="standard-vm-customer-controlled-maintenance"></a>Manutenção controlada pelo cliente VM padrão

- O VM é transferido para um hospedeiro atualizado durante uma janela de 30 dias.
- Os dados de armazenamento locais Lsv2 podem ser perdidos, pelo que os dados de backup antes do evento são recomendados.

### <a name="automatic-maintenance"></a>Manutenção automática

- Ocorre se o cliente não executar a manutenção controlada pelo cliente, ou em caso de procedimentos de emergência, como um evento de segurança zero-dia.
- Destinado a preservar os dados do cliente, mas existe um pequeno risco de congelamento ou reinicialização de VM.
- Os dados de armazenamento locais Lsv2 podem ser perdidos, pelo que os dados de backup antes do evento são recomendados.

Para quaisquer eventos de serviço futuros, utilize o processo de manutenção controlado para selecionar um tempo mais conveniente para si para a atualização. Antes do evento, poderá fazer o repor os seus dados no armazenamento premium. Após o evento de manutenção estar concluído, pode devolver os seus dados ao armazenamento local de NVMe de LSv2.

Os cenários que mantêm dados nos discos NVMe locais incluem:

- O VM está a correr e saudável.
- O VM é reiniciado no lugar (por si ou Azure).
- O VM é interrompido (parado sem desatribuição).
- A maioria das operações de manutenção planeadas.

Os cenários que apagam de forma segura os dados para proteger o cliente incluem:

- O VM é redistribuído, parado (desalocado) ou eliminado (por si).
- O VM torna-se insalubre e tem de servir para curar outro nó devido a um problema de hardware.
- Um pequeno número das operações de manutenção planeadas que exigem que o VM seja realojado para outro hospedeiro para manutenção.

Para saber mais sobre as opções de backup de dados no armazenamento local, consulte Backup e recuperação de [desastres para discos Azure IaaS](backup-and-disaster-recovery-for-azure-iaas-disks.md).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

* **Como posso começar a implantar VMs da série Lsv2?**  
   Tal como qualquer outro VM, utilize o [Portal,](quick-create-portal.md) [o Azure CLI](quick-create-cli.md)ou o [PowerShell](quick-create-powershell.md) para criar um VM.

* **Uma única falha no disco NVMe fará com que todos os VMs do hospedeiro falhem?**  
   Se for detetada uma falha no disco no nó do hardware, o hardware encontra-se em estado falhado. Quando isto ocorre, todos os VMs no nó são automaticamente desalocados e movidos para um nó saudável. Para os VMs da série Lsv2, isto significa que os dados do cliente sobre o nó em falha também são apagados de forma segura e terão de ser recriados pelo cliente no novo nó. Como notado, antes que a migração ao vivo fique disponível em Lsv2, os dados sobre o nó falhado serão proactivamente movidos com os VMs à medida que são transferidos para outro nó.

* **Preciso de fazer algum ajuste na rq_affinity para o desempenho?**  
   A definição rq_affinity é um pequeno ajuste quando se utilizam as operações máximas de entrada/saída por segundo (IOPS). Uma vez que tudo o resto esteja funcionando bem, então tente colocar rq_affinity a 0 para ver se faz diferença.

* **Preciso de alterar as definições blk_mq?**  
   O RHEL/CentOS 7.x utiliza automaticamente blk-mq para os dispositivos NVMe. Não são necessárias alterações ou definições de configuração. A definição scsi_mod.use_blk_mq é apenas para SCSI e foi usada durante a Pré-visualização Lsv2 porque os dispositivos NVMe eram visíveis nos VMs convidados como dispositivos SCSI. Atualmente, os dispositivos NVMe são visíveis como dispositivos NVMe, pelo que a definição de blk-mq SCSI é irrelevante.

* **Preciso de mudar de fio?**  
   Para obter o máximo de IOPS com uma ferramenta de medição de desempenho como 'fio' nos tamanhos L64v2 e L80v2 VM, delineie "rq_affinity" para 0 em cada dispositivo NVMe.  Por exemplo, esta linha de comando definirá "rq_affinity" para zero para todos os 10 dispositivos NVMe num VM L80v2:

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   Note também que o melhor desempenho é obtido quando o I/O é feito diretamente em cada um dos dispositivos NVMe crus sem divisórias, sem sistemas de ficheiros, sem config RAID 0, etc. Antes de iniciar uma sessão de teste, certifique-se `blkdiscard` de que a configuração está num estado fresco/limpo conhecido, funcionando em cada um dos dispositivos NVMe.
   
## <a name="next-steps"></a>Passos seguintes

* Consulte as especificações para todos os [VMs otimizados para o desempenho](sizes-storage.md) de armazenamento no Azure
