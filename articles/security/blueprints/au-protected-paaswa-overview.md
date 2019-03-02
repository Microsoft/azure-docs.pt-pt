---
title: Segurança do Azure e o esquema de conformidade - aplicação Web de PaaS para a Austrália protegida
description: Segurança do Azure e o esquema de conformidade - aplicação Web de PaaS para a Austrália protegida
services: security
author: meladie
ms.assetid: 708aa129-b226-4e02-85c6-1f86e54564e4
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: 3eff78aa6b13c48868b95bae03a8406a550a42c9
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243878"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-australia-protected"></a>Segurança do Azure e o esquema de conformidade - aplicação Web de PaaS para a Austrália protegida

## <a name="overview"></a>Descrição geral

Este Azure no Security and Compliance Blueprint fornece orientações para a implementação de uma plataforma como um ambiente de serviço (PaaS) adequado para a coleção, armazenamento e obtenção de dados protegidos de AU government que está em conformidade com os objetivos das Australian Government informações de segurança Manual ISM () produzidos pela australiana sinais australianos (ASD). Este esquema apresenta uma arquitetura de referência comuns e ajuda a demonstrar o processamento adequado dos dados de Governo confidenciais num ambiente seguro, em conformidade, com várias camado.

Esta arquitetura de referência, o guia de implementação e o modelo de ameaças fornecem uma Fundação para que possam executar seus próprios processos de acreditação planejamento e de sistema, ajudando os clientes a implementar cargas de trabalho para o Azure de forma compatível com ASD. Os clientes podem optar por implementar um Gateway de VPN do Azure ou o ExpressRoute para utilizar serviços federados e integrar os recursos no local com recursos do Azure. Os clientes devem considerar as implicações de segurança do uso de recursos no local. Configuração adicional é necessária para cumprir os requisitos, como eles podem variar com base nas especificidades da implementação de cada cliente.

Alcançando a conformidade de ASD requer que um avaliador de informações de segurança registado Audita o sistema. Os clientes são responsáveis pela realização de segurança adequados e avaliações de conformidade de qualquer solução criada com esta arquitetura, como os requisitos podem variar com base nas especificidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama de arquitetura e componentes
Esta solução fornece uma arquitetura de referência para um aplicativo da web de PaaS com um back-end de base de dados do Azure SQL. O aplicativo web é hospedado num Azure App Service ambiente isolado, que é um ambiente privado e dedicado num Datacenter do Azure. A carga de ambiente equilibra o tráfego para a aplicação web nas máquinas virtuais geridas pelo Azure. Todas as ligações de aplicações web necessitam de TLS com uma versão mínima do 1.2. Esta arquitetura também inclui os grupos de segurança de rede, um Gateway de aplicação, o DNS do Azure e o Balanceador de carga.

A arquitetura pode fornecer um ambiente de híbrida segura que expande uma rede no local para o Azure, permitindo que cargas de trabalho baseadas na web ser acedida em segurança por utilizadores da empresa de rede de área local privada de uma organização ou da internet. Para soluções no local, o cliente é responsável e responsável por todos os aspectos de segurança, as operações e conformidade.

