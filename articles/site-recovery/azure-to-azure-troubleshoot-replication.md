---
title: O Azure Site Recovery de resolução de problemas para problemas de replicação em curso do Azure para o Azure | Documentos da Microsoft
description: Erros e problemas de resolução de problemas ao replicar máquinas virtuais do Azure para recuperação após desastre
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 9ff756270c368d39b7ef78d7c1046f7c91169668
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103751"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>Resolução de problemas em curso na replicação de VM do Azure para o Azure

Este artigo descreve problemas comuns no Azure Site Recovery ao replicar e recuperar máquinas virtuais do Azure a partir de uma região para outra região. Também explica como solucionar esses problemas. Para obter mais informações sobre configurações suportadas, consulte a [matriz de suporte para replicar VMs do Azure](site-recovery-support-matrix-azure-to-azure.md).

O Azure Site Recovery replica os dados da região de origem para a região de recuperação após desastre de forma consistente e cria um ponto de recuperação consistentes com falhas a cada 5 minutos. Se o Site Recovery não é possível criar pontos de recuperação durante 60 minutos, alerta-o com estas informações:

Mensagem de erro: "Não existem falhas consistente ponto de recuperação disponível para a VM nos últimos 60 minutos."</br>
ID do erro: 153007 </br>

As secções seguintes descrevem as causas e soluções.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Taxa de alteração de dados de elevada na máquina de virtual de origem
O Azure Site Recovery dispara um evento se a taxa de alteração de dados na máquina de virtual de origem for maior do que os limites suportados. Para verificar se o problema é devido ao elevado número de alterações, aceda a **itens replicados** > **VM** > **eventos-últimas 72 horas**.
Deverá ver o evento de "Taxa além dos limites suportados de alteração de dados":

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Se selecionar o evento, deverá ver as informações do disco exata:

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


### <a name="azure-site-recovery-limits"></a>Limites do Azure Site Recovery
A tabela seguinte fornece os limites do Azure Site Recovery. Estes limites baseiam-se nos nossos testes, mas não abrangem todas as combinações de e/s de aplicações possíveis. Os resultados reais podem variar consoante a combinação de E/S da sua aplicação. 

Existem limites de duas a serem considerados, alterações a dados por máquina virtual e alterações a dados por disco. Por exemplo, vamos analisar o disco Premium P20 na tabela seguinte. Recuperação de sites pode processar 5 MB/s de alterações a dados por disco com um máximo de cinco esses discos por VM, devido ao limite de 25 MB/s de quantidade total de alterações por VM.

**Destino do armazenamento da replicação** | **Tamanho médio de e/s de disco de origem** |**Alterações a dados média de disco de origem** | **Total de dados de alterações por dia para o disco de dados de origem**
---|---|---|---
Armazenamento Standard | 8 KB | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB  | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |  336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou superior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou superior |10 MB/s | 842 GB por disco

### <a name="solution"></a>Solução
O Azure Site Recovery tem limites na taxa de alterações de dados, com base no tipo de disco. Para saber se este problema é recorrente ou momentânea, encontre a que taxa da máquina virtual afetada de alteração de dados. Vá para a máquina virtual de origem, encontre as métricas sob **monitorização**e adicione as métricas, como mostrado nesta captura de ecrã:

![Processo em três etapas para localizar a taxa de alteração de dados](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Selecione **adicionar métrica**e adicione **SO disco Bytes escritos/seg** e **dados disco Bytes escritos/seg**.
2. Monitorize o pico, conforme mostrado na captura de ecrã.
3. Curso nos discos de SO e todos os discos de dados combinados de operações de escrita de ver o total. Estas métricas não poderão fornecer informações ao nível por disco, mas indicam o padrão de total de alterações a dados.

Se é um pico de um dados ocasionais de rajada e os dados alterar taxa é maior que 10 MB/s (para Premium) e 2 MB/s (por padrão) para algumas de tempo e se resume, replicação será acompanhar. Mas, se o volume de alterações está muito além do suporte limitar a maior parte do tempo, considere uma destas opções se possível:

* **Excluir o disco que está a causar uma taxa elevada de alteração de dados**: Pode excluir o disco, utilizando [PowerShell](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine).
* **Alterar a camada do disco de armazenamento de recuperação após desastre**: Esta opção só é possível se o volume de alterações de dados do disco é inferior a 10 MB/s. Vamos supor que uma VM com um disco P10 está a ter uma alterações a dados da maior que 8 MB/s, mas inferior a 10 MB/s. Se o cliente pode utilizar um disco de P30 para armazenamento de destino durante a proteção, pode ser resolvido o problema.

## <a name="Network-connectivity-problem"></a>Problemas de conectividade de rede

### <a name="network-latency-to-a-cache-storage-account"></a>Latência de rede para uma conta de armazenamento de cache
Recuperação de site envia os dados replicados para a conta de armazenamento de cache. Pode ver a latência de rede se carregar os dados de uma máquina virtual para a conta de armazenamento de cache é inferior a 4 MB em 3 segundos. 

Para verificar a existência de um problema relacionado com a latência, utilize [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) para carregar dados a partir da máquina virtual para a conta de armazenamento de cache. Se a latência alta, verifique se está a utilizar uma aplicação virtual de rede (NVA) para controlar o tráfego de rede de saída das VMs. A aplicação poderá ficar bloqueada se todo o tráfego de replicação passa através da NVA. 

Recomendamos que crie um ponto de extremidade do serviço de rede na sua rede virtual para "Armazenamento", para que o tráfego de replicação não vai para a NVA. Para obter mais informações, consulte [configuração de aplicação virtual de rede](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Conectividade de rede
Para replicação do Site Recovery para o trabalho, a conectividade de saída para URLs ou IP específicos a intervalos é necessária da VM. Se a VM estiver protegido por uma firewall ou utiliza regras de grupo (NSG) de segurança de rede para controlar a conectividade de saída, poderá deparar-se com um desses problemas. Para garantir que todos os URLs estão ligados, consulte [conectividade de saída para URLs do Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges). 