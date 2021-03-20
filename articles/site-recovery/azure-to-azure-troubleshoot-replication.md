---
title: Resolução de problemas replicação de VMs Azure com Recuperação do Local de Azure
description: Replicação de resolução de problemas na recuperação de desastres da Azure VM com recuperação do local de Azure
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 04/03/2020
ms.openlocfilehash: dc14334668b76ee8cbb81e48abfe1eecf17fa138
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96007363"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Resolução de problemas na recuperação de desastres da Azure VM

Este artigo descreve problemas comuns na Recuperação do Site Azure quando está a replicar e a recuperar máquinas virtuais Azure (VM) de uma região para outra região. Também explica como resolver os problemas comuns. Para obter mais informações sobre configurações suportadas, consulte a [matriz de suporte para replicar VMs Azure](./azure-to-azure-support-matrix.md).

A recuperação do local de azure replica consistentemente os dados da região de origem para a região de recuperação de desastres. Também cria um ponto de recuperação consistente a cada 5 minutos. Se a Recuperação do Site não conseguir criar pontos de recuperação durante 60 minutos, alerta-o com esta informação:

```plaintext
Error message: "No crash consistent recovery point available for the VM in the last 60 minutes."

Error ID: 153007
```

As seguintes secções descrevem causas e soluções.

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a>Taxa elevada de alteração de dados na máquina virtual de origem

A Azure Site Recovery cria um evento se a taxa de alteração de dados na máquina virtual de origem for superior aos limites suportados. Para ver se o problema é devido a alta agitação, vá a **itens replicados**  >  **VM**  >  **Events - duram 72 horas**.
Deve ver a taxa de alteração de dados do evento **para além dos limites suportados:**

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png" alt-text="Página de Recuperação do Site Azure que mostra uma alta taxa de mudança de dados que é muito alta.":::

Se selecionar o evento, deverá ver as informações exatas do disco:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png" alt-text="Página que mostra os detalhes do evento de alteração de dados.":::

### <a name="azure-site-recovery-limits"></a>Limites do Azure Site Recovery

A tabela seguinte fornece os limites do Azure Site Recovery. Estes limites baseiam-se nos nossos testes, mas não conseguem cobrir todas as combinações possíveis de entradas-saídas (I/O). Os resultados reais podem variar consoante a combinação de E/S da sua aplicação.

Há dois limites a ter em conta: dados por disco e dados por máquina virtual. Vejamos o disco Premium P20 na tabela seguinte, como exemplo. Para um único VM, a Recuperação do Local pode manusear 5 MB/s de churn por disco com um máximo de cinco discos deste tipo. A Recuperação do Site tem um limite de 54 MB/s de churn total por VM.

**Alvo de armazenamento de replicação** | **Tamanho médio de E/O para disco de origem** |**Churn de dados médios para disco de origem** | **Total de dados por dia para o disco de dados de origem**
---|---|---|---
Armazenamento Standard | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB    | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |    336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou superior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou superior |20 MB/s | 1684 GB por disco

### <a name="solution"></a>Solução

A Recuperação do Site Azure tem limites nas taxas de alteração de dados, dependendo do tipo de disco. Para ver se este problema é recorrente ou temporário, encontre a taxa de alteração de dados da máquina virtual afetada. Vá à máquina virtual de origem, encontre as métricas em **Monitorização** e adicione as métricas como mostrado nesta imagem:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/churn.png" alt-text="Página que mostra o processo em três etapas para encontrar a taxa de alteração de dados.":::

1. **Selecione Adicionar métrica**, e adicione **os bytes de escrita do disco de OS/Sec** e **os bytes de escrita do disco de dados/sec**.
1. Monitorize o pico como mostrado na imagem.
1. Veja o total das operações de escrita a acontecer nos discos de OS e em todos os discos de dados combinados. Estas métricas podem não lhe dar informações ao nível do disco, mas indicam o padrão total de dados.

