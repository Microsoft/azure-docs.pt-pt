---
title: Análise de Blueprint de Segurança e Conformidade do Azure para FedRAMP
description: Análise de Blueprint de Segurança e Conformidade do Azure para FedRAMP
services: security
author: jomolesk
ms.assetid: b4675715-668e-4557-9c1c-698aabf62ceb
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 9850c5f064815315db6f85a931e7e175d605dcc1
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "71257596"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-fedramp"></a>Blueprint de Segurança e Conformidade do Azure: Analytics para FedRAMP

## <a name="overview"></a>Visão geral

O [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) é um programa Estados Unidos de todo o governo que fornece uma abordagem padronizada para avaliação de segurança, autorização e monitoramento contínuo para produtos e serviços de nuvem. Este Blueprint de Segurança e Conformidade do Azure fornece diretrizes sobre como fornecer uma arquitetura de análise de Microsoft Azure que ajuda a implementar um subconjunto de controles altos do FedRAMP. Esta solução fornece orientação sobre a implantação e a configuração de recursos do Azure para uma arquitetura de referência comum, demonstrando maneiras nas quais os clientes podem atender aos requisitos específicos de segurança e conformidade e serve como base para os clientes Crie e configure suas próprias soluções de análise no Azure.

Essa arquitetura de referência, os guias de implementação de controle associados e os modelos de ameaça são destinados a servir como base para os clientes se ajustarem aos requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção. Implantar um aplicativo nesse ambiente sem modificação é insuficiente para atender totalmente aos requisitos da linha de base FedRAMP alta. Tenha em atenção o seguinte:
- A arquitetura fornece uma linha de base para ajudar os clientes a implantar cargas de trabalho no Azure de maneira compatível com FedRAMP.
- Os clientes são responsáveis por realizar avaliações apropriadas de segurança e conformidade de qualquer solução criada usando essa arquitetura, já que os requisitos podem variar com base nas especificidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura

Essa solução fornece uma plataforma de análise na qual os clientes podem criar suas próprias ferramentas de análise. A arquitetura de referência descreve um caso de uso genérico em que os clientes de entrada são dados por meio de importações de dados em massa pelo administrador do SQL/data ou por meio de atualizações de dados operacionais por meio de um usuário operacional. Os dois fluxos de trabalho incorporam [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) para importar dados para o banco de dado SQL. Azure Functions deve ser configurado pelo cliente por meio do portal do Azure para lidar com as tarefas de importação exclusivas dos requisitos de análise de cada cliente.

O Microsoft Azure oferece uma variedade de relatórios e serviços de análise para o cliente; no entanto, essa solução incorpora Azure Analysis Services em conjunto com o banco de dados SQL do Azure para navegar rapidamente por data e fornecer resultados mais rápidos por meio da modelagem mais inteligente de dados do cliente. Os serviços de análise do Azure são uma forma de aprendizado de máquina destinado a aumentar as velocidades de consulta, descobrindo novas relações entre conjuntos de os. Depois que os dados tiverem sido treinados por meio de várias funções estatísticas, até 7 pools de consulta adicionais (8 total incluindo o servidor do cliente) podem ser sincronizados com os mesmos modelos de tabela para espalhar a carga de trabalho de consulta e reduzir os tempos de resposta.

Para análise e relatórios aprimorados, os bancos de dados SQL podem ser configurados com índices columnstore. Os serviços de análise do Azure e os bancos de dados SQL podem ser escalados ou reduzidos verticalmente ou desligados completamente em resposta ao uso do cliente. Todo o tráfego do SQL é criptografado com SSL por meio da inclusão de certificados autoassinados. Como prática recomendada, o Azure recomenda o uso de uma autoridade de certificação confiável para aumentar a segurança.

Depois que os dados são carregados no banco de dado SQL do Azure e treinados pelo Azure Analysis Services, eles são resumidos pelo usuário operacional e pelo administrador do SQL/data com Power BI. Power BI exibe os dados de maneira intuitiva e reúne informações em vários conjuntos de dados para extrair mais insights. Seu alto grau de adaptabilidade e fácil integração com o banco de dados SQL do Azure garante que os clientes possam configurá-lo para lidar com uma ampla gama de cenários conforme exigido por suas necessidades comerciais.

