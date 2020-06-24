---
title: Notas de lançamento para O Centro de Segurança Azure
description: Uma descrição do que é novo e mudou no Centro de Segurança Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2020
ms.author: memildin
ms.openlocfilehash: f41b87f50dfac15c6228398a2c9d1c6ae470903d
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85260938"
---
# <a name="whats-new-in-azure-security-center"></a>O que há de novo no Centro de Segurança Azure?

A Azure Security está em desenvolvimento ativo e recebe melhorias continuamente. Para se manter atualizado com os desenvolvimentos mais recentes, esta página fornece-lhe informações sobre:

- Novas funcionalidades
- Correções de erros
- Funcionalidade preterida

Esta página é atualizada regularmente, por isso revisite-a com frequência. Se procura itens com mais de seis meses, vai encontrá-los no Arquivo para o [que há de novo no Azure Security Center.](release-notes-archive.md)


## <a name="june-2020"></a>Junho de 2020

### <a name="secure-score-api-preview"></a>API de pontuação segura (pré-visualização)

Pode agora aceder à sua pontuação através da [pontuação segura API](https://docs.microsoft.com/rest/api/securitycenter/securescores/) (atualmente em pré-visualização). Os métodos API fornecem a flexibilidade para consultar os dados e construir o seu próprio mecanismo de reporte das suas pontuações seguras ao longo do tempo. Por exemplo, pode utilizar a API **de Pontuações Seguras** para obter a pontuação de uma subscrição específica. Além disso, pode utilizar a API **de Controlos de Pontuação Segura** para listar os controlos de segurança e a pontuação atual das suas subscrições.

Por exemplo, ferramentas externas possibilitadas com a pontuação segura API, consulte [a área de pontuação segura da nossa comunidade GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Saiba mais sobre [os controlos de pontuação e segurança seguros no Azure Security Center](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>Segurança avançada de dados para máquinas SQL (Azul, outras nuvens e on-prem) (pré-visualização)

A segurança avançada de dados do Azure Security Center para máquinas SQL protege agora os SqL Servers alojados em Azure, em outros ambientes de nuvem e até mesmo máquinas no local. Isto alarga as proteções para os seus SqL Servers nativos do Azure para suportar totalmente ambientes híbridos.

A segurança avançada de dados fornece uma avaliação de vulnerabilidade e proteção avançada de ameaças para as suas máquinas SQL onde quer que estejam localizadas.

A configuração envolve dois passos:

1. Implantar o agente Log Analytics na máquina de anfitrião do seu SQL Server para fornecer a ligação à conta Azure.

1. Ativar o pacote opcional na página de preços e definições do Security Center.

Saiba mais sobre [a segurança avançada de dados para máquinas SQL](security-center-iaas-advanced-data.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Duas novas recomendações para implantar o agente Log Analytics nas máquinas Azure Arc (pré-visualização)

Foram adicionadas duas novas recomendações para ajudar a implantar o [Agente Desanalização](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent) do Log para as suas máquinas Azure Arc e garantir que estão protegidas pelo Azure Security Center:

- **O agente Log Analytics deve ser instalado nas suas máquinas Azure Arc baseadas no Windows (Pré-visualização)**
- **O agente Log Analytics deve ser instalado nas suas máquinas Azure Arc baseadas em Linux (Pré-visualização)**

Estas novas recomendações aparecerão nos mesmos quatro controlos de segurança que a recomendação existente (relacionada), o **agente de monitorização deve ser instalado nas suas máquinas**: remediar as configurações de segurança, aplicar o controlo adaptativo da aplicação de aplicações, aplicar atualizações do sistema e permitir a proteção do ponto final.

As recomendações também incluem a capacidade de correção rápida para ajudar a acelerar o processo de implementação. 

Saiba mais sobre estas duas novas recomendações na tabela [de recomendações computacional e de aplicações.](recommendations-reference.md#recs-computeapp)

Saiba mais sobre como o Azure Security Center utiliza o agente em [O que é o agente Log Analytics?](https://docs.microsoft.com/azure/security-center/faq-data-collection-agents#what-is-the-log-analytics-agent)

Saiba mais sobre [extensões para máquinas Azure Arc](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal).


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Nova recomendação para a utilização de NSGs para proteger máquinas virtuais não-orientadas para a Internet

O controlo de segurança "implementar as melhores práticas de segurança" inclui agora a seguinte nova recomendação:

- **Máquinas virtuais não orientadas para a Internet devem ser protegidas com grupos de segurança de rede**

Uma recomendação existente, **as máquinas virtuais viradas para a Internet devem ser protegidas com grupos**de segurança de rede, não distinguindo entre VMs virados para a Internet e não virados para a Internet. Para ambos, uma recomendação de alta gravidade foi gerada se um VM não fosse atribuído a um grupo de segurança de rede. Esta nova recomendação separa as máquinas que não se estamos a passar pela Internet para reduzir os falsos positivos e evitar alertas desnecessários de alta gravidade.

Saiba mais na tabela [de recomendações da Rede.](recommendations-reference.md#recs-network)



## <a name="may-2020"></a>Maio de 2020


### <a name="alert-suppression-rules-preview"></a>Regras de supressão de alerta (pré-visualização)

Esta nova funcionalidade (atualmente em pré-visualização) ajuda a reduzir a fadiga do alerta. Use regras para ocultar automaticamente alertas que são conhecidos por serem inócuos ou relacionados com atividades normais na sua organização. Isto permite-lhe focar nas ameaças mais relevantes. 

Os alertas que correspondam às suas regras de supressão ativadas ainda serão gerados, mas o seu estado será definido para ser rejeitado. Pode ver o estado no portal Azure ou no entanto acede aos alertas de segurança do Seu Centro de Segurança.

As regras de supressão definem os critérios para os quais os alertas devem ser automaticamente dispensados. Normalmente, usaria uma regra de supressão para:

- suprimir alertas que identificou como falsos positivos

- suprimir alertas que estão sendo desencadeados com demasiada frequência para ser útil

Saiba mais sobre [a supressão de alertas da proteção contra ameaças do Azure Security Center](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>A avaliação da vulnerabilidade da máquina virtual está agora geralmente disponível

O nível padrão do Security Center inclui agora uma avaliação integrada de vulnerabilidade para máquinas virtuais sem qualquer custo adicional. Esta extensão é alimentada pela Qualys, mas reporta as suas descobertas diretamente ao Centro de Segurança. Você não precisa de uma licença Qualys ou mesmo uma conta Qualys - tudo é tratado perfeitamente dentro do Centro de Segurança.

A nova solução pode digitalizar continuamente as suas máquinas virtuais para encontrar vulnerabilidades e apresentar as descobertas no Centro de Segurança. 

Para implementar a solução, utilize a nova recomendação de segurança:

"Permitir a solução de avaliação de vulnerabilidade incorporada em máquinas virtuais (alimentadas por Qualys)"

Saiba mais sobre [a avaliação integrada de vulnerabilidade do Security Center para máquinas virtuais.](built-in-vulnerability-assessment.md)



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Alterações no acesso à máquina virtual (JIT) (JIT)

O Security Center inclui uma funcionalidade opcional para proteger as portas de gestão dos seus VMs. Isto fornece uma defesa contra a forma mais comum de ataques de força bruta.

Esta atualização traz as seguintes alterações a esta funcionalidade:

- A recomendação que o aconselha a ativar o JIT num VM foi renomeada. Anteriormente, "O controlo de acesso à rede just-in-time deve ser aplicado em máquinas virtuais" é agora: "As portas de gestão das máquinas virtuais devem ser protegidas com controlo de acesso à rede just-in-time".

- A recomendação só foi desencadeada se existirem portos de gestão abertos.

Saiba mais sobre [a funcionalidade de acesso JIT.](security-center-just-in-time.md)


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Recomendações personalizadas foram transferidas para um controlo de segurança separado

Um dos controlos de segurança introduzidos com a pontuação de segurança reforçada foi "Implementar as melhores práticas de segurança". Quaisquer recomendações personalizadas criadas para as suas subscrições foram automaticamente colocadas nesse controlo. 

Para facilitar a procura das suas recomendações personalizadas, transferimo-las para um controlo de segurança dedicado, "Recomendações personalizadas". Este controlo não tem impacto na sua pontuação segura.

Saiba mais sobre os controlos de segurança na [pontuação de segurança melhorada (pré-visualização) no Azure Security Center](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Toggle adicionado para ver recomendações em controlos ou como uma lista plana

Os controlos de segurança são grupos lógicos de recomendações de segurança relacionadas. Refletem as suas superfícies vulneráveis de ataque. Um controlo é um conjunto de recomendações de segurança, com instruções que o ajudam a implementar essas recomendações.

Para ver imediatamente o quão bem a sua organização está a proteger cada superfície de ataque individual, reveja as pontuações para cada controlo de segurança.

Por predefinição, as suas recomendações são apresentadas nos controlos de segurança. A partir desta atualização também pode exibi-los como uma lista. Para vê-los como uma lista simples classificada pelo estado de saúde dos recursos afetados, utilize o novo "Grupo por controlos". O toggle está acima da lista no portal.

Os controlos de segurança - e este alternador - fazem parte da nova experiência de pontuação segura. Lembre-se de enviar-nos o seu feedback de dentro do portal.

Saiba mais sobre os controlos de segurança na [pontuação de segurança melhorada (pré-visualização) no Azure Security Center](secure-score-security-controls.md).


### <a name="expanded-security-control-implement-security-best-practices"></a>Controlo de segurança alargado "Implementar as melhores práticas de segurança" 

Um dos controlos de segurança introduzidos com a pontuação de segurança reforçada é "Implementar as melhores práticas de segurança". Quando uma recomendação está neste controlo, não afeta a pontuação segura. 

Com esta atualização, três recomendações saíram dos controlos em que foram inicialmente colocados, e para este controlo das boas práticas. Demos este passo porque determinámos que o risco destas três recomendações é menor do que se pensava inicialmente.

Além disso, foram introduzidas duas novas recomendações e acrescentadas a este controlo.

As três recomendações que se moveram são:

- **O MFA deve ser ativado em contas com permissões de leitura na sua subscrição** (originalmente no controlo "Enable MFA")
- **As contas externas com permissões de leitura devem ser removidas da sua subscrição** (originalmente no controlo "Gerir o acesso e permissões")
- **Um máximo de 3 proprietários deve ser designado para a sua subscrição** (originalmente no controlo "Gerir o acesso e permissões")

As duas novas recomendações adicionadas ao controlo são:

- **A extensão de configuração do hóspede deve ser instalada em máquinas virtuais do Windows (Preview)** - A utilização da [Configuração do Hóspede Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) proporciona visibilidade dentro de máquinas virtuais para configurações de servidor e aplicação (apenas Windows).

- A **Proteção de Exploração do Windows Defender deve ser ativada nas suas máquinas (Preview)** - O Windows Defender Exploit Guard aproveita o agente de configuração de convidados Azure Policy. A Exploit Guard tem quatro componentes que são projetados para bloquear dispositivos contra uma grande variedade de vetores de ataque e comportamentos de bloqueio geralmente usados em ataques de malware, ao mesmo tempo que permite às empresas equilibrar os seus requisitos de risco de segurança e produtividade (apenas Windows).

Saiba mais sobre o Windows Defender Exploit Guard in [Create e implemente uma política de Exploit Guard](https://docs.microsoft.com/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Saiba mais sobre os controlos de segurança na [pontuação de segurança melhorada (pré-visualização)](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>As políticas personalizadas com metadados personalizados estão agora geralmente disponíveis

As políticas personalizadas fazem agora parte da experiência de recomendações do Centro de Segurança, pontuação segura e o painel de padrões de conformidade regulamentar. Esta funcionalidade está agora geralmente disponível e permite-lhe alargar a cobertura de avaliação de segurança da sua organização no Security Center. 

Crie uma iniciativa personalizada na política Azure, adicione políticas a ela e embarque-a no Azure Security Center, e visualize-a como recomendações.

Agora também acrescentámos a opção de editar os metadados de recomendação personalizados. As opções de metadados incluem gravidade, medidas de reparação, informações de ameaças e muito mais.  

Saiba mais sobre [o reforço das suas recomendações personalizadas com informações detalhadas.](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information)



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Capacidades de análise de despejo de colisão migram para deteção de ataque sem ficheiros 

Estamos a integrar as capacidades de deteção de falhas do Windows (CDA) na [deteção de ataques sem ficheiros.](https://docs.microsoft.com/azure/security-center/threat-protection#windows-fileless) A análise de deteção de ataques sem ficheiros traz versões melhoradas dos seguintes alertas de segurança para máquinas Windows: Injeção de código descoberta, Módulo do Windows de mascarado detetado, Shellcode descoberto e segmento de código suspeito detetado.

Alguns dos benefícios desta transição:

- **Deteção proativa e oportuna de malware** - A abordagem do CDA envolveu esperar que ocorresse um acidente e, em seguida, executar análises para encontrar artefactos maliciosos. A deteção de ataques sem ficheiros traz identificação proactiva de ameaças na memória enquanto estão em execução. 

- **Alertas enriquecidos** - Os alertas de segurança da deteção de ataques sem ficheiros incluem enriquecimentos que não estão disponíveis no CDA, como a informação de ligações de rede ativa. 

- **Agregação de alerta** - Quando o CDA detetou vários padrões de ataque dentro de uma única lixeira de colisão, desencadeou vários alertas de segurança. A deteção de ataques sem ficheiro combina todos os padrões de ataque identificados do mesmo processo num único alerta, removendo a necessidade de correlacionar vários alertas.

- **Requisitos reduzidos no seu espaço de trabalho Log Analytics** - Os despejos de crash que contenham dados potencialmente sensíveis deixarão de ser enviados para o seu espaço de trabalho Log Analytics.



## <a name="april-2020"></a>Abril de 2020

### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Pacotes de conformidade dinâmicos estão agora geralmente disponíveis

O painel de conformidade regulamentar do Azure Security Center inclui agora **pacotes de conformidade dinâmicos** (agora geralmente disponíveis) para acompanhar normas adicionais da indústria e regulamentares.

Pacotes de conformidade dinâmicos podem ser adicionados ao seu grupo de subscrição ou gestão a partir da página de política de segurança do Centro de Segurança. Quando você tem bordo um padrão ou benchmark, o padrão aparece no seu painel de conformidade regulamentar com todos os dados de conformidade associados mapeados como avaliações. Um relatório resumo de qualquer uma das normas que foram a bordo estará disponível para download.

Agora, pode adicionar padrões como:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK Official and UK NHS**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (novo)** (que é uma representação mais completa do Azure CIS 1.1.0)

Além disso, adicionámos recentemente o **Azure Security Benchmark**, as diretrizes específicas para segurança e conformidade da Microsoft para as melhores práticas de segurança e conformidade baseadas em quadros comuns de conformidade. As normas adicionais serão suportadas no painel de instrumentos à medida que se tornarem disponíveis.  
 
Saiba mais sobre [a personalização do conjunto de normas no seu painel de conformidade regulamentar.](update-regulatory-compliance-packages.md)


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Recomendações de identidade agora incluídas no free tier do Azure Security Center

Recomendações de segurança para identidade e acesso no free tier do Azure Security Center estão agora geralmente disponíveis. Isto faz parte do esforço para tornar gratuita a gestão da postura de segurança na nuvem (CSPM). Até agora, estas recomendações só estavam disponíveis no nível normal de preços.

Exemplos de recomendações de identidade e acesso incluem:

- "A autenticação multifactor deve ser ativada em contas com permissões do proprietário na sua subscrição."
- "Um máximo de três proprietários deve ser designado para a sua assinatura."
- "As contas precotados devem ser removidas da sua subscrição."

Se tiver subscrições no nível de preços gratuitos, as suas pontuações seguras serão impactadas por esta alteração, uma vez que nunca foram avaliadas pela sua identidade e segurança de acesso.

Saiba mais sobre [recomendações de identidade e acesso.](recommendations-reference.md#recs-identity)

Saiba mais sobre [a monitorização da identidade e do acesso.](security-center-identity-access.md)


## <a name="march-2020"></a>Março de 2020

### <a name="workflow-automation-is-now-generally-available"></a>A automatização do fluxo de trabalho está agora geralmente disponível

A funcionalidade de automatização do fluxo de trabalho do Azure Security Center está agora geralmente disponível. Utilize-o para ativar automaticamente as Aplicações Lógicas em alertas de segurança e recomendações. Além disso, os gatilhos manuais estão disponíveis para alertas e todas as recomendações que têm a opção de correção rápida disponível.

Todos os programas de segurança incluem vários fluxos de trabalho para resposta a incidentes. Estes processos podem incluir notificar as partes interessadas relevantes, lançar um processo de gestão de mudanças e aplicar medidas de reparação específicas. Os peritos em segurança recomendam que automatize o máximo de passos possível. A automatização reduz a sobrecarga e pode melhorar a sua segurança garantindo que os passos do processo são feitos de forma rápida, consistente e de acordo com os seus requisitos predefinidos.

Para obter mais informações sobre as capacidades automáticas e manuais do Centro de Segurança para executar os seus fluxos de trabalho, consulte [a automatização do fluxo de trabalho.](workflow-automation.md)

Saiba mais sobre [a criação de Aplicações Lógicas.](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integração do Centro de Segurança Azure com o Windows Admin Center

Agora é possível mover os seus servidores Windows no local do Windows Admin Center diretamente para o Centro de Segurança Azure. O Security Center torna-se então o seu único painel de vidro para visualizar informações de segurança para todos os seus recursos do Windows Admin Center, incluindo servidores no local, máquinas virtuais e cargas de trabalho adicionais do PaaS.

Depois de mover um servidor do Windows Admin Center para o Azure Security Center, poderá:

- Ver alertas de segurança e recomendações na extensão do Centro de Segurança do Windows Admin Center.
- Veja a postura de segurança e recupere informações detalhadas adicionais dos servidores geridos do Windows Admin Center no Centro de Segurança dentro do portal Azure (ou através de uma API).

Saiba mais sobre [como integrar o Azure Security Center com o Windows Admin Center](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Proteção para o Serviço Azure Kubernetes

O Azure Security Center está a expandir as suas funcionalidades de segurança para proteger o Serviço Azure Kubernetes (AKS).

A popular plataforma de código aberto Kubernetes foi adotada de forma tão ampla que agora é um padrão da indústria para a orquestração de contentores. Apesar desta implementação generalizada, ainda há uma falta de compreensão sobre como garantir um ambiente Kubernetes. A defesa das superfícies de ataque de uma aplicação contentorizada requer conhecimentos especializados para garantir que a infraestrutura seja configurada de forma segura e constante para potenciais ameaças.

A defesa do Centro de Segurança inclui:

- **Descoberta e visibilidade** - Descoberta contínua de instâncias AKS geridas dentro das subscrições registadas no Centro de Segurança.
- **Recomendações de segurança** - Recomendações acccáveis para ajudá-lo a cumprir as melhores práticas de segurança para a AKS. Estas recomendações estão incluídas na sua pontuação segura para garantir que são vistas como parte da postura de segurança da sua organização. Um exemplo de uma recomendação relacionada com a AKS que pode ver é "O controlo de acesso baseado em papéis deve ser usado para restringir o acesso a um cluster de serviços Kubernetes".
- **Proteção contra ameaças** - Através de uma análise contínua da sua implementação AKS, o Security Center alerta-o para ameaças e atividades maliciosas detetadas ao nível do cluster hospedeiro e AKS.

Saiba mais sobre [a integração dos Serviços Azure Kubernetes com o Security Center.](azure-kubernetes-service-integration.md)

Saiba mais sobre [as funcionalidades de segurança do contentor no Centro de Segurança.](container-security.md)


### <a name="improved-just-in-time-experience"></a>Experiência just-in-time melhorada

As funcionalidades, operação e UI para as ferramentas just-in-time do Azure Security Center que protegem as suas portas de gestão foram melhoradas da seguinte forma: 

- **Campo de justificação** - Ao solicitar o acesso a uma máquina virtual (VM) através da página just-in-time do portal Azure, está disponível um novo campo opcional para introduzir uma justificação para o pedido. As informações inseridas neste campo podem ser rastreadas no registo de atividades. 
- **Limpeza automática das regras redundantes just-in-time (JIT)** - Sempre que atualiza uma política JIT, uma ferramenta de limpeza funciona automaticamente para verificar a validade de todo o seu conjunto de regras. A ferramenta procura desencontros entre regras na sua política e regras no NSG. Se a ferramenta de limpeza encontrar um desfasamento, determina a causa e, quando é seguro fazê-lo, remove regras incorporadas que já não são necessárias. O aspirador nunca apaga as regras que criaste. 

Saiba mais sobre [a funcionalidade de acesso JIT.](security-center-just-in-time.md)


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Duas recomendações de segurança para aplicações web depreciadas

Duas recomendações de segurança relacionadas com aplicações web estão a ser depreciadas: 

- As regras para aplicações web em IAAS NSGs devem ser endurecidas.
    (Política relacionada: as regras do INE para aplicações web no IAAS devem ser endurecidas)

- O acesso aos Serviços de Aplicações deve ser restringido.
    (Política relacionada: o acesso aos Serviços de Aplicações deve ser restringido [pré-visualização])

Estas recomendações deixarão de constar da lista de recomendações do Centro de Segurança. As políticas relacionadas deixarão de ser incluídas na iniciativa denominada "Security Center Default".

Saiba mais sobre [recomendações de segurança.](recommendations-reference.md)



## <a name="february-2020"></a>Fevereiro de 2020

### <a name="fileless-attack-detection-for-linux-preview"></a>Deteção de ataque sem ficheiro para Linux (pré-visualização)

À medida que os atacantes aumentam empregam métodos mais furtivos para evitar a deteção, o Azure Security Center está a alargar a deteção de ataques sem ficheiros para o Linux, além do Windows. Os ataques sem ficheiros exploram vulnerabilidades de software, injetam cargas maliciosas em processos benignos do sistema e escondem-se na memória. Estas técnicas:

- minimizar ou eliminar vestígios de malware no disco
- reduzir consideravelmente as chances de deteção por soluções de digitalização de malware baseadas em disco

Para combater esta ameaça, o Azure Security Center lançou a deteção de ataques sem ficheiros para o Windows em outubro de 2018, e agora alargou também a deteção de ataques sem ficheiros no Linux. 


## <a name="january-2020"></a>Janeiro de 2020

### <a name="enhanced-secure-score-preview"></a>Pontuação segura melhorada (pré-visualização)

Uma versão melhorada da funcionalidade de pontuação segura do Azure Security Center está agora disponível na pré-visualização. Nesta versão, várias recomendações são agrupadas em Controlos de Segurança que melhor refletem as suas superfícies de ataque vulneráveis (por exemplo, restringir o acesso a portas de gestão).

Familiarize-se com as alterações de pontuação seguras durante a fase de pré-visualização e determine outras reparações que o ajudarão a proteger ainda mais o seu ambiente.

Saiba mais sobre [a pontuação de segurança melhorada (pré-visualização) no Azure Security Center](secure-score-security-controls.md).