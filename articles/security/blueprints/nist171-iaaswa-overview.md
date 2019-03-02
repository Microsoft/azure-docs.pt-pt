---
title: Segurança do Azure e o esquema de conformidade - aplicação Web de IaaS para SP do NIST 800-171
description: Segurança do Azure e o esquema de conformidade - IaaS Web-SP aplicação NIST 800-171
services: security
author: jomolesk
ms.assetid: 1f1ad27f-32c3-4e76-abb9-ea768d01747f
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: b30094e264086f018acbf84144300df46c60ac4e
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57241328"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-nist-sp-800-171"></a>Segurança do Azure e o esquema de conformidade - aplicação Web de IaaS para SP do NIST 800-171

## <a name="overview"></a>Descrição geral
[Publicação especial do NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) fornece diretrizes para proteger a informação de não controlada (CUI) que residem nos sistemas de informação nonfederal e organizações. SP do NIST 800-171 estabelece 14 famílias de requisitos de segurança para proteger a confidencialidade das CUI.

Este Azure no Security and Compliance Blueprint disponibiliza orientações para ajudar os clientes a implementar uma arquitetura de aplicativos web no Azure que implementa um subconjunto de NIST SP 800-171 controles. Esta solução demonstra maneiras em que os clientes podem satisfazer requisitos de conformidade e de segurança específicos. Ele também serve como uma base para os clientes criar e configurar as suas aplicações web no Azure.

Esta arquitetura de referência, o guia de implementação associados e o modelo de risco destinam-se a servir de base para os clientes para se adaptar às suas necessidades específicas. Não deve ser usados como-está num ambiente de produção. Implementar esta arquitetura sem modificação não é suficiente para atender completamente aos requisitos de SP do NIST 800-171. Os clientes são responsáveis pela realização de avaliações de conformidade de qualquer solução criados com esta arquitetura e segurança adequada. Requisitos podem variar com base nas especificidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama de arquitetura e componentes
Este Azure no Security and Compliance Blueprint implementa uma arquitetura de referência para um aplicativo da web de IaaS com um back-end do SQL Server. A arquitetura inclui uma camada web, camada de dados, infra-estrutura do Active Directory, Gateway de aplicação do Azure e o Balanceador de carga do Azure. Máquinas virtuais (VMs) implementadas para os escalões web e os dados são configuradas num conjunto de disponibilidade. Instâncias do SQL Server são configuradas num grupo de disponibilidade Always On para elevada disponibilidade. As VMs estão associados a um domínio. Políticas de grupo do Active Directory impõem configurações de segurança e conformidade ao nível do sistema operativo.

A solução inteira é construída de armazenamento do Azure, o que os clientes configurar no portal do Azure. Armazenamento encripta todos os dados com o Storage Service Encryption para manter a confidencialidade dos dados em repouso. Armazenamento georredundante garante que um evento adverso no Centro de dados principal do cliente não resulta numa perda de dados. Uma segunda cópia é armazenada numa localização separada centenas de quilómetros de distância.

Para maior segurança, todos os recursos nesta solução são geridos como um grupo de recursos através do Gestor de recursos do Azure. Controlo de acesso baseado em funções do Azure Active Directory (Azure AD) (RBAC) é utilizado para controlar o acesso a recursos implementados e chaves no Azure Key Vault. Estado de funcionamento do sistema é monitorizado através do Azure Monitor. Os clientes configurar ambos os serviços de monitorização para capturar os registos. Estado de funcionamento do sistema é exibido num único dashboard, é fácil de usar.

Anfitrião de bastião de gestão fornece uma ligação segura para os administradores de recursos de acesso implementado. *A Microsoft recomenda que configure uma ligação VPN ou Azure ExpressRoute para importação de dados e de gestão para a sub-rede de arquitetura de referência.*


![Aplicação Web de IaaS para o diagrama de arquitetura de referência de SP do NIST 800-171](images/nist171-iaaswa-architecture.png "aplicativo da Web de IaaS para o diagrama de arquitetura de referência de SP do NIST 800-171")

