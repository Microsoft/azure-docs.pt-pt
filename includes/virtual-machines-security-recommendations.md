---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 11/13/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: df577ab2b5e9658fd55324c8fd6fd008621b4d46
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80545909"
---
Este artigo contém recomendações de segurança para máquinas virtuais Azure. Siga estas recomendações para ajudar a cumprir as obrigações de segurança descritas no nosso modelo de responsabilidade partilhada. As recomendações também o ajudarão a melhorar a segurança global para as soluções da sua aplicação web. Para obter mais informações sobre o que a Microsoft faz para cumprir as responsabilidades do prestador de serviços, consulte [responsabilidades partilhadas para a computação em nuvem](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Algumas das recomendações deste artigo podem ser abordadas automaticamente pelo Azure Security Center. O Azure Security Center é a primeira linha de defesa para os seus recursos em Azure. Analisa periodicamente o estado de segurança dos seus recursos Azure para identificar potenciais vulnerabilidades de segurança. Recomenda então como lidar com as vulnerabilidades. Para mais informações, consulte recomendações de segurança no Centro de [Segurança Azure.](../articles/security-center/security-center-recommendations.md)

Para obter informações gerais sobre o Azure Security Center, consulte o Centro de [Segurança Azure?](../articles/security-center/security-center-intro.md)

## <a name="general"></a>Geral

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
| Quando construir imagens VM personalizadas, aplique as últimas atualizações. | Antes de criar imagens, instale as últimas atualizações para o sistema operativo e para todas as aplicações que farão parte da sua imagem.  | - |
| Mantenha os vMs atuais. | Pode utilizar a solução [Update Management](../articles/automation/automation-update-management.md) no Azure Automation para gerir as atualizações do sistema operativo para os computadores Windows e Linux em Azure. | [Sim](../articles/security-center/security-center-apply-system-updates.md) |
| Recuem os vossos VMs. | [A Azure Backup](../articles/backup/backup-overview.md) ajuda a proteger os dados da sua aplicação e tem custos operacionais mínimos. Erros de aplicação podem corromper os seus dados, e erros humanos podem introduzir bugs nas suas aplicações. O Azure Backup protege os seus VMs que executam windows e Linux. | - |
| Utilize vários VMs para uma maior resiliência e disponibilidade. | Se o seu VM executa aplicações que devem estar altamente disponíveis, utilize vários VMs ou [conjuntos de disponibilidade](../articles/virtual-machines/windows/manage-availability.md). | - |
| Adote uma estratégia de continuidade de negócios e recuperação de desastres (BCDR). | A Recuperação do Site Azure permite-lhe escolher entre diferentes opções concebidas para apoiar a continuidade do negócio. Suporta diferentes replicações e cenários de failover. Para mais informações, consulte sobre a recuperação do [site.](../articles/site-recovery/site-recovery-overview.md) | - |

## <a name="data-security"></a>Segurança de dados

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
| Criptografe os discos do sistema operativo. | [A encriptação](../articles/security/azure-security-disk-encryption-overview.md) do disco Azure ajuda-o a encriptar os discos VM Do Windows e do Linux IaaS. Sem as chaves necessárias, o conteúdo dos discos encriptados é ilegível. A encriptação do disco protege os dados armazenados de acesso não autorizado que de outra forma seriam possíveis se o disco fosse copiado.| [Sim](../articles/security-center/security-center-apply-disk-encryption.md) |
| Criptografe discos de dados. | [A encriptação](../articles/security/azure-security-disk-encryption-overview.md) do disco Azure ajuda-o a encriptar os discos VM Do Windows e do Linux IaaS. Sem as chaves necessárias, o conteúdo dos discos encriptados é ilegível. A encriptação do disco protege os dados armazenados de acesso não autorizado que de outra forma seriam possíveis se o disco fosse copiado.| -  |
| Limite o software instalado. | Limite o software instalado ao necessário para aplicar com sucesso a sua solução. Esta orientação ajuda a reduzir a superfície de ataque da sua solução. | - |
| Utilize antivírus ou antimalware. | No Azure, pode utilizar software antimalware de fornecedores de segurança como microsoft, Symantec, Trend Micro e Kaspersky. Este software ajuda a proteger os seus VMs de ficheiros maliciosos, adware e outras ameaças. Pode implementar o Microsoft Antimalware com base nas cargas de trabalho da sua aplicação. Utilize uma configuração personalizada básica de segurança por padrão ou avançada. Para mais informações, consulte [o Microsoft Antimalware para Serviços de Nuvem Azure e Máquinas Virtuais](../articles/security/azure-security-antimalware.md). | - |
| Guarde as chaves e segredos. | Simplifique a gestão dos seus segredos e chaves, fornecendo aos seus proprietários de aplicações uma opção segura e gerida centralmente. Esta gestão reduz o risco de um compromisso acidental ou fuga. O Azure Key Vault pode armazenar as suas chaves de forma segura em módulos de segurança de hardware (HSMs) certificados para o FIPS 140-2 Level 2. Se precisar de utilizar FIPs 140.2 Level 3 para armazenar as suas chaves e segredos, pode utilizar [o Azure Dedicated HSM](../articles/dedicated-hsm/overview.md). | - |

## <a name="identity-and-access-management"></a>Gestão de identidades e acessos 

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
| Centralize a autenticação VM. | Pode centralizar a autenticação dos seus VMs Windows e Linux utilizando a [autenticação do Diretório Ativo Azure](../articles/active-directory/develop/authentication-scenarios.md). | - |

## <a name="monitoring"></a>Monitorização

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
| Monitorize os seus VMs. | Pode utilizar o [Monitor Azure para VMs](../articles/azure-monitor/insights/vminsights-overview.md) para monitorizar o estado dos seus VMs Azure e conjuntos de escala de máquinas virtuais. Problemas de desempenho com um VM podem levar a perturbações no serviço, o que viola o princípio de segurança da disponibilidade. | - |

## <a name="networking"></a>Redes

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
| Restringir o acesso a portos de gestão. | Os atacantes procuram em nuvem pública as gamas ip para portas de gestão abertas e tentam ataques "fáceis", como senhas comuns e vulnerabilidades conhecidas sem remendar. Pode utilizar [o acesso VM just-in-time (JIT)](../articles/security-center/security-center-just-in-time.md) para bloquear o tráfego de entrada para os seus VMs Azure, reduzindo a exposição a ataques, fornecendo ligações fáceis aos VMs quando são necessários. | - |
| Limite o acesso à rede. | Os grupos de segurança da rede permitem-lhe restringir o acesso à rede e controlar o número de pontos finais expostos. Para mais informações, consulte [Criar, alterar ou eliminar um grupo](../articles/virtual-network/manage-network-security-group.md)de segurança de rede . | - |

## <a name="next-steps"></a>Passos seguintes

Consulte o seu fornecedor de aplicações para saber sobre requisitos adicionais de segurança. Para obter mais informações sobre o desenvolvimento de aplicações seguras, consulte [documentação de desenvolvimento seguro.](../articles/security/fundamentals/abstract-develop-secure-apps.md)
