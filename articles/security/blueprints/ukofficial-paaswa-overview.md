---
title: Hospedagem de aplicativos Web de Blueprint de Segurança e Conformidade do Azure PaaS para cargas de trabalho oficiais do Reino Unido
description: Hospedagem de aplicativos Web de Blueprint de Segurança e Conformidade do Azure PaaS para cargas de trabalho oficiais do Reino Unido
services: security
author: jomolesk
ms.assetid: 446105ad-a863-44f5-a964-6ead1dac4787
ms.service: security
ms.topic: article
ms.date: 07/13/2018
ms.author: jomolesk
ms.openlocfilehash: c0163b5280de942491f2174aa371fa7cc83d5984
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946523"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-hosting-for-uk-official-workloads"></a>Esquema de Segurança e Conformidade do Azure: Hospedagem de aplicativos Web de PaaS para cargas de trabalho oficiais do Reino Unido

## <a name="azure-security-and-compliance-blueprints"></a>Segurança do Azure e Esquemas de Conformidade

Os planos gráficos do Azure consistem em documentos de orientação e modelos de automação que implantam arquiteturas baseadas em nuvem para oferecer soluções a cenários com requisitos de certificação ou conformidade. Os planos gráficos do Azure são coleções de modelos de automação e diretrizes que permitem aos clientes de Microsoft Azure acelerar a entrega de suas metas de negócios por meio do provisionamento de uma arquitetura de fundação que pode ser estendida para atender a quaisquer requisitos adicionais.

## <a name="overview"></a>Descrição geral