A solução inteira é criada com base em um armazenamento do Azure que conta os clientes configuram no portal do Azure. O armazenamento do Azure criptografa todos os dados com Criptografia do Serviço de Armazenamento para manter a confidencialidade dos dados em repouso.  O GRS (armazenamento com redundância geográfica) garante que um evento adverso na data center primária do cliente não resulte em uma perda de dados, pois uma segunda cópia será armazenada em um local separado a centenas de quilômetros de distância.

Para aumentar a segurança, essa arquitetura gerencia recursos com Azure Active Directory e Azure Key Vault. A integridade do sistema é monitorada por meio de Azure Monitor. Os clientes configuram ambos os serviços de monitoramento para capturar logs e exibir a integridade do sistema em um único painel fácil e navegável.

O banco de dados SQL do Azure é normalmente gerenciado por meio do SQL Server Management Studio (SSMS), que é executado de um computador local configurado para acessar o banco de dados SQL do Azure por meio de uma conexão VPN ou ExpressRoute segura. **O Azure recomenda configurar uma conexão VPN ou Azure ExpressRoute para gerenciamento e importação de dados no grupo de recursos arquitetura de referência.**

![Análise do diagrama da arquitetura de referência do FedRAMP](images/fedramp-analytics-reference-architecture.png?raw=true "Análise do diagrama da arquitetura de referência do FedRAMP")

### <a name="roles"></a>Funções
O plano gráfico de análise descreve um cenário com três tipos de usuário gerais: o usuário operacional, o administrador de SQL/dados e o engenheiro de sistemas. O RBAC (controle de acesso baseado em função) do Azure permite a implementação do gerenciamento de acesso preciso por meio de funções personalizadas internas. Os recursos estão disponíveis para configurar o [controle de acesso baseado em função e a](../../role-based-access-control/role-assignments-portal.md) estrutura de tópicos e a implementação de [funções predefinidas](../../role-based-access-control/built-in-roles.md).

#### <a name="systems-engineer"></a>Engenheiro de sistemas
O engenheiro de sistemas possui a assinatura de cliente do Azure e configura a implantação da solução por meio do portal do Azure.

#### <a name="sqldata-administrator"></a>SQL/administrador de dados
O administrador de SQL/dados estabelece a função de importação de dados em massa e a função de atualização de dados operacional para carregar no banco de dados SQL do Azure. O administrador do SQL/data não é responsável por nenhuma atualização de dados operacional no banco de dados, mas poderá exibir os dados por meio de Power BI.

#### <a name="operational-user"></a>Usuário operacional
O usuário operacional atualiza os dados regularmente e possui a geração de dados do dia a dia. O usuário operacional também irá interpretar os resultados por meio de Power BI.

### <a name="azure-services"></a>Serviços do Azure

Essa solução usa os seguintes serviços do Azure. Os detalhes da arquitetura de implantação estão na seção [arquitetura de implantação](#deployment-architecture) .
- Funções do Azure
- Base de Dados SQL do Azure
- Serviço de análise do Azure
- Azure Active Directory
- Azure Key Vault
- Azure Monitor (logs)
- Armazenamento do Azure
- Gateway de ExpressRoute/VPN
- Dashboard do Power BI

## <a name="deployment-architecture"></a>Arquitetura de implantação
A seção a seguir detalha os elementos de desenvolvimento e implementação.

![texto alt](images/fedramp-analytics-components.png?raw=true "Diagrama do Analytics for FedRAMP Components")

**Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) são soluções para executar pequenas partes de código na nuvem por meio da maioria das linguagens de programação. As funções nessa solução se integram ao armazenamento do Azure para extrair automaticamente os dados do cliente para a nuvem, facilitando a integração com outros serviços do Azure. As funções são facilmente escalonáveis e só incorrem em um custo quando estão em execução.

