---
title: Arquivo do que há de novo no Centro de Segurança Azure
description: Uma descrição do que é novo e alterado no Centro de Segurança Azure de há seis meses e mais cedo.
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
ms.openlocfilehash: 253ea748462ec09cd9cf4eab14c05be678cfb82d
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860907"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Arquivar para o que há de novo no Centro de Segurança Azure?

As principais novidades [no Azure Security Center?](release-notes.md)

Esta página fornece-lhe informações sobre:

- Novas funcionalidades
- Correções de erros
- Funcionalidade preterida

## <a name="november-2019"></a>Novembro de 2019

### <a name="threat-protection-for-azure-key-vault-in-public-preview-in-north-america-regions"></a>Proteção contra ameaças para cofre chave azure em pré-visualização pública nas regiões da América do Norte

O Azure Key Vault é um serviço essencial para proteger dados e melhorar o desempenho das aplicações em nuvem, oferecendo a capacidade de gerir centralmente chaves, segredos, chaves criptográficas e políticas na nuvem. Uma vez que o Azure Key Vault armazena dados críticos e sensíveis ao negócio, requer segurança máxima para os cofres-chave e os dados armazenados nos mesmos.

O apoio do Azure Security Center para a Proteção contra Ameaças para o Cofre chave Azure fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente prejudiciais de aceder ou explorar cofres chave. Esta nova camada de proteção permite que os clientes abordem ameaças contra os seus cofres-chave sem serem especialistas em segurança ou gerirem sistemas de monitorização de segurança. A funcionalidade está em pré-visualização pública nas Regiões da América do Norte.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Proteção contra ameaças para armazenamento azure inclui rastreio de reputação de malware

A proteção contra ameaças para o Armazenamento Azure oferece novas deteções alimentadas pela Microsoft Threat Intelligence para detetar uploads de malware para o Armazenamento Azure utilizando análise de reputação de hash e acesso suspeito a partir de um nó de saída tor ativo (um proxy anoonizador). Agora pode ver malware detetado através de contas de armazenamento usando o Azure Security Center.


### <a name="workflow-automation-with-logic-apps-preview"></a>Automatização de fluxo de trabalho com Aplicações Lógicas (pré-visualização)

Organizações com segurança gerida centralmente e TI/operações implementam processos internos de fluxo de trabalho para impulsionar a ação necessária dentro da organização quando as discrepâncias são descobertas nos seus ambientes. Em muitos casos, estes fluxos de trabalho são processos repetíveis e a automação pode simplificar muito os processos dentro da organização.

Hoje estamos a introduzir uma nova capacidade no Security Center que permite aos clientes criar configurações de automação alavancando as Aplicações Lógicas Do Azure e criar políticas que as desencadearão automaticamente com base em descobertas específicas do ASC, como Recomendações ou Alertas. A App Azure Logic pode ser configurada para fazer qualquer ação personalizada suportada pela vasta comunidade de conectores de Aplicações Lógicas, ou usar um dos modelos fornecidos pelo Security Center, como enviar um e-mail ou abrir um bilhete ServiceNow™.

Para obter mais informações sobre as capacidades automáticas e manuais do Centro de Segurança para executar os seus fluxos de trabalho, consulte a [automatização do fluxo de trabalho.](workflow-automation.md)

Para aprender sobre a criação de Apps Lógicas, consulte [aplicações lógicas do Azure.](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)


### <a name="quick-fix-for-bulk-resources-generally-available"></a>Correção rápida para recursos a granel geralmente disponíveis

Com as muitas tarefas que um utilizador é dado como parte do Secure Score, a capacidade de remediar eficazmente os problemas em uma grande frota pode tornar-se desafiante.

Para simplificar a remediação das configurações de segurança e para ser capaz de remediar rapidamente as recomendações sobre uma grande maioria de recursos e melhorar a sua pontuação segura, use a reparação Quick Fix.

Esta operação permitir-lhe-á selecionar os recursos a que pretende aplicar a reparação e lançar uma ação de reparação que configurará a definição em seu nome.

A correção rápida está geralmente disponível hoje em dia os clientes como parte da página de recomendações do Centro de Segurança.

Veja quais as recomendações que têm uma correção rápida ativada no [guia de referência às recomendações](recommendations-reference.md)de segurança .


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Analisar imagens de contentores para vulnerabilidades (pré-visualização)

