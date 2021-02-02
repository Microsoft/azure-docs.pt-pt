---
title: Documentação de orientação e melhores práticas
description: Descubra as melhores práticas e orientação para apoiar a nuvem e a carga de trabalho no local para a nuvem
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 7b65556d8dd9b5b12e8da25055f6e39732c83afd
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258766"
---
# <a name="backup-cloud-and-on-premises-workloads-to-cloud"></a>Nuvem de backup e cargas de trabalho no local para cloud

## <a name="introduction"></a>Introdução

O Azure Backup protege exaustivamente os seus ativos de dados em Azure através de uma solução simples, segura e económica que requer infraestruturas zero. É a solução de proteção de dados incorporada do Azure para uma vasta gama de cargas de trabalho. Ajuda a proteger as cargas de trabalho críticas da sua missão que estão na nuvem, e garante que os seus backups estão sempre disponíveis e geridos em escala em toda a sua propriedade de backup.

### <a name="intended-audience"></a>Público-alvo

O público-alvo principal deste artigo são os administradores de TI e aplicações, e implementadores de grandes e médias organizações, que querem aprender sobre as capacidades da tecnologia de proteção de dados incorporada da Azure, Azure Backup, e como implementar eficientemente soluções que melhor protejam as suas implementações. O artigo assume que está familiarizado com as tecnologias core Azure, conceitos de proteção de dados e tem experiência em trabalhar com uma solução de backup. As orientações abrangidas por este artigo podem facilitar a conceção da sua solução de backup no Azure utilizando padrões estabelecidos e evitar armadilhas conhecidas.

### <a name="how-this-article-is-organized"></a>Como este artigo é organizado

Embora seja fácil começar a proteger infraestruturas e aplicações no Azure, quando você garante que os recursos Azure subjacentes são configurado corretamente e sendo usados da melhor forma, você pode acelerar o seu tempo para valorizar. Este artigo abrange uma breve visão geral das considerações de design e orientação para configurar o idealmente a sua implementação de Backup Azure. Examina os componentes principais (por exemplo, cofre de serviços de recuperação, política de backup) e conceitos (por exemplo, governação) e como pensar neles e suas capacidades com ligações à documentação detalhada do produto.

## <a name="architecture"></a>Arquitetura

![Arquitetura do Azure Backup](./media/guidance-best-practices/azure-backup-architecture.png)

### <a name="workloads"></a>Cargas de trabalho

O Azure Backup permite a proteção de dados para várias cargas de trabalho (no local e nuvem). É um mecanismo seguro e fiável de proteção de dados incorporado em Azure. Pode escalar perfeitamente a sua proteção através de várias cargas de trabalho sem qualquer sobrecarga de gestão para si. Existem vários canais de automatização também para permitir isso (via PowerShell, CLI, Azure Resource Manager e REST APIs.)

* **Armazenamento escalável, durável e seguro -** O Azure Backup utiliza um armazenamento blob fiável com segurança incorporada e funcionalidades de alta disponibilidade. Pode escolher armazenamentos LRS, GRS ou RA-GRS para os seus dados de backup.  

* **Integração da carga de trabalho nativa -** O Azure Backup proporciona integração nativa com Azure Workloads (VMs, SAP HANA, SQL em VMs Azure e até mesmo Ficheiros Azure) sem que você gere automatização ou infraestrutura para implantar agentes, escrever novos scripts ou armazenamento de provisões.

### <a name="data-plane"></a>Plano de dados

* **Gestão automatizada de armazenamento** – A Azure Backup automatiza o fornecimento e gestão de contas de armazenamento para os dados de backup para garantir que ele escala à medida que os dados de backup crescem.

* **Proteção maliciosa de exclusão –** Proteja contra quaisquer tentativas acidentais e maliciosas de eliminar as suas cópias de segurança através de uma eliminação suave das cópias de segurança. Os dados de backup eliminados são armazenados gratuitamente durante 14 dias e permitem a sua recuperação deste estado.

* **Segurança encriptada segura...** O Azure Backup garante que os seus dados de backup são armazenados de forma segura, aproveitando as capacidades de segurança incorporadas da plataforma Azure como o Azure RBAC e a Encriptação.

* **Gestão do ciclo de vida de dados de backup -** O Azure Backup limpa automaticamente os dados de backup mais antigos para cumprir as políticas de retenção. Também pode nivelar os seus dados do armazenamento operacional para o armazenamento do cofre.