Este Blueprint de Segurança e Conformidade do Azure fornece orientações e scripts de automação para fornecer uma arquitetura de aplicativo web hospedada de [PaaS (plataforma como serviço)](https://azure.microsoft.com/overview/what-is-paas/) Microsoft Azure apropriada para lidar com cargas de trabalho classificadas como o [oficial do Reino Unido ](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/715778/May-2018_Government-Security-Classifications-2.pdf). Essa classificação de segurança abrange a maioria das informações criadas ou processadas pelo setor público. Isso inclui serviços e operações de negócios rotineiras, que, se forem perdidos, roubados ou publicados na mídia, alguns dos quais poderiam ter conseqüências prejudiciais. O perfil típico de ameaças para a classificação oficial é muito semelhante a uma empresa privada que fornece informações e serviços valiosos. O Reino Unido prevê a necessidade de defender os dados ou serviços do governo do Reino Unido contra ameaças ou comprometimento por invasores com recursos e recursos limitados, como (mas não se limitando a) hactivists, grupos de pressão de emissão única, jornalistas de investigação, hackers individuais competente e a maioria de indivíduos e grupos criminais.

Este projeto foi revisado pelo NCSC (Centro Nacional de segurança) do Reino Unido e alinha-se aos princípios de segurança de nuvem do NCSC 14.

A arquitetura usa componentes de [plataforma como serviço](https://azure.microsoft.com/overview/what-is-paas/) do Azure para fornecer um ambiente que permite aos clientes evitar a despesa e a complexidade de comprar licenças de software, de gerenciar a infraestrutura de aplicativo subjacente e middleware ou o ferramentas de desenvolvimento e outros recursos. Os clientes gerenciam os aplicativos e serviços que eles desenvolvem, concentrando-se no fornecimento de valor comercial, enquanto Microsoft Azure gerencia os outros recursos do Azure, como máquinas virtuais, armazenamento e rede, trazendo mais da [divisão de responsabilidade](../fundamentals/paas-deployments.md) pelo gerenciamento de infraestrutura na plataforma do Azure. Os [serviços de Azure app](https://azure.microsoft.com/services/app-service/) oferecem dimensionamento automático, alta disponibilidade, suporte para Windows e Linux e permite implantações automatizadas do GitHub, do Azure DevOps ou de qualquer repositório Git como serviços padrão. Com o uso dos serviços de aplicativos, os desenvolvedores podem se concentrar em fornecer valor comercial sem a sobrecarga de gerenciar a infraestrutura. É possível criar Greenfield novos aplicativos Java, PHP, Node. js, Python, HTML ou C# Web, ou também para migrar aplicativos Web locais ou de nuvem existentes para serviços de Azure app (embora a auditoria detalhada e os testes para confirmar o desempenho sejam obrigatório).

Este projeto se concentra no provisionamento de uma plataforma de base segura [como uma](https://azure.microsoft.com/overview/what-is-paas/) interface baseada na Web de serviço para usuários públicos e também de Back-Office. Este cenário de design Blueprint considera o uso de serviços hospedados na Web do Azure, em que um usuário público pode enviar, exibir e gerenciar dados confidenciais com segurança; Além disso, um operador back office ou governamental pode processar com segurança os dados confidenciais que o usuário público enviou. Os casos de uso para esse cenário podem incluir:

- Um usuário que envia um retorno de imposto, com um operador governamental processando o envio;
- Um usuário solicitando um serviço por meio de um aplicativo baseado na Web, com um usuário de Back Office Validando e entregando o serviço; or
- Um usuário que procura e exibe informações de ajuda de domínio público sobre um serviço governamental.

Usando modelos de [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) e scripts da interface de linha de comando do Azure, o plano gráfico implanta um ambiente que se alinha aos [princípios de segurança de nuvem](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) do Reino Unido (ncsc) 14 e ao centro da Internet [Controles de segurança críticos](https://www.cisecurity.org/critical-controls.cfm)de CIS (segurança). O NCSC recomenda que os princípios de segurança de nuvem sejam usados pelos clientes para avaliar as propriedades de segurança do serviço e para ajudar a entender a divisão de responsabilidade entre o cliente e o fornecedor. A Microsoft forneceu informações sobre cada um desses princípios para ajudar a entender melhor a divisão de responsabilidades. Essa arquitetura e os modelos de Azure Resource Manager correspondentes têm suporte no White paper da Microsoft, [14 controles de segurança na nuvem para a nuvem do Reino Unido usando Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Essa arquitetura foi revisada pelo NCSC e se alinha com os princípios de segurança de nuvem do Reino Unido 14, permitindo que as organizações do setor público acompanhem rapidamente sua capacidade de atender às obrigações de conformidade usando serviços baseados em nuvem globalmente e no Reino Unido no Microsoft Azure nuvem. Este modelo implanta a infraestrutura para a carga de trabalho. O código do aplicativo e a camada de negócios de suporte e o software da camada de dados devem ser instalados e configurados pelos clientes. Instruções de implantação detalhadas estão disponíveis [aqui](https://aka.ms/ukofficial-paaswa-repo/).

Este projeto é uma arquitetura básica. Nossos clientes podem usar esse plano gráfico como base para suas cargas de trabalho de classificação oficial e expandir os modelos e recursos com seus próprios requisitos. Este projeto se baseia nos princípios do [projeto de aplicativos Web IaaS-Official de três camadas do Reino Unido](https://aka.ms/ukofficial-iaaswa) para oferecer aos nossos clientes opções de implementação de [IaaS (infraestrutura como serviço)](https://azure.microsoft.com/overview/what-is-iaas/) e PaaS para hospedar cargas de trabalho baseadas na Web.

Para implantar esse plano gráfico, é necessária uma assinatura do Azure. Se você não tiver uma assinatura do Azure, poderá se inscrever de forma rápida e fácil sem nenhum encargo: Introdução ao Azure. Clique [aqui](https://aka.ms/ukofficial-paaswa-repo/) para obter instruções de implantação.

## <a name="architecture-and-components"></a>Arquitetura e componentes

Este projeto fornece uma solução de Hospedagem de aplicativo Web em um ambiente de nuvem do Azure que dá suporte a cargas de trabalho oficiais do Reino Unido. A arquitetura fornece um ambiente seguro que aproveita os recursos de plataforma como serviço do Azure. No ambiente, dois aplicativos Web do serviço de aplicativo são implantados (um para usuários públicos e outro para usuários de Back Office), com uma camada de aplicativo de API para fornecer os serviços corporativos para o front-end da Web. Um banco de dados SQL do Azure é implantado como um repositório de dados relacional gerenciado para o aplicativo. A conectividade com esses componentes de fora da plataforma e entre todos esses componentes é criptografada por meio do TLS 1,2 para garantir a privacidade dos dados em transporte, com o acesso autenticado pelo Azure Active Directory.

![Hospedagem de aplicativos Web de PaaS para cargas de trabalho oficiais do Reino Unido diagrama de arquitetura](images/ukofficial-paaswa-architecture.png?raw=true "Hospedagem de aplicativos Web de PaaS para cargas de trabalho oficiais do Reino Unido diagrama de arquitetura")

Como parte da arquitetura de implantação, o provisionamento de armazenamento seguro, o monitoramento & registro em log, o gerenciamento de segurança unificado & proteção avançada contra ameaças e recursos de gerenciamento também são implantados para garantir que os clientes tenham todas as ferramentas necessárias para Proteja e monitore seu ambiente para essa solução.

Essa solução usa os seguintes serviços do Azure. Os detalhes da arquitetura de implantação estão na seção [arquitetura de implantação](#deployment-architecture) .

- Azure Active Directory
- Serviço de Aplicações
- Aplicação Web
- Aplicação API
- DNS do Azure
- Cofre de Chaves
- Azure Monitor (logs)
- Application Insights
- Azure Resource Manager
- Centro de Segurança do Azure
- Base de Dados SQL do Azure
- Storage do Azure

## <a name="deployment-architecture"></a>Arquitetura de implantação

A seção a seguir detalha os elementos de implantação e implementação.

### <a name="security"></a>Segurança

#### <a name="identity-and-authentication"></a>Identidade e autenticação

Esse plano gráfico garante que o acesso aos recursos seja protegido por meio dos serviços de gerenciamento de identidade e diretório. Essa arquitetura faz uso completo da [identidade como o perímetro de segurança](../fundamentals/paas-deployments.md). 

As tecnologias a seguir fornecem recursos de gerenciamento de identidade no ambiente do Azure:

- O [Azure Active Directory (AD do Azure)](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. Todos os usuários da solução foram criados no Azure Active Directory, incluindo usuários que acessam o banco de dados SQL.
- A autenticação para o aplicativo Web voltado para o operador e o acesso à administração dos recursos do Azure é executada usando o Azure AD. Para obter mais informações, consulte [integrando aplicativos com Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).
- Criptografia de coluna de banco de dados usa o Azure AD para autenticar o aplicativo no banco de dados SQL do Azure. Para obter mais informações, [consulte Always Encrypted: Proteger dados confidenciais no banco](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)de dado SQL.
- O aplicativo Web voltado para o cidadão está configurado para acesso público. Para permitir a criação de contas e a autenticação por meio do Active Directory ou provedores de identidade de rede social [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) podem ser integrados, se necessário.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detecta possíveis vulnerabilidades e contas arriscadas fornece recomendações para aprimorar a postura de segurança das identidades de sua organização, configura as respostas automatizadas para detectadas suspeitas ações relacionadas às identidades da sua organização e investiga incidentes suspeitos e executa a ação apropriada para resolvê-los.
- O [RBAC (controle de acesso baseado em função) do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) permite o gerenciamento de acesso precisamente focalizado para o Azure. O acesso à assinatura é limitado ao administrador da assinatura e Azure Key Vault acesso é restrito apenas aos usuários que precisam de acesso de gerenciamento de chaves.
- Por meio do aproveitamento Azure Active Directory os clientes de [acesso condicional](../../active-directory/active-directory-conditional-access-azure-portal.md) podem impor controles de segurança adicionais sobre o acesso a aplicativos ou usuários em seu ambiente com base em condições específicas, como localização, dispositivo, estado e risco de entrada.
- A [proteção contra DDoS do Azure](../fundamentals/paas-deployments.md#security-advantages-of-a-paas-cloud-service-model) combinada com as práticas recomendadas de design de aplicativos, fornece defesa contra ataques de DDoS, com monitoramento de tráfego AlwaysOn e mitigação em tempo real de ataques comuns em nível de rede. Com uma arquitetura PaaS, a proteção contra DDoS de nível de plataforma é transparente para o cliente e incorporada à plataforma, mas é importante observar que a responsabilidade do design de segurança do aplicativo está no cliente.

#### <a name="data-in-transit"></a>Dados em trânsito

Os dados são transmitidos de fora e entre os componentes do Azure são protegidos usando [TLS/protocolo SSL (segurança da camada de transporte)](https://www.microsoft.com/TrustCenter/Security/Encryption), que usa a criptografia simétrica com base em um segredo compartilhado para criptografar as comunicações conforme elas trafegam pela rede. Por padrão, o tráfego de rede é protegido usando o TLS 1,2.

#### <a name="security-and-malware-protection"></a>Proteção contra malware e segurança

A [central de segurança do Azure](https://azure.microsoft.com/services/security-center/) fornece uma exibição centralizada do estado de segurança de todos os seus recursos do Azure. Em um relance, você pode verificar se os controles de segurança apropriados estão em vigor e configurados corretamente, e pode identificar rapidamente todos os recursos que exigem atenção.

O [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) é um consultor de nuvem personalizado que ajuda você a seguir as práticas recomendadas para otimizar suas implantações do Azure. Ele analisa a sua telemetria de configuração e utilização de recursos e, em seguida, recomenda soluções que o podem ajudar a melhorar a rentabilidade, o desempenho, a elevada disponibilidade e a segurança dos seus recursos do Azure.

[O Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) é um recurso de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados. Isso por padrão é instalado na infraestrutura de máquina virtual de PaaS subjacente e é gerenciado pela malha do Azure de forma transparente para o cliente.

### <a name="paas-services-in-this-blueprint"></a>Serviços de PaaS nesta especificação técnica

#### <a name="azure-app-service"></a>Serviço de Aplicações do Azure

Azure App serviço fornece um ambiente de hospedagem na Web totalmente gerenciado para aplicativos Web desenvolvidos em Java, PHP, Node. js Python, C# HTML e sem precisar gerenciar a infraestrutura. Ele oferece dimensionamento automático e alta disponibilidade, dá suporte ao Windows e ao Linux e permite implantações automatizadas do [Azure DevOps](https://azure.microsoft.com/services/visual-studio-team-services/) ou de qualquer repositório baseado em git.

O serviço de aplicativo é [compatível com ISO, SoC e PCI](https://www.microsoft.com/TrustCenter/) e pode autenticar usuários com [Azure Active Directory](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) ou com logon social ([Google](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-google), [Facebook](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-facebook), [Twitter](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-twitter)e [autenticação da Microsoft](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-microsoft).

Os planos básico, Standard e Premium são para cargas de trabalho de produção e são executados em instâncias de máquina virtual dedicadas. Cada instância pode dar suporte a vários aplicativos e domínios. Os serviços de aplicativos também dão suporte a [restrições de endereço IP](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions) para proteger o tráfego para endereços IP confiáveis, se necessário e também [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity) para conexão segura com outros serviços de PaaS, como [Key Vault](https://azure.microsoft.com/services/key-vault/) e [SQL do Azure Banco de dados](https://azure.microsoft.com/services/sql-database/). Em que a segurança adicional é necessária, nosso plano isolado hospeda seus aplicativos em um ambiente privado dedicado do Azure e é ideal para aplicativos que exigem conexões seguras com sua rede local, ou desempenho e escala adicionais.

Este modelo implanta os seguintes recursos do serviço de aplicativo:

- [Padrão](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) Camada do plano do serviço de aplicativo
- Vários slots de [implantação](https://docs.microsoft.com/azure/app-service/deploy-staging-slots)do serviço de aplicativo: Dev, Preview, QA, UAT e, é claro, Production (slot padrão).
- [Identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity) para se conectar ao [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) (isso também pode ser usado para fornecer acesso ao [banco de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) 
- Integração com o [aplicativo Azure](../../azure-monitor/app/azure-web-apps.md) insights para monitorar o desempenho
- [Registos de Diagnóstico](../../azure-monitor/platform/diagnostic-logs-overview.md) 
- [Alertas](../../azure-monitor/app/alerts.md) de métrica 
- [Aplicações API do Azure](https://azure.microsoft.com/services/app-service/api/) 

#### <a name="azure-sql-database"></a>Base de Dados SQL do Azure

A Base de Dados SQL é um serviço gerido de bases de dados relacionais para fins gerais do Microsoft Azure que suporta estruturas como dados relacionados, JSON, espaciais e XML. O SQL Database oferece bancos de dados SQL únicos gerenciados, bancos de dados SQL gerenciados em um [pool elástico](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)e [instâncias gerenciadas](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) do SQL (em visualização pública). Proporciona [desempenho dimensionável de forma dinâmica](../../sql-database/sql-database-purchase-models.md) e disponibiliza opções como [índices columnstore](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) para análises e relatórios aprofundados e [OLTP](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory) dentro da memória para processamento transacional avançado. A Microsoft lida com a aplicação de patches e a atualização da base de código do SQL ininterruptamente e abstrai toda a gestão da infraestrutura subjacente.

Banco de dados SQL do Azure neste projeto

A instância do banco de dados SQL do Azure usa as seguintes medidas de segurança de banco de dados:

- Regras de firewall no nível de [servidor e de banco de dados](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)ou por meio de [pontos de extremidade de serviço de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) usando [regras de rede virtual](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview).
- A Transparent [Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) ajuda a proteger o banco de dados SQL do Azure e o Azure data warehouse contra a ameaça de atividades mal-intencionadas. Ele executa criptografia e descriptografia em tempo real do banco de dados, backups associados e arquivos de log de transações em repouso, sem a necessidade de alterações no aplicativo.
- [Autenticação do Azure ad](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication), você pode gerenciar centralmente as identidades dos usuários do banco de dados e outros serviços da Microsoft em um local central. O gerenciamento de identificação central fornece um único local para gerenciar usuários de banco de dados e simplifica o gerenciamento de permissões.
- Uso de Azure Active Directory para administração de banco de dados
- [Logs de auditoria](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) para contas de armazenamento
- [Alertas](../../azure-monitor/app/alerts.md) de métrica para conexões de BD com falha
- [Detecção de ameaças do SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Colunas Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

### <a name="azure-storage"></a>Storage do Azure

O [armazenamento do Microsoft Azure](https://azure.microsoft.com/services/storage/) é um serviço de nuvem gerenciado pela Microsoft que fornece armazenamento altamente disponível, seguro, durável, escalonável e redundante. O Armazenamento do Azure consiste no Armazenamento de blobs, no Armazenamento de ficheiros e no Armazenamento de filas.

#### <a name="azure-storage-in-this-blueprint"></a>Armazenamento do Azure neste projeto

Este modelo usa os seguintes componentes do armazenamento do Azure:

- [Criptografia do Serviço de Armazenamento](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 
- Permitir somente conexões HTTPS

#### <a name="data-at-rest"></a>Dados inativos

Por meio do [criptografia do serviço de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) todos os dados gravados no armazenamento do Azure são criptografados por meio da criptografia AES de 256 bits, uma das codificações de bloco mais fortes disponíveis. Você pode usar as chaves de criptografia gerenciadas pela Microsoft com a SSE ou pode usar [suas próprias chaves de criptografia](../../storage/common/storage-encryption-keys-portal.md).

As contas de armazenamento podem ser protegidas por meio de [pontos de extremidade de serviço de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) usando [regras de rede virtual](https://docs.microsoft.com/azure/storage/common/storage-network-security).

Informações detalhadas sobre como proteger o armazenamento do Azure podem ser encontradas no [Guia de segurança](https://docs.microsoft.com/azure/storage/common/storage-security-guide).


### <a name="secrets-management"></a>Gerenciamento de segredos

#### <a name="azure-key-vault"></a>Azure Key Vault

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) é usado para proteger chaves de aplicativo e segredos para garantir que eles não sejam acessíveis por terceiros. O Key Vault não se destina a ser utilizado como arquivo para palavras-passe de utilizadores. Ele permite que você crie vários contêineres seguros, chamados cofres. Estes cofres são apoiados por módulos de segurança de hardware (HSMs). Os cofres centralizam o armazenamento dos segredos das aplicações, o que ajuda a reduzir as possibilidades de perda acidental de informações de segurança. Os cofres de chaves também controlam e registam o acesso a tudo o que está armazenado nos mesmos. O Azure Key Vault pode processar pedidos e renovações de certificados Transport Layer Security (TLS), proporcionando as funcionalidades inerentes a uma solução de gestão de ciclo de vida de certificados robusta.

#### <a name="azure-key-vault-in-this-blueprint"></a>Azure Key Vault neste projeto

- Mantém a chave de acesso de armazenamento, com acesso de leitura concedido à [identidade gerenciada](https://docs.microsoft.com/azure/app-service/overview-managed-identity) do aplicativo Web voltado para o cliente
- Contém a senha do DBA SQL Server (em um cofre separado)
- Log de diagnóstico

### <a name="monitoring-logging-and-audit"></a>Monitoramento, registro em log e auditoria

#### <a name="azure-monitor-logs"></a>Registos do Azure Monitor

[Os logs de Azure monitor](https://azure.microsoft.com/services/log-analytics/) são um serviço no Azure que ajuda a coletar e analisar dados gerados pelos recursos em seus ambientes de nuvem e locais.

#### <a name="azure-monitor-logs-in-this-blueprint"></a>Logs de Azure Monitor neste projeto

- Avaliação SQL
- Diagnóstico de Key Vault
- Application Insights conexão
- Registo de atividades do Azure

#### <a name="application-insights"></a>Application Insights

O [Application insights](../../azure-monitor/app/app-insights-overview.md) é um serviço de gerenciamento de desempenho de aplicativos (APM) extensível para desenvolvedores da Web em várias plataformas. Usado para monitorar aplicativos Web dinâmicos, ele detectará automaticamente anomalias de desempenho, analisará o desempenho, diagnosticará problemas e entenderá como os usuários interagem com o aplicativo. Application Insights podem ser implantadas em plataformas, incluindo .NET, Node. js e Java EE, hospedadas localmente ou na nuvem. Integra-se com o seu processo de DevOps e tem pontos de ligação a diversas outras ferramentas de programação.

#### <a name="application-insights-in-this-blueprint"></a>Application Insights neste projeto

Este modelo usa os seguintes componentes de Application Insights:

- Painel de Application Insights por site (operador, cliente e API)

#### <a name="azure-activity-logs"></a>Logs de atividades do Azure

O [log de atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) audita eventos de plano de controle para suas assinaturas. Usando o log de atividades, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, excluir) tomada nos recursos em sua assinatura. Também é possível compreender o estado da operação e outras propriedades relevantes.

#### <a name="azure-monitor"></a>Azure Monitor

O [Azure monitor](../../azure-monitor/overview.md) permite o monitoramento de núcleo para os serviços do Azure, permitindo a coleta de métricas, logs de atividade e logs de diagnóstico. O Azure Monitor fornece métricas de infraestrutura de nível base e registos para a maioria dos serviços do Microsoft Azure.

## <a name="threat-model"></a>Modelo de ameaça

O diagrama de fluxo de dados para esta arquitetura de referência está disponível para [Download](https://aka.ms/ukofficial-paaswa-tm) ou pode ser encontrado abaixo. Esse modelo pode ajudar os clientes a entender os pontos de risco potencial na infraestrutura do sistema ao fazer modificações.

![Hospedagem de aplicativos Web de PaaS para o modelo de risco de cargas de trabalho oficiais do Reino Unido](images/ukofficial-paaswa-threat-model.png?raw=true "Hospedagem de aplicativos Web de PaaS para o modelo de risco de cargas de trabalho oficiais do Reino Unido")

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Documentação de conformidade dos princípios de segurança do NCSC Cloud

O serviço comercial de coroa (uma agência que trabalha para melhorar a atividade comercial e de compras pelo governo) renovou a classificação dos serviços de nuvem corporativa em escopo da Microsoft para o G-Cloud V6, cobrindo todas as suas ofertas no nível oficial. Os detalhes do Azure e do G-Cloud podem ser encontrados no [Resumo da avaliação de segurança do Azure UK G-Cloud](https://www.microsoft.com/trustcenter/compliance/uk-g-cloud).

Este projeto se alinha aos 14 princípios de segurança de nuvem documentados nos princípios de [segurança de nuvem](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) do ncsc para ajudar a garantir um ambiente que ofereça suporte a cargas de trabalho classificadas como o oficial do Reino Unido.

O [Blueprint de segurança e conformidade do Azure-matriz de responsabilidade do cliente oficial do Reino Unido](https://aka.ms/ukofficial-crm) (pasta de trabalho do Excel) lista todos os 14 princípios de segurança de nuvem e as notas, para cada princípio (ou subitem de princípio), se a implementação do princípio é a responsabilidade da Microsoft, do cliente ou compartilhado entre os dois.

O [aplicativo Web Blueprint de segurança e conformidade do Azure-PaaS para a matriz de implementação do princípio oficial do Reino Unido](https://aka.ms/ukofficial-paaswa-pim) (pasta de trabalho do Excel) lista todos os 14 princípios de segurança de nuvem e as notas, para cada princípio (ou subitem de princípio) que é designado como um responsabilidade do cliente na matriz de responsabilidades do cliente, 1) se o plano gráfico implementar o princípio e 2) uma descrição de como a implementação se alinha com os requisitos de princípio.  

Além disso, a CSA (Cloud Security Alliance) publicou a matriz de controle de nuvem para dar suporte aos clientes na avaliação de provedores de nuvem e para identificar perguntas que devem ser respondidas antes de passar para os serviços de nuvem. Em resposta, Microsoft Azure respondeu ao questionário da iniciativa de avaliação de consenso do CSA ([CSA CAIQ](https://www.microsoft.com/TrustCenter/Compliance/CSA)), que descreve como a Microsoft aborda os princípios sugeridos.

## <a name="third-party-assessment"></a>Avaliação de terceiros

Este projeto foi revisado pelo NCSC (Centro Nacional de segurança) do Reino Unido e alinha os princípios de segurança de nuvem do NCSC 14

Os modelos de automação foram testados pela equipe de sucesso do cliente do Reino Unido e pelo nosso parceiro da Microsoft, [Ampliphae](https://www.ampliphae.com/).


## <a name="deploy-the-solution"></a>Implementar a solução

Essa Blueprint de Segurança e Conformidade do Azure automação é composta por arquivos de configuração JSON e scripts do PowerShell que são manipulados pelo serviço de API do Azure Resource Manager para implantar recursos no Azure. Instruções de implantação detalhadas estão disponíveis [aqui](https://aka.ms/ukofficial-paaswa-repo).

Três abordagens foram fornecidas para implantação; Um simples "Express" [CLI do Azure 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) adequado para criar rapidamente um ambiente de teste; uma abordagem com parâmetros de [CLI do Azure 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) que fornece maior configuração para ambientes de carga de trabalho; e uma implantação baseada em portal do Azure em que o operador pode especificar os parâmetros de implantação por meio do portal do Azure. 

1.  Clone ou baixe [este](https://aka.ms/ukofficial-paaswa-repo) repositório GitHub em sua estação de trabalho local.
2.  Examine [o método 1: CLI do Azure 2 (versão expressa)](https://aka.ms/ukofficial-paaswa-repo/#method-1-azure-cli-2-express-version) e executar os comandos fornecidos.
3.  Examinar [o método 1a: CLI do Azure 2 (Configurando a implantação por meio](https://aka.ms/ukofficial-paaswa-repo/#method-1a-azure-cli-2-configuring-the-deployment-via-script-arguments) de argumentos de script) e executar os comandos fornecidos
4.  Examinar [método 2: Portal do Azure processo](https://aka.ms/ukofficial-paaswa-repo/#method-2-azure-portal-deployment-process) de implantação e executar os comandos listados

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações

### <a name="api-management"></a>Gestão de API

O [Gerenciamento de API do Azure](https://azure.microsoft.com/services/api-management/) pode ser usado na frente do serviço de aplicativo de API para fornecer camadas adicionais de segurança, limitação e controles para expor, proxy e proteger APIs.

### <a name="azure-b2c"></a>B2C do Azure

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) pode ser implementado como um controle para permitir que os usuários se registrem, criem uma identidade e habilitem a autorização e o controle de acesso para o aplicativo Web público.

## <a name="disclaimer"></a>Exclusão de Responsabilidade

- Este documento é apenas para fins informativos. A MICROSOFT NÃO OFERECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU ESTATUTÁRIA, QUANTO ÀS INFORMAÇÕES CONTIDAS NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e as exibições expressas neste documento, incluindo URLs e outras referências de site da Internet, podem ser alteradas sem aviso prévio. Os clientes que lêem este documento trazem o risco de usá-lo.
- Este documento não fornece aos clientes nenhum direito legal sobre qualquer propriedade intelectual em qualquer produto ou solução da Microsoft.
- Os clientes podem copiar e usar este documento para fins de referência interna.
- Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure, e podem aumentar os custos de assinatura ou de licença do Azure do cliente.
- Essa arquitetura destina-se a servir como base para os clientes se ajustarem aos requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção.
- Este documento é desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender a requisitos e regulamentos específicos de conformidade. Os clientes devem buscar o suporte legal de sua organização em implementações aprovadas do cliente.
