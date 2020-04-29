---
title: Visão geral dos recursos de segurança
description: Conheça as capacidades de segurança no Azure Backup que o ajudam a proteger os seus dados de backup e a atender às necessidades de segurança do seu negócio.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 2eec3ee50f1de695b5432ee50b0900e35b81a6eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585823"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Visão geral das funcionalidades de segurança no Azure Backup

Um dos passos mais importantes que pode tomar para proteger os seus dados é ter uma infraestrutura de backup fiável. Mas é igualmente importante garantir que os seus dados são protegidos de forma segura, e que os seus backups estão sempre protegidos. O Azure Backup fornece segurança ao seu ambiente de backup - tanto quando os seus dados estão em trânsito como em repouso. Este artigo lista as capacidades de segurança no Azure Backup que o ajudam a proteger os seus dados de backup e a satisfazer as necessidades de segurança do seu negócio.

## <a name="management-and-control-of-identity-and-user-access"></a>Gestão e controlo da identidade e do acesso ao utilizador

A Backup Azure permite-lhe gerir o acesso de grãos finos utilizando o Controlo de [Acesso baseado em papel Azure (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). O RBAC permite-lhe segregar os deveres dentro da sua equipa e conceder apenas a quantidade de acesso aos utilizadores necessários para fazer o seu trabalho.

A Azure Backup fornece três funções incorporadas para controlar as operações de gestão de backup:

* Backup Contributor - para criar e gerir backups, exceto apagar cofre de Serviços de Recuperação e dar acesso a outros
* Backup Operator - tudo o que um colaborador faz, exceto remover backup e gerir políticas de backup
* Backup Reader - permissões para visualizar todas as operações de gestão de backup

Saiba mais sobre [o controlo de acesso baseado em funções para gerir](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)o Backup Azure .

O Azure Backup tem vários controlos de segurança integrados no serviço para prevenir, detetar e responder a vulnerabilidades de segurança. Saiba mais sobre os controlos de [segurança para o Azure Backup](https://docs.microsoft.com/azure/backup/backup-security-controls).

## <a name="separation-between-guest-and-azure-storage"></a>Separação entre o armazenamento de hóspedes e azure

Com o Azure Backup, que inclui backup de máquinavirtual e SQL e SAP HANA em backup VM, os dados de backup são armazenados no armazenamento Azure e o hóspede não tem acesso direto ao armazenamento de backup ou ao seu conteúdo.  Com backup de máquina virtual, a criação e armazenamento de backup de imagens de backup é feito por tecido Azure onde o hóspede não tem qualquer envolvimento além de quiescing a carga de trabalho para backups consistentes de aplicação.  Com a SQL e a SAP HANA, a extensão de backup obtém acesso temporário para escrever a bolhas específicas.  Desta forma, mesmo num ambiente comprometido, os backups existentes não podem ser adulterados ou apagados pelo hóspede.

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Conectividade da Internet não é necessária para backup Azure VM

A cópia de segurança dos VMs Azure requer movimento de dados do disco da sua máquina virtual para o cofre dos Serviços de Recuperação. No entanto, toda a comunicação e transferência de dados necessárias só acontece na rede de espinha dorsal Azure sem necessidade de aceder à sua rede virtual. Por isso, a cópia de segurança dos VMs Azure colocados dentro de redes seguras não requer que permita o acesso a quaisquer IPs ou FQDNs.

## <a name="private-endpoints-for-azure-backup"></a>Pontos finais privados para backup azure

Agora pode utilizar [pontos finais privados](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) para fazer o backback dos seus dados de forma segura a partir de servidores dentro de uma rede virtual para o seu cofre de Serviços de Recuperação. O ponto final privado utiliza um IP do espaço de endereço VNET para o seu cofre, pelo que não precisa de expor as suas redes virtuais a quaisquer IPs públicos. Os Pontos Finais Privados podem ser utilizados para fazer backup e restaurar as bases de dados SQL e SAP HANA que funcionam dentro dos seus VMs Azure. Também pode ser usado para os seus servidores no local utilizando o agente MARS.

>[!NOTE]
> Esta funcionalidade encontra-se atualmente em disponibilidade limitada. Preencha [este inquérito](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) e [envie-nos um e-mail](mailto:azbackupnetsec@microsoft.com) se estiver interessado em utilizar pontos finais privados para backup azure. A capacidade de utilizar esta funcionalidade está sujeita à aprovação do serviço de backup Azure.

## <a name="encryption-of-data-in-transit-and-at-rest"></a>Encriptação de dados em trânsito e em repouso

A encriptação protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e conformidade. Dentro do Azure, os dados em trânsito entre o armazenamento do Azure e o cofre estão protegidos por HTTPS. Estes dados permanecem na rede de espinha dorsal Azure.

* Os dados de cópia de segurança são automaticamente encriptados utilizando chaves geridas pela Microsoft. Também pode encriptar os seus VMs de disco geridos com backed no Cofre de Serviços de Recuperação utilizando [chaves geridas](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption-of-backup-data-using-customer-managed-keys) pelo cliente armazenadas no Cofre de Chaves Azure. Não precisa de tomar nenhuma medida explícita para permitir esta encriptação. Aplica-se a todas as cargas de trabalho que estão a ser apoiadas no cofre dos Serviços de Recuperação.

* O Azure Backup suporta cópias de segurança e restauro de VMs Azure que têm os seus discos OS/dados encriptados com encriptação de disco azure (ADE). [Saiba mais sobre Os VMs Azure encriptados e backup azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>Proteção de dados de backup de exclusões não intencionais

O Azure Backup fornece funcionalidades de segurança para ajudar a proteger os dados de backup mesmo após a eliminação. Com a eliminação suave, se o utilizador eliminar a cópia de segurança de um VM, os dados de backup são conservados por 14 dias adicionais, permitindo a recuperação desse item de backup sem perda de dados. A retenção adicional de 14 dias de dados de backup no estado de "soft delete" não incorre em nenhum custo para o cliente. [Saiba mais sobre a eliminação suave](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#soft-delete).

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>Monitorização e alertas de atividades suspeitas

O Azure Backup fornece [capacidades de monitorização e alerta incorporadas](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) para visualizar e configurar ações para eventos relacionados com o Azure Backup. [Os Relatórios de Backup](https://docs.microsoft.com/azure/backup/configure-reports) servem como um destino único para rastrear o uso, auditoria de backups e restauros, e identificar tendências-chave em diferentes níveis de granularidade. A utilização das ferramentas de monitorização e reporte da Azure Backup pode alertá-lo para qualquer atividade não autorizada, suspeita ou maliciosa assim que ocorrerem.

## <a name="security-features-to-help-protect-hybrid-backups"></a>Funcionalidades de segurança para ajudar a proteger backups híbridos

O serviço de backup Azure utiliza o agente microsoft Azure Recovery Services (MARS) para fazer backup e restaurar ficheiros, pastas e o estado de volume ou sistema de um computador no local para o Azure. Mars agora fornece funcionalidades de segurança para ajudar a proteger backups híbridos. As funcionalidades incluem:

* Uma camada adicional de autenticação é adicionada sempre que uma operação crítica como mudar uma palavra-passe é executada. Esta validação destina-se a garantir que tais operações só podem ser realizadas por utilizadores que tenham credenciais Azure válidas. [Saiba mais sobre as funcionalidades que previnem ataques.](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#prevent-attacks)

* Os dados de cópia de segurança eliminados são conservados por mais 14 dias a partir da data de eliminação. Isto garante a recuperabilidade dos dados dentro de um determinado período de tempo, pelo que não há perda de dados mesmo que um ataque aconteça. Além disso, um maior número de pontos mínimos de recuperação são mantidos para proteger contra dados corruptos. [Saiba mais sobre a recuperação de dados de backup eliminados](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#recover-deleted-backup-data).

* Para dados com cópia de segurança utilizando o agente microsoft Azure Recovery Services (MARS), é utilizada uma frase-passe para garantir que os dados são encriptados antes de serem enviados para o Azure Backup e desencriptados apenas após o download do Azure Backup. Os detalhes da frase-passe só estão disponíveis para o utilizador que criou a frase-passe e para o agente que está configurado com a sua configuração. Nada é transmitido ou partilhado com o serviço. Isto garante a segurança total dos seus dados, uma vez que quaisquer dados expostos inadvertidamente (como um ataque homem-no-meio na rede) são inutilizáveis sem a palavra-passe, e a frase-passe não é enviada na rede.

## <a name="compliance-with-standardized-security-requirements"></a>Cumprimento dos requisitos de segurança padronizados

Para ajudar as organizações a cumprir os requisitos nacionais, regionais e específicos da indústria que regem a recolha e utilização de dados individuais, o Microsoft Azure & Azure Backup oferecem um conjunto abrangente de certificações e atestados. [Consulte a lista de certificações de conformidade](compliance-offerings.md)

## <a name="next-steps"></a>Passos seguintes

* [Funcionalidades de segurança para ajudar a proteger cargas de trabalho em nuvem que usam Backup Azure](backup-azure-security-feature-cloud.md)
* [Funcionalidades de segurança para ajudar a proteger backups híbridos que usam Backup Azure](backup-azure-security-feature.md)