### <a name="management-plane"></a>Plano de gestão

* **Controlo de acessos** – Os cofres (Serviços de Recuperação e cofres de backup) fornecem as capacidades de gestão e são acessíveis através do portal Azure, Backup Center, Dashboards Vault, SDK, CLI e até MESMO REST APIs. É também um limite Azure RBAC, proporcionando-lhe a opção de restringir o acesso a backups apenas para administradores de backup autorizados.

* **Gestão de** políticas – As políticas de backup do Azure dentro de cada cofre definem quando as cópias de segurança devem ser ativadas e quanto tempo precisam de ser mantidas. Também pode gerir estas políticas e aplicá-las em vários itens.

* **Monitoring and Reporting** – Azure Backup integra-se com o Log Analytics e fornece a capacidade de ver relatórios através de Livros de Trabalho.

* **Gestão snapshot** – Azure Backup tira fotos para algumas cargas de trabalho nativas do Azure (VMs e Ficheiros Azure), gere estes instantâneos e permite restauros rápidos a partir deles. Esta opção reduz drasticamente o tempo de recuperação dos seus dados para o armazenamento original.

## <a name="vault-considerations"></a>Considerações de abóbada

O Azure Backup usa cofres (Serviços de Recuperação e cofres de reserva) para orquestrar e gerir backups. Também usa cofres para armazenar dados com reserva. O design eficaz do cofre ajuda as organizações a estabelecer uma estrutura para organizar e gerir ativos de backup em Azure para apoiar as suas prioridades de negócio. Considere as seguintes diretrizes ao criar um cofre:  

### <a name="align-to-subscription-design-strategy"></a>Alinhar com a estratégia de design de subscrição

