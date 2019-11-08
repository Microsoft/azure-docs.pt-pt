---
title: Práticas recomendadas de segurança para cargas de trabalho de IaaS no Azure | Microsoft Docs
description: " A migração de cargas de trabalho para o Azure IaaS traz oportunidades para reavaliar nossos designs "
services: security
documentationcenter: na
author: barclayn
manager: rkarlin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: barclayn
ms.openlocfilehash: 7558ad2e5a4404db140daff89171af92c6072ef6
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73805982"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Melhores práticas de segurança para cargas de trabalho IaaS no Azure
Este artigo descreve as práticas recomendadas de segurança para VMs e sistemas operacionais.

As práticas recomendadas baseiam-se em um consenso de opinião e funcionam com os recursos atuais da plataforma Azure e os conjuntos de recursos. Como as opiniões e as tecnologias podem mudar ao longo do tempo, este artigo será atualizado para refletir essas alterações.

Na maioria dos cenários de IaaS (infraestrutura como serviço), as [VMS (máquinas virtuais) do Azure](/azure/virtual-machines/) são a carga de trabalho principal para organizações que usam a computação em nuvem. Esse fato é evidente em [cenários híbridos](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) em que as organizações desejam migrar lentamente cargas de trabalho para a nuvem. Nesses cenários, siga as [Considerações gerais de segurança para IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)e aplique as práticas recomendadas de segurança a todas as suas VMs.

## <a name="protect-vms-by-using-authentication-and-access-control"></a>Proteger VMs usando o controle de acesso e autenticação
A primeira etapa na proteção de suas VMs é garantir que somente usuários autorizados possam configurar novas VMs e acessar VMs.

> [!NOTE]
> Para melhorar a segurança das VMs do Linux no Azure, você pode integrar com a autenticação do Azure AD. Quando você usa [a autenticação do Azure ad para VMs do Linux](/azure/virtual-machines/linux/login-using-aad), controla e aplica centralmente políticas que permitem ou negam o acesso às VMs.
>
>

**Prática recomendada**: controlar o acesso à VM.   
**Detalhe**: use [as políticas do Azure](/azure/azure-policy/azure-policy-introduction) para estabelecer convenções para recursos em sua organização e criar políticas personalizadas. Aplique essas políticas a recursos, como [grupos de recursos](/azure/azure-resource-manager/resource-group-overview). As VMs que pertencem a um grupo de recursos herdam suas políticas.

Se sua organização tiver muitas assinaturas, talvez seja necessário uma maneira de gerenciar o acesso, as políticas e a conformidade com eficiência para essas assinaturas. Os [grupos de gerenciamento do Azure](/azure/azure-resource-manager/management-groups-overview) fornecem um nível de escopo acima das assinaturas. Você organiza as assinaturas em grupos de gerenciamento (contêineres) e aplica suas condições de governança a esses grupos. Todas as assinaturas em um grupo de gerenciamento herdam automaticamente as condições aplicadas ao grupo. Os grupos de gestão dão-lhe capacidades de gestão de nível empresarial em grande escala, independentemente do seu tipo de subscrição.

**Prática recomendada**: Reduza a variabilidade em sua configuração e implantação de VMs.   
**Detalhe**: use modelos de [Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates) para reforçar suas escolhas de implantação e facilitar a compreensão e o inventário das VMs em seu ambiente.

**Prática recomendada**: acesso com privilégios seguros.   
**Detalhe**: Use uma [abordagem de privilégios mínimos](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) e funções internas do Azure para permitir que os usuários acessem e configurem VMs:

