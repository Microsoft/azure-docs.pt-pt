---
title: Recurso de restauração instantânea do Azure
description: Capacidade de restauração instantânea do Azure e perguntas frequentes para pilha de backup da VM, modelo de implantação do Resource Manager
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 21e5ae82fc8274874e97d5e91a140b811b36c05e
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293832"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Obtenha melhor desempenho de backup e restauração com o recurso de restauração instantânea do backup do Azure

> [!NOTE]
> Com base nos comentários dos usuários, estamos renomeando a **pilha de backup da VM v2** para **restauração instantânea** para reduzir a confusão com Azure Stack funcionalidade.
> Todos os usuários do backup do Azure agora foram atualizados para **restauração instantânea**.

O novo modelo para restauração instantânea fornece os seguintes aprimoramentos de recursos:

* Capacidade de usar instantâneos tirados como parte de um trabalho de backup que está disponível para recuperação sem aguardar a conclusão da transferência de dados para o cofre. Ele reduz o tempo de espera para que os instantâneos sejam copiados para o cofre antes de disparar a restauração.
* Reduz os tempos de backup e restauração mantendo instantâneos localmente, por padrão, por dois dias. Esse valor de retenção de instantâneo padrão é configurável para qualquer valor entre 1 e 5 dias.
* Dá suporte a tamanhos de disco de até 32 TB. O redimensionamento de discos não é recomendado pelo backup do Azure.
* Dá suporte a discos SSD Standard juntamente com discos HDD Standard e SSD Premium discos.
* Capacidade de usar uma conta de armazenamento original de VMs não gerenciadas (por disco) ao restaurar. Essa capacidade existe mesmo quando a VM tem discos que são distribuídos entre contas de armazenamento. Ele acelera as operações de restauração para uma ampla variedade de configurações de VM.
* Para o backup de VMs que estão usando o armazenamento Premium, com a restauração instantânea, é recomendável alocar *50%* de espaço livre do espaço de armazenamento total alocado, que é necessário **apenas** para o primeiro backup. O espaço livre de 50% não é um requisito para backups após a conclusão do primeiro backup.

## <a name="whats-new-in-this-feature"></a>O que há de novo neste recurso

Atualmente, o trabalho de backup consiste em duas fases:

1. Tirando um instantâneo da VM.
2. Transferindo um instantâneo de VM para o cofre dos serviços de recuperação do Azure.

Um ponto de recuperação é considerado criado somente depois que as fases 1 e 2 são concluídas. Como parte dessa atualização, um ponto de recuperação é criado assim que o instantâneo é concluído e esse ponto de recuperação do tipo de instantâneo pode ser usado para executar uma restauração usando o mesmo fluxo de restauração. Você pode identificar esse ponto de recuperação no portal do Azure usando "instantâneo" como o tipo de ponto de recuperação e, depois que o instantâneo é transferido para o cofre, o tipo de ponto de recuperação muda para "instantâneo e cofre".

![Trabalho de backup no modelo de implantação do Gerenciador de recursos da pilha de backup da VM – armazenamento e cofre](./media/backup-azure-vms/instant-rp-flow.png)

Por padrão, os instantâneos são retidos por dois dias. Esse recurso permite a operação de restauração desses instantâneos, reduzindo os tempos de restauração. Ele reduz o tempo necessário para transformar e copiar dados de volta do cofre.

## <a name="feature-considerations"></a>Considerações sobre recursos

* Os instantâneos são armazenados junto com os discos para impulsionar a criação do ponto de recuperação e para acelerar as operações de restauração. Como resultado, você verá os custos de armazenamento que correspondem aos instantâneos feitos durante esse período.
* Os instantâneos incrementais são armazenados como BLOBs de páginas. Todos os usuários que usam discos não gerenciados são cobrados pelos instantâneos armazenados em sua conta de armazenamento local. Como as coleções de pontos de restauração usadas por backups de VM gerenciados usam instantâneos de blob no nível de armazenamento subjacente, para discos gerenciados, você verá os custos correspondentes ao preço do instantâneo de BLOB e eles são incrementais.
* Para contas de armazenamento Premium, os instantâneos feitos para pontos de recuperação instantâneas contam para o limite de 10 TB de espaço alocado.
* Você Obtém a capacidade de configurar a retenção de instantâneo com base nas necessidades de restauração. Dependendo do requisito, você pode definir a retenção de instantâneo para um mínimo de um dia na folha política de backup, conforme explicado abaixo. Isso o ajudará a economizar o custo para retenção de instantâneo se você não executar restaurações com frequência.
* É uma atualização direcional, uma vez atualizada para restauração instantânea, você não pode voltar.

>[!NOTE]
>Com essa atualização de restauração instantânea, a duração da retenção de instantâneo de todos os clientes (**novos e existentes ambos incluídos**) será definida com um valor padrão de dois dias. No entanto, você pode definir a duração de acordo com seu requisito para qualquer valor entre 1 e 5 dias.

## <a name="cost-impact"></a>Impacto de custo

