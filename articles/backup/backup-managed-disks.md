---
title: Back up Azure Managed Disks
description: Saiba como fazer o back up Azure Managed Disks a partir do portal Azure.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: ca86550c4dec4b51c60d9ecdef124e38783a3764
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98738157"
---
# <a name="back-up-azure-managed-disks-in-preview"></a>Faça o back up Azure Managed Disks (na pré-visualização)

>[!IMPORTANT]
>A Azure Disk Backup está em pré-visualização sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para disponibilidade da região, consulte a [matriz de suporte.](disk-backup-support-matrix.md)
>
>[Preencha este formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) para se inscrever para a pré-visualização.

Este artigo explica como fazer o back up [Azure Managed Disk](../virtual-machines/managed-disks-overview.md) a partir do portal Azure.

Neste artigo, vai aprender a:

- Criar um cofre de reserva

- Criar uma política de cópias de segurança

- Configure uma cópia de segurança de um Disco Azure

- Executar um trabalho de reserva a pedido

Para obter informações sobre a disponibilidade da região de backup do Disco Azure, cenários e limitações apoiados, consulte a matriz de [suporte](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Criar um cofre de reserva

Um cofre de backup é uma entidade de armazenamento em Azure que contém dados de backup para várias cargas de trabalho mais recentes que o Azure Backup suporta, como a Azure Database para servidores PostgreSQL e Discos Azure. Os cofres de reserva facilitam a organização dos seus dados de backup, minimizando a sobrecarga de gestão. Os cofres de backup são baseados no modelo Azure Resource Manager da Azure, que fornece capacidades melhoradas para ajudar a proteger dados de backup.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).
1. Digite **o centro de reserva** na caixa de pesquisa.
1. Em **Serviços**, **selecione Backup center**.
1. Na página central de **Backup,** selecione **Vault**.

   ![Selecione Vault no centro de backup](./media/backup-managed-disks/backup-center.png)

1. No **Iniciador: Criar ecrã de abóbada,** selecionar **o cofre de backup** e **proceder**.

   ![Iniciar: Criar cofre](./media/backup-managed-disks/initiate-create-vault.png)

