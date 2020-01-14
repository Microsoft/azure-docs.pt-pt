---
title: Solucionar problemas de replicação contínua de VMs Azrue com Azure Site Recovery
description: Solução de problemas e erros ao replicar máquinas virtuais do Azure para recuperação de desastre
services: site-recovery
author: carmonmills
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.author: carmonm
ms.openlocfilehash: b738ffc36334fc540582ba29e803eb2790e2119e
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930747"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>Solucionar problemas contínuos na replicação de VM do Azure para o Azure

Este artigo descreve problemas comuns no Azure Site Recovery quando você está replicando e recuperando máquinas virtuais do Azure de uma região para outra região. Ele também explica como solucionar esses problemas. Para obter mais informações sobre configurações suportadas, consulte a [matriz de suporte para replicar VMs do Azure](site-recovery-support-matrix-azure-to-azure.md).

Azure Site Recovery Replica consistentemente os dados da região de origem para a região de recuperação de desastre e cria um ponto de recuperação consistente com falhas a cada 5 minutos. Se Site Recovery não puder criar pontos de recuperação por 60 minutos, ele o alertará com essas informações:

Mensagem de erro: "nenhum ponto de recuperação consistente com falha disponível para a VM nos últimos 60 minutos".</br>
ID do erro: 153007 </br>

As seções a seguir descrevem as causas e as soluções.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Alta taxa de alteração de dados na máquina virtual de origem
Azure Site Recovery acionará um evento se a taxa de alteração de dados na máquina virtual de origem for maior do que os limites com suporte. Para verificar se o problema se deve à alta rotatividade, acesse **itens replicados** > **VM** > **eventos – últimas 72 horas**.
Você deve ver o evento "taxa de alteração de dados além dos limites com suporte":

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Se você selecionar o evento, deverá ver as informações exatas do disco:

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


### <a name="azure-site-recovery-limits"></a>Limites do Azure Site Recovery
A tabela seguinte fornece os limites do Azure Site Recovery. Esses limites se baseiam em nossos testes, mas não podem abranger todas as combinações de e/s de aplicativo possíveis. Os resultados reais podem variar consoante a combinação de E/S da sua aplicação. 

Há dois limites a serem considerados, variação de dados por disco e variação de dados por máquina virtual. Por exemplo, vamos examinar o disco P20 Premium na tabela a seguir. Site Recovery pode lidar com 5 MB/s de rotatividade por disco com um máximo de cinco discos por VM, devido ao limite de 25 MB/s de rotatividade total por VM.

**Destino do armazenamento da replicação** | **Tamanho médio de e/s para o disco de origem** |**Variação média de dados para o disco de origem** | **Total de rotatividade de dados por dia para o disco de dados de origem**
---|---|---|---
Armazenamento Standard | 8 KB | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB  | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |  336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou superior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou superior |10 MB/s | 842 GB por disco

### <a name="solution"></a>Solução
O Azure Site Recovery tem limites na taxa de alteração de dados, com base no tipo de disco. Para saber se esse problema é recorrente ou momentâneo, localize a taxa de alteração de dados da máquina virtual afetada. Vá para a máquina virtual de origem, localize as métricas em **monitoramento**e adicione as métricas, conforme mostrado nesta captura de tela:

