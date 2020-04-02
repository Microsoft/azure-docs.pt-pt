---
title: Boas práticas de segurança para cargas de trabalho iaaS em Azure Microsoft Docs
description: " A migração de cargas de trabalho para o Azure IaaS traz oportunidades para reavaliar os nossos projetos "
services: security
documentationcenter: na
author: terrylanfear
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
ms.author: terrylan
ms.openlocfilehash: fadf07f312c86f8ca15f5a97ebbe99e84bcffc89
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548232"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Melhores práticas de segurança para cargas de trabalho IaaS no Azure
Este artigo descreve as melhores práticas de segurança para VMs e sistemas operativos.

As melhores práticas baseiam-se num consenso de opinião, e funcionam com as capacidades atuais da plataforma Azure e conjuntos de funcionalidades. Como as opiniões e as tecnologias podem mudar ao longo do tempo, este artigo será atualizado para refletir essas mudanças.

Na maioria das infraestruturas como cenários de serviço (IaaS), as [máquinas virtuais Azure (VMs)](/azure/virtual-machines/) são a principal carga de trabalho para organizações que usam computação em nuvem. Este facto é evidente em [cenários híbridos](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) onde as organizações querem migrar lentamente cargas de trabalho para a nuvem. Nestes cenários, siga as considerações gerais de segurança para o [IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)e aplique as melhores práticas de segurança a todos os seus VMs.

## <a name="protect-vms-by-using-authentication-and-access-control"></a>Proteja os VMs utilizando a autenticação e o controlo de acesso
O primeiro passo para proteger os seus VMs é garantir que apenas os utilizadores autorizados podem configurar novos VMs e aceder a VMs.

> [!NOTE]
> Para melhorar a segurança dos VMs Linux no Azure, pode integrar-se com a autenticação Azure AD. Quando utiliza a [autenticação Azure AD para VMs Linux,](/azure/virtual-machines/linux/login-using-aad)controla e aplica políticas que permitam ou negam o acesso aos VMs.
>
>

**Boas práticas**: Controlar o acesso vM.   
**Detalhe**: Utilize [as políticas do Azure](/azure/azure-policy/azure-policy-introduction) para estabelecer convenções de recursos na sua organização e criar políticas personalizadas. Aplicar estas políticas a recursos, como [grupos de recursos.](/azure/azure-resource-manager/resource-group-overview) VMs que pertencem a um grupo de recursos herdam as suas políticas.

Se a sua organização tiver muitas subscrições, poderá precisar de uma forma de gerir o acesso, as políticas e a conformidade dessas subscrições. Os grupos de [gestão Azure](/azure/azure-resource-manager/management-groups-overview) fornecem um nível de âmbito acima das subscrições. Organiza subscrições em grupos de gestão (contentores) e aplica as suas condições de governação a esses grupos. Todas as subscrições dentro de um grupo de gestão herdam automaticamente as condições aplicadas ao grupo. Os grupos de gestão dão-lhe capacidades de gestão de nível empresarial em grande escala, independentemente do seu tipo de subscrição.

**Boas práticas**: Reduza a variabilidade na sua configuração e implantação de VMs.   
**Detalhe**: Utilize os modelos do Gestor de [Recursos Azure](/azure/azure-resource-manager/resource-group-authoring-templates) para fortalecer as suas escolhas de implementação e facilitar a compreensão e inventário dos VMs no seu ambiente.

**Boas práticas**: Acesso privilegiado seguro.   
**Detalhe**: Utilize uma [abordagem menos privilegiada](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) e funções azure incorporadas para permitir aos utilizadores aceder e configurar VMs:

