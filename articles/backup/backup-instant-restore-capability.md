---
title: Capacidade de restauro instantâneo azure
description: Capacidade de restauro instantâneo azure e FAQs para pilha de backup VM, modelo de implementação do Gestor de Recursos
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: fc29d1ac4c2e4c22ce6e6f8356927e768dc274e3
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82597659"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Obtenha uma cópia de segurança melhorada e restaure o desempenho com a capacidade de restauro instantâneo de backup azure

> [!NOTE]
> Com base no feedback dos utilizadores, estamos a renomear a pilha de **backup VM V2** para **o Instant Restore** para reduzir a confusão com a funcionalidade Azure Stack.
> Todos os utilizadores de backup do Azure foram agora atualizados para **o Instant Restore**.

O novo modelo de Restauro Instantâneo fornece as seguintes melhorias de funcionalidades:

* Capacidade de usar instantâneos tirados como parte de um trabalho de backup que está disponível para recuperação sem esperar pela transferência de dados para o cofre para terminar. Reduz o tempo de espera para as fotos copiarem para o cofre antes de desencadear o restauro.
* Reduz os tempos de backup e restauro mantendo instantâneos localmente, durante dois dias por defeito. Este valor de retenção por padrão instantâneo é configurável a qualquer valor entre 1 a 5 dias.
* Suporta tamanhos de disco até 32 TB. A redimensionamento de discos não é recomendada pela Azure Backup.
* Suporta discos SSD standard juntamente com discos Standard HDD e discos Premium SSD.
* Capacidade de utilizar uma conta de armazenamento original de VMs não gerida (por disco), ao restaurar. Esta capacidade existe mesmo quando o VM tem discos que são distribuídos através de contas de armazenamento. Acelera as operações de restauro para uma grande variedade de configurações VM.
* Para backup de VMs que estão usando discos premium não geridos em contas de armazenamento, com Instant Restore, recomendamos a locação *50%* de espaço livre do espaço total de armazenamento atribuído, que é necessário **apenas** para a primeira cópia de segurança. O espaço 50% livre não é um requisito para backups após a primeira cópia de segurança estar completa.

## <a name="whats-new-in-this-feature"></a>O que há de novo nesta funcionalidade

Atualmente, o trabalho de backup consiste em duas fases:

1. Tirando uma foto vm.
2. Transferindo uma foto vm para o cofre dos Serviços de Recuperação Azure.

Um ponto de recuperação é considerado criado apenas após as fases 1 e 2 estarem concluídas. Como parte desta atualização, um ponto de recuperação é criado assim que o instantâneo é terminado e este ponto de recuperação do tipo instantâneo pode ser usado para realizar uma restauração usando o mesmo fluxo de restauro. Pode identificar este ponto de recuperação no portal Azure utilizando o "instantâneo" como o tipo de ponto de recuperação, e após a transferência do instantâneo para o cofre, o tipo de ponto de recuperação muda para "instantâneo e cofre".

![Trabalho de backup em VM backup stack Resource Manager modelo de implementação -- armazenamento e cofre](./media/backup-azure-vms/instant-rp-flow.png)

Por padrão, as imagens são retidas por dois dias. Esta função permite restaurar o funcionamento a partir destes instantâneos, reduzindo os tempos de restauro. Reduz o tempo necessário para transformar e copiar dados do cofre.

## <a name="feature-considerations"></a>Considerações de recurso

* Os instantâneos são armazenados juntamente com os discos para impulsionar a criação do ponto de recuperação e acelerar as operações de restauro. Como resultado, você verá custos de armazenamento que correspondem a instantâneos tirados durante este período.
* Os instantâneos incrementais são armazenados como bolhas de página. Todos os utilizadores que utilizem discos não geridos são cobrados pelas imagens armazenadas na sua conta de armazenamento local. Uma vez que as coleções de pontos de restauro utilizadas pelas cópias de segurança Geridas vM utilizam instantâneos blob ao nível de armazenamento subjacente, para discos geridos você verá custos correspondentes ao preço de instantâneo blob e são incrementais.
* Para contas de armazenamento premium, os instantâneos para pontos de recuperação instantâneacontam para o limite de 10 TB do espaço atribuído.
* Obtém-se a capacidade de configurar a retenção de instantâneos com base nas necessidades de restauro. Dependendo da exigência, pode definir a retenção de instantâneos para um mínimo de um dia na lâmina de política de backup, conforme explicado abaixo. Isto irá ajudá-lo a economizar custos para a retenção de instantâneos se não realizar restauros com frequência.
* É uma atualização direcional, uma vez atualizada para restauro instantâneo, não pode voltar atrás.

>[!NOTE]
>Com esta atualização instantânea de restauro, a duração de retenção instantânea de todos os clientes **(novos e existentes ambos incluídos**) será definida para um valor padrão de dois dias. No entanto, pode definir a duração de acordo com o seu requisito para qualquer valor entre 1 a 5 dias.

## <a name="cost-impact"></a>Impacto do custo

