---
title: Otimizar o desempenho em máquinas de virtuais de série Lsv2 do Azure - armazenamento | Documentos da Microsoft
description: Saiba como otimizar o desempenho para a sua solução nas máquinas de virtuais de série Lsv2.
services: virtual-machines-linux
author: laurenhughes
manager: jeconnoc
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: 7be86c8934b8766217f9fca432327d254204f0c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60738949"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Otimizar o desempenho nas máquinas de virtuais de série Lsv2

Máquinas de virtuais de série Lsv2 suportam uma variedade de cargas de trabalho que precisam de alto débito no armazenamento local e e/s numa grande variedade de aplicativos e setores.  A série Lsv2 é ideal para grandes volumes de dados, SQL, NoSQL bases de dados, armazenamento de dados e grandes bancos de dados transacionais, incluindo o Cassandra, MongoDB, Cloudera e Redis.

O design das máquinas de virtuais de série Lsv2 (VMs) maximiza o processador de AMD EPYC™ 7551 para proporcionar o melhor desempenho entre o processador, memória, dispositivos NVMe e as VMs. Além do aumento do desempenho de hardware, as VMs da série Lsv2 foram concebidas para trabalhar com as necessidades dos sistemas operativos Linux para um melhor desempenho com o hardware e software.

Otimização de software e hardware resultou na versão otimizada do [do Canonical Ubuntu 18.04 e 16.04](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer?tab=Overview), lançada no início de Dezembro de 2018, no Azure Marketplace, que suporta o máximo desempenho nos dispositivos NVMe no VMs da série Lsv2.

Este artigo apresenta dicas e sugestões para assegurar a cargas de trabalho e aplicativos atinjam o máximo desempenho concebido para as VMs. As informações nesta página serão continuamente atualizadas à medida que mais de Lsv2 otimizado de imagens são adicionadas no Azure Marketplace.

## <a name="amd-eypc-chipset-architecture"></a>Arquitetura de chipset de AMD EYPC™

As VMs da série Lsv2 utilizam processadores de servidor AMD EYPC™ com base em microarchitecture o Zen. AMD desenvolvido infinito recursos de infraestrutura (se) para EYPC™ como dimensionável interligações para seu modelo de NUMA que poderia ser usado para comunicações-die e pacotes multi no pacote. Em comparação com QPI (caminho rápido interligações) e UPI (Ultra-Path interligações) utilizado em processadores de monolítica-die modernos Intel, arquitetura da AMD muitos NUMA pequena-die possa apresentar ambos os benefícios de desempenho, bem como desafios. O impacto real de restrições de largura de banda e latência de memória pode variar consoante o tipo de cargas de trabalho em execução.

## <a name="tips-to-maximize-performance"></a>Dicas para maximizar o desempenho

* Se estiver a carregar um GuestOS personalizado do Linux para a sua carga de trabalho, tenha em atenção de que redes aceleradas serão **OFF** por predefinição. Se pretende ativar a redes aceleradas, ativá-la no momento da criação da VM para um melhor desempenho.

* O hardware que alimenta as VMs da série Lsv2 utiliza dispositivos NVMe com oito pares de fila de e/s (QP) s. Cada fila de e/s de dispositivo de NVMe é, na verdade, um par: uma fila de envio e uma fila de conclusão. O driver de nvme, algo está configurado para otimizar a utilização dessas oito QPs de e/s ao distribuir eu / S num rodízio agendar. Para obter o máximo desempenho, execute tarefas de oito por dispositivo para corresponder.

* Evite misturar NVMe comandos de administrador (por exemplo, consulta de informações INTELIGENTES de NVMe, etc.) com os comandos de e/s de NVMe durante cargas de trabalho ativas. Dispositivos Lsv2 NVMe são apoiados por tecnologia Hyper-V NVMe direto, o que muda para o "modo lento" sempre que os comandos de administrador de NVMe estão pendentes. Lsv2 utilizadores podem obter um desempenho dramática soltar no desempenho de e/s de NVMe, se isso acontecer.

* Os utilizadores de Lsv2 não deverá confiar nas informações de do dispositivo (todos os 0) reportadas a partir de dentro da VM para unidades de dados para decidir a afinidade para seus aplicativos. É a forma recomendada para um melhor desempenho distribuir as cargas de trabalho entre CPUs, se possível.

* A profundidade de fila de suportado máximo por par de filas de e/s para o dispositivo de Lsv2 VM NVMe é 1024 (vs. Amazon i3 QD 32 limite). Os utilizadores de Lsv2 devem limitar suas cargas de trabalho de benchmark (sintéticas) a profundidade de fila de 1024 bits ou inferior para evitar acionar condições total de fila, o que podem reduzir o desempenho.

## <a name="utilizing-local-nvme-storage"></a>Utilizar o armazenamento de NVMe local

Armazenamento local no disco de NVMe 1.92 TB em todas as VMs de Lsv2 é efémeros. Durante uma reinicialização bem sucedida padrão da VM, os dados no disco local NVMe serão mantidas. Os dados não serão mantidas no NVMe se a VM é reimplementada, remover a atribuição ou eliminada. Dados não persistirá se a outro problema faz com que a VM ou o hardware está em execução, para se tornar mau estado de funcionamento. Quando isto acontecer, todos os dados no anfitrião antigo são apagados em segurança.

