---
title: Linha de segurança Azure para Cloud Shell
description: A linha de base de segurança Cloud Shell fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Azure Security Benchmark.
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 24856e458858ad86d953b50709669823b35794fc
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348267"
---
# <a name="azure-security-baseline-for-cloud-shell"></a>Linha de segurança Azure para Cloud Shell

Esta linha de base de segurança aplica orientações da [versão 1.0 do Benchmark de Segurança Azure](../security/benchmarks/overview-v1.md) para cloud Shell. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure.
O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável à Cloud Shell. Foram excluídos **controlos** não aplicáveis à Cloud Shell.

 
Para ver como a Cloud Shell mapeia completamente para o Benchmark de Segurança Azure, consulte o [ficheiro de mapeamento de base de segurança cloud Shell completo.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Segurança da rede

*Para obter mais informações, consulte o [Azure Security Benchmark: Segurança da rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: Os clientes podem implantar a Azure Cloud Shell numa Rede Virtual detida pelo cliente.

Quando coloca o Azure Cloud Shell numa Rede Virtual detida por um cliente, tem de criar ou utilizar uma rede virtual existente. Certifique-se de que a rede virtual escolhida tem um grupo de segurança de rede aplicado às suas sub-redes e controlos de acesso à rede configurados específicos das portas e fontes fidedignas da sua aplicação.

- [Implementar a Cloud Shell numa rede virtual Azure](private-vnet.md)

- [Como criar um grupo de segurança de rede com regras de segurança](../virtual-network/tutorial-filter-network-traffic.md)

- [Como implantar e configurar firewall Azure](../firewall/tutorial-firewall-deploy-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e controlo de acessos](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

**Orientação**: Azure Cloud Shell é uma experiência de linha de comando baseada no navegador que usa a mesma autorização que é usada para aceder ao portal Azure, neste caso um SSO no portal Azure também irá autenticar você com Cloud Shell. 

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação**: Azure Cloud Shell é uma experiência de linha de comando baseada no navegador que utiliza a mesma autorização que é usada para aceder ao portal Azure, neste caso, qualquer MFA que seja necessário para ligar ao portal Azure também será necessária para a Cloud Shell. 

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades

*Para obter mais informações, consulte o [Azure Security Benchmark: Gestão de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Azure Cloud Shell é uma experiência de linha de comando baseada no navegador que é usada para gestão interativa de recursos em nuvem.  Cada recipiente de cliente é efémero, um novo recipiente é utilizado para cada sessão.  As imagens do contentor são monitorizadas e atualizadas pela equipa cloud Shell.

A Azure Cloud Shell permite que os clientes instalem as suas próprias ferramentas ou software à sua imagem de acordo com as suas necessidades organizacionais.

Os clientes são responsáveis por executar ferramentas de digitalização de vulnerabilidade automatizadas contra software que está a funcionar no ambiente.  

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Implementar uma solução automatizada de gestão de patchs para títulos de software de terceiros

**Orientação**: Não aplicável; Azure Cloud Shell é uma experiência de linha de comando baseada no navegador que é usada para gestão interativa de recursos em nuvem.  Cada recipiente de cliente é efémero, um novo recipiente é utilizado para cada sessão.  As imagens do contentor são monitorizadas e atualizadas pela equipa cloud Shell.

A Azure Cloud Shell permite que os clientes instalem as suas próprias ferramentas ou software à sua imagem de acordo com as suas necessidades organizacionais.

Os clientes são responsáveis pela gestão de patchs de software em execução no seu ambiente.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: Azure Cloud Shell é uma experiência de linha de comando baseada no navegador que é usada para gestão interativa de recursos em nuvem.  Cada recipiente de cliente é efémero, um novo recipiente é utilizado para cada sessão.  As imagens do contentor são monitorizadas e atualizadas pela equipa cloud Shell.

A Azure Cloud Shell permite que os clientes instalem as suas próprias ferramentas ou software à sua imagem de acordo com as suas necessidades organizacionais.

Os clientes são responsáveis por remediar vulnerabilidades que são descobertas através das suas verificações de vulnerabilidade de software. A análise das exportações resulta em intervalos consistentes e compara os resultados com as análises anteriores para verificar se as vulnerabilidades foram remediadas. Ao utilizar recomendações de gestão de vulnerabilidades sugeridas pelo Azure Security Center, pode entrar no portal da solução selecionada para visualizar dados históricos de digitalização.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Azure Cloud Shell é uma experiência de linha de comando baseada no navegador que é usada para gestão interativa de recursos em nuvem.  Cada recipiente de cliente é efémero, um novo recipiente é utilizado para cada sessão.  As imagens do contentor são monitorizadas e atualizadas pela equipa cloud Shell.

A Azure Cloud Shell permite que os clientes instalem as suas próprias ferramentas ou software à sua imagem de acordo com as suas necessidades organizacionais.

Os clientes são responsáveis por remediar vulnerabilidades que são descobertas através das suas verificações de vulnerabilidade de software.  Utilize um programa comum de pontuação de risco (por exemplo, Sistema Comum de Pontuação de Vulnerabilidade) ou as classificações de risco padrão fornecidas pela sua ferramenta de digitalização de terceiros. 

- [NIST Publication--Common Vulnerability Scoring System](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e gestão de ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definir e manter um inventário dos recursos aprovados do Azure

**Orientação**: Azure Cloud Shell é uma experiência de linha de comando baseada no navegador que é usada para gestão interativa de recursos em nuvem.  Cada recipiente de cliente é efémero, um novo recipiente é utilizado para cada sessão.  As imagens e ferramentas do contentor são monitorizadas e atualizadas pela equipa cloud Shell.  O cliente é capaz de instalar as suas próprias ferramentas à sua própria imagem de acordo com as suas necessidades organizacionais e as ferramentas não requerem `sudo` permissões durante a instalação.

Os clientes são recomendados para criar um inventário de software aprovado que é instalado através da Azure Cloud Shell de acordo com as suas necessidades organizacionais.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: A Azure Cloud Shell é um serviço gratuito sem ativos pertencentes ao cliente.  As imagens e ferramentas do contentor são monitorizadas e atualizadas pela equipa cloud Shell. 

A Azure Cloud Shell permite que os clientes instalem as suas próprias ferramentas ou software à sua imagem de acordo com as suas necessidades organizacionais.

Os clientes são responsáveis por monitorizar as aplicações de software em execução no ambiente para garantir que são aprovadas por política de organização.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: A Azure Cloud Shell é um serviço gratuito sem ativos pertencentes ao cliente.  As imagens e ferramentas do contentor são monitorizadas e atualizadas pela equipa cloud Shell. 

A Azure Cloud Shell permite que os clientes instalem as suas próprias ferramentas ou software à sua imagem de acordo com as suas necessidades organizacionais.

Os clientes são responsáveis por monitorizar as aplicações de software em execução no ambiente para garantir que o software não aprovado seja gerido por política de organização.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: A Azure Cloud Shell é um serviço gratuito sem ativos pertencentes ao cliente.  As imagens e ferramentas do contentor são monitorizadas e atualizadas pela equipa cloud Shell.  As ferramentas específicas não podem ser removidas pelo cliente.

O Azure Cloud Shell permite que os clientes instalem as suas próprias ferramentas ou aplicações à sua imagem de acordo com as suas necessidades organizacionais.

Os clientes são responsáveis por monitorizar as aplicações em execução no ambiente para garantir que são aprovadas por política de organização.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Manter um inventário de títulos de software aprovados

**Orientação**: Não aplicável; Azure Cloud Shell é uma experiência de linha de comando baseada no navegador que é usada para gestão interativa de recursos em nuvem.  Cada recipiente de cliente é efémero, um novo recipiente é utilizado para cada sessão.  As imagens do contentor são monitorizadas e atualizadas pela equipa cloud Shell.

A Azure Cloud Shell permite que os clientes instalem as suas próprias ferramentas ou software à sua imagem de acordo com as suas necessidades organizacionais.

Os clientes são responsáveis por manter um inventário de software aprovado em execução no ambiente para garantir que são software aprovado por política de organização.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts em recursos computacional

**Orientação**: Azure Cloud Shell é uma experiência de linha de comando baseada no navegador que é usada para gestão interativa de recursos em nuvem.  As ações que são tomadas dentro da Cloud Shell funcionam da mesma forma que as ações tomadas a partir das mesmas ferramentas ou idiomas funcionam num ambiente local.  As ações de ferramentas e idiomas individuais devem ser restringidas, os clientes não podem restringir o acesso à Cloud Shell ou restringir o que está disponível para um utilizador.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: A Azure Cloud Shell pode ser isolada numa rede virtual do cliente.

- [Implementar a Cloud Shell numa rede virtual Azure](private-vnet.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Cloud Shell permite que scripts sejam executados, da autoria e enviados para o ambiente Cloud Shell.  Mudar credenciais para a Azure Key Vault é a nossa recomendação.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para mais informações, consulte o [Azure Security Benchmark: Malware defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Utilizar software antimalware gerido centralmente

**Orientação**: Azure Cloud Shell é uma experiência de linha de comando baseada no navegador que é usada para gestão interativa de recursos em nuvem.  Cada recipiente de cliente é efémero, um novo recipiente é utilizado para cada sessão.  As imagens e ferramentas do contentor são monitorizadas e atualizadas pela equipa cloud Shell.  O cliente é capaz de instalar as suas próprias ferramentas à sua própria imagem de acordo com as suas necessidades organizacionais e as ferramentas não requerem `sudo` permissões durante a instalação.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: Assegurar que o software e assinaturas antimalware sejam atualizados

**Orientação**: Azure Cloud Shell é uma experiência de linha de comando baseada no navegador que é usada para gestão interativa de recursos em nuvem.  Cada recipiente de cliente é efémero, um novo recipiente é utilizado para cada sessão.  As imagens e ferramentas do contentor são monitorizadas e atualizadas pela equipa cloud Shell.  O cliente é capaz de instalar as suas próprias ferramentas à sua própria imagem de acordo com as suas necessidades organizacionais e as ferramentas não requerem `sudo` permissões durante a instalação.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para mais informações, consulte o [Benchmark de Segurança Azure: Resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente.
- [Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](../security-center/security-center-planning-and-operations-guide.md) 
- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 
- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 
- [O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) 

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.
Além disso, marque claramente as subscrições (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos da Azure.
- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md) 
- [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md) 

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.
- [Consulte a publicação do NIST: Guia para testar, treinar e exercitar programas para planos e capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf) 

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.
- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md) 

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a função exportação contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas Sentinel.
- [Como configurar a exportação contínua](../security-center/continuous-export.md) 
- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md) 

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.
- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios de equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as regras de teste de penetração da Microsoft cloud para garantir que os seus testes de penetração não violam as políticas da Microsoft. Utilize a estratégia e a execução de "Equipas de Ataque" e os testes de penetração no local em direto da Microsoft na infraestrutura, nos serviços e nas aplicações cloud geridas pela Microsoft.
- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 
- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e) 

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência de segurança Azure](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)