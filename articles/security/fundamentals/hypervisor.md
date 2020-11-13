---
title: Segurança hipervisor na frota Azure - Azure Security
description: Visão geral técnica da segurança hipervisor na frota Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 1f81285e869bd2c65cce29766de0b2bd39a627f6
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557972"
---
# <a name="hypervisor-security-on-the-azure-fleet"></a>Segurança hipervisor na frota Azure

O sistema de hipervisoreS Azure baseia-se no Hiper-V do Windows. O sistema de hipervisor permite ao administrador do computador especificar divisórias de hóspedes que têm espaços de endereço separados. Os espaços de endereço separados permitem carregar um sistema operativo e aplicações que operam paralelamente ao sistema operativo (anfitrião) que executa na partição raiz do computador. O hospedeiro OS (também conhecido como partição de raiz privilegiada) tem acesso direto a todos os dispositivos físicos e periféricos do sistema (controladores de armazenamento, adaptações de ligação em rede). O oso anfitrião permite que as divisórias dos hóspedes partilhem o uso destes dispositivos físicos expondo "dispositivos virtuais" a cada partição de hóspedes. Assim, um sistema operativo que executa numa partição de hóspedes tem acesso a dispositivos periféricos virtualizados que são fornecidos por serviços de virtualização que executam na partição raiz.

O hipervisor Azure é construído tendo em mente os seguintes objetivos de segurança:

| Objetivo | Origem |
|--|--|
| Isolamento | Uma política de segurança não obriga a transferência de informação entre VMs. Esta restrição requer capacidades no Gestor de Máquinas Virtuais (VMM) e hardware para isolamento de memória, dispositivos, rede e recursos geridos, como dados persistidos. |
| Integridade do VMM | Para alcançar a integridade geral do sistema, a integridade dos componentes individuais do hipervisor é estabelecida e mantida. |
| Integridade da plataforma | A integridade do hipervisor depende da integridade do hardware e software em que se baseia. Apesar de o hipervisor não ter controlo direto sobre a integridade da plataforma, o Azure conta com mecanismos de hardware e firmware, como o chip [Cerberus,](project-cerberus.md) para proteger e detetar a integridade da plataforma subjacente. O VMM e os hóspedes estão impedidos de funcionar se a integridade da plataforma estiver comprometida. |
| Acesso restrito | As funções de gestão são exercidas apenas por administradores autorizados ligados sobre ligações seguras. Um princípio de menor privilégio é aplicado por mecanismos de controlo de acesso baseados em funções (RBAC). |
| Auditoria | O Azure permite a capacidade de auditoria para capturar e proteger dados sobre o que acontece num sistema para que possa ser inspecionado mais tarde. |

A abordagem da Microsoft para endurecer o hipervisor Azure e o subsistema de virtualização pode ser dividida nas três categorias seguintes.

## <a name="strongly-defined-security-boundaries-enforced-by-the-hypervisor"></a>Limites de segurança fortemente definidos pelo hipervisor

O hipervisor Azure impõe múltiplas fronteiras de segurança entre:

- Partições "convidadas" virtualizadas e partição privilegiada ("anfitrião")
- Vários hóspedes
- Si mesmo e o anfitrião
- Si mesmo e todos os convidados

Confidencialidade, integridade e disponibilidade são asseguradas para os limites de segurança hipervisor. Os limites defendem-se contra uma série de ataques, incluindo fugas de informação de canal lateral, negação de serviço e elevação de privilégios.

A fronteira de segurança hipervisor também fornece segmentação entre inquilinos para tráfego de rede, dispositivos virtuais, armazenamento, recursos computatórios, e todos os outros recursos VM.

## <a name="defense-in-depth-exploit-mitigations"></a>Mitigações de exploração aprofundadas da defesa

No caso improvável de uma fronteira de segurança ter uma vulnerabilidade, o hipervisor Azure inclui várias camadas de mitigações, incluindo:

- Isolamento do processo baseado no hospedeiro que hospeda componentes cross-VM
- Segurança baseada na virtualização (VBS) para garantir a integridade dos componentes do modo utilizador e kernel de um mundo seguro
- Múltiplos níveis de mitigação de exploração. As mitigações incluem a aleatoriedade do layout do espaço de endereço (ASLR), prevenção da execução de dados (DEP), guarda de código arbitrário, integridade do fluxo de controlo e prevenção da corrupção de dados
- Inicialização automática de variáveis de pilha ao nível do compilador
- APIs kernel que automaticamente zero inicializam alocações de pilhas de núcleo feitas por Hyper-V

Estas mitigações destinam-se a tornar inviável o desenvolvimento de uma exploração para uma vulnerabilidade transversal vm.

## <a name="strong-security-assurance-processes"></a>Processos fortes de garantia de segurança

A superfície de ataque relacionada com o hipervisor inclui rede de software, dispositivos virtuais e todas as superfícies cross-VM. A superfície de ataque é rastreada através da integração automatizada de construção, o que desencadeia revisões periódicas de segurança.

Todas as superfícies de ataque VM são modeladas por ameaças, códigos revistos, confusos e testados pela nossa equipa RED por violações de fronteiras de segurança. A Microsoft tem um [programa de recompensas](https://www.microsoft.com/msrc/bounty-hyper-v) por bugs que paga um prémio por vulnerabilidades relevantes em versões de produtos elegíveis para o Microsoft Hyper-V.

> [!NOTE]
> Saiba mais sobre [processos fortes de garantia de segurança](../../azure-government/azure-secure-isolation-guidance.md#strong-security-assurance-processes) em Hiper-V.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que fazemos para impulsionar a integridade e segurança da plataforma, consulte:

- [Segurança firmware](firmware.md)
- [Bota segura](secure-boot.md)
- [Atestado de arranque e hospedeiro medido](measured-boot-host-attestation.md)
- [Projeto Cerberus](project-cerberus.md)
- [Encriptação inativa](encryption-atrest.md)