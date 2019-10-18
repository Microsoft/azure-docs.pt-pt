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
ms.openlocfilehash: d43860d71b14bdac275df51695c9206539529171
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72511508"
---
Este artigo contém recomendações de segurança para máquinas virtuais do Azure. Siga estas recomendações para ajudar a atender as obrigações de segurança descritas em nosso modelo para responsabilidade compartilhada. As recomendações também ajudarão você a melhorar a segurança geral para suas soluções de aplicativo Web. Para obter mais informações sobre o que a Microsoft faz para atender às responsabilidades do provedor de serviços, consulte [responsabilidades compartilhadas para computação em nuvem](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225237/1/Shared%20Responsibilities%20for%20Cloud%20Computing%20(2017-04-03).pdf).

Algumas das recomendações deste artigo podem ser abordadas automaticamente pela central de segurança do Azure. A central de segurança do Azure é a primeira linha de defesa para seus recursos no Azure. Ele analisa periodicamente o estado de segurança de seus recursos do Azure para identificar possíveis vulnerabilidades de segurança. Em seguida, ele recomenda como tratar as vulnerabilidades. Para obter mais informações, consulte [recomendações de segurança na central de segurança do Azure](../articles/security-center/security-center-recommendations.md).

Para obter informações gerais sobre a central de segurança do Azure, consulte [o que é a central de segurança do Azure?](../articles/security-center/security-center-intro.md).

## <a name="recommendations"></a>Recomendações

| Categoria | Recomendação | Comentários | Centro de Segurança |
|-|-|----|--|
| Geral | Ao criar imagens de VM personalizadas, aplique as atualizações mais recentes. | Antes de criar imagens, instale as atualizações mais recentes para o sistema operacional e para todos os aplicativos que serão parte da sua imagem.  | - |
| Geral | Mantenha suas VMs atuais. | Você pode usar a solução [Gerenciamento de atualizações](../articles/automation/automation-update-management.md) na automação do Azure para gerenciar atualizações do sistema operacional para seus computadores Windows e Linux no Azure. | [Sim](../articles/security-center/security-center-apply-system-updates.md) |
| Geral | Faça backup de suas VMs. | O [backup do Azure](../articles/backup/backup-overview.md) ajuda a proteger os dados do aplicativo e tem custos operacionais mínimos. Os erros das aplicações podem danificar os dados e os erros humanos podem introduzir erros nas suas aplicações. O backup do Azure protege suas VMs que executam Windows e Linux. | - |
| Geral | Use várias VMs para obter maior resiliência e disponibilidade. | Se sua VM executar aplicativos que devem ser altamente disponíveis, use várias VMs ou [conjuntos de disponibilidade](../articles/virtual-machines/windows/manage-availability.md). | - |
| Geral | Adote uma estratégia de BCDR (continuidade dos negócios e recuperação de desastre). | Azure Site Recovery permite que você escolha entre diferentes opções criadas para dar suporte à continuidade dos negócios. Ele dá suporte a diferentes cenários de replicação e failover. Para obter mais informações, consulte [About site Recovery](../articles/site-recovery/site-recovery-overview.md). | - |
| Gestão de identidades e acesso | Centralize a autenticação de VM. | Você pode centralizar a autenticação de suas VMs Windows e Linux usando [Azure Active Directory autenticação](../articles/active-directory/develop/authentication-scenarios.md). | - |
| Segurança dos dados | Criptografar discos do sistema operacional. | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) ajuda a criptografar seus discos de VM IaaS Windows e Linux. Sem as chaves necessárias, o conteúdo dos discos criptografados não podem ser lidos. A criptografia de disco protege dados armazenados de acesso não autorizado que de outra forma seriam possíveis se o disco fosse copiado.| [Sim](../articles/security-center/security-center-apply-disk-encryption.md) |
| Segurança dos dados | Criptografar discos de dados. | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) ajuda a criptografar seus discos de VM IaaS Windows e Linux. Sem as chaves necessárias, o conteúdo dos discos criptografados não podem ser lidos. A criptografia de disco protege dados armazenados de acesso não autorizado que de outra forma seriam possíveis se o disco fosse copiado.| -  |
| Segurança dos dados | Limite o software instalado. | Limite o software instalado ao que é necessário para aplicar a solução com êxito. Essa diretriz ajuda a reduzir a superfície de ataque da solução. | - |
| Segurança dos dados | Use antivírus ou antimalware. | No Azure, você pode usar o software antimalware de fornecedores de segurança, como Microsoft, Symantec, Trend Micro e Kaspersky. Esse software ajuda a proteger suas VMs contra arquivos mal-intencionados, adware e outras ameaças. Você pode implantar o Microsoft Antimalware com base em suas cargas de trabalho de aplicativo. Use a configuração básica segura por padrão ou personalizada avançada. Para obter mais informações, consulte [Microsoft antimalware para serviços de nuvem do Azure e máquinas virtuais](../articles/security/azure-security-antimalware.md). | - |
| Segurança dos dados | Armazene segredos e chaves com segurança. | Simplifique o gerenciamento de seus segredos e chaves fornecendo aos proprietários do aplicativo uma opção segura e gerenciada centralmente. Esse gerenciamento reduz o risco de um comprometimento acidental ou vazamento. Azure Key Vault pode armazenar com segurança suas chaves em HSMs (módulos de segurança de hardware) certificados para o FIPS 140-2 nível 2. Se você precisar usar o FIPs 140,2 nível 3 para armazenar suas chaves e segredos, poderá usar o [HSM dedicado do Azure](../articles/dedicated-hsm/overview.md). | - |
| Funcionamento em Rede | Restrinja o acesso às portas de gerenciamento. | Os invasores verificam intervalos de IP de nuvem pública para portas de gerenciamento aberto e tentam ataques "fáceis", como senhas comuns e vulnerabilidades não corrigidas conhecidas. Você pode usar o [acesso à VM just-in-time (JIT)](../articles/security-center/security-center-just-in-time.md) para bloquear o tráfego de entrada para suas VMs do Azure, reduzindo a exposição a ataques ao mesmo tempo em que fornece conexões fáceis às VMs quando elas forem necessárias. | - |
| Funcionamento em Rede | Limite o acesso à rede. | Os grupos de segurança de rede permitem restringir o acesso à rede e controlar o número de pontos de extremidade expostos. Para obter mais informações, consulte [criar, alterar ou excluir um grupo de segurança de rede](../articles/virtual-network/manage-network-security-group.md). | - |
| Monitorização | Monitore suas VMs. | Você pode usar [Azure monitor para VMs](../articles/azure-monitor/insights/vminsights-overview.md) para monitorar o estado de suas VMs do Azure e conjuntos de dimensionamento de máquinas virtuais. Problemas de desempenho com uma VM podem levar à interrupção do serviço, o que viola o princípio de segurança de disponibilidade. | - |

## <a name="next-steps"></a>Passos seguintes

Verifique com seu provedor de aplicativos para saber mais sobre os requisitos de segurança adicionais. Para obter mais informações sobre como desenvolver aplicativos seguros, consulte [a documentação de desenvolvimento seguro](../articles/security/fundamentals/abstract-develop-secure-apps.md).
