---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 11/13/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: e64e6b6abc921b1db6614ed36ba2e9c04fc86b1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87451202"
---
Este artigo contém recomendações de segurança para máquinas virtuais Azure. Siga estas recomendações para ajudar a cumprir as obrigações de segurança descritas no nosso modelo de responsabilidade partilhada. As recomendações também o ajudarão a melhorar a segurança geral das suas soluções de aplicações web. Para obter mais informações sobre o que a Microsoft faz para cumprir as responsabilidades do prestador de serviços, consulte [responsabilidades partilhadas na computação em nuvem.](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)

Algumas das recomendações deste artigo podem ser abordadas automaticamente pelo Azure Security Center. O Centro de Segurança Azure é a primeira linha de defesa para os seus recursos em Azure. Analisa periodicamente o estado de segurança dos seus recursos Azure para identificar potenciais vulnerabilidades de segurança. Recomenda então como lidar com as vulnerabilidades. Para mais informações, consulte [as recomendações de segurança no Centro de Segurança Azure.](../articles/security-center/security-center-recommendations.md)

Para obter informações gerais sobre o Azure Security Center, veja [o que é o Centro de Segurança Azure?](../articles/security-center/security-center-intro.md)

## <a name="general"></a>Geral

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
| Quando construir imagens VM personalizadas, aplique as últimas atualizações. | Antes de criar imagens, instale as mais recentes atualizações para o sistema operativo e para todas as aplicações que farão parte da sua imagem.  | - |
| Mantenha os seus VMs atuais. | Pode utilizar a solução [de Gestão](../articles/automation/update-management/update-mgmt-overview.md) de Atualização na Azure Automation para gerir as atualizações do sistema operativo para os seus computadores Windows e Linux em Azure. | [Sim](../articles/security-center/security-center-apply-system-updates.md) |
| Apoiem os vossos VMs. | [O Azure Backup](../articles/backup/backup-overview.md) ajuda a proteger os dados da sua aplicação e tem custos de funcionamento mínimos. Os erros de aplicação podem corromper os seus dados, e os erros humanos podem introduzir erros nas suas aplicações. O Azure Backup protege os seus VMs que executam Windows e Linux. | - |
| Utilize vários VMs para uma maior resiliência e disponibilidade. | Se o seu VM executa aplicações que devem estar altamente disponíveis, utilize vários VMs ou [conjuntos de disponibilidade](../articles/virtual-machines/windows/manage-availability.md). | - |
| Adotar uma estratégia de continuidade de negócios e recuperação de desastres (BCDR). | A Azure Site Recovery permite-lhe escolher entre diferentes opções desenhadas para apoiar a continuidade do negócio. Suporta diferentes cenários de replicação e falha. Para mais informações, consulte [Sobre a Recuperação do Local.](../articles/site-recovery/site-recovery-overview.md) | - |

## <a name="data-security"></a>Segurança de dados

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
| Criptografe os discos do sistema operativo. | [A encriptação do disco Azure](../articles/security/azure-security-disk-encryption-overview.md) ajuda-o a encriptar os discos VM do Windows e do Linux IaaS. Sem as chaves necessárias, o conteúdo dos discos encriptados é ilegível. A encriptação do disco protege os dados armazenados de acesso não autorizado que de outra forma seriam possíveis se o disco fosse copiado.| [Sim](../articles/security-center/security-center-apply-disk-encryption.md) |
| Criptografe os discos de dados. | [A encriptação do disco Azure](../articles/security/azure-security-disk-encryption-overview.md) ajuda-o a encriptar os discos VM do Windows e do Linux IaaS. Sem as chaves necessárias, o conteúdo dos discos encriptados é ilegível. A encriptação do disco protege os dados armazenados de acesso não autorizado que de outra forma seriam possíveis se o disco fosse copiado.| -  |
| Limite o software instalado. | Limite o software instalado ao que é necessário para aplicar com sucesso a sua solução. Esta orientação ajuda a reduzir a superfície de ataque da sua solução. | - |
| Utilize antivírus ou antimalware. | No Azure, pode utilizar software antimalware de fornecedores de segurança como Microsoft, Symantec, Trend Micro e Kaspersky. Este software ajuda a proteger os seus VMs de ficheiros maliciosos, adware e outras ameaças. Pode implementar o Microsoft Antimalware com base nas cargas de trabalho da sua aplicação. O Microsoft Antimalware está disponível apenas para máquinas Windows. Utilize uma configuração personalizada básica de segurança por defeito ou avançada. Para obter mais informações, consulte [o Microsoft Antimalware para serviços em nuvem azure e máquinas virtuais.](../articles/security/azure-security-antimalware.md) | - |
| Armazenar chaves e segredos seguros. | Simplifique a gestão dos seus segredos e chaves, fornecendo aos seus proprietários de aplicações uma opção segura e gerida centralmente. Esta gestão reduz o risco de um compromisso acidental ou de fuga. O Azure Key Vault pode armazenar de forma segura as suas chaves em módulos de segurança de hardware (HSMs) certificados para FIPS 140-2 Nível 2. Se precisar de utilizar o FIPs 140.2 Nível 3 para armazenar as suas chaves e segredos, pode utilizar [o Azure Dedicated HSM](../articles/dedicated-hsm/overview.md). | - |

## <a name="identity-and-access-management"></a>Gestão de identidades e acessos 

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
| Centralizar a autenticação em VM. | Pode centralizar a autenticação dos seus VMs Windows e Linux utilizando [a autenticação do Azure Ative Directory](../articles/active-directory/develop/authentication-scenarios.md). | - |

## <a name="monitoring"></a>Monitorização

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
| Monitorize os seus VMs. | Pode utilizar [o Azure Monitor para VMs](../articles/azure-monitor/insights/vminsights-overview.md) para monitorizar o estado dos seus VMs Azure e conjuntos de balanças de máquinas virtuais. Problemas de desempenho com um VM podem levar a uma interrupção do serviço, o que viola o princípio de segurança da disponibilidade. | - |

## <a name="networking"></a>Redes

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
| Restringir o acesso aos portos de gestão. | Os atacantes digitalizam as gamas IP em nuvem pública para portas de gestão abertas e tentam ataques "fáceis", como senhas comuns e vulnerabilidades conhecidas por não ser remadas. Você pode usar [o acesso VM just-in-time (JIT)](../articles/security-center/security-center-just-in-time.md) para bloquear o tráfego de entrada para os seus VMs Azure, reduzindo a exposição a ataques, ao mesmo tempo que fornece ligações fáceis aos VM quando são necessários. | - |
| Limite o acesso à rede. | Os grupos de segurança da rede permitem-lhe restringir o acesso à rede e controlar o número de pontos finais expostos. Para obter mais informações, consulte [Criar, alterar ou eliminar um grupo de segurança de rede](../articles/virtual-network/manage-network-security-group.md). | - |

## <a name="next-steps"></a>Passos seguintes

Consulte o seu fornecedor de aplicações para saber mais sobre os requisitos adicionais de segurança. Para obter mais informações sobre o desenvolvimento de aplicações seguras, consulte [a documentação de desenvolvimento seguro](../articles/security/fundamentals/abstract-develop-secure-apps.md).
