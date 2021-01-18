---
title: Visão geral da cópia de segurança do disco Azure
description: Saiba mais sobre a solução de backup do Disco Azure.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: fea0dd9d01bdc7c31d724cedd89d1fe6891c650a
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98558437"
---
# <a name="overview-of-azure-disk-backup-in-preview"></a>Visão geral da cópia de segurança do disco Azure (na pré-visualização)

>[!IMPORTANT]
>A Azure Disk Backup está em pré-visualização sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para disponibilidade da região, consulte a [matriz de suporte.](disk-backup-support-matrix.md)
>
>[Preencha este formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) para se inscrever para a pré-visualização.

A Azure Disk Backup é uma solução de backup baseada na nuvem nativa que protege os seus dados em discos geridos. É uma solução simples, segura e económica que lhe permite configurar a proteção para discos geridos em alguns passos. Garante que pode recuperar os seus dados num cenário de desastre.

O Azure Disk Backup oferece uma solução chave na mão que fornece uma gestão instantânea do ciclo de vida para discos geridos, automatizando a criação periódica de instantâneos e mantendo-o durante a duração configurada usando a política de backup. Você pode gerir os instantâneos do disco com custos de infraestrutura zero e sem a necessidade de scripts personalizados ou qualquer sobrecarga de gestão. Esta é uma solução de backup consistente por falhas que requer uma cópia de segurança pontual de um disco gerido usando [instantâneos incrementais](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots) com suporte para múltiplas cópias de segurança por dia. É também uma solução sem agente e não tem impacto no desempenho da aplicação de produção. Suporta a cópia de segurança e a restauração dos discos de SEGURANÇA e de dados (incluindo discos partilhados), quer estejam ou não ligados a uma máquina virtual Azure em funcionamento.

Se necessitar de uma cópia de segurança consistente da máquina virtual, incluindo os discos de dados, ou uma opção para restaurar uma máquina virtual inteira a partir de cópias de segurança, restaurar um ficheiro ou pasta ou restaurar para uma região secundária, use a solução [de backup Azure VM.](backup-azure-vms-introduction.md) O Azure Backup oferece suporte lado a lado para cópia de segurança de discos geridos utilizando backup de disco, além de soluções [de backup Azure VM.](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction) Isto é útil quando você precisa de uma aplicação consistente cópias de segurança de máquinas virtuais e também cópias de segurança mais frequentes de discos DE ou um disco de dados específico que são consistentes de colisão, e não impactam o desempenho da aplicação de produção.

O Azure Disk Backup está integrado no Backup Center, que proporciona uma **única experiência de gestão unificada** em Azure para as empresas governarem, monitorizarem, operarem e analisarem backups em escala.

## <a name="key-benefits-of-disk-backup"></a>Principais benefícios da cópia de segurança do disco

A azure Disk backup é uma solução consistente sem agente e que usa [instantâneos incrementais](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots) e oferece as seguintes vantagens:

- Cópias de segurança mais frequentes e rápidas sem interromper a máquina virtual.
- Não afeta o desempenho da aplicação de produção.
- Sem preocupações de segurança, uma vez que não requer scripts personalizados ou agentes instalados.
- Uma solução rentável para fazer backup de discos específicos quando comparado com o backup de toda a máquina virtual.

A solução de backup do disco Azure é útil nos seguintes cenários:

- Necessidade de backups frequentes por dia sem que a aplicação seja quiescente
- Aplicações em execução num cenário de cluster: tanto o Windows Server Failover Cluster como os clusters Linux estão a escrever para discos partilhados
- Necessidade específica de cópia de segurança sem agente devido a preocupações de segurança ou desempenho na aplicação
- A cópia de segurança consistente da aplicação de VM não é viável uma vez que as aplicações de linha de negócios não suportam o Volume Shadow Copy Service (VSS).

Considere a cópia de segurança do disco Azure em cenários onde:

- uma aplicação crítica de missão está a ser executada numa máquina Virtual Azure que exige múltiplas cópias de segurança por dia para cumprir o objetivo do ponto de recuperação, mas sem afetar o ambiente de produção ou o desempenho da aplicação
- sua organização ou regulação do setor restringe a instalação de agentes por causa de preocupações de segurança
- executar scripts personalizados pré ou post e invocar congelamento e descongelamento em máquinas virtuais Linux para obter backup consistente de aplicação coloca sobrecarga indevida na disponibilidade de carga de trabalho de produção
- as aplicações contentorizadas em execução no Serviço Azure Kubernetes (cluster AKS) estão a usar discos geridos como armazenamento persistente. Hoje em dia você tem que fazer o back up do disco gerido através de scripts de automação que são difíceis de gerir.
- um disco gerido está a deter dados de negócios críticos, usados como partilha de ficheiros, ou contém ficheiros de backup de bases de dados, e pretende otimizar o custo de backup ao não investir na cópia de segurança do Azure VM
- Tem muitas máquinas virtuais de disco único Linux e Windows (isto é, uma máquina virtual com apenas um disco DE e sem discos de dados ligados) que acolhem webserver ou máquinas sem estado ou serve como um ambiente de preparação com configurações de configuração de aplicações e precisa de uma solução de backup eficiente em termos de custos para proteger o disco DE. Por exemplo, para desencadear uma cópia de segurança rápida a pedido antes de atualizar ou remendar a máquina virtual
- uma máquina virtual está a executar uma configuração de SISTEMA que não é suportada pela solução de backup Azure VM (por exemplo, Servidor Windows 2008 32 bits)