Também haverá casos quando a VM tem de ser movidos para uma máquina de anfitrião diferente, por exemplo, durante uma operação de manutenção planeada. Operações de manutenção planeada e algumas falhas de hardware podem ser previstas com [eventos agendados](scheduled-events.md). Eventos agendados devem ser usados para se manter atualizado em qualquer manutenção prevista e operações de recuperação.

No caso do que um evento de manutenção planeada requer que a VM ser recriada num anfitrião novo com discos locais vazios, os dados terá de ser ressincronizada (novamente, com todos os dados no anfitrião antigo a ser apagado em segurança). Isto ocorre porque as VMs da série Lsv2 não suportam atualmente migração em direto no disco local NVMe.

Existem dois modos para a manutenção planeada.

### <a name="standard-vm-customer-controlled-maintenance"></a>Manutenção de controlado pelo cliente VM Standard

- A VM é movida para um anfitrião atualizado durante uma janela de 30 dias.
- Dados de armazenamento local Lsv2 poderão se perder, portanto, são recomendados criar cópias de segurança dados anterior ao evento.

### <a name="automatic-maintenance"></a>Manutenção automática

- Ocorre se o cliente não executar a manutenção controlado pelo cliente ou em caso de emergência procedimentos, como um evento de dia zero de segurança.
- A finalidade de preservar dados dos clientes, mas há um pequeno risco de um congelamento VM ou um reinício.
- Dados de armazenamento local Lsv2 poderão se perder, portanto, são recomendados criar cópias de segurança dados anterior ao evento.

Para quaisquer eventos futura do serviço, utilize o processo de manutenção controlado para selecionar um período de tempo mais conveniente para a atualização. Antes do evento, pode fazer backup dos dados no armazenamento premium. Depois de concluir o evento de manutenção, pode retornar os dados no armazenamento atualizado para o Lsv2 VMs local NVMe.

Cenários de manter os dados em discos de NVMe locais incluem:

- A VM está em execução e em bom estado.
- A VM é reiniciada no local (por utilizador ou do Azure).
- A VM está em pausa (parado sem Desalocação).
- A maioria da manutenção planeada, as operações de serviço.

Os cenários que apague em segurança de dados para proteger o cliente incluem:

- A VM está a ser reimplementada, parada (desalocada), ou eliminado (por si).
- A VM fica em mau estado de funcionamento e tem de serviço tratá-lo para outro nó devido a um problema de hardware.
- Um pequeno número de manutenção planeada, as operações de serviço que requer que a VM sejam realocados para outro anfitrião para a manutenção.

Para saber mais sobre as opções para backup dos dados no armazenamento local, veja [cópia de segurança e recuperação após desastre para discos IaaS do Azure](backup-and-disaster-recovery-for-azure-iaas-disks.md).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

* **Como iniciar a implementação de VMs de série Lsv2?**  
   Muito como qualquer outra VM, utilize o [Portal](quick-create-portal.md), [CLI do Azure](quick-create-cli.md), ou [PowerShell](quick-create-powershell.md) para criar uma VM.

* **Uma única falha de disco de NVMe causará todas as VMs no anfitrião para efetuar a ativação?**  
   Se for detetada uma falha de disco no nó de hardware, o hardware está em estado de falha. Quando isto ocorrer, todas as VMs no nó são automaticamente desalocar e movidas para um nó de bom estado de funcionamento. Para VMs de série Lsv2, isso significa que os dados do cliente no nó com defeito são apagados também em segurança e terão de ser recriados pelo cliente no novo nó. Como observado, para que migração em direto fica disponível no Lsv2, os dados no nó com falha serão proativamente movidos com as VMs à medida que são transferidos para outro nó.

* **É necessário fazer ajustamentos aos rq_affinity para um desempenho?**  
   A definição de rq_affinity é um pequeno ajuste, ao utilizar as operações de entrada/saída máximas absolutas por segundo (IOPS). Assim que tudo está funcionando bem, em seguida, tente definir rq_affinity como 0 para ver se faz uma diferença.

* **É necessário alterar as definições de blk_mq?**  
   RHEL/CentOS 7.x utiliza automaticamente blk mq para os dispositivos NVMe. Sem alterações de configuração ou as definições são necessárias. A definição de scsi_mod.use_blk_mq destina-se apenas ao SCSI e foi utilizada durante a pré-visualização de Lsv2 porque os dispositivos NVMe eram visíveis nas VMs de convidados, como dispositivos SCSI. Atualmente, os dispositivos NVMe ficam visíveis como dispositivos NVMe, para que a definição de blk mq SCSI é irrelevante.

* **É necessário alterar o "fio"?**  
   Para obter o máximo IOPS com um desempenho a medir a ferramenta como o fio no tamanho dos L64v2 e L80v2 VM, defina "rq_affinity" como 0 em cada dispositivo de NVMe.  Por exemplo, esta linha de comandos irá definir "rq_affinity" como zero para todos os dispositivos NVMe 10 numa L80v2 VM:

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   Observe também que o melhor desempenho é obtido quando a e/s é realizada diretamente para cada um dos dispositivos NVMe não processados com sem a criação de partições, não existem sistemas de ficheiros, não RAID 0 configuração, etc. Antes de iniciar uma sessão de teste, certifique-se a configuração está num estado conhecido atualizados/limpar executando `blkdiscard` em cada um dos dispositivos NVMe.
   
## <a name="next-steps"></a>Passos Seguintes

* Consulte as especificações para todos os [VMs otimizadas para desempenho de armazenamento](sizes-storage.md) no Azure
