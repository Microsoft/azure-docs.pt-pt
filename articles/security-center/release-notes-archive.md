---
title: Arquivo do que há de novo no Centro de Segurança Azure
description: Uma descrição do que é novo e mudado no Centro de Segurança Azure de há seis meses e mais cedo.
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
ms.openlocfilehash: 09b23e9951c6da597b82d2e012a57f2c93410a5d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91289808"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Arquivo para o que há de novo no Centro de Segurança Azure?

A página primária [O que há de novo no Azure Security Center?](release-notes.md)

Esta página fornece-lhe informações sobre:

- Novas funcionalidades
- Correções de erros
- Funcionalidade preterida


## <a name="march-2020"></a>Março de 2020

As atualizações em março incluem:

- [A automatização do fluxo de trabalho está agora geralmente disponível](#workflow-automation-is-now-generally-available)
- [Integração do Centro de Segurança Azure com o Windows Admin Center](#integration-of-azure-security-center-with-windows-admin-center)
- [Proteção para o Serviço Azure Kubernetes](#protection-for-azure-kubernetes-service)
- [Experiência just-in-time melhorada](#improved-just-in-time-experience)
- [Duas recomendações de segurança para aplicações web depreciadas](#two-security-recommendations-for-web-applications-deprecated)


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

Saiba mais sobre [a integração dos Serviços Azure Kubernetes com o Security Center.](defender-for-kubernetes-introduction.md)

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

Saiba mais sobre [a pontuação de segurança melhorada (pré-visualização)](secure-score-security-controls.md).



## <a name="november-2019"></a>Novembro de 2019

As atualizações em novembro incluem:
 - [Proteção contra ameaças para o cofre-chave de Azure nas regiões da América do Norte (pré-visualização)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [Proteção de ameaças para armazenamento de Azure inclui rastreio de reputação de malware](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Automatização do fluxo de trabalho com Apps Lógicas (pré-visualização)](#workflow-automation-with-logic-apps-preview)
 - [Quick Fix para recursos a granel geralmente disponíveis](#quick-fix-for-bulk-resources-generally-available)
 - [Digitalizar imagens de contentores para vulnerabilidades (pré-visualização)](#scan-container-images-for-vulnerabilities-preview)
 - [Normas regulamentares adicionais de conformidade (pré-visualização)](#additional-regulatory-compliance-standards-preview)
 - [Proteção contra ameaças para o serviço Azure Kubernetes (pré-visualização)](#threat-protection-for-azure-kubernetes-service-preview)
 - [Avaliação da vulnerabilidade da máquina virtual (pré-visualização)](#virtual-machine-vulnerability-assessment-preview)
 - [Segurança avançada de dados para servidores SQL em Azure Virtual Machines (pré-visualização)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [Suporte para políticas personalizadas (pré-visualização)](#support-for-custom-policies-preview)
 - [Ampliação da cobertura do Centro de Segurança Azure com plataforma para comunidade e parceiros](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [Integrações avançadas com exportação de recomendações e alertas (pré-visualização)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Servidores a bordo a bordo para o Centro de Segurança do Windows Admin Center (pré-visualização)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>Proteção contra ameaças para o cofre-chave Azure nas regiões da América do Norte (pré-visualização)

O Azure Key Vault é um serviço essencial para proteger dados e melhorar o desempenho das aplicações em nuvem, oferecendo a capacidade de gerir centralmente chaves, segredos, chaves criptográficas e políticas na nuvem. Uma vez que o Azure Key Vault armazena dados críticos sensíveis e empresariais, requer segurança máxima para os cofres-chave e os dados armazenados nos mesmos.

O suporte do Azure Security Center para a Proteção de Ameaças para o Cofre de Chaves Azure fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar cofres chave. Esta nova camada de proteção permite que os clientes abordem ameaças contra os seus cofres-chave sem serem peritos em segurança ou gerirem sistemas de monitorização de segurança. A funcionalidade está em pré-visualização pública nas regiões da América do Norte.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Proteção de ameaças para armazenamento de Azure inclui rastreio de reputação de malware

A proteção contra ameaças para o Azure Storage oferece novas deteções alimentadas pela Microsoft Threat Intelligence para detetar uploads de malware para o Azure Storage usando análises de reputação de haxixe e acesso suspeito a partir de um nó de saída tor ativo (um proxy anonimizador). Agora pode ver malware detetado através de contas de armazenamento usando o Azure Security Center.


### <a name="workflow-automation-with-logic-apps-preview"></a>Automatização do fluxo de trabalho com Apps Lógicas (pré-visualização)

Organizações com segurança gerida centralmente e TI/operações implementam processos internos de fluxo de trabalho para impulsionar a ação necessária dentro da organização quando as discrepâncias são descobertas nos seus ambientes. Em muitos casos, estes fluxos de trabalho são processos repetíveis e a automatização pode agilizar muito os processos dentro da organização.

Hoje estamos a introduzir uma nova capacidade no Security Center que permite aos clientes criar configurações de automação alavancando as Aplicações Lógicas Azure e criar políticas que as desencadeiem automaticamente com base em resultados específicos da ASC, como Recomendações ou Alertas. A Azure Logic App pode ser configurada para fazer qualquer ação personalizada suportada pela vasta comunidade de conectores Logic App, ou usar um dos modelos fornecidos pelo Security Center, como o envio de um e-mail ou a abertura de um bilhete serviceNow™.

Para obter mais informações sobre as capacidades automáticas e manuais do Centro de Segurança para executar os seus fluxos de trabalho, consulte [a automatização do fluxo de trabalho.](workflow-automation.md)

Para aprender a criar Aplicações Lógicas, consulte [as Aplicações Lógicas Azure.](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)


### <a name="quick-fix-for-bulk-resources-generally-available"></a>Quick Fix para recursos a granel geralmente disponíveis

Com as muitas tarefas que um utilizador é dado como parte do Secure Score, a capacidade de remediar eficazmente problemas através de uma grande frota pode tornar-se um desafio.

Para simplificar a remediação de configurações erradas de segurança e para ser capaz de remediar rapidamente recomendações sobre uma grande parte dos recursos e melhorar a sua pontuação segura, utilize a remediação quick Fix.

Esta operação permitir-lhe-á selecionar os recursos a que pretende aplicar a remediação e lançar uma ação de reparação que configurará a definição em seu nome.

A correção rápida está geralmente disponível hoje em dia clientes como parte da página de recomendações do Centro de Segurança.

Veja quais as recomendações que têm uma correção rápida ativada no [guia de referência às recomendações de segurança](recommendations-reference.md).


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Digitalizar imagens de contentores para vulnerabilidades (pré-visualização)

O Centro de Segurança Azure pode agora digitalizar imagens de contentores no Registo de Contentores Azure para vulnerabilidades.

A digitalização da imagem funciona analisando o ficheiro de imagem do recipiente, verificando depois se existem vulnerabilidades conhecidas (alimentadas por Qualys).

A própria varredura é ativada automaticamente ao empurrar novas imagens de contentores para o Registo do Contentor de Azure. As vulnerabilidades encontradas surgirão como recomendações do Security Center e incluídas no Azure Secure Score juntamente com informações sobre como corrigi-las para reduzir a superfície de ataque que permitiram.


### <a name="additional-regulatory-compliance-standards-preview"></a>Normas regulamentares adicionais de conformidade (pré-visualização)

O painel de conformidade regulamentar fornece informações sobre a sua postura de conformidade com base nas avaliações do Centro de Segurança. O painel de instrumentos mostra como o seu ambiente cumpre os controlos e requisitos designados por normas regulamentares específicas e parâmetros de referência da indústria e fornece recomendações prescritivas para como lidar com estes requisitos.

Até agora, o painel de conformidade regulamentar apoiou quatro normas incorporadas: Azure CIS 1.1.0, PCI-DSS, ISO 27001 e SOC-TSP. Estamos agora a anunciar a divulgação pública de normas adicionais apoiadas: NIST SP 800-53 R4, SWIFT CSP CSCF v2020, Canadá Federal PBMM e Uk Official juntamente com o UK NHS. Estamos também a lançar uma versão atualizada do Azure CIS 1.1.0, abrangendo mais controlos a partir da extensibilidade padrão e melhorando a extensibilidade.

[Saiba mais sobre a personalização do conjunto de normas no seu painel de conformidade regulamentar.](update-regulatory-compliance-packages.md)


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Proteção contra ameaças para o serviço Azure Kubernetes (pré-visualização)

A Kubernetes está rapidamente a tornar-se o novo padrão para implementar e gerir software na nuvem. Poucas pessoas têm uma vasta experiência com Kubernetes e muitas apenas se concentram na engenharia e administração gerais e ignoram o aspeto da segurança. O ambiente de Kubernetes precisa de ser configurado cuidadosamente para ser seguro, certificando-se de que nenhuma porta de superfície de ataque focada no contentor não é deixada aberta para os atacantes. O Security Center está a expandir o seu suporte no espaço de contentores para um dos serviços que mais cresce em Azure - Azure Kubernetes Service (AKS).

As novas capacidades desta versão de pré-visualização pública incluem:

- **Discovery & Visibility** - Descoberta contínua de instâncias AKS geridas dentro das subscrições registadas do Security Center.
- **Recomendações secure Score** - Itens acionáveis para ajudar os clientes a cumprir as melhores práticas de segurança em AKS como parte da Pontuação Segura do cliente, tais como "O Controlo de Acesso Baseado em Função deve ser usado para restringir o acesso a um Cluster de Serviços Kubernetes".
- **Deteção de Ameaças** - Analítica baseada em hospedeiros e clusters, tais como "Um recipiente privilegiado detetado".


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Avaliação da vulnerabilidade da máquina virtual (pré-visualização)

As aplicações que são instaladas em máquinas virtuais podem muitas vezes ter vulnerabilidades que podem levar a uma violação da máquina virtual. Estamos anunciando que o nível padrão do Centro de Segurança inclui avaliação de vulnerabilidade incorporada para máquinas virtuais sem qualquer taxa adicional. A avaliação de vulnerabilidade, alimentada pela Qualys na pré-visualização pública, permitirá analisar continuamente todas as aplicações instaladas numa máquina virtual para encontrar aplicações vulneráveis e apresentar as conclusões na experiência do portal do Security Center. O Security Center cuida de todas as operações de implantação para que não seja necessário trabalho extra do utilizador. Para o futuro, estamos a planear fornecer opções de avaliação de vulnerabilidades para apoiar as necessidades de negócio únicas dos nossos clientes.

[Saiba mais sobre avaliações de vulnerabilidade para as suas Máquinas Virtuais Azure.](deploy-vulnerability-assessment-vm.md)


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Segurança avançada de dados para servidores SQL em Azure Virtual Machines (pré-visualização)

O apoio do Azure Security Center à proteção de ameaças e avaliação de vulnerabilidade para DBs SQL em execução em IaaS VMs está agora em pré-visualização.

A [Avaliação da vulnerabilidade](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) é um serviço fácil de configurar que pode detetar, controlar e ajudar a corrigir potenciais vulnerabilidades da base de dados. Ele fornece visibilidade na sua postura de segurança como parte da pontuação segura do Azure e inclui os passos para resolver problemas de segurança e melhorar as fortificações da sua base de dados.

[A proteção avançada de ameaças](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) deteta atividades anómalas que indiquem tentativas incomuns e potencialmente nocivas de aceder ou explorar o seu servidor SQL. Monitoriza continuamente a sua base de dados para atividades suspeitas e fornece alertas de segurança orientados para a ação em padrões de acesso a bases de dados anómalas. Estes alertas fornecem detalhes de atividades suspeitas e ações recomendadas para investigar e mitigar a ameaça.


### <a name="support-for-custom-policies-preview"></a>Suporte para políticas personalizadas (pré-visualização)

O Azure Security Center suporta agora políticas personalizadas (em pré-visualização).

Os nossos clientes têm vindo a querer alargar a cobertura das suas avaliações de segurança no Centro de Segurança com as suas próprias avaliações de segurança baseadas em políticas que criam na Azure Policy. Com o apoio a políticas personalizadas, isso é agora possível.

Estas novas políticas farão parte da experiência de recomendações do Centro de Segurança, Da Pontuação Segura e do painel de normas de conformidade regulamentar. Com o apoio a políticas personalizadas, agora é capaz de criar uma iniciativa personalizada na Política Azure, em seguida, adicioná-la como uma política no Centro de Segurança e visualizá-la como uma recomendação.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Ampliação da cobertura do Centro de Segurança Azure com plataforma para comunidade e parceiros

Utilize o Security Center para receber recomendações não só da Microsoft, mas também de soluções existentes de parceiros como Check Point, Tenable e CyberArk com muitas mais integrações a chegar.  O simples fluxo de embarque do Security Center pode ligar as suas soluções existentes ao Security Center, permitindo-lhe visualizar as suas recomendações de postura de segurança num único local, executar relatórios unificados e aproveitar todas as capacidades do Security Center contra as recomendações incorporadas e parceiras. Também pode exportar recomendações do Security Center para produtos parceiros.

[Saiba mais sobre a Microsoft Intelligent Security Association](https://www.microsoft.com/security/partnerships/intelligent-security-association).



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Integrações avançadas com exportação de recomendações e alertas (pré-visualização)

De forma a permitir cenários de nível empresarial no topo do Security Center, agora é possível consumir alertas e recomendações do Centro de Segurança em locais adicionais, exceto o portal Azure ou API. Estes podem ser exportados diretamente para um Centro de Eventos e para log analytics espaços de trabalho. Aqui estão alguns fluxos de trabalho que pode criar em torno destas novas capacidades:

- Com a exportação para log analytics espaço de trabalho, você pode criar dashboards personalizados com Power BI.
- Com a exportação para o Event Hub, poderá exportar alertas e recomendações do Security Center para os seus SIEMs de terceiros, para uma solução de terceiros em tempo real, ou Azure Data Explorer.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Servidores a bordo a bordo para o Centro de Segurança do Windows Admin Center (pré-visualização)

O Windows Admin Center é um portal de gestão para servidores do Windows que não estão implantados no Azure, oferecendo-lhes várias capacidades de gestão Azure, como backup e atualizações do sistema. Recentemente, adicionámos a capacidade de embarcar nestes servidores não-Azure para serem protegidos pela ASC diretamente da experiência do Windows Admin Center.

Com esta nova experiência, os utilizadores estarão a bordo de um servidor WAC para o Azure Security Center e permitirão visualizar os seus alertas de segurança e recomendações diretamente na experiência do Windows Admin Center.


## <a name="september-2019"></a>Setembro de 2019

As atualizações em setembro incluem:

 - [Gerir regras com melhorias nos controlos de aplicações adaptativas](#managing-rules-with-adaptive-application-controls-improvements)
 - [Recomendação de segurança do contentor de controlo usando a Política Azure](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Gerir regras com melhorias nos controlos de aplicações adaptativas

A experiência de gestão de regras para máquinas virtuais utilizando controlos de aplicações adaptativos melhorou. Os controlos de aplicação adaptativos do Azure Security Center ajudam-no a controlar quais aplicações podem ser executadas nas suas máquinas virtuais. Além de uma melhoria geral na gestão de regras, um novo benefício permite-lhe controlar quais os tipos de ficheiros que serão protegidos quando adicionar uma nova regra.

[Saiba mais sobre controlos de aplicações adaptativos.](security-center-adaptive-application.md)


### <a name="control-container-security-recommendation-using-azure-policy"></a>Recomendação de segurança do contentor de controlo usando a Política Azure

A recomendação do Azure Security Center para remediar vulnerabilidades na segurança do contentor pode agora ser ativada ou desativada através da Política Azure.

Para visualizar as suas políticas de segurança ativadas, a partir do Centro de Segurança abre a página Política de Segurança.


## <a name="august-2019"></a>Agosto de 2019

As atualizações em agosto incluem:

 - [Acesso VM just-in-time (JIT) para Azure Firewall](#just-in-time-jit-vm-access-for-azure-firewall)
 - [Remediação de um único clique para aumentar a sua postura de segurança (pré-visualização)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [Gestão entre inquilinos](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Acesso VM just-in-time (JIT) para Azure Firewall 

O acesso em VM just-in-time (JIT) para Azure Firewall está agora geralmente disponível. Utilize-o para proteger os ambientes protegidos do Azure Firewall, para além dos ambientes protegidos da NSG.

O acesso JIT VM reduz a exposição a ataques volumétricos de rede, fornecendo acesso controlado aos VMs apenas quando necessário, utilizando as suas regras NSG e Azure Firewall.

Quando ativa o JIT para os seus VMs, cria uma política que determina a proteção das portas, quanto tempo as portas devem permanecer abertas e endereços IP aprovados a partir do local onde essas portas podem ser acedidas. Esta política ajuda-o a manter o controlo do que os utilizadores podem fazer quando pedem acesso.

Os pedidos estão registados no Registo de Atividades do Azure, para que possa monitorizar e auditar facilmente o acesso. A página just-in-time também ajuda a identificar rapidamente os VM existentes que têm JIT ativado e VMs onde JIT é recomendado.

[Saiba mais sobre a Azure Firewall](https://docs.microsoft.com/azure/firewall/overview).


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Remediação de um único clique para aumentar a sua postura de segurança (pré-visualização)

A pontuação segura é uma ferramenta que o ajuda a avaliar a sua postura de segurança de carga de trabalho. Ele revê as suas recomendações de segurança e prioriza-as para si, para que saiba quais as recomendações a executar primeiro. Isto ajuda-o a encontrar as mais graves vulnerabilidades de segurança para priorizar a investigação.

De forma a simplificar a remediação de configurações erradas de segurança e ajudá-lo a melhorar rapidamente a sua pontuação segura, adicionámos uma nova capacidade que lhe permite remediar uma recomendação sobre uma maioria de recursos num único clique.

Esta operação permitir-lhe-á selecionar os recursos a que pretende aplicar a remediação e lançar uma ação de reparação que configurará a definição em seu nome.

Veja quais as recomendações que têm uma correção rápida ativada no [guia de referência às recomendações de segurança](recommendations-reference.md).


### <a name="cross-tenant-management"></a>Gestão entre inquilinos

O Centro de Segurança agora apoia cenários de gestão de inquilinos cruzados como parte do Farol de Azure. Isto permite-lhe ganhar visibilidade e gerir a postura de segurança de vários inquilinos no Centro de Segurança. 

[Saiba mais sobre experiências de gestão de inquilinos cruzados.](security-center-cross-tenant-management.md)


## <a name="july-2019"></a>Julho de 2019

### <a name="updates-to-network-recommendations"></a>Atualizações das recomendações da rede

O Azure Security Center (ASC) lançou novas recomendações de networking e melhorou algumas existentes. Agora, a utilização do Security Center garante uma proteção de rede ainda maior para os seus recursos. 

[Saiba mais sobre as recomendações de rede.](recommendations-reference.md#recs-network)


## <a name="june-2019"></a>Junho de 2019

### <a name="adaptive-network-hardening---generally-available"></a>Hardening de rede adaptativa - geralmente disponível

Uma das maiores superfícies de ataque para cargas de trabalho que correm na nuvem pública são as ligações de e para a Internet pública. Os nossos clientes têm dificuldade em saber quais as regras do Grupo de Segurança de Rede (NSG) para garantir que as cargas de trabalho do Azure só estão disponíveis para as gamas de origem necessárias. Com esta funcionalidade, o Security Center aprende os padrões de tráfego de rede e conectividade das cargas de trabalho do Azure e fornece recomendações de regras NSG, para máquinas virtuais viradas para a Internet. Isto ajuda o nosso cliente a configurar melhor as suas políticas de acesso à rede e a limitar a sua exposição a ataques. 

[Saiba mais sobre o endurecimento adaptativo da rede.](security-center-adaptive-network-hardening.md)