## <a name="how-the-backup-and-restore-process-works"></a>Como funciona o processo de backup e restauro

- O primeiro passo para configurar o backup para as ações de ficheiros Azure é criar um [cofre de reserva.](backup-vault-overview.md) O cofre dá-lhe uma visão consolidada das cópias de segurança configuradas em diferentes cargas de trabalho.

- Em seguida, crie uma política de backup que lhe permita configurar a frequência de backup e a duração da retenção.

- Para configurar a cópia de segurança, vá ao cofre de backup, atribua uma política de backup, selecione o disco gerido que precisa de ser apoiado e forneça um grupo de recursos onde as imagens devem ser armazenadas e geridas. O Azure Backup ativa automaticamente trabalhos de backup programados que criam uma imagem incremental do disco de acordo com a frequência de backup. Os instantâneos mais antigos são eliminados de acordo com a duração de retenção especificada pela política de backup.

- O Azure Backup utiliza [instantâneos incrementais](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal#restrictions) do disco gerido. Os instantâneos incrementais são uma cópia de segurança pontual e pontual dos discos geridos que são faturados para as alterações delta no disco desde o último instantâneo. São sempre armazenados no armazenamento mais rentável, armazenamento HDD padrão, independentemente do tipo de armazenamento dos discos-mãe. O primeiro instantâneo do disco ocupará o tamanho usado do disco, e os instantâneos incrementais consecutivos armazenam alterações delta no disco desde o último instantâneo.

- Assim que configurar a cópia de segurança de um disco gerido, será criada uma instância de backup dentro do cofre de reserva. Utilizando a instância de backup, pode encontrar a saúde das operações de backup, desencadear backups a pedido e realizar operações de restauro. Também pode ver a saúde de backups em vários cofres e instâncias de backup usando backup Center, que fornece uma única vidraça de vidro.

- Para restaurar, basta selecionar o ponto de recuperação a partir do qual pretende restaurar o disco. Forneça ao grupo de recursos onde o disco restaurado deve ser criado a partir do instantâneo. O Azure Backup proporciona uma experiência de restauro instantâneo uma vez que os instantâneos são armazenados localmente na sua subscrição.

- Backup Vault usa Identidade Gerida para aceder a outros recursos Azure. Para configurar a cópia de segurança de um disco gerido e restaurar a partir de cópias de segurança passadas, a identidade gerida do Backup Vault requer um conjunto de permissões no disco de origem, o grupo de recursos instantâneos onde as imagens são criadas e geridas, e o grupo de recursos-alvo onde pretende restaurar a cópia de segurança. Pode conceder permissões à identidade gerida utilizando o controlo de acesso baseado em funções Azure (Azure RBAC). A identidade gerida é um principal de serviço de um tipo especial que só pode ser usado com recursos Azure. Saiba mais sobre [identidades geridas.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

- Atualmente, o Azure Disk Backup suporta a cópia de segurança operacional dos discos geridos e não copia as cópias de segurança para o armazenamento do Cofre de Reserva. Consulte a matriz de [apoio](disk-backup-support-matrix.md)para obter uma lista detalhada de cenários apoiados e não apoiados e disponibilidade da região.

## <a name="pricing"></a>Preços

O Azure Backup oferece uma solução de gestão de ciclo de vida instantânea para proteger os Discos Azure. As imagens de disco criadas pela Azure Backup são armazenadas no grupo de recursos dentro da sua subscrição Azure e incorrem em custos **de Armazenamento Instantâneo.** Pode visitar [o Preço do Disco Gerido](https://azure.microsoft.com/pricing/details/managed-disks/) para obter mais detalhes sobre o preço do instantâneo. Como as imagens não são copiadas para o Cofre de Reserva, o Azure Backup não cobra uma taxa **de instância protegida** e o custo de armazenamento de **backup** não se aplica. Além disso, os instantâneos incrementais ocupam alterações delta desde o último instantâneo e são sempre armazenados em armazenamento padrão, independentemente do tipo de armazenamento dos discos geridos pelos pais e são cobrados de acordo com o preço do armazenamento padrão. Isto faz do Azure Disk Backup uma solução rentável.

## <a name="next-steps"></a>Passos seguintes

- [Matriz de suporte de backup do disco Azure](disk-backup-support-matrix.md)