Os instantâneos incrementais são armazenados na conta de armazenamento do VM, que é usada para recuperação instantânea. Instantâneo incremental significa que o espaço ocupado por um instantâneo é igual ao espaço ocupado por páginas que são escritas após a criação do instantâneo. A faturação ainda é para o espaço usado por GB ocupado pelo instantâneo, e o preço por GB é o mesmo que mencionado na [página de preços](https://azure.microsoft.com/pricing/details/managed-disks/). Para VMs que utilizam discos não geridos, as imagens podem ser vistas no menu para o ficheiro VHD de cada disco. Para discos geridos, os instantâneos são armazenados num recurso de recolha de pontos de restauro num grupo de recursos designado, e os próprios instantâneos não são diretamente visíveis.

>[!NOTE]
> A retenção de instantâneos é fixada em 5 dias para políticas semanais.

## <a name="configure-snapshot-retention"></a>Configurar a retenção de instantâneos

### <a name="using-azure-portal"></a>Com o Portal do Azure

No portal Azure, pode ver um campo adicionado na lâmina de política de **backup VM** sob a secção **Restauro Instantâneo.** Pode alterar a duração da retenção de instantâneos a partir da lâmina de política de **backup VM** para todos os VMs associados à política de backup específica.

![Capacidade de restauro instantâneo](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>Com o PowerShell

>[!NOTE]
> A partir da versão 1.6.0 da Az PowerShell, pode atualizar o período instantâneo de retenção instantânea de instantâneos na política utilizando o PowerShell

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

A retenção de instantâneopadrão para cada política está definida para dois dias. O utilizador pode alterar o valor para um mínimo de 1 e no máximo de cinco dias. Para as políticas semanais, a retenção instantânea é fixada em cinco dias.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Quais são as implicações de custos da restauração instantânea?

Os instantâneos são armazenados juntamente com os discos para acelerar a criação do ponto de recuperação e restaurar as operações. Como resultado, você verá custos de armazenamento que correspondem à retenção de instantâneos selecionado como parte da política de backup VM.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Nas contas de Armazenamento Premium, as imagens tiradas para o ponto de recuperação instantânea ocupam o limite de instantâneo de 10 TB?

Sim, para contas de armazenamento premium as fotos tiradas para o ponto de recuperação instantânea ocupam 10 TB de espaço instantâneo atribuído.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Como funciona a retenção instantânea durante o período de cinco dias?

Todos os dias é tirada uma nova fotografia, depois há cinco instantâneos incrementais individuais. O tamanho do instantâneo depende do respingo de dados, que na maioria dos casos rondam os 2%-7%.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Um instantâneo de restauração instantâneo é um instantâneo incremental ou instantâneo completo?

As fotos tiradas como parte da capacidade de restauro instantâneo são instantâneos incrementais.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Como posso calcular o aumento de custos aproximado devido à função de restauro instantâneo?

Depende da agitação do VM. Em um estado estável, você pode assumir que o aumento do custo é = Período de retenção instantânea diariamente churn por custo de armazenamento VM por GB.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Se o tipo de recuperação para um ponto de restauro for "Snapshot and vault" e eu executar uma operação de restauro, que tipo de recuperação será usado?

Se o tipo de recuperação for "instantâneo e cofre", o restauro será feito automaticamente a partir do instantâneo local, que será muito mais rápido em comparação com o restauro feito a partir do cofre.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>O que acontece se eu selecionar o período de retenção do ponto de restauro (Nível 2) menos do que o período de retenção instantâneo (Tier1) ?

O novo modelo não permite eliminar o ponto de restauro (Tier2) a menos que o instantâneo (Tier1) seja eliminado. Recomendamos o período de retenção do ponto de restauração de agendamento (Tier2) superior ao período de retenção de instantâneos.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>Porque é que a minha foto existe mesmo depois do período de retenção definido na política de backup?

Se o ponto de recuperação tiver instantâneo e este for o mais recente RP disponível, é mantido até ao momento em que houver um próximo backup bem sucedido. Isto de acordo com a política de "recolha de lixo" (GC) projetada hoje que obriga pelo menos um RP mais recente a estar sempre presente no caso de todos os backups ainda mais falharem devido a um problema no VM. Em cenários normais, os RPs são limpos no máximo 24 horas após o seu termo.

### <a name="i-dont-need-instant-restore-functionality-can-it-be-disabled"></a>Não preciso da funcionalidade Instant Restore. Pode ser desativado?

A função de restauro instantâneo está ativada para todos e não pode ser desativada. Pode reduzir a retenção de instantâneos para um mínimo de um dia.

>[!NOTE]
> **O Azure Backup suporta agora a cópia de segurança seletiva do disco e restaura utilizando a solução de backup da Máquina Virtual Azure.**
>
>Hoje em dia, o Azure Backup suporta o backup de todos os discos (Sistema Operativo e dados) num VM juntos utilizando a solução de backup da Máquina Virtual. Com a funcionalidade de exclusão de disco, obtém-se a opção de fazer backup de um ou alguns dos muitos discos de dados de um VM. Isto fornece uma solução eficiente e rentável para as suas necessidades de backup e restauro. Cada ponto de recuperação contém dados dos discos incluídos na operação de backup, o que permite ainda ter um subconjunto de discos restaurado sabotado a partir do ponto de recuperação dado durante a operação de restauro. Isto aplica-se para restaurar tanto a partir do instantâneo como do cofre.
>
>**Para se inscrever para a pré-visualização, escreva-nos emAskAzureBackupTeam@microsoft.com**
