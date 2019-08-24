---
title: Proteger o aplicativo Web para PCI DSS | Microsoft Docs
description: Este aplicativo de exemplo implementa práticas recomendadas de segurança que melhoram seu aplicativo e a postura de segurança de sua organização quando você desenvolve no Azure.
keywords: na
services: security
documentationcenter: na
author: fehase
manager: rkarlin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: terrylan
ms.openlocfilehash: 4fe612db65d985be2f1f1c81d03c3ee735c03889
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992617"
---
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>Desenvolver uma infraestrutura segura para um aplicativo PCI

## <a name="overview"></a>Descrição geral

Essa infraestrutura segura para um aplicativo de PCI (setor de cartão de pagamento) fornece orientação para a implantação de um ambiente de PaaS (plataforma como serviço) do setor de cartão de pagamento adequado para a coleta, o armazenamento e a recuperação de dados de titulares de cartões. Essa solução automatiza a implantação e a configuração de recursos do Azure para uma arquitetura de referência comum, demonstrando maneiras nas quais os clientes podem atender aos requisitos específicos de segurança e conformidade e serve como base para os clientes criarem e configure suas próprias soluções no Azure. A solução implementa um subconjunto de requisitos semelhantes a padrões de segurança de dados do setor de cartão de pagamento (PCI DSS 3,2).

Este exemplo implanta automaticamente uma arquitetura de referência de aplicativo Web de PaaS com controles de segurança pré-configurados para ajudar os clientes a obter conformidade semelhante aos requisitos de PCI DSS 3,2. A solução consiste em modelos de Azure Resource Manager e scripts do PowerShell que guiam a implantação e a configuração de recursos.

Você deve seguir as etapas descritas neste artigo sequencialmente para garantir que os componentes do aplicativo estejam configurados corretamente. O banco de dados, Azure App serviço, Azure Key Vault instância e Aplicativo Azure instância de gateway dependem uns dos outros.

Os scripts de implantação configuram a infraestrutura. Depois de executar os scripts de implantação, você precisará fazer alguma configuração manual no portal do Azure para vincular os componentes e serviços juntos.

Este exemplo destina-se a desenvolvedores experientes no Azure que trabalham no setor de varejo e desejam criar um aplicativo de varejo com a infraestrutura do Azure segura.

> [!NOTE]
> Essa arquitetura destina-se a servir como base para os clientes se ajustarem aos requisitos específicos e não devem ser usados no estado em que se encontram em um ambiente de produção.

Implantar um aplicativo nesse ambiente sem modificação não é suficiente para atender completamente aos requisitos do PCI DSS 3,2. Tenha em atenção o seguinte:

- Essa arquitetura fornece uma linha de base para ajudar os clientes a usar o Azure de uma maneira compatível com o PCI DSS 3,2.
- Os clientes são responsáveis por realizar a avaliação apropriada de segurança e conformidade de qualquer solução criada usando essa arquitetura, pois os requisitos podem variar com base nas especificidades da implementação de cada cliente.

Ao desenvolver e implantar este aplicativo, você aprende a:

