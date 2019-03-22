---
title: Segurança do Azure e o esquema de conformidade - aplicação Web de IaaS para NHS do Reino Unido
description: Segurança do Azure e o esquema de conformidade - aplicação Web de IaaS para NHS do Reino Unido
services: security
author: jomolesk
ms.assetid: 6081bab2-315a-4af4-92a1-7c773f449d66
ms.service: security
ms.topic: article
ms.date: 06/15/2018
ms.author: jomolesk
ms.openlocfilehash: 5f7f9641e8fc7cd4c0e8dd153b350b9dd876b004
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58168695"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-uk-nhs"></a>Azure Security and Compliance Blueprint: Aplicação Web de IaaS para NHS do Reino Unido

## <a name="overview"></a>Descrição geral

Este Azure no Security and Compliance Blueprint fornece uma arquitetura de referência e orientação para uma infraestrutura como um aplicativo da web do serviço (IaaS) adequado para a coleção, armazenamento e obtenção de dados de cuidados de saúde. Esta solução demonstra maneiras em que, os clientes podem estar em conformidade com a orientação fornecida na [guia de práticas de bom em segurança de Cloud](https://digital.nhs.uk/data-and-information/looking-after-information/data-security-and-information-governance/nhs-and-social-care-data-off-shoring-and-the-use-of-public-cloud-services/health-and-social-care-cloud-security-good-practice-guide) publicada pela [NHS Digital](https://digital.nhs.uk/), um parceiro do Reino Unido (Reino Unido) Departamento de estado de funcionamento e sociais cuidados de saúde (DHSC). O guia de práticas de boa de segurança do Cloud baseia-se a 14 [princípios de segurança de Cloud](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) publicado pelo do Reino Unido National Cyber Security Centre (NCSC).

Esta arquitetura de referência, o guia de implementação e o modelo de risco são deve ser usado como uma base para os clientes que se ajustar às suas necessidades específicas e não devem ser utilizados como-está num ambiente de produção sem configuração adicional. Os clientes são responsáveis pela realização de segurança adequados e avaliações de conformidade de qualquer solução criada com esta arquitetura, como os requisitos podem variar com base nas especificidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama de arquitetura e componentes

Esta solução implementa uma arquitetura de referência para um aplicativo da web de IaaS com um back-end do SQL Server. A arquitetura inclui uma camada web, camada de dados, infra-estrutura do Active Directory, Gateway de aplicação e Balanceador de carga. Máquinas virtuais implementadas para os escalões web e os dados são configuradas num conjunto de disponibilidade e instâncias do SQL Server são configuradas num grupo de disponibilidade Always On para elevada disponibilidade. As máquinas virtuais estão associados a um domínio e políticas de grupo do Active Directory são utilizadas para impor configurações de segurança e conformidade ao nível do sistema operativo.

A solução utiliza contas de armazenamento do Azure, o que os clientes podem configurar para utilizar a encriptação do serviço de armazenamento para manter a confidencialidade dos dados em repouso. Azure armazena três cópias dos dados num datacenter escolhido de um cliente para resiliência. Armazenamento com redundância geográfico garante que os dados vão ser replicados para um datacenter secundário centenas de quilómetros de distância e novamente armazenados como três cópias dentro desse datacenter, impedir que um evento adverso no Centro de dados principal do cliente resulta numa perda de dados.

Para maior segurança, todos os recursos nesta solução são geridos como um grupo de recursos através do Gestor de recursos do Azure. O Azure Active Directory, o controlo de acesso baseado em funções é utilizado para controlar o acesso ao implementar recursos, incluindo as chaves no Azure Key Vault. Estado de funcionamento do sistema é monitorizado através do Centro de segurança do Azure e o Azure Monitor. Os clientes configurar ambos os serviços de monitorização para capturar os registos e exibir o estado de funcionamento do sistema num dashboard único, navegável facilmente. O Gateway de aplicação do Azure está configurado como uma firewall no modo de prevenção e não permite o tráfego que não seja TLSv1.2.

Anfitrião de bastião de gestão fornece uma ligação segura para os administradores de recursos de acesso implementado. **A Microsoft recomenda configurar uma ligação VPN ou ExpressRoute para importação de dados e de gestão para a sub-rede de arquitetura de referência.**

![Aplicação Web de IaaS para o diagrama de arquitetura de referência do Reino Unido NHS](images/uknhs-iaaswa-architecture.png?raw=true "aplicativo da Web de IaaS para o diagrama de arquitetura de referência de NHS do Reino Unido")

Esta solução utiliza os seguintes serviços do Azure. Detalhes da arquitetura de implementação estão localizadas no [arquitetura de implantação](#deployment-architecture) secção.

- Máquinas Virtuais do Azure
    - (1) gestão/bastion (Windows Server 2016 Datacenter)
    - (2) controlador de domínio do Active Directory (Windows Server 2016 Datacenter)
    - (2) nó de cluster o SQL Server (SQL Server 2017 no Windows Server 2016)
    - (2) web/IIS (Centro de dados do Windows Server 2016)
- Conjuntos de Disponibilidade
    - (1) controladores de domínio o active Directory
    - (1) nós de cluster do SQL
    - (1) web/IIS
- Rede Virtual do Azure
    - (1) /16 de rede
    - (5) redes /24
    - (5) grupo de segurança de rede
    - Cofre dos serviços de recuperação
- Gateway de Aplicação do Azure
    - (1) firewall de aplicações web
        - Modo de firewall: prevenção
        - Conjunto de regras: OWASP 3.0
        - Porta do serviço de escuta: 443
- Azure Active Directory
- Testemunho de nuvem do Azure
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Centro de Segurança do Azure
- Automatização do Azure
- Storage do Azure
    - (7) contas de armazenamento com redundância geográfica

## <a name="deployment-architecture"></a>Arquitetura de implantação

A secção seguinte fornece detalhes sobre os elementos de implantação e a implementação.

**Anfitrião de bastião**: O anfitrião de bastião é o único ponto de entrada que permite aos utilizadores aceder os recursos implementados neste ambiente. O anfitrião de bastião fornece uma ligação segura a recursos implementados, permitindo apenas tráfego remoto a partir de endereços IP públicos numa lista segura. Para permitir o tráfego de (protocolo RDP) de área de trabalho remoto, a origem do tráfego deve ser definido no grupo de segurança de rede.

Esta solução cria uma máquina virtual como um anfitrião de bastião associados a um domínio com as seguintes configurações:
-   [Extensão de antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Extensão de diagnóstico do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [O Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) com o Azure Key Vault
-   Uma [política de encerramento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reduzir o consumo de recursos de máquina virtual quando não está em utilização
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) ativado para que as credenciais e outros segredos executar num ambiente protegido, que está isolado do sistema operativo em execução

### <a name="virtual-network"></a>Rede virtual

A arquitetura define uma rede privada virtual com um espaço de endereços de 10.200.0.0/16.

**Grupos de segurança de rede**: Esta solução implementa recursos numa arquitetura com uma sub-rede de web separado, uma sub-rede de base de dados, sub-rede do Active Directory e a sub-rede de gestão na mesma rede virtual. Sub-redes logicamente são separados por aplicadas às sub-redes individuais para restringir o tráfego entre sub-redes para que apenas necessário para a funcionalidade de gerenciamento do sistema e de regras de grupo de segurança de rede.

Consulte a configuração para [grupos de segurança de rede](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) implementado com esta solução. As organizações podem configurar grupos de segurança de rede ao editar o ficheiro acima usando [esta documentação](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) como guia.

Cada um das sub-redes tem um grupo de segurança de rede dedicado:
- grupo de segurança de rede de 1 para o Gateway de aplicação (LBNSG)
- grupo de segurança de rede de 1 para o anfitrião de bastião (MGTNSG)
- grupo de segurança de rede de 1 para controladores de domínio primário e de cópia de segurança (ADNSG)
- grupo de segurança de rede de 1 para SQL Servers e testemunho de nuvem (SQLNSG)
- grupo de segurança de rede de 1 para a camada web (WEBNSG)

### <a name="data-in-transit"></a>Dados em trânsito
Azure encripta todas as comunicações de e para os datacenters do Azure por predefinição. Além disso, todas as transações de armazenamento do Azure através do portal do Azure ocorrem através de HTTPS.

### <a name="data-at-rest"></a>Dados inativos

A arquitetura protege os dados em descanso através da encriptação, a auditoria de base de dados e outras medidas.

**O armazenamento do Azure**: Para atender aos dados criptografados em requisitos de rest, todos os [armazenamento do Azure](https://azure.microsoft.com/services/storage/) utiliza [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Isto ajuda a proteger e salvaguardar os dados para oferecer suporte a compromissos de segurança organizacional e requisitos de conformidade definidos pelo NHS Digital.

**Azure Disk Encryption**: [O Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aproveita a funcionalidade BitLocker do Windows para fornecer encriptação de volume para discos de dados. A solução se integra com o Azure Key Vault para ajudar a controlar e gerir as chaves de encriptação de disco.

**Base de dados SQL do Azure**: A instância de base de dados do Azure SQL usa as seguintes medidas de segurança da base de dados:

- [Autenticação do Active Directory e a autorização](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permite a gestão de identidades de utilizadores de base de dados e outros serviços da Microsoft num local central.
- [A auditoria de base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) faixas de base de dados eventos e escreve-os para uma auditoria registo numa conta de armazenamento do Azure.
- Base de dados SQL do Azure está configurado para utilizar [encriptação de dados transparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que realiza a encriptação em tempo real e a desencriptação da base de dados, cópias de segurança associadas e ficheiros de registo de transação para proteger as informações em rest. Encriptação de dados transparente fornece garantia de que são armazenados os dados não tiver sido sujeita a acesso não autorizado.
- [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedir o acesso de todos os servidores de base de dados até que sejam concedidas as permissões apropriadas. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
- [Deteção de ameaças SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite a deteção e resposta a ameaças potenciais à medida que ocorrem ao fornecer alertas de segurança para atividades suspeitas da base de dados, potenciais vulnerabilidades, ataques de injeção de SQL e acesso de base de dados anómalas padrões.
- [Encriptados colunas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Certifique-se de que os dados confidenciais nunca é apresentada como texto não criptografado dentro do sistema de base de dados. Depois de ativar a encriptação de dados, apenas as aplicações de cliente ou servidores de aplicações com acesso às chaves podem aceder aos dados de texto sem formatação.
- [Máscara de dados dinâmicos da base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais ao mascará os dados para aplicações ou utilizadores sem privilégios. Máscara de dados dinâmica pode detetar dados potencialmente confidenciais e sugerir as máscaras de apropriado a ser aplicada automaticamente. Isto ajuda a identificar e reduzir o acesso aos dados, de modo que ele não for fechada a base de dados por meio do acesso não autorizado. Os clientes são responsáveis por ajustar as definições de cumprir para seu esquema de base de dados de máscara de dados dinâmicos.

### <a name="identity-management"></a>Gestão de identidades

As seguintes tecnologias fornecem capacidades para gerir o acesso aos dados no ambiente do Azure:

- [O Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é o Microsoft&#39;s multi-inquilino com base na cloud diretório e identidade do serviço de gestão. Todos os utilizadores para esta solução são criados no Azure Active Directory, incluindo os utilizadores que acedem a base de dados do SQL do Azure.
- Autenticação para a aplicação é executada com o Azure Active Directory. Para obter mais informações, consulte [integrar aplicações com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Além disso, a encriptação de coluna de base de dados utiliza o Azure Active Directory para autenticar a aplicação à base de dados do Azure SQL. Para obter mais informações, consulte como [proteger dados confidenciais na base de dados do Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Controlo de acesso baseado em função do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite aos administradores definir permissões de acesso detalhado para conceder apenas a quantidade de acesso que os utilizadores precisam realizar seus trabalhos. Em vez de dar a cada permissão de utilizador sem restrições para recursos do Azure, os administradores podem permitir apenas certas ações para aceder aos dados. Acesso à subscrição está limitado ao administrador da subscrição.
- [O Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permite que os clientes minimizar o número de utilizadores que têm acesso a determinadas informações. Os administradores podem usar o Azure Active Directory Privileged Identity Management para detetar, restringir e monitorizar identidades privilegiadas e respetivo acesso a recursos. Esta funcionalidade também pode ser utilizada para impor acesso administrativo a pedido e just-in-time quando necessário.
- [O Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) Deteta potenciais vulnerabilidades que afetam uma organização&#39;identidades s, configura as respostas automáticas para detetado ações suspeitas relacionadas com uma organização&#39;identidades s e investiga os incidentes suspeitos para tomar as medidas adequadas para resolvê-los.


### <a name="security"></a>Segurança

**Gestão de segredos**: A solução utiliza [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços. As seguintes capacidades do Azure Key Vault ajudam os clientes a proteger e aceder a esses dados:

- Políticas de acesso avançadas são configuradas de forma necessidade.
- Políticas de acesso do Cofre de chaves são definidas com o mínimo permissões necessárias para chaves e segredos.
- Todas as chaves e segredos no Key Vault tem datas de expiração.
- Todas as chaves no Cofre de chaves estão protegidas por módulos de segurança de hardware especializado. O tipo de chave estiver protegida por um módulo de hardware de segurança chave RSA de 2048 bits.
- Todos os utilizadores e identidades são concedidas as permissões mínimas necessárias usando o controle de acesso baseado em funções.
- Registos de diagnóstico para o Cofre de chaves estão ativados com um período de retenção de, pelo menos, 365 dias.
- Operações de criptografia permitidas para as chaves estão limitadas aos obrigatórios.
- A solução está integrada no Azure Key Vault para gerir chaves de encriptação de disco de máquina virtual IaaS e segredos.

**Gerenciamento de patches**: Máquinas de virtuais do Windows implementadas como parte desta arquitetura de referência estão configuradas por predefinição para receber atualizações automáticas do serviço de atualização do Windows. Esta solução também inclui a [automatização do Azure](https://docs.microsoft.com/azure/automation/automation-intro) serviço por meio do qual as implementações atualizadas podem ser criadas para máquinas de virtuais de patch quando necessário.

**Proteção contra Malware**: [O Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para máquinas virtuais fornece a capacidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outro software malicioso, com alertas configuráveis quando conhecido software malicioso ou indesejável tenta instalar ou executar nas máquinas virtuais protegidas.

**Centro de segurança do Azure**: Com o [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), os clientes podem centralmente aplicam-se e gerir políticas de segurança em cargas de trabalho, limitar a exposição a ameaças e detetar e responder a ataques. Além disso, o Centro de segurança do Azure acessa as configurações existentes dos serviços do Azure para fornecer a configuração e recomendações de serviço para o ajudar a melhorar a postura de segurança e proteger os dados.

Centro de segurança do Azure utiliza uma variedade de capacidades de deteção para alertar os clientes de possíveis ataques direcionados aos respetivos ambientes. Estes alertas contêm informações valiosas sobre o que acionou o alerta, os recursos afetados e a origem do ataque. Centro de segurança do Azure tem um conjunto de [predefinidos alertas de segurança](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), que são acionados quando uma ameaça ou atividade suspeita tem lugar. [Regras de alerta personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) no Centro de segurança do Azure permitem aos clientes definir novos alertas de segurança com base nos dados que já são recolhidos do seu ambiente.

Centro de segurança do Azure fornece alertas de segurança priorizados e incidentes, tornando mais simples para os clientes detetar e resolver potenciais problemas de segurança. R [relatório de inteligência de ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) é gerado para cada detetado contra ameaças para ajudar as equipes de resposta a incidentes a investigar e resolver ameaças.

Além disso, esta arquitetura de referência utiliza o [avaliação de vulnerabilidade](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) no Centro de segurança do Azure. Depois de configurada, um agente de parceiro (por exemplo, Qualys) relatórios de dados de vulnerabilidade à plataforma de gestão do parceiro. Por sua vez, plataforma de gestão do parceiro fornece vulnerabilidade e o estado de funcionamento a monitorização dos dados para o Centro de segurança do Azure, permitindo que os clientes a identificar rapidamente as máquinas de virtuais vulneráveis.

**Gateway de aplicação do Azure**: A arquitetura reduz o risco de vulnerabilidades de segurança com um Gateway de aplicação do Azure com uma firewall de aplicações web configurado e o conjunto de regras do OWASP ativada. Capacidades adicionais incluem:

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Ativar [descarga de SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Desativar [TLS versões 1.0 e 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall de aplicações Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modo de prevenção)
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com o conjunto de regras do OWASP 3.0
- Ativar [registo de diagnósticos](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Sondas de estado de funcionamento personalizados](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center) e [Assistente do Azure](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fornecer proteção adicional e notificações. Centro de segurança do Azure também fornece um sistema de reputação.

### <a name="business-continuity"></a>Continuidade do negócio

**Elevada disponibilidade**: A solução implementa todas as máquinas virtuais num [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Conjuntos de disponibilidade garantem que as máquinas virtuais são distribuídas por vários clusters de hardware isolados para melhorar a disponibilidade. Pelo menos uma máquina virtual está disponível durante um evento de manutenção não planeada ou não planeada, atendendo a 99,95% do SLA do Azure.

**Cofre de serviços de recuperação**: O [cofre dos Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) hospeda os dados de cópia de segurança e protege todas as configurações de máquinas virtuais do Azure nesta arquitetura. Com um cofre de serviços de recuperação, os clientes podem restaurar ficheiros e pastas da máquina virtual IaaS sem restaurar toda a máquina virtual, permitindo que os tempos de restauro mais rápidos.

**Testemunho de nuvem**: [Testemunho de nuvem](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) é um tipo de testemunho de quórum do Cluster de ativação pós-falha no Windows Server 2016 que tira partido do Azure como o ponto de arbitragem. O testemunho de nuvem, como outro testemunho de quórum, obtém um voto e pode participar nos cálculos do quórum, mas utiliza o armazenamento de Blobs do Azure disponíveis publicamente padrão. Isso elimina a sobrecarga de manutenção extra de máquinas virtuais alojadas numa nuvem pública.

### <a name="logging-and-auditing"></a>Registro e auditoria

Serviços do Azure extensivamente de registo do sistema e a atividade do utilizador, bem como o estado de funcionamento do sistema:
- **Registos de atividades**: [Registos de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações executadas em recursos numa subscrição. Registos de atividades podem ajudar a determinar o iniciador de uma operação, hora da ocorrência e o estado.
- **Os registos de diagnóstico**: [Os registos de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os registos emitidos por cada recurso. Estes registos incluem registos de sistema de eventos do Windows, registos de armazenamento do Azure, registos de auditoria do Cofre de chaves e os registos de acesso e de firewall do Gateway de aplicação. Todos os registos de diagnóstico escrevem para uma conta de armazenamento do Azure centralizado e criptografado para arquivamento. O período de retenção é configurável pelo utilizador, cópia de segurança e 730 dias, para atender aos requisitos de retenção de específicas da organização.

**Registos de Monitor do Azure**: Estes registos são consolidados no [registos do Azure Monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e relatórios do dashboard. Depois de recolhidos, os dados são organizados em tabelas separadas para cada tipo de dados, que permite que todos os dados sejam analisados em conjunto, independentemente da respetiva origem. Além disso, o Centro de segurança do Azure integra-se com os registos do Azure Monitor permitindo que os clientes utilizar consultas de Kusto para aceder aos respetivos dados de eventos de segurança e combiná-los com dados de outros serviços.

O Azure seguinte [soluções de monitorização](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) são incluídos como parte desta arquitetura:
-   [Avaliação do Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de verificação de estado de funcionamento do Active Directory avalia o risco e estado de funcionamento dos ambientes de servidor num intervalo regular e fornece uma lista prioritária de recomendações específicas para a infraestrutura de servidor implementado.
- [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de verificação de estado de funcionamento do SQL avalia o risco e estado de funcionamento dos ambientes de servidor num intervalo regular e fornece aos clientes uma lista prioritária de recomendações específicas para a infraestrutura de servidor implementado.
- [Estado de funcionamento do agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução de estado de funcionamento do agente relata o número de agentes é implementado e a distribuição geográfica, como número de agentes que não respondem e o número de agentes que está a enviar dados operacionais.
-   [Log Analytics da atividade](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução Log Analytics da atividade auxilia com uma análise de registos de atividade do Azure em todas as subscrições do Azure para um cliente.

**A automatização do Azure**: [A automatização do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gere runbooks. Nesta solução, runbooks ajudam a recolher registos de base de dados do Azure SQL. A automação [controlo de alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking) solução permite aos clientes identificar facilmente as alterações no ambiente.

**Azure Monitor**: [O Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a acompanhar o desempenho, manter a segurança e identificar tendências, permitindo que as organizações de auditoria, criar alertas e arquivar dados, incluindo o controlo de chamadas à API em seus recursos do Azure.

## <a name="threat-model"></a>Modelo de risco

O diagrama de fluxo de dados para esta arquitetura de referência está disponível para [transferir](https://aka.ms/uknhs-iaaswa-tm) ou podem ser encontradas abaixo. Esse modelo pode ajudar os clientes a compreender os pontos do potencial risco de na infraestrutura de sistema ao fazer modificações.

![Aplicação Web de IaaS para o modelo de risco do Reino Unido NHS](images/uknhs-iaaswa-threat-model.png?raw=true "aplicativo da Web de IaaS para o modelo de risco NHS do Reino Unido")

## <a name="compliance-documentation"></a>Documentação de conformidade

O [Azure no Security and Compliance Blueprint – matriz de responsabilidade do Reino Unido NHS cliente](https://aka.ms/uknhs-crm) apresenta uma lista de todos os requisitos de NHS do Reino Unido. Essa matriz se a implementação de cada princípio é da responsabilidade da Microsoft, o cliente, os detalhes ou partilhados entre os dois.

O [Azure no Security and Compliance Blueprint – matriz implementação de aplicação Web do Reino Unido NHS IaaS](https://aka.ms/uknhs-iaaswa-cim) fornece informações sobre quais NHS do Reino Unido requisitos são tratados por arquitetura de aplicativos web de IaaS, incluindo detalhada descrições de como a implementação cumpre os requisitos de cada princípio coberto.

## <a name="guidance-and-recommendations"></a>Orientações e recomendações

### <a name="vpn-and-expressroute"></a>ExpressRoute e VPN

Um túnel VPN seguro ou [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) tem de ser configurado com segurança estabelecer uma ligação para os recursos implementados como parte desta arquitetura de referência de aplicação de web de IaaS. Configurando adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para dados em trânsito.

Ao implementar um túnel VPN seguro com o Azure, é possível criar uma ligação privada virtual entre uma rede no local e uma rede virtual do Azure. Esta ligação ocorre através da Internet e permite que os clientes de forma segura &quot;túnel&quot; informações dentro de uma ligação encriptada entre o cliente&#39;rede s e o Azure. VPN site a site é uma tecnologia madura e segura que tenha sido implementada por empresas de todos os tamanhos há décadas. O [modo de encapsulamento IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é utilizada nesta opção como um mecanismo de criptografia.

Porque o tráfego de túnel VPN atravessa a Internet com uma VPN de site a site, a Microsoft oferece a opção de ligação de outra, ainda mais seguro. O Azure ExpressRoute é uma WAN dedicada ligação entre o Azure e uma localização no local ou um fornecedor de alojamento do Exchange. Como as ligações ExpressRoute não entram na Internet, estas ligações oferecem mais fiabilidade, velocidades mais rápidas, latências mais baixas e maior segurança do que as ligações normais pela Internet. Além disso, como se trata de uma conexão direta de cliente&#39;fornecedor de telecomunicação s, os dados não percorre a Internet e, portanto, não são expostos ao mesmo.

Melhores práticas para implementar uma rede híbrida segura que expande uma rede no local para o Azure são [disponível](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Exclusão de Responsabilidade

- Este documento é apenas para fins informativos. A MICROSOFT FAZ NÃO OFERECE QUAISQUER GARANTIAS, EXPRESSAS, IMPLÍCITAS OU ESTATUTÁRIAS QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "como-está." Informações e opiniões expressados neste documento, incluindo URLs e outras referências de Web site da Internet, podem ser alteradas sem aviso prévio. Os clientes a ler este documento assume o risco de usá-lo.
- Este documento não fornece aos clientes quaisquer direitos de propriedade intelectual em qualquer produto da Microsoft ou soluções.
- Os clientes podem copiar e utilizar este documento para efeitos de referência interna.
- Algumas recomendações neste documento podem resultar em maior de dados, a rede ou a utilização de recursos de computação no Azure e podem aumentar os custos de licenciamento ou de subscrição do Azure num cliente.
- Esta arquitetura deve ser usado como uma base para os clientes que se ajustar às suas necessidades específicas e não devem ser utilizada como-está num ambiente de produção.
- Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos requisitos de conformidade e normas. Os clientes procuram legal suporte da sua organização em implementações do cliente aprovadas.