Esta solução utiliza os seguintes serviços do Azure. Para obter mais informações, consulte a [arquitetura de implantação](#deployment-architecture) secção.

- Máquinas Virtuais do Azure
    - (1) gestão/bastion (Windows Server 2016 Datacenter)
    - (2) controlador de domínio do Active Directory (Windows Server 2016 Datacenter)
    - (2) nó de cluster o SQL Server (SQL Server 2017 no Windows Server 2016)
    - (2) web/IIS (Centro de dados do Windows Server 2016)
- Rede Virtual do Azure
    - rede de /16 (1)
    - redes de /24 (5)
    - (5) grupos de segurança de rede
- Conjuntos de disponibilidade
    - (1) controladores de domínio o active Directory
    - (1) nós de cluster do SQL
    - (1) web/IIS
- Gateway de Aplicação do Azure
    - (1) firewall de aplicações web
        - Modo de firewall: prevenção
        - Conjunto de regras: OWASP 3.0
        - Porta do serviço de escuta: 443
- Azure Active Directory
- Azure Key Vault
- Azure Load Balancer
- O Azure Monitor (logs)
- Azure Resource Manager
- Centro de Segurança do Azure
- Storage do Azure
- Automatização do Azure
- Testemunho de nuvem
- Cofre dos Serviços de Recuperação

## <a name="deployment-architecture"></a>Arquitetura de implantação
A secção seguinte fornece detalhes sobre os elementos de implantação e a implementação.

**Anfitrião de bastião**: O anfitrião de bastião é o único ponto de entrada que os utilizadores podem utilizar para aceder os recursos implementados neste ambiente. O anfitrião de bastião fornece uma ligação segura a recursos implementados, permitindo apenas tráfego remoto de endereços IP públicos numa lista segura. Para permitir o tráfego de ambiente de trabalho remoto, a origem do tráfego deve ser definida no grupo de segurança de rede (NSG).

Esta solução cria uma VM como um anfitrião de bastião associados a um domínio com as seguintes configurações:
-   [Extensão de antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Extensão de diagnóstico do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [O Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) com o Key Vault.
-   Uma [política de encerramento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reduzir o consumo de recursos VM quando não está em utilização.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) está ativada para que as credenciais e outros segredos executar num ambiente protegido isolada do sistema operativo em execução.

### <a name="virtual-network"></a>Rede virtual
A arquitetura define uma rede privada virtual com um espaço de endereços de 10.200.0.0/16.

**Grupos de segurança de rede**: Esta solução implementa recursos numa arquitetura com sub-redes separadas para gerenciamento da web, base de dados, do Active Directory e numa rede virtual. Sub-redes logicamente são separados por regras NSG aplicadas às sub-redes individuais. As regras de restringem o tráfego entre sub-redes para que apenas necessário para a funcionalidade de gerenciamento do sistema e.

Consulte a configuração para [NSGs](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) implementado com esta solução. As organizações podem configurar NSGs ao editar o ficheiro anterior usando [esta documentação](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) como guia.

Cada um das sub-redes tem um NSG dedicado:
- Um NSG para o Gateway de aplicação (LBNSG)
- Um NSG para o anfitrião de bastião (MGTNSG)
- Um NSG para controladores de domínio primário e de cópia de segurança (ADNSG)
- Um NSG para servidores SQL e o testemunho de nuvem (SQLNSG)
- Um NSG para a camada web (WEBNSG)

### <a name="data-in-transit"></a>Dados em trânsito
Azure encripta todas as comunicações de e para os centros de dados do Azure por predefinição. Além disso, todas as transações para o armazenamento através do portal do Azure ocorrem através de HTTPS.

### <a name="data-at-rest"></a>Dados inativos
A arquitetura protege dados em repouso através de várias medidas. Estas medidas incluem a criptografia e auditoria de base de dados.

**O armazenamento do Azure**: Para cumprir os requisitos para os dados encriptados em inatividade, todos os [armazenamento](https://azure.microsoft.com/services/storage/) utiliza [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Esta funcionalidade ajuda a proteger e salvaguardar os dados para oferecer suporte a requisitos de conformidade definidos pelo SP NIST 800-171 e de compromissos de segurança organizacional.

**Azure Disk Encryption**: Encriptação de disco é utilizado para discos de VM de IaaS do Windows criptografados. [Encriptação de disco](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utiliza a funcionalidade BitLocker do Windows para fornecer a criptografia de volume de sistema operativo e discos de dados. A solução está integrada com o Key Vault para ajudar a controlar e gerir as chaves de encriptação de disco.

**SQL Server**: Instância do SQL Server usa as seguintes medidas de segurança da base de dados:
-   [Auditoria de SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) regista os eventos de base de dados e escreve-as para registos de auditoria.
-   [Encriptação de dados transparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) executa em tempo real criptografia e descriptografia da base de dados, cópias de segurança associadas e ficheiros de registo de transação para proteger as informações em repouso. Encriptação de dados transparente fornece garantia de que são armazenados os dados não tem sido sujeita a acesso não autorizado.
-   [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedir o acesso de todos os servidores de base de dados até que sejam concedidas as permissões apropriadas. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
-   [Encriptados colunas](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) Certifique-se de que os dados confidenciais nunca é apresentada como texto simples dentro do sistema de base de dados. Depois de é ativada a encriptação de dados, apenas as aplicações de cliente ou servidores de aplicações com acesso às chaves podem aceder a dados de texto sem formatação.
- [Máscara de dados dinâmicos](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) limita a exposição de dados confidenciais ao mascará os dados de utilizadores não privilegiados ou aplicações. É, automaticamente, pode detetar dados potencialmente confidenciais e sugerir as máscaras de apropriado a ser aplicado. Máscara de dados dinâmica ajuda a reduzir o acesso, de modo a que dados confidenciais não sair da base de dados por meio do acesso não autorizado. *Os clientes são responsáveis por ajustar as definições de cumprir para seu esquema de base de dados.*

### <a name="identity-management"></a>Gestão de identidades
As seguintes tecnologias fornecem capacidades para gerir o acesso aos dados no ambiente do Azure:
-   [O Azure AD](https://azure.microsoft.com/services/active-directory/) é multi-inquilino com base na cloud diretório e identidade do serviço de gestão da Microsoft. Todos os utilizadores para esta solução são criados no Azure AD e incluem os utilizadores que acessar a instância do SQL Server.
-   Autenticação para a aplicação é executada ao utilizar o Azure AD. Para obter mais informações, consulte como [integrar aplicações com o Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
-   [RBAC do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) podem ser utilizadas pelos administradores para definir permissões de acesso detalhado para conceder apenas a quantidade de acesso que os utilizadores precisam realizar seus trabalhos. Em vez de dar todas as permissões de utilizador sem restrições para recursos do Azure, os administradores podem permitir apenas certas ações para aceder aos dados. Acesso à subscrição está limitado ao administrador da subscrição.
- [O Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) pode ser utilizado pelos clientes para minimizar o número de utilizadores que têm acesso a determinados recursos. Os administradores podem utilizar o Azure AD Privileged Identity Management para detetar, restringir e monitorizar identidades privilegiadas e respetivo acesso a recursos. Esta funcionalidade também pode ser utilizada para impor acesso administrativo a pedido e just-in-time quando necessário.
- [O Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) Deteta potenciais vulnerabilidades que afetam as identidades de uma organização. Configura as respostas automáticas para detetado ações suspeitas relacionadas com identidades de uma organização. Também é mostrada a incidentes suspeitos para tomar as medidas adequadas para resolvê-los.

### <a name="security"></a>Segurança
**Gestão de segredos**: A solução utiliza [Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. Key Vault ajuda a salvaguardar as chaves criptográficas e segredos utilizados pelas aplicações e serviços cloud. As seguintes capacidades de Key Vault ajudam os clientes a proteger os dados:
- Políticas de acesso avançadas são configuradas de forma necessidade.
- Políticas de acesso do Cofre de chaves são definidas com o mínimo permissões necessárias para chaves e segredos.
- Todas as chaves e segredos no Key Vault tem datas de expiração.
- Todas as chaves no Cofre de chaves estão protegidas por módulos de segurança de hardware especializado. O tipo de chave é uma chave RSA de 2048 bits segurança--protegidas por módulo de hardware.
- Todos os utilizadores e identidades são concedidas as permissões necessárias mínimas utilizando o RBAC.
- Registos de diagnóstico para o Cofre de chaves estão ativados com um período de retenção de, pelo menos, 365 dias.
- Operações de criptografia permitidas para as chaves estão limitadas aos obrigatórios.
- A solução está integrada com o Key Vault para gerir as chaves de encriptação de discos de VM de IaaS e segredos.

**Gerenciamento de patches**: VMs do Windows implementadas como parte desta arquitetura de referência estão configuradas por predefinição para receber atualizações automáticas do serviço de atualização do Windows. Esta solução também inclui a [automatização do Azure](https://docs.microsoft.com/azure/automation/automation-intro) serviço por meio do qual as implementações atualizadas podem ser criadas para o patch VMs quando necessário.

**Proteção contra Malware**: [O Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para VMs fornece a capacidade de proteção em tempo real que ajuda a identifica e remove vírus, spyware e outro software malicioso. Os clientes podem configurar alertas que geram quando conhecido software malicioso ou indesejável se tentar instalar ou executar em VMs protegidas.

**Centro de segurança do Azure**: Com o [Centro de segurança](https://docs.microsoft.com/azure/security-center/security-center-intro), os clientes podem centralmente aplicam-se e gerir políticas de segurança em cargas de trabalho, limitar a exposição a ameaças e detetar e responder a ataques. Centro de segurança também acessa as configurações existentes dos serviços do Azure para fornecer a configuração e recomendações de serviço para o ajudar a melhorar a postura de segurança e proteger os dados.

Centro de segurança utiliza uma variedade de capacidades de deteção para alertar os clientes de potenciais ataques que visam seus ambientes. Estes alertas contêm informações valiosas sobre o que acionou o alerta, os recursos afetados e a origem do ataque. Centro de segurança tem um conjunto de [predefinidos alertas de segurança](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) que são acionados quando uma ameaça ou atividade suspeita tem lugar. Os clientes podem usar [regras de alerta personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) para definir novos alertas de segurança com base nos dados que já são recolhidos do seu ambiente.

Centro de segurança fornece alertas de segurança priorizados e incidentes. Centro de segurança torna mais simples para os clientes detetar e resolver potenciais problemas de segurança. R [relatório de inteligência de ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) é gerado para cada detetadas ameaças. As equipes de resposta a incidentes podem utilizar os relatórios quando investigar e corrigir as ameaças.

Esta arquitetura de referência utiliza o [avaliação de vulnerabilidade](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) capacidade no Centro de segurança. Depois de estar configurada, um agente de parceiro (por exemplo, Qualys) relatórios de dados de vulnerabilidade à plataforma de gestão do parceiro. Por sua vez, a plataforma de gestão do parceiro fornece dados de vulnerabilidades e de estado de funcionamento de volta ao Centro de Segurança. Os clientes podem utilizar estas informações para identificar rapidamente VMs vulneráveis.

**Gateway de aplicação do Azure**: A arquitetura reduz o risco de vulnerabilidades de segurança através da utilização de um gateway de aplicação com uma firewall de aplicações web configuradas e o conjunto de regras OWASP ativada. Capacidades adicionais incluem:

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Ativar [descarga de SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal).
- Desativar [TLS versões 1.0 e 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Firewall de aplicações Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modo de prevenção).
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com OWASP 3.0 conjunto de regra.
- Ativar [registo de diagnósticos](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Sondas de estado de funcionamento personalizado](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal).
- [Centro de segurança](https://azure.microsoft.com/services/security-center) e [Assistente do Azure](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fornecer proteção adicional e notificações. Centro de segurança também fornece um sistema de reputação.

### <a name="business-continuity"></a>Continuidade do negócio

**Elevada disponibilidade**: A solução implementa todas as VMs num [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Conjuntos de disponibilidade garantem que as VMs são distribuídas por vários clusters de hardware isolados para melhorar a disponibilidade. Pelo menos uma VM está disponível durante um evento de manutenção não planeada ou não planeada, que cumpre 99,95% do SLA do Azure.

**Cofre dos Recovery Services**: O [cofre dos Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) hospeda os dados de cópia de segurança e protege todas as configurações de máquinas virtuais do Azure nesta arquitetura. Com o Cofre de serviços de recuperação, os clientes podem restaurar ficheiros e pastas a partir de uma VM de IaaS sem restaurar toda a VM. Este processo acelera os tempos de restauro.

**Testemunho de nuvem**: [Testemunho de nuvem](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) é um tipo de testemunho de quórum do cluster de ativação pós-falha no Windows Server 2016 que utiliza o Azure como o ponto de arbitragem. Testemunho de nuvem, como outro testemunho de quórum, obtém um voto e pode participar nos cálculos do quórum. Utiliza o armazenamento de Blobs do Azure standard publicamente disponível. Essa organização elimina a sobrecarga de manutenção extra de VMs alojadas numa nuvem pública.

### <a name="logging-and-auditing"></a>Registro e auditoria

Serviços do Azure extensivamente de registo do sistema e a atividade do utilizador, bem como o estado de funcionamento do sistema:
- **Registos de atividades**: [Registos de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações executadas em recursos numa subscrição. Registos de atividades podem ajudar a determinar o iniciador de uma operação, hora da ocorrência e o estado.
- **Os registos de diagnóstico**: [Os registos de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os registos emitidos por cada recurso. Estes registos incluem registos de sistema de eventos do Windows, registos de armazenamento, registos de auditoria do Cofre de chaves e os registos de acesso e de firewall do Gateway de aplicação. Todos os registos de diagnóstico escrevem para uma conta de armazenamento do Azure centralizado e criptografado para arquivamento. Os utilizadores podem configurar o período de retenção, até e 730 dias, para satisfazer os seus requisitos específicos.

**Registos de Monitor do Azure**: Estes registos são consolidados no [registos do Azure Monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e relatórios do dashboard. Depois de recolhidos, os dados são organizado em tabelas separadas para cada tipo de dados nas áreas de trabalho do Log Analytics. Dessa forma, todos os dados podem ser analisados em conjunto, independentemente de sua fonte original. Centro de segurança integra-se com os registos do Azure Monitor. Os clientes podem utilizar consultas de Kusto para aceder aos respetivos dados de eventos de segurança e combiná-los com dados de outros serviços.

O Azure seguinte [soluções de monitorização](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) são incluídos como parte desta arquitetura:
-   [Avaliação do Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de verificação de estado de funcionamento do Active Directory avalia o risco e estado de funcionamento dos ambientes de servidor num intervalo regular. Ele fornece uma lista prioritária de recomendações específicas para a infraestrutura de servidor implementado.
- [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de verificação de estado de funcionamento do SQL avalia o risco e estado de funcionamento dos ambientes de servidor num intervalo regular. Fornece aos clientes com uma lista prioritária de recomendações específicas para a infraestrutura de servidor implementado.
- [Estado de funcionamento do agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução de estado de funcionamento do agente relata o número de agentes é implementado e a respetiva distribuição geográfica. Também relata o número de agentes é sem resposta e o número de agentes que submeter dados operacionais.
-   [Log Analytics da atividade](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução Log Analytics da atividade auxilia com uma análise de registos de atividade do Azure em todas as subscrições do Azure para um cliente.

**A automatização do Azure**: [Automatização](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gere runbooks. Nesta solução, runbooks ajudam a recolher registos do SQL Server. Os clientes podem usar a automação [controlo de alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking) solução para o identificar facilmente as alterações no ambiente.

**Azure Monitor**: [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a acompanhar o desempenho, manter a segurança e identificar tendências. As organizações podem usá-lo de auditoria, criar alertas e arquivar dados. Também pode controlar as chamadas de API em seus recursos do Azure.

## <a name="threat-model"></a>Modelo de risco

O diagrama de fluxo de dados para esta arquitetura de referência está disponível para [transferir](https://aka.ms/nist171-iaaswa-tm) ou podem ser encontradas aqui. Esse modelo pode ajudar os clientes a compreender os pontos de risco potencial na infra-estrutura do sistema quando eles fazer modificações.

![Aplicação Web de IaaS para o modelo de risco de SP do NIST 800-171](images/nist171-iaaswa-threat-model.png "aplicativo da Web de IaaS para o modelo de risco de SP do NIST 800-171")

## <a name="compliance-documentation"></a>Documentação de conformidade

O [Azure no Security and Compliance Blueprint - matriz de responsabilidade do cliente de SP do NIST 800-171](https://aka.ms/nist171-crm) apresenta uma lista de todos os controlos de segurança exigidos pelo SP do NIST 800-171. Essa matriz se a implementação de cada controle é da responsabilidade da Microsoft, o cliente, os detalhes ou partilhados entre os dois.

O [Azure no Security and Compliance Blueprint - matriz de implementação de controlo Web aplicação SP do NIST 800-171 IaaS](https://aka.ms/nist171-iaaswa-cim) fornece informações sobre quais SP NIST 800-171 controles são resolvidas à arquitetura de aplicativo da web de IaaS. Ele inclui descrições detalhadas de como a implementação cumpre os requisitos de cada controle coberta.

## <a name="guidance-and-recommendations"></a>Orientações e recomendações
### <a name="vpn-and-expressroute"></a>ExpressRoute e VPN
Um túnel VPN seguro ou [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) tem de ser configurado com segurança estabelecer uma ligação para os recursos implementados como parte desta arquitetura de referência de aplicação de web de IaaS. Configurando adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para dados em trânsito.

Ao implementar um túnel VPN seguro com o Azure, é possível criar uma ligação privada virtual entre uma rede no local e uma rede Virtual do Azure. Esta ligação ocorre através da Internet. Os clientes podem utilizar esta ligação para informações de "encapsulamento" em segurança dentro de uma ligação encriptada entre a rede do cliente e o Azure. VPN site a site é uma tecnologia madura e segura que tenha sido implementada por empresas de todos os tamanhos há décadas. O [modo de encapsulamento IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é utilizada nesta opção como um mecanismo de criptografia.

Uma vez que o tráfego no túnel VPN atravessa a Internet com uma VPN de site a site, a Microsoft oferece outra opção de ligação ainda mais segura. O ExpressRoute é uma WAN dedicada ligação entre o Azure e uma localização no local ou um fornecedor de alojamento do Exchange. As ligações ExpressRoute ligar diretamente ao fornecedor de telecomunicação do cliente. Como resultado, os dados não viajam através da Internet e não estão expostos à mesma. Estas ligações oferecem mais fiabilidade, velocidades mais rápidas, latências mais baixas e maior segurança do que as ligações normais. 

Melhores práticas para implementar uma rede híbrida segura que expande uma rede no local para o Azure são [disponível](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Exclusão de Responsabilidade

- Este documento é apenas para fins informativos. A MICROSOFT FAZ NÃO OFERECE QUAISQUER GARANTIAS, EXPRESSAS, IMPLÍCITAS OU ESTATUTÁRIAS QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "como-está." Informações e opiniões expressados neste documento, incluindo URLs e outras referências de Web site da Internet, podem ser alteradas sem aviso prévio. Os clientes a ler este documento assume o risco de usá-lo. 
- Este documento não fornece aos clientes quaisquer direitos de propriedade intelectual em qualquer produto da Microsoft ou soluções. 
- Os clientes podem copiar e utilizar este documento para efeitos de referência interna. 
- Algumas recomendações neste documento podem resultar em maior de dados, a rede ou a utilização de recursos de computação no Azure e podem aumentar os custos de licenciamento ou de subscrição do Azure num cliente. 
- Esta arquitetura deve ser usado como uma base para os clientes que se ajustar às suas necessidades específicas e não devem ser utilizada como-está num ambiente de produção.
- Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos requisitos de conformidade e normas. Os clientes procuram legal suporte da sua organização em implementações do cliente aprovadas.
