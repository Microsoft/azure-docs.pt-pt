---
title: Segurança do Azure e o esquema de conformidade - aplicação Web de PaaS de alojamento para cargas de trabalho oficial do Reino Unido
description: Segurança do Azure e o esquema de conformidade - aplicação Web de PaaS de alojamento para cargas de trabalho oficial do Reino Unido
services: security
author: jomolesk
ms.assetid: 446105ad-a863-44f5-a964-6ead1dac4787
ms.service: security
ms.topic: article
ms.date: 07/13/2018
ms.author: jomolesk
ms.openlocfilehash: 7f67b7363719f544c485503e0f8207bb1f1078ce
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244711"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-hosting-for-uk-official-workloads"></a>Azure Security and Compliance Blueprint: Aplicação Web de PaaS de alojamento para cargas de trabalho oficial do Reino Unido

## <a name="azure-security-and-compliance-blueprints"></a>Segurança do Azure e Esquemas de Conformidade

Planos gráficos do Azure é composto por documentos de diretrizes e modelos de automatização que implementar arquiteturas com base na cloud para oferecer soluções para cenários que têm requisitos de compatibilidade ou acreditação. Planos gráficos do Azure são coleções de modelos automação e diretrizes que permitem aos clientes do Microsoft Azure acelerar o fornecimento de suas metas de negócios por meio de uma arquitetura de base que pode ser estendida para satisfazer quaisquer requisitos adicionais de aprovisionamento.

## <a name="overview"></a>Descrição geral

