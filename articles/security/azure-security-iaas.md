---
title: Melhores práticas de segurança de IaaS cargas de trabalho no Azure | Documentos da Microsoft
description: " A migração de cargas de trabalho de IaaS do Azure oferece oportunidades para reavaliar a nossa designs "
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2018
ms.author: barclayn
ms.openlocfilehash: c60022d43af1601359ab5de8b4d3e4eb06dd239a
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243232"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Melhores práticas de segurança para cargas de trabalho de IaaS no Azure

Na maioria dos infraestrutura como um cenários de serviço (IaaS), [máquinas virtuais do Azure (VMs)](https://docs.microsoft.com/azure/virtual-machines/) é a carga de trabalho principal para organizações que utilizam a cloud de informática. Esse fato é evidente nos [cenários híbridos](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) em que as organizações querem lentamente migrar cargas de trabalho para a cloud. Em tais cenários, siga os [considerações de segurança geral para IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)e aplique as melhores práticas de segurança para todas as suas VMs.

A responsabilidade de segurança baseia-se no tipo de serviço em nuvem. A tabela a seguir resume o saldo de responsabilidade para a Microsoft e:

![Áreas de responsabilidade](./media/azure-security-iaas/sec-cloudstack-new.png)

Requisitos de segurança variam consoante vários fatores, incluindo diferentes tipos de cargas de trabalho. Não uma dessas práticas recomendadas pode por si só, proteger seus sistemas. Como qualquer outra coisa em segurança, terá de escolher as opções apropriadas e veja como as soluções podem se complementam ao preencher as lacunas.

Este artigo descreve as melhores práticas de segurança para sistemas operacionais e de VMs.

As melhores práticas baseiam-se um consenso de opinião, e trabalhar com recursos atuais da plataforma do Azure e conjuntos de recursos. Uma vez que opiniões e as tecnologias podem mudar ao longo do tempo, este artigo será atualizado para refletir essas alterações.

## <a name="protect-vms-by-using-authentication-and-access-control"></a>Proteger VMs com o controlo de acesso e autenticação
O primeiro passo para proteger as suas VMs é garantir que os utilizadores autorizados apenas pode configurar novas VMs e acesso de VMs.

**Melhor prática**: Controlar o acesso VM.   
**Detalhe**: Uso [políticas do Azure](../azure-policy/azure-policy-introduction.md) para estabelecer as convenções para recursos na sua organização e criar políticas personalizadas. Aplicar estas políticas para recursos, como [grupos de recursos](../azure-resource-manager/resource-group-overview.md). As VMs que pertencem a um grupo de recursos herdam suas diretivas.

Se sua organização tiver várias subscrições, poderá ter uma forma de gerenciar com eficiência o acesso, políticas e conformidade para nessas subscrições. [Grupos de gestão do Azure](../azure-resource-manager/management-groups-overview.md) fornecer um nível de âmbito acima subscrições. Organizar subscrições para grupos de gestão (contentores) e aplicar suas condições de governação a esses grupos. Todas as subscrições dentro de um grupo de gestão herdam automaticamente as condições aplicadas ao grupo. Os grupos de gestão dão-lhe capacidades de gestão de nível empresarial em grande escala, independentemente do seu tipo de subscrição.

**Melhor prática**: Reduza a variabilidade na sua configuração e implementação de VMs.   
**Detalhe**: Uso [do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) modelos para reforçar suas opções de implantação e torná-lo mais fácil de compreender e as VMs no seu ambiente de inventário.

**Melhor prática**: Proteger o acesso privilegiado.   
**Detalhe**: Utilize um [abordagem de privilégios mínimos](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) e funções do Azure incorporadas, para permitir que os utilizadores acessar e definir a segurança de VMs:

- [Contribuinte de máquina virtual](../role-based-access-control/built-in-roles.md#virtual-machine-contributor): Pode gerir VMs, mas não a rede ou armazenamento conta virtual ao qual estão ligados.
- [Contribuinte de Máquina Virtual clássica](../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor): Pode gerir as VMs criadas com o modelo de implementação clássica, mas não a rede ou armazenamento conta virtual ao qual as VMs estão ligadas.
- [Administrador de segurança](../role-based-access-control/built-in-roles.md#security-admin): No Centro de segurança apenas: Pode ver as políticas de segurança, visualizar os Estados de segurança, editar as políticas de segurança, ver alertas e recomendações, dispensar alertas e recomendações.
- [Utilizador de DevTest Labs](../role-based-access-control/built-in-roles.md#devtest-labs-user): Pode ver tudo e ligar, iniciar, reiniciar e encerrar VMs.

Os administradores de subscrição e co-administradores, podem alterar esta definição, tornando-os administradores de todas as VMs numa subscrição. Certifique-se de que confia todos os seus administradores da subscrição e co-administradores para iniciar sessão qualquer uma das suas máquinas.

> [!NOTE]
> Recomendamos que consolida as VMs com o mesmo ciclo de vida no mesmo grupo de recursos. Ao utilizar grupos de recursos, pode implementar, monitorizar e agregar os custos para os seus recursos de faturação.
>
>

As organizações que controlam o acesso à VM e configuração melhorar a segurança geral de VM.

## <a name="use-multiple-vms-for-better-availability"></a>Utilizar várias VMs para melhor disponibilidade
Se a VM executa aplicações críticas que tem de ter elevada disponibilidade, recomendamos vivamente que utilize várias VMs. Para melhor disponibilidade, utilize um [conjunto de disponibilidade](../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

Um conjunto de disponibilidade é um agrupamento lógico que pode utilizar no Azure para se certificar de que os recursos da VM que nele colocar estão isolados uns dos outros quando são implantados no datacenter do Azure. Azure garante que as VMs que colocar num disponibilidade definida execução em vários servidores físicos, suportes de computação, unidades de armazenamento e comutadores de rede. Se ocorrer uma falha de software do Azure ou de hardware, apenas um subconjunto das suas VMs são afetados e a aplicação global continua a estar disponível para os seus clientes. Conjuntos de disponibilidade são uma função essencial quando pretende criar soluções cloud fiáveis.

## <a name="protect-against-malware"></a>Proteja-se contra o software maligno
Deve instalar a proteção antimalware para ajudar a identificar e remover vírus, spyware e outro software malicioso. Pode instalar [Microsoft Antimalware](azure-security-antimalware.md) ou solução de proteção de ponto final de um parceiro da Microsoft ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [O Windows Defender](https://www.microsoft.com/search/result.aspx?q=Windows+defender+endpoint+protection), e [System Center Endpoint Protection](https://www.microsoft.com/search/result.aspx?q=System+Center+endpoint+protection)).

Antimalware da Microsoft inclui recursos como a proteção em tempo real, análise agendada, remediação de software maligno, atualizações de assinatura, atualizações de mecanismos, exemplos de relatórios e recolha de eventos de exclusão. Para ambientes alojados em separado do seu ambiente de produção, pode usar uma extensão de antimalware para ajudar a proteger as suas VMs e serviços em nuvem.

Pode integrar soluções de Antimalware da Microsoft e de parceiros com [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/) para facilitar a implantação e incorporada deteções (alertas e incidentes).

**Melhor prática**: Instale uma solução de antimalware para proteger contra software maligno.   
**Detalhe**: [Instalar uma solução de parceiro da Microsoft ou o Antimalware da Microsoft](../security-center/security-center-install-endpoint-protection.md)

**Melhor prática**: Integre a sua solução de antimalware no Centro de segurança para monitorizar o estado de sua proteção.   
**Detalhe**: [Gerir problemas de proteção de ponto final com o Centro de segurança](../security-center/security-center-partner-integration.md)

## <a name="manage-your-vm-updates"></a>Gerir as atualizações VM
VMs do Azure, como todas as VMs no local, se destinam a ser gerido pelo utilizador. Azure não enviar atualizações do Windows para eles. Tem de gerir as atualizações VM.

**Melhor prática**: Mantenha as suas VMs atuais.   
**Detalhe**: Utilize o [gestão de atualizações](../automation/automation-update-management.md) solução na automatização do Azure para gerir atualizações do sistema para os seus computadores Windows e Linux que estão implementadas no Azure, a funcionar em ambientes no local ou em outros fornecedores de nuvem. Pode rapidamente avaliar o estado das atualizações disponíveis em todos os computadores agente e gerir o processo de instalação de atualizações necessárias para os servidores.

Computadores que são geridos pela gestão de atualizações utilizam as seguintes configurações para efetuar a avaliação e implementações de atualizações:

- Microsoft Monitoring Agent (MMA) para Windows ou Linux
- Configuração de Estado Pretendido do PowerShell (DSC) para Linux
- Função de Trabalho de Runbook Híbrida da Automatização
- Microsoft Update ou Windows Server Update Services (WSUS) para computadores Windows

Se utilizar o Windows Update, deixe a definição de atualização do Windows automática ativada.

**Melhor prática**: Certifique-se na implementação que criou as imagens incluem a rodada mais recente de atualizações do Windows.   
**Detalhe**: Procure e instale todas as atualizações do Windows como uma primeira etapa de cada implementação. Esta medida é especialmente importante a ser aplicada quando implantar imagens provenientes de ou sua própria biblioteca. Embora as imagens do Azure Marketplace são atualizadas automaticamente por predefinição, pode haver um tempo de latência (até algumas semanas) após o lançamento público.

**Melhor prática**: Reimplemente periodicamente as suas VMs para forçar uma nova versão do sistema operacional.   
**Detalhe**: Definir a sua VM com uma [modelo Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) para que pode facilmente implementar novamente. Com um modelo dá-lhe uma VM segura e corrigida quando precisa dela.

**Melhor prática**: Instale as atualizações de segurança mais recentes.   
**Detalhe**: Algumas das primeiras cargas de trabalho que os clientes mover para o Azure são laboratórios e sistemas de face externa. Se as VMs do Azure alojar aplicações ou serviços que precisam de estar acessíveis à internet, ser vigilante sobre a aplicação de patches. Aplicar o patch ponha o sistema operativo. Vulnerabilidades não corrigidas em aplicações de parceiros também podem levar a problemas que podem ser evitados se gerenciamento de patches boa estiver em vigor.

**Melhor prática**: Implementar e testar uma solução de cópia de segurança.   
**Detalhe**: Uma cópia de segurança deve ser Tratado da mesma forma que lidar com qualquer outra operação. Isso é verdadeiro de sistemas que fazem parte do seu ambiente de produção, alargando-se para a cloud.

Sistemas de teste e desenvolvimento tem de seguir as estratégias de cópia de segurança que fornecem capacidades de restauro que são semelhantes às que os utilizadores que cresceram acostumados a, com base nessa experiência com ambientes no local. Cargas de trabalho de produção movidas para o Azure, devem integrar com soluções de cópia de segurança existentes sempre que possível. Em alternativa, pode utilizar [cópia de segurança do Azure](../backup/backup-azure-vms-first-look-arm.md) para ajudar a solucionar seus requisitos de cópia de segurança.

As organizações que não impõem as políticas de atualização de software são mais expostas a ameaças que exploram vulnerabilidades conhecidas e fixas anteriormente. Para estar em conformidade com normas do setor, as empresas tem de provar que são diligentes e usando controles de segurança correto para o ajudar a garantir a segurança de cargas de trabalho localizada na cloud.

Melhores práticas para um datacenter tradicional de atualização de software e de IaaS do Azure têm muitas semelhanças com. Recomendamos que avaliar as políticas de atualização de software atual para incluir as VMs localizadas no Azure.

## <a name="manage-your-vm-security-posture"></a>Gerir a sua postura de segurança VM
Ameaças virtuais estão a evoluir. Proteger as suas VMs requer uma capacidade de monitorização que pode rapidamente detetar ameaças, impedir o acesso não autorizado aos seus recursos, acionar alertas e reduzir os falsos positivos.

Para monitorizar a postura de segurança da sua [Windows](../security-center/security-center-virtual-machine.md) e [VMs do Linux](../security-center/security-center-linux-virtual-machine.md), utilize [Centro de segurança do Azure](../security-center/security-center-intro.md). No Centro de segurança, proteja as suas VMs, tirando partido dos recursos a seguir:

- Aplica definições de segurança do sistema operacional com regras de configuração recomendadas.
- Identifique e transferir atualizações críticas que podem estar em falta e de segurança do sistema.
- Implemente recomendações para o endpoint protection de antimalware.
- Valide a encriptação de disco.
- Avaliar e remediar vulnerabilidades.
- Detete ameaças.

Centro de segurança pode monitorizar ativamente ameaças e potenciais ameaças são expostas nos alertas de segurança. Ameaças correlacionadas são agregadas numa única vista chamada um incidente de segurança.

Centro de segurança armazena os dados no [registos do Azure Monitor](../log-analytics/log-analytics-overview.md). Registos de Monitor do Azure fornece um mecanismo de análise e linguagem de consulta que lhe dá informações sobre o funcionamento das suas aplicações e recursos. Dados também são recolhidos a partir [do Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md), soluções de gestão e agentes instalados nas máquinas virtuais na cloud ou no local. Esta funcionalidade partilhada ajuda-o a formar uma visão geral do seu ambiente.

As organizações que não impõem uma segurança forte para as suas VMs permaneçam sem conhecimento dos potenciais tentativas por usuários não autorizados para driblar controles de segurança.

## <a name="monitor-vm-performance"></a>Monitorizar o desempenho de VM
Abuso de recursos pode ser um problema quando os processos VM consumam mais recursos do que deveriam. Problemas de desempenho com uma VM podem levar a interrupção do serviço, que viola o princípio de segurança de disponibilidade. Isso é particularmente importante para as VMs que estão a alojar o IIS ou outros servidores web, porque o alto uso de CPU ou memória pode indicar um ataque de negação de serviço (DoS). É imperativo para monitorizar o acesso VM apenas forma reativa enquanto está a ocorrer um problema, mas também proativamente contra o desempenho de linha de base, conforme medido durante o funcionamento normal.

Recomendamos que utilize [do Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md) ganhar visibilidade sobre o estado de funcionamento do seu recurso. Funcionalidades de Monitor do Azure:

- [Ficheiros de registo de diagnóstico de recursos](../azure-monitor/platform/diagnostic-logs-overview.md): Monitoriza os seus recursos VM e identifica potenciais problemas que possam comprometer o desempenho e disponibilidade.
- [Extensão de diagnóstico do Azure](../azure-monitor/platform/diagnostics-extension-overview.md): Fornece capacidades de monitorização e diagnóstico em VMs do Windows. Pode ativar estas capacidades, incluindo a extensão como parte da [modelo Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md).

As organizações que não monitorizar o desempenho da VM não é possível determinar se são determinadas alterações nos padrões de desempenho normal ou anormal. Uma VM que está a consumir mais recursos do que o normal pode indicar um ataque de um recurso externo ou um processo comprometido em execução na VM.

## <a name="encrypt-your-virtual-hard-disk-files"></a>Encriptar os ficheiros de disco rígido virtual
Recomendamos que encripte os discos rígidos virtuais (VHDs) para ajudar a proteger o volume de arranque e volumes de dados Inativos no armazenamento, juntamente com as chaves de encriptação e segredos.

[O Azure Disk Encryption](azure-security-disk-encryption-overview.md) ajuda-o a encriptar discos da máquina virtual Windows e Linux IaaS. O Azure Disk Encryption utiliza o padrão da indústria [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) recurso do Windows e o [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funcionalidade do Linux para fornecer encriptação de volume para o sistema operacional e os discos de dados. A solução está integrada [do Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para o ajudar a controlar e gerir as chaves de encriptação de disco e segredos na sua subscrição do Cofre de chaves. A solução também garante que todos os dados nos discos de máquina virtual são encriptados em inatividade no armazenamento do Azure.

Seguem-se melhores práticas para utilizar o Azure Disk Encryption:

**Melhor prática**: Ative a encriptação em VMs.   
**Detalhe**: O Azure Disk Encryption gera e escreve as chaves de encriptação para o seu Cofre de chaves. A gestão de chaves de encriptação no seu Cofre de chaves requer autenticação do Azure AD. Crie uma aplicação do Azure AD para esta finalidade. Para fins de autenticação, pode utilizar a autenticação com base no segredo do cliente ou [autenticação de cliente baseada em certificado do Azure AD](../active-directory/active-directory-certificate-based-authentication-get-started.md).

**Melhor prática**: Utilize uma chave de encriptação de chaves (KEK) para uma camada adicional de segurança para as chaves de encriptação. Adicione um KEK ao seu Cofre de chaves.   
**Detalhe**: Utilize o [Add-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azurekeyvaultkey) cmdlet para criar uma chave de encriptação de chave no Cofre de chaves. Também pode importar um KEK de seu módulo de segurança de hardware (HSM) de no local para a gestão de chaves. Para obter mais informações, consulte a [documentação do Key Vault](../key-vault/key-vault-hsm-protected-keys.md). Quando é especificada uma chave de encriptação de chave, o Azure Disk Encryption utiliza essa chave para encapsular os segredos de encriptação antes de escrever para o Key Vault. Manter uma cópia de caução desta chave num local a gestão de chaves HSM oferece proteção adicional contra a eliminação acidental de chaves.

**Melhor prática**: Efetuar uma [instantâneo](../virtual-machines/windows/snapshot-copy-managed-disk.md) e/ou criar cópias de segurança antes dos discos são encriptados. As cópias de segurança fornecem uma opção de recuperação se acontecer de uma falha inesperada durante a encriptação.   
**Detalhe**: As VMs com discos geridos requerem uma cópia de segurança antes de ocorre de encriptação. Depois de uma cópia de segurança é feita, pode utilizar o **conjunto AzVMDiskEncryptionExtension** cmdlet para encriptar discos geridos ao especificar a *- skipVmBackup* parâmetro. Para obter mais informações sobre como criar cópias de segurança e restaurar VMs encriptadas, consulte a [Azure Backup](../backup/backup-azure-vms-encryption.md) artigo.

**Melhor prática**: Para certificar-se de que os segredos de encriptação, não ultrapassam limites regionais, o Azure Disk Encryption tem o Cofre de chaves e as VMs localizadas na mesma região.   
**Detalhe**: Criar e utilizar um cofre de chaves que está na mesma região que a VM ser encriptada.

Quando aplica o Azure Disk Encryption, pode atender as necessidades de negócio seguintes:

- VMs de IaaS são protegidas em descanso através da tecnologia de encriptação de norma da indústria para atender a requisitos de segurança e conformidade organizacionais.
- Comece a VMs de IaaS em controlado pelo cliente chaves e políticas e pode auditar a utilização das mesmas no seu Cofre de chaves.

## <a name="next-steps"></a>Passos Seguintes
Ver [padrões e práticas recomendadas de segurança do Azure](security-best-practices-and-patterns.md) para obter mais melhores práticas de segurança a utilizar quando estiver conceber, implementar e gerir soluções na cloud ao utilizar o Azure.

Os seguintes recursos estão disponíveis para fornecer informações mais gerais acerca da segurança do Azure e serviços Microsoft relacionados:
* [Blogue de equipa de segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – para obter informações atualizadas sobre as mais recentes da segurança do Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – onde podem ser comunicadas as vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, ou por e-mail para secure@microsoft.com