- [Colaborador de Máquinavirtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor): Pode gerir VMs, mas não a rede virtual ou conta de armazenamento a que estão ligados.
- [Classic Virtual Machine Contributor](../../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor): Pode gerir VMs criados utilizando o modelo de implementação clássico, mas não a rede virtual ou conta de armazenamento a que os VMs estão ligados.
- [Segurança :](../../role-based-access-control/built-in-roles.md#security-admin)Apenas no Centro de Segurança: Pode ver políticas de segurança, ver estados de segurança, editar políticas de segurança, ver alertas e recomendações, descartar alertas e recomendações.
- Utilizador de [Laboratórios DevTest:](../../role-based-access-control/built-in-roles.md#devtest-labs-user)Pode ver tudo e ligar, iniciar, reiniciar e desligar VMs.

Os seus administradores de subscrição e coadminos podem alterar esta definição, tornando-os administradores de todos os VMs numa subscrição. Certifique-se de que confia em todos os seus administradores de subscrição e coadminos para iniciar sessão em qualquer uma das suas máquinas.

> [!NOTE]
> Recomendamos que consolide VMs com o mesmo ciclo de vida no mesmo grupo de recursos. Ao utilizar grupos de recursos, pode implementar, monitorizar e aumentar os custos de faturação dos seus recursos.
>
>

As organizações que controlam o acesso e a configuração vm melhoram a sua segurança geral em VM.

## <a name="use-multiple-vms-for-better-availability"></a>Use vários VMs para uma melhor disponibilidade
Se o seu VM executa aplicações críticas que precisam de ter alta disponibilidade, recomendamos vivamente que utilize vários VMs. Para uma melhor disponibilidade, utilize um conjunto de [disponibilidade](../../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) ou [zonas](../../availability-zones/az-overview.md)de disponibilidade.

Um conjunto de disponibilidade é um agrupamento lógico que você pode usar em Azure para garantir que os recursos VM que você coloca dentro dele são isolados uns dos outros quando são implantados em um datacenter Azure. O Azure garante que os VMs que coloca num conjunto de disponibilidade seleção de vários servidores físicos, racks de cálculo, unidades de armazenamento e comutadores de rede. Se ocorrer uma falha de hardware ou software Azure, apenas um subconjunto dos seus VMs é afetado, e a sua aplicação global continua disponível para os seus clientes. Os conjuntos de disponibilidade são uma capacidade essencial quando se quer construir soluções de nuvem fiáveis.

## <a name="protect-against-malware"></a>Proteção contra software maligno
Deve instalar proteção antimalware para ajudar a identificar e remover vírus, spyware e outros softwares maliciosos. Pode instalar o [Microsoft Antimalware](antimalware.md) ou a solução de proteção de pontofinal do parceiro Microsoft ([Trend Micro,](https://help.deepsecurity.trendmicro.com/Welcome.html) [Broadcom,](https://www.broadcom.com/products) [McAfee,](https://www.mcafee.com/us/products.aspx) [Windows Defender](https://www.microsoft.com/windows/comprehensive-security)e System [Center Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)).

O Microsoft Antimalware inclui funcionalidades como proteção em tempo real, digitalização programada, reparação de malware, atualizações de assinaturas, atualizações do motor, relatórios de amostras e recolha de eventos de exclusão. Para ambientes que estão alojados separadamente do seu ambiente de produção, você pode usar uma extensão antimalware para ajudar a proteger seus VMs e serviços na nuvem.

Pode integrar soluções de Antimalware da Microsoft e parceiros com [o Azure Security Center](../../security-center/index.yml) para facilitar a implementação e deteções incorporadas (alertas e incidentes).

**Boas práticas**: Instale uma solução antimalware para proteger contra malware.   
**Detalhe**: [Instale uma solução de parceiro da Microsoft ou O Antimalware da Microsoft](../../security-center/security-center-install-endpoint-protection.md)

**Boas práticas**: Integre a sua solução antimalware com o Centro de Segurança para monitorizar o estado da sua proteção.   
**Detalhe**: Gerir problemas de [proteção de pontos finais com o Centro](../../security-center/security-center-partner-integration.md) de Segurança

## <a name="manage-your-vm-updates"></a>Gerencie as atualizações vM
Os VMs azure, como todos os VMs no local, destinam-se a ser geridos pelo utilizador. O Azure não emite atualizações do Windows para as VMs. Tens de gerir as atualizações do VM.

**Boas práticas**: Mantenha os vMs atuais.   
**Detalhe**: Utilize a solução [de Gestão](../../automation/automation-update-management.md) de Atualizações na Automatização Azure para gerir as atualizações do sistema operativo para os computadores Windows e Linux que são implantados no Azure, em ambientes no local ou noutros fornecedores de nuvem. Pode rapidamente avaliar o estado das atualizações disponíveis em todos os computadores agente e gerir o processo de instalação de atualizações necessárias para os servidores.

Os computadores que são geridos pela Gestão de Atualizações utilizam as seguintes configurações para realizar a avaliação e as implementações de atualização:

- Agente de Monitorização da Microsoft (MMA) para Windows ou Linux
- Configuração de Estado Pretendido do PowerShell (DSC) para Linux
- Função de Trabalho de Runbook Híbrida da Automatização
- Microsoft Update ou Windows Server Update Services (WSUS) para computadores Windows

Se utilizar o Windows Update, deixe ativada a definição automática de Atualização do Windows.

**Boas práticas**: Certifique-se de que as imagens que construiu incluem a mais recente ronda de atualizações do Windows.   
**Detalhe**: Verifique e instale todas as atualizações do Windows como um primeiro passo de cada implementação. Esta medida é especialmente importante para aplicar quando implementa imagens que vêm de si ou da sua própria biblioteca. Embora as imagens do Azure Marketplace sejam atualizadas automaticamente por padrão, pode haver um tempo de atraso (até algumas semanas) após um lançamento público.

**Boas práticas**: Recoloque periodicamente os seus VMs para forçar uma nova versão do Sistema Operativo.   
**Detalhe**: Defina o seu VM com um modelo de Gestor de [Recursos Azure](../../azure-resource-manager/templates/template-syntax.md) para que possa reimplantá-lo facilmente. Usar um modelo dá-lhe um VM remendado e seguro quando precisa.

**Boas práticas**: Aplique rapidamente atualizações de segurança para VMs.   
**Detalhe**: Habilitar o Centro de Segurança Azure (nível gratuito ou nível Standard) para identificar as atualizações de [segurança em falta e aplicá-las](../../security-center/security-center-apply-system-updates.md).

**Boas práticas**: Instale as últimas atualizações de segurança.   
**Detalhe**: Algumas das primeiras cargas de trabalho que os clientes mudam para o Azure são laboratórios e sistemas externos. Se os seus VMs Azure hospedarem aplicações ou serviços que precisam de ser acessíveis à internet, esteja atento ao remendo. Remende para além do sistema operativo. Vulnerabilidades não corrigidas em aplicações parceiras também podem levar a problemas que podem ser evitados se houver uma boa gestão de patchs.

**Boas práticas**: Implementar e testar uma solução de backup.   
**Detalhe**: Uma cópia de segurança precisa de ser manuseada da mesma forma que lida com qualquer outra operação. Isto é verdade em sistemas que fazem parte do seu ambiente de produção que se estende até à nuvem.

Os sistemas de teste e dev devem seguir estratégias de backup que fornecem capacidades de restauro semelhantes às a que os utilizadores se habituaram, com base na sua experiência com ambientes no local. As cargas de trabalho de produção transferidas para o Azure devem integrar-se com as soluções de backup existentes, quando possível. Ou pode utilizar [o Azure Backup](../../backup/backup-azure-vms-first-look-arm.md) para ajudar a responder aos seus requisitos de backup.

As organizações que não aplicam políticas de atualização de software estão mais expostas a ameaças que exploram vulnerabilidades conhecidas e previamente fixas. Para cumprir os regulamentos do setor, as empresas devem provar que são diligentes e usam controlos de segurança corretos para ajudar a garantir a segurança das suas cargas de trabalho localizadas na nuvem.

As melhores práticas de atualização de software para um datacenter tradicional e O Azure IaaS têm muitas semelhanças. Recomendamos que avalie as suas políticas atuais de atualização de software para incluir VMs localizados no Azure.

## <a name="manage-your-vm-security-posture"></a>Gerencie a sua postura de segurança VM
As ameaças cibernéticas estão a evoluir. A salvaguarda dos seus VMs requer uma capacidade de monitorização que possa detetar rapidamente ameaças, impedir o acesso não autorizado aos seus recursos, desencadear alertas e reduzir falsos positivos.

Para monitorizar a postura de segurança dos seus [VMs Windows](../../security-center/security-center-virtual-machine.md) e [Linux,](../../security-center/security-center-linux-virtual-machine.md)utilize o [Azure Security Center](../../security-center/security-center-intro.md). No Centro de Segurança, proteja os seus VMs tirando partido das seguintes capacidades:

- Aplicar as definições de segurança do OS com as regras de configuração recomendadas.
- Identifique e descarregue a segurança do sistema e atualizações críticas que possam estar em falta.
- Implemente recomendações para a proteção antimalware de ponto final.
- Validar a encriptação do disco.
- Avaliar e remediar vulnerabilidades.
- Detete ameaças.

O Centro de Segurança pode monitorizar ativamente as ameaças, e potenciais ameaças são expostas em alertas de segurança. Ameaças correlacionadas são agregadas numa única visão chamada incidente de segurança.

O Security Center armazena dados em [registos do Monitor Azure.](/azure/log-analytics/log-analytics-overview) Os registos do Azure Monitor fornecem um motor de linguagem e análise de consulta que lhe dá informações sobre o funcionamento das suas aplicações e recursos. Os dados são também recolhidos do [Azure Monitor,](../../batch/monitoring-overview.md)soluções de gestão e agentes instalados em máquinas virtuais na nuvem ou no local. Esta funcionalidade partilhada ajuda-o a formar uma visão geral do seu ambiente.

As organizações que não impõem uma segurança forte para os seus VMs continuam desconhecendo possíveis tentativas de utilizadores não autorizados para contornar os controlos de segurança.

## <a name="monitor-vm-performance"></a>Monitor desempenho VM
O abuso de recursos pode ser um problema quando os processos de VM consomem mais recursos do que deviam. Problemas de desempenho com um VM podem levar a perturbações no serviço, o que viola o princípio de segurança da disponibilidade. Isto é particularmente importante para os VMs que estão hospedando IIS ou outros servidores web, porque o alto CPU ou uso de memória pode indicar um ataque de negação de serviço (DoS). É imperativo monitorizar o acesso vm não só de forma reativa enquanto ocorre um problema, mas também proactivamente contra o desempenho da linha de base medido durante o funcionamento normal.

Recomendamos que utilize o [Monitor Azure](/azure/monitoring-and-diagnostics/monitoring-overview-metrics) para ganhar visibilidade na saúde do seu recurso. Características do Monitor Azure:

- [Ficheiros](../../azure-monitor/platform/platform-logs-overview.md)de registo de diagnóstico de recursos : Monitoriza os seus recursos VM e identifica potenciais problemas que podem comprometer o desempenho e a disponibilidade.
- [Extensão de Diagnóstico Azure](/azure/azure-monitor/platform/diagnostics-extension-overview): Fornece capacidades de monitorização e diagnóstico nos VMs do Windows. Pode ativar estas capacidades, incluindo a extensão como parte do modelo do Gestor de [Recursos Azure](/azure/virtual-machines/windows/extensions-diagnostics-template).

Organizações que não monitorizam o desempenho da VM não conseguem determinar se certas mudanças nos padrões de desempenho são normais ou anormais. Um VM que está a consumir mais recursos do que o normal pode indicar um ataque de um recurso externo ou um processo comprometido a decorrer no VM.

## <a name="encrypt-your-virtual-hard-disk-files"></a>Criptografe os seus ficheiros de disco rígido virtual
Recomendamos que criptografe os seus discos rígidos virtuais (VHDs) para ajudar a proteger o volume de arranque e os volumes de dados em repouso no armazenamento, juntamente com as suas chaves e segredos de encriptação.

[A encriptação](../azure-security-disk-encryption-overview.md) do disco Azure ajuda-o a encriptar os discos de máquinavirtual Windows e Linux IaaS. A Encriptação do Disco Azure utiliza a funcionalidade [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) padrão da indústria do Windows e a funcionalidade [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer encriptação de volume para o OS e os discos de dados. A solução está integrada com o [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para ajudá-lo a controlar e gerir as chaves e segredos de encriptação de discos na subscrição do cofre chave. A solução também garante que todos os dados dos discos da máquina virtual são encriptados em repouso no Armazenamento Azure.

Seguem-se as melhores práticas para a utilização da encriptação do disco Azure:

**Boas práticas**: Ativar a encriptação em VMs.   
**Detalhe**: A encriptação do disco azure gera e escreve as chaves de encriptação do seu cofre chave. Gerir chaves de encriptação no seu cofre de chaves requer autenticação Azure AD. Crie uma aplicação Azure AD para o efeito. Para efeitos de autenticação, pode utilizar a autenticação baseada em segredo do cliente ou a [autenticação Azure AD baseada em certificadode cliente.](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)

**Boas práticas**: Utilize uma chave de encriptação (KEK) para uma camada adicional de segurança para chaves de encriptação. Adicione um KEK ao seu cofre chave.   
**Detalhe**: Utilize o cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) para criar uma chave de encriptação no cofre da chave. Também pode importar um KEK do seu módulo de segurança de hardware no local (HSM) para gestão de chaves. Para mais informações, consulte a documentação do [Cofre chave.](../../key-vault/key-vault-hsm-protected-keys.md) Quando uma chave de encriptação é especificada, a Encriptação do Disco Azure usa essa chave para embrulhar os segredos de encriptação antes de escrever para key vault. Manter uma cópia desta chave numa gestão chave no local, o HSM oferece proteção adicional contra a supressão acidental de chaves.

**Boas práticas**: Tire uma [fotografia](../../virtual-machines/windows/snapshot-copy-managed-disk.md) e/ou cópia de segurança antes de os discos estarem encriptados. As cópias de segurança fornecem uma opção de recuperação se uma falha inesperada ocorrer durante a encriptação.   
**Detalhe**: Os VMs com discos geridos requerem uma cópia de segurança antes da encriptação ocorrer. Depois de ser feita uma cópia de segurança, pode utilizar o cmdlet **set-AzVMDiskEncryptionExtension** para encriptar discos geridos especificando o parâmetro *-skipVmBackup.* Para obter mais informações sobre como fazer backup e restaurar VMs encriptados, consulte o artigo [Azure Backup.](../../backup/backup-azure-vms-encryption.md)

**Boas práticas**: Para garantir que os segredos de encriptação não ultrapassem os limites regionais, a Encriptação do Disco Azure precisa do cofre chave e dos VMs para estar localizado na mesma região.   
**Detalhe**: Crie e utilize um cofre chave que se encontra na mesma região que o VM para ser encriptado.

Quando aplicar encriptação de disco azure, pode satisfazer as seguintes necessidades de negócio:

- Os VMs IaaS são protegidos em repouso através de tecnologia de encriptação padrão da indústria para responder aos requisitos de segurança organizacional e conformidade.
- Os VMs IaaS começam com chaves e políticas controladas pelo cliente, e você pode auditar o seu uso no seu cofre chave.

## <a name="restrict-direct-internet-connectivity"></a>Restringir a conectividade direta da Internet
Monitorize e restringa a conectividade direta da Internet VM. Os atacantes procuram constantemente as gamas ip da nuvem pública para portas de gestão abertas e tentam ataques "fáceis" como senhas comuns e vulnerabilidades conhecidas sem remendar. A tabela que se segue lista as melhores práticas para ajudar a proteger contra estes ataques:

**Boas práticas**: Evitar exposição inadvertida ao encaminhamento e segurança da rede.   
**Detalhe**: Utilize o RBAC para garantir que apenas o grupo central de rede tenha permissão para os recursos de rede.

**Boas práticas**: Identificar e remediar VMs expostos que permitam o acesso a partir de "qualquer" endereço IP de origem.   
**Detalhe**: Utilize o Centro de Segurança Azure. O Security Center recomendará que restrinja o acesso através de pontos finais virados para a Internet se algum dos seus grupos de segurança de rede tiver uma ou mais regras de entrada que permitam o acesso a partir de "qualquer" endereço IP de origem. O Security Center recomendará que edite estas regras de entrada para restringir o [acesso](../../security-center/security-center-network-recommendations.md) a endereços IP de origem que realmente precisam de acesso.

**Boas práticas**: Restringir as portas de gestão (RDP, SSH).   
**Detalhe**: [O acesso VM just-in-time (JIT)](../../security-center/security-center-just-in-time.md) pode ser utilizado para bloquear o tráfego de entrada para os seus VMs Azure, reduzindo a exposição a ataques, proporcionando um fácil acesso à ligação aos VMs quando necessário. Quando o JIT está ativado, o Security Center bloqueia o tráfego de entrada para os seus VMs Azure, criando uma regra de grupo de segurança de rede. Selecione as portas do VM para as quais o tráfego de entrada será bloqueado. Estas portas são controladas pela solução JIT.

## <a name="next-steps"></a>Passos seguintes
Consulte as [melhores práticas e padrões](best-practices-and-patterns.md) de segurança azure para obter mais práticas de segurança para usar quando está a projetar, implementar e gerir as suas soluções em nuvem utilizando o Azure.

Os seguintes recursos estão disponíveis para fornecer informações mais gerais sobre segurança Azure e serviços relacionados com a Microsoft:
* [Azure Security Team Blog](https://blogs.msdn.microsoft.com/azuresecurity/) - para informações atualizadas sobre as últimas novidades em Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) - onde as vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, podem ser reportadas ou via e-mail parasecure@microsoft.com
