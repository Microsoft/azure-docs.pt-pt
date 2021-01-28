---
title: Matriz de suporte da Cópia de Segurança de Discos do Azure
description: Fornece um resumo das definições de suporte e limitações Azure Disk Backup.
ms.topic: conceptual
ms.date: 01/07/2021
ms.custom: references_regions
ms.openlocfilehash: 08d2ac8451e88dd8d40ed5faee0368ff7739cd16
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954703"
---
# <a name="azure-disk-backup-support-matrix-in-preview"></a>Matriz de suporte de backup do disco Azure (na pré-visualização)

>[!IMPORTANT]
>A Azure Disk Backup está em pré-visualização sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
>[Preencha este formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) para se inscrever para a pré-visualização.

Pode utilizar [a cópia de segurança do Azure](./backup-overview.md) para proteger discos Azure. Este artigo resume a disponibilidade da região, cenários apoiados e limitações.

## <a name="supported-regions"></a>Regiões suportadas

A azure Disk Backup está disponível em pré-visualização nas seguintes regiões: West Central US, East US2, Korea Central, Korea South, Japan West, UAE North. 

Serão anunciadas mais regiões quando estiverem disponíveis.

## <a name="limitations"></a>Limitações

- A Azure Disk Backup é suportado para Discos Geridos Azure, incluindo discos partilhados (SSDs premium partilhados). Discos não geridos não são suportados. Atualmente esta solução não suporta ultra-discos, incluindo ultra-discos partilhados, devido à falta de capacidade de instantâneo.

- A Azure Disk Backup suporta a cópia de segurança do disco do Acelerador de Escrita. No entanto, durante a restauração, o disco seria restaurado como um disco normal. Escrever Cache acelerada pode ser ativada no disco depois de montá-lo num VM.

- O Azure Backup fornece backup de nível operacional (instantâneo) de discos geridos Azure com suporte para múltiplas cópias de segurança por dia. Os reforços não são copiados para o cofre de reserva.

- Atualmente, a opção Original-Location Recovery (OLR) para restaurar, substituindo os discos de origem existentes de onde as cópias de segurança foram tomadas não é suportada. Pode restaurar do ponto de recuperação para criar um novo disco no mesmo grupo de recursos que o do disco de origem de onde as cópias de segurança foram tomadas ou em qualquer outro grupo de recursos. Isto é conhecido como recuperação Alternate-Location (ALR).

- A Azure Backup for Managed Disks utiliza instantâneos incrementais, que estão limitados a 200 instantâneos por disco. Para permitir que você assuma o backup a pedido para além de backups programados, a política de backup limita o total de backups para 180. Saiba mais sobre [instantâneos incrementais](../virtual-machines/disks-incremental-snapshots.md#restrictions) para discos geridos.

- Os limites de [subscrição e serviço](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machine-disk-limits) azure aplicam-se ao número total de snapshots de disco por região por subscrição.

- Não são suportadas imagens pontuais de vários discos ligados a uma máquina virtual.

- O cofre de reserva e os discos a serem apoiados podem estar nas mesmas subscrições ou diferentes. No entanto, tanto o cofre de reserva como o disco a apoiar devem estar na mesma região.

- Os discos a serem apoiados e o grupo de recursos instantâneos onde as imagens são armazenadas localmente devem estar na mesma subscrição.

- Restaurar um disco de cópia de segurança para o mesmo ou uma subscrição diferente é suportado. No entanto, o disco restaurado será criado na mesma região que o instantâneo.

- Os discos encriptados ADE não podem ser protegidos.

- Os discos encriptados com chaves geridas pela plataforma ou chaves geridas pelo cliente são suportados. No entanto, a restauração falhará nos pontos de restauro de um disco que é encriptado utilizando teclas geridas pelo cliente (CMK) se a tecla KeyVault do Conjunto de Encriptação do Disco for desativada ou eliminada.

- Atualmente, a política de Backup não pode ser modificada, e o grupo de Recursos Instantâneos que é atribuído a uma instância de backup quando configurar a cópia de segurança de um disco não pode ser alterado.

- Atualmente, a experiência do portal Azure para configurar a cópia de segurança dos discos está limitada a um máximo de 20 discos da mesma subscrição.

- Atualmente (durante a pré-visualização), a utilização do PowerShell e do Azure CLI para configurar a cópia de segurança e a restauração dos discos não é suportada.

- Ao configurar a cópia de segurança, o disco selecionado para ser apoiado e o grupo de recursos instantâneos onde as imagens devem ser armazenadas deve fazer parte da mesma subscrição. Não é possível criar uma imagem incremental para um disco específico fora da subscrição do disco. Saiba mais sobre [instantâneos incrementais](../virtual-machines/windows/disks-incremental-snapshots-portal.md#restrictions) para o disco gerido. Para obter mais informações sobre como escolher um grupo de recursos instantâneos, consulte  [a cópia de segurança Configure](backup-managed-disks.md#configure-backup).

- Para operações de backup e restauro bem sucedidas, as atribuições de funções são necessárias pela identidade gerida do cofre de reserva. Utilize apenas as definições de função fornecidas na documentação. O uso de outros papéis como proprietário, colaborador, e assim por diante, não é apoiado. Pode enfrentar problemas de permissão, se começar a configurar operações de backup ou restaurar as operações logo após a atribuição de funções. Isto porque as tarefas de função demoram alguns minutos a fazer efeito.

- Os discos geridos permitem alterar o nível de desempenho na implementação ou depois sem alterar o tamanho do disco. A solução Azure Disk Backup suporta as alterações do nível de desempenho no disco de origem que está a ser apoiado. Durante a restauração, o nível de desempenho do disco restaurado será o mesmo que o do disco de origem no momento da cópia de segurança. Siga a documentação [aqui](../virtual-machines/disks-performance-tiers-portal.md) para alterar o nível de desempenho do seu disco após a operação de restauro.

- O suporte [de Links Privados](../virtual-machines/disks-enable-private-links-for-import-export-portal.md) para discos geridos permite-lhe restringir a exportação e importação de discos geridos para que ocorra apenas dentro da sua rede virtual Azure. A Azure Disk Backup suporta a cópia de segurança dos discos que têm pontos finais privados ativados. Isto não inclui os dados de backup ou instantâneos para serem acessíveis através do ponto final privado.

- Durante a pré-visualização, não é possível desativar a cópia de segurança, pelo que a opção para parar a **cópia de segurança e reter dados de backup** não é suportada. Pode eliminar uma instância de backup, que não só irá parar a cópia de segurança como também eliminar todos os dados de backup.

## <a name="next-steps"></a>Passos seguintes

- [Back up Azure Managed Disks](backup-managed-disks.md)
