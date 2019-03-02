---
title: Segurança do Azure e o esquema de conformidade - aplicação Web de PaaS para FedRAMP
description: Segurança do Azure e o esquema de conformidade - aplicação Web de PaaS para FedRAMP
services: security
author: jomolesk
ms.assetid: 41570fc1-4d74-48ed-afb0-ef1be857029e
ms.service: security
ms.topic: article
ms.date: 06/01/2018
ms.author: jomolesk
ms.openlocfilehash: 46c72191ee17f63311b041d798cccec279e4b000
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244694"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-fedramp"></a>Azure Security and Compliance Blueprint: Aplicação Web de PaaS para FedRAMP

## <a name="overview"></a>Descrição geral

O [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) é um programa de toda a administração pública dos Estados Unidos que oferece uma abordagem padronizada à avaliação de segurança, autorização e monitorização contínua para cloud produtos e serviços. Este Azure no Security and Compliance Blueprint fornece orientações sobre como fornecer uma plataforma Microsoft Azure como uma arquitetura de serviço (PaaS) que ajuda a implementar um subconjunto de controles do FedRAMP elevado. Esta solução fornece orientações sobre a implementação e configuração de recursos do Azure para uma arquitetura de referência comuns, que demonstram formas em que os clientes podem satisfazer os requisitos específicos de segurança e conformidade e serve como uma base para os clientes criar e configurar suas próprias soluções no Azure.

Esta arquitetura de referência, guias de implementação do controlo associado e modelos de ameaça são deve ser usado como uma base para os clientes que se ajustar às suas necessidades específicas e não devem ser utilizados como-está num ambiente de produção. Implementar uma aplicação para este ambiente sem modificação não é suficiente para atender completamente aos requisitos da linha de base FedRAMP elevado. Tenha em atenção o seguinte:
- A arquitetura fornece uma linha de base para ajudar os clientes a implementar cargas de trabalho para o Azure de forma em conformidade com FedRAMP.
- Os clientes são responsáveis pela realização de segurança adequados e avaliações de conformidade de qualquer solução criada com esta arquitetura, como os requisitos podem variar com base nas especificidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama de arquitetura e componentes
Esta solução fornece uma arquitetura de referência para um aplicativo da web de PaaS com um back-end de base de dados do Azure SQL. O aplicativo web é hospedado num Azure App Service ambiente isolado, que é um ambiente privado e dedicado num datacenter do Azure. A carga de ambiente equilibra o tráfego para a aplicação web em VMs geridas pelo Azure. Esta arquitetura também inclui os grupos de segurança de rede, um Gateway de aplicação, o DNS do Azure e o Balanceador de carga. Além disso, o Azure Monitor fornece análise em tempo real do Estado de funcionamento do sistema. **Azure recomenda configurar uma ligação VPN ou ExpressRoute para importação de dados e de gestão para a sub-rede de arquitetura de referência.**

![Aplicação Web de PaaS para o diagrama de arquitetura de referência do FedRAMP](images/fedramp-paaswa-architecture.png?raw=true "PaaS aplicativo da Web para FedRAMP diagrama da arquitetura de referência")

