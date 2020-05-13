---
title: Notas de lançamento para o Centro de Segurança Azure
description: Uma descrição do que é novo e alterado no Centro de Segurança Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2020
ms.author: memildin
ms.openlocfilehash: bfe1e5d6a0c4171a262b36387f02be356fb1d72d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210899"
---
# <a name="whats-new-in-azure-security-center"></a>Quais as novidades no Centro de Segurança Azure?

A Azure Security está em desenvolvimento ativo e recebe melhorias de forma contínua. Para se manter atualizado com os desenvolvimentos mais recentes, esta página fornece-lhe informações sobre:

- Novas funcionalidades
- Correções de erros
- Funcionalidade preterida

Esta página é atualizada regularmente, por isso revisite-a com frequência. Se procura artigos com mais de seis meses, vai encontrá-los no [Arquivo para o que há de novo no Azure Security Center.](release-notes-archive.md)


## <a name="may-2020"></a>Maio de 2020

### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Alterações no acesso à máquina virtual (VM) just-in-time (VM)

O Security Center inclui uma funcionalidade opcional para proteger as portas de gestão dos seus VMs. Isto fornece uma defesa contra a forma mais comum de ataques de força bruta.

Esta atualização traz as seguintes alterações para esta funcionalidade:

- A recomendação que o aconselha a permitir o JIT num VM foi renomeada. Anteriormente, "O controlo de acesso à rede just-in-time deve ser aplicado em máquinas virtuais" é agora: "As portas de gestão de máquinas virtuais devem ser protegidas com controlo de acesso à rede just-in-time".

- A recomendação só foi desencadeada se existirem portos de gestão abertos.

[Saiba mais sobre a funcionalidade de acesso jit.](security-center-just-in-time.md)


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Recomendações personalizadas foram transferidas para um controlo de segurança separado

Um dos controlos de segurança introduzidos com a pontuação segura reforçada foi "Implementar as melhores práticas de segurança". Quaisquer recomendações personalizadas criadas para as suas subscrições foram automaticamente colocadas nesse controlo. 

Para facilitar a descoberta das suas recomendações personalizadas, mudámo-las para um controlo de segurança dedicado, "Recomendações personalizadas". Este controlo não tem impacto na sua pontuação segura.

