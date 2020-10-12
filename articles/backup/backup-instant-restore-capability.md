---
title: Capacidade de restauro instantâneo Azure
description: Capacidade de restauro instantâneo Azure e FAQs para pilha de backup VM, modelo de implementação do Gestor de Recursos
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 69348a9902224f9f73f80d5b1900143c885d20ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89000384"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Obtenha uma cópia de segurança melhorada e restaure o desempenho com a capacidade de restauro instantâneo de backup Azure

> [!NOTE]
> Com base no feedback dos utilizadores, rebatizámos **a pilha de backup V2 v2** para Instant **Restore** para reduzir a confusão com a funcionalidade Azure Stack.
> Todos os utilizadores da Azure Backup foram agora atualizados para **Instant Restore**.

O novo modelo para Instant Restore fornece as seguintes melhorias de funcionalidades:

* Capacidade de usar instantâneos tomados como parte de um trabalho de reserva que está disponível para recuperação sem esperar que os dados transfiram para o cofre para terminar. Reduz o tempo de espera para as imagens copiarem para o cofre antes de desencadear o restauro.
* Reduz os tempos de backup e restauro, retendo instantâneos localmente, durante dois dias por padrão. Este valor de retenção de instantâneo padrão é configurável a qualquer valor entre 1 a 5 dias.
* Suporta tamanhos de disco até 32 TB. A redimensionamento dos discos não é recomendada pela Azure Backup.
* Suporta discos SSD standard juntamente com discos HDD standard e discos Premium SSD.
* Capacidade de utilizar uma conta de armazenamento original de VMs não gerido (por disco), ao restaurar. Esta capacidade existe mesmo quando o VM tem discos que são distribuídos por contas de armazenamento. Acelera as operações de restauro para uma grande variedade de configurações de VM.
* Para a cópia de segurança dos VM que estão a utilizar discos premium não geridos em contas de armazenamento, com Instant Restore, recomendamos a atribuição de *50%* de espaço livre do espaço total de armazenamento atribuído, que é necessário **apenas** para a primeira cópia de segurança. O espaço livre de 50% não é um requisito para cópias de segurança depois de o primeiro backup estar completo.

## <a name="whats-new-in-this-feature"></a>Quais as novidades nesta funcionalidade

Atualmente, a tarefa de cópia de segurança é composta por duas fases:

1. Capturar um instantâneo de VM.
2. Transferir um instantâneo de VM para o cofre dos Serviços de Recuperação do Azure.

Considera-se que um ponto de recuperação foi criado apenas após as fases 1 e 2 estarem concluídas. Como parte desta atualização, é criado um ponto de recuperação assim que o instantâneo é concluído. Este ponto de recuperação do tipo de instantâneo pode ser utilizado para executar um restauro com o mesmo fluxo de restauro. Pode identificar este ponto de recuperação no portal Azure utilizando o "snapshot" como tipo de ponto de recuperação, e depois de o instantâneo ser transferido para o cofre, o tipo de ponto de recuperação muda para "instantâneo e cofre".

![Trabalho de backup na pilha de backup VM modelo de implementação de gestor de recursos -- armazenamento e cofre](./media/backup-azure-vms/instant-rp-flow.png)

Por predefinição, as imagens são mantidas por dois dias. Esta funcionalidade permite restaurar o funcionamento destes instantâneos, reduzindo os tempos de restauro. Reduz o tempo necessário para transformar e copiar dados do cofre.

## <a name="feature-considerations"></a>Considerações de recurso

* As imagens são armazenadas juntamente com os discos para impulsionar a criação de pontos de recuperação e acelerar as operações de restauro. Como resultado, você verá os custos de armazenamento que correspondem a instantâneos tirados durante este período.
* As imagens incrementais são armazenadas como bolhas de página. Todos os utilizadores que usam discos não geridos são cobrados para os instantâneos armazenados na sua conta de armazenamento local. Uma vez que as coleções de pontos de restauração utilizadas pelas cópias de segurança Geridas VM utilizam instantâneos blob no nível de armazenamento subjacente, para discos geridos verás custos correspondentes ao preço do snapshot blob e são incrementais.
* Para as contas de armazenamento premium, os instantâneos recolhidos para pontos de recuperação instantâneas contam para o limite de 10-TB do espaço atribuído.
* Obtém-se a capacidade de configurar a retenção instantânea com base nas necessidades de restauro. Dependendo da exigência, pode definir a retenção de instantâneo para um mínimo de um dia no painel de política de backup, conforme explicado abaixo. Isto irá ajudá-lo a economizar custos para a retenção de instantâneos se não realizar restauros frequentemente.
* É um upgrade direcional. Uma vez atualizado para restaurar o Instant, não pode voltar.

>[!NOTE]
>Com esta atualização de restauro instantâneo, a duração de retenção instantânea de todos os clientes (**novos e existentes ambos incluídos**) será definida como um valor padrão de dois dias. No entanto, pode definir a duração de acordo com o seu requisito para qualquer valor entre 1 a 5 dias.