Esta solução utiliza os seguintes serviços do Azure. Detalhes da arquitetura de implementação estão localizadas no [arquitetura de implantação](#deployment-architecture) secção.

- Azure Active Directory
- Azure Key Vault
- Base de Dados SQL do Azure
- Gateway de Aplicação
    - (1) Firewall de aplicações web
        - Modo de firewall: prevenção
        - Conjunto de regras: OWASP 3.0
        - serviço de escuta: porta 443
- Rede virtual do Azure
- Grupos de segurança de rede
- DNS do Azure
- Storage do Azure
- Azure Monitor
- Ambiente de serviço de aplicações v2
- Azure Load Balancer
- Aplicação Web do Azure
- Azure Resource Manager

## <a name="deployment-architecture"></a>Arquitetura de implantação
A secção seguinte fornece detalhes sobre os elementos de implantação e a implementação.

**Azure Resource Manager**: [O Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) permite que os clientes trabalhar com os recursos da solução como um grupo. Os clientes podem implementar, atualizar ou eliminar todos os recursos para a solução numa operação única e coordenada. Os clientes a utilizar um modelo para a implementação e esse modelo pode funcionar para ambientes diferentes, tais como teste, transição e produção. O Resource Manager proporciona segurança, auditoria e etiquetagem recursos para ajudar os clientes a gerir os seus recursos após a implementação.

**Ambiente de serviço de aplicações v2**: O [ambiente de serviço de aplicações (ASE) do Azure](https://docs.microsoft.com/azure/app-service/environment/intro) é uma funcionalidade do serviço de aplicações que fornece um ambiente totalmente isolado e dedicado para uma execução segura de aplicativos de serviço de aplicações a uma escala elevada.

Os ASEs são isolados para apenas executar aplicativos de um único cliente e são sempre implementados numa rede virtual. Os clientes têm um controle refinado sobre o tráfego de rede aplicação de entrada e saída e as aplicações podem estabelecer conexões seguras de alta velocidade nas redes virtuais para recursos da empresa no local.

Utilização dos ASEs para esta arquitetura são permitidas para as seguintes configurações/controles:

- Alojar dentro de uma rede Virtual do Azure seguro e regras de segurança de rede
- ASE configurado com o certificado autoassinado do ILB para comunicação HTTPS
- [Modo de balanceamento de carga interno](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer)
- Desativar [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Alteração [cifras TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Controle [portas de N/W de tráfego de entrada](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Firewall de aplicações Web – restringir dados](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Permitir [tráfego de base de dados do Azure SQL](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

O [orientações e recomendações](#guidance-and-recommendations) secção contém informações adicionais sobre os ASEs.

**Aplicação Web do Azure**: [Serviço de aplicações do Azure](https://docs.microsoft.com/azure/app-service/) permite aos clientes criar e alojar aplicações web na linguagem de programação de sua preferência sem ter de gerir infraestrutura. Este serviço garante dimensionamento automático e elevada disponibilidade, suporta Windows e Linux e permite implementações automáticas a partir do GitHub, DevOps do Azure ou de qualquer repositório de Git.

### <a name="virtual-network"></a>Rede Virtual
A arquitetura define uma rede privada virtual com um espaço de endereços de 10.200.0.0/16.

**Grupos de segurança de rede**: [Grupos de segurança (NSGs) de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contêm listas de controlo de acesso que permitem ou negam o tráfego dentro de uma rede virtual. Podem ser utilizados NSGs para proteger o tráfego numa sub-rede ou o nível VM individual. Existem os NSGs seguintes:
- 1 NSG para o Gateway de aplicação
- 1 NSG para o ambiente de serviço de aplicações
- 1 NSG para a base de dados SQL do Azure

Cada um dos NSGs têm portas específicas e protocolos abrir para que a solução pode trabalhar de forma segura e corretamente. Além disso, as seguintes configurações são habilitadas para cada NSG:
  - [Eventos e registos de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) forem ativadas e armazenados numa conta de armazenamento
  - Registos de Monitor do Azure está ligada a [diagnósticos do NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Sub-redes**: Cada sub-rede está associado a seu NSG correspondente.

**O DNS do Azure**: O sistema de nomes de domínio ou DNS, é responsável pela tradução (ou resolver) um nome de Web site ou serviço para o endereço IP. [O DNS do Azure](https://docs.microsoft.com/azure/dns/dns-overview) é um serviço de alojamento dos domínios DNS que fornece resolução de nome, através da infraestrutura do Azure. Ao alojar os domínios no Azure, os utilizadores podem gerir registos DNS com as mesmas credenciais, APIs, ferramentas e faturação dos outros serviços do Azure. O DNS do Azure também suporta domínios DNS privados.

**Balanceador de carga do Azure**: [O Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) permite aos clientes Dimensionar seus aplicativos e criar disponibilidade elevada para serviços. Balanceador de carga dá suporte a cenários de entrada e de saída e fornece um débito elevado, de baixa latência e aumenta até milhões de fluxos para todas as aplicações TCP e UDP.

### <a name="data-in-transit"></a>Dados em trânsito
Azure encripta todas as comunicações de e para os datacenters do Azure por predefinição. A ocorrência de todas as transações para o armazenamento do Azure através do portal do Azure através de HTTPS.

### <a name="data-at-rest"></a>Dados inativos
A arquitetura protege os dados em descanso através da encriptação, a auditoria de base de dados e outras medidas.

**O armazenamento do Azure**: Para atender aos dados criptografados em requisitos de rest, todos os [armazenamento do Azure](https://azure.microsoft.com/services/storage/) utiliza [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**O Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aproveita a funcionalidade BitLocker do Windows para fornecer encriptação de volume para discos de dados. A solução se integra com o Azure Key Vault para ajudar a controlar e gerir as chaves de encriptação de disco.

**Base de dados SQL do Azure**: A instância de base de dados do Azure SQL usa as seguintes medidas de segurança da base de dados:
-   [Autenticação e autorização AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permite a gestão de identidades de utilizadores de base de dados e outros serviços da Microsoft num local central.
-   [A auditoria de base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) faixas de base de dados eventos e escreve-os para uma auditoria registo numa conta de armazenamento do Azure.
-   Base de dados SQL do Azure está configurado para utilizar [encriptação de dados transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que realiza a encriptação em tempo real e a desencriptação da base de dados, cópias de segurança associadas e ficheiros de registo de transação para proteger as informações em rest.
-   [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedir o acesso de todos os servidores de base de dados até que sejam concedidas as permissões apropriadas. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
-   [Deteção de ameaças SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite a deteção e resposta a ameaças potenciais à medida que ocorrem ao fornecer alertas de segurança para atividades suspeitas da base de dados, potenciais vulnerabilidades, ataques de injeção de SQL e acesso de base de dados anómalas padrões.
-   [Sempre as colunas encriptadas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Certifique-se de que os dados confidenciais nunca é apresentada como texto não criptografado dentro do sistema de base de dados. Depois de ativar a encriptação de dados, apenas as aplicações de cliente ou servidores de aplicações com acesso às chaves podem aceder aos dados de texto sem formatação.
- [Segurança ao nível da linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permite aos utilizadores definir políticas para restringir o acesso aos dados de descontinuar o processamento.
- [Máscara de dados dinâmicos da base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) pode ser feito depois de implementa a arquitetura de referência. Os clientes terão de ajustar as definições de cumprir para seu esquema de base de dados de máscara de dados dinâmicos.

### <a name="identity-management"></a>Gestão de identidades
As seguintes tecnologias fornecem capacidades de gestão no ambiente do Azure de identidade:
-   [O Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é multi-inquilino com base na cloud diretório e identidade do serviço de gestão da Microsoft. Todos os utilizadores para esta solução são criados no AAD, incluindo os utilizadores que acedem a base de dados do SQL do Azure.
-   Autenticação para a aplicação é executada através do AAD. Para obter mais informações, consulte [integrar aplicações com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Além disso, a encriptação de coluna de base de dados utiliza o Azure Active Directory para autenticar a aplicação à base de dados do Azure SQL. Para obter mais informações, consulte como [proteger dados confidenciais na base de dados do Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Controlo de acesso baseado em função do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite a gestão de acesso direcionado precisamente para o Azure. Acesso à subscrição está limitado ao administrador da subscrição e acesso a recursos pode ser limitado com base na função de utilizador.
- [O Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permite que os clientes minimizar o número de utilizadores que têm acesso a determinadas informações.  Os administradores podem utilizar o AAD Privileged Identity Management para detetar, restringir e monitorizar identidades privilegiadas e respetivo acesso a recursos. Esta funcionalidade também pode ser utilizada para impor acesso administrativo a pedido e just-in-time quando necessário.
- [O Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) Deteta potenciais vulnerabilidades que afetam as identidades da organização, configura as respostas automáticas para detetado ações suspeitas relacionadas com identidades de uma organização e investiga incidentes suspeitos para tomar as medidas adequadas para resolvê-los.

### <a name="security"></a>Segurança
**Gestão de segredos** a solução utiliza [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços. As seguintes capacidades do Azure Key Vault ajudam os clientes a protegerem dados e acesso a esses dados:
- Políticas de acesso avançadas são configuradas de forma necessidade.
- Políticas de acesso do Cofre de chaves são definidas com o mínimo permissões necessárias para chaves e segredos.
- Todas as chaves e segredos no Key Vault tem datas de expiração.
- Todas as chaves no Cofre de chaves estão protegidas por módulos de hardware especializado de segurança (HSMs). O tipo de chave é uma chave RSA de 2048 protegidas de HSM bits.
- Todos os utilizadores e identidades são concedidas as permissões mínimas necessárias usando o controle de acesso baseado em funções.
- Registos de diagnóstico para o Cofre de chaves estão ativados com um período de retenção de, pelo menos, 365 dias.
- Operações de criptografia permitidas para as chaves estão limitadas aos obrigatórios.

**Gateway de aplicação** a arquitetura reduz o risco de vulnerabilidades de segurança com um Gateway de aplicação com Firewall de aplicações Web e o conjunto de regras do OWASP ativada. Capacidades adicionais incluem:
- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Ativar [descarga de SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Desativar [TLS versões 1.0 e 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall de aplicações Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com o conjunto de regras do OWASP 3.0
- Ativar [registo de diagnósticos](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Sondas de estado de funcionamento personalizados](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center) e [Assistente do Azure](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fornecer proteção adicional e notificações. Centro de segurança do Azure também fornece um sistema de reputação.

### <a name="logging-and-auditing"></a>Registro e auditoria
O Azure Monitor proporciona extenso registo de atividade do sistema e do usuário, bem como o estado de funcionamento do sistema. Ele recolhe e analisa os dados gerados pelos recursos no Azure e ambientes no local.
- **Registos de atividades**: [Registos de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações executadas em recursos numa subscrição. Registos de atividades podem ajudar a determinar o iniciador de uma operação, hora da ocorrência e o estado.
- **Os registos de diagnóstico**: [Os registos de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os registos emitidos por cada recurso. Estes registos incluem registos de sistema de eventos do Windows, registos de armazenamento do Azure, registos de auditoria do Cofre de chaves e os registos de acesso e de firewall do Gateway de aplicação.
- **Arquivo de log**: Todos os registos de diagnóstico escrevem para uma conta de armazenamento do Azure centralizado e criptografado para arquivamento. O período de retenção é configurável pelo utilizador, cópia de segurança e 730 dias, para atender aos requisitos de retenção de específicas da organização. Estes registos ligar-se aos registos do Azure Monitor para processamento, armazenamento e relatórios do dashboard.

Além disso, as seguintes soluções de monitorização são incluídas como parte desta arquitetura:
-   [Do Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de verificação de estado de funcionamento do Active Directory avalia o risco e estado de funcionamento dos ambientes de servidor num intervalo regular e fornece uma lista prioritária de recomendações específicas para a infraestrutura de servidor implementado.
-   [Avaliação de antimalware](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): A solução Antimalware relatórios sobre o estado de malware, ameaças e a proteção.
-   [A automatização do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): A solução de automatização do Azure armazena, executa e gere runbooks. Nesta solução, runbooks ajudam a recolher registos do Application Insights e o SQL Database do Azure.
-   [Segurança e auditoria](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): O dashboard de segurança e auditoria fornece uma alto nível visão do Estado de segurança de recursos, fornecendo as métricas em domínios de segurança, problemas relevantes, deteções, informações sobre ameaças e consultas de segurança comuns.
-   [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de verificação de estado de funcionamento do SQL avalia o risco e estado de funcionamento dos ambientes de servidor num intervalo regular e fornece aos clientes uma lista prioritária de recomendações específicas para a infraestrutura de servidor implementado.
-   [Gestão de atualizações](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): A solução de gestão de atualizações permite a gestão de cliente de atualizações de segurança do sistema operativo, incluindo o processo de instalação de atualizações necessárias e o estado das atualizações disponíveis.
-   [Estado de funcionamento do agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução de estado de funcionamento do agente relata o número de agentes é implementado e a distribuição geográfica, como número de agentes que não respondem e o número de agentes que está a enviar dados operacionais.
-   [Registos de atividades do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução Log Analytics da atividade auxilia com uma análise de registos de atividade do Azure em todas as subscrições do Azure para um cliente.
-   [Controlo de alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking): A solução de controlo de alterações permite aos clientes identificar facilmente as alterações no ambiente.

**O Azure Monitor**
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a acompanhar o desempenho, manter a segurança e identificar tendências, permitindo que as organizações de auditoria, criar alertas e arquivar dados, incluindo chamadas da API de controlo em recursos do Azure dos clientes.

## <a name="threat-model"></a>Modelo de risco

O diagrama de fluxo de dados para esta arquitetura de referência está disponível para [transferir](https://aka.ms/fedrampPaaSWebAppDFD) ou podem ser encontradas abaixo. Esse modelo pode ajudar os clientes a compreender os pontos do potencial risco de na infraestrutura de sistema ao fazer modificações.

![Aplicação Web de PaaS para o modelo de risco do FedRAMP](images/fedramp-paaswa-threat-model.png?raw=true "aplicativo da Web de PaaS para o modelo de risco do FedRAMP")

## <a name="compliance-documentation"></a>Documentação de conformidade
O [Azure no Security and Compliance Blueprint - matriz de responsabilidade de cliente elevada do FedRAMP](https://aka.ms/blueprinthighcrm) apresenta uma lista de todos os controlos de segurança necessários para a linha de base do FedRAMP elevado. A matriz indica se a implementação de cada controle é da responsabilidade da Microsoft, o cliente, ou partilhados entre os dois.

O [segurança do Azure e o esquema de conformidade - FedRAMP PaaS WebApp elevada controle implementação matriz](https://aka.ms/fedrampPaaSWebAppCIM) apresenta uma lista de todos os controlos de segurança necessários para a linha de base do FedRAMP elevado. A matriz fornece informações nos quais controles estão abrangidos pela arquitetura de aplicativo da web PaaS, incluindo descrições detalhadas de como a implementação cumpre os requisitos de cada controle coberta.

## <a name="guidance-and-recommendations"></a>Orientações e recomendações
### <a name="vpn-and-expressroute"></a>ExpressRoute e VPN
Um túnel VPN seguro ou [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) tem de ser configurado com segurança estabelecer uma ligação para os recursos implementados como parte desta arquitetura de referência de aplicação de web de PaaS. Configurando adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para dados em trânsito.

Ao implementar um túnel VPN seguro com o Azure, é possível criar uma ligação VPN entre uma rede no local e uma rede Virtual do Azure. Essa conexão ocorre através da Internet e permite que os clientes com segurança as informações de "encapsulamento" dentro de uma ligação encriptada entre a rede do cliente e o Azure. VPN site a Site é uma tecnologia madura e segura que tenha sido implementada por empresas de todos os tamanhos há décadas. O [modo de encapsulamento IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é utilizada nesta opção como um mecanismo de criptografia.

Porque o tráfego de túnel VPN atravessa a Internet com uma VPN de site a site, a Microsoft oferece a opção de ligação de outra, ainda mais seguro. O Azure ExpressRoute é uma WAN dedicada ligação entre o Azure e uma localização no local ou um fornecedor de alojamento do Exchange. Como as ligações ExpressRoute não entram na Internet, estas ligações oferecem mais fiabilidade, velocidades mais rápidas, latências mais baixas e maior segurança do que as ligações normais pela Internet. Além disso, como se trata de uma conexão direta do fornecedor de telecomunicação do cliente, os dados não percorre a Internet e, portanto, não são expostos ao mesmo.

Melhores práticas para implementar uma rede híbrida segura que expande uma rede no local para o Azure são [disponível](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Exclusão de Responsabilidade

 - Este documento é apenas para fins informativos. A MICROSOFT FAZ NÃO OFERECE QUAISQUER GARANTIAS, EXPRESSAS, IMPLÍCITAS OU ESTATUTÁRIAS QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "como-está." Informações e opiniões expressados neste documento, incluindo URLs e outras referências de Web site da Internet, podem ser alteradas sem aviso prévio. Os clientes a ler este documento assume o risco de usá-lo.
 - Este documento não fornece aos clientes quaisquer direitos de propriedade intelectual em qualquer produto da Microsoft ou soluções.
 - Os clientes podem copiar e utilizar este documento para efeitos de referência interna.
 - Algumas recomendações neste documento podem resultar em maior de dados, a rede ou a utilização de recursos de computação no Azure e podem aumentar os custos de licenciamento ou de subscrição do Azure num cliente.
 - Esta arquitetura deve ser usado como uma base para os clientes que se ajustar às suas necessidades específicas e não devem ser utilizada como-está num ambiente de produção.
 - Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos requisitos de conformidade e normas. Os clientes procuram legal suporte da sua organização em implementações do cliente aprovadas.
