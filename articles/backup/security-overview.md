---
title: Visão geral das funcionalidades de segurança
description: Saiba mais sobre as capacidades de segurança no Azure Backup que o ajudam a proteger os seus dados de backup e a satisfazer as necessidades de segurança do seu negócio.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 9aa1909f1590b477d9a7f7a09ad0c2b1936e3e29
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325660"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Visão geral das funcionalidades de segurança no Azure Backup

Um dos passos mais importantes que pode tomar para proteger os seus dados é ter uma infraestrutura de backup fiável. Mas é igualmente importante garantir que os seus dados são apoiados de forma segura, e que os seus backups estão sempre protegidos. O Azure Backup fornece segurança ao seu ambiente de backup - tanto quando os seus dados estão em trânsito como em repouso. Este artigo lista as capacidades de segurança no Azure Backup que o ajudam a proteger os seus dados de backup e a satisfazer as necessidades de segurança do seu negócio.

## <a name="management-and-control-of-identity-and-user-access"></a>Gestão e controlo da identidade e do acesso ao utilizador

As contas de armazenamento utilizadas pelos cofres dos Serviços de Recuperação estão isoladas e não podem ser acedidas pelos utilizadores para fins maliciosos. O acesso só é permitido através de operações de gestão de Backup Azure, como a restauração. O Azure Backup permite-lhe controlar as operações geridas através de um acesso fino utilizando [o controlo de acesso baseado em funções Azure (Azure RBAC)](./backup-rbac-rs-vault.md). O Azure RBAC permite-lhe segregar funções dentro da sua equipa e conceder apenas a quantidade de acesso aos utilizadores necessário para fazer o seu trabalho.

A Azure Backup fornece três [funções incorporadas](../role-based-access-control/built-in-roles.md) para controlar as operações de gestão de backup:

* Backup Contributor - para criar e gerir backups, exceto eliminar o cofre dos Serviços de Recuperação e dar acesso a outros
* Backup Operator - tudo o que um contribuinte faz exceto remover backup e gerir políticas de backup
* Backup Reader - permissões para visualizar todas as operações de gestão de backup

Saiba mais sobre [o controlo de acesso baseado em funções da Azure para gerir o Azure Backup](./backup-rbac-rs-vault.md).

O Azure Backup tem vários controlos de segurança incorporados no serviço para prevenir, detetar e responder a vulnerabilidades de segurança. Saiba mais sobre [os controlos de segurança para a Azure Backup](./security-baseline.md).

## <a name="separation-between-guest-and-azure-storage"></a>Separação entre o hóspede e o armazenamento Azure

Com a Azure Backup, que inclui cópia de segurança virtual da máquina e SQL e SAP HANA na cópia de segurança VM, os dados de backup são armazenados no armazenamento Azure e o hóspede não tem acesso direto ao armazenamento de backup ou ao seu conteúdo.  Com a cópia de segurança da máquina virtual, a criação e armazenamento de imagens de backup é feita por tecido Azure, onde o hóspede não tem outro envolvimento que não seja a desemaçar a carga de trabalho para backups consistentes da aplicação.  Com SQL e SAP HANA, a extensão de backup obtém acesso temporário para escrever para bolhas específicas.  Desta forma, mesmo num ambiente comprometido, as cópias de segurança existentes não podem ser adulteradas ou eliminadas pelo hóspede.

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Conectividade na Internet não é necessária para cópia de segurança Azure VM

A cópia de segurança dos VMs Azure requer o movimento de dados do disco da sua máquina virtual para o cofre dos Serviços de Recuperação. No entanto, toda a comunicação e transferência de dados necessárias ocorre apenas na rede de espinha dorsal Azure sem precisar de aceder à sua rede virtual. Portanto, a cópia de segurança dos VMs Azure colocados dentro de redes seguras não requer que você permita o acesso a quaisquer IPs ou FQDNs.

## <a name="private-endpoints-for-azure-backup"></a>Pontos finais privados para backup Azure

Agora pode utilizar [o Private Endpoints](../private-link/private-endpoint-overview.md) para fazer o back up de segurança dos seus dados de servidores dentro de uma rede virtual até ao cofre dos Serviços de Recuperação. O ponto final privado utiliza um IP do espaço de endereço VNET para o seu cofre, por isso não precisa de expor as suas redes virtuais a quaisquer IPs públicos. Os pontos finais privados podem ser utilizados para fazer o backup e restaurar as bases de dados SQL e SAP HANA que funcionam dentro dos seus VMs Azure. Também pode ser usado para os seus servidores no local usando o agente MARS.

Leia mais sobre os pontos finais privados para o Azure Backup [aqui](./private-endpoints.md).

## <a name="encryption-of-data"></a>Encriptação de dados

A encriptação protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e de conformidade. A encriptação de dados ocorre em muitas fases no Azure Backup:

