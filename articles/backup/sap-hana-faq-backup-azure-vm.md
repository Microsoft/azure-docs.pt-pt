---
title: FAQ - Fazer a cópia de segurança de bases de dados SAP HANA nas VMs do Azure
description: Neste artigo, descubra respostas a perguntas comuns sobre o backup das bases de dados SAP HANA utilizando o serviço de backup Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: bf662600bafcd18b00c8f8d3b673fc3f9c110aca
ms.sourcegitcommit: 1d366d72357db47feaea20c54004dc4467391364
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95400212"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Perguntas frequentes - Apoiar bases de dados SAP HANA em VMs Azure

Este artigo responde a perguntas comuns sobre o backup das bases de dados SAP HANA utilizando o serviço de backup Azure.

## <a name="backup"></a>Cópia de segurança

### <a name="how-many-full-backups-are-supported-per-day"></a>Quantos reforços completos são suportados por dia?

Apoiamos apenas um reforço completo por dia. Não podes ter apoio diferencial e cópias de segurança ativadas no mesmo dia.

### <a name="do-successful-backup-jobs-create-alerts"></a>As tarefas de cópia de segurança bem-sucedida criam alertas?

Não. Trabalhos de apoio bem sucedidos não geram alertas. Os alertas são enviados apenas para trabalhos de reserva que falham. O comportamento detalhado dos alertas do portal está documentado [aqui.](./backup-azure-monitoring-built-in-monitor.md) No entanto, se estiver interessado em ter alertas mesmo para trabalhos bem sucedidos, pode utilizar [o Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Posso ver trabalhos de reserva agendados no menu Backup Jobs?

O menu Backup Job só mostrará trabalhos de backup a pedido. Para trabalhos programados, utilize [o Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md).

### <a name="are-future-databases-automatically-added-for-backup"></a>As bases de dados futuras são adicionadas automaticamente para cópia de segurança?

Não, isto não é apoiado.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Se eu apagar uma base de dados de um caso, o que acontecerá com as cópias de segurança?

Se uma base de dados for retirada de uma instância SAP HANA, as cópias de segurança da base de dados ainda são tentadas. Isto implica que a base de dados eliminada começa a aparecer como pouco saudável sob **Itens de Cópia de Segurança** e ainda está protegida.
A forma correta de parar de proteger esta base de dados é efetuar **o Stop Backup com a eliminação de dados** nesta base de dados.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Se eu mudar o nome da base de dados depois de ter sido protegida, qual será o comportamento?

Uma base de dados renomeada é tratada como uma nova base de dados. Portanto, o serviço tratará esta situação como se a base de dados não tivesse sido encontrada e falhará nas cópias de segurança. A base de dados renomeada será uma nova base de dados e deve ser configurada para proteção.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Quais são os pré-requisitos para fazer o back bases de dados SAP HANA num Azure VM?

Consulte os [pré-requisitos](tutorial-backup-sap-hana-db.md#prerequisites) e [o que o script de pré-registo faz.](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)

### <a name="what-permissions-should-be-set-so-azure-can-back-up-sap-hana-databases"></a>Que permissões devem ser definidas para que o Azure possa fazer o back bases de dados SAP HANA?

A execução do script pré-registo define as permissões necessárias para permitir que a Azure faça o back-up das bases de dados SAP HANA. Pode saber mais sobre o que o script de pré-registo faz [aqui.](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)

### <a name="will-backups-work-after-migrating-sap-hana-from-sdc-to-mdc"></a>Os backups funcionarão após a migração do SAP HANA da SDC para a MDC?

Consulte [esta secção](./backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid) do guia de resolução de problemas.

### <a name="what-should-be-done-while-upgrading-within-the-same-version"></a>O que deve ser feito durante a atualização dentro da mesma versão?

Consulte [esta secção](backup-azure-sap-hana-database-troubleshoot.md#sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm) no guia de resolução de problemas.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>O Backup Azure HANA pode ser configurado contra um IP virtual (balanceador de carga) e não uma máquina virtual?

Atualmente não temos a capacidade de configurar a solução apenas contra um IP virtual. Precisamos de uma máquina virtual para executar a solução.

### <a name="how-can-i-move-an-on-demand-backup-to-the-local-file-system-instead-of-the-azure-vault"></a>Como posso mover uma cópia de segurança a pedido para o sistema de ficheiros local em vez do cofre do Azure?

1. Aguarde que a cópia de segurança atualmente em execução esteja concluída na base de dados desejada (verifique a partir do estúdio para conclusão).
1. Desative as cópias de segurança de registo e descreva a cópia de segurança do catálogo para o **Ficheiro** para o DB pretendido utilizando os seguintes passos:
1. Configuração de duplo clique **SYSTEMDB**  ->  **configuration**  ->  **Selecione** filtro de base de dados  ->  **(log)**
    1. Desema enable_auto_log_backup para **não.**
    1. Desateia catalog_backup_using_backint **em falso.**
1. Pegue uma cópia de segurança a pedido (completa/diferencial/ incremental) na base de dados desejada e aguarde a conclusão da cópia de segurança e do catálogo.
1. Se também pretender mover as cópias de segurança de registo para o Sistema de Ficheiros, desloque enable_auto_log_backup para **sim**.
1. Reverta para as definições anteriores para permitir que as cópias de segurança fluam para o cofre Azure:
    1. Desateia enable_auto_log_backup para **sim.**
    1. De catalog_backup_using_backint a **verdade.**

>[!NOTE]
>Mover cópias de segurança para o sistema de ficheiros locais e voltar a mudar para o cofre Azure pode causar uma rutura da corrente de registo das cópias de segurança do cofre. Isto irá desencadear uma cópia de segurança completa, que uma vez concluída com sucesso, começará a fazer backup dos registos.

### <a name="how-can-i-use-sap-hana-backup-with-my-hana-replication-set-up"></a>Como posso usar o SAP HANA Backup com a minha configuração de replicação HANA?

Atualmente, o Azure Backup não tem a capacidade de entender uma configuração HSR. Isto significa que os nós primários e secundários do HSR serão tratados como dois VMs individuais e não relacionados. Primeiro terá de configurar o nó primário. Quando uma falha acontece, a cópia de segurança deve ser configurada no nó secundário (que agora se torna o nó primário). Não há falha automática de apoio ao outro nó.

Para fazer o armazenamento de dados do nó ativo (primário) em qualquer ponto do tempo, pode mudar a **proteção** para o nó secundário, que agora se tornou o principal após o ressalimbamento.

Para efetuar esta **proteção do interruptor,** siga estes passos:

- [Parar a proteção](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) (com os dados de retenção) nas primárias
- Executar o [script de pré-registo](https://aka.ms/scriptforpermsonhana) no nó secundário
- [Descubra as bases de dados](tutorial-backup-sap-hana-db.md#discover-the-databases) no nó secundário e [configuure cópias de segurança neles](tutorial-backup-sap-hana-db.md#configure-backup)

Estes passos devem ser executados manualmente após cada falha. Pode efetuar estes passos através da linha de comando / HTTP REST para além do portal Azure. Para automatizar estes passos, pode utilizar um livro de bordo Azure.

Aqui está um exemplo detalhado de como a **proteção do interruptor** deve ser realizada:

Neste exemplo, tem dois nós - Nó 1 (primário) e Nó 2 (secundário) na configuração do HSR.  As cópias de segurança estão configuradas no nó 1. Como mencionado acima, não tente configurar backups no Nó 2.

Quando a primeira falha acontece, o Nó 2 torna-se o principal. Então,

1. Parar a proteção do Nó 1 (primário anterior) com a opção de retenção de dados.
1. Executar o script de pré-registo no Nó 2 (que agora é o principal).
1. Descubra bases de dados no Nó 2, atribua a política de backup e configurar backups.

Em seguida, uma primeira cópia de segurança completa é acionada no Nó 2 e, depois disso, iniciam-se as cópias de segurança.

Quando o próximo fail-over acontece, o Nó 1 torna-se primário novamente e o Nó 2 torna-se secundário. Agora repita o processo:

1. Parar a proteção do Nó 2 com a opção de retenção de dados.
1. Executar o script de pré-registo no nó 1 (que se tornou novamente o principal)
1. Em [seguida, retome a cópia de segurança](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) no Nó 1 com a política necessária (uma vez que as cópias de segurança foram paradas mais cedo no Nó 1).

Em seguida, a cópia de segurança completa será novamente ativada no Nó 1 e, depois disso, iniciam-se as cópias de segurança.

## <a name="restore"></a>Restauro

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Por que não consigo ver o sistema HANA a que quero que a minha base de dados seja restaurada?

Verifique se todos os pré-requisitos para a restauração para a instância SAP HANA alvo são cumpridos. Para obter mais informações, consulte [Pré-requisitos - Restaurar as bases de dados SAP HANA em Azure VM](./sap-hana-db-restore.md#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Porque é que o DB overwrite está a falhar na minha base de dados?

Certifique-se de que a opção **De Substituição da Força** é selecionada durante a restauração.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Por que vejo que os sistemas de origem e alvo para restauro são incompatíveis?

Consulte a Nota [HANA 1642148](https://launchpad.support.sap.com/#/notes/1642148) para ver que tipos de restauro são suportados atualmente.

### <a name="can-i-use-a-backup-of-a-database-running-on-sles-to-restore-to-an-rhel-hana-system-or-vice-versa"></a>Posso usar uma cópia de segurança de uma base de dados em funcionamento no SLES para restaurar um sistema RHEL HANA ou vice-versa?

Sim, pode utilizar cópias de segurança de streaming ativadas numa base de dados HANA em funcionamento no SLES para restaurar para um sistema RHEL HANA e vice-versa. Ou seja, a restauração do sistema operativo transversal é possível utilizando cópias de segurança de streaming. No entanto, terá de garantir que o sistema HANA a que pretende restaurar, e o sistema HANA utilizado para restauro, são ambos compatíveis para restauro de acordo com o SAP. Consulte a Nota [HANA 1642148](https://launchpad.support.sap.com/#/notes/1642148) para ver quais os tipos de restauro compatíveis.

## <a name="policy"></a>Política

### <a name="different-options-available-during-creation-of-a-new-policy-for-sap-hana-backup"></a>Diferentes opções disponíveis durante a criação de uma nova política para o backup SAP HANA

Antes de criar uma política, deve ser claro sobre os requisitos da RPO e da RTO e as suas implicações de custos relevantes.

O RPO (objetivo de recuperação- ponto de recuperação) indica quanto a perda de dados é aceitável para o utilizador/cliente. Isto é determinado pela frequência de reserva de registo. As cópias de segurança de registo mais frequentes indicam um RPO mais baixo e o valor mínimo suportado pelo serviço Azure Backup é de 15 minutos. Assim, a frequência de backup pode ser de 15 minutos ou mais.

O RTO (objetivo do tempo de recuperação) indica a rapidez com que os dados devem ser restaurados até ao último ponto de tempo disponível após um cenário de perda de dados. Isto depende da estratégia de recuperação utilizada pela HANA, que normalmente depende do número de ficheiros necessários para ser restaurado. Isto também tem implicações de custos, e a tabela a seguir deve ajudar a compreender todos os cenários e as suas implicações.

|Política de cópias de segurança  |RTO  |Custo  |
|---------|---------|---------|
|Diário Full + registos     |   Mais rápido, já que precisamos apenas de uma cópia completa + registos necessários para restauro pontual      |    A opção mais dispendiosa uma vez que uma cópia completa é tomada diariamente e assim cada vez mais dados são acumulados em backend até ao tempo de retenção   |
|SemanalMente Full + diferencial diário + registos     |   Mais lento do que a opção acima, mas mais rápido do que a opção seguinte, uma vez que exigimos uma cópia completa + uma cópia diferencial + registos para restauro pontual      |    Opção mais barata uma vez que o diferencial diário é geralmente menor do que cheio e uma cópia completa é tomada apenas uma vez por semana      |
|SemanalMente Full + diário incremental + logs     |  Mais lento já que precisamos de uma cópia completa + 'n' incrementais + registos para recuperação pontual       |     Opção menos dispendiosa uma vez que o incremental diário será menor do que o diferencial e uma cópia completa é tomada apenas semanalmente    |

> [!NOTE]
> As opções acima são as mais comuns, mas não as únicas opções. Por exemplo, pode ter uma cópia de segurança completa semanal + diferenciais duas vezes por semana + registos.

Portanto, pode selecionar a variante de política com base em objetivos de RPO e RTO e considerações de custos.

### <a name="impact-of-modifying-a-policy"></a>Impacto da modificação de uma política

Alguns princípios devem ser mentes na determinação do impacto da mudança da política de um item de reserva da Política 1 (P1) para a Política 2 (P2) ou da política de edição 1 (P1).

- Todas as alterações são também aplicadas retroativamente. A última política de backup é aplicada também nos pontos de recuperação tomados anteriormente. Por exemplo, assumam que a retenção diária total é de 30 dias e foram tomados 10 pontos de recuperação de acordo com a política atualmente ativa. Se a retenção diária for alterada para 10 dias, então o prazo de validade do ponto anterior também é recalculado como hora de início + 10 dias e apagado se estiverem expirados.
- O âmbito de mudança também inclui dia de backup, tipo de backup juntamente com a retenção. Por exemplo: Se uma política for alterada de diariamente para dia inteiro aos domingos, todos os ofustados anteriores que não são aos domingos serão marcados para eliminação.
- Um progenitor não é apagado até que a criança esteja ativa/não expirada. Cada tipo de backup tem um tempo de validade de acordo com a política atualmente ativa. Mas um tipo completo de backup é considerado como pai para subsequentes 'diferenciais', 'incrementais' e 'logs'. Um "diferencial" e um "log" não são pais de mais ninguém. Um 'incremental' pode ser um pai para subsequente 'incremental'. Mesmo que um 'pai' esteja marcado para a eliminação, não é realmente apagado se os "diferenciais" ou 'registos' da criança não estiverem caducados. Por exemplo, se uma política for alterada de diariamente para se manter completa aos domingos, todos os ofustados anteriores que não são aos domingos serão marcados para eliminação. Mas não são apagados até que os registos que foram tomados diariamente mais cedo expirem. Por outras palavras, são retidos de acordo com a última duração do registo. Uma vez expiradas as registos, tanto os registos como estes fulls serão eliminados.

Com estes princípios, pode ler-se a seguinte tabela para compreender as implicações de uma mudança de política.

|Política antiga/ Nova política  |Diárias + registos  | Diárias + diferenciais diárias + troncos  |Diárias + incrementais diários + troncos  |
|---------|---------|---------|---------|
|Diárias + registos     |   -      |    Os ofícios anteriores que não estão no mesmo dia da semana estão marcados para eliminação, mas mantidos até ao período de retenção de registos.     |    Os ofícios anteriores que não estão no mesmo dia da semana estão marcados para eliminação, mas mantidos até ao período de retenção de registos.     |
|Diárias + diferenciais diárias + troncos     |   A retenção semanal anterior é recalculada de acordo com a política mais recente. Os diferenciais anteriores são imediatamente eliminados      |    -     |    Os diferenciais anteriores são imediatamente eliminados     |
|Diárias + incrementais diários + troncos     |     A retenção semanal anterior é recalculada de acordo com a política mais recente. Os incrementos anteriores são imediatamente eliminados    |     Os incrementos anteriores são imediatamente eliminados    |    -     |

## <a name="next-steps"></a>Próximos passos

Saiba como [fazer o back bases de dados SAP HANA](./backup-azure-sap-hana-database.md) em funcionamento em VMs Azure.