Uma vez que o cofre é traçado para uma subscrição, ajuste o seu design de cofre para atender à estratégia de design de subscrição, como *a estratégia de categoria de aplicação,* onde as subscrições são separadas com base em aplicações ou serviços específicos ou ao longo das linhas de arquétipos de aplicação. Para mais informações, consulte este [artigo.](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

### <a name="single-or-multiple-vault"></a>Cofre único ou múltiplo

Você pode usar um único cofre ou vários cofres para organizar e gerir o seu backup. Tenha em consideração as seguintes orientações:

* Se as suas cargas de trabalho forem geridas por uma única subscrição e um único recurso, então pode usar um único cofre para monitorizar e gerir a sua propriedade de backup.

* Se as suas cargas de trabalho estiverem distribuídas por subscrições, então pode criar vários cofres, um ou mais por subscrição.
  * O Backup Center permite-lhe ter um único painel de vidro para gerir todas as tarefas relacionadas com a Cópia de Segurança. [Saiba mais aqui]().
  * Pode personalizar as suas opiniões com modelos de livros de trabalho. Backup Explorer é um desses modelos para VMs Azure. [Saiba mais aqui](monitor-azure-backup-with-backup-explorer.md).
  * Se precisa de uma política consistente através de cofres, então pode usar a política do Azure para propagar a política de backup em vários cofres. Você pode escrever uma definição de [Política Azure](../governance/policy/concepts/definition-structure.md) personalizada que usa o efeito ['deployifnotexists'](../governance/policy/concepts/effects.md#deployifnotexists) para propagar uma política de backup em vários cofres. Também pode [atribuir](../governance/policy/assign-policy-portal.md) esta definição de Política Azure a um determinado âmbito (subscrição ou RG), de modo a que implemente um recurso de "política de backup" a todos os cofres dos Serviços de Recuperação no âmbito da atribuição da Política Azure. As definições da política de backup (tais como frequência de backup, retenção, e assim por diante) devem ser especificadas pelo utilizador como parâmetros na atribuição da Política Azure.

* À medida que a sua pegada organizacional aumenta, talvez queira mover cargas de trabalho através de subscrições pelas seguintes razões: alinhar por política de backup, consolidar cofres, trocar por redundância mais baixa para economizar no custo (passar de GRS para LRS).  O Azure Backup suporta a movimentação de um cofre de Serviços de Recuperação através de subscrições da Azure ou para outro grupo de recursos dentro da mesma subscrição. [Saiba mais aqui](backup-azure-move-recovery-services-vault.md).

### <a name="review-default-settings"></a>Rever definições predefinições

Reveja as definições predefinidos do tipo de replicação de armazenamento e as definições de segurança para satisfazer os seus requisitos antes de configurar cópias de segurança no cofre.

* *O tipo de replicação de armazenamento* por padrão é definido para Geo-redundante (GRS). Uma vez configurada a cópia de segurança, a opção de modificação é desativada. Siga [estes](backup-create-rs-vault.md#set-storage-redundancy) passos para rever e modificar as definições.

* *A eliminação suave* por padrão está ativada em cofres recém-criados para proteger os dados de cópias de segurança de eliminações acidentais ou maliciosas. Siga [estes](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) passos para rever e modificar as definições.

* *Cross Region Restore* permite restaurar VMs Azure em uma região secundária, que é uma região emparelhada Azure. Esta opção permite-lhe realizar exercícios para cumprir os requisitos de auditoria ou conformidade, e restaurar o VM ou o seu disco se houver um desastre na região primária. CRR é uma funcionalidade de opt-in para qualquer cofre GRS. [Saiba mais aqui](backup-create-rs-vault.md#set-cross-region-restore).

* Antes de finalizar o seu design de abóbada, reveja as [matrizes de suporte](backup-support-matrix.md#vault-support) do cofre para entender os fatores que podem influenciar ou limitar as suas escolhas de design.

## <a name="backup-policy-considerations"></a>Considerações de Política de Backup

A Política de Backup Azure tem dois componentes: *Agendar* (quando tomar cópias de segurança) e *Retenção* (quanto tempo para reter o backup). Pode definir a política com base no tipo de dados que está a ser apoiado, requisitos de RTO/RPO, necessidades operacionais ou regulamentares de conformidade e tipo de carga de trabalho (por exemplo, VM, base de dados, ficheiros). [Saiba mais aqui](backup-architecture.md#backup-policy-essentials).

Considere as seguintes orientações ao criar a Política de Backup:

### <a name="schedule-considerations"></a>Considerações de agendamento

* Considere agrupar VMs que requerem a mesma hora de início de programação, frequência e definições de retenção dentro de uma única política.

* Certifique-se de que a hora de início programada de cópia de segurança é durante o tempo de aplicação não máximo de produção.

* Para distribuir tráfego de backup, considere apoiar diferentes VMs em diferentes horas do dia e certifique-se de que os tempos não se sobrepõem.

### <a name="retention-considerations"></a>Considerações de retenção

* A retenção a curto prazo pode ser "minutos" ou "diário". A retenção para pontos de backup "semanais", "mensais" ou "anoridos" é referida como retenção a longo prazo.

* Retenção a longo prazo:
  * Planejado (requisitos de conformidade) - se souber antecipadamente que os dados são necessários anos a partir do momento atual, então use a retenção a longo prazo.
  * Não planeado (requisito a pedido) - se não souber com antecedência, use-o a pedido com definições específicas de retenção personalizadas (estas definições de retenção personalizadas não são impactadas pelas definições de política).

* Backup a pedido com retenção personalizada - se precisar de fazer uma cópia de segurança não agendada através da política de backup, então pode usar uma cópia de segurança a pedido. Isto pode ser útil para obter backups que não se encaixem na sua cópia de segurança programada ou para obter cópias de segurança granulares (por exemplo, várias cópias de segurança IaaS VM por dia, uma vez que o backup programado permite apenas uma cópia de segurança por dia). É importante notar que a política de retenção definida na política programada não se aplica a backups a pedido.

### <a name="optimize-backup-policy"></a>Otimizar a política de backup

* À medida que os requisitos do seu negócio mudam, poderá ter de prolongar ou reduzir a duração da retenção. Quando o fizer, pode esperar o seguinte:  
  * Se a retenção for alargada, os pontos de recuperação existentes são marcados e mantidos de acordo com a nova política.
  * Se a retenção for reduzida, os pontos de recuperação são marcados para poda no próximo trabalho de limpeza e subsequentemente eliminados.
  * As últimas regras de retenção aplicam-se a todos os pontos de retenção (excluindo os pontos de retenção a pedido). Assim, se o período de retenção for prorrogado (por exemplo, para 100 dias), então quando a cópia de segurança é tomada, seguida da redução da retenção (por exemplo de 100 dias para sete dias), todos os dados de backup serão conservados de acordo com o último período de retenção especificado (ou seja, 7 dias).

* O Azure Backup proporciona-lhe a flexibilidade para *parar de proteger e gerir as suas cópias de segurança:*
  * *Pare a proteção e retenha os dados de backup*. Se estiver a retirar ou a desativar a sua fonte de dados (VM, aplicação), mas precisar de reter dados para fins de auditoria ou conformidade, então pode usar esta opção para impedir que todos os futuros trabalhos de backup protejam a sua fonte de dados e retenha os pontos de recuperação que foram apoiados. Em seguida, pode restaurar ou retomar a proteção VM.
  * *Parar a proteção e eliminar dados de cópia de segurança*. Esta opção impedirá que todos os futuros trabalhos de backup protejam o seu VM e eliminarão todos os pontos de recuperação. Não poderá restaurar o VM nem utilizar a opção de backup do Resume.

  * Se retomar a proteção (de uma fonte de dados que foi interrompida com dados de retenção), então as regras de retenção serão aplicadas. Quaisquer pontos de recuperação caducados serão removidos (na hora prevista).

* Antes de completar o seu design de política, é importante estar ciente dos seguintes fatores que podem influenciar as suas escolhas de design.
  * Uma política de reserva é vista para um cofre.
  * Há um limite no número de itens por política (por exemplo, 100 VMs). Em escala, pode criar políticas duplicadas com os mesmos horários ou horários diferentes.
  * Não é possível eliminar seletivamente pontos de recuperação específicos.
  * Não é possível desativar completamente a cópia de segurança programada e manter a fonte de dados num estado protegido. A cópia de segurança menos frequente que pode configurar com a apólice é ter uma cópia de segurança semanal programada. Uma alternativa seria parar a proteção com os dados de retenção e permitir a proteção sempre que quiser fazer uma cópia de segurança, fazer uma cópia de segurança a pedido e, em seguida, desligar a proteção, mas reter os dados de backup. [Saiba mais aqui](backup-azure-manage-vms.md#stop-protecting-a-vm).

## <a name="security-considerations"></a>Considerações de segurança

Para ajudá-lo a proteger os seus dados de backup e a satisfazer as necessidades de segurança do seu negócio, o Azure Backup fornece garantias de confidencialidade, integridade e disponibilidade contra ataques deliberados e abuso dos seus valiosos dados e sistemas. Considere as seguintes diretrizes de segurança para a sua solução de Backup Azure:

### <a name="authentication-and-authorization"></a>Autenticação e autorização

* O controlo de acesso baseado em funções (Azure RBAC) permite uma gestão de acessos finos, a segregação de deveres dentro da sua equipa e a concessão apenas da quantidade de acesso aos utilizadores necessários para desempenharem os seus trabalhos. [Saiba mais aqui](backup-rbac-rs-vault.md).

* A Azure Backup fornece três funções incorporadas para controlar operações de gestão de backup: colaboradores de backup, operadores e leitores. [Saiba mais aqui](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions).

* O Azure Backup tem vários controlos de segurança incorporados no serviço para prevenir, detetar e responder a vulnerabilidades de segurança (Saiba mais)

* As contas de armazenamento utilizadas pelos cofres dos Serviços de Recuperação estão isoladas e não podem ser acedidas pelos utilizadores para fins maliciosos. O acesso só é permitido através de operações de gestão de Backup Azure, como a restauração.

### <a name="encryption-of-data-in-transit-and-at-rest"></a>Encriptação de dados em trânsito e em repouso

A encriptação protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e de conformidade.

* Dentro do Azure, os dados em trânsito entre o armazenamento Azure e o cofre estão protegidos por HTTPS. Estes dados permanecem dentro da rede Azure.

* Os dados de backup são automaticamente encriptados utilizando as teclas geridas pela Microsoft. Em alternativa, pode utilizar as suas próprias chaves, também conhecidas como [teclas geridas pelo cliente](encryption-at-rest-with-cmk.md).

* O Azure Backup suporta a cópia de segurança e a restauração de VMs Azure que têm os seus discos DE/S/Data encriptados com Encriptação do Disco Azure (ADE). [Saiba mais aqui](backup-azure-vms-encryption.md).

### <a name="protection-of-backup-data-from-unintentional-deletes"></a>Proteção de dados de backup de eliminações não intencionais

O Azure Backup fornece funcionalidades de segurança para ajudar a proteger os dados de backup mesmo após a eliminação. Com a eliminação suave, se um utilizador eliminar a cópia de segurança (de uma base de dados VM, SQL Server, partilha de ficheiros Azure, base de dados SAP HANA) os dados de backup são retidos por 14 dias adicionais, permitindo a recuperação desse item de backup sem perda de dados. Os 14 dias adicionais de retenção de dados de backup no estado de "soft delete" não lhe incorrem em qualquer custo. [Saiba mais aqui](backup-azure-security-feature-cloud.md).

### <a name="monitoring-and-alerts-of-suspicious-activity"></a>Monitorização e alertas de atividades suspeitas

O Azure Backup fornece capacidades de monitorização e alerta incorporadas para visualizar e configurar ações para eventos relacionados com a Azure Backup. [Saiba mais aqui](security-overview.md#monitoring-and-alerts-of-suspicious-activity).

### <a name="security-features-to-help-protect-hybrid-backups"></a>Funcionalidades de segurança para ajudar a proteger backups híbridos

O serviço Azure Backup utiliza o agente Microsoft Azure Recovery Services (MARS) para fazer backup e restaurar ficheiros, pastas e o estado de volume ou sistema de um computador no local para o Azure. A MARS agora fornece funcionalidades de segurança: uma palavra-passe para encriptar antes do upload e desencriptar após o download do Azure Backup, os dados de backup eliminados são retidos por mais 14 dias a partir da data de eliminação, e operação crítica (ex. alterar uma palavra-passe) só pode ser realizada por utilizadores que tenham credenciais Azure válidas. [Saiba mais aqui](backup-azure-security-feature.md).

## <a name="network-considerations"></a>Considerações de rede

A Azure Backup requer movimento de dados da sua carga de trabalho para o cofre dos Serviços de Recuperação. O Azure Backup fornece várias capacidades para proteger os dados de backup de serem expostos inadvertidamente (como um ataque homem-no-meio na rede). Tenha em consideração as seguintes orientações:

### <a name="internet-connectivity"></a>Conectividade Internet

* *Backup Azure VM* - toda a comunicação e transferência de dados necessárias entre o armazenamento e o serviço Azure Backup acontece dentro da rede Azure sem necessidade de aceder à sua rede virtual. Assim, a cópia de segurança dos VMs Azure colocados dentro de redes seguras não requer que você permita o acesso a quaisquer IPs ou FQDNs.

* *As bases de dados SAP HANA em Azure VM, bases de dados do SQL Server em Azure VM* - requer conectividade ao serviço de Backup Azure, Azure Storage e Azure Ative Directory. Isto pode ser conseguido utilizando pontos finais privados ou permitindo o acesso aos endereços IP públicos necessários ou FQDNs. Não permitir a conectividade adequada aos serviços Azure necessários pode levar a falhas em operações como a descoberta de bases de dados, configurar backup, realizar backups e restaurar dados. Para obter uma orientação completa da rede durante a utilização de tags NSG, firewall Azure e HTTP Proxy, consulte estes artigos [SQL](backup-sql-server-database-azure-vms.md#establish-network-connectivity) e [SAP HANA.](./backup-azure-sap-hana-database.md#establish-network-connectivity)

* *Híbrido* - o agente MARS (Microsoft Azure Recovery Services) requer acesso à rede para todas as operações críticas - instalar, configurar, fazer backup e restaurar. O agente MARS pode ligar-se ao serviço Azure Backup sobre a [Azure ExpressRoute](install-mars-agent.md#use-azure-expressroute) utilizando o espreitamento público (disponível para circuitos antigos) e o esprevamento da Microsoft, utilizando [pontos finais privados](install-mars-agent.md#private-endpoints) ou através de [proxy/firewall com controlos de acesso apropriados](install-mars-agent.md#verify-internet-access).

### <a name="private-endpoints-for-azure-backup"></a>Pontos finais privados para backup Azure

O Azure [Private Endpoint](../private-link/private-endpoint-overview.md) é uma interface de rede que o liga de forma privada e segura a um serviço alimentado pela Azure Private Link. O Azure Backup permite-lhe fazer o backup seguro e restaurar os seus dados a partir dos cofres dos Serviços de Recuperação utilizando pontos finais privados.

* Quando ativa pontos finais privados para o cofre, eles só são usados para cópias de segurança e restauro de cargas de trabalho SQL e SAP HANA em backups de agentes Azure VM e MARS.  Pode utilizar o cofre para a cópia de segurança de outras cargas de trabalho também (no entanto, não exigirão pontos finais privados). Além da cópia de segurança das cargas de trabalho SQL e SAP HANA e da cópia de segurança utilizando o agente MARS, os pontos finais privados também são utilizados para realizar a recuperação de ficheiros no caso de cópia de segurança Azure VM. [Saiba mais aqui](private-endpoints.md#recommended-and-supported-scenarios).

* O Azure Ative Directory não suporta atualmente pontos finais privados. Assim, os IPs e FQDNs necessários para o Azure Ative Directory terão de ser autorizados a aceder à saída da rede segura ao realizar cópias de segurança de bases de dados em VMs Azure e cópia de segurança utilizando o agente MARS. Também pode utilizar tags NSG e Azure Firewall para permitir o acesso ao Azure AD, conforme aplicável. Saiba mais sobre os [pré-requisitos aqui.](./private-endpoints.md#before-you-start)

## <a name="governance-considerations"></a>Considerações de governação

A governação em Azure é implementada principalmente com [a Azure Policy](../governance/policy/overview.md) e [a Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md). [A Azure Policy](../governance/policy/overview.md) permite-lhe criar, atribuir e gerir definições políticas para impor regras para os seus recursos. Esta funcionalidade mantém esses recursos em conformidade com os seus padrões corporativos. [A Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) permite-lhe acompanhar o uso da nuvem e as despesas para os seus recursos Azure e outros fornecedores de nuvem. Além disso, as seguintes ferramentas como [Azure Price Calculator](https://azure.microsoft.com/pricing/calculator/) e [Azure Advisor](../advisor/advisor-overview.md)  desempenham um papel importante no processo de gestão de custos.

### <a name="azure-backup-support-two-key-scenarios-via-built-in-azure-policy"></a>Azure Backup suporta dois cenários-chave através da política de Azure incorporada

* Certifique-se de que as máquinas críticas de negócio recentemente criadas são automaticamente apoiadas com as definições de retenção certas. O Azure Backup fornece uma política incorporada (usando a Política Azure) que pode ser atribuída a todos os VMs Azure em um local especificado dentro de um grupo de subscrição ou recursos. Quando esta política é atribuída a um determinado âmbito, todos os novos VMs criados nesse âmbito são automaticamente configurados para cópia de segurança para um cofre existente no mesmo local e subscrição. O utilizador pode especificar o cofre e a política de retenção à qual os VMs apoiados devem estar associados. [Saiba mais aqui](backup-azure-auto-enable-backup.md).

* Certifique-se de que os cofres recém-criados têm diagnósticos habilitados a suportar relatórios. Muitas vezes, adicionar uma definição de diagnóstico manualmente por abóbada pode ser uma tarefa complicada. Além disso, qualquer novo cofre criado precisa de ter configurações de diagnóstico ativadas para que possa ver relatórios para este cofre. Para simplificar a criação de configurações de diagnóstico em escala (com o Log Analytics como destino), o Azure Backup fornece uma Política de Azure incorporada. Esta política adiciona uma definição de diagnóstico de LA a todos os cofres de cada grupo de subscrição ou recursos. As seguintes secções fornecem instruções sobre como utilizar esta política. [Saiba mais aqui](azure-policy-configure-diagnostics.md).

### <a name="azure-backup-cost-considerations"></a>Considerações de custos de backup Azure

As capacidades do serviço Azure Backup oferecem a flexibilidade para gerir eficazmente os seus custos, e ainda satisfazem os requisitos de negócio do BCDR (continuidade do negócio e recuperação de desastres). Tenha em consideração as seguintes orientações:

* Utilize a calculadora de preços para avaliar e otimizar o custo ajustando várias alavancas. [Saiba mais aqui](azure-backup-pricing.md)

* Backup Explorer: Utilize Backup Explorer ou Relatórios de Backup para compreender e otimizar o crescimento do armazenamento de backup, parar cópias de segurança para cargas de trabalho não críticas ou VMs eliminados. Você pode obter uma visão agregada de toda a sua propriedade de uma perspetiva de reserva. Isto inclui não só informações sobre os seus itens de backup, mas também recursos que não estão configurados para cópia de segurança. Isto garante que nunca perca a proteção de dados críticos no seu espólio em crescimento e as suas cópias de segurança são otimizadas para cargas de trabalho não críticas ou cargas de trabalho eliminadas.

* Otimizar a política de backup
  * Otimizar as definições de horário e retenção com base em arquétipos de carga de trabalho (por exemplo, críticos da missão, não críticos)
  * Otimizar as definições de retenção para restauro instantâneo
  * Escolha o tipo de backup certo para satisfazer os requisitos, enquanto toma em consideração os tipos de backup suportados (completos, incrementais, log, diferenciais) pela carga de trabalho em Azure Backup.

* Discos de backup seletivos: Excluir o disco (funcionalidade de pré-visualização) fornece uma escolha eficiente e económica para fazer o backup seletivo de dados críticos. Por exemplo, faça uma errólhada quando não quiser fazer o back up do resto dos discos ligados a um VM. Isto também é útil quando você tem várias soluções de backup. Por exemplo, quando faz cópia de segurança das suas bases de dados ou dados com uma solução de backup de carga de trabalho (base de dados SQL Server na cópia de segurança Azure VM) e pretende utilizar a cópia de segurança do nível Azure VM para discos selecionados.

* O Azure Backup tira fotos dos VMs Azure e armazena-os juntamente com os discos para aumentar a criação de pontos de recuperação e acelerar as operações de restauro. Isto é referido como Restauro Instantâneo. Por predefinição, as imagens instante Restore são mantidas durante dois dias. Esta funcionalidade permite uma operação de restauro a partir destes instantâneos, reduzindo os tempos de restauro. Reduz o tempo necessário para transformar e copiar dados do cofre. Como resultado, você verá os custos de armazenamento que correspondem a instantâneos tirados durante este período. [Saiba mais aqui](backup-instant-restore-capability.md#configure-snapshot-retention).

* O tipo de replicação de armazenamento do cofre de backup Azure por predefinição é definido para Geo-redundante (GRS). Esta opção não pode ser alterada depois de proteger os itens. O armazenamento geo-redundante (GRS) proporciona um nível mais elevado de durabilidade de dados do que o armazenamento localmente redundante (LRS), permite que um opt-in utilize o Cross Region Restore e custa mais. Reveja as compensações entre custos mais baixos e maior durabilidade dos dados, e decida o que é melhor para o seu cenário. [Saiba mais aqui](backup-create-rs-vault.md#set-storage-redundancy)

* Se estiver a proteger a carga de trabalho que funciona dentro de um VM e do próprio VM, verifique se esta dupla proteção é necessária.

## <a name="monitoring-and-alerting-considerations"></a>Questões de monitorização e alerta

Como utilizador ou administrador de backup, deverá ser capaz de monitorizar todas as soluções de backup e ser notificado em cenários importantes. Esta secção detalha as capacidades de monitorização e notificação fornecidas pelo serviço de backup Azure.

### <a name="monitoring"></a>Monitorização

* O Azure Backup fornece **monitorização de trabalho incorporada** para operações como configurar backup, backup, restaurar, eliminar backup, e assim por diante. Isto é traçado para o cofre, e ideal para monitorizar um único cofre. [Saiba mais aqui](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault).

* Se você precisa monitorizar as atividades operacionais em escala, então **o Backup Explorer** fornece uma visão agregada de toda a sua propriedade de backup, permitindo uma análise detalhada de perfuração e resolução de problemas. É um livro de trabalho incorporado do Azure Monitor que dá uma localização única e central para ajudá-lo a monitorizar as atividades operacionais em toda a propriedade de backup em Azure, abrangendo inquilinos, locais, subscrições, grupos de recursos e cofres. [Saiba mais aqui](monitor-azure-backup-with-backup-explorer.md).
  * Use-o para identificar recursos que não estão configurados para backup, e certifique-se de que nunca perde a proteção de dados críticos na sua propriedade em crescimento.
  * O painel de instrumentos proporciona atividades operacionais nos últimos sete dias (máximo). Se precisar de reter estes dados, poderá exportar como ficheiro Excel e retê-los.
  * Se você é um utilizador do Azure Lighthouse, você pode ver informações através de vários inquilinos, permitindo uma monitorização sem limites.

* Se precisar de manter e ver as atividades operacionais a longo prazo, utilize **relatórios**. Um requisito comum para os administradores de backup é obter insights sobre cópias de segurança com base em dados que se estendem por um longo período de tempo. Os casos de utilização para tal solução incluem:
  * Alocação e previsão de armazenamento em nuvem consumido.
  * Auditoria de backups e restauros.
  * Identificar as principais tendências em diferentes níveis de granularidade.

* Além disso
  * Pode enviar dados (por exemplo, empregos, políticas, e assim por diante) para o espaço de trabalho **Log Analytics.** Isto permitirá que as funcionalidades dos Registos do Monitor Azure permitam a correlação de dados com outros dados de monitorização recolhidos pelo Azure Monitor, consolidar as entradas de registo de várias subscrições de Azure e inquilinos num local para análise em conjunto, utilizar consultas de registo para realizar análises complexas e obter informações profundas sobre as entradas de Log. [Saiba mais aqui](../azure-monitor/platform/activity-log.md#send-to-log-analytics-workspace).
  * Pode enviar dados para o Event Hub para enviar entradas fora do Azure, por exemplo para um SIEM de terceiros (Informação de Segurança e Gestão de Eventos) ou outra solução de análise de registos. [Saiba mais aqui](../azure-monitor/platform/activity-log.md#send-to-azure-event-hubs).
  * Pode enviar dados para uma conta de Armazenamento Azure se pretender reter os seus dados de registo por mais de 90 dias para auditoria, análise estática ou backup. Se precisar apenas de reter os seus eventos durante 90 dias ou menos, não precisa de configurar arquivos para uma conta de armazenamento, uma vez que os eventos de Registo de Atividade são mantidos na plataforma Azure durante 90 dias. [Saiba mais](../azure-monitor/platform/activity-log.md#send-to--azure-storage).

### <a name="alerting"></a>Alertas

* Os alertas são, em primeiro lugar, uma forma de serem notificados para tomar medidas relevantes. A secção alertas de cópia de segurança mostra alertas gerados pelo serviço de backup Azure.

* O Azure Backup fornece um mecanismo de notificação **de alerta incorporado** via e-mail para falhas, avisos e operações críticas. Pode especificar endereços de e-mail individuais ou listas de distribuição para ser notificado quando um alerta é gerado. Também pode escolher se deve ser notificado para cada alerta individual ou agrupar-se numa digestão de hora a hora e, em seguida, ser notificado.
  * Estes alertas são definidos pelo serviço e fornecem suporte para cenários limitados - falhas de backup/restauro, Parar a proteção com retenção de dados/proteção stop com dados de eliminação, e assim por diante. [Saiba mais aqui](backup-azure-monitoring-built-in-monitor.md#alert-scenarios).
  * Se for realizada uma operação destrutiva como a proteção stop com os dados de eliminação, é levantado um alerta e é enviado um e-mail aos proprietários de assinaturas, administradores e administradores, mesmo que as notificações **não** estejam configuradas para o cofre dos Serviços de Recuperação.
  * Certas cargas de trabalho podem gerar alta frequência de falhas (por exemplo, SQL Server a cada 15 minutos). Para evitar que se sobreponha a alertas levantados para cada falha, os alertas são consolidados. [Saiba mais aqui](backup-azure-monitoring-built-in-monitor.md#consolidated-alerts).
  * Os alertas incorporados não podem ser personalizados e estão restritos a e-mails definidos no portal Azure.

* Se precisar de **criar alertas personalizados** (por exemplo, alertas de trabalhos bem sucedidos) utilize o Log Analytics. No Azure Monitor, pode criar os seus próprios alertas num espaço de trabalho log analytics. As cargas de trabalho híbridas (DPM/MABS) também podem enviar dados para LA e usar LA para fornecer alertas comuns através de cargas de trabalho apoiadas pela Azure Backup.

* Também pode obter notificações através de **registos** de atividades de cofres incorporados nos Serviços de Recuperação. No entanto, suporta cenários limitados e não é adequado para operações como backup programado, que se alinha melhor com registos de recursos do que com registos de atividade. Para saber mais sobre estas limitações e como pode utilizar o espaço de trabalho do Log Analytics para monitorizar e alertar em escala para todas as suas cargas de trabalho protegidas pela Azure Backup, consulte este [artigo](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-to-monitor-at-scale).

## <a name="next-steps"></a>Passos seguintes

Recomendamos que leia os seguintes artigos como pontos de partida para a utilização de Azure Backup:

* [Visão geral do Backup Azure](backup-overview.md)
* [Perguntas Mais Frequentes](backup-azure-backup-faq.md)