Um aumento na taxa de mudança de dados pode vir de uma explosão de dados ocasionais. Se a taxa de alteração de dados for superior a 10 MB/s (para Premium) ou 2 MB/s (para Standard) e descer, a replicação irá recuperar. Se o churn for consistentemente muito além do limite suportado, considere uma destas opções:

- Exclua o disco que está a causar uma alta taxa de mudança de dados: Primeiro, desative a replicação. Em seguida, pode excluir o disco utilizando [o PowerShell](azure-to-azure-exclude-disks.md).
- Alterar o nível do disco de armazenamento de recuperação de desastres: Esta opção só é possível se os dados do disco forem inferiores a 20 MB/s. Por exemplo, um VM com um disco P10 tem um churn de dados superior a 8 MB/s, mas inferior a 10 MB/s. Se o cliente puder utilizar um disco P30 para armazenamento de alvo durante a proteção, o problema pode ser resolvido. Esta solução só é possível para máquinas que estejam a utilizar discos Premium-Managed. Siga estes passos:

  1. Vá aos **Discos** da máquina replicada afetada e copie o nome do disco de réplica.
  1. Vá a esta réplica do disco gerido.
  1. Pode ver um banner no **Overview** que diz que um URL SAS foi gerado. Selecione este banner e cancele a exportação. Ignore este passo se não vir o estandarte.
  1. Assim que o URL SAS for revogado, vá à **Configuração** para o disco gerido. Aumente o tamanho para que a Recuperação do Local suporte a taxa de churn observada no disco de origem.

## <a name="network-connectivity-problems"></a>Problemas de conectividade de rede

### <a name="network-latency-to-a-cache-storage-account"></a>Latência da rede para uma conta de armazenamento de cache

A Recuperação do Site envia dados replicados para a conta de armazenamento de cache. Poderá sentir latência de rede se o envio dos dados de uma máquina virtual para a conta de armazenamento de cache for mais lento do que 4 MB em 3 segundos.

Para verificar se há um problema relacionado com a latência, utilize [a AzCopy](../storage/common/storage-use-azcopy-v10.md). Pode utilizar este utilitário de linha de comando para enviar dados da máquina virtual para a conta de armazenamento de cache. Se a latência for elevada, verifique se está a utilizar um aparelho virtual de rede (NVA) para controlar o tráfego de rede de saída dos VM. O aparelho pode ser estrangulado se todo o tráfego de replicação passar pela NVA.

