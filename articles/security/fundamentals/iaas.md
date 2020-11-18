---
title: Melhores práticas de segurança para cargas de trabalho iaaS em Azure Microsoft Docs
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
ms.openlocfilehash: 9b9a83cf71dfa7658c34c3c98f8d12a056adad0c
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698789"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Melhores práticas de segurança para cargas de trabalho IaaS no Azure
Este artigo descreve as melhores práticas de segurança para VMs e sistemas operativos.

As melhores práticas baseiam-se num consenso de opinião, e funcionam com as capacidades e conjuntos de funcionalidades atuais da plataforma Azure. Como as opiniões e tecnologias podem mudar ao longo do tempo, este artigo será atualizado para refletir essas mudanças.

Na maioria das infraestruturas como cenários de serviço (IaaS), [as máquinas virtuais Azure (VMs)](../../virtual-machines/index.yml) são a principal carga de trabalho para organizações que usam computação em nuvem. Este facto é evidente em [cenários híbridos](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) onde as organizações querem migrar lentamente cargas de trabalho para a nuvem. Nesses cenários, siga as [considerações gerais de segurança para o IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)e aplique as melhores práticas de segurança a todos os seus VMs.

## <a name="protect-vms-by-using-authentication-and-access-control"></a>Proteger VMs utilizando o controlo de autenticação e acesso
O primeiro passo para proteger os seus VMs é garantir que apenas os utilizadores autorizados podem configurar novos VMs e aceder a VMs.

> [!NOTE]
> Para melhorar a segurança dos VMs Linux em Azure, pode integrar-se com a autenticação AD AZure. Quando utiliza [a autenticação AZure AD para Os VMs Linux,](../../virtual-machines/linux/login-using-aad.md)controla centralmente e aplica políticas que permitem ou negam o acesso aos VMs.
>
>

**Melhores práticas**: Controle o acesso vM.   
**Detalhe**: Use [as políticas Azure](../../governance/policy/overview.md) para estabelecer convenções para recursos na sua organização e crie políticas personalizadas. Aplicar estas políticas a recursos, como [grupos de recursos.](../../azure-resource-manager/management/overview.md) Os VMs que pertencem a um grupo de recursos herdam as suas políticas.

Se a sua organização tiver muitas subscrições, poderá precisar de uma forma de gerir o acesso, as políticas e a conformidade dessas subscrições. [Os grupos de gestão Azure](../../governance/management-groups/overview.md) proporcionam um nível de âmbito acima das subscrições. Organiza subscrições em grupos de gestão (contentores) e aplica as suas condições de governação a esses grupos. Todas as subscrições dentro de um grupo de gestão herdam automaticamente as condições aplicadas ao grupo. Os grupos de gestão dão-lhe capacidades de gestão de nível empresarial em grande escala, independentemente do seu tipo de subscrição.

**Melhores práticas**: Reduza a variabilidade na sua configuração e implantação de VMs.   
**Detalhe**: Utilize modelos [do Gestor de Recursos Azure](../../azure-resource-manager/templates/template-syntax.md) para fortalecer as suas escolhas de implementação e facilitar a compreensão e inventário dos VMs no seu ambiente.

**Melhores práticas:** Garantir acesso privilegiado.   
**Detalhe**: Utilize uma [abordagem de menor privilégio](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) e funções Azure incorporadas para permitir aos utilizadores aceder e configurar VMs:

- [Contribuinte de Máquinas Virtuais](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor): Pode gerir VMs, mas não a rede virtual ou conta de armazenamento a que estão ligados.
- [Classic Virtual Machine Contributor](../../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor): Pode gerir VMs criados utilizando o modelo de implementação clássico, mas não a rede virtual ou conta de armazenamento a que os VM estão conectados.
- [Administração de Segurança](../../role-based-access-control/built-in-roles.md#security-admin): Apenas no Centro de Segurança: Pode ver políticas de segurança, ver estados de segurança, editar políticas de segurança, ver alertas e recomendações, rejeitar alertas e recomendações.
- [Utilizador de Laboratórios DevTest:](../../role-based-access-control/built-in-roles.md#devtest-labs-user)Pode ver tudo e ligar, iniciar, reiniciar e desligar VMs.

Os seus administradores de subscrição e coadmins podem alterar esta definição, tornando-os administradores de todos os VMs numa subscrição. Certifique-se de que confia em todos os seus administradores de subscrição e coadmins para iniciar sessão em qualquer uma das suas máquinas.

> [!NOTE]
> Recomendamos que consolide VMs com o mesmo ciclo de vida no mesmo grupo de recursos. Ao utilizar grupos de recursos, pode implementar, monitorizar e aumentar os custos de faturação dos seus recursos.
>
>

As organizações que controlam o acesso e configuração de VM melhoram a sua segurança geral em VM.

## <a name="use-multiple-vms-for-better-availability"></a>Use vários VMs para uma melhor disponibilidade
Se o seu VM executa aplicações críticas que precisam de ter alta disponibilidade, recomendamos vivamente que utilize vários VMs. Para uma melhor disponibilidade, utilize um [conjunto de disponibilidade](../../virtual-machines/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) ou [zonas](../../availability-zones/az-overview.md)de disponibilidade.

Um conjunto de disponibilidade é um agrupamento lógico que pode usar em Azure para garantir que os recursos VM que coloca dentro dele são isolados uns dos outros quando são implantados num datacenter Azure. O Azure garante que os VMs que coloca num conjunto de disponibilidade funcionam através de vários servidores físicos, racks de computação, unidades de armazenamento e interruptores de rede. Se ocorrer uma falha de hardware ou software Azure, apenas um subconjunto dos seus VMs são afetados, e a sua aplicação global continua disponível para os seus clientes. Os conjuntos de disponibilidade são uma capacidade essencial quando se pretende construir soluções de nuvem fiáveis.

## <a name="protect-against-malware"></a>Proteção contra software maligno
Deve instalar proteção antimalware para ajudar a identificar e remover vírus, spyware e outros softwares maliciosos. Pode instalar [o Microsoft Antimalware](antimalware.md) ou a solução de proteção de ponto final de um parceiro da Microsoft[(Trend Micro](https://help.deepsecurity.trendmicro.com/Welcome.html), [Broadcom,](https://www.broadcom.com/products) [McAfee,](https://www.mcafee.com/us/products.aspx) [Windows Defender](https://www.microsoft.com/windows/comprehensive-security)e System [Center Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)).

O Microsoft Antimalware inclui funcionalidades como proteção em tempo real, digitalização programada, remediação de malware, atualizações de assinaturas, atualizações de motores, relatórios de amostras e recolha de eventos de exclusão. Para ambientes que são hospedados separadamente do seu ambiente de produção, você pode usar uma extensão antimalware para ajudar a proteger seus VMs e serviços na nuvem.

Pode integrar soluções de Antimalware e parceiros da Microsoft com [o Azure Security Center](../../security-center/index.yml) para facilitar a implementação e deteções incorporadas (alertas e incidentes).

**Melhores práticas**: Instale uma solução antimalware para proteger contra malware.   
**Detalhe**: [Instale uma solução de parceiro microsoft ou Antimalware Microsoft](../../security-center/security-center-services.md#supported-endpoint-protection-solutions-)

**Melhores práticas**: Integre a sua solução antimalware com o Security Center para monitorizar o estado da sua proteção.   
**Detalhe**: [Gerir problemas de proteção de pontos finais com o Centro de Segurança](../../security-center/security-center-partner-integration.md)

## <a name="manage-your-vm-updates"></a>Gerencie as suas atualizações de VM
Os VMs Azure, como todos os VMs no local, devem ser geridos pelo utilizador. O Azure não emite atualizações do Windows para as VMs. Precisa de gerir as atualizações de VM.

**Melhores práticas**: Mantenha os seus VMs atuais.   
**Detalhe**: Utilize a solução [de Gestão](../../automation/update-management/overview.md) de Atualização na Azure Automation para gerir as atualizações do sistema operativo para os seus computadores Windows e Linux que são implantados em Azure, em ambientes no local ou em outros fornecedores de nuvem. Pode rapidamente avaliar o estado das atualizações disponíveis em todos os computadores agente e gerir o processo de instalação de atualizações necessárias para os servidores.

Os computadores que são geridos pela Gestão de Atualizações utilizam as seguintes configurações para realizar a avaliação e as implementações de atualização:

- Agente de Monitorização da Microsoft (MMA) para Windows ou Linux
- Configuração de Estado Pretendido do PowerShell (DSC) para Linux
- Função de Trabalho de Runbook Híbrida da Automatização
- Microsoft Update ou Windows Server Update Services (WSUS) para computadores Windows

Se utilizar o Windows Update, deixe ativar a definição automática de Atualização do Windows.

**Melhores práticas**: Certifique-se de que as imagens que construiu incluem a mais recente ronda de atualizações do Windows.   
**Detalhe**: Verifique e instale todas as atualizações do Windows como um primeiro passo de cada implementação. Esta medida é especialmente importante para aplicar quando implementa imagens que vêm de si ou da sua própria biblioteca. Embora as imagens do Azure Marketplace sejam atualizadas automaticamente por padrão, pode haver um tempo de atraso (até algumas semanas) após um lançamento público.

**Melhores práticas**: Reenfectuar periodicamente os seus VMs para forçar uma nova versão do SISTEMA.   
**Detalhe**: Defina o seu VM com um [modelo de Gestor de Recursos Azure](../../azure-resource-manager/templates/template-syntax.md) para que possa reimplantá-lo facilmente. A utilização de um modelo dá-lhe um VM remendado e seguro quando precisa.

**Melhores práticas**: Aplique rapidamente atualizações de segurança em VMs.   
**Detalhe**: Permita ao Azure Security Center (nível gratuito ou standard) [identificar as atualizações de segurança em falta e aplicá-las](../../security-center/asset-inventory.md).

**Melhores práticas**: Instale as últimas atualizações de segurança.   
**Detalhe**: Algumas das primeiras cargas de trabalho que os clientes mudam para a Azure são laboratórios e sistemas virados para o exterior. Se os seus VMs Azure hospedar aplicações ou serviços que precisam de ser acessíveis à internet, esteja atento ao patching. Remendar para além do sistema operativo. As vulnerabilidades não remendadas nas aplicações dos parceiros também podem levar a problemas que podem ser evitados se estiver em vigor uma boa gestão de patchs.

**Melhores práticas**: Implementar e testar uma solução de backup.   
**Detalhe**: Uma cópia de segurança tem de ser manuseada da mesma forma que lida com qualquer outra operação. Isto aplica-se a sistemas que fazem parte do seu ambiente de produção que se estende até à nuvem.

Os sistemas de teste e dev devem seguir estratégias de backup que forneçam capacidades de restauro semelhantes às que os utilizadores se habituaram, com base na sua experiência com ambientes no local. As cargas de trabalho de produção transferidas para a Azure devem integrar-se com as soluções de backup existentes, quando possível. Ou, pode utilizar [o Azure Backup](../../backup/backup-azure-vms-first-look-arm.md) para ajudar a responder aos seus requisitos de backup.

As organizações que não aplicam políticas de atualização de software estão mais expostas a ameaças que exploram vulnerabilidades conhecidas e previamente corrigidas. Para cumprir os regulamentos do setor, as empresas devem provar que são diligentes e utilizar os controlos de segurança corretos para ajudar a garantir a segurança das suas cargas de trabalho localizadas na nuvem.

As melhores práticas de atualização de software para um datacenter tradicional e Azure IaaS têm muitas semelhanças. Recomendamos que avalie as suas atuais políticas de atualização de software para incluir VMs localizados em Azure.

## <a name="manage-your-vm-security-posture"></a>Gerencie a sua postura de segurança VM
As ameaças cibernéticas estão a evoluir. A salvaguarda dos seus VMs requer uma capacidade de monitorização que possa detetar rapidamente ameaças, impedir o acesso não autorizado aos seus recursos, desencadear alertas e reduzir falsos positivos.

Para monitorizar a postura de segurança dos seus [VMs Windows](../../security-center/security-center-introduction.md) e [Linux,](../../security-center/security-center-introduction.md)utilize [o Centro de Segurança Azure](../../security-center/security-center-introduction.md). No Centro de Segurança, proteja os seus VMs tirando partido das seguintes capacidades:

- Aplicar definições de segurança de SO com regras de configuração recomendadas.
- Identifique e descarregue a segurança do sistema e as atualizações críticas que podem estar em falta.
- Implementar recomendações para a proteção antimalware de ponta.
- Validar a encriptação do disco.
- Avaliar e remediar vulnerabilidades.
- Detetar ameaças.

O Centro de Segurança pode monitorizar ativamente ameaças, e potenciais ameaças são expostas em alertas de segurança. Ameaças correlacionadas são agregadas numa única vista chamada incidente de segurança.

O Centro de Segurança armazena dados nos [registos do Monitor Azure](../../azure-monitor/log-query/log-query-overview.md). Os registos do Azure Monitor fornecem uma linguagem de consulta e um motor de análise que lhe dá informações sobre o funcionamento das suas aplicações e recursos. Os dados são também recolhidos a partir do [Azure Monitor,](../../batch/monitoring-overview.md)soluções de gestão e agentes instalados em máquinas virtuais na nuvem ou no local. Esta funcionalidade partilhada ajuda-o a formar uma visão geral do seu ambiente.

Organizações que não impõem uma segurança forte para os seus VMs continuam desconhecendo possíveis tentativas de utilizadores não autorizados de contornar os controlos de segurança.

## <a name="monitor-vm-performance"></a>Desempenho do Monitor VM
O abuso de recursos pode ser um problema quando os processos de VM consomem mais recursos do que deviam. Problemas de desempenho com um VM podem levar a uma interrupção do serviço, o que viola o princípio de segurança da disponibilidade. Isto é particularmente importante para VMs que estão hospedando IIS ou outros servidores web, porque o uso elevado de CPU ou memória pode indicar um ataque de negação de serviço (DoS). É imperativo monitorizar o acesso vm não só reactivamente enquanto um problema está ocorrendo, mas também proativamente contra o desempenho de base como medido durante o funcionamento normal.

Recomendamos que utilize [o Azure Monitor](../../azure-monitor/platform/data-platform.md) para ganhar visibilidade na saúde do seu recurso. Características do Azure Monitor:

- [Ficheiros de registo de diagnóstico de recursos](../../azure-monitor/platform/platform-logs-overview.md): Monitoriza os seus recursos VM e identifica potenciais problemas que podem comprometer o desempenho e a disponibilidade.
- [Extensão Azure Diagnostics](../../azure-monitor/platform/diagnostics-extension-overview.md): Fornece capacidades de monitorização e diagnóstico em VMs do Windows. Pode ativar estas capacidades incluindo a extensão como parte do [modelo do Gestor de Recursos Azure](../../virtual-machines/extensions/diagnostics-template.md).

As organizações que não monitorizam o desempenho do VM não conseguem determinar se certas alterações nos padrões de desempenho são normais ou anormais. Um VM que está a consumir mais recursos do que o normal pode indicar um ataque de um recurso externo ou um processo comprometido em execução no VM.

## <a name="encrypt-your-virtual-hard-disk-files"></a>Criptografe os seus ficheiros de discos rígidos virtuais
Recomendamos que criptografe os seus discos rígidos virtuais (VHDs) para ajudar a proteger o volume de arranque e os volumes de dados em repouso no armazenamento, juntamente com as suas chaves e segredos de encriptação.

[A encriptação do disco Azure](./azure-disk-encryption-vms-vmss.md) ajuda-o a encriptar os discos de máquina virtual Windows e Linux IaaS. A Azure Disk Encryption utiliza a funcionalidade [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) padrão da indústria do Windows e a funcionalidade [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer encriptação de volume para o SISTEMA e os discos de dados. A solução está integrada com [o Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para o ajudar a controlar e gerir as chaves e segredos de encriptação do disco na subscrição do cofre principal. A solução também garante que todos os dados dos discos de máquinas virtuais são encriptados em repouso no Azure Storage.

Seguem-se as melhores práticas para a utilização da encriptação do disco Azure:

**Melhores práticas**: Ative a encriptação em VMs.   
**Detalhe**: Azure Disk Encryption gera e escreve as chaves de encriptação do cofre da chave. A gestão das chaves de encriptação no cofre requer autenticação AZure AD. Crie uma aplicação AD AZure para este fim. Para efeitos de autenticação, pode utilizar a autenticação [AZure AD baseada em clientes ou o certificado de cliente AZure AD.](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)

**Melhor prática**: Utilize uma chave de encriptação chave (KEK) para uma camada adicional de segurança para chaves de encriptação. Adicione um KEK ao seu cofre de chaves.   
**Detalhe**: Utilize o [cmdlet Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) para criar uma chave de encriptação chave no cofre da chave. Também pode importar um KEK do seu módulo de segurança de hardware no local (HSM) para gestão de chaves. Para mais informações, consulte a documentação do [Cofre de Chaves.](../../key-vault/keys/hsm-protected-keys.md) Quando uma chave de encriptação chave é especificada, a Encriptação do Disco Azure usa essa chave para embrulhar os segredos de encriptação antes de escrever para Key Vault. Manter uma cópia desta chave numa gestão chave no local, o HSM oferece proteção adicional contra a supressão acidental das chaves.

**Melhor prática**: Faça uma [foto e/ou](../../virtual-machines/windows/snapshot-copy-managed-disk.md) cópia de segurança antes que os discos sejam encriptados. As cópias de segurança fornecem uma opção de recuperação se uma falha inesperada acontecer durante a encriptação.   
**Detalhe**: VMs com discos geridos requerem uma cópia de segurança antes da encriptação ocorrer. Depois de ser feita uma cópia de segurança, pode utilizar o **cmdlet Set-AzVMDiskEncryptionExtension** para encriptar os discos geridos especificando o parâmetro *-skipVmBackup.* Para obter mais informações sobre como fazer backup e restaurar VMs encriptados, consulte o artigo [de Backup Azure.](../../backup/backup-azure-vms-encryption.md)

**Melhores práticas**: Para garantir que os segredos de encriptação não cruzem fronteiras regionais, a Encriptação do Disco Azure precisa do cofre-chave e os VMs para serem localizados na mesma região.   
**Detalhe**: Criar e utilizar um cofre-chave que esteja na mesma região que o VM para ser encriptado.

Quando aplicar encriptação de disco Azure, pode satisfazer as seguintes necessidades do negócio:

- As VMs IaaS são protegidas em inatividade através de tecnologias de encriptação padrão no setor para dar resposta a requisitos de conformidade e segurança organizacionais.
- Os IAAS VMs começam sob chaves e políticas controladas pelo cliente, e você pode auditar o seu uso no seu cofre chave.

## <a name="restrict-direct-internet-connectivity"></a>Restringir a conectividade direta da internet
Monitorize e restringa a conectividade direta da Internet VM. Os atacantes verificam constantemente as gamas IP em nuvem pública para portas de gestão abertas e tentam ataques "fáceis", como senhas comuns e vulnerabilidades conhecidas por não ser remadas. A tabela que se segue enumera as melhores práticas para ajudar a proteger contra estes ataques:

**Melhores práticas**: Evitar exposição inadvertida ao encaminhamento e segurança da rede.   
**Detalhe**: Utilize o Azure RBAC para garantir que apenas o grupo central de rede tenha permissão para recursos de rede.

**Melhores práticas**: Identifique e remedia vMs expostos que permitam o acesso a partir de "qualquer" endereço IP de origem.   
**Detalhe**: Utilize o Centro de Segurança Azure. O Security Center recomendará que restringa o acesso através de pontos finais virados para a Internet se algum dos seus grupos de segurança de rede tiver uma ou mais regras de entrada que permitam o acesso a partir de "qualquer" endereço IP de origem. O Security Center recomendará que edite estas regras de entrada para restringir o [acesso](../../security-center/security-center-network-recommendations.md) a endereços IP de origem que realmente precisam de acesso.

**Melhores práticas**: Restringir portas de gestão (RDP, SSH).   
**Detalhe**: [O acesso em VM just-in-time (JIT)](../../security-center/security-center-just-in-time.md) pode ser utilizado para bloquear o tráfego de entrada nos seus VMs Azure, reduzindo a exposição a ataques, proporcionando fácil acesso à ligação aos VM quando necessário. Quando o JIT está ativado, o Centro de Segurança bloqueia o tráfego de entrada para os seus VMs Azure, criando uma regra de grupo de segurança de rede. Selecione as portas do VM para as quais o tráfego de entrada será bloqueado. Estas portas são controladas pela solução JIT.

## <a name="next-steps"></a>Passos seguintes
Consulte as [melhores práticas e padrões](best-practices-and-patterns.md) de segurança da Azure para obter mais boas práticas de segurança quando estiver a desenhar, implementar e gerir as suas soluções em nuvem utilizando o Azure.

Estão disponíveis os seguintes recursos para fornecer informações mais gerais sobre a segurança da Azure e serviços relacionados com a Microsoft:
* [Azure Security Team Blog](/archive/blogs/azuresecurity/) - para informações atualizadas sobre as últimas informações em Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) - onde as vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, podem ser reportadas ou via e-mail para secure@microsoft.com