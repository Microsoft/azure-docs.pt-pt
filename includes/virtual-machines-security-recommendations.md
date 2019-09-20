---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: barclayn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/19/2019
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: 60d4957f65c124de683c6d156561de3b52aaec94
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71163946"
---
Este artigo contém recomendações de segurança para máquinas virtuais do Azure. Implementar essas recomendações ajudará você a atender suas obrigações de segurança, conforme descrito em nosso modelo de responsabilidade compartilhada, e melhorará a segurança geral para suas soluções de aplicativo Web. Para obter mais informações sobre o que a Microsoft faz para atender às responsabilidades do provedor de serviços, leia [responsabilidades compartilhadas para computação em nuvem](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

Algumas das recomendações incluídas neste artigo podem ser monitoradas automaticamente pela central de segurança do Azure. A central de segurança do Azure é a primeira linha de defesa na proteção de seus recursos no Azure. Ele analisa periodicamente o estado de segurança de seus recursos do Azure para identificar possíveis vulnerabilidades de segurança. Em seguida, ele fornece recomendações sobre como solucioná-los.

- Para obter mais informações sobre as recomendações da central de segurança do Azure, consulte [recomendações de segurança na central de segurança do Azure](../articles/security-center/security-center-recommendations.md).
- Para obter informações sobre a central de segurança do Azure, consulte o [que é a central de segurança do Azure?](../articles/security-center/security-center-intro.md)

## <a name="recommendations"></a>Recomendações

| Category | Recomendação | Comentários | Centro de Segurança |
|-|-|----|--|
| Geral | Ao criar imagens de VM personalizadas, use as atualizações mais recentes | Antes de criar imagens, verifique se todas as atualizações mais recentes estão instaladas para o sistema operacional e todos os aplicativos que serão parte da sua imagem.  | - |
| Geral | Mantenha suas VMs atuais | Você pode usar a solução [Gerenciamento de atualizações](../articles/automation/automation-update-management.md) na automação do Azure para gerenciar atualizações do sistema operacional para seus computadores Windows e Linux no Azure | [Sim](../articles/security-center/security-center-apply-system-updates.md) |
| Geral | Fazer backup de suas máquinas virtuais | O [backup do Azure](../articles/backup/backup-overview.md) ajuda a proteger os dados do aplicativo com custos operacionais mínimos. Os erros das aplicações podem danificar os dados e os erros humanos podem introduzir erros nas suas aplicações. Com o backup do Azure, suas máquinas virtuais executando Windows e Linux são protegidas. | - |
| Geral | Use várias VMs para obter maior resiliência e disponibilidade | Se sua VM executa aplicativos que precisam ter alta disponibilidade, você deve ter várias VMs ou [conjuntos de disponibilidade](../articles/virtual-machines/windows/manage-availability.md) | - |
| Geral | Adote uma estratégia de BCDR (continuidade dos negócios e recuperação de desastre) | Azure Site Recovery permite que você escolha entre diferentes opções criadas para dar suporte às suas necessidades de continuidade de negócios. Ele dá suporte à replicação diferente e faz failover de cenários. Para obter mais informações, consulte [About site Recovery](../articles/site-recovery/site-recovery-overview.md) | - |
| Gestão de acesso e identidades | Centralizar a autenticação de VM | Você pode centralizar a autenticação de suas máquinas virtuais Windows e Linux usando a [autenticação do Azure ad](../articles/active-directory/develop/authentication-scenarios.md). | - |
| Segurança de dados | Criptografar discos do sistema operacional | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) ajuda a criptografar seus discos de máquina virtual IaaS Windows e Linux. A criptografia de discos torna o conteúdo ilegível sem as chaves necessárias. A criptografia de disco protege dados armazenados de acesso não autorizado que de outra forma seriam possíveis se o disco fosse copiado| [Sim](../articles/security-center/security-center-apply-disk-encryption.md) |
| Segurança de dados | Criptografar discos de dados | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) ajuda a criptografar seus discos de máquina virtual IaaS Windows e Linux. A criptografia de discos torna o conteúdo ilegível sem as chaves necessárias. A criptografia de disco protege dados armazenados de acesso não autorizado que de outra forma seriam possíveis se o disco fosse copiado| -  |
| Segurança de dados | Limitar o software instalado | Limitar o software instalado ao que é necessário para implementar com êxito sua solução ajuda a reduzir a superfície de ataque da solução | - |
| Segurança de dados | Usar antivírus/Antimalware | No Azure, você pode usar o software antimalware de fornecedores de segurança, como Microsoft, Symantec, Trend Micro e Kaspersky. Esse software ajuda a proteger suas máquinas virtuais contra arquivos mal-intencionados, adware e outras ameaças. Você pode implantar a proteção antimalware da Microsoft com base em suas cargas de trabalho de aplicativo, com a configuração básica de segurança padrão ou personalizada avançada. Para obter mais informações sobre o Microsoft antimalware para o Azure, consulte [Microsoft antimalware para serviços de nuvem do Azure e máquinas virtuais](../articles/security/azure-security-antimalware.md) | - |
| Segurança de dados | Armazenar chaves e segredos com segurança | Simplificar o gerenciamento de seus segredos e chaves fornecendo aos proprietários do aplicativo uma opção seguramente gerenciada permite que você reduza o risco de comprometimento acidental ou vazamentos. Azure Key Vault pode armazenar com segurança suas chaves em HSMs (módulos de segurança de hardware) certificados para o FIPS 140-2 nível 2. Se você precisar armazenar suas chaves e segredos usando o FIPs 140,2 nível 3, poderá usar o [HSM dedicado do Azure](../articles/dedicated-hsm/overview.md) | - |
| Redes | Restringir o acesso às portas de gerenciamento | Os invasores verificam intervalos de IP de nuvem pública para portas de gerenciamento aberto e tentam ataques "fáceis", como senhas comuns e vulnerabilidades não corrigidas conhecidas. O [acesso à VM (just-in-time) de máquina virtual (JIT](../articles/security-center/security-center-just-in-time.md) ) pode ser usado para bloquear o tráfego de entrada para suas VMs do Azure, reduzindo a exposição a ataques e, ao mesmo tempo, fornecendo acesso fácil para se conectar às VMs quando necessário. | - |
| Redes | Limitar o acesso à rede | Os grupos de segurança de rede permitem restringir o acesso à rede e controlar o número de pontos de extremidade expostos. Para obter mais informações, consulte [criar, alterar ou excluir um grupo de segurança de rede](../articles/virtual-network/manage-network-security-group.md) | - |
| Monitorização | Monitorar suas máquinas virtuais | Você pode usar o [Azure monitor para VMs](../articles/azure-monitor/insights/vminsights-overview.md) para monitorar o estado de suas máquinas virtuais do Azure e conjuntos de dimensionamento de máquinas virtuais. Problemas de desempenho com uma VM podem levar à interrupção do serviço, o que viola o princípio de segurança de disponibilidade. | - |

## <a name="next-steps"></a>Passos seguintes

Verifique com seu provedor de aplicativos para ver se há requisitos de segurança adicionais. Para obter mais informações sobre como desenvolver aplicativos seguros, consulte a [documentação de desenvolvimento seguro](../articles/security/fundamentals/abstract-develop-secure-apps.md).