**Azure Analysis Service**: o [serviço de análise do Azure](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) fornece a modelagem e a integração de dados empresariais com os serviços da plataforma de dados do Azure. O Azure Analysis Service acelera a navegação por grandes quantidades de dados combinando dados de várias fontes em um único modelo de dados.

**Power bi**: o [Power bi](https://docs.microsoft.com/power-bi/service-azure-and-power-bi) fornece a funcionalidade de análise e geração de relatórios para clientes que tentam extrair mais informações sobre seus esforços de processamento de dados.

### <a name="networking"></a>Funcionamento em Rede
**Grupos de segurança de rede**: [NSGs](../../virtual-network/virtual-network-vnet-plan-design-arm.md) são configurados para gerenciar o tráfego direcionado em serviços e recursos implantados. Os grupos de segurança de rede são definidos como um esquema de negação por padrão e só permitem o tráfego contido na ACL (lista de controle de acesso) pré-configurada.

Cada um dos NSGs tem portas e protocolos específicos abertos para que a solução possa funcionar de forma segura e correta. Além disso, as seguintes configurações estão habilitadas para cada NSG:
  - [Os logs e eventos de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) são habilitados e armazenados em uma conta de armazenamento
  - [Os logs de Azure monitor](../../azure-monitor/insights/azure-networking-analytics.md) estão conectados aos logs de diagnóstico do NSG.

### <a name="data-at-rest"></a>Dados inativos
A arquitetura protege os dados em repouso por meio de criptografia, auditoria de banco e outras medidas.

**Replicação de dados** O Azure governamental tem duas opções de [replicação de dados](https://docs.microsoft.com/azure/storage/common/storage-redundancy):
 - A configuração padrão para a replicação de dados é o **armazenamento com redundância geográfica (GRS)** , que armazena de forma assíncrona os dados do cliente em um Data Center separado fora da região primária. Isso garante a recuperação de dados em um evento de perda total para o data center primário.
 - O **LRS (armazenamento com redundância local)** pode ser configurado como alternativa por meio da conta de armazenamento do Azure. O LRS replica dados em uma unidade de escala de armazenamento, que é hospedada na mesma região em que o cliente cria sua conta. Todos os dados são replicados simultaneamente, garantindo que nenhum dado de backup seja perdido em uma falha de unidade de escala de armazenamento primária.

**Armazenamento do Azure** Para atender aos requisitos de dados criptografados em repouso, todos os serviços implantados nessa arquitetura de referência aproveitam o [armazenamento do Azure](https://azure.microsoft.com/services/storage/), que armazena dados com [criptografia do serviço de armazenamento](../../storage/common/storage-service-encryption.md).

**Azure Disk Encryption** 
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) aproveita o recurso BitLocker do Windows para fornecer criptografia de volume para o sistema operacional e discos de dados. A solução se integra com Azure Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

**Banco de dados SQL do Azure** A instância do banco de dados SQL do Azure usa as seguintes medidas de segurança de banco de dados:
-   A [autenticação e a autorização do AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permitem o gerenciamento de identidades de usuários de banco de dados e outros serviços da Microsoft em um local central.
-   A [auditoria do banco de dados SQL](../../sql-database/sql-database-auditing.md) rastreia eventos de banco de dados e os grava em um log de auditoria em uma conta de armazenamento do Azure.
-   O banco de dados SQL está configurado para usar o [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que executa criptografia e descriptografia em tempo real de arquivos de log e data para proteger informações em repouso. O TDE fornece garantia de que os dados armazenados não estão sujeitos a acesso não autorizado.
-   [As regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem todo o acesso a servidores de banco de dados até que sejam concedidas permissões adequadas A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
-   A [detecção de ameaças do SQL](../../sql-database/sql-database-threat-detection.md) permite a detecção e a resposta a ameaças potenciais à medida que elas ocorrem, fornecendo alertas de segurança para atividades suspeitas de banco de dados, vulnerabilidades potenciais, ataques de injeção de SQL e padrões de acesso de banco de dados anormais
-   [Always Encrypted colunas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que os dados confidenciais nunca apareçam como texto não criptografado no sistema de banco de dados. Depois de habilitar a criptografia de dados, somente aplicativos cliente ou servidores de aplicativos com acesso às chaves podem acessar dados de texto sem formatação.
-   O [mascaramento de dados dinâmicos do banco SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) pode ser feito após a implantação da arquitetura de referência. Os clientes precisarão ajustar as configurações de mascaramento de dados dinâmicos para aderir ao seu esquema de banco de dado.

### <a name="logging-and-audit"></a>Registro em log e auditoria
O [Azure monitor](../../azure-monitor/overview.md) gera uma exibição completa dos dados de monitoramento, incluindo logs de atividades, métricas e dados de diagnóstico, permitindo que os clientes criem uma visão geral da integridade do sistema.  
[Os logs de Azure monitor](../azure-security-disk-encryption-overview.md) fornecem log extensivo de atividade do sistema e do usuário, bem como a integridade do sistema. Ele coleta e analisa os dados gerados pelos recursos no Azure e em ambientes locais.
- **Logs de atividade**: [os logs de atividade](../../azure-monitor/platform/activity-logs-overview.md) fornecem informações sobre as operações executadas nos recursos em uma assinatura.
- **Logs de diagnóstico**: os [logs de diagnóstico](../../azure-monitor/platform/resource-logs-overview.md) incluem todos os logs emitidos por cada recurso. Esses logs incluem logs do sistema de eventos do Windows e armazenamento de BLOBs do Azure, tabelas e logs de fila.
- **Logs de firewall**: o gateway de aplicativo fornece logs completos de diagnóstico e acesso. Os logs de firewall estão disponíveis para recursos de gateway de aplicativo habilitados para WAF.
- **Arquivamento de log**: todos os logs de diagnóstico gravam em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento com um período de retenção definido de 2 dias. Esses logs se conectam a logs de Azure Monitor para processamento, armazenamento e relatórios de painel.

Além disso, as soluções de monitoramento a seguir são incluídas como parte dessa arquitetura:
-   [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): a solução de automação do Azure armazena, executa e gerencia runbooks.
-   [Segurança e auditoria](../../security-center/security-center-intro.md): o painel de segurança e auditoria fornece uma percepção de alto nível sobre o estado de segurança dos recursos fornecendo métricas sobre domínios de segurança, problemas notáveis, detecções, inteligência contra ameaças e consultas de segurança comuns.
-   [Avaliação do SQL](../../azure-monitor/insights/sql-assessment.md): a solução de verificação da integridade do SQL avalia o risco e a integridade de ambientes de servidor em um intervalo regular e fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
-   [Logs de atividades do Azure](../../azure-monitor/platform/collect-activity-logs.md): a solução análise do log de atividades auxilia na análise dos logs de atividade do Azure em todas as assinaturas do Azure para um cliente.

### <a name="identity-management"></a>Gestão de identidades
-   A autenticação para o aplicativo é executada usando o Azure AD. Para obter mais informações, consulte [integrando aplicativos com Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Além disso, a criptografia de coluna de banco de dados usa o Azure AD para autenticar o aplicativo no banco de dados SQL do Azure. Para obter mais informações, Confira como [proteger dados confidenciais no SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detecta possíveis vulnerabilidades que afetam as identidades de uma organização, configura as respostas automatizadas para detectar ações suspeitas relacionadas às identidades de uma organização e investiga incidentes suspeitos para tomar as medidas apropriadas para resolvê-los.
-   O [RBAC (controle de acesso baseado em função) do Azure](../../role-based-access-control/role-assignments-portal.md) permite o gerenciamento de acesso focado para o Azure. O acesso à assinatura é limitado ao administrador da assinatura.

Para saber mais sobre como usar os recursos de segurança do banco de dados SQL do Azure, consulte o exemplo de [aplicativo de demonstração da Contoso Clinic](https://github.com/Microsoft/azure-sql-security-sample) .

### <a name="security"></a>Segurança
**Gerenciamento de segredos**: a solução usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços.

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações

### <a name="expressroute-and-vpn"></a>ExpressRoute e VPN
O [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou um túnel VPN seguro precisa ser configurado para estabelecer com segurança uma conexão com os recursos implantados como parte dessa arquitetura de referência da análise de dados. Como as conexões do ExpressRoute não passam pela Internet, essas conexões oferecem mais confiabilidade, velocidades mais rápidas, latências menores e mais segurança do que as conexões típicas pela Internet. Ao configurar adequadamente o ExpressRoute ou uma VPN, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

### <a name="azure-active-directory-setup"></a>Azure Active Directory configuração
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) é essencial para gerenciar a implantação e o provisionamento de acesso à equipe interagindo com o ambiente. Um Active Directory do Windows Server existente pode ser integrado com o AAD em [quatro cliques](../../active-directory/hybrid/how-to-connect-install-express.md). Os clientes também podem vincular a infraestrutura de Active Directory implantada (controladores de domínio) a um AAD existente, tornando a infraestrutura de Active Directory implantada um subdomínio de uma floresta do AAD.

### <a name="additional-services"></a>Serviços adicionais
#### <a name="iaas---vm-considerations"></a>Considerações sobre IaaS-VM
Essa solução PaaS não incorpora nenhuma VM IaaS do Azure. Um cliente pode criar uma VM do Azure para executar muitos desses serviços de PaaS. Nesse caso, os recursos e serviços específicos para continuidade dos negócios e logs de Azure Monitor podem ser aproveitados:

##### <a name="business-continuity"></a>Continuidade de negócio
- **Alta disponibilidade**: as cargas de trabalho do servidor são agrupadas em um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ajudar a garantir a alta disponibilidade de máquinas virtuais no Azure. Pelo menos uma máquina virtual está disponível durante um evento de manutenção planejada ou não planejada, atendendo ao SLA de 99,95% do Azure.

- **Cofre dos serviços de recuperação**: o [cofre dos serviços de recuperação](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) hospeda dados de backup e protege todas as configurações de máquinas virtuais do Azure nessa arquitetura. Com um cofre dos serviços de recuperação, os clientes podem restaurar arquivos e pastas de uma VM IaaS sem restaurar toda a VM, permitindo tempos de restauração mais rápidos.

##### <a name="monitoring-solutions"></a>Soluções de monitorização
-   [Avaliação do AD](../../azure-monitor/insights/ad-assessment.md): a solução de verificação de integridade Active Directory avalia o risco e a integridade dos ambientes de servidor em um intervalo regular e fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
-   [Avaliação de antimalware](../../security-center/security-center-install-endpoint-protection.md): a solução antimalware relata sobre malware, ameaças e status de proteção.
-   [Gerenciamento de atualizações](../../automation/automation-update-management.md): a solução gerenciamento de atualizações permite o gerenciamento de clientes das atualizações de segurança do sistema operacional, incluindo o status das atualizações disponíveis e o processo de instalação das atualizações necessárias.
-   [Integridade do agente](../../monitoring/monitoring-solution-agenthealth.md): a solução integridade do agente relata Quantos agentes são implantados e sua distribuição geográfica, bem como Quantos agentes que não respondem e o número de agentes que estão enviando dados operacionais.
-   [Controle de alterações](../../automation/change-tracking.md): a solução controle de alterações permite que os clientes identifiquem facilmente as alterações no ambiente.

##### <a name="security"></a>Segurança
- **Proteção contra malware**: [o Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) para máquinas virtuais fornece recursos de proteção em tempo real que ajudam a identificar e remover vírus, spyware e outros softwares mal-intencionados, com alertas configuráveis quando conhecidos ou mal-intencionados o software indesejado tenta instalar ou executar em máquinas virtuais protegidas.
- **Gerenciamento de patches**: máquinas virtuais do Windows implantadas como parte dessa arquitetura de referência são configuradas por padrão para receber atualizações automáticas do serviço Windows Update. Essa solução também inclui o serviço de [automação do Azure](https://docs.microsoft.com/azure/automation/automation-intro) por meio do qual implantações atualizadas podem ser criadas para aplicar patch às máquinas virtuais quando necessário.

#### <a name="azure-commercial"></a>Comercial do Azure
Embora essa arquitetura de análise de dados não seja destinada à implantação no ambiente [comercial do Azure](https://azure.microsoft.com/overview/what-is-azure/) , objetivos semelhantes podem ser obtidos por meio dos serviços descritos nesta arquitetura de referência, bem como serviços adicionais disponíveis somente no ambiente comercial do Azure. Observe que o Azure Commercial mantém um FedRAMP JAB P-ATO no nível de impacto moderado, permitindo que agências governamentais e parceiros implantem informações moderadamente confidenciais na nuvem, aproveitando o ambiente comercial do Azure.

O Azure Commercial oferece uma ampla variedade de serviços de análise para obter informações sobre grandes quantidades de dados:
-   [Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio), um componente do [Cortana Intelligence Suite](https://azure.microsoft.com/overview/ai-platform/), desenvolve um modelo de análise preditiva de uma ou mais fontes de dados. As funções estatísticas são usadas em várias iterações para gerar um modelo efetivo que aplicativos como Power BI podem, então, consumir.
-   [Azure data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) permite a captura de dados de qualquer tamanho, tipo e velocidade de ingestão em um único lugar para análise operacional e exploratório. Azure Data Lake Store é compatível com a maioria dos componentes de software livre no ecossistema do Hadoop e se integra perfeitamente com outros serviços do Azure.

## <a name="threat-model"></a>Modelo de ameaça

O DFD (diagrama de fluxo de dados) para esta arquitetura de referência está disponível para [Download](https://aka.ms/blueprintanalyticsthreatmodel) ou pode ser encontrado abaixo:

## <a name="compliance-documentation"></a>Documentação de conformidade
A [matriz de responsabilidade do cliente Blueprint de segurança e conformidade do Azure – FedRAMP alta](https://aka.ms/blueprinthighcrm) lista todos os controles de segurança exigidos pela linha de base FedRAMP alta. Da mesma forma, a [matriz de responsabilidade do cliente Blueprint de segurança e conformidade do Azure – FedRAMP moderada](https://aka.ms/blueprintanalyticscimmod) lista todos os controles de segurança exigidos pela linha de base moderada FedRAMP. Os dois documentos detalham se a implementação de cada controle é de responsabilidade da Microsoft, do cliente ou do compartilhamento entre os dois.

A [matriz de implementação de controle alto de Blueprint de segurança e conformidade do Azure FedRAMP](https://aka.ms/blueprintanalyticscimhigh) e a [matriz de implementação de controle moderado Blueprint de segurança e conformidade do Azure-FedRAMP](https://aka.ms/blueprintanalyticscimmod) fornecem informações sobre quais controles são cobertos por a arquitetura de análise para cada linha de base FedRAMP, incluindo descrições detalhadas de como a implementação atende aos requisitos de cada controle abordado.

## <a name="disclaimer"></a>Exclusão de Responsabilidade

 - Este documento é apenas para fins informativos. A MICROSOFT NÃO OFERECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU ESTATUTÁRIA, QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e as exibições expressas neste documento, incluindo URLs e outras referências de site da Internet, podem ser alteradas sem aviso prévio. Os clientes que lêem este documento trazem o risco de usá-lo.
 - Este documento não fornece aos clientes nenhum direito legal sobre qualquer propriedade intelectual em qualquer produto ou solução da Microsoft.
 - Os clientes podem copiar e usar este documento para fins de referência interna.
 - Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure, e podem aumentar os custos de assinatura ou de licença do Azure do cliente.
 - Essa arquitetura destina-se a servir como base para os clientes se ajustarem aos requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção.
 - Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender a requisitos e regulamentos específicos de conformidade. Os clientes devem buscar o suporte legal de sua organização em implementações aprovadas do cliente.