* Dentro do Azure, os dados em trânsito entre o armazenamento Azure e o cofre estão [protegidos por HTTPS](backup-support-matrix.md#network-traffic-to-azure). Estes dados permanecem na rede de espinha dorsal do Azure.

* Os dados de backup são automaticamente encriptados utilizando [teclas geridas pela plataforma](backup-encryption.md), e não é necessário tomar nenhuma ação explícita para o ativar. Também pode encriptar os seus dados de reserva utilizando [as teclas geridas pelo cliente armazenadas](encryption-at-rest-with-cmk.md) no Cofre de Chaves Azure. Aplica-se a todas as cargas de trabalho que estão a ser apoiadas até ao cofre dos Serviços de Recuperação.

* O Azure Backup suporta a cópia de segurança e a restauração de VMs Azure que têm os seus discos DE/S/Dados encriptados com [Encriptação de Disco Azure (ADE)](backup-azure-vms-encryption.md#encryption-support-using-ade) e [VMs com discos encriptados CMK](backup-azure-vms-encryption.md#encryption-using-customer-managed-keys). Para mais informações, [saiba mais sobre VMs Azure encriptados e Backup Azure](./backup-azure-vms-encryption.md).

* Quando os dados são cópias de segurança dos servidores no local com o agente MARS, os dados são encriptados com uma palavra-passe antes do upload para Azure Backup e desencriptados apenas depois de serem descarregados a partir do Azure Backup. Leia mais sobre [funcionalidades de segurança para ajudar a proteger as cópias de segurança híbridas.](#security-features-to-help-protect-hybrid-backups)

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>Proteção de dados de backup de eliminações não intencionais

O Azure Backup fornece funcionalidades de segurança para ajudar a proteger os dados de backup mesmo após a eliminação. Com a eliminação suave, se o utilizador eliminar a cópia de segurança de um VM, os dados de cópia de segurança são retidos por mais 14 dias, permitindo a recuperação desse item de backup sem perda de dados. Os 14 dias adicionais de retenção de dados de backup no estado de "soft delete" não lhe incorrem em qualquer custo. [Saiba mais sobre a eliminação suave.](backup-azure-security-feature-cloud.md)

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>Monitorização e alertas de atividades suspeitas

O Azure Backup fornece [capacidades de monitorização e alerta incorporadas](./backup-azure-monitoring-built-in-monitor.md) para visualizar e configurar ações para eventos relacionados com a Azure Backup. [Os Relatórios de Backup](./configure-reports.md) servem como um destino único para rastrear o uso, auditar backups e restauros, e identificar tendências-chave em diferentes níveis de granularidade. A utilização das ferramentas de monitorização e reporte da Azure Backup pode alertá-lo para qualquer atividade não autorizada, suspeita ou maliciosa assim que ocorram.

## <a name="security-features-to-help-protect-hybrid-backups"></a>Funcionalidades de segurança para ajudar a proteger backups híbridos

O serviço Azure Backup utiliza o agente Microsoft Azure Recovery Services (MARS) para fazer backup e restaurar ficheiros, pastas e o estado de volume ou sistema de um computador no local para o Azure. A MARS agora fornece funcionalidades de segurança para ajudar a proteger as cópias de segurança híbridas. Estas funcionalidades incluem:

* Uma camada adicional de autenticação é adicionada sempre que é realizada uma operação crítica como alterar uma palavra-passe. Esta validação destina-se a garantir que tais operações só podem ser realizadas por utilizadores que tenham credenciais Azure válidas. [Saiba mais sobre as funcionalidades que impedem os ataques.](./backup-azure-security-feature.md#prevent-attacks)

* Os dados de cópia de segurança eliminados são retidos por mais 14 dias a contar da data de eliminação. Isto garante a recuperabilidade dos dados dentro de um determinado período de tempo, por isso não há perda de dados mesmo que um ataque aconteça. Além disso, um maior número de pontos mínimos de recuperação são mantidos para proteger contra dados corruptos. [Saiba mais sobre a recuperação de dados de backup eliminados.](./backup-azure-security-feature.md#recover-deleted-backup-data)

* Para os dados com cópia de segurança utilizando o agente Microsoft Azure Recovery Services (MARS), é utilizada uma palavra-passe para garantir que os dados são encriptados antes do upload para a Azure Backup e desencriptados apenas após o download do Azure Backup. Os detalhes da palavra-passe só estão disponíveis para o utilizador que criou a palavra-passe e o agente que está configurado com ele. Nada é transmitido ou partilhado com o serviço. Isto garante a segurança completa dos seus dados, uma vez que todos os dados expostos inadvertidamente (como um ataque homem-no-meio na rede) são inutilizáveis sem a palavra-passe, e a palavra-passe não é enviada pela rede.

## <a name="compliance-with-standardized-security-requirements"></a>Cumprimento dos requisitos de segurança normalizados

Para ajudar as organizações a cumprirem os requisitos nacionais, regionais e específicos da indústria que regem a recolha e utilização de dados individuais, o Microsoft Azure & Azure Backup oferecem um conjunto abrangente de certificações e atestados. [Consulte a lista de certificações de conformidade](compliance-offerings.md)

## <a name="next-steps"></a>Passos seguintes

* [Funcionalidades de segurança para ajudar a proteger cargas de trabalho em nuvem que usam backup Azure](backup-azure-security-feature-cloud.md)
* [Funcionalidades de segurança para ajudar a proteger backups híbridos que usam backup Azure](backup-azure-security-feature.md)