Saiba mais sobre os controlos de segurança na [pontuação segura melhorada (pré-visualização) no Centro de Segurança Azure](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Alternado adicionado para ver recomendações em controlos ou como uma lista plana

Os controlos de segurança são grupos lógicos de recomendações de segurança relacionadas. Refletem as suas superfícies vulneráveis de ataque. Um controlo é um conjunto de recomendações de segurança, com instruções que o ajudam a implementar essas recomendações.

Para ver imediatamente como a sua organização está a garantir cada superfície de ataque individual, reveja as pontuações de cada controlo de segurança.

Por padrão, as suas recomendações são mostradas nos controlos de segurança. A partir desta atualização, também pode apresentá-los como uma lista. Para vê-los como uma lista simples ordenada pelo estado de saúde dos recursos afetados, utilize o novo "Grupo por controlos". O toggle está acima da lista no portal.

Os controlos de segurança - e este alternância - fazem parte da nova experiência de pontuação segura. Lembre-se de nos enviar o seu feedback de dentro do portal.

Saiba mais sobre os controlos de segurança na [pontuação segura melhorada (pré-visualização) no Centro de Segurança Azure](secure-score-security-controls.md).


### <a name="account-security-recommendations-moved-to-security-best-practices-security-control"></a>Recomendações de segurança da conta transferidas para o controlo de segurança das "boas práticas de segurança"

Um dos controlos de segurança introduzidos com a pontuação segura reforçada é "As melhores práticas de segurança". Quando uma recomendação está neste controlo, não afeta a pontuação segura. 

Com esta atualização, três recomendações saíram dos controlos em que foram inicialmente colocadas, e para este controlo das melhores práticas. Demos este passo porque determinámos que o risco destas três recomendações é menor do que se pensava inicialmente.

As recomendações são:

- O MFA deve ser ativado em contas com permissões de leitura na sua subscrição (originalmente no controlo "Enable MFA")
- As contas externas com permissões de leitura devem ser removidas da sua subscrição (originalmente no controlo "Gerir o acesso e as permissões")
- Um máximo de 3 proprietários deve ser designado para a sua subscrição (originalmente no controlo "Gerir o acesso e as permissões")

Saiba mais sobre os controlos de segurança na [pontuação segura melhorada (pré-visualização) no Centro de Segurança Azure](secure-score-security-controls.md).


### <a name="custom-policies-with-custom-metadata-generally-available"></a>Políticas personalizadas com metadados personalizados geralmente disponíveis

As políticas personalizadas fazem agora parte da experiência de recomendações do Security Center, pontuação segura e o dashboard de normas de conformidade regulamentar. Esta funcionalidade está agora geralmente disponível e permite-lhe alargar a cobertura de avaliação de segurança da sua organização no Security Center. 

Crie uma iniciativa personalizada na política do Azure, adicione políticas e a bordo ao Azure Security Center, e visualize-a como recomendações.

Agora também adicionámos a opção de editar os metadados de recomendação personalizada. As opções de metadados incluem gravidade, passos de reparação, informações de ameaças e muito mais.  

Saiba mais sobre o reforço das [suas recomendações personalizadas com informações detalhadas.](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information)


## <a name="april-2020"></a>Abril de 2020

### <a name="dynamic-compliance-packages-now-generally-available"></a>Pacotes dinâmicos de conformidade agora disponíveis

O painel de conformidade regulamentar do Azure Security Center inclui agora **pacotes dinâmicos** de conformidade (agora geralmente disponíveis) para acompanhar normas adicionais da indústria e regulamentares.

Os pacotes dinâmicos de conformidade podem ser adicionados ao seu grupo de subscrição ou gestão a partir da página de política de segurança do Security Center. Quando você está a bordo de um padrão ou benchmark, o padrão aparece no seu dashboard de conformidade regulamentar com todos os dados de conformidade associados mapeados como avaliações. Um relatório sumário de qualquer uma das normas que tenham sido a bordo estará disponível para download.

Agora, pode adicionar padrões como:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **NHS oficiais e do Reino Unido**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (novo)** (que é uma representação mais completa do Azure CIS 1.1.0)

Além disso, adicionámos recentemente o **Azure Security Benchmark,** as diretrizes específicas do Azure para a segurança e conformidade com base em quadros comuns de conformidade. Normas adicionais serão suportadas no painel de instrumentos à medida que forem disponibilizadas.  
 
[Saiba mais sobre personalizar o conjunto de normas no seu dashboard](update-regulatory-compliance-packages.md)de conformidade regulamentar.


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Recomendações de identidade agora incluídas no azure Security Center free tier

Recomendações de segurança para identidade e acesso no centro de segurança Azure estão agora geralmente disponíveis. Isto faz parte do esforço para que a gestão da postura de segurança na nuvem (CSPM) seja gratuita. Até agora, estas recomendações só estavam disponíveis no nível de preços padrão.

Exemplos de recomendações de identidade e acesso incluem:

- "A autenticação multifactor deve ser ativada em contas com permissões do proprietário na sua subscrição."
- "Um máximo de três proprietários deve ser designado para a sua subscrição."
- "As contas depreciadas devem ser removidas da sua subscrição."

Se tiver subscrições no nível de preços gratuitos, as suas pontuações seguras serão impactadas por esta alteração, uma vez que nunca foram avaliadas pela sua identidade e segurança de acesso.

[Saiba mais sobre as recomendações](recommendations-reference.md#recs-identity)de identidade e acesso.
[Saiba mais sobre monitorização da identidade e acesso.](security-center-identity-access.md)


## <a name="march-2020"></a>Março de 2020

### <a name="workflow-automation-is-now-generally-available"></a>A automatização do fluxo de trabalho está agora geralmente disponível

A funcionalidade de automatização de fluxos de trabalho do Azure Security Center está agora geralmente disponível. Use-o para ativar automaticamente aplicações lógicas em alertas de segurança e recomendações. Além disso, os gatilhos manuais estão disponíveis para alertas e todas as recomendações que tenham a opção de correção rápida disponível.

Todos os programas de segurança incluem múltiplos fluxos de trabalho para resposta a incidentes. Estes processos podem incluir notificar as partes interessadas relevantes, lançar um processo de gestão de alterações e aplicar medidas específicas de reparação. Os peritos em segurança recomendam que automatizar o máximo de passos possível desses procedimentos. A automatização reduz a sobrecarga e pode melhorar a sua segurança garantindo que os passos do processo são feitos de forma rápida, consistente e de acordo com os seus requisitos predefinidos.

Para obter mais informações sobre as capacidades automáticas e manuais do Centro de Segurança para executar os seus fluxos de trabalho, consulte a [automatização do fluxo de trabalho.](workflow-automation.md)

Para aprender sobre a criação de Apps Lógicas, consulte [aplicações lógicas do Azure.](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integração do Centro de Segurança Azure com o Windows Admin Center

Agora é possível mover os seus servidores Windows no local do Windows Admin Center diretamente para o Centro de Segurança Azure. O Security Center torna-se então o seu único painel de vidro para visualizar informações de segurança para todos os recursos do Windows Admin Center, incluindo servidores no local, máquinas virtuais e cargas de trabalho adicionais do PaaS.

Depois de mover um servidor do Windows Admin Center para o Azure Security Center, poderá:

- Consulte alertas de segurança e recomendações na extensão do Centro de Segurança do Windows Admin Center.
- Veja a postura de segurança e recupere informações detalhadas adicionais dos servidores geridos pelo Windows Admin Center no Centro de Segurança dentro do portal Azure (ou através de uma API).

Saiba mais sobre como integrar o [Azure Security Center com o Windows Admin Center](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Proteção para o Serviço Azure Kubernetes

O Azure Security Center está a expandir as suas funcionalidades de segurança de contentores para proteger o Serviço Azure Kubernetes (AKS).

A popular plataforma de código aberto Kubernetes foi adotada de forma tão ampla que agora é um padrão da indústria para a orquestração de contentores. Apesar desta implementação generalizada, ainda há falta de compreensão sobre como garantir um ambiente kubernetes. A defesa das superfícies de ataque de uma aplicação contentorizada requer conhecimentos especializados para garantir que a infraestrutura seja configurada de forma segura e constante para potenciais ameaças.

A defesa do Centro de Segurança inclui:

- **Descoberta e visibilidade** - Descoberta contínua de casos de AKS geridos dentro das subscrições registadas no Security Center.
- **Recomendações** de segurança - Recomendações acionáveis para ajudá-lo a cumprir as melhores práticas de segurança para a AKS. Estas recomendações estão incluídas na sua pontuação segura para garantir que são vistas como parte da postura de segurança da sua organização. Um exemplo de uma recomendação relacionada com a AKS que pode ver é "O controlo de acesso baseado em papéis deve ser usado para restringir o acesso a um cluster de serviços Kubernetes".
- **Proteção contra ameaças** - Através da análise contínua da sua implementação AKS, o Security Center alerta-o para ameaças e atividades maliciosas detetadas ao nível do hospedeiro e do cluster AKS.

Saiba mais sobre a integração de [Serviços Azure Kubernetes com o Security Center.](azure-kubernetes-service-integration.md)
[Saiba mais sobre as funcionalidades de segurança do contentor no Centro](container-security.md)de Segurança .


### <a name="improved-just-in-time-experience"></a>Melhor experiência just-in-time

As funcionalidades, operação e UI para as ferramentas just-in-time do Azure Security Center que protegem as suas portas de gestão foram melhoradas da seguinte forma: 

- Campo de **justificação** - Ao solicitar o acesso a uma máquina virtual (VM) através da página just-in-time do portal Azure, está disponível um novo campo opcional para introduzir uma justificação para o pedido. As informações introduzidas neste campo podem ser rastreadas no registo de atividade. 
- **Limpeza automática de regras redundantes just-in-time (JIT) -** Sempre que atualiza uma política de JIT, uma ferramenta de limpeza funciona automaticamente para verificar a validade de todo o seu conjunto de regras. A ferramenta procura desencontros entre regras na sua política e regras no NSG. Se a ferramenta de limpeza encontrar uma incompatibilidade, determina a causa e, quando é seguro fazê-lo, remove regras incorporadas que já não são necessárias. O limpador nunca apaga as regras que criaste. 

[Saiba mais sobre a funcionalidade de acesso jit.](security-center-just-in-time.md)


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Duas recomendações de segurança para aplicações web depreciadas

Estão a ser consideradas duas recomendações de segurança relacionadas com aplicações web: 

- As regras relativas às aplicações web em NsGs IaaS devem ser reforçadas.
    (Política conexa: As regras do SNS para aplicações web em IaaS devem ser reforçadas)

- O acesso aos Serviços de Aplicações deve ser restringido.
    (Política relacionada: O acesso aos Serviços de Aplicações deve ser restringido [pré-visualização])

Estas recomendações deixarão de figurar na lista de recomendações do Centro de Segurança. As políticas conexas deixarão de ser incluídas na iniciativa denominada "Falha do Centro de Segurança".

[Saiba mais sobre recomendações](recommendations-reference.md)de segurança.

## <a name="february-2020"></a>Fevereiro de 2020

### <a name="fileless-attack-detection-for-linux-is-now-in-preview"></a>Deteção de ataque sem ficheiros para linux está agora em pré-visualização

À medida que os atacantes aumentam empregam métodos mais furtivos para evitar a deteção, o Azure Security Center está a alargar a deteção de ataques sem ficheiros para o Linux, além do Windows. Os ataques sem ficheiroexploram vulnerabilidades de software, injetam cargas maliciosas em processos benignos do sistema e escondem-se na memória. Estas técnicas:

- minimizar ou eliminar vestígios de malware no disco
- reduzir consideravelmente as chances de deteção por soluções de pesquisa de malware baseadas em disco

Para combater esta ameaça, o Azure Security Center divulgou a deteção de ataques sem ficheiros para windows em outubro de 2018, e já alargou a deteção de ataques sem ficheiros no Linux. 


## <a name="january-2020"></a>Janeiro de 2020

### <a name="enhanced-secure-score"></a>Pontuação segura melhorada

Uma versão melhorada da funcionalidade de pontuação segura do Azure Security Center está agora disponível na pré-visualização. Nesta versão, várias recomendações são agrunadas em Controlos de Segurança que melhor refletem as suas superfícies de ataque vulneráveis (por exemplo, restringir o acesso a portas de gestão).

Familiarize-se com as alterações de pontuação seguras durante a fase de pré-visualização e determine outras reparações que o ajudarão a proteger ainda mais o seu ambiente.

Saiba mais em [Pontuação segura melhorada (pré-visualização) no Centro de Segurança Azure](secure-score-security-controls.md).