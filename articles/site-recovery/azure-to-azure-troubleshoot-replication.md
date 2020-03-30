---
title: Replicação de lançamento de problemas de VMs Azure com recuperação do site Azure
description: Replicação de problemas na recuperação de desastres da VM Azure com recuperação do site Azure
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.openlocfilehash: 67b68cc8a1db4a058675dc51fb3805093c455908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276670"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Replicação de problemas na recuperação de desastres da VM Azure

Este artigo descreve problemas comuns na Recuperação do Sítio Azure quando está a replicar e a recuperar máquinas virtuais Azure de uma região para outra. Também explica como resolver os problemas comuns. Para obter mais informações sobre configurações suportadas, consulte a matriz de [suporte para replicar VMs Azure](site-recovery-support-matrix-azure-to-azure.md).

A Recuperação do Site Azure replica consistentemente dados da região de origem para a região de recuperação de desastres. Também cria um ponto de recuperação consistente a cada 5 minutos. Se a Recuperação do Site não conseguir criar pontos de recuperação durante 60 minutos, alerta-o com esta informação:

Error message: "No crash consistente ponto de recuperação disponível para o VM nos últimos 60 minutos."</br>
ID de erro: 153007

As seguintes secções descrevem causas e soluções.

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a><a name="high-data-change-rate-on-the-source-virtal-machine"></a>Taxa elevada de alteração de dados na máquina virtual de origem

A Recuperação do Site Azure cria um evento se a taxa de alteração de dados na máquina virtual de origem for superior aos limites suportados. Para ver se o problema se deve ao alto churn, vá a **itens replicados** > **VM** > **Eventos VM - dura72 horas**.
Deve ver o evento "Taxa de alteração de dados para além dos limites suportados":