## <a name="cost-impact"></a>Impacto de custos

Os instantâneos incrementais são armazenados na conta de armazenamento do VM, que é usada para recuperação instantânea. O instantâneo incremental significa que o espaço ocupado por um instantâneo é igual ao espaço ocupado por páginas que são escritas após a criação do instantâneo. A faturação ainda é para o espaço usado por GB ocupado pelo instantâneo, e o preço por GB é o mesmo que mencionado na [página de preços](https://azure.microsoft.com/pricing/details/managed-disks/). Para os VMs que utilizam discos não geridos, as imagens podem ser vistas no menu para o ficheiro VHD de cada disco. Para discos geridos, os instantâneos são armazenados num recurso de recolha de pontos de restauração num grupo de recursos designado, e os instantâneos em si não são diretamente visíveis.

>[!NOTE]
> A retenção instantânea é fixada a 5 dias para as políticas semanais.

## <a name="configure-snapshot-retention"></a>Configurar a retenção de instantâneos

### <a name="using-azure-portal"></a>Com o Portal do Azure

No portal Azure, pode ver um campo adicionado no painel **de segurança VM** sob a secção **De Restauro Instantâneo.** Pode alterar a duração da retenção instantânea a partir do painel de **segurança VM** para todos os VMs associados à política de backup específica.

![Capacidade de Restauro Instantâneo](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>Com o PowerShell

>[!NOTE]
> A partir da versão 1.6.0 da Az PowerShell, pode atualizar o período de retenção instantânea de retenção instantânea na política utilizando o PowerShell

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

A retenção de instantâneo padrão para cada política está definida em dois dias. Pode alterar o valor para um mínimo de 1 e um máximo de cinco dias. Para as políticas semanais, a retenção de instantâneos é fixada a cinco dias.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Quais são as implicações de custo da restauração instantânea?

As imagens são armazenadas juntamente com os discos para acelerar a criação de pontos de recuperação e restaurar as operações. Como resultado, você verá os custos de armazenamento que correspondem à retenção instantânea selecionada como parte da política de backup VM.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Nas contas de Armazenamento Premium, os instantâneos retirados para o ponto de recuperação instantâneo ocupam o limite de instantâneo de 10-TB?

Sim, para contas de armazenamento premium, os instantâneos tomados para o ponto de recuperação instantâneo ocupam 10 TB de espaço instantâneo atribuído.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Como funciona a retenção de instantâneos durante o período de cinco dias?

Cada dia é tirada uma nova foto, então há cinco instantâneos incrementais individuais. O tamanho do instantâneo depende dos dados, que na maioria dos casos rondam os 2%-7%.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Um instantâneo de restauro instantâneo é um instantâneo incremental ou instantâneo completo?

As imagens tomadas como parte da capacidade de restauro instantâneo são instantâneos incrementais.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Como posso calcular o aumento de custos aproximado devido à funcionalidade de restauro instantâneo?

Depende da agitação do VM. Em um estado estável, você pode assumir que o aumento do custo é = Período de retenção instantânea diário churn por VM custo de armazenamento por GB.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Se o tipo de recuperação de um ponto de restauração for "Snapshot e vault" e eu realizar uma operação de restauro, que tipo de recuperação será usado?

Se o tipo de recuperação for "instantâneo e abóbada", a restauração será feita automaticamente a partir do instantâneo local, que será muito mais rápido em comparação com a restauração feita a partir do cofre.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>O que acontece se eu selecionar o período de retenção do ponto de restauração (Nível 2) inferior ao período de retenção do instantâneo (Tier1) ?

O novo modelo não permite apagar o ponto de restauro (Tier2) a menos que o instantâneo (Tier1) seja eliminado. Recomendamos o período de retenção do ponto de restauração do ponto de programação (Tier2) superior ao período de retenção do instantâneo.

### <a name="why-does-my-snapshot-still-exist-even-after-the-set-retention-period-in-backup-policy"></a>Porque é que a minha foto ainda existe, mesmo depois do período de retenção definido na política de backup?

Se o ponto de recuperação tiver uma foto e for o último ponto de recuperação disponível, é mantido até ao próximo backup bem sucedido. Isto de acordo com a política designada de "recolha de lixo" (GC). Determina que pelo menos um último ponto de recuperação esteja sempre presente, caso todos os backups subsequentes falhem devido a um problema no VM. Em cenários normais, os pontos de recuperação são limpos no máximo 24 horas após a sua expiração.

### <a name="i-dont-need-instant-restore-functionality-can-it-be-disabled"></a>Não preciso da funcionalidade De Restauro Instantâneo. Pode ser desativado?

A função de restauro instantâneo está ativada para todos e não pode ser desativada. Pode reduzir a retenção de instantâneo para um mínimo de um dia.