Este Azure no Security and Compliance Blueprint fornece scripts de automação e diretrizes para entregar um Microsoft Azure [plataforma como serviço (PaaS)](https://azure.microsoft.com/overview/what-is-paas/) alojado adequada para processar cargas de trabalho classificadas da arquitetura de aplicativos web como [oficial do Reino Unido](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/715778/May-2018_Government-Security-Classifications-2.pdf). Esta classificação de segurança abrange a maioria das informações criado ou processados do setor público. Isto inclui operações de negócios de rotina e serviços, que, se perdido, roubado ou publicado no suporte de dados, algumas das quais podem ter conseqüências graves. O perfil de ameaça típico para a classificação OFFICIAL é muito semelhante a uma empresa privada que fornece serviços e informações valiosas. Reino Unido oficial prevê a necessidade de se proteger dados do Governo do Reino Unido ou serviços contra ameaças ou compromisso pelos atacantes com estagnação capacidades e recursos, tais como (mas não está limitado a) hactivists single-issue de pressão de grupos, jornalistas investigativos, competente hackers individuais e a maioria dos grupos e indivíduos criminais.

Este esquema tiver sido revisto pelo do Reino Unido National Cyber Security Centre (NCSC) e alinha os princípios de segurança de Cloud de 14 NCSC.

A arquitetura utiliza o Azure [plataforma como serviço](https://azure.microsoft.com/overview/what-is-paas/) componentes para entregar um ambiente que permite aos clientes evitar a despesa e a complexidade da compra de licenças de software, de gestão da infraestrutura subjacente do aplicativo e middleware ou as ferramentas de desenvolvimento e outros recursos. Os clientes gerir as aplicações e serviços que desenvolve, concentrando-se em fornecer valor comercial, lidando simultaneamente e o Microsoft Azure gere os outros recursos do Azure como máquinas virtuais, armazenamento e rede, colocar mais do [divisão da responsabilidade](https://docs.microsoft.com/azure/security/security-paas-deployments#division-of-responsibility) para a gestão de infraestrutura para a plataforma do Azure. [Serviços de aplicações Azure](https://azure.microsoft.com/services/app-service/) oferece o dimensionamento automático, de elevada disponibilidade, suporta Windows e Linux e permite implementações automáticas a partir do GitHub, do Azure DevOps ou qualquer repositório de Git como serviços de predefinição. Durante a utilização dos serviços de aplicações, os desenvolvedores podem se concentrar no fornecimento de valor de negócio sem a sobrecarga de gerenciamento de infraestrutura. É possível criar novas aplicações de web de Java, PHP, node. js, Python, HTML ou c# de ambiente intacto, ou também para migrar a nuvem existente ou no local da web aplicações para serviços de aplicações do Azure (embora completa devida diligência e de teste para confirmar o desempenho é necessário).

Este esquema enfoca o aprovisionamento de uma base segura [plataforma como serviço](https://azure.microsoft.com/overview/what-is-paas/) interface baseada na web para público e também os utilizadores de back office. Neste cenário de design do esquema considera que a utilização do Azure alojada serviços baseados na web, onde um usuário público pode com segurança submeter, ver e gerir dados confidenciais; também que um operador de back-office ou do Governo pode processar os dados confidenciais que o usuário público submeteu em segurança. Casos de utilização para este cenário podem incluir:

- Um utilizador de submeter um retorno de imposto, com um operador de Governo processar a submissão;
- Um utilizador que solicitam um serviço através de uma aplicação baseada na web, com um utilizador de back office, validação e fornecer o serviço; ou
- Um usuário procurar e visualizar o domínio público ajudam a informações relativas um serviço de administração pública.

Usando [do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modelos e scripts de Interface de linha de comandos do Azure, o plano gráfico implementa um ambiente que se alinha para do Reino Unido National Cyber Security Centre (NCSC) 14 [osprincípiosdesegurançanaCloud](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) e o Centro de segurança de Internet (CIS) [controlos de segurança críticas](https://www.cisecurity.org/critical-controls.cfm). O NCSC recomenda seus princípios de segurança na Cloud utilizadas pelos clientes para avaliar as propriedades de segurança do serviço de e para ajudar a compreender a divisão de responsabilidade entre o cliente e o fornecedor. A Microsoft forneceu informações em relação a cada um desses princípios para ajudar a compreender melhor a divisão das responsabilidades. Esta arquitetura e modelos do Azure Resource Manager correspondente são compatíveis com o White Paper [utilizar o Microsoft Azure na cloud de 14 controlos de segurança de Cloud para o RU](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Esta arquitetura tiver sido revista pelo NCSC e se alinha com do Reino Unido NCSC 14 Cloud princípios de segurança, permitindo desta forma organizações do setor público para sua capacidade para cumprir as obrigações reguladoras de conformidade com serviços baseados na nuvem, globalmente e no Reino Unido, no instante o Nuvem do Microsoft Azure. Este modelo implementa a infraestrutura para a carga de trabalho. Código do aplicativo e o suporte de camada de negócio e o software de camada de dados devem ser instalados e configurados por parte dos clientes. Estão disponíveis instruções de implementação detalhados [aqui](https://aka.ms/ukofficial-paaswa-repo/).

Este esquema é uma arquitetura de base. Os nossos clientes podem usar esse plano gráfico como base para a classificação OFFICIAL baseada na web cargas de trabalho e expandir os modelos e recursos com seus próprios requisitos. Esta arquitetura baseia-se sobre os princípios dos [esquema de aplicativos de Web de IaaS de três camadas do Reino Unido OFFICAL](https://aka.ms/ukofficial-iaaswa) para oferecer os nossos clientes [infraestrutura como serviço (IaaS)](https://azure.microsoft.com/overview/what-is-iaas/) e opções de implementação de PaaS para alojar cargas de trabalho baseadas na web.

Para implementar esta arquitetura, é necessária uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode inscrever-se rapidamente e facilmente, sem qualquer custo: Introdução ao Azure. Clique em [aqui](https://aka.ms/ukofficial-paaswa-repo/) para obter instruções de implementação.

## <a name="architecture-and-components"></a>Arquitetura e componentes

Esta arquitetura fornece um solução num ambiente de cloud do Azure que suporta cargas de trabalho do Reino Unido oficial de hospedagem de aplicativos de web. A arquitetura proporciona um ambiente seguro que tira partido da plataforma do Azure como um capacidades do serviço. Dentro do ambiente, duas aplicações web do serviço de aplicações são implementado (um para os utilizadores públicos) e outra para usuários de back office, com uma camada de aplicação API fornecer os serviços de negócios para o front-end da web. Uma base de dados do SQL do Azure é implementada como um arquivo de dados relacionais geridos para a aplicação. Conectividade para esses componentes de fora a plataforma e entre todos esses componentes é criptografada por meio de TLS 1.2 para garantir que os dados em privacidade de transporte, com acesso autenticado pelo Azure Active Directory.

![PaaS alojamento da aplicação Web para o diagrama de arquitetura de referência de cargas de trabalho oficial do Reino Unido](images/ukofficial-paaswa-architecture.png?raw=true "PaaS alojamento da aplicação Web para o diagrama de arquitetura de referência de cargas de trabalho oficial do Reino Unido")

Como parte da arquitetura de implantação, aprovisionar armazenamento seguro, monitorização & registo, gestão de segurança unificada e proteção avançada contra ameaças e gestão capacidades também são implementadas para garantir que os clientes tenham todas as ferramentas necessárias para proteger e monitorizar o seu ambiente para esta solução.

Esta solução utiliza os seguintes serviços do Azure. Os detalhes a arquitetura de implementação estão no [arquitetura de implantação](#deployment-architecture) secção.

- Azure Active Directory
- Serviço de Aplicações
- Aplicação Web
- Aplicação API
- DNS do Azure
- Cofre de Chaves
- O Azure Monitor (logs)
- Application Insights
- Azure Resource Manager
- Centro de Segurança do Azure
- Base de Dados SQL do Azure
- Storage do Azure

## <a name="deployment-architecture"></a>Arquitetura de implantação

A secção seguinte fornece detalhes sobre os elementos de implantação e a implementação.

### <a name="security"></a>Segurança

#### <a name="identity-and-authentication"></a>Identidade e autenticação

Este esquema garante que o acesso aos recursos está protegido por meio de serviços de gestão de identidades e diretórios. Esta arquitetura faz uso total de [identidade como o perímetro de segurança](https://docs.microsoft.com/azure/security/security-paas-deployments). 

As seguintes tecnologias fornecem capacidades de gestão no ambiente do Azure de identidade:

- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) é multi-inquilino com base na cloud diretório e identidade do serviço de gestão da Microsoft. Todos os utilizadores para a solução foram criados no Azure Active Directory, incluindo os utilizadores que acedem a base de dados SQL.
- Autenticação para o operador com acesso à aplicação web e acesso para a administração de recursos do Azure é efetuada a utilizar o Azure AD. Para obter mais informações, consulte [integrar aplicações com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- Encriptação de coluna de base de dados utiliza o Azure AD para autenticar a aplicação à base de dados do Azure SQL. Para obter mais informações, consulte [Always Encrypted: Proteger dados confidenciais na base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- O recurso de linha com acesso à aplicação web está configurado para acesso público. Para permitir a fornecedores de identidade de rede de criação da conta e a autenticação através do Active Directory ou redes sociais [do Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) podem ser integradas se necessário.
- [O Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) Deteta potenciais vulnerabilidades e contas de risco fornece recomendações para melhorar a postura de segurança de identidades da sua organização, configura as respostas automáticas para detetado ações suspeitas relacionadas com identidades da sua organização e investiga os incidentes suspeitos e tome as medidas adequadas para resolvê-los.
- [Do Azure com base em função de controlo de acesso (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) permite a gestão de acesso direcionado precisamente para o Azure. Acesso à subscrição está limitado ao administrador da subscrição e o acesso do Azure Key Vault é limitado apenas aos utilizadores que necessitam de acesso de gestão de chaves.
- Através do aproveitamento [do Azure Active Directory condicional acesso](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) os clientes podem impor controlos de segurança adicionais no acesso a aplicações ou utilizadores no seu ambiente com base em condições específicas, como localização, dispositivo, estado e início de sessão em risco.
- [O Azure DDoS Protection](https://docs.microsoft.com/azure/security/security-paas-deployments#security-advantages-of-a-paas-cloud-service-model) juntamente com práticas recomendadas de design de aplicativo, fornece a defesa contra ataques de DDoS, com a monitorização do tráfego sempre ativa e em tempo real mitigação de ataques comuns de nível de rede. Com uma arquitetura PaaS, o nível de plataforma a proteção contra DDoS é transparente para o cliente e incorporados na plataforma mas é importante observar que a responsabilidade de design de segurança de aplicação está com o cliente.

#### <a name="data-in-transit"></a>Dados em trânsito

Os dados é o trânsito de fora e entre os componentes do Azure protegidos utilizando [Transport Layer Security/Secure Sockets Layer (TLS/SSL)](https://www.microsoft.com/TrustCenter/Security/Encryption), que utiliza a criptografia simétrica, com base num segredo partilhado para encriptar as comunicações como eles viajam através da rede. Por predefinição, o tráfego de rede é protegido com TLS 1.2.

#### <a name="security-and-malware-protection"></a>Proteção de segurança e software maligno

[Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) fornece uma visão centralizada do Estado de segurança dos seus recursos do Azure. Rapidamente, pode verificar que os controlos de segurança adequados estão implementados e corretamente configurado, e pode identificar rapidamente os recursos que requerem atenção.

[O Assistente do Azure](https://docs.microsoft.com/azure/advisor/advisor-overview) é consultor da cloud personalizado que o ajuda a seguir as melhores práticas para otimizar as suas implementações do Azure. Ele analisa a sua telemetria de configuração e utilização de recursos e, em seguida, recomenda soluções que o podem ajudar a melhorar a rentabilidade, o desempenho, a elevada disponibilidade e a segurança dos seus recursos do Azure.

[O Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) é uma funcionalidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outro software malicioso. Isso por predefinição é instalado na infraestrutura subjacente para a máquina virtual de PaaS e é gerido pelo malha do Azure de forma transparente para o cliente, garantindo

### <a name="paas-services-in-this-blueprint"></a>Serviços de PaaS neste esquema

#### <a name="azure-app-service"></a>Serviço de Aplicações do Azure

Serviço de aplicações do Azure fornece um ambiente para o aplicativo web desenvolvido no Java, PHP, node. js Python, HTML de alojamento na web completamente gerida e C# sem ter de gerir infraestrutura. Ele oferece dimensionamento automático e elevada disponibilidade, suporta Windows e Linux e permite automatizada implementações a partir [do Azure DevOps](https://azure.microsoft.com/services/visual-studio-team-services/) ou de qualquer repositório baseada no Git.

Serviço de aplicações é [ISO, SOC e PCI em conformidade](https://www.microsoft.com/TrustCenter/) e pode autenticar utilizadores com [Azure Active Directory](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) ou com início de sessão social ([Google](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-google), [Facebook](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-facebook), [Twitter](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-twitter), e [autenticação da Microsoft](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-microsoft).

Básico, Standard, e os planos Premium são para cargas de trabalho de produção e executados em instâncias de Máquina Virtual dedicadas. Cada instância pode suportar várias aplicações e domínios. Aplicação também dos serviços de suporte [restrições de endereço IP](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions) para proteger o tráfego para endereços IP fidedignos se for necessário e também [geridos identidades para recursos do Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity) para uma ligação segura a outros serviços PaaS Por exemplo, [Cofre de chaves](https://azure.microsoft.com/services/key-vault/) e [base de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/). Sempre que for necessário segurança adicional o nosso plano isolado aloja as suas aplicações num ambiente privado e dedicado do Azure e é ideal para aplicações que exigem ligações seguras com sua rede no local, ou o desempenho adicional e o dimensionamento.

Este modelo implementa as seguintes funcionalidades do serviço de aplicações:

- [Padrão](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) escalão do plano de serviço de aplicações
- Serviço de aplicações de várias [blocos de implementação](https://docs.microsoft.com/azure/app-service/deploy-staging-slots): Desenvolvimento, pré-visualização, controle de qualidade, UAT e certamente produção (bloco padrão).
- [Gerido identidades para recursos do Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity) para ligar ao [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/) (Isto também pode ser utilizado para fornecer acesso a [SQL Database do Azure](https://azure.microsoft.com/services/sql-database/) 
- Integração com o [do Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-azure-web-apps) para monitorizar o desempenho
- [Registos de Diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) 
- Métrica [alertas](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) 
- [Aplicações API do Azure](https://azure.microsoft.com/services/app-service/api/) 

#### <a name="azure-sql-database"></a>Base de Dados SQL do Azure

A Base de Dados SQL é um serviço gerido de bases de dados relacionais para fins gerais do Microsoft Azure que suporta estruturas como dados relacionados, JSON, espaciais e XML. Ofertas de base de dados SQL únicas bases de dados SQL geridas, bases de dados SQL no geridas uma [conjunto elástico](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)e o SQL [instâncias geridas](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) (em pré-visualização pública). Ele fornece [desempenho dimensionável de forma dinâmica])https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers) e disponibiliza opções como [índices columnstore](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) para extreme análises e relatórios, e [OLTP dentro da memória](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory) para extremo processamento de transações. A Microsoft lida com a aplicação de patches e a atualização da base de código do SQL ininterruptamente e abstrai toda a gestão da infraestrutura subjacente.

Base de dados de SQL do Azure neste esquema

A instância de base de dados do Azure SQL usa as seguintes medidas de segurança da base de dados:

- [Regras de firewall ao nível do servidor e ao nível da base de dados](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure), ou pelo [pontos finais de serviço de rede Virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) usando [regras de rede virtual](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview).
- [Encriptação de dados transparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) ajuda a proteger a base de dados do Azure SQL e o armazém de dados do Azure contra ameaças de atividades maliciosas. Ele executa criptografia em tempo real e a descriptografia da base de dados, cópias de segurança associadas e ficheiros de registo de transação em repouso sem a necessidade de alterações à aplicação.
- [Autenticação do Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication), pode gerenciar centralmente as identidades dos utilizadores de base de dados e outros serviços da Microsoft num local central. Gerenciamento de ID central fornece um único local para gerir utilizadores de base de dados e simplifica a gestão de permissões.
- Utilização do Azure Active Directory para a administração de banco de dados
- [Registos de auditoria](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) para contas de armazenamento
- Métrica [alertas](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) para falhas de ligações de DB
- [Deteção de ameaças do SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Colunas sempre encriptadas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

### <a name="azure-storage"></a>Storage do Azure

Microsoft [armazenamento do Azure](https://azure.microsoft.com/services/storage/) é um serviço cloud gerido da Microsoft que oferece armazenamento altamente disponível, seguro, durável, dimensionável e redundante. O Armazenamento do Azure consiste no Armazenamento de blobs, no Armazenamento de ficheiros e no Armazenamento de filas.

#### <a name="azure-storage-in-this-blueprint"></a>Armazenamento do Azure nesse plano gráfico

Este modelo utiliza os seguintes componentes de armazenamento do Azure:

- [Encriptação do serviço de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 
- Permitir apenas ligações de HTTPS

#### <a name="data-at-rest"></a>Dados inativos

Por meio [Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) todos os dados escritos no armazenamento do Azure são encriptados através da encriptação AES de 256 bits, uma das codificações de bloco mais fortes disponíveis. Pode utilizar chaves de encriptação gerida pela Microsoft e o SSE ou pode utilizar [suas próprias chaves de encriptação](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

Contas de armazenamento podem estar protegidas por [pontos finais de serviço de rede Virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) usando [regras de rede virtual](https://docs.microsoft.com/azure/storage/common/storage-network-security).

Informações detalhadas sobre como proteger o armazenamento do Azure podem ser encontradas no [guia de segurança](https://docs.microsoft.com/azure/storage/common/storage-security-guide).


### <a name="secrets-management"></a>Gestão de segredos

#### <a name="azure-key-vault"></a>Azure Key Vault

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) utilizado para proteger as chaves da aplicação e os segredos para se certificar de que não estão acessíveis por terceiros. O Key Vault não se destina a ser utilizado como arquivo para palavras-passe de utilizadores. Permite-lhe criar vários contentores seguros, chamados "cofres". Estes cofres são apoiados por módulos de segurança de hardware (HSMs). Os cofres centralizam o armazenamento dos segredos das aplicações, o que ajuda a reduzir as possibilidades de perda acidental de informações de segurança. Os cofres de chaves também controlam e registam o acesso a tudo o que está armazenado nos mesmos. O Azure Key Vault pode processar pedidos e renovações de certificados Transport Layer Security (TLS), proporcionando as funcionalidades inerentes a uma solução de gestão de ciclo de vida de certificados robusta.

#### <a name="azure-key-vault-in-this-blueprint"></a>O Azure Key Vault neste esquema

- Contém a chave de acesso de armazenamento, com acesso de leitura concedido para o [identidade gerida](https://docs.microsoft.com/azure/app-service/overview-managed-identity) da aplicação web com acesso ao cliente
- Contém a palavra-passe do SQL Server DBA (no cofre separado)
- Registo de diagnósticos

### <a name="monitoring-logging-and-audit"></a>Monitorização, registo e auditoria

#### <a name="azure-monitor-logs"></a>Registos de Monitor do Azure

[Registos de Monitor do Azure](https://azure.microsoft.com/services/log-analytics/) é um serviço no Azure que ajuda a recolher e analisar dados gerados pelos recursos na sua cloud e ambientes no local.

#### <a name="azure-monitor-logs-in-this-blueprint"></a>Registos de Monitor do Azure neste esquema

- Avaliação do SQL
- Diagnóstico do Cofre de chaves
- Ligação do Application Insights
- Registo de atividades do Azure

#### <a name="application-insights"></a>Application Insights

[O Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) é um serviço de gestão de desempenho de aplicações (APM) extensível para desenvolvedores da web em várias plataformas. Utilizado para monitorizar aplicações web em direto, ele será automaticamente detetar anomalias de desempenho, analisar o desempenho, diagnosticar problemas e para compreender como os utilizadores interagem com a aplicação. O Application Insights pode ser implementado em plataformas, incluindo .NET, node. js e Java EE, alojado no local ou na cloud. Integra-se com o seu processo de DevOps e tem pontos de ligação a diversas outras ferramentas de programação.

#### <a name="application-insights-in-this-blueprint"></a>Application Insights nesse plano gráfico

Este modelo utiliza os seguintes componentes do Application Insights:

- Painel do Application Insights por site (operador, o cliente e API)

#### <a name="azure-activity-logs"></a>Registos de atividades do Azure

[Registo de atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs#what-you-can-do-with-the-activity-log) auditorias de eventos de plano de controlo para as suas subscrições. Utilizar o registo de atividades, pode determinar o "o quê, quem e quando" para quaisquer operações (PUT, POST, DELETE) efetuadas nos recursos na sua subscrição de escrita. Também é possível compreender o estado da operação e outras propriedades relevantes.

#### <a name="azure-monitor"></a>Azure Monitor

[O Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) permite a monitorização dos componentes essenciais para serviços do Azure, permitindo que a coleção de métricas, registos de atividades e os registos de diagnóstico. O Azure Monitor fornece métricas de infraestrutura de nível base e registos para a maioria dos serviços do Microsoft Azure.

## <a name="threat-model"></a>Modelo de risco

O diagrama de fluxo de dados para esta arquitetura de referência está disponível para [transferir](https://aka.ms/ukofficial-paaswa-tm) ou podem ser encontradas abaixo. Esse modelo pode ajudar os clientes a compreender os pontos do potencial risco de na infraestrutura de sistema ao fazer modificações.

![PaaS alojamento da aplicação Web para o modelo de risco de cargas de trabalho oficial do Reino Unido](images/ukofficial-paaswa-threat-model.png?raw=true "PaaS alojamento da aplicação Web para o modelo de risco de cargas de trabalho oficial do Reino Unido")

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Documentação de conformidade de princípios de segurança de Cloud NCSC

O serviço comercial Crown (uma agência de que funciona para melhorar a atividade comercial e compra pelo Governo) renovado a classificação de serviços cloud da Microsoft no âmbito enterprise para v6 G-Cloud, que abrange todas as suas ofertas ao nível OFFICIAL. Detalhes do Azure e G-Cloud podem ser encontrados no [resumo de avaliação de segurança do Azure UK G-Cloud](https://www.microsoft.com/trustcenter/compliance/uk-g-cloud).

Este esquema alinha os princípios de segurança de 14 cloud documentados no NCSC [princípios de segurança de Cloud](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) para ajudar a garantir um ambiente que suporta cargas de trabalho classificadas como oficial do Reino Unido.

O [Azure no Security and Compliance Blueprint - matriz de responsabilidade de cliente oficial do Reino Unido](https://aka.ms/ukofficial-crm) (livro do Excel) apresenta uma lista de todos os princípios de segurança de 14 cloud e denota, para cada princípio (ou subpart princípio), quer o princípio implementação é da responsabilidade da Microsoft, o cliente, ou partilhados entre os dois.

O [Azure no Security and Compliance Blueprint - aplicação Web de PaaS para a matriz de implementação de princípio oficial do Reino Unido](https://aka.ms/ukofficial-paaswa-pim) (livro do Excel) apresenta uma lista de todos os princípios de segurança de 14 cloud e denota, para cada princípio (ou subpart princípio) que é designado uma responsabilidade do cliente na matriz de responsabilidades de cliente, 1) se o plano gráfico implementa o princípio e 2) uma descrição de como a implementação se alinha com os seguintes requisitos de princípio.  

Além disso, a Cloud Security Alliance (CSA) publicado a matriz de controle de Cloud para suportar clientes na avaliação de fornecedores de serviços cloud e para identificar questões que devem ser abordadas antes de mudar para serviços cloud. Em resposta, o Microsoft Azure respondidas o questionário de iniciativa de avaliação de consenso CSA ([CSA CAIQ](https://www.microsoft.com/TrustCenter/Compliance/CSA)), que descreve como a Microsoft lida com os princípios sugeridos.

## <a name="third-party-assessment"></a>Avaliação de terceiros

Este esquema tiver sido revisto pelo do Reino Unido National Cyber Security Centre (NCSC) e alinha os princípios de segurança de Cloud de 14 NCSC

Os modelos de automatização foram testados pela equipe do Arquiteto de soluções ao Cloud do Azure de unidade do Reino Unido cliente sucesso e pelo nosso parceiro da Microsoft, [Ampliphae](http://www.ampliphae.com/).


## <a name="deploy-the-solution"></a>Implementar a solução

Esta segurança do Azure e a automação de plano gráfico de conformidade é composto por ficheiros de configuração JSON e scripts do PowerShell que são processados pelo serviço de API do Azure Resource Manager para implementar recursos no Azure. Estão disponíveis instruções de implementação detalhados [aqui](https://aka.ms/ukofficial-paaswa-repo).

Foram fornecidas três abordagens para a implementação; Um simples "expressa" [Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) adequado para a criação rápida de um teste de ambiente; um parametrizado [Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) abordagem fornecendo maior configuração em ambientes de carga de trabalho; e um portal do Azure com a base de implementação em que o operador pode especificar os parâmetros de implementação através do portal do Azure. 

1.  Clonar ou transferir [isso](https://aka.ms/ukofficial-paaswa-repo) repositório do GitHub para a estação de trabalho local.
2.  Revisão [método 1: 2 de CLI do Azure (versão Express)](https://aka.ms/ukofficial-paaswa-repo/#method-1-azure-cli-2-express-version) e execute os comandos fornecidos.
3.  Revisão [método 1a: 2 de CLI do Azure (configurar a implantação por meio de argumentos de script)](https://aka.ms/ukofficial-paaswa-repo/#method-1a-azure-cli-2-configuring-the-deployment-via-script-arguments) e execute os comandos fornecidos
4.  Revisão [método 2: Portal do Azure, o processo de implantação](https://aka.ms/ukofficial-paaswa-repo/#method-2-azure-portal-deployment-process) e execute os comandos listados

## <a name="guidance-and-recommendations"></a>Orientações e recomendações

### <a name="api-management"></a>Gestão de API

[Gestão de API do Azure](https://azure.microsoft.com/services/api-management/) poderia ser utilizado em frente o serviço de aplicações de API para fornecer camadas adicionais de segurança, limitação e controles para expor, proxy e proteger APIs.

### <a name="azure-b2c"></a>Azure B2C

[O Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) pode ser implementado como um controle para permitir que os utilizadores registem, criar uma identidade e controlo de acesso e autorização de ativar para a aplicação web pública.

## <a name="disclaimer"></a>Exclusão de Responsabilidade

- Este documento é apenas para fins informativos. A MICROSOFT FAZ NÃO OFERECE QUAISQUER GARANTIAS, EXPRESSAS, IMPLÍCITAS OU ESTATUTÁRIAS QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "como-está." Informações e opiniões expressados neste documento, incluindo URLs e outras referências de Web site da Internet, podem ser alteradas sem aviso prévio. Os clientes a ler este documento assume o risco de usá-lo.
- Este documento não fornece aos clientes quaisquer direitos de propriedade intelectual em qualquer produto da Microsoft ou soluções.
- Os clientes podem copiar e utilizar este documento para efeitos de referência interna.
- Algumas recomendações neste documento podem resultar em maior de dados, a rede ou a utilização de recursos de computação no Azure e podem aumentar os custos de licenciamento ou de subscrição do Azure num cliente.
- Esta arquitetura deve ser usado como uma base para os clientes que se ajustar às suas necessidades específicas e não devem ser utilizada como-está num ambiente de produção.
- Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos requisitos de conformidade e normas. Os clientes procuram legal suporte da sua organização em implementações do cliente aprovadas.