- Crie uma instância do Azure Key Vault e armazene e recupere os segredos dela.
- Implantar o banco de dados do Azure para SQL do Azure, configurar o acesso a eles e autorizar a ti.
- Implante o aplicativo Web do Azure com o ambiente do serviço de aplicativo, que é um dedicado isolado com aEcess de firewall de front-end.
- Crie e configure uma instância de gateway Aplicativo Azure com um firewall que usa o [OWASP 10 principais RuleSet](https://coreruleset.org/).
- Habilite a criptografia de dados em trânsito e em repouso usando os serviços do Azure.
- Configurar a política do Azure e as impressões azuis para avaliar as conformidades

Depois de desenvolver e implantar esse aplicativo, você terá configurado o exemplo de aplicativo Web a seguir junto com as medidas de configuração e segurança descritas.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura
O aplicativo é um aplicativo típico de n camadas com três camadas. O front-end, back-end e a camada de banco de dados com componentes de monitoramento e gerenciamento de segredo integrados são mostrados aqui:

![Arquitetura de aplicações](./media/secure-pci-web-app/architecture.png)

A arquitetura consiste nesses componentes:

- [Ambiente do serviço de aplicativo v2](https://docs.microsoft.com/azure/app-service/environment/intro/). Fornece o Ambiente do Serviço de Aplicativo e o balanceador de carga para nossa arquitetura de aplicativo.
- [Aplicativo Azure gateway](https://docs.microsoft.com/azure/application-gateway/). Fornece o gateway e o firewall para nossa arquitetura de aplicativo.
- [Application insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Fornece um serviço de gerenciamento de desempenho de aplicativos (APM) extensível em várias plataformas.
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/). Armazena e criptografa os segredos de nosso aplicativo e gerencia a criação de políticas de acesso em relação a eles.
- [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis/). Fornece identidade baseada em nuvem e serviço de gerenciamento de acesso, entrada e recursos de acesso.
- [DNS do Azure](https://docs.microsoft.com/azure/dns/dns-overview). Fornece o serviço para hospedar o domínio.
- [Balanceador de Carga do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview/). Dimensiona seus aplicativos e cria alta disponibilidade para seus serviços
- [Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction/). Fornece solução para cenários de armazenamento de dados modernos.
- [Aplicação Web do Azure](https://docs.microsoft.com/azure/app-service/overview/).  Fornece um serviço baseado em HTTP para hospedar aplicativos Web.
- [Banco de dados do Azure para AzureSQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview/). Armazena com segurança os dados do aplicativo.
- [Plantas do Azure](https://docs.microsoft.com/azure/governance/blueprints/overview/). Fornece especificações e conformidade com determinados padrões e requisitos.
- [Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/)
- [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview). Avalia seus recursos para não conformidade com políticas atribuídas.

## <a name="threat-model"></a>Modelo de ameaça
A modelagem de ameaças é o processo de identificar possíveis ameaças à segurança para seus negócios e aplicativos e, em seguida, garantir que um plano de mitigação adequado esteja em vigor.

Este exemplo usou o [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) para implementar a modelagem de ameaças para o aplicativo de exemplo seguro. Ao diagramar os componentes e os fluxos de dados, você pode identificar problemas e ameaças no início do processo de desenvolvimento. Isso poupa tempo e dinheiro mais tarde.

Este é o modelo de ameaça para o aplicativo de exemplo:

![Modelo de ameaça](./media/secure-pci-web-app/threat-model.png)

Algumas ameaças de exemplo e possíveis vulnerabilidades que a ferramenta de modelagem de ameaças gera são mostradas na captura de tela a seguir. O modelo de ameaça fornece uma visão geral da superfície de ataque exposta e solicita que os desenvolvedores pensem sobre como mitigar os problemas.

![Saída do modelo de ameaça](./media/secure-web-app/threat-model-output.png)

Por exemplo, a injeção de SQL na saída do modelo de ameaça anterior é atenuada com a limpeza de entradas do usuário e o uso de funções armazenadas no banco de dados do Azure para PostgreSQL. Essa mitigação impede a execução arbitrária de consultas durante leituras e gravações de dados.

Os desenvolvedores melhoram a segurança geral do sistema, reduzindo cada uma das ameaças na saída do modelo de ameaça.

## <a name="deployment"></a>Implementação
### <a name="prerequisites"></a>Pré-requisitos
Para colocar o aplicativo em funcionamento, você precisa instalar essas ferramentas:

- Um editor de código para modificar e exibir o código do aplicativo. [Visual Studio Code](https://code.visualstudio.com/) é uma opção de código-fonte aberto.
- [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest) em seu computador de desenvolvimento.
- [Git](https://git-scm.com/) em seu sistema. O Git é usado para clonar o código-fonte localmente.
- [JQ](https://stedolan.github.io/jq/), uma ferramenta UNIX para consultar o JSON de maneira amigável.

Este exemplo é composto por arquivos de configuração JSON e scripts do PowerShell que são manipulados pelo serviço de API do Azure Resource Manager para implantar recursos no Azure. Instruções de implantação detalhadas estão disponíveis [aqui](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM).

#### <a name="quickstart"></a>Início Rápido

1.  Clone ou baixe [este](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) repositório GitHub em sua estação de trabalho local.
2.  Examine o 0-Setup-AdministrativeAccountAndPermission.md e execute os comandos fornecidos.
3.  Implante uma solução de teste com dados de exemplo da Contoso ou pilote um ambiente de produção inicial.

    Esse script implanta recursos do Azure para uma demonstração de uma loja da Web usando dados de exemplo da contoso.

Neste exemplo, você executa o script de implantação que implanta o aplicativo Web no serviço de aplicativo e cria os recursos.

Há várias maneiras de implantar aplicativos no Azure, incluindo:

- Modelos do Azure Resource Manager
- PowerShell
- CLI do Azure
- Portal do Azure
- DevOps do Azure

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações

### <a name="network"></a>Rede
A arquitetura define uma rede virtual privada com um espaço de endereço de 10.200.0.0/16.
 ![Virtual_Network](./media/secure-pci-web-app/virtual-network.png)

### <a name="network-security-groups"></a>Grupos de segurança de rede
Grupos de segurança de https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) rede (que contêm ACLs (listas de controle de acesso) que permitem ou negam o tráfego em uma rede virtual. Os grupos de segurança de rede podem ser usados para proteger o tráfego em uma sub-rede ou nível de VM individual. Existem os seguintes grupos de segurança de rede:

- 1 grupo de segurança de rede para o gateway de aplicativo
- 1 grupo de segurança de rede para Ambiente do Serviço de Aplicativo
- 1 grupo de segurança de rede para o banco de dados SQL do Azure
- 1 grupo de segurança de rede para host bastião

Cada um dos grupos de segurança de rede tem portas e protocolos específicos abertos para que a solução possa funcionar de forma segura e correta. Além disso, as seguintes configurações estão habilitadas para cada grupo de segurança de rede:

- Logs de diagnóstico e eventos https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) (estão habilitados e armazenados em uma conta de armazenamento
- Os logs de Azure Monitor estão conectados ao diagnóstico do grupo de segurança de rede (https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

### <a name="nsg-config"></a>Configuração do NSG
A configuração do NSG para Ambiente do Serviço de Aplicativo deve ser configurada conforme mostrado na imagem abaixo.

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

Cada sub-rede é associada ao seu grupo de segurança de rede correspondente.

### <a name="config"></a>Configuração
As sub-redes são configuradas conforme mostrado na imagem abaixo.
 ![Configuração](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>DNS do Azure
O DNS (sistema de nomes de domínio) é responsável por converter (ou resolver) um site ou nome de serviço para seu endereço IP. O [DNS do Azure](https://docs.microsoft.com/azure/dns/dns-overview) é um serviço de hospedagem para domínios DNS que fornece resolução de nomes usando a infraestrutura do Azure. Ao hospedar domínios no Azure, os usuários podem gerenciar registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que outros serviços do Azure. O DNS do Azure também dá suporte a domínios DNS privados.

### <a name="protect-data"></a>Proteger os dados
Para ajudar a proteger os dados na nuvem, você precisa considerar os possíveis Estados em que os dados podem ocorrer e quais controles estão disponíveis para esse estado. As práticas recomendadas para segurança e criptografia de dados do Azure estão relacionadas aos seguintes Estados de dados:

- Em repouso: Isso inclui todos os objetos de armazenamento de informações, contêineres e tipos que existem estaticamente em mídia física, seja em disco magnético ou óptico.
- Em trânsito: Quando os dados estão sendo transferidos entre componentes, locais ou programas, eles estão em trânsito. Os exemplos são transferidos pela rede, em um barramento de serviço (do local para a nuvem e vice-versa, incluindo conexões híbridas, como ExpressRoute), ou durante um processo de entrada/saída.

### <a name="azure-storage"></a>Storage do Azure
Para atender aos requisitos de dados criptografados em repouso, todo o [armazenamento do Azure](https://azure.microsoft.com/services/storage/) usa Azure Key Vault para manter o controle das chaves que acessam e criptografam os dados. Isso ajuda a proteger e proteger os dados do titular do cartão para dar suporte a compromissos de segurança organizacional e requisitos de conformidade definidos pelo PCI DSS 3,2.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption aproveita o recurso BitLocker do Windows para fornecer criptografia de volume para discos de dados. A solução se integra com Azure Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

### <a name="azure-sql-database"></a>Base de Dados SQL do Azure
A instância do banco de dados SQL do Azure usa as seguintes medidas de segurança de banco de dados:

- [Active Directory autenticação e autorização](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permitem o gerenciamento de identidades de usuários de banco de dados e outros serviços da Microsoft em um local central.
- A [auditoria do banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started/) rastreia eventos de banco de dados e os grava em um log de auditoria em uma conta de armazenamento do Azure.
- O banco de dados SQL do Azure está configurado para usar a Transparent [Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que executa criptografia e descriptografia em tempo real do banco dados, backups associados e arquivos de log de transações para proteger informações em repouso. A Transparent Data Encryption fornece garantia de que os dados armazenados não estão sujeitos a acesso não autorizado.
- [As regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem todo o acesso a servidores de banco de dados até que sejam concedidas permissões adequadas A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
- A [detecção de ameaças do SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite a detecção e a resposta a ameaças potenciais à medida que elas ocorrem, fornecendo alertas de segurança para atividades suspeitas de banco de dados, vulnerabilidades potenciais, ataques de injeção de SQL e padrões de acesso de banco de dados anormais
- As [colunas criptografadas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que os dados confidenciais nunca apareçam como texto sem formatação no sistema de banco de dados. Depois de habilitar a criptografia de dados, somente aplicativos cliente ou servidores de aplicativos com acesso às chaves podem acessar dados de texto sem formatação.
- O mascaramento de [dados dinâmicos do SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais mascarando os dados para usuários ou aplicativos sem privilégios. A máscara de dados dinâmicos pode descobrir dados potencialmente confidenciais automaticamente e sugerir que as máscaras apropriadas sejam aplicadas. Isso ajuda a identificar e reduzir o acesso a dados de modo que ele não saia do banco por acesso não autorizado. Os clientes são responsáveis por ajustar as configurações de mascaramento de dados dinâmicos para aderir ao seu esquema de banco de dado.

### <a name="identity-management"></a>Gestão de identidades
As tecnologias a seguir fornecem recursos para gerenciar o acesso aos dados do titular do cartão no ambiente do Azure:

- Azure Active Directory é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. Todos os usuários desta solução são criados no Azure Active Directory, incluindo usuários que acessam o banco de dados SQL do Azure.
- A autenticação para o aplicativo é executada usando Azure Active Directory. Para obter mais informações, consulte [integrando aplicativos com Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration). Além disso, a criptografia de coluna de banco de dados usa Azure Active Directory para autenticar o aplicativo no banco de dados SQL do Azure. Para obter mais informações, confira [como proteger dados confidenciais no Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- O controle de acesso baseado em função do Azure permite que os administradores definam permissões de acesso refinadas para conceder apenas a quantidade de acesso que os usuários precisam para executar seus trabalhos. Em vez de fornecer a cada usuário permissão irrestrita para recursos do Azure, os administradores podem permitir apenas determinadas ações para acessar dados de titulares de cartão. O acesso à assinatura é limitado ao administrador da assinatura.
- Azure Active Directory Privileged Identity Management permite que os clientes minimizem o número de usuários que têm acesso a determinadas informações, como dados de titulares de cartão. Os administradores podem usar Azure Active Directory Privileged Identity Management para descobrir, restringir e monitorar identidades com privilégios e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor acesso administrativo sob demanda e Just-in-time quando necessário.
- Azure Active Directory Identity Protection detecta possíveis vulnerabilidades que afetam as identidades de uma organização, configura as respostas automatizadas para detectar ações suspeitas relacionadas às identidades de uma organização e investiga suspeitas incidentes para executar a ação apropriada para resolvê-los.

### <a name="secrets-management"></a>Gerenciamento de segredos
A solução usa Azure Key Vault para o gerenciamento de chaves e segredos. O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços. Os recursos de Azure Key Vault a seguir ajudam os clientes a proteger e acessar esses dados:

- As políticas de acesso avançadas são configuradas de acordo com a necessidade.
- Key Vault políticas de acesso são definidas com as permissões mínimas necessárias para chaves e segredos.
- Todas as chaves e segredos em Key Vault têm datas de expiração.
- Todas as chaves em Key Vault são protegidas por módulos de segurança de hardware especializados. O tipo de chave é uma chave RSA de 2048 bits protegida por HSM.
- Com o Key Vault, você pode criptografar chaves e segredos (como chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados,. Arquivos PFX e senhas) usando chaves que são protegidas por HSMs (módulos de segurança de hardware)
- Use o RBAC para atribuir permissões a usuários, grupos e aplicativos em um determinado escopo.
- Use Key Vault para gerenciar seus certificados TLS com renovação automática.
- Os logs de diagnóstico para Key Vault estão habilitados com um período de retenção de pelo menos 365 dias.
- As operações de criptografia permitidas para chaves são restritas às necessárias.

### <a name="azure-security-center"></a>Centro de Segurança do Azure
Com a central de segurança do Azure, os clientes podem aplicar e gerenciar centralmente políticas de segurança entre cargas de trabalho, limitar a exposição a ameaças e detectar e responder a ataques. Além disso, a central de segurança do Azure acessa as configurações existentes dos serviços do Azure para fornecer recomendações de configuração e de serviço para ajudar a melhorar a postura de segurança e proteger os dados.

A central de segurança do Azure usa uma variedade de recursos de detecção para alertar os clientes sobre possíveis ataques visando seus ambientes. Estes alertas contêm informações valiosas sobre o que acionou o alerta, os recursos afetados e a origem do ataque. A central de segurança do Azure tem um conjunto de alertas de segurança predefinidos, que são disparados quando ocorre uma ameaça ou atividade suspeita. As regras de alerta personalizadas na central de segurança do Azure permitem que os clientes definam novos alertas de segurança com base nos dados que já foram coletados de seu ambiente.

A central de segurança do Azure fornece alertas e incidentes de segurança priorizados, tornando mais simples para os clientes descobrir e resolver problemas potenciais de segurança. Um relatório de inteligência contra ameaças é gerado para cada ameaça detectada para ajudar as equipes de resposta a incidentes na investigação e na correção de ameaças.

### <a name="azure-application-gateway"></a>Gateway de Aplicação do Azure
A arquitetura reduz o risco de vulnerabilidades de segurança usando um gateway de Aplicativo Azure com um firewall de aplicativo Web configurado e o conjunto de regras OWASP habilitado. Os recursos adicionais incluem:

- SSL de ponta a ponta
- Desabilitar TLS v 1.0 e v 1.1
- Habilitar o TLSv 1.2
- Firewall do aplicativo Web (modo de prevenção)
- Modo de prevenção com conjunto de regras OWASP 3,0
- Ativar o registo de diagnósticos
- Investigações de integridade personalizadas
- A central de segurança do Azure e o Azure Advisor fornecem proteção e notificações adicionais. A central de segurança do Azure também fornece um sistema de reputação.

### <a name="logging-and-auditing"></a>Registro em log e auditoria
Os serviços do Azure registram extensivamente a atividade do sistema e do usuário, bem como a integridade do sistema:

- [Os logs de atividade](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações executadas nos recursos em uma assinatura. Os logs de atividades podem ajudar a determinar o iniciador de uma operação, a hora de ocorrência e o status.
- Os [logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os logs emitidos por cada recurso. Esses logs incluem logs do sistema de eventos do Windows, logs de armazenamento do Azure, Key Vault logs de auditoria e acesso do gateway de aplicativo e logs de firewall. Todos os logs de diagnóstico gravam em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. A retenção é configurável pelo usuário, até 730 dias, para atender aos requisitos de retenção específicos da organização.

### <a name="azure-monitor-logs"></a>Registos do Azure Monitor
Esses logs são consolidados em [logs de Azure monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e relatórios de Dashboard. Depois de coletados, os dados são organizados em tabelas separadas para cada tipo de dados dentro de Log Analytics espaços de trabalho, o que permite que todos os dados sejam analisados juntos, independentemente de sua fonte original. Além disso, a central de segurança do Azure integra-se aos logs de Azure Monitor, permitindo que os clientes usem consultas Kusto para acessar seus dados de eventos de segurança e combiná-los com dados de outros serviços.

As seguintes [soluções de monitoramento](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) do Azure estão incluídas como parte dessa arquitetura:

- [Avaliação do Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de verificação de integridade Active Directory avalia o risco e a integridade dos ambientes de servidor em um intervalo regular e fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de verificação de integridade do SQL avalia o risco e a integridade dos ambientes de servidor em um intervalo regular e fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Integridade do agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução Integridade do Agente relata Quantos agentes são implantados e sua distribuição geográfica, bem como Quantos agentes não respondem e o número de agentes que estão enviando dados operacionais.
- [Análise do log de atividades](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução Análise do Log de Atividades auxilia na análise dos logs de atividade do Azure em todas as assinaturas do Azure para um cliente.

### <a name="azure-monitor"></a>Azure Monitor
[Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a controlar o desempenho, manter a segurança e identificar tendências, permitindo que as organizações façam auditoria, criem alertas e arquivem dados, incluindo o acompanhamento de chamadas de API em seus recursos do Azure.

### <a name="application-insights"></a>Application Insights
O [Application insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) é um serviço de gerenciamento de desempenho de aplicativos extensível para desenvolvedores da Web em várias plataformas. Application Insights detecta anomalias de desempenho e os clientes podem usá-lo para monitorar o aplicativo Web em tempo real. Ele inclui ferramentas de análise poderosas para ajudar os clientes a diagnosticar problemas e entender o que os usuários realmente fazem com seus aplicativos. Ele foi projetado para ajudar os clientes a melhorar continuamente o desempenho e a usabilidade.

### <a name="azure-key-vault"></a>Azure Key Vault
Crie um cofre para a organização no qual armazenar chaves e mantenha a responsabilidade de tarefas operacionais, como a seguir:

- Os dados armazenados no Key Vault incluem:

   - Chave do Application insights
   - Chave de acesso de armazenamento de dados
   - Cadeia de ligação
   - Nome da tabela de dados
   - Credenciais de Utilizador

- As políticas de acesso avançadas são configuradas de acordo com a necessidade
- Key Vault políticas de acesso são definidas com as permissões mínimas necessárias para chaves e segredos
- Todas as chaves e segredos no Key Vault têm datas de expiração
- Todas as chaves no Key Vault são protegidas pelo HSM [tipo de chave = chave RSA de 2048 bits protegida por HSM]
- Todos os usuários/identidades recebem as permissões mínimas necessárias usando o RBAC (controle de acesso baseado em função)
- Os aplicativos não compartilham um Key Vault, a menos que eles confiem um do outro e precisem acessar os mesmos segredos no tempo de execução
- Os logs de diagnóstico para Key Vault estão habilitados com um período de retenção de pelo menos 365 dias.
- As operações de criptografia permitidas para chaves são restritas às necessárias

### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute
Um túnel VPN seguro ou o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) precisa ser configurado para estabelecer com segurança uma conexão com os recursos implantados como parte dessa arquitetura de referência do aplicativo Web PaaS. Ao configurar adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

Ao implementar um túnel VPN seguro com o Azure, uma conexão privada virtual entre uma rede local e uma rede virtual do Azure pode ser criada. Essa conexão ocorre pela Internet e permite aos clientes "encapsular" informações com segurança dentro de um link criptografado entre a rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura, que foi implantada por empresas de todos os tamanhos por décadas. O modo de encapsulamento IPsec é usado nessa opção como um mecanismo de criptografia.

Como o tráfego dentro do túnel VPN atravessa a Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O Azure ExpressRoute é um link WAN dedicado entre o Azure e um local ou um provedor de hospedagem do Exchange. Como as conexões do ExpressRoute não passam pela Internet, essas conexões oferecem mais confiabilidade, velocidades mais rápidas, latências menores e mais segurança do que as conexões típicas pela Internet. Além disso, como essa é uma conexão direta do provedor de telecomunicação do cliente, os dados não viajam pela Internet e, portanto, não são expostos a ele.

As práticas recomendadas para implementar uma rede híbrida segura que estende uma rede local para o Azure estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="cost-considerations"></a>Considerações de custos
Se você ainda não tiver uma conta do Azure, poderá criar uma gratuita. Acesse a [página conta gratuita](https://azure.microsoft.com/free/) para começar, veja o que você pode fazer com uma conta gratuita do Azure e saiba quais produtos são gratuitos por 12 meses.

Para implantar os recursos no aplicativo de exemplo com os recursos de segurança, você precisa pagar por alguns recursos premium. Conforme o aplicativo é dimensionado e as camadas e avaliações gratuitas oferecidas pelo Azure precisam ser atualizadas para atender aos requisitos do aplicativo, seus custos podem aumentar. Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) do Azure para estimar seus custos.

## <a name="next-steps"></a>Passos Seguintes
Os artigos a seguir podem ajudá-lo a projetar, desenvolver e implantar aplicativos seguros.

- [Desenvolver](secure-design.md)
- [Programar](secure-develop.md)
- [Implementar](secure-deploy.md)