Os instantâneos incrementais são armazenados na conta de armazenamento da VM, que é usada para recuperação instantânea. Instantâneo incremental significa que o espaço ocupado por um instantâneo é igual ao espaço ocupado por páginas que são gravadas após a criação do instantâneo. A cobrança ainda é para o espaço usado por GB ocupado pelo instantâneo e o preço por GB é o mesmo mencionado na página de [preços](https://azure.microsoft.com/pricing/details/managed-disks/). Para VMs que usam discos não gerenciados, os instantâneos podem ser vistos no menu do arquivo VHD de cada disco. Para discos gerenciados, os instantâneos são armazenados em um recurso de coleção de pontos de restauração em um grupo de recursos designado, e os instantâneos em si não são diretamente visíveis.

>[!NOTE]
> A retenção de instantâneos é fixada em 5 dias para políticas semanais.

## <a name="configure-snapshot-retention"></a>Configurar retenção de instantâneo

### <a name="using-azure-portal"></a>Utilizar o portal do Azure

No portal do Azure, você pode ver um campo adicionado na folha **política de backup de VM** na seção **restauração instantânea** . Você pode alterar a duração da retenção de instantâneo da folha **política de backup da VM** para todas as VMs associadas à política de backup específica.

![Capacidade de restauração instantânea](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>Utilizar o PowerShell

>[!NOTE]
> Em AZ PowerShell Version 1.6.0 em diante, você pode atualizar o período de retenção do instantâneo de restauração instantânea na política usando o PowerShell

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

A retenção de instantâneo padrão para cada política é definida como dois dias. O usuário pode alterar o valor para um mínimo de 1 e um máximo de cinco dias. Para as políticas semanais, a retenção de instantâneos é fixada em cinco dias.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Quais são as implicações de custo da restauração instantânea?

Os instantâneos são armazenados junto com os discos para acelerar as operações de criação e restauração do ponto de recuperação. Como resultado, você verá os custos de armazenamento que correspondem à retenção de instantâneo selecionada como parte da política de backup de VM.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Em contas de armazenamento Premium, os instantâneos feitos para o ponto de recuperação instantânea ocupam o limite de instantâneo de 10 TB?

Sim, para contas de armazenamento Premium, os instantâneos feitos para o ponto de recuperação instantânea ocupam 10 TB de espaço de instantâneo alocado.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Como funciona a retenção de instantâneo durante o período de cinco dias?

Todos os dias em que um novo instantâneo é tirado, há cinco instantâneos incrementais individuais. O tamanho do instantâneo depende da variação de dados, que estão na maioria dos casos em cerca de 2%-7%.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Um instantâneo de restauração instantânea é um instantâneo incremental ou um instantâneo completo?

Instantâneos tirados como parte do recurso de restauração instantânea são instantâneos incrementais.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Como calcular o aumento de custo aproximado devido ao recurso de restauração instantânea?

Depende da rotatividade da VM. Em um estado estável, você pode assumir que o aumento no custo é = período de retenção do instantâneo rotatividade diária por custo de armazenamento de VM por GB.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Se o tipo de recuperação de um ponto de restauração for "instantâneo e cofre" e eu executar uma operação de restauração, qual tipo de recuperação será usado?

Se o tipo de recuperação for "instantâneo e cofre", a restauração será feita automaticamente a partir do instantâneo local, o que será muito mais rápido em comparação com a restauração feita no cofre.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>O que acontece se eu selecionar o período de retenção do ponto de restauração (camada 2) menor que o período de retenção do instantâneo (nível 1)?

O novo modelo não permite a exclusão do ponto de restauração (tier2), a menos que o instantâneo (nível 1) seja excluído. É recomendável agendar o período de retenção do ponto de restauração (tier2) maior que o período de retenção do instantâneo.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>Por que o meu instantâneo é existente mesmo após o período de retenção definido na política de backup?

Se o ponto de recuperação tiver um instantâneo e esse for o RP mais recente disponível, ele será retido até o momento em que houver um novo backup bem-sucedido. Isso é de acordo com a política de "coleta de lixo" (GC) projetada hoje que exige pelo menos um RP mais recente para estar sempre presente no caso de falha em todos os backups devido a um problema na VM. Em cenários normais, o RPs é limpo no máximo 24 horas após a expiração.

>[!NOTE]
> O backup do Azure agora oferece suporte a backup e restauração de disco seletivo usando a solução de backup de máquina virtual do Azure.
>
>Hoje, o backup do Azure dá suporte ao backup de todos os discos (sistema operacional e dados) em uma VM em conjunto usando a solução de backup de máquina virtual. Com a funcionalidade excluir disco, você obtém uma opção para fazer backup de um ou alguns dos vários discos de dados em uma VM. Isso fornece uma solução eficiente e econômica para suas necessidades de backup e restauração. Cada ponto de recuperação contém dados dos discos incluídos na operação de backup, o que permite que você tenha um subconjunto de discos restaurados do ponto de recuperação fornecido durante a operação de restauração. Isso se aplica à restauração tanto do instantâneo quanto do cofre.
>
> Essa solução é particularmente útil nos seguintes cenários:
>  
>1. Você tem dados críticos para fazer backup em apenas um disco e não deseja fazer backup do restante dos discos anexados a uma VM. Isso minimiza os custos de armazenamento de backup.  
>2. Você tem outras soluções de backup para parte dos dados da VM. Por exemplo, você faz o backup de seus bancos de dados ou de sua capacidade com uma solução de backup de carga de trabalho diferente e deseja usar o backup de nível de VM do Azure para o restante dos seus discos e dados para criar um sistema eficiente e robusto utilizando os melhores recursos disponíveis.
>
>Para se inscrever na versão prévia, escreva para nós em AskAzureBackupTeam@microsoft.com