- [Colaborador da máquina virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor): pode gerenciar VMS, mas não a rede virtual ou a conta de armazenamento à qual elas estão conectadas.
- [Colaborador de máquina virtual clássica](../../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor): pode gerenciar VMs criadas usando o modelo de implantação clássico, mas não a rede virtual ou a conta de armazenamento à qual as VMs estão conectadas.
- [Administrador de segurança](../../role-based-access-control/built-in-roles.md#security-admin): somente na central de segurança: pode exibir políticas de segurança, exibir Estados de segurança, editar políticas de segurança, exibir alertas e recomendações, ignorar alertas e recomendações.
- [Usuário do DevTest Labs](../../role-based-access-control/built-in-roles.md#devtest-labs-user): pode exibir tudo e conectar, iniciar, reiniciar e desligar VMS.

Seus administradores de assinatura e coadministradores podem alterar essa configuração, tornando-os administradores de todas as VMs em uma assinatura. Certifique-se de confiar em todos os administradores de assinatura e coadministradores para fazer logon em qualquer um de seus computadores.

> [!NOTE]
> Recomendamos que você consolide VMs com o mesmo ciclo de vida no mesmo grupo de recursos. Usando grupos de recursos, você pode implantar, monitorar e acumular custos de cobrança para seus recursos.
>
>

As organizações que controlam o acesso à VM e a instalação melhoram a segurança geral da VM.

## <a name="use-multiple-vms-for-better-availability"></a>Use várias VMs para melhorar a disponibilidade
Se sua VM executar aplicativos críticos que precisam ter alta disponibilidade, é altamente recomendável que você use várias VMs. Para obter melhor disponibilidade, use um [conjunto de disponibilidade](../../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) ou [zonas](../../availability-zones/az-overview.md)de disponibilidade.

Um conjunto de disponibilidade é um agrupamento lógico que você pode usar no Azure para garantir que os recursos de VM colocados dentro dele sejam isolados uns dos outros quando forem implantados em um datacenter do Azure. O Azure garante que as VMs colocadas em um conjunto de disponibilidade sejam executadas em vários servidores físicos, racks de computação, unidades de armazenamento e comutadores de rede. Se ocorrer uma falha de hardware ou de software do Azure, apenas um subconjunto de suas VMs será afetado e seu aplicativo geral continuará disponível para seus clientes. Os conjuntos de disponibilidade são uma funcionalidade essencial quando você deseja criar soluções de nuvem confiáveis.

## <a name="protect-against-malware"></a>Proteja-se contra o software maligno
Você deve instalar a proteção antimalware para ajudar a identificar e remover vírus, spyware e outros softwares mal-intencionados. Você pode instalar o [Microsoft Antimalware](antimalware.md) ou uma solução de proteção de ponto de extremidade do parceiro da Microsoft ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://www.microsoft.com/windows/comprehensive-security)e [System Center Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)).

O Microsoft Antimalware inclui recursos como proteção em tempo real, verificação agendada, correção de malware, atualizações de assinatura, atualizações de mecanismo, relatórios de amostras e coleta de eventos de exclusão. Para ambientes hospedados separadamente do seu ambiente de produção, você pode usar uma extensão antimalware para ajudar a proteger suas VMs e serviços de nuvem.

Você pode integrar o Microsoft antimalware e as soluções de parceiros à [central de segurança do Azure](../../security-center/index.yml) para facilitar a implantação e detecções internas (alertas e incidentes).

**Prática recomendada**: instalar uma solução antimalware para proteger contra malware.   
**Detalhe**: [instalar uma solução de parceiro da Microsoft ou Microsoft Antimalware](../../security-center/security-center-install-endpoint-protection.md)

**Prática recomendada**: integre sua solução antimalware com a central de segurança para monitorar o status da sua proteção.   
**Detalhe**: [gerenciar problemas do Endpoint Protection com a central de segurança](../../security-center/security-center-partner-integration.md)

## <a name="manage-your-vm-updates"></a>Gerenciar suas atualizações de VM
As VMs do Azure, como todas as VMs locais, devem ser gerenciadas pelo usuário. O Azure não envia por push atualizações do Windows para eles. Você precisa gerenciar as atualizações da VM.

**Prática recomendada**: Mantenha suas VMs atuais.   
**Detalhe**: Use a solução [Gerenciamento de atualizações](../../automation/automation-update-management.md) na automação do Azure para gerenciar atualizações do sistema operacional para seus computadores Windows e Linux implantados no Azure, em ambientes locais ou em outros provedores de nuvem. Pode rapidamente avaliar o estado das atualizações disponíveis em todos os computadores agente e gerir o processo de instalação de atualizações necessárias para os servidores.

Os computadores gerenciados pelo Gerenciamento de Atualizações usam as seguintes configurações para executar implantações de avaliação e atualização:

- Microsoft Monitoring Agent (MMA) para Windows ou Linux
- Configuração de Estado Pretendido do PowerShell (DSC) para Linux
- Função de Trabalho de Runbook Híbrida da Automatização
- Microsoft Update ou Windows Server Update Services (WSUS) para computadores Windows

Se você usar Windows Update, deixe a configuração de Windows Update automática habilitada.

**Prática recomendada**: garanta a implantação de que as imagens que você criou incluem a rodada mais recente de atualizações do Windows.   
**Detalhe**: Verifique e instale todas as atualizações do Windows como uma primeira etapa de cada implantação. Essa medida é especialmente importante para se aplicar ao implantar imagens provenientes de você ou de sua própria biblioteca. Embora as imagens do Azure Marketplace sejam atualizadas automaticamente por padrão, pode haver um tempo de retardo (até algumas semanas) após uma versão pública.

**Prática recomendada**: reimplante periodicamente suas VMs para forçar uma nova versão do sistema operacional.   
**Detalhe**: defina sua VM com um [modelo de Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) para que você possa reimplantá-lo facilmente. O uso de um modelo fornece uma VM com patch e segurança quando você precisa dela.

**Prática recomendada**: aplicar rapidamente atualizações de segurança às VMs.   
**Detalhe**: habilitar a central de segurança do Azure (camada gratuita ou Standard) para [identificar atualizações de segurança ausentes e aplicá-las](../../security-center/security-center-apply-system-updates.md).

**Prática recomendada**: Instale as atualizações de segurança mais recentes.   
**Detalhe**: algumas das primeiras cargas de trabalho que os clientes movem para o Azure são laboratórios e sistemas de face externa. Se suas VMs do Azure hospedam aplicativos ou serviços que precisam ser acessíveis à Internet, esteja atento a patches. Patch além do sistema operacional. Vulnerabilidades sem patch em aplicativos de parceiros também podem levar a problemas que podem ser evitados se um bom gerenciamento de patches estiver em vigor.

**Prática recomendada**: implantar e testar uma solução de backup.   
**Detalhe**: um backup precisa ser tratado da mesma maneira que você lida com qualquer outra operação. Isso é verdadeiro para sistemas que fazem parte de seu ambiente de produção estendendo para a nuvem.

Os sistemas de teste e desenvolvimento devem seguir as estratégias de backup que fornecem recursos de restauração que são semelhantes ao que os usuários cresceram acostumados, com base em sua experiência com ambientes locais. As cargas de trabalho de produção movidas para o Azure devem se integrar a soluções de backup existentes, quando possível. Ou, você pode usar o [backup do Azure](../../backup/backup-azure-vms-first-look-arm.md) para ajudar a atender aos seus requisitos de backup.

As organizações que não impõem políticas de atualização de software são mais expostas a ameaças que exploram vulnerabilidades conhecidas e fixadas anteriormente. Para estar em conformidade com as normas do setor, as empresas devem provar que são uma prova e usando os controles de segurança corretos para ajudar a garantir a segurança de suas cargas de trabalho localizadas na nuvem.

As práticas recomendadas de atualização de software para um datacenter tradicional e o Azure IaaS têm muitas semelhanças. Recomendamos que você avalie suas políticas de atualização de software atuais para incluir as VMs localizadas no Azure.

## <a name="manage-your-vm-security-posture"></a>Gerencie sua postura de segurança de VM
Ameaças cibernéticas estão em evolução. Proteger suas VMs requer um recurso de monitoramento que pode detectar ameaças rapidamente, impedir o acesso não autorizado aos seus recursos, disparar alertas e reduzir falsos positivos.

Para monitorar a postura de segurança de suas [VMs](../../security-center/security-center-linux-virtual-machine.md) [Windows](../../security-center/security-center-virtual-machine.md) e Linux, use a [central de segurança do Azure](../../security-center/security-center-intro.md). Na central de segurança, proteja suas VMs tirando proveito dos seguintes recursos:

- Aplique as configurações de segurança do sistema operacional com as regras de configuração recomendadas.
- Identifique e baixe a segurança do sistema e as atualizações críticas que podem estar ausentes.
- Implantar recomendações para proteção antimalware de ponto de extremidade.
- Validar a criptografia de disco.
- Avaliar e corrigir vulnerabilidades.
- Detectar ameaças.

A central de segurança pode monitorar as ameaças ativamente e as possíveis ameaças são expostas em alertas de segurança. As ameaças correlacionadas são agregadas em uma única exibição chamada incidente de segurança.

A central de segurança armazena dados em [logs de Azure monitor](/azure/log-analytics/log-analytics-overview). Os logs de Azure Monitor fornecem uma linguagem de consulta e um mecanismo de análise que fornece informações sobre a operação de seus aplicativos e recursos. Os dados também são coletados de [Azure monitor](../../batch/monitoring-overview.md), soluções de gerenciamento e agentes instalados em máquinas virtuais na nuvem ou no local. Esta funcionalidade partilhada ajuda-o a formar uma visão geral do seu ambiente.

As organizações que não impõem segurança forte para suas VMs permanecem cientes de possíveis tentativas por usuários não autorizados de burlar os controles de segurança.

## <a name="monitor-vm-performance"></a>Monitorar o desempenho da VM
O abuso de recursos pode ser um problema quando processos de VM consomem mais recursos do que deveriam. Problemas de desempenho com uma VM podem levar à interrupção do serviço, o que viola o princípio de segurança de disponibilidade. Isso é particularmente importante para VMs que hospedam o IIS ou outros servidores Web, pois alta utilização de CPU ou memória pode indicar um ataque de DoS (negação de serviço). É imperativo monitorar o acesso à VM não apenas reativamente enquanto um problema está ocorrendo, mas também proativamente contra o desempenho da linha de base conforme medido durante a operação normal.

Recomendamos que você use [Azure monitor](/azure/monitoring-and-diagnostics/monitoring-overview-metrics) para obter visibilidade da integridade do recurso. Recursos de Azure Monitor:

- [Arquivos de log de diagnóstico de recurso](../../azure-monitor/platform/resource-logs-overview.md): monitora os recursos da VM e identifica possíveis problemas que podem comprometer o desempenho e a disponibilidade.
- [Extensão de diagnóstico do Azure](/azure/azure-monitor/platform/diagnostics-extension-overview): fornece recursos de monitoramento e diagnóstico em VMs do Windows. Você pode habilitar esses recursos incluindo a extensão como parte do modelo de [Azure Resource Manager](/azure/virtual-machines/windows/extensions-diagnostics-template).

As organizações que não monitoram o desempenho da VM não podem determinar se determinadas alterações nos padrões de desempenho são normais ou anormais. Uma VM que está consumindo mais recursos do que o normal pode indicar um ataque de um recurso externo ou um processo comprometido em execução na VM.

## <a name="encrypt-your-virtual-hard-disk-files"></a>Criptografar seus arquivos de disco rígido virtual
Recomendamos que você criptografe seus VHDs (discos rígidos virtuais) para ajudar a proteger o volume de inicialização e os volumes de dados em repouso no armazenamento, juntamente com suas chaves de criptografia e segredos.

[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) ajuda a criptografar seus discos de máquina virtual IaaS Windows e Linux. O Azure Disk Encryption usa o recurso [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) padrão do setor do Windows e o recurso [DM-cript](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer criptografia de volume para o sistema operacional e os discos de dados. A solução é integrada com [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para ajudá-lo a controlar e gerenciar as chaves de criptografia de disco e os segredos em sua assinatura do cofre de chaves. A solução também garante que todos os dados nos discos da máquina virtual sejam criptografados em repouso no armazenamento do Azure.

A seguir estão as práticas recomendadas para usar Azure Disk Encryption:

**Prática recomendada**: habilitar a criptografia em VMS.   
**Detalhe**: Azure Disk Encryption gera e grava as chaves de criptografia em seu cofre de chaves. O gerenciamento de chaves de criptografia em seu cofre de chaves requer a autenticação do Azure AD. Crie um aplicativo do Azure AD para essa finalidade. Para fins de autenticação, você pode usar a autenticação baseada em segredo do cliente ou a [autenticação do Azure ad baseada em certificado do cliente](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

**Prática recomendada**: Use uma chave de criptografia de chave (Kek) para obter uma camada adicional de segurança para chaves de criptografia. Adicione um KEK ao cofre de chaves.   
**Detalhe**: Use o cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) para criar uma chave de criptografia de chave no cofre de chaves. Você também pode importar um KEK do seu HSM (módulo de segurança de hardware) local para o gerenciamento de chaves. Para obter mais informações, consulte a [documentação do Key Vault](../../key-vault/key-vault-hsm-protected-keys.md). Quando uma chave de criptografia de chave é especificada, o Azure Disk Encryption usa essa chave para encapsular os segredos de criptografia antes de gravar em Key Vault. Manter uma cópia de caução dessa chave em um HSM de gerenciamento de chaves local oferece proteção adicional contra a exclusão acidental de chaves.

**Prática recomendada**: faça um [instantâneo](../../virtual-machines/windows/snapshot-copy-managed-disk.md) e/ou backup antes que os discos sejam criptografados. Os backups fornecem uma opção de recuperação se ocorrer uma falha inesperada durante a criptografia.   
**Detalhe**: as VMs com discos gerenciados exigem um backup antes que a criptografia ocorra. Depois que um backup é feito, você pode usar o cmdlet **set-AzVMDiskEncryptionExtension** para criptografar discos gerenciados especificando o parâmetro *-skipVmBackup* . Para obter mais informações sobre como fazer backup e restaurar VMs criptografadas, consulte o artigo [backup do Azure](../../backup/backup-azure-vms-encryption.md) .

**Prática recomendada**: para garantir que os segredos de criptografia não entrem em limites regionais, Azure Disk Encryption precisa que o cofre de chaves e as VMs estejam localizados na mesma região.   
**Detalhe**: Crie e use um cofre de chaves que esteja na mesma região que a VM a ser criptografada.

Ao aplicar Azure Disk Encryption, você pode atender às seguintes necessidades comerciais:

- As VMs de IaaS são protegidas em repouso por meio da tecnologia de criptografia padrão da indústria para atender aos requisitos de conformidade e segurança organizacional.
- As VMs de IaaS iniciam em chaves e políticas controladas pelo cliente, e você pode auditar seu uso em seu cofre de chaves.

## <a name="restrict-direct-internet-connectivity"></a>Restringir a conectividade direta com a Internet
Monitore e restrinja a conectividade direta com a Internet da VM. Os invasores examinam constantemente intervalos de IP de nuvem pública para portas de gerenciamento aberto e tentam ataques "fáceis", como senhas comuns e vulnerabilidades conhecidas sem patches. A tabela a seguir lista as práticas recomendadas para ajudar a proteger contra esses ataques:

**Prática recomendada**: evitar a exposição inadvertida ao roteamento de rede e à segurança.   
**Detalhe**: Use o RBAC para garantir que apenas o grupo de rede central tenha permissão para recursos de rede.

**Prática recomendada**: identificar e corrigir VMs expostas que permitem o acesso de "qualquer" endereço IP de origem.   
**Detalhe**: Use a central de segurança do Azure. A central de segurança recomendará que você restrinja o acesso por meio de pontos de extremidade voltados para a Internet se qualquer um dos grupos de segurança de rede tiver uma ou mais regras de entrada que permitam o acesso de "qualquer" endereço IP de origem. A central de segurança recomendará que você edite essas regras de entrada para [restringir o acesso](../../security-center/security-center-network-recommendations.md) a endereços IP de origem que realmente precisam de acesso.

**Prática recomendada**: restringir as portas de gerenciamento (RDP, SSH).   
**Detalhe**: o [acesso à VM just-in-time (JIT)](../../security-center/security-center-just-in-time.md) pode ser usado para bloquear o tráfego de entrada para suas VMs do Azure, reduzindo a exposição a ataques e, ao mesmo tempo, fornecendo acesso fácil para se conectar às VMs quando necessário. Quando o JIT está habilitado, a central de segurança bloqueia o tráfego de entrada para suas VMs do Azure criando uma regra de grupo de segurança de rede. Você seleciona as portas na VM para a qual o tráfego de entrada será bloqueado. Essas portas são controladas pela solução JIT.

## <a name="next-steps"></a>Passos seguintes
Veja [padrões e práticas recomendadas de segurança do Azure](best-practices-and-patterns.md) para obter mais práticas recomendadas de segurança para usar ao projetar, implantar e gerenciar suas soluções de nuvem usando o Azure.

Os recursos a seguir estão disponíveis para fornecer informações mais gerais sobre segurança do Azure e serviços da Microsoft relacionados:
* [Blog da equipe de segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – para obter informações atualizadas sobre o mais recente na segurança do Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – em que vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, podem ser relatadas ou enviadas por email para secure@microsoft.com