![Processo de três etapas para localizar a taxa de alteração de dados](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Selecione **Adicionar métrica**e adicionar **bytes de gravação de disco do sistema operacional/s** e bytes de gravação do disco de **dados/s**.
2. Monitore o pico conforme mostrado na captura de tela.
3. Exiba o total de operações de gravação que ocorrem nos discos do sistema operacional e todos os discos de dados combinados. Essas métricas podem não fornecer informações no nível por disco, mas indicam o padrão total de rotatividade de dados.

Se um pico for de uma intermitência de dados ocasional e a taxa de alteração de dados for maior que 10 MB/s (para Premium) e 2 MB/s (para o padrão) por algum tempo e ficar inativa, a replicação será atualizada. Mas se a rotatividade estiver bem além do limite com suporte na maior parte do tempo, considere uma dessas opções, se possível:

* **Exclua o disco que está causando uma alta taxa de alteração de dados**: você pode excluir o disco usando o [PowerShell](./azure-to-azure-exclude-disks.md). Para excluir o disco, você precisa desabilitar a replicação primeiro. 
* **Alterar a camada do disco de armazenamento de recuperação de desastre**: essa opção só será possível se a variação de dados do disco for menor que 20 MB/s. Digamos que uma VM com um disco P10 esteja tendo uma variação de dados maior que 8 MB/s, mas menos de 10 MB/s. Se o cliente puder usar um disco p30 para o armazenamento de destino durante a proteção, o problema poderá ser resolvido. Observe que essa solução só é possível para computadores que usam Managed Disks Premium. Siga as etapas abaixo:
    - Navegue até a folha discos da máquina replicada afetada e copie o nome do disco de réplica
    - Navegar até este disco gerenciado de réplica
    - Você pode ver uma faixa na folha de visão geral dizendo que uma URL SAS foi gerada. Clique nessa faixa e cancele a exportação. Ignore esta etapa se você não vir a faixa.
    - Assim que a URL SAS for revogada, vá para a folha de configuração do disco gerenciado e aumente o tamanho para que a ASR dê suporte à taxa de rotatividade observada no disco de origem

## <a name="Network-connectivity-problem"></a>Problemas de conectividade de rede

### <a name="network-latency-to-a-cache-storage-account"></a>Latência de rede para uma conta de armazenamento de cache
Site Recovery envia dados replicados para a conta de armazenamento de cache. Você poderá ver a latência de rede se o carregamento dos dados de uma máquina virtual para a conta de armazenamento de cache for mais lento do que 4 MB em 3 segundos. 

Para verificar um problema relacionado à latência, use [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) para carregar dados da máquina virtual para a conta de armazenamento de cache. Se a latência for alta, verifique se você está usando uma solução de virtualização de rede (NVA) para controlar o tráfego de rede de saída das VMs. O dispositivo poderá ser limitado se todo o tráfego de replicação passar pelo NVA. 

É recomendável criar um ponto de extremidade de serviço de rede em sua rede virtual para "armazenamento" para que o tráfego de replicação não vá para o NVA. Para obter mais informações, consulte [configuração de solução de virtualização de rede](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Conectividade de rede
Para replicação do Site Recovery para o trabalho, a conectividade de saída para URLs ou IP específicos a intervalos é necessária da VM. Se sua VM estiver atrás de um firewall ou usar regras de NSG (grupo de segurança de rede) para controlar a conectividade de saída, você poderá enfrentar um desses problemas. Para verificar se todas as URLs estão conectadas, confira [conectividade de saída para URLs de site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges). 

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>ID do erro 153006-nenhum ponto de recuperação consistente com o aplicativo disponível para a VM nos últimos ' XXX ' minutos

Alguns dos problemas mais comuns estão listados abaixo

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Causa 1: problema conhecido no SQL Server 2008/2008 R2 
**Como corrigir** : há um problema conhecido com o SQL Server 2008/2008 R2. Consulte este artigo da base [de conhecimento Azure site Recovery agente ou outro backup de VSS que não seja de componente falha para um servidor que hospeda SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Causa 2: os trabalhos de Azure Site Recovery falham em servidores que hospedam qualquer versão de instâncias de SQL Server com bancos de AUTO_CLOSE 
**Como corrigir** : consulte o [artigo](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) da base de conhecimento 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Causa 3: problema conhecido em SQL Server 2016 e 2017
**Como corrigir** : consulte o [artigo](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) da base de conhecimento 

#### <a name="cause-4-you-are-using-storage-spaces-direct-configuration"></a>Causa 4: você está usando a configuração de espaços de armazenamento diretos
**Como corrigir** : Azure site Recovery não é possível criar um ponto de recuperação consistente com o aplicativo para a configuração de espaços de armazenamento diretos. Consulte o artigo para [configurar corretamente a política de replicação](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms)

### <a name="more-causes-due-to-vss-related-issues"></a>Mais causas devido a problemas relacionados ao VSS:

Para solucionar os problemas, verifique os arquivos no computador de origem para obter o código de erro exato para a falha:
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Como localizar os erros no arquivo?
Procure a cadeia de caracteres "vacpError" abrindo o arquivo vacp. log em um editor
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

No exemplo acima, **2147754994** é o código de erro que informa sobre a falha, conforme mostrado abaixo

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>O gravador VSS não está instalado-erro 2147221164 

*Como corrigir*: para gerar a marca de consistência do aplicativo, Azure site Recovery usa o VSS (serviço de cópias de sombra de volume) da Microsoft. Ele instala um provedor VSS para sua operação para obter instantâneos de consistência do aplicativo. Este provedor VSS é instalado como um serviço. Caso o serviço do provedor do VSS não esteja instalado, a criação do instantâneo de consistência do aplicativo falha com a ID do erro 0x80040154 "classe não registrada". </br>
Consulte o [artigo para solução de problemas de instalação do gravador VSS](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>O gravador VSS está desabilitado-erro 2147943458

**Como corrigir**: para gerar a marca de consistência do aplicativo, Azure site Recovery usa o VSS (serviço de cópias de sombra de volume) da Microsoft. Ele instala um provedor VSS para sua operação para obter instantâneos de consistência do aplicativo. Este provedor VSS é instalado como um serviço. Caso o serviço do provedor do VSS esteja desabilitado, a criação do instantâneo de consistência do aplicativo falha com a ID do erro "o serviço especificado está desabilitado e não pode ser iniciado (0x80070422)". </br>

- Se o VSS estiver desabilitado,
    - Verifique se o tipo de inicialização do serviço do provedor do VSS está definido como **automático**.
    - Reinicie os seguintes serviços:
        - Serviço VSS
        - Azure Site Recovery o provedor de VSS
        - Serviço VDS

####  <a name="vss-provider-not_registered---error-2147754756"></a>PROVEDOR VSS NOT_REGISTERED-erro 2147754756

**Como corrigir**: para gerar a marca de consistência do aplicativo, Azure site Recovery usa o VSS (serviço de cópias de sombra de volume) da Microsoft. Verifique se o serviço do provedor VSS Azure Site Recovery está instalado ou não. </br>

- Repita a instalação do provedor usando os seguintes comandos:
- Desinstalar o provedor existente: C:\Arquivos de programas (x86) \Microsoft Azure site Recovery\agent\ InMageVSSProvider_Uninstall. cmd
- Reinstalar: C:\Arquivos de programas (x86) \Microsoft Azure site Recovery\agent\ InMageVSSProvider_Install. cmd
 
Verifique se o tipo de inicialização do serviço do provedor do VSS está definido como **automático**.
    - Reinicie os seguintes serviços:
        - Serviço VSS
        - Azure Site Recovery o provedor de VSS
        - Serviço VDS