Recomendamos a criação de um ponto final de serviço de rede na sua rede virtual para "Armazenamento" para que o tráfego de replicação não vá para a NVA. Para obter mais informações, consulte [a configuração do aparelho virtual network](azure-to-azure-about-networking.md#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Conectividade de rede

Para que a replicação da Recuperação do Local funcione, precisa do VM para fornecer conectividade de saída a URLs ou gamas IP específicas. Pode ter o seu VM atrás de uma firewall ou utilizar as regras do grupo de segurança da rede (NSG) para controlar a conectividade de saída. Se assim for, pode ter problemas. Para se certificar de que todos os URLs estão ligados, consulte [a conectividade de saída para URLs](azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>ID de erro 153006 - Nenhum ponto de recuperação consistente disponível para o VM nos minutos "X" passados

Seguem-se algumas das questões mais comuns.

### <a name="known-issue-in-sql-server-20082008-r2"></a>Emissão conhecida no servidor SQL 2008/2008 R2

**Como corrigir:** Há um problema conhecido com o servidor SQL 2008/2008 R2. Consulte o artigo [Azure Site Recovery Agent ou outras falhas de backup VSS não componentes para um servidor que hospeda SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2).

### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Os trabalhos de recuperação do site Azure falham nos servidores que hospedam qualquer versão de instâncias do SQL Server com DBs de AUTO_CLOSE

**Como corrigir:** Consulte o artigo [Cópias de segurança VSS não componentes, tais como os trabalhos de recuperação do site Azure falham nos servidores que hospedam instâncias do SQL Server com DBs de AUTO_CLOSE](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser).

### <a name="known-issue-in-sql-server-2016-and-2017"></a>Problema conhecido no SQL Server 2016 e 2017

**Como corrigir**: Consulte o artigo [Atualização Cumulativa 16 para SQL Server 2017](https://support.microsoft.com/help/4508218/cumulative-update-16-for-sql-server-2017).

### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>Está a usar a configuração direta dos espaços de armazenamento Azure

**Como corrigir:** A recuperação do local de azure não pode criar um ponto de recuperação consistente para a configuração direta dos espaços de armazenamento. [Configure a política de replicação](azure-to-azure-how-to-enable-replication-s2d-vms.md).

### <a name="app-consistency-not-enabled-on-linux-servers"></a>Consistência de aplicativos não ativada nos servidores Linux

**Como corrigir** : A Azure Site Recovery for Linux Operation System suporta scripts personalizados de aplicações para consistência de aplicações. O script personalizado com opções pré e post será usado pelo Agente de Mobilidade de Recuperação do Site Azure para obter consistência de aplicações. [Aqui](./site-recovery-faq.md#replication) estão os passos para o permitir.

### <a name="more-causes-because-of-vss-related-issues"></a>Mais causas devido a problemas relacionados com VSS:

Para resolver mais problemas, consulte os ficheiros da máquina de origem para obter o código de erro exato para avaria:

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log`

Para localizar os erros, abra o ficheiro _vacp.log_ num editor de texto procure a cadeia **vacpError**.

```plaintext
Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|
```

No exemplo anterior, **2147754994** é o código de erro que lhe diz sobre a falha que se seguiu a esta frase.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>O escritor VSS não está instalado - Erro 2147221164

**Como corrigir**: Para gerar a etiqueta de consistência da aplicação, a Azure Site Recovery utiliza o Serviço de Cópia De Sombra de Volume (VSS). A Recuperação do Site instala um Fornecedor VSS para o seu funcionamento para obter instantâneos de consistência da aplicação. A Azure Site Recovery instala este VsS Provider como um serviço. Se o VSS Provider não estiver instalado, a criação de instantâneo de consistência da aplicação falha. Mostra o **erro de identificação 0x80040154 Classe não registada**. Consulte o artigo para a [resolução de problemas de instalação do escritor VSS](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

#### <a name="vss-writer-is-disabled---error-2147943458"></a>O escritor vss está desativado - Erro 2147943458

**Como corrigir:** Para gerar a etiqueta de consistência da aplicação, a Recuperação do Local de Azure utiliza VSS. A Recuperação do Site instala um Fornecedor VSS para o seu funcionamento para obter instantâneos de consistência da aplicação. Este VSS Provider está instalado como um serviço. Se não tiver o serviço VSS Provider ativado, a criação de instantâneo de consistência da aplicação falha. Mostra o erro: **O serviço especificado está desativado e não pode ser iniciado (0x80070422)**.

Se o VSS estiver desativado:

- Verifique se o tipo de arranque do serviço VSS Provider está definido como **Automático**.
- Reiniciar os seguintes serviços:
  - Serviço VSS.
  - Fornecedor vss de recuperação do site Azure.
  - Serviço VDS.

#### <a name="vss-provider-not_registered---error-2147754756"></a>VSS PROVIDER NOT_REGISTERED - Erro 2147754756

**Como corrigir:** Para gerar a etiqueta de consistência da aplicação, a Recuperação do Local de Azure utiliza VSS. Verifique se o serviço Azure Site Recovery VSS Provider está instalado.

Utilize os seguintes comandos para reinstalar o Fornecedor VSS:

1. Desinstalar o fornecedor existente:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd"`

1. Reinstalar o fornecedor VSS:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Verifique se o tipo de arranque do serviço VSS Provider está definido como **Automático**.

Reiniciar os seguintes serviços:

- Serviço VSS.
- Fornecedor vss de recuperação do site Azure.
- Serviço VDS.