Os recursos do Azure incluídos nesta solução, podem ligar-se para um local rede ou Datacenter instalações de colocação (por exemplo, CDC em Canberra) por meio de uma VPN IPSec com um Gateway de VPN e ExpressRoute. A decisão de utilise uma VPN deve ser feita com a classificação dos dados transmitidos e o caminho de rede em mente. Os clientes em execução em grande escala, as cargas de trabalho críticas de missão requisitos de grandes volumes de dados devem considerar uma arquitetura de rede híbrida com o ExpressRoute para conectividade de rede privada aos serviços do Azure. Consulte a [orientações e recomendações](#guidance-and-recommendations) secção para obter mais detalhes sobre os mecanismos de ligação para o Azure.

Federação com o Azure Active Directory deve ser usada para permitir que os utilizadores para se autenticar com as credenciais no local e aceder a todos os recursos na cloud ao utilizar uma infraestrutura de serviços de Federação do Active Directory no local. Serviços de Federação do Active Directory pode fornecer identidade simplificada segura web e de Federação únicos início de sessão em recursos para este ambiente híbrido. Consulte a [orientações e recomendações](#guidance-and-recommendations) secção para ainda mais a configuração do Azure Active Directory para obter detalhes.

A solução utiliza contas de armazenamento do Azure, o que os clientes podem configurar para utilizar a encriptação do serviço de armazenamento para manter a confidencialidade dos dados em repouso. Azure armazena três cópias dos dados na região selecionada de um cliente para resiliência. Regiões do Azure são implementadas em pares de região resiliente, e o armazenamento com redundância geográfico garante que os dados serão replicados para a segunda região com três cópias também. Isto impede que um evento adverso na localização de dados principal do cliente resulta numa perda de dados.

Para maior segurança, todos os recursos do Azure nesta solução são geridos como um grupo de recursos através do Gestor de recursos do Azure. Controlo de acesso baseado em funções do Azure Active Directory é utilizado para controlar o acesso para implementar recursos e as chaves no Azure Key Vault. Estado de funcionamento do sistema é monitorizado através do Centro de segurança do Azure e o Azure Monitor. Os clientes configurar ambos os serviços de monitorização para capturar os registos e exibir o estado de funcionamento do sistema num dashboard único, navegável facilmente. O Gateway de aplicação do Azure está configurado como uma firewall no modo de prevenção e não permite o tráfego que não é a versão do TLS 1.2 ou superior. A solução utiliza o ambiente de serviço de aplicações do Azure v2 para isolar a camada da web num ambiente de não multi-inquilino.

![Aplicação Web de PaaS para arquitetura de referência de AU protegida](images/au-protected-paaswa-architecture.png?raw=true "aplicação Web de PaaS para de diagrama da arquitetura de referência protegidos de AU")

Esta solução utiliza os seguintes serviços do Azure. Existem mais detalhes estão no [arquitetura de implantação](#deployment-architecture) secção.

- Gateway de Aplicação
    - Firewall de aplicação Web
        - Modo de firewall: prevenção
        - Conjunto de regras: OWASP
        - Porta do serviço de escuta: 443
- Application Insights
- Azure Active Directory
- Ambiente de serviço de aplicações do Azure v2
- Automatização do Azure
- DNS do Azure
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Centro de Segurança do Azure
- Base de Dados SQL do Azure
- Storage do Azure
- Registos de Monitor do Azure
- Rede Virtual do Azure
    - (1) /16 de rede
    - (4) redes /24
    - Grupos de segurança de rede
- Grupos de segurança de rede
- Cofre dos serviços de recuperação
- Aplicação Web do Azure

Este esquema contém os serviços do Azure que não tenham sido certificados para utilização com a classificação de protegido por Centre australianas de segurança informáticas (ACSC). A Microsoft recomenda que os clientes rever a segurança publicada e relatórios de auditoria relacionados com estes serviços do Azure e utilizam a sua estrutura de gerenciamento de risco para determinar se o serviço do Azure é adequado para suas acreditação interna e a utilização de cada a Classificação de protegido.

## <a name="deployment-architecture"></a>Arquitetura de implantação
A secção seguinte fornece detalhes sobre os elementos de implantação e a implementação.

**Azure Resource Manager**: [O Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) permite que os clientes trabalhar com os recursos da solução como um grupo. Os clientes podem implementar, atualizar ou eliminar todos os recursos para a solução numa operação única e coordenada. Os clientes a utilizar um modelo para a implementação e esse modelo pode funcionar para ambientes diferentes, tais como teste, transição e produção. O Resource Manager proporciona segurança, auditoria e etiquetagem recursos para ajudar os clientes a gerir os seus recursos após a implementação.

**Anfitrião de bastião**: O anfitrião de bastião é o único ponto de entrada que permite aos utilizadores aceder os recursos implementados neste ambiente. O anfitrião de bastião fornece uma ligação segura a recursos implementados, permitindo apenas tráfego remoto a partir de endereços IP públicos numa lista segura. Para permitir o tráfego de (protocolo RDP) de área de trabalho remoto, a origem do tráfego deve ser definido no grupo de segurança de rede.

Esta solução cria uma máquina virtual como um anfitrião de bastião associados a um domínio com as seguintes configurações:
-   [Extensão de antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Extensão de diagnóstico do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [O Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) com o Azure Key Vault
-   Uma [política de encerramento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reduzir o consumo de recursos de máquina virtual quando não está em utilização

**Ambiente de serviço de aplicações v2**: O [ambiente de serviço de aplicações do Azure](https://docs.microsoft.com/azure/app-service/environment/intro) é uma funcionalidade do serviço de aplicações que fornece um ambiente totalmente isolado e dedicado para uma execução segura de aplicativos de serviço de aplicações a uma escala elevada.

Ambientes de serviço de aplicações são isolados para apenas executar aplicativos de um único cliente e são sempre implementados numa rede virtual. Os clientes têm um controle refinado sobre o tráfego de rede aplicação de entrada e saída e as aplicações podem estabelecer conexões seguras de alta velocidade nas redes virtuais para recursos da empresa no local.

Permitir a utilização de ambientes de serviço de aplicações para esta arquitetura para as seguintes configurações/controles:

- Ambientes de serviço de aplicações tem de ser configurados para utilizar o plano de serviço isolado
    - Configurar diferentes ambientes do App Service para aplicações em classificações diferentes
- Alojar dentro de uma rede virtual do Azure protegida e regras de segurança de rede
- Ambientes de serviço de aplicações configurados com um certificado de Balanceador de carga interno com assinatura automática para comunicação HTTPS. Como melhor prática, a Microsoft recomenda a utilização de uma autoridade de certificação fidedigna para uma maior segurança.
- [Modo de balanceamento de carga interno](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (modo 3)
- Desativar [TLS versões 1.0 e 1.1](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Alteração [cifras TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Controle [portas de N/W de tráfego de entrada](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Firewall de aplicações Web – restringir dados](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Permitir [tráfego de base de dados do Azure SQL](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

**Aplicação Web do Azure**: [Serviço de aplicações do Azure](https://docs.microsoft.com/azure/app-service/) permite aos clientes criar e alojar aplicações web na linguagem de programação de sua preferência sem ter de gerir infraestrutura. Este serviço garante dimensionamento automático e elevada disponibilidade, suporta Windows e Linux e permite implementações automáticas a partir do GitHub, Serviços de DevOps do Azure ou de qualquer repositório de Git.

### <a name="virtual-network"></a>Rede Virtual
A arquitetura define uma rede privada virtual com um espaço de endereços de 10.200.0.0/16.

**Grupos de segurança de rede**: [Grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contêm listas de controlo de acesso que permitem ou negam o tráfego dentro de uma rede virtual. Grupos de segurança de rede podem ser utilizados para proteger o tráfego numa sub-rede ou o nível de máquina virtual individual. Existem os seguintes grupos de segurança de rede:
- grupo de segurança de rede de 1 para o Gateway de aplicação
- grupo de segurança de rede de 1 para o ambiente de serviço de aplicações
- grupo de segurança de rede de 1 para a base de dados do Azure SQL
- grupo de segurança de rede de 1 para o anfitrião de bastião

Cada um dos grupos de segurança de rede têm portas específicas e protocolos abrir para que a solução possam funcionar corretamente e de forma segura. Além disso, as seguintes configurações são habilitadas para cada grupo de segurança de rede:

  - [Eventos e registos de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) forem ativadas e armazenados numa conta de armazenamento
  - Registos de Monitor do Azure está ligada a [diagnósticos do grupo de segurança de rede](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Sub-redes**: Cada sub-rede está associado a seu grupo de segurança de rede correspondente.

**O DNS do Azure**: O sistema de nomes de domínio ou DNS, é responsável pela tradução (ou resolver) um nome de Web site ou serviço para o endereço IP. [O DNS do Azure](https://docs.microsoft.com/azure/dns/dns-overview) é um serviço de alojamento dos domínios DNS que fornece resolução de nome, através da infraestrutura do Azure. Ao alojar os domínios no Azure, os utilizadores podem gerir registos DNS com as mesmas credenciais, APIs, ferramentas e faturação dos outros serviços do Azure. O DNS do Azure também suporta domínios DNS privados.

**Balanceador de carga do Azure**: [O Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) permite aos clientes Dimensionar seus aplicativos e criar disponibilidade elevada para serviços. Balanceador de carga dá suporte a cenários de entrada e de saída e fornece um débito elevado, de baixa latência e aumenta até milhões de fluxos para todas as aplicações TCP e UDP.

### <a name="data-in-transit"></a>Dados em trânsito
Azure encripta todas as comunicações para e a partir do Azure como, por predefinição. 

Para dados protegidos em trânsito de redes de clientes, a arquitetura utiliza o Azure da Internet ou o ExpressRoute com um Gateway de VPN configurado com o IPSEC.

Além disso, todas as transações para o Azure através do portal de gestão do Azure ocorrem através de HTTPS utilizando TLS versão 1.2.

### <a name="data-at-rest"></a>Dados inativos
A arquitetura protege os dados em descanso através da encriptação, a auditoria de base de dados e outras medidas.

**O armazenamento do Azure**: Para atender aos dados criptografados em requisitos de rest, todos os [armazenamento do Azure](https://azure.microsoft.com/services/storage/) utiliza [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Isto ajuda a proteger e salvaguardar os dados para oferecer suporte a compromissos de segurança organizacional e requisitos de conformidade definidos pela Australian Government ISM.

**Azure Disk Encryption**: [O Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aproveita a funcionalidade BitLocker do Windows para fornecer encriptação de volume para discos de dados. A solução se integra com o Azure Key Vault para ajudar a controlar e gerir as chaves de encriptação de disco.

**Base de dados SQL do Azure**: A instância de base de dados do Azure SQL usa as seguintes medidas de segurança da base de dados:
-   [Autenticação do Active Directory e a autorização](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) permite a gestão de identidades de utilizadores de base de dados e outros serviços da Microsoft num local central.
-   [A auditoria de base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) faixas de base de dados eventos e escreve-os para uma auditoria registo numa conta de armazenamento do Azure.
-   Base de dados SQL do Azure está configurado para utilizar [encriptação de dados transparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que realiza a encriptação em tempo real e a desencriptação da base de dados, cópias de segurança associadas e ficheiros de registo de transação para proteger as informações em rest. Encriptação de dados transparente fornece garantia de que são armazenados os dados não tiver sido sujeita a acesso não autorizado.
-   [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedir o acesso de todos os servidores de base de dados até que sejam concedidas as permissões apropriadas. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
-   [Deteção de ameaças SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite a deteção e resposta a ameaças potenciais à medida que ocorrem ao fornecer alertas de segurança para atividades suspeitas da base de dados, potenciais vulnerabilidades, ataques de injeção de SQL e acesso de base de dados anómalas padrões. Deteção de ameaças SQL integra alertas no [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/), que inclui detalhes de atividade suspeita e ação sobre como investigar e mitigar a ameaça recomendada.
-   [Sempre as colunas encriptadas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Certifique-se de que os dados confidenciais nunca é apresentada como texto não criptografado dentro do sistema de base de dados. Depois de ativar a encriptação de dados, apenas as aplicações de cliente ou servidores de aplicações com acesso às chaves podem aceder aos dados de texto sem formatação.
- [Máscara de dados dinâmicos da base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais ao mascará os dados para aplicações ou utilizadores sem privilégios. Máscara de dados dinâmica pode detetar dados potencialmente confidenciais e sugerir as máscaras de apropriado a ser aplicada automaticamente. Isto ajuda a reduzir o acesso, de modo a que dados confidenciais não for fechada a base de dados por meio do acesso não autorizado. Os clientes terão de ajustar as definições de cumprir para seu esquema de base de dados de máscara de dados dinâmicos.

### <a name="identity-management"></a>Gestão de identidades
Os clientes podem utilizar a no local Active Directory Federated Services para federar com [do Azure Active Directory](https://azure.microsoft.com/services/active-directory/), que é multi-inquilino com base na cloud diretório e identidade do serviço de gestão da Microsoft. [O Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) integra os diretórios no local com o Azure Active Directory. Todos os utilizadores nesta solução requerem contas do Azure Active Directory, incluindo os utilizadores que acedem a base de dados do SQL do Azure. Com o Federação início de sessão, os utilizadores podem iniciar sessão no Azure Active Directory e se autenticarem nos recursos do Azure com credenciais no local.

Além disso, as seguintes capacidades do Azure Active Directory ajudam a gerir o acesso a dados no ambiente do Azure:
- Autenticação para a aplicação é executada com o Azure Active Directory. Para obter mais informações, consulte [integrar aplicações com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Além disso, a encriptação de coluna de base de dados utiliza o Azure Active Directory para autenticar a aplicação à base de dados do Azure SQL. Para obter mais informações, consulte como [proteger dados confidenciais na base de dados do Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Controlo de acesso baseado em função do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite aos administradores definir permissões de acesso detalhado para conceder apenas a quantidade de acesso que os utilizadores precisam realizar seus trabalhos. Em vez de dar a cada permissão de utilizador sem restrições para recursos do Azure, os administradores podem permitir apenas certas ações para aceder aos dados. Acesso à subscrição está limitado ao administrador da subscrição.
- [O Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permite que os clientes minimizar o número de utilizadores que têm acesso a determinadas informações. Os administradores podem usar o Azure Active Directory Privileged Identity Management para detetar, restringir e monitorizar identidades privilegiadas e respetivo acesso a recursos. Esta funcionalidade também pode ser utilizada para impor acesso administrativo a pedido e just-in-time quando necessário.
- [O Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) Deteta potenciais vulnerabilidades que afetam as identidades de organizações, configura as respostas automáticas para detetado ações suspeitas relacionadas com identidades de uma organização e investiga incidentes suspeitos para tomar as medidas adequadas para resolvê-los.

**Multi-factor Authentication**: Para proteger as identidades, deve ser implementada a autenticação multifator. [O Azure multi-factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) é uma solução fácil de usar, escalável e fiável que fornece um segundo método de autenticação para proteger os usuários. O Azure multi-factor Authentication utiliza o poder da cloud e integra-se com o Active Directory no local e aplicações personalizadas. Esta proteção é expandida para cenários de missão crítica, de grande volume.

### <a name="security"></a>Segurança
**Gestão de segredos**: A solução utiliza [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços. As seguintes capacidades do Azure Key Vault ajudam os clientes a proteger os dados:
- Políticas de acesso avançadas são configuradas de forma necessidade.
- Políticas de acesso do Cofre de chaves são definidas com o mínimo permissões necessárias para chaves e segredos.
- Todas as chaves e segredos no Key Vault tem datas de expiração.
- Todas as chaves no Cofre de chaves estão protegidas por módulos de segurança de hardware especializado. O tipo de chave estiver protegida por um módulo de hardware de segurança chave RSA de 2048 bits.
- Todos os utilizadores e identidades são concedidas as permissões mínimas necessárias usando o controle de acesso baseado em funções.
- Registos de diagnóstico para o Cofre de chaves estão ativados com um período de retenção de, pelo menos, 365 dias.
- Operações de criptografia permitidas para as chaves estão limitadas aos obrigatórios.

**Centro de segurança do Azure**: Com o [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), os clientes podem centralmente aplicam-se e gerir políticas de segurança em cargas de trabalho, limitar a exposição a ameaças e detetar e responder a ataques. Além disso, o Centro de segurança do Azure acessa as configurações existentes dos serviços do Azure para fornecer a configuração e recomendações de serviço para o ajudar a melhorar a postura de segurança e proteger os dados.

Centro de segurança do Azure utiliza uma variedade de capacidades de deteção para alertar os clientes de possíveis ataques direcionados aos respetivos ambientes. Estes alertas contêm informações valiosas sobre o que acionou o alerta, os recursos afetados e a origem do ataque. Centro de segurança do Azure tem um conjunto de [predefinidos alertas de segurança](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), que são acionados quando uma ameaça ou atividade suspeita tem lugar. [Regras de alerta personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) no Centro de segurança do Azure permitem aos clientes definir novos alertas de segurança com base nos dados que já são recolhidos do seu ambiente.

Centro de segurança do Azure fornece alertas de segurança priorizados e incidentes, tornando mais simples para os clientes detetar e resolver potenciais problemas de segurança. R [relatório de inteligência de ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) é gerado para cada detetado contra ameaças para ajudar as equipes de resposta a incidentes a investigar e resolver ameaças.

**Gateway de aplicação**: A arquitetura reduz o risco de vulnerabilidades de segurança com um Gateway de aplicação com uma firewall de aplicações web e o conjunto de regras do OWASP ativada. Capacidades adicionais incluem:

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Ativar [descarga de SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Desativar [TLS versões 1.0 e 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall de aplicações Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modo de prevenção)
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com o conjunto de regras do OWASP 3.0
- Ativar [registo de diagnósticos](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Sondas de estado de funcionamento personalizados](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center) e [Assistente do Azure](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fornecer proteção adicional e notificações. Centro de segurança do Azure também fornece um sistema de reputação.

### <a name="logging-and-auditing"></a>Registro e auditoria

Serviços do Azure extensivamente de registo do sistema e a atividade do utilizador, bem como o estado de funcionamento do sistema:
- **Registos de atividades**: [Registos de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações executadas em recursos numa subscrição. Registos de atividades podem ajudar a determinar o iniciador de uma operação, hora da ocorrência e o estado.
- **Os registos de diagnóstico**: [Os registos de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os registos emitidos por cada recurso. Estes registos incluem registos de sistema de eventos do Windows, registos de armazenamento do Azure, registos de auditoria do Cofre de chaves e os registos de acesso e de firewall do Gateway de aplicação. Todos os registos de diagnóstico escrevem para uma conta de armazenamento do Azure centralizado e criptografado para arquivamento. O período de retenção é configurável pelo utilizador, cópia de segurança e 730 dias, para atender aos requisitos de retenção de específicas da organização.

**Registos de Monitor do Azure**: Estes registos são consolidados no [registos do Azure Monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e relatórios do dashboard. Depois de recolhidos, os dados são organizados em tabelas separadas para cada tipo de dados, que permite que todos os dados a ser analisados em conjunto, independentemente de sua fonte original. Além disso, o Centro de segurança do Azure integra-se com os registos do Azure Monitor permitindo que os clientes utilizar consultas de Kusto para aceder aos respetivos dados de eventos de segurança e combiná-los com dados de outros serviços.

O Azure seguinte [soluções de monitorização](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) são incluídos como parte desta arquitetura:
-   [Avaliação do Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de verificação de estado de funcionamento do Active Directory avalia o risco e estado de funcionamento dos ambientes de servidor num intervalo regular e fornece uma lista prioritária de recomendações específicas para a infraestrutura de servidor implementado.
- [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de verificação de estado de funcionamento do SQL avalia o risco e estado de funcionamento dos ambientes de servidor num intervalo regular e fornece aos clientes uma lista prioritária de recomendações específicas para a infraestrutura de servidor implementado.
- [Estado de funcionamento do agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução de estado de funcionamento do agente relata o número de agentes é implementado e a distribuição geográfica, como número de agentes que não respondem e o número de agentes que está a enviar dados operacionais.
-   [Log Analytics da atividade](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução Log Analytics da atividade auxilia com uma análise de registos de atividade do Azure em todas as subscrições do Azure para um cliente.

**A automatização do Azure**: [A automatização do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gere runbooks. Nesta solução, runbooks ajudam a recolher registos de base de dados do Azure SQL. A automação [controlo de alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking) solução permite aos clientes identificar facilmente as alterações no ambiente.

**Azure Monitor**: [O Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a acompanhar o desempenho, manter a segurança e identificar tendências, permitindo que as organizações de auditoria, criar alertas e arquivar dados, incluindo o controlo de chamadas à API em seus recursos do Azure.

Observador de rede do Azure: [O observador de rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) fornece ferramentas para monitorizar, diagnosticar, veja as métricas e ativar ou desativar os registos de recursos numa rede virtual do Azure.  Entidades de Comunidade devem implementar os registos de fluxo do observador de rede para NSGs e máquinas virtuais. Estes registos devem ser armazenados numa conta de armazenamento dedicado que apenas os registos de segurança são armazenados no e acesso à conta de armazenamento deve ser protegido por controlos de acesso com base em função.

## <a name="threat-model"></a>Modelo de risco

O diagrama de fluxo de dados para esta arquitetura de referência está disponível para [transferir](https://aka.ms/au-protected-paaswa-tm) ou podem ser encontradas abaixo. Esse modelo pode ajudar os clientes a compreender os pontos do potencial risco de na infraestrutura de sistema ao fazer modificações.

![Aplicação Web de PaaS para o modelo de risco AU-PROTECTED](images/au-protected-paaswa-threat-model.png?raw=true "aplicativo da Web de PaaS para o diagrama de modelo de ameaça protegidos de AU")

## <a name="compliance-documentation"></a>Documentação de conformidade
Esta documentação de conformidade é produzida pela Microsoft com base nas plataformas e serviços disponibilizados pela Microsoft. Devido à grande variedade de implementações dos clientes, esta documentação fornece uma abordagem generalizada para uma solução alojada apenas no ambiente do Azure. Os clientes podem identificar e utilizar alternativos produtos e serviços com base nos seus próprios ambientes e os resultados de negócios a funcionar. Os clientes que optarem utilizar recursos locais devem atender a segurança e as operações para esses recursos no local. A solução documentada pode ser personalizada pelos clientes para resolver seus locais específicos e requisitos de segurança.

O [Azure no Security and Compliance Blueprint - matriz de responsabilidade do cliente AU-PROTECTED](https://aka.ms/au-protected-crm) apresenta uma lista de todos os controlos de segurança exigidos pelo Prot. de AU Essa matriz se a implementação de cada controle é da responsabilidade da Microsoft, o cliente, os detalhes ou partilhados entre os dois.

O [Azure no Security and Compliance Blueprint - matriz de implementação de aplicação de Web PaaS AU-PROTECTED](https://aka.ms/au-protected-paaswa-cim) fornece informações no qual os controles protegidos de AU são resolvidas à arquitetura do aplicativo web PaaS, incluindo descrições detalhadas de como a implementação cumpre os requisitos de cada controle coberta.

## <a name="guidance-and-recommendations"></a>Orientações e recomendações
### <a name="vpn-and-expressroute"></a>ExpressRoute e VPN

Para informações confidenciais um túnel IPSec VPN seguro tem de ser configurado com segurança estabelecer uma ligação para os recursos implementados como parte desta arquitetura de referência de aplicação de web de IaaS. Ao configurar uma VPN IPSec adequadamente, os clientes podem adicionar uma camada de proteção para dados em trânsito.

Ao implementar um túnel IPSec VPN seguro com o Azure, é possível criar uma ligação privada virtual entre uma rede no local e uma rede virtual do Azure. Esta ligação pode ser feitas através da Internet e permite que os clientes com segurança as informações de "encapsulamento" dentro de uma ligação encriptada entre a rede do cliente e o Azure. VPN site a site é uma tecnologia madura e segura que tenha sido implementada por empresas de todos os tamanhos há décadas. 

Uma vez que o tráfego no túnel VPN atravessa a Internet com uma VPN de site a site, a Microsoft oferece uma opção de ligação privada. O Azure ExpressRoute é uma ligação dedicada entre o Azure e uma localização no local ou um fornecedor de alojamento do Exchange e é considerado uma rede privada. Como as ligações ExpressRoute não entram na Internet, estas ligações oferecem mais fiabilidade, velocidades mais rápidas e latências mais baixas que as ligações normais pela Internet. Além disso, como se trata de uma conexão direta do fornecedor de telecomunicação do cliente, os dados não percorre a Internet e, portanto, não são expostos ao mesmo.

Melhores práticas para implementar uma rede híbrida segura que expande uma rede no local para o Azure são [disponível](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid). 

Para ajudar a proteger dados confidenciais, independentemente de utilizar a Internet ou o Azure ExpressRoute, os clientes tem de configurar sua VPN IPSec ao aplicar as seguintes definições:

• O Iniciador do cliente VPN tem de ser configurado para uma duração de SA de não superior a 14400 segundos.
• O Iniciador do cliente VPN tem de desativar modo de agressivo IKEv1.
• O Iniciador do cliente VPN tem de configurar Perfect Forward Secrecy.
• O cliente VPN iniciador tem de configurar a utilização de HMAC-SHA256 ou superior.

Opções de configuração para dispositivos VPN e de IPSec / IKE parâmetros é [disponível](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) para revisão.

### <a name="azure-active-directory-setup"></a>Configuração do Active Directory do Azure
[O Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é essencial para a gestão da implementação e aprovisionamento do acesso ao pessoal de interação com o ambiente. Num Windows Server Active Directory podem ser integrada no Azure Active Directory no [quatro cliques](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express).

Além disso, [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) permite que os clientes configurar a Federação no local [serviços de Federação do Active Directory]( https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs) e Azure Active Directory. Com o Federação início de sessão, os clientes podem permitir aos utilizadores iniciar sessão em serviços baseados no Azure Active Directory com as palavras-passe de no local e sem ter de introduzir as palavras-passe novamente enquanto na rede empresarial. Ao utilizar a opção de federação com serviços de Federação do Active Directory, pode implementar uma nova instalação dos serviços de Federação do Active Directory ou pode especificar uma instalação existente num farm do Windows Server 2012 R2.

Para impedir que os dados confidenciais a sincronizar com o Azure Active Directory, os clientes podem restringir os atributos que vão ser replicados para o Azure Active Directory ao aplicar as seguintes definições no Azure Active Directory Connect:

- [Permitir a filtragem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-configure-filtering)
- [Desativar sincronização de hash de palavra-passe](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)
-   [Desativar a repetição de escrita de palavra-passe](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
-   [Desativar a repetição de escrita do dispositivo](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-device-writeback)
-   Deixe as predefinições para [impedir eliminações acidentais](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-prevent-accidental-deletes) e [a atualização automática](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-automatic-upgrade)


## <a name="disclaimer"></a>Exclusão de Responsabilidade

 - Este documento é apenas para fins informativos. A MICROSOFT FAZ NÃO OFERECE QUAISQUER GARANTIAS, EXPRESSAS, IMPLÍCITAS OU ESTATUTÁRIAS QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "como-está." Informações e opiniões expressados neste documento, incluindo URLs e outras referências de Web site da Internet, podem ser alteradas sem aviso prévio. Os clientes a ler este documento assume o risco de usá-lo.
 - Este documento não fornece aos clientes quaisquer direitos de propriedade intelectual em qualquer produto da Microsoft ou soluções.
 - Os clientes podem copiar e utilizar este documento para efeitos de referência interna.
 - Algumas recomendações neste documento podem resultar em maior de dados, a rede ou a utilização de recursos de computação no Azure e podem aumentar os custos de licenciamento ou de subscrição do Azure num cliente.
 - Esta arquitetura deve ser usado como uma base para os clientes que se ajustar às suas necessidades específicas e não devem ser utilizada como-está num ambiente de produção.
 - Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos requisitos de conformidade e normas. Os clientes procuram legal suporte da sua organização em implementações do cliente aprovadas.