![Página de Recuperação de Sites Azure que mostra uma alta taxa de mudança de dados que é muito alta](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Se selecionar o evento, deverá ver as informações exatas sobre o disco:

![Página que mostra os detalhes do evento de alteração de dados](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)

### <a name="azure-site-recovery-limits"></a>Limites do Azure Site Recovery

A tabela seguinte fornece os limites do Azure Site Recovery. Estes limites baseiam-se nos nossos testes, mas não podem cobrir todas as combinações possíveis de entrada de entrada (I/O). Os resultados reais podem variar consoante a combinação de E/S da sua aplicação.

Há dois limites a ter em conta: o churn de dados por disco e o churn de dados por máquina virtual. Vejamos o disco Premium P20 na tabela seguinte, por exemplo. Para um único VM, a Recuperação do Site pode manusear 5 MB/s de churn por disco com um máximo de cinco discos deste tipo. A Recuperação do Site tem um limite de 25 MB/s de total de churn por VM.

**Alvo de armazenamento de replicação** | **Tamanho médio de I/S para disco de origem** |**Recolha média de dados para disco de origem** | **Total de dados churn por dia para disco de dados de origem**
---|---|---|---
Armazenamento Standard | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |    336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou superior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou superior |20 MB/s | 1684 GB por disco

### <a name="solution"></a>Solução

A Recuperação do Site Azure tem limites nas taxas de alteração de dados, dependendo do tipo de disco. Para ver se este problema é recorrente ou temporário, encontre a taxa de mudança de dados da máquina virtual afetada. Vá à máquina virtual de origem, encontre as métricas sob **Monitorização,** e adicione as métricas como mostrado nesta imagem:

![Página que mostra o processo de três etapas para encontrar a taxa de alteração de dados](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. **Selecione Adicionar métrica**, e adicionar Os Disk Write **Bytes/Sec** e **Data Disk Write Bytes/Sec**.
1. Monitorize o pico como mostrado na imagem.
1. Veja as operações totais de escrita que estão a ocorrer através dos discos de OS e de todos os discos de dados combinados. Estas métricas podem não lhe dar informações ao nível por disco, mas indicam o padrão total de dados.

Um pico na taxa de mudança de dados pode vir de uma explosão ocasional de dados. Se a taxa de alteração de dados for superior a 10 MB/s (para Premium) ou 2 MB/s (para a Standard) e descer, a replicação irá recuperar. Se o churn está consistentemente muito além do limite suportado, considere uma destas opções:

- Exclua o disco que está a causar uma elevada taxa de mudança de dados: Primeiro, desative a replicação. Em seguida, pode excluir o disco utilizando [powerShell](./azure-to-azure-exclude-disks.md).
- Alterar o nível do disco de armazenamento de recuperação de desastres: Esta opção só é possível se o ressuposição de dados do disco for inferior a 20 MB/s. Digamos que um VM com um disco P10 tem um conjunto de dados superior a 8 MB/s, mas inferior a 10 MB/s. Se o cliente puder utilizar um disco P30 para armazenamento de alvos durante a proteção, o problema pode ser resolvido. Esta solução só é possível para máquinas que utilizem discos geridos por prémios. Siga estes passos.

    1. Vá aos **Discos** da máquina replicada afetada e copie o nome do disco de réplica.
    1. Vá a esta réplica do disco gerido.
    1. Pode ver um banner em **visão geral** que diz que um URL SAS foi gerado. Selecione este banner e cancele a exportação. Ignore este passo se não vir o estandarte.
    1. Assim que o URL SAS for revogado, vá à **Configuração** para o disco gerido. Aumente o tamanho de modo que a Recuperação do Site suporte a taxa de churn observada no disco de origem.

## <a name="network-connectivity-problems"></a><a name="Network-connectivity-problem"></a>Problemas de conectividade da rede

### <a name="network-latency-to-a-cache-storage-account"></a>Latência da rede a uma conta de armazenamento de cache

A Recuperação do Site envia dados replicados para a conta de armazenamento de cache. Poderá sentir latência da rede se o upload dos dados de uma máquina virtual para a conta de armazenamento de cache for mais lento do que 4 MB em 3 segundos.

Para verificar se há um problema relacionado com a latência, utilize o [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy). Pode utilizar este utilitário de linha de comando para fazer o upload de dados da máquina virtual para a conta de armazenamento de cache. Se a latência for elevada, verifique se está a utilizar um aparelho virtual de rede (NVA) para controlar o tráfego de rede de saída a partir de VMs. O aparelho pode ficar estrangulado se todo o tráfego de replicação passar pela NVA.

Recomendamos a criação de um ponto final do serviço de rede na sua rede virtual para "Armazenamento" para que o tráfego de replicação não vá para o NVA. Para mais informações, consulte a [configuração do aparelho virtual da Rede](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Conectividade de rede

Para que a replicação da recuperação do site funcione, precisa do VM para fornecer conectividade de saída a URLs específicos ou intervalos IP. Pode ter o seu VM atrás de uma firewall ou utilizar regras do grupo de segurança da rede (NSG) para controlar a conectividade de saída. Se assim for, pode ter problemas. Para se certificar de que todos os URLs estão ligados, consulte a [conectividade de saída para URLs](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)de Recuperação do Local .

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>Error ID 153006 - Nenhum ponto de recuperação consistente com aplicações disponíveis para o VM nos minutos "X" passados

Seguem-se algumas das questões mais comuns.

#### <a name="known-issue-in-sql-server-20082008-r2"></a>Problema conhecido no servidor SQL 2008/2008 R2

**Como corrigir**: Existe um problema conhecido com o servidor SQL 2008/2008 R2. Consulte o artigo O Agente de Recuperação do [Site Azure ou outra cópia de segurança VSS não componente falha para um servidor que hospeda o SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2).

#### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Os trabalhos de recuperação do site Azure falham nos servidores que hospedam qualquer versão de instâncias do SQL Server com DBs AUTO_CLOSE

**Como corrigir**: Consulte o artigo Cópias de [segurança VSS não componentes, tais como trabalhos de recuperação de sites do Azure, falham nos servidores que hospedam instâncias do Servidor SQL com DBs AUTO_CLOSE](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser).


#### <a name="known-issue-in-sql-server-2016-and-2017"></a>Problema conhecido no SQL Server 2016 e 2017

**Como corrigir**: Consulte o artigo O erro ocorre quando faz backup de uma máquina virtual com cópia de [segurança não baseada em componentes no SQL Server 2016 e 2017](https://support.microsoft.com/en-us/help/4508218/cumulative-update-16-for-sql-server-2017).

#### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>Está a usar a configuração direta dos espaços de armazenamento azure

**Como corrigir:** A Recuperação do Site Azure não pode criar um ponto de recuperação consistente para espaços de armazenamento Configuração direta. [Configure a política](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms)de replicação .

### <a name="more-causes-because-of-vss-related-issues"></a>Mais causas por causa de questões relacionadas com o VSS:

Para resolver ainda mais os problemas, verifique os ficheiros da máquina de origem para obter o código de erro exato para falha:

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Para localizar os erros no ficheiro, procure a cadeia "vacpError" abrindo o ficheiro vacp.log num editor.

    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

No exemplo anterior, **2147754994** é o código de erro que lhe diz sobre a falha que se segue a esta frase.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>O escritor VSS não está instalado - Error 2147221164

**Como corrigir**: Para gerar uma etiqueta de consistência da aplicação, a Recuperação do Site Azure utiliza o Serviço de Cópia sonora de Volume Shadow (VSS). A Recovery do Site instala um Fornecedor VSS para a sua operação para tirar fotos de consistência da aplicação. A Azure Site Recovery instala este Fornecedor VSS como um serviço. Se o Fornecedor VSS não estiver instalado, a criação de instantâneos de consistência da aplicação falha. Mostra o erro ID 0x80040154 "Classe não registada". Consulte o artigo para a instalação de problemas de instalação de [escritores VSS](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures).

#### <a name="vss-writer-is-disabled---error-2147943458"></a>Escritor VSS está incapacitado - Error 2147943458

**Como corrigir**: Para gerar a etiqueta de consistência da aplicação, a Recuperação do Site Azure utiliza VSS. A Recovery do Site instala um Fornecedor VSS para a sua operação para tirar fotos de consistência da aplicação. Este Fornecedor VSS está instalado como um serviço. Se não tiver o serviço VSS Provider ativado, a criação de instantâneos de consistência da aplicação falha. Mostra o erro "O serviço especificado está desativado e não pode ser iniciado (0x80070422)."

Se o VSS estiver desativado:

- Verifique se o tipo de arranque do serviço VSS Provider está definido para **Automático**.
- Reiniciar os seguintes serviços:
   - Serviço VSS
   - Fornecedor VSS de Recuperação do Site Azure
   - Serviço VDS

#### <a name="vss-provider-not_registered---error-2147754756"></a>VSS PROVIDER NOT_REGISTERED - Erro 2147754756

**Como corrigir**: Para gerar a etiqueta de consistência da aplicação, a Recuperação do Site Azure utiliza VSS. Verifique se o serviço de recuperação do site Azure VSS Provider está instalado.

Utilize os seguintes comandos para reinstalar o Fornecedor VSS:
1. Desinstalar o fornecedor existente: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
1. Reinstalar o Fornecedor VSS: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd

Verifique se o tipo de arranque do serviço VSS Provider está definido para **Automático**.

Reiniciar os seguintes serviços:
- Serviço VSS
- Fornecedor VSS de Recuperação do Site Azure
- Serviço VDS
