---
title: Segurança do Azure e o esquema de conformidade - aplicação de IaaS Web para FedRAMP
description: Segurança do Azure e o esquema de conformidade - aplicação de IaaS Web para FedRAMP
services: security
documentationcenter: na
author: jomolesk
manager: barbkess
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: jomolesk
ms.openlocfilehash: 1ba5b813843ce2f5d31f337ab4d3d94e521b0e0c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57864478"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-fedramp"></a>Azure Security and Compliance Blueprint: Aplicação Web de IaaS para FedRAMP

## <a name="overview"></a>Descrição geral

O [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov) é um programa de toda a administração pública dos EUA que fornece uma abordagem padronizada à avaliação de segurança, autorização e monitorização contínua de produtos em nuvem e serviços. Esta segurança do Azure e a automação de plano gráfico de conformidade fornece orientações para a implementação de uma infraestrutura em conformidade com FedRAMP como um ambiente de serviço (IaaS) adequado para um simple aplicativo de web de acesso à Internet. Esta solução automatiza a implantação e configuração de recursos do Azure para uma arquitetura de referência comuns, que demonstram formas em que os clientes podem satisfazer os requisitos específicos de segurança e conformidade e serve como uma base para os clientes podem criar e Configure as suas próprias soluções no Azure. A solução implementa um subconjunto de controles da linha de base FedRAMP elevado, com base em SP do NIST 800-53. Para obter mais informações sobre requisitos de FedRAMP e esta solução, consulte a [documentação de conformidade](#compliance-documentation).
> [!NOTE]
> Esta solução implementa para o Azure Government.

Esta segurança do Azure e a automação de plano gráfico de conformidade implementa automaticamente uma arquitetura de referência de aplicação web IaaS com controlos de segurança pré-configuradas para ajudar os clientes conseguirem a conformidade com FedRAMP requisitos. A solução consiste em modelos Azure Resource Manager e scripts do PowerShell que o guiam de implementação de recursos e a configuração.

Esta arquitetura deve ser usado como uma base para os clientes que se ajustar às suas necessidades específicas e não devem ser utilizada como-está num ambiente de produção. Implementar uma aplicação para este ambiente sem modificação não é suficiente para atender completamente aos requisitos da linha de base FedRAMP elevado. Tenha em atenção o seguinte:
- Esta arquitetura fornece uma linha de base para ajudar os clientes que utilizam o Azure de forma em conformidade com FedRAMP.
- Os clientes são responsáveis pela realização de segurança adequados e avaliação de conformidade de qualquer solução criada com esta arquitetura, como os requisitos podem variar com base nas especificidades da implementação de cada cliente.

Para obter uma descrição geral de como funciona esta solução, veja este [vídeo](https://aka.ms/fedrampblueprintvideo) explicar e demonstrar a implementação.

Clique em [aqui](https://aka.ms/fedrampblueprintrepo) para obter instruções de implementação.

## <a name="architecture-diagram-and-components"></a>Diagrama de arquitetura e componentes
Esta solução implementa uma arquitetura de referência para um aplicativo da web de IaaS com um back-end do SQL Server. A arquitetura inclui uma camada web, camada de dados, infra-estrutura do Active Directory, Gateway de aplicação e Balanceador de carga. Máquinas virtuais implementadas para os escalões web e os dados são configuradas num conjunto de disponibilidade e instâncias do SQL Server são configuradas num grupo de Disponibilidade AlwaysOn para elevada disponibilidade. As máquinas virtuais estão associados a um domínio e políticas de grupo do Active Directory são utilizadas para impor configurações de segurança e conformidade ao nível do sistema operativo. Anfitrião de bastião fornece uma ligação segura para os administradores de recursos de acesso implementado. **Azure recomenda configurar uma ligação VPN ou Azure ExpressRoute para importação de dados e de gestão para a sub-rede de arquitetura de referência.**

![Aplicação Web de IaaS para o diagrama de arquitetura de referência do FedRAMP](images/fedramp-iaaswa-architecture.png?raw=true "IaaS aplicativo da Web para FedRAMP diagrama da arquitetura de referência")

Esta solução utiliza os seguintes serviços do Azure. Detalhes da arquitetura de implementação estão localizadas no [arquitetura de implantação](#deployment-architecture) secção.

- Máquinas Virtuais do Azure
    - (1) anfitrião de bastião (Windows Server 2016 Datacenter)
    - (2) controlador de domínio do Active Directory (Windows Server 2016 Datacenter)
    - (2) nó de cluster o SQL Server (SQL Server 2017 no Windows Server 2016)
    - (2) web/IIS (Centro de dados do Windows Server 2016)
- Conjuntos de Disponibilidade
    - (1) controladores de domínio o active Directory
    - (1) nós de cluster do SQL
    - (1) web/IIS
- Rede Virtual do Azure
    - redes virtuais do /16 (1)
    - sub-redes de /24 (5)
    - As definições de DNS estão definidas para os dois controladores de domínio
- Azure Load Balancer
- Gateway de Aplicação do Azure
    - (1) Gateway de aplicação WAF ativado
        - Modo de firewall: prevenção
        - Conjunto de regras: OWASP 3.0
        - serviço de escuta: porta 443
- Storage do Azure
    - (7) contas de armazenamento com redundância geográfica
- Testemunho de nuvem do Azure
- Cofre dos Serviços de Recuperação
- Azure Key Vault
- Azure Active Directory (Azure AD)
- Azure Resource Manager
- O Azure Monitor (logs)

## <a name="deployment-architecture"></a>Arquitetura de implantação

A secção seguinte fornece detalhes sobre os elementos de desenvolvimento e implementação.

**Anfitrião de bastião**: O anfitrião de bastião é o único ponto de entrada que fornece uma ligação segura para os administradores de recursos de acesso implementado. O bastion NSG do host permite ligações apenas na porta TCP 3389 para RDP. Os clientes podem ainda mais a configurar o anfitrião de bastião para atender aos requisitos de proteção do sistema de organização.

### <a name="virtual-network"></a>Rede virtual
A arquitetura define uma rede privada virtual com um espaço de endereços de 10.200.0.0/16.

**Grupos de segurança de rede**: Esta solução implementa recursos numa arquitetura com uma sub-rede de web separado, uma sub-rede de base de dados, sub-rede do Active Directory e a sub-rede de gestão na mesma rede virtual. Sub-redes logicamente são separados por aplicadas às sub-redes individuais para restringir o tráfego entre sub-redes para que apenas necessário para a funcionalidade de gerenciamento do sistema e de regras de grupo de segurança de rede.

Consulte a configuração para [grupos de segurança de rede](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) implementado com esta solução. Os clientes podem configurar grupos de segurança de rede ao editar o ficheiro acima usando [esta documentação](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) como guia.

Cada um das sub-redes tem um grupo de segurança de rede dedicada (NSG):
- 1 NSG para o Gateway de aplicação (LBNSG)
- 1 NSG para o anfitrião de bastião (MGTNSG)
- 1 NSG para controladores de domínio primário e de cópia de segurança (ADNSG)
- 1 NSG para servidores SQL (SQLNSG)
- 1 NSG para a camada Web (WEBNSG)

**Sub-redes**: Cada sub-rede está associado a seu NSG correspondente.

### <a name="data-at-rest"></a>Dados inativos

A arquitetura protege dados em repouso com várias medidas de encriptação.

**O armazenamento do Azure**: Para atender aos requisitos de encriptação de dados em repouso, todas as contas de armazenamento utilizam [Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

**SQL Server**: SQL Server está configurado para utilizar [encriptação de dados transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), que realiza a encriptação em tempo real e desencriptação de dados e arquivos de log para proteger as informações em repouso. TDE fornece garantia de que são armazenados os dados não tiver sido sujeita a acesso não autorizado.

Os clientes também podem configurar as seguintes medidas de segurança do SQL Server:
-   [Autenticação e autorização AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permite a gestão de identidades de utilizadores de base de dados e outros serviços da Microsoft num local central.
-   [A auditoria de base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) faixas de base de dados eventos e escreve-os para uma auditoria registo numa conta de armazenamento do Azure.
-   [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedir o acesso de todos os servidores de base de dados até que sejam concedidas as permissões apropriadas. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
-   [Deteção de ameaças SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite a deteção e resposta a ameaças potenciais à medida que ocorrem ao fornecer alertas de segurança para atividades suspeitas da base de dados, potenciais vulnerabilidades, ataques de injeção de SQL e acesso de base de dados anómalas padrões.
-   [Sempre encriptado colunas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Certifique-se de que os dados confidenciais nunca é apresentada como texto não criptografado dentro do sistema de base de dados. Depois de ativar a encriptação de dados, apenas as aplicações de cliente ou servidores de aplicações com acesso às chaves podem aceder aos dados de texto sem formatação.
-   [Máscara de dados dinâmicos da base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) pode ser feito depois de implementa a arquitetura de referência. Os clientes terão de ajustar as definições de cumprir para seu esquema de base de dados de máscara de dados dinâmicos.

**Azure Disk Encryption**: O Azure Disk Encryption é utilizado para discos de máquinas virtuais de IaaS do Windows criptografados. [O Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aproveita a funcionalidade BitLocker do Windows para fornecer encriptação de volume para o SO e discos de dados. A solução está integrada no Azure Key Vault para ajudar a controlar e gerir as chaves de encriptação de disco.

### <a name="identity-management"></a>Gestão de identidades

As seguintes tecnologias fornecem capacidades de gestão no ambiente do Azure de identidade:
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) é multi-inquilino com base na cloud diretório e identidade do serviço de gestão da Microsoft.
- Autenticação a uma aplicação web implementadas com o cliente pode ser executada com o Azure AD. Para obter mais informações, consulte [integrar aplicações com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- [Do Azure com base em função de controlo de acesso (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) permite a gestão de acesso direcionado precisamente para o Azure. Acesso à subscrição está limitado ao administrador da subscrição e acesso a recursos pode ser limitado com base na função de utilizador.
- Uma instância do Active Directory de IaaS fornece gestão de identidade ao nível do SO para máquinas de virtuais de IaaS implementadas.

### <a name="security"></a>Segurança
**Gestão de segredos**: A solução utiliza [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços. O Azure Key Vault ajuda a gerir as chaves de encriptação de disco de máquina virtual IaaS e segredos para esta arquitetura de referência.

**Gerenciamento de patches**: Máquinas de virtuais do Windows implementadas por esta segurança do Azure e a automatização de plano gráfico de conformidade são configuradas por predefinição para receber atualizações automáticas do serviço de atualização do Windows. Esta solução também implementa a solução de automatização do Azure através do qual as implementações de atualizações podem ser criadas para implantar patches para servidores do Windows quando necessário.

**Proteção contra Malware**: [O Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para máquinas virtuais fornece a capacidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outro software malicioso, com alertas configuráveis quando conhecido software malicioso ou indesejável tenta instalar ou executar nas máquinas virtuais protegidas.

**Gateway de aplicação**: A arquitetura reduz o risco de vulnerabilidades de segurança com um Gateway de aplicação com firewall de aplicações web (WAF) e o conjunto de regras do OWASP ativada. Capacidades adicionais incluem:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Ativar [descarga de SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Desativar [TLS versões 1.0 e 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall de aplicações Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modo de WAF)
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com o conjunto de regras do OWASP 3.0

### <a name="business-continuity"></a>Continuidade do negócio

**Elevada disponibilidade**: Pelo menos uma máquina virtual está disponível durante um evento de manutenção não planeada ou não planeada, atendendo a 99,95% do SLA do Azure. A solução implementa todas as camadas da web e máquinas virtuais na camada de dados de uma [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Conjuntos de disponibilidade garantem que as máquinas virtuais são distribuídas por vários clusters de hardware isolados para melhorar a disponibilidade. Além disso, esta solução implementa as máquinas virtuais do SQL Server num conjunto de disponibilidade como um [grupo de Disponibilidade AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). A funcionalidade de grupo Always On disponibilidade fornece capacidades de elevada disponibilidade e recuperação após desastre.

**Cofre de serviços de recuperação**: O [cofre dos Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) hospeda os dados de cópia de segurança e protege todas as configurações de máquinas virtuais do Azure nesta arquitetura. Com um cofre de serviços de recuperação, os clientes podem restaurar ficheiros e pastas a partir de uma VM de IaaS sem restaurar a VM inteira, permitindo que os tempos de restauro mais rápidos.

**Testemunho de nuvem**: [Testemunho de nuvem](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) é um tipo de testemunho de quórum do Cluster de ativação pós-falha no Windows Server 2016 que tira partido do Azure como o ponto de arbitragem. O testemunho de nuvem, como outro testemunho de quórum, obtém um voto e pode participar nos cálculos do quórum, mas utiliza o armazenamento de Blobs do Azure disponíveis publicamente padrão. Isso elimina a sobrecarga de manutenção extra de VMs alojadas numa nuvem pública.

### <a name="logging-and-auditing"></a>Registro e auditoria

Registos de Monitor do Azure fornece o registo extensivo de atividade do sistema e do usuário, bem como o estado de funcionamento do sistema. O [registos do Azure Monitor](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) solução recolhe e analisa os dados gerados pelos recursos no Azure e ambientes no local.

- **Registos de atividades:**  [Registos de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações executadas em recursos numa subscrição. Registos de atividades podem ajudar a determinar o iniciador de uma operação, hora da ocorrência e o estado.
- **Registos de diagnóstico:**  [Os registos de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) são emitidos por todos os recursos de todos os registos. Estes registos incluem registos de sistema de eventos do Windows, registos de armazenamento do Azure, registos de auditoria do Cofre de chaves e os registos de acesso e de firewall do Gateway de aplicação.
- **Arquivamento de registo:**  Todos os registos de diagnóstico escrevem para uma conta de armazenamento do Azure centralizado e criptografado para arquivamento. O período de retenção é configurável pelo utilizador, cópia de segurança e 730 dias, para atender aos requisitos de retenção de específicas da organização. Estes registos ligar-se aos registos do Azure Monitor para processamento, armazenamento e relatórios do dashboard.

Além disso, as seguintes soluções de monitorização são instaladas como parte dessa arquitetura. Tenha em atenção que é responsabilidade do cliente para configurar estas soluções para se alinhar com controlos de segurança do FedRAMP:
-   [Avaliação do AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de verificação de estado de funcionamento do Active Directory avalia o risco e estado de funcionamento dos ambientes de servidor num intervalo regular e fornece uma lista prioritária de recomendações específicas para a infraestrutura de servidor implementado.
-   [Avaliação de antimalware](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): A solução Antimalware relatórios sobre o estado de malware, ameaças e a proteção.
-   [A automatização do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): A solução de automatização do Azure armazena, executa e gere runbooks.
-   [Segurança e auditoria](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): O dashboard de segurança e auditoria fornece uma alto nível visão do Estado de segurança de recursos, fornecendo as métricas em domínios de segurança, problemas relevantes, deteções, informações sobre ameaças e consultas de segurança comuns.
-   [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de verificação de estado de funcionamento do SQL avalia o risco e estado de funcionamento dos ambientes de servidor num intervalo regular e fornece aos clientes uma lista prioritária de recomendações específicas para a infraestrutura de servidor implementado.
-   [Gestão de atualizações](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): A solução de gestão de atualizações permite a gestão de cliente de atualizações de segurança do sistema operativo, incluindo o processo de instalação de atualizações necessárias e o estado das atualizações disponíveis.
-   [Estado de funcionamento do agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução de estado de funcionamento do agente relata o número de agentes é implementado e a distribuição geográfica, como número de agentes que não respondem e o número de agentes que está a enviar dados operacionais.
-   [Registos de atividades do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução Log Analytics da atividade auxilia com uma análise de registos de atividade do Azure em todas as subscrições do Azure para um cliente.
-   [Controlo de alterações](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução de controlo de alterações permite aos clientes identificar facilmente as alterações no ambiente.

**O Azure Monitor**
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a acompanhar o desempenho, manter a segurança e identificar tendências, permitindo que as organizações de auditoria, criar alertas e arquivar dados, incluindo chamadas da API de controlo em recursos do Azure dos clientes.

## <a name="threat-model"></a>Modelo de risco
O diagrama de fluxo de dados para esta arquitetura de referência está disponível para [transferir](https://aka.ms/fedrampWAdfd) ou podem ser encontradas abaixo. Esse modelo pode ajudar os clientes a compreender os pontos do potencial risco de na infraestrutura de sistema ao fazer modificações.

![Aplicação Web de IaaS para o modelo de risco do FedRAMP](images/fedramp-iaaswa-threat-model.png?raw=true "aplicativo da Web de IaaS para o modelo de risco do FedRAMP")

## <a name="compliance-documentation"></a>Documentação de conformidade

O [Azure no Security and Compliance Blueprint - matriz de responsabilidade de cliente elevada do FedRAMP](https://aka.ms/blueprinthighcrm) apresenta uma lista de todos os controlos de segurança necessários para a linha de base do FedRAMP elevado. A matriz indica se a implementação de cada controle é da responsabilidade da Microsoft, o cliente, ou partilhados entre os dois.

O [segurança do Azure e o esquema de conformidade - FedRAMP IaaS Web Application elevada controle implementação Matrix](https://aka.ms/blueprintwacim) apresenta uma lista de todos os controlos de segurança necessários para a linha de base do FedRAMP elevado. A matriz fornece informações nos quais controles estão abrangidos pela arquitetura de aplicativo da web IaaS, incluindo descrições detalhadas de como a implementação cumpre os requisitos de cada controle coberta.

## <a name="deploy-the-solution"></a>Implementar a solução

Esta segurança do Azure e a automação de plano gráfico de conformidade é composto por ficheiros de configuração JSON e scripts do PowerShell que são processados pelo serviço de API do Azure Resource Manager para implementar recursos no Azure. Estão disponíveis instruções de implementação detalhados [aqui](https://aka.ms/fedrampblueprintrepo).
> [!NOTE]
> Esta solução implementa para o Azure Government.

#### <a name="quickstart"></a>Início Rápido
1. Clonar ou transferir [isso](https://aka.ms/fedrampblueprintrepo) repositório do GitHub para a estação de trabalho local.

2. Executar o script do PowerShell de pré-implementação: azure-blueprint/predeploy/Orchestration_InitialSetup.ps1.

3. Clique no botão abaixo, inicie sessão no portal do Azure, introduza os parâmetros de modelo ARM necessários e clique em **Compra**.

    [![Implementar no Azure](https://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="guidance-and-recommendations"></a>Orientações e recomendações
### <a name="vpn-and-expressroute"></a>ExpressRoute e VPN
Um túnel VPN seguro ou [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) tem de ser configurado com segurança estabelecer uma ligação para os recursos implementados como parte desta arquitetura de referência de aplicação Web IaaS. Configurando adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para dados em trânsito.

Ao implementar um túnel VPN seguro com o Azure, é possível criar uma ligação privada virtual entre uma rede no local e uma rede Virtual do Azure. Essa conexão ocorre através da Internet e permite que os clientes com segurança as informações de "encapsulamento" dentro de uma ligação encriptada entre a rede do cliente e o Azure. VPN site a Site é uma tecnologia madura e segura que tenha sido implementada por empresas de todos os tamanhos há décadas. O [modo de encapsulamento IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é utilizada nesta opção como um mecanismo de criptografia.

Porque o tráfego de túnel VPN atravessa a Internet com uma VPN de site a site, a Microsoft oferece a opção de ligação de outra, ainda mais seguro. O Azure ExpressRoute é uma WAN dedicada ligação entre o Azure e uma localização no local ou um fornecedor de alojamento do Exchange. Como as ligações ExpressRoute não entram na Internet, estas ligações oferecem mais fiabilidade, velocidades mais rápidas, latências mais baixas e maior segurança do que as ligações normais pela Internet. Além disso, como se trata de uma conexão direta do fornecedor de telecomunicação do cliente, os dados não percorre a Internet e, portanto, não são expostos ao mesmo.

Melhores práticas para implementar uma rede híbrida segura que expande uma rede no local para o Azure são [disponível](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Exclusão de Responsabilidade

- Este documento é apenas para fins informativos. A MICROSOFT FAZ NÃO OFERECE QUAISQUER GARANTIAS, EXPRESSAS, IMPLÍCITAS OU ESTATUTÁRIAS QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "como-está." Informações e opiniões expressados neste documento, incluindo URLs e outras referências de Web site da Internet, podem ser alteradas sem aviso prévio. Os clientes a ler este documento assume o risco de usá-lo.  
- Este documento não fornece aos clientes quaisquer direitos de propriedade intelectual em qualquer produto da Microsoft ou soluções.  
- Os clientes podem copiar e utilizar este documento para efeitos de referência interna.  
- Algumas recomendações neste documento podem resultar em maior de dados, a rede ou a utilização de recursos de computação no Azure e podem aumentar os custos de licenciamento ou de subscrição do Azure num cliente.  
- Esta arquitetura deve ser usado como uma base para os clientes que se ajustar às suas necessidades específicas e não devem ser utilizada como-está num ambiente de produção.
- Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos requisitos de conformidade e normas. Os clientes procuram legal suporte da sua organização em implementações do cliente aprovadas.