1. No separador **Basics,** forneça subscrição, grupo de recursos, nome do cofre de reserva, região e redundância de armazenamento de backup. Continue selecionando **Review + create**. Saiba mais sobre [a criação de um cofre de reserva.](./backup-vault-overview.md#create-a-backup-vault)

   ![Rever e criar cofre](./media/backup-managed-disks/review-and-create.png)

## <a name="create-backup-policy"></a>Criar política de backup

1. No cofre de **Backup** *DemoVault* criado no passo anterior, vá para as **políticas de Backup** e selecione **Add**.

   ![Adicionar política de backup](./media/backup-managed-disks/backup-policies.png)

1. No **separador Basics,** forneça o nome da política, selecione **datasource type** como **Disco Azure**. O cofre já está pré-voizado e as propriedades do cofre selecionados são apresentadas.

   >[!NOTE]
   > Embora o cofre selecionado possa ter a definição global de redundância, atualmente a Azure Disk Backup suporta apenas a loja de dados instantânea. Todas as cópias de segurança são armazenadas num grupo de recursos na sua subscrição e não são copiadas para o armazenamento de cofre de reserva.

   ![Selecione o tipo de fonte de dados](./media/backup-managed-disks/datasource-type.png)

1. No separador **política de backup,** selecione a frequência de agendamento de backup.

   ![Selecione frequência de agenda de backup](./media/backup-managed-disks/backup-schedule-frequency.png)

   O Azure Disk Backup oferece várias cópias de segurança por dia. Se necessitar de cópias de segurança mais frequentes, escolha a frequência de backup **hourly** com a capacidade de fazer backups com intervalos de cada 4, 6, 8 ou 12 horas. As cópias de segurança são programadas com base no intervalo **de tempo** selecionado. Por exemplo, se selecionar **De 4 em 4 horas,** as cópias de segurança são tomadas aproximadamente no intervalo de cada 4 horas para que as cópias de segurança sejam distribuídas igualmente ao longo do dia. Se uma cópia de segurança uma vez por dia for suficiente, então escolha a frequência de backup **Diária.** Na frequência de backup diária, pode especificar a hora do dia quando as suas cópias de segurança são tomadas. É importante notar que a hora do dia indica a hora de início de backup e não a hora em que a cópia de segurança termina. O tempo necessário para completar a operação de backup depende de vários fatores, incluindo o tamanho do disco, e a taxa de churn entre cópias de segurança consecutivas. No entanto, a cópia de segurança do Azure Disk é uma cópia de segurança sem agente que utiliza [instantâneos incrementais](../virtual-machines/disks-incremental-snapshots.md), o que não afeta o desempenho da aplicação de produção.

1. No separador **política de backup,** selecione as definições de retenção que satisfaçam o requisito do objetivo do ponto de recuperação (RPO).

   A regra de retenção por defeito aplica-se se não for especificada nenhuma outra regra de retenção. A regra de retenção por defeito pode ser modificada para alterar a duração da retenção, mas não pode ser eliminada. Pode adicionar uma nova regra de retenção selecionando a **regra de retenção de adicionar**.

   ![Adicione uma regra de retenção](./media/backup-managed-disks/add-retention-rule.png)

   Pode escolher **a primeira cópia de segurança bem sucedida** tomada diariamente ou semanalmente, e fornecer a duração de retenção que as cópias de segurança específicas devem ser mantidas antes de serem eliminadas. Esta opção é útil para reter cópias de segurança específicas do dia ou da semana por um período de tempo mais longo. Todas as outras cópias de segurança frequentes podem ser mantidas por uma duração mais curta.

   ![Definições de retenção](./media/backup-managed-disks/retention-settings.png)

   >[!NOTE]
   >A Azure Backup for Managed Disks utiliza instantâneos incrementais que estão limitados a 200 instantâneos por disco. Para permitir que você assuma backups a pedido para além de backups programados, a política de backup limita o total de backups para 180. Saiba mais sobre [instantâneos incrementais](../virtual-machines/disks-incremental-snapshots.md#restrictions) para o disco gerido.

1. Complete a criação de política de backup selecionando **Review + create**.

## <a name="configure-backup"></a>Configurar a cópia de segurança

Backup Vault usa Identidade Gerida para aceder a outros recursos Azure. Para configurar a cópia de segurança dos discos geridos, a identidade gerida do cofre de backup requer um conjunto de permissões nos discos de origem e grupos de recursos onde as imagens são criadas e geridas.

Um sistema atribuído à identidade gerida é restrito a um por recurso e está ligado ao ciclo de vida deste recurso. Pode conceder permissões à identidade gerida utilizando o controlo de acesso baseado em funções Azure (Azure RBAC). A identidade gerida é um principal de serviço de um tipo especial que só pode ser usado com recursos Azure. Saiba mais sobre [identidades geridas.](../active-directory/managed-identities-azure-resources/overview.md)

São necessários os seguintes pré-requisitos para configurar a cópia de segurança dos discos geridos:

1. Atribua o papel **de Leitor de Cópia de Segurança** do Disco à identidade gerida do Backup Vault no disco Source que deve ser apoiado.

   1. Vai para o disco que precisa de ser apoiado.

   1. Ir ao **controlo de acesso (IAM)** e selecionar **atribuições de funções adicionar**

   1. No painel de contexto certo, selecione **Disk Backup Reader** na lista de dropdown **role.** Selecione a identidade gerida do cofre de reserva e **guarde**.

   >[!TIP]
   >Digite o nome do cofre de reserva para selecionar a identidade gerida do cofre.

   ![Adicione o papel do leitor de backup do disco](./media/backup-managed-disks/disk-backup-reader-role.png)

1. Atribua o papel **de Contribuinte Snapshot de Disco** à identidade gerida do cofre de backup no grupo de recursos onde as cópias de segurança são criadas e geridas pelo serviço de backup Azure. As imagens do disco são armazenadas num grupo de recursos dentro da sua subscrição. Para permitir que o serviço de backup da Azure crie, armazene e gere fotografias, é necessário fornecer permissões para o cofre de reserva.

   **Escolher o grupo de recursos para armazenar e gerir instantâneos:**

   - Não selecione o mesmo grupo de recursos que o do disco de origem.

   - Como orientação, é recomendado criar um grupo de recursos dedicado como uma loja de dados instantânea para ser usado pelo serviço Azure Backup. Ter um grupo de recursos dedicado permite restringir as permissões de acesso ao grupo de recursos, proporcionando segurança e facilidade de gestão dos dados de backup.

   - Pode utilizar este grupo de recursos para armazenar instantâneos em vários discos que estão a ser (ou planeados) apoiados.  

   - Não é possível criar uma imagem incremental para um disco específico fora da subscrição do disco. Por isso, escolha o grupo de recursos dentro da mesma subscrição que a do disco a ser apoiado. Saiba mais sobre [instantâneos incrementais](../virtual-machines/disks-incremental-snapshots.md#restrictions) para discos geridos.

   Para atribuir o papel, siga estes passos:

   1. Vá ao grupo de recursos. Por exemplo, o grupo de recursos é *o SnapshotRG,* que está na mesma subscrição que a do disco a ser apoiado.

   1. Vá ao **controlo de acesso (IAM)** e selecione **atribuições de funções de adicionar**.

   1. No painel de contexto certo, selecione **Disk Snapshot Contributor** na lista de dropdown **Role.** Selecione a identidade gerida do cofre de reserva e **guarde**.

   >[!TIP]
   >Digite o nome do cofre de reserva para selecionar a identidade gerida do cofre.

   ![Adicione a função de contribuinte snapshot de disco](./media/backup-managed-disks/disk-snapshot-contributor-role.png)

1. Verifique se a identidade gerida do cofre de backup tem o conjunto certo de atribuições de funções no disco de origem e no grupo de recursos que serve como a loja de dados instantânea.

   1. Vá para **o cofre de backup - > Identidade** e selecione **atribuições de funções Azure**.

      ![Selecione atribuições de funções Azure](./media/backup-managed-disks/azure-role-assignments.png)

   1. Verifique se a função, o nome do recurso e o tipo de recurso estão corretamente refletidos.

      ![Verifique a função, nome do recurso e tipo de recurso](./media/backup-managed-disks/verify-role.png)

   >[!NOTE]
   >Embora as atribuições de funções sejam refletidas corretamente no portal, pode levar aproximadamente 15 minutos para que a permissão seja aplicada na identidade gerida do cofre de reserva.

1. Assim que os pré-requisitos forem cumpridos, vá ao **cofre de backup - > visão geral** e selecione **Backup** para começar a configurar a cópia de segurança dos discos.

   ![Selecione backup](./media/backup-managed-disks/select-backup.png)

1. No **separador Basics,** selecione **O Disco Azure** como o tipo de fonte de dados.

   ![Selecione disco Azure](./media/backup-managed-disks/select-azure-disk.png)

   >[!NOTE]
   >O Azure Backup utiliza [instantâneos incrementais](../virtual-machines/disks-incremental-snapshots.md#restrictions) de discos geridos, que armazenam apenas as alterações delta no disco desde a última imagem no armazenamento standard hdd, independentemente do tipo de armazenamento do disco-mãe. Para uma fiabilidade adicional, os instantâneos incrementais são armazenados em Zone Redundant Storage (ZRS) por padrão em regiões que suportam ZRS. Atualmente, o Azure Disk Backup suporta a cópia de segurança operacional de discos geridos que não copiam as cópias de segurança para o armazenamento do cofre de backup. Assim, a definição de redundância de armazenamento de backup do cofre de backup não se aplica aos pontos de recuperação.

1. No separador **política de backup,** escolha uma política de backup.

   ![Escolha a política de backup](./media/backup-managed-disks/choose-backup-policy.png)

1. No **separador Recursos,** **selecione Select Azure Disk**. No painel de contexto certo, selecione os discos a serem apoiados.

   ![Selecione discos para fazer o back-up](./media/backup-managed-disks/select-disks-to-backup.png)

   >[!NOTE]
   >Enquanto o portal permite selecionar vários discos e configurar a cópia de segurança, cada disco é uma instância de backup individual. Atualmente, o Azure Disk Backup suporta apenas a cópia de segurança de discos individuais. A cópia de segurança pontual de vários discos ligados a um disco virtual não é suportada.
   >
   >Ao utilizar o portal, limita-se a selecionar discos dentro da mesma subscrição. Se tiver vários discos a serem apoiados ou se os discos estiverem espalhados por diferentes subscrições, pode utilizar scripts para automatizar.
   >
   >Para obter mais informações sobre a disponibilidade da região de backup do Disco Azure, cenários e limitações apoiados, consulte a matriz de [suporte](disk-backup-support-matrix.md).

1. Selecione um **Grupo de Recursos Instantâneos** e selecione **validar.** Este é o grupo de recursos onde o serviço Azure Backup criou e gere as imagens incrementais para o cofre de reserva. A identidade gerida é atribuída com as permissões de função necessárias como parte dos pré-requisitos.

   ![Selecione grupo de recursos instantâneos](./media/backup-managed-disks/select-snapshot-resource-group.png)

   >[!NOTE]
   >A validação pode demorar alguns minutos a ser concluída antes de conseguir configurar o fluxo de trabalho de backup. A validação pode falhar se:
   >
   > - um disco não é suportado. Consulte a [Matriz de Suporte](disk-backup-support-matrix.md) para cenários não apoiados.
   > - a identidade gerida pelo cofre de backup não tem atribuições de funções válidas no **disco** a ser apoiada ou no **grupo de recursos Snapshot** onde são armazenados instantâneos incrementais.

1. Após uma validação bem sucedida, selecione **revisão e configuração** para configurar a cópia de segurança dos discos selecionados.

## <a name="run-an-on-demand-backup"></a>Executar uma cópia de segurança a pedido

1. No cofre de **Backup** *DemoVault* criado no passo anterior, vá a **instâncias de Backup** e selecione uma instância de backup.

   ![Selecione a instância de backup](./media/backup-managed-disks/select-backup-instance.png)

1. No ecrã **de instâncias de backup,** você encontrará:

   - informações **essenciais,** incluindo o nome do disco de origem, o grupo de recursos instantâneos onde são armazenados instantâneos incrementais, cofre de backup e política de backup.
   - **Estado do trabalho** mostrando resumo das operações de backup e restauro e o seu estado nos últimos sete dias.
   - Uma lista de **pontos** de restauro para o período de tempo selecionado.

1. Selecione **Backup** para iniciar uma cópia de segurança a pedido.

   ![Selecione Backup Agora](./media/backup-managed-disks/backup-now.png)

1. Selecione uma das regras de retenção associadas à política de backup. Esta regra de retenção determinará a duração de retenção deste backup a pedido. Selecione **Backup agora** para iniciar a cópia de segurança.

   ![Iniciar backup](./media/backup-managed-disks/initiate-backup.png)

## <a name="track-a-backup-operation"></a>Acompanhe uma operação de backup

O serviço Azure Backup cria um trabalho para cópias de segurança programadas ou se ativar uma operação de backup a pedido para rastreio. Para visualizar o estado de trabalho de reserva:

1. Vá ao ecrã **de identificação do Backup.** Mostra o painel de trabalhos com operação e estado nos últimos sete dias.

   ![Painel de empregos](./media/backup-managed-disks/jobs-dashboard.png)

1. Para visualizar o estado da operação de backup, **selecione Ver tudo** para mostrar trabalhos em curso e passados desta instância de backup.

   ![Selecione ver tudo](./media/backup-managed-disks/view-all.png)

1. Reveja a lista de backups e restaurar os empregos e o seu estado. Selecione um trabalho na lista de empregos para ver detalhes do trabalho.

   ![Selecione trabalho para ver detalhes](./media/backup-managed-disks/select-job.png)

## <a name="next-steps"></a>Passos seguintes

- [Restaurar Discos Geridos Azure](restore-managed-disks.md)