O Azure Security Center já pode digitalizar imagens de contentores no Registo de Contentores de Azure para obter vulnerabilidades.

A digitalização da imagem funciona analisando o ficheiro de imagem do recipiente, verificando depois se existem vulnerabilidades conhecidas (alimentadas por Qualys).

A verificação em si é automaticamente acionada ao empurrar novas imagens de contentores para o Registo de Contentores De Azure. As vulnerabilidades encontradas surgirão como recomendações do Security Center e incluídas no Azure Secure Score juntamente com informações sobre como corrigi-las para reduzir a superfície de ataque que permitiram.


### <a name="additional-regulatory-compliance-standards-preview"></a>Normas adicionais de conformidade regulamentar (pré-visualização)

O dashboard de conformidade regulamentar fornece insights sobre a sua postura de conformidade com base nas avaliações do Security Center. O painel de instrumentos mostra como o seu ambiente cumpre os controlos e requisitos designados por normas regulamentares específicas e critérios de referência da indústria e fornece recomendações prescritivas para como lidar com estes requisitos.

O painel de controlo regulamentar apoiou até agora quatro normas incorporadas: Azure CIS 1.1.0, PCI-DSS, ISO 27001 e SOC-TSP. Estamos agora a anunciar a divulgação pública de normas apoiadas adicionais: NIST SP 800-53 R4, SWIFT CSP CSCF v2020, Canadá Federal PBMM e UK Official juntamente com o NHS do Reino Unido. Estamos também a lançar uma versão atualizada do Azure CIS 1.1.0, cobrindo mais controlos a partir da norma e aumentando a extensibility.

[Saiba mais sobre personalizar o conjunto de normas no seu dashboard](update-regulatory-compliance-packages.md)de conformidade regulamentar.


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Proteção contra ameaças para o Serviço Azure Kubernetes (pré-visualização)

A Kubernetes está rapidamente a tornar-se o novo padrão para implementar e gerir software na nuvem. Poucas pessoas têm uma vasta experiência com kubernetes e muitas apenas se concentram na engenharia e administração gerais e ignoram o aspeto da segurança. O ambiente kubernetes precisa de ser configurado cuidadosamente para ser seguro, certificando-se de que nenhuma porta de superfície de ataque focada em recipientes não é deixada aberta para os atacantes. O Security Center está a expandir o seu suporte no espaço de contentores para um dos serviços de maior crescimento no Serviço Azure - Azure Kubernetes (AKS).

As novas capacidades neste lançamento de pré-visualização pública incluem:

- **Discovery & Visibilidade** - Descoberta contínua de casos de AKS geridos dentro das subscrições registadas do Security Center.
- **Recomendações Secure Score** - Itens acionáveis para ajudar os clientes a cumprir as melhores práticas de segurança no AKS como parte da Pontuação Segura do cliente, tais como "O Controlo de Acesso baseado em funções deve ser usado para restringir o acesso a um Cluster de Serviçokubernetes".
- **Deteção de Ameaças** - Analíticos baseados em hospedeiros e clusters, tais como "Um recipiente privilegiado detetado".


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Avaliação de vulnerabilidade virtual da máquina (pré-visualização)

As aplicações instaladas em máquinas virtuais podem muitas vezes ter vulnerabilidades que podem levar a uma violação da máquina virtual. Anunciamos que o nível Padrão do Centro de Segurança inclui avaliação de vulnerabilidade incorporada para máquinas virtuais sem qualquer taxa adicional. A avaliação de vulnerabilidade, alimentada pela Qualys na pré-visualização pública, permitirá digitalizar continuamente todas as aplicações instaladas numa máquina virtual para encontrar aplicações vulneráveis e apresentar as conclusões na experiência do portal do Security Center. O Security Center trata de todas as operações de implantação para que não seja necessário trabalho extra por parte do utilizador. Para a frente, estamos a planear fornecer opções de avaliação de vulnerabilidades para suportar as necessidades únicas do negócio dos nossos clientes.

[Saiba mais sobre avaliações de vulnerabilidade para as suas Máquinas Virtuais Azure](security-center-vulnerability-assessment-recommendations.md).


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Segurança avançada de dados para servidores SQL em Máquinas Virtuais Azure (pré-visualização)

O apoio do Azure Security Center para a proteção contra ameaças e avaliação de vulnerabilidade para DBs SQL em execução em VMs IaaS está agora em pré-visualização.

[A avaliação de vulnerabilidade](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) é um serviço fácil de configurar que pode descobrir, rastrear e ajudá-lo a remediar potenciais vulnerabilidades de base de dados. Proporciona visibilidade na sua postura de segurança como parte da pontuação segura do Azure e inclui os passos para resolver problemas de segurança e melhorar as fortificações da sua base de dados.

[A proteção avançada contra ameaças](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de aceder ou explorar o seu servidor SQL. Monitoriza continuamente a sua base de dados para atividades suspeitas e fornece alertas de segurança orientados para a ação sobre padrões de acesso anómalos à base de dados. Estes alertas fornecem detalhes de atividades suspeitas e ações recomendadas para investigar e mitigar a ameaça.


### <a name="support-for-custom-policies-preview"></a>Suporte para políticas personalizadas (pré-visualização)

O Azure Security Center agora suporta políticas personalizadas (em pré-visualização).

Os nossos clientes têm procurado alargar a cobertura atual de avaliações de segurança no Centro de Segurança com as suas próprias avaliações de segurança baseadas em políticas que criam na Política Azure. Com o apoio a políticas personalizadas, isso agora é possível.

Estas novas políticas farão parte da experiência de recomendações do Centro de Segurança, Secure Score, e do dashboard de normas de conformidade regulamentar. Com o apoio a políticas personalizadas, agora é capaz de criar uma iniciativa personalizada na Política Azure, depois adicioná-la como uma política no Centro de Segurança e visualizá-la como uma recomendação.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Alargar a cobertura do Azure Security Center com plataforma para comunidade e parceiros

Use o Security Center para receber recomendações não só da Microsoft, mas também de soluções existentes de parceiros como Check Point, Tenable e CyberArk com muitas mais integrações vindo.  O fluxo de embarque simples do Security Center pode ligar as suas soluções existentes ao Centro de Segurança, permitindo-lhe visualizar as suas recomendações de postura de segurança num único local, executar relatórios unificados e alavancar todas as capacidades do Security Center contra recomendações incorporadas e parceiras. Também pode exportar recomendações do Security Center para produtos parceiros.

[Saiba mais sobre a Microsoft Intelligent Security Association](https://www.microsoft.com/security/partnerships/intelligent-security-association).



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Integrações avançadas com exportação de recomendações e alertas (pré-visualização)

De forma a permitir cenários de nível empresarial no topo do Centro de Segurança, é agora possível consumir alertas e recomendações do Centro de Segurança em locais adicionais, exceto o portal Azure ou API. Estes podem ser diretamente exportados para um Hub de Eventos e para log analytics espaços de trabalho. Aqui estão alguns fluxos de trabalho que pode criar em torno destas novas capacidades:

- Com a exportação para log Analytics espaço de trabalho, você pode criar dashboards personalizados com Power BI.
- Com a exportação para o Event Hub, poderá exportar alertas e recomendações do Centro de Segurança para os seus SIEMs de terceiros, para uma solução de terceiros em tempo real, ou Azure Data Explorer.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Servidores on-prem a bordo para O Centro de Segurança do Windows Admin Center (pré-visualização)

O Windows Admin Center é um portal de gestão para servidores Windows que não estão implantados no Azure, oferecendo-lhes várias capacidades de gestão Azure, como backup e atualizações de sistema. Adicionámos recentemente a capacidade de embarcar nestes servidores não-Azure para serem protegidos pelo ASC diretamente da experiência do Windows Admin Center.

Com esta nova experiência, os utilizadores estarão a bordo de um servidor WAC para o Azure Security Center e a permitir visualizar os seus alertas de segurança e recomendações diretamente na experiência do Windows Admin Center.


## <a name="september-2019"></a>Setembro de 2019

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Gerir regras com melhorias adapttivas de controlode aplicações

A experiência de gestão de regras para máquinas virtuais utilizando controlos de aplicações adaptativos melhorou. Os controlos de aplicação adaptativo do Azure Security Center ajudam-no a controlar quais as aplicações que podem ser executadas nas suas máquinas virtuais. Além de uma melhoria geral na gestão de regras, um novo benefício permite controlar quais os tipos de ficheiros que serão protegidos quando adicionar uma nova regra.

[Saiba mais sobre controlos de aplicação adaptativo.](security-center-adaptive-application.md)


### <a name="control-container-security-recommendation-using-azure-policy"></a>Recomendação de segurança de contentores de controlo utilizando a Política Azure

A recomendação do Azure Security Center para remediar vulnerabilidades na segurança dos contentores pode agora ser ativada ou desativada através da Política Azure.

Para visualizar as suas políticas de segurança ativadas, a partir do Centro de Segurança abra a página de Política de Segurança.


## <a name="august-2019"></a>Agosto de 2019

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Acesso VM just-in-time (JIT) para Azure Firewall 

O acesso VM just-in-time (JIT) para o Azure Firewall está geralmente disponível. Use-o para proteger os ambientes protegidos da Firewall Azure, para além dos ambientes protegidos pelo NSG.

O acesso ao JIT VM reduz a exposição a ataques volumétricos da rede, proporcionando acesso controlado a VMs apenas quando necessário, utilizando as suas regras NSG e Azure Firewall.

Quando ativa o JIT para os seus VMs, cria uma política que determina a proteção das portas, quanto tempo as portas devem permanecer abertas e endereços IP aprovados a partir de onde estas portas podem ser acedidas. Esta política ajuda-o a manter o controlo do que os utilizadores podem fazer quando pedem acesso.

Os pedidos estão registados no Registo de Atividades do Azure, para que possa monitorizar e auditar facilmente o acesso. A página just-in-time também ajuda a identificar rapidamente os VMs existentes que têm JIT ativado e VMs onde o JIT é recomendado.

[Saiba mais sobre o Azure Firewall.](https://docs.microsoft.com/azure/firewall/overview)


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Reparação de clique único para aumentar a sua postura de segurança (pré-visualização)

A pontuação segura é uma ferramenta que o ajuda a avaliar a sua postura de segurança da carga de trabalho. Revê as suas recomendações de segurança e prioriza-as para si, para que saiba quais as recomendações a executar primeiro. Isto ajuda-o a encontrar as vulnerabilidades de segurança mais graves para priorizar a investigação.

De forma a simplificar a remediação das configurações de segurança e ajudá-lo a melhorar rapidamente a sua pontuação segura, adicionámos uma nova capacidade que lhe permite remediar uma recomendação sobre uma grande maioria de recursos num único clique.

Esta operação permitir-lhe-á selecionar os recursos a que pretende aplicar a reparação e lançar uma ação de reparação que configurará a definição em seu nome.

Veja quais as recomendações que têm uma correção rápida ativada no [guia de referência às recomendações](recommendations-reference.md)de segurança .


### <a name="cross-tenant-management"></a>Gestão entre inquilinos

O Centro de Segurança agora apoia cenários de gestão de inquilinos cruzados como parte do Farol azure. Isto permite-lhe ganhar visibilidade e gerir a postura de segurança de vários inquilinos no Centro de Segurança. 

[Saiba mais sobre experiências de gestão de inquilinos cruzados.](security-center-cross-tenant-management.md)


## <a name="july-2019"></a>Julho de 2019

### <a name="updates-to-network-recommendations"></a>Atualizações às recomendações da rede

O Azure Security Center (ASC) lançou novas recomendações de networking e melhorou algumas existentes. Agora, a utilização do Centro de Segurança garante ainda mais proteção de rede para os seus recursos. 

[Saiba mais sobre as recomendações da rede.](recommendations-reference.md#recs-network)


## <a name="june-2019"></a>Junho de 2019

### <a name="adaptive-network-hardening---generally-available"></a>Adaptive Network Hardening - geralmente disponível

Uma das maiores superfícies de ataque para cargas de trabalho que correm na nuvem pública são as ligações de e para a Internet pública. Os nossos clientes têm dificuldade em saber quais as regras do Network Security Group (NSG) para garantir que as cargas de trabalho do Azure só estejam disponíveis para as gamas de origem exigidas. Com esta funcionalidade, o Security Center aprende os padrões de tráfego de rede e conectividade das cargas de trabalho do Azure e fornece recomendações de regra nsg, para máquinas virtuais viradas para a Internet. Isto ajuda o nosso cliente a configurar melhor as suas políticas de acesso à rede e a limitar a sua exposição a ataques. 

[Saiba mais sobre o endurecimento da rede adaptativo.](security-center-adaptive-network-hardening.md)
