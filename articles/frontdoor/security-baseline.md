---
title: Linha de segurança Azure para Azure Front Door
description: A linha de base de segurança Azure Front Door fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: frontdoor
ms.topic: conceptual
ms.date: 11/12/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 2b5995478d1c9e65916f76c70c8af374ce82ca54
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94631575"
---
# <a name="azure-security-baseline-for-azure-front-door"></a>Linha de segurança Azure para Azure Front Door

Esta linha de base de segurança aplica orientações da [versão 2.0 do Azure Security Benchmark](../security/benchmarks/overview.md) para a Porta Frontal Azure. O Azure Security Benchmark fornece recomendações sobre como pode proteger as suas soluções em nuvem no Azure. O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável à Porta Frontal Azure. Foram excluídos **os controlos** não aplicáveis à Porta frontal Azure.

Para ver como a Porta Frontal Azure mapeia completamente para o Azure Security Benchmark, consulte o ficheiro completo de [mapeamento de base de base da porta frontal Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para mais informações, consulte o [Benchmark de Segurança Azure: Segurança da Rede](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Ligar redes privadas em conjunto

**Orientação** : Não aplicável; A Porta Frontal azul não foi concebida para ser implantada ou segura numa rede privada, este controlo destina-se a descrever a conectividade da rede e não se aplica.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade:** Unet. Por favor, forneça um valor no item de trabalho.

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Estabelecer acesso à rede privada aos serviços da Azure

**Orientação** : Não aplicável, a Porta Frontal Azure não foi concebida para ser implantada ou protegida por uma rede virtual de acesso à rede privada..

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade:** Unet. Por favor, forneça um valor no item de trabalho.

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Proteger aplicações e serviços contra ataques de rede externa

**Orientação** : Utilize o Azure PowerShell para criar uma política de geo-filtragem e associe a política ao anfitrião frontal Azure Front Door existente. Esta política de geo-filtragem bloqueará pedidos de redes externas, como as de outros países ou regiões, com exceção dos Estados Unidos.

- [Tutorial - Como configurar uma política de WAF de geo-filtragem para a sua Porta frontal](front-door-tutorial-geo-filtering.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: Simplificar as regras de segurança da rede

**Orientação** : Utilize etiquetas de Serviço de Rede Virtual para definir controlos de acesso à rede em grupos de segurança de rede configurados para a sua Porta Frontal Azure que oferece recursos. As etiquetas de serviço podem ser usadas em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da etiqueta de serviço( AzureFrontDoor.Frontend, AzureFrontDoor.Backend,AzureFrontDoor.FirstParty) no campo de origem ou destino apropriado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. 

A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

- [Compreender e usar etiquetas de serviço](../virtual-network/service-tags-overview.md)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

## <a name="identity-management"></a>Gestão de Identidades

*Para mais informações, consulte o [Azure Security Benchmark: Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Restringir o acesso a recurso Azure com base em condições

**Orientação** : Por predefinição, a Porta Frontal Azure responde a todos os pedidos do utilizador, independentemente da origem do pedido. Os clientes também podem restringir o acesso à sua aplicação web por países ou regiões. O serviço de firewall de aplicação Web em Azure Front Door permite que os clientes definam uma política usando regras de acesso personalizado, para um caminho específico no seu ponto final, para permitir ou bloquear o acesso de países ou regiões especificados.

- [Tutorial - Como configurar uma política de WAF de geo-filtragem para a sua Porta Frontal Azure](front-door-tutorial-geo-filtering.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

## <a name="privileged-access"></a>Acesso Privilegiado

*Para mais informações, consulte o [Azure Security Benchmark: Acesso Privilegiado](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Restringir o acesso administrativo a sistemas críticos de negócios

**Orientação** : A Azure Front Door utiliza o controlo de acesso baseado em funções Azure (Azure RBAC) para isolar o acesso a sistemas críticos de negócio. Utilize o Azure RBAC para limitar as contas a quem é concedido acesso privilegiado às subscrições e grupos de gestão, onde residem.

Garantir o acesso restrito a sistemas de gestão, identidade e segurança que tenham acesso administrativo a sistemas críticos de negócio, tais como controladores de domínio de diretório ativo, ferramentas de segurança e ferramentas de gestão do sistema. Alinhe todos os tipos de controlos de acesso à sua estratégia de segmentação da empresa para uma implementação contínua e consistente.

- [Componentes Azure e modelo de referência](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Acesso ao Grupo de Gestão](../governance/management-groups/overview.md#management-group-access) 

- [Administradores de subscrição da Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Compartilhado

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Utilize postos de trabalho privilegiados de acesso

**Orientação** : Estações de trabalho seguras e isoladas são de importância crucial para a segurança de funções sensíveis como administradores, desenvolvedores e operadores de serviços críticos. 

Utilize estações de trabalho de utilizador altamente seguras com Azure Bastion para tarefas administrativas. Escolha O Azure Ative Directory (Azure AD), Microsoft Defender Advanced Threat Protection (ATP) e Microsoft Intune para implementar estações de trabalho seguras e geridas para tarefas administrativas. As estações de trabalho seguras devem ser geridas centralmente para impor uma configuração segura, incluindo bases de autenticação forte, software e hardware, restrições lógicos e acesso à rede.

- [Compreender estações de trabalho de acesso privilegiada](../active-directory/devices/concept-azure-managed-workstation.md) 

- [Implementar uma estação de trabalho de acesso privilegiada](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Siga a administração suficiente (princípio de mínimo privilégio) 

**Orientação** : A Porta Frontal azul é integrada com o controlo de acesso baseado em funções Azure (Azure RBAC) para gerir os seus recursos. O Azure RBAC permite-lhe gerir o acesso a recursos Azure através de atribuições de funções. Pode atribuir estas funções aos utilizadores, grupos de diretores de serviço e identidades geridas. Existem funções incorporadas pré-definidas para determinados recursos, e estas funções podem ser inventariadas ou consultadas através de ferramentas como Azure CLI, Azure PowerShell ou o portal Azure. 

Siga o modelo de privilégios menos privilegiados para permissões baseadas em funções atribuídas a recursos com a Azure RBAC e certifique-se de que são baseadas na necessidade de negócio. Isto complementa a abordagem just in time (JIT) da Azure AD Privileged Identity Management (PIM) e deve ser revisto periodicamente.

Use funções incorporadas para alocar permissão e apenas criar funções personalizadas com base nos requisitos do negócio.

- [O que é o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md) 

- [Como configurar o RBAC em Azure](../role-based-access-control/role-assignments-portal.md) 

- [Como utilizar a identidade AD do Azure e as avaliações de acesso](../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Compartilhado

## <a name="data-protection"></a>Proteção de Dados

*Para mais informações, consulte o [Benchmark de Segurança Azure: Proteção de Dados](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Criptografar informações sensíveis em trânsito

**Orientação** : Utilize encriptação para proteger o tráfego em redes externas e públicas, uma vez que é fundamental para a proteção de dados. Além disso,

- Utilize controlos de acesso,

- proteger os dados em trânsito contra ataques "fora de banda" (como a captura de tráfego) usando encriptação para garantir que os atacantes não podem ler ou modificar facilmente os dados.
- Certifique-se de que qualquer cliente que se conecte aos seus recursos Azure para tráfego HTTP pode negociar TLS v1.2 ou maior.
- Utilize SSH (para Linux) ou RDP/TLS (para Windows) para gestão remota, em vez de protocolos não encriptados

- Desativar versões, protocolos e cifras obsoletas de SSL/TLS/SSH

Por padrão, o Azure fornece dados em encriptação de trânsito para o tráfego de dados entre centros de dados Azure. 

- [Tutorial - Como Configurar HTTPS num domínio personalizado da Porta da Frente](front-door-custom-domain-https.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Compartilhado

## <a name="asset-management"></a>Gestão de Recursos

*Para mais informações, consulte o [Azure Security Benchmark: Gestão de Ativos](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Garantir que a equipa de segurança tem visibilidade em riscos para os ativos

**Orientação** : Certifique-se de que as equipas de segurança recebem permissões de Leitor de Segurança no seu inquilino Azure e subscrições para que monitorizem os riscos de segurança utilizando o Centro de Segurança Azure. 

Dependendo da forma como as responsabilidades das equipas de segurança são estruturadas, a monitorização dos riscos de segurança pode ser da responsabilidade de uma equipa de segurança central ou de uma equipa local. No entanto, os conhecimentos e riscos de segurança devem ser sempre agregados centralmente dentro de uma organização. 

As permissões do Security Reader podem ser aplicadas amplamente a um inquilino inteiro (Root Management Group) ou a grupos de gestão ou subscrições específicas. 

Nota: que podem ser necessárias permissões adicionais para visibilidade em cargas de trabalho e serviços. 

- [Visão geral do papel do leitor de segurança](../role-based-access-control/built-in-roles.md#security-reader)

- [Visão geral dos Grupos de Gestão Azure](../governance/management-groups/overview.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Garantir que a equipa de segurança tem acesso ao inventário de ativos e metadados

**Orientação** : Aplicar etiquetas nos recursos da Azure, grupos de recursos e subscrições para os organizar logicamente numa taxonomia. Cada etiqueta é composta por um nome e um par de valor. Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" a todos os recursos em produção..

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Gestão de inventário de ativos do Azure Security Center](../security-center/asset-inventory.md) 

- [Guia de decisão de atribuição de nomes e de identificação de recursos](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Utilize apenas serviços Azure aprovados

**Orientação** : Utilize a Política Azure para auditar e restringir quais os serviços que os utilizadores podem prestação no seu ambiente. Consultar e descobrir recursos dentro das suas subscrições com o Azure Resource Graph.

Utilize o Azure Monitor para criar regras para acionar alertas quando for detetado um serviço não aprovado.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/built-in-policies.md#general) 

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Garantir a segurança da gestão do ciclo de vida dos ativos

**Orientação** : Não aplicável; A Azure Front Door não pode ser usada para garantir a segurança dos bens num processo de gestão do ciclo de vida. É da responsabilidade do cliente manter atributos e configurações de rede de ativos que são considerados de alto impacto. 

Recomenda-se que o cliente crie um processo para capturar as alterações de atributos e de configuração de rede, medir o impacto de mudança e criar tarefas de reparação, conforme aplicável.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

## <a name="logging-and-threat-detection"></a>Registo e Deteção de Ameaças

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e Deteção de Ameaças](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Permitir o registo de atividades da rede Azure

**Orientação** : A Porta Azure Frond não se destina a implantar em redes virtuais; por causa destes clientes são incapazes de permitir a registo de fluxo de fluxo do grupo de segurança de rede, o tráfego de rotas através de uma firewall ou realizar capturas de pacotes.

A Azure Front Door regista todo o tráfego de rede que processa para acesso ao cliente. Permitir a funcionalidade de registos de fluxo de rede e configurar estes registos para serem enviados para uma conta de armazenamento para retenção e auditoria a longo prazo.

- [Tutorial - Como configurar métricas e registos de monitorização na Porta frontal do Azure](front-door-diagnostics.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Compartilhado

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Permitir a exploração de madeira para recursos Azure

**Orientação** : Os registos de atividade, que estão automaticamente disponíveis, contêm todas as operações de escrita (PUT, POST, DELETE) para os seus recursos da Porta Frontal Azure, exceto operações de leitura (GET). Os registos de atividade podem ser utilizados para encontrar um erro na resolução de problemas ou para monitorizar como um utilizador na sua organização modificou um recurso.

Ativar os registos de recursos do Azure para a Porta frontal Azure. Pode utilizar o Azure Security Center e a Azure Policy para permitir registos de recursos e recolher dados. Estes registos podem ser cruciais para mais tarde investigar incidentes de segurança e realizar exercícios forenses.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Compreenda o registo e diferentes tipos de registo em Azure](../azure-monitor/platform/platform-logs-overview.md) 

- [Compreender a recolha de dados do Centro de Segurança Azure](../security-center/security-center-enable-data-collection.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Compartilhado

## <a name="incident-response"></a>Resposta a Incidentes

*Para mais informações, consulte o [Benchmark de Segurança Azure: Incident Response](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparação – atualizar o processo de resposta a incidentes para o Azure

**Orientação** : Certifique-se de que a sua organização definiu processos para responder a incidentes de segurança. Confirme que mantém estes processos atualizados para os recursos da Azure e testa-os regularmente para garantir a prontidão.

- [Implementar segurança em todo o ambiente da empresa](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de referência de resposta a incidentes](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Preparação – notificação de incidente de configuração

**Orientação** : Configurar informações de contacto de incidentes de segurança no Centro de Segurança Azure. Estas informações de contacto são utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Também tem opções para personalizar alerta de incidentes e notificação em diferentes serviços Azure com base nas suas necessidades de resposta a incidentes. 

- [Como definir o contacto de segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Deteção e análise – criar incidentes baseados em alertas de alta qualidade

**Orientação** : Certifique-se de que tem um processo para criar alertas de alta qualidade e medir a qualidade dos alertas. Isto permite-lhe aprender lições de incidentes passados e priorizar alertas para analistas, para que não percam tempo com falsos positivos. 

Alertas de alta qualidade podem ser construídos com base na experiência de incidentes passados, fontes comunitárias validadas e ferramentas projetadas para gerar e limpar alertas fundindo e correlacionando diversas fontes de sinal. 

O Azure Security Center fornece alertas de alta qualidade em muitos ativos da Azure. Pode utilizar o conector de dados ASC para transmitir os alertas ao Azure Sentinel. O Azure Sentinel permite criar regras avançadas de alerta para gerar incidentes automaticamente para uma investigação. 

Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de exportação para ajudar a identificar riscos para os recursos da Azure. Alertas e recomendações de exportação, manualmente ou de forma contínua e contínua.

- [Como configurar a exportação](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Deteção e análise – investigue um incidente

**Orientação** : Certifique-se de que os analistas podem consultar e usar diversas fontes de dados, uma vez que investigam potenciais incidentes para construir uma visão completa do que ocorreu. Devem ser recolhidos diversos tipos de registos para rastrear as atividades de um potencial atacante através da cadeia de morte para evitar quaisquer pontos cegos.  Certifique-se de que os insights e aprendizagens são capturados para outros analistas e para referência histórica futura.  

As fontes de dados para investigação incluem as fontes centralizadas de registo que já estão a ser recolhidas dos serviços de âmbito e dos sistemas de funcionamento, mas também podem incluir:

- Dados da rede – utilize os registos de fluxo dos grupos de segurança da rede, o Azure Network Watcher e o Azure Monitor para capturar registos de fluxo de rede e outras informações analíticas. 

- Instantâneos dos sistemas de funcionamento: 

    - Utilize a capacidade de instantâneo da máquina virtual Azure para criar uma imagem do disco do sistema de funcionamento. 

    - Utilize a capacidade de despejo de memória nativa do sistema operativo para criar uma imagem da memória do sistema de funcionamento.

    - Utilize a funcionalidade instantânea dos serviços Azure ou a capacidade do seu próprio software para criar instantâneos dos sistemas de execução.

O Azure Sentinel fornece uma análise extensiva de dados em praticamente qualquer fonte de registo e um portal de gestão de casos para gerir todo o ciclo de vida dos incidentes. Informações de inteligência durante uma investigação podem ser associadas a um incidente para rastrear e reportar propósitos. 

- [Snapshot um disco de máquina do Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Snapshot um disco de máquina Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure Suporta informações de diagnóstico e recolha de despejo de memória](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Investigue incidentes com Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Deteção e análise – priorizar incidentes

**Orientação** : Fornecer contexto aos analistas sobre quais incidentes se concentrar em primeiro lugar com base na gravidade do alerta e na sensibilidade do ativo. 

O Centro de Segurança Azure atribui uma gravidade a cada alerta para ajudá-lo a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque recursos usando tags e crie um sistema de nomeação para identificar e categorizar recursos Azure, especialmente aqueles que processam dados sensíveis.  É da sua responsabilidade priorizar a reparação de alertas com base na criticidade dos recursos e ambiente do Azure onde ocorreu o incidente.

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Utilizar etiquetas para organizar os recursos do Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Contenção, erradicação e recuperação – automatizar o tratamento do incidente

**Orientação** : Automatizar tarefas repetitivas manuais para acelerar o tempo de resposta e reduzir o fardo sobre os analistas. As tarefas manuais demoram mais tempo a ser executadas, abrandando cada incidente e reduzindo quantos incidentes um analista pode lidar. As tarefas manuais também aumentam a fadiga dos analistas, o que aumenta o risco de erro humano que causa atrasos, e degrada a capacidade dos analistas de se concentrarem eficazmente em tarefas complexas. Utilize funcionalidades de automatização de fluxos de trabalho no Azure Security Center e no Azure Sentinel para desencadear automaticamente ações ou executar um livro de jogadas para responder aos alertas de segurança que chegam. O livro de jogadas toma medidas, tais como o envio de notificações, a desativação de contas e a isolação de redes problemáticas. 

- [Configurar automatização de fluxos de trabalho no Centro de Segurança](../security-center/workflow-automation.md)

- [Criar respostas automáticas de ameaças no Centro de Segurança Azure](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurar respostas automáticas de ameaças em Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

## <a name="posture-and-vulnerability-management"></a>Gestão de Postura e Vulnerabilidade

*Para mais informações, consulte o [Azure Security Benchmark: Posture and Vulnerability Management](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: Estabelecer configurações seguras para recursos computacional

**Orientação** : Utilize o Azure Security Center e a Azure Policy para estabelecer configurações seguras em todos os recursos de computação, incluindo Máquinas Virtuais, contentores e muito mais.

- [Como monitorizar as recomendações do Centro de Segurança Azure](../security-center/security-center-recommendations.md) 

- [Recomendações de segurança: um guia de referência](../security-center/recommendations-reference.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Compartilhado

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: Remediar rapidamente e automaticamente as vulnerabilidades do software

**Orientação** : Implemente rapidamente atualizações de software para remediar vulnerabilidades de software em sistemas operativos e aplicações.

Priorize usar um programa comum de pontuação de risco (por exemplo, Sistema Comum de Pontuação de Vulnerabilidade) ou as classificações de risco padrão fornecidas por uma ferramenta de digitalização de terceiros que está a usar. e adaptar-se ao seu ambiente utilizando o contexto de que as aplicações apresentam um alto risco de segurança e quais as que requerem tempo elevado.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Realizar simulação regular de ataque

**Orientação** : Conforme necessário, realize testes de penetração ou atividades de equipa vermelha nos seus recursos Azure e garanta a reparação de todos os resultados críticos de segurança.
Siga as Regras de Teste de Penetração na Nuvem da Microsoft para garantir que os seus testes de penetração não violam as políticas da Microsoft. Use a estratégia da Microsoft e a execução de testes de penetração em red teaming e site ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft.

- [Testes de penetração em Azure](../security/fundamentals/pen-testing.md)

- [Regras de teste de penetração de compromisso](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Compartilhado

## <a name="governance-and-strategy"></a>Governação e Estratégia

*Para mais informações, consulte o [Benchmark de Segurança Azure: Governação e Estratégia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definir estratégia de gestão de ativos e proteção de dados 

**Orientação** : Certifique-se de que documenta e comunica uma estratégia clara de monitorização e proteção contínua de sistemas e dados. Priorize a descoberta, a avaliação, a proteção e o acompanhamento de dados e sistemas críticos do negócio. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Norma de classificação de dados de acordo com os riscos empresariais

-   Visibilidade da organização de segurança em riscos e inventário de ativos 

-   Aprovação da organização de segurança dos serviços da Azure para utilização 

-   Segurança dos bens através do seu ciclo de vida

-   Estratégia de controlo de acesso exigida de acordo com a classificação de dados organizacionais

-   Utilização de capacidades de proteção de dados nativas e de terceiros da Azure

-   Requisitos de encriptação de dados para casos de utilização em trânsito e em repouso

-   Padrões criptográficos adequados

Mais informações estão disponíveis nos links referenciados.

- [Recomendação de arquitetura de segurança Azure - Armazenamento, dados e encriptação](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Fundamentos de Segurança Azure - Segurança de dados Azure, encriptação e armazenamento](../security/fundamentals/encryption-overview.md)

- [Benchmark de segurança Azure - Proteção de Dados](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definir estratégia de segmentação empresarial 

**Orientação** : Estabeleça uma estratégia em toda a empresa para segmentar o acesso a ativos utilizando uma combinação de identidade, rede, aplicação, subscrição, grupo de gestão e outros controlos.

Equilibra cuidadosamente a necessidade de separação de segurança com a necessidade de permitir o funcionamento diário dos sistemas que precisam de comunicar entre si e aceder aos dados.

Certifique-se de que a estratégia de segmentação é implementada de forma consistente em todos os tipos de controlo, incluindo modelos de segurança de rede, identidade e acesso, e modelos de permissão/acesso de aplicações e controlos de processos humanos.

- [Orientação sobre estratégia de segmentação em Azure (vídeo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Orientação sobre estratégia de segmentação em Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Alinhar a segmentação da rede com a estratégia de segmentação empresarial](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definir estratégia de gestão da postura de segurança

**Orientação:** Medir e mitigar continuamente os riscos para os seus ativos individuais e para o ambiente em que estão hospedados. Priorize ativos de alto valor e superfícies de ataque altamente expostas, tais como aplicações publicadas, entradas de rede e pontos de saída, pontos finais de utilizador e administrador, e assim por diante.

- [Azure Security Benchmark - Postura e gestão de vulnerabilidades](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Alinhar papéis de organização, responsabilidades e responsabilidades

**Orientação** : Certifique-se de que documenta e comunica uma estratégia clara para funções e responsabilidades na sua organização de segurança. Priorizar a prestação de contas claras às decisões de segurança, educar todos no modelo de responsabilidade partilhada e educar as equipas técnicas em tecnologia para garantir a nuvem.

- [Azure Security Best Practice 1 - Pessoas: Educar equipas na jornada de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure Security Best Practice 2 - Pessoas: Educar equipas sobre tecnologia de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure Security Best Practice 3 - Processo: Atribuir responsabilidade para decisões de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definir estratégia de segurança de rede

**Orientação** : Estabeleça uma abordagem de segurança da rede Azure como parte da estratégia global de controlo de acesso à segurança da sua organização.  

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Responsabilidade centralizada de gestão de redes e segurança

-   Modelo de segmentação de rede virtual alinhado com a estratégia de segmentação da empresa

-   Estratégia de remediação em diferentes cenários de ameaça e ataque

-   Internet borda e estratégia de entrada e saída

-   Estratégia híbrida de interconectividade em nuvem e no local

-   Artefactos de segurança da rede atualizados (por exemplo, diagramas de rede, arquitetura de rede de referência)

Mais informações estão disponíveis nos links referenciados.

- [Azure Security Best Practice 11 - Arquitetura. Estratégia de segurança unificada única](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Benchmark de segurança Azure - Segurança de rede](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Visão geral da segurança da rede Azure](../security/fundamentals/network-overview.md)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definir estratégia de identidade e acesso privilegiado

**Orientação** : Estabeleça uma identidade azul e abordagens privilegiadas de acesso como parte da estratégia global de controlo de acesso à segurança da sua organização.  

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Um sistema centralizado de identidade e autenticação e a sua interconectividade com outros sistemas de identidade interna e externa

-   Métodos de autenticação forte em diferentes casos e condições de utilização

-   Proteção de utilizadores altamente privilegiados

-   Monitorização e manuseamento de atividades de utilizadores de anomalias  

-   Revisão e reconciliação da identidade do utilizador e acesso

Para mais informações, consulte as seguintes referências:

- [Azure Security Benchmark - Gestão de identidade](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Benchmark de Segurança Azure - Acesso privilegiado](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure Security Best Practice 11 - Arquitetura. Estratégia de segurança unificada única](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Visão geral da segurança da gestão de identidade Azure](../security/fundamentals/identity-management-overview.md)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definir a estratégia de resposta ao abate e à ameaça

**Orientação** : Estabeleça uma estratégia de resposta à exploração madeireira e a ameaças para detetar e remediar rapidamente as ameaças, cumprindo os requisitos de conformidade. Priorize fornecer aos analistas alertas de alta qualidade e experiências perfeitas para que se concentrem em ameaças em vez de integração e passos manuais. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   O papel e as responsabilidades da organização das operações de segurança (SecOps) 

-   Um processo de resposta a incidentes bem definido, alinhado com o NIST ou outro quadro da indústria 

-   Registar captura e retenção para apoiar a deteção de ameaças, resposta a incidentes e necessidades de conformidade

-   Visibilidade centralizada e correlação de informações sobre ameaças, utilizando siem, capacidades nativas de Azure, e outras fontes 

-   Plano de comunicação e notificação com os seus clientes, fornecedores e partes públicas de interesse

-   Utilização de plataformas nativas e de terceiros da Azure para o tratamento de incidentes, tais como deteção de registos e ameaças, perícia e remediação e erradicação de ataques

-   Processos de tratamento de incidentes e atividades pós-incidente, tais como lições aprendidas e retenção de provas

Mais informações estão disponíveis nos links referenciados.

- [Azure Security Benchmark - Registo e deteção de ameaças](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure Security Benchmark - Resposta a incidentes](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security Best Practice 4 - Processo. Atualizar processos de resposta a incidentes para cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Quadro de adoção Azure, registo e guia de decisão de reporte](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

## <a name="next-steps"></a>Passos seguintes

- Consulte a [visão geral do Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base de segurança da Azure](/azure/security/benchmarks/security-baselines-overview)
