---
title: Aplicativo web seguro para PCI DSS / Microsoft Docs
description: Esta aplicação de amostra implementa as melhores práticas de segurança que melhoram a sua aplicação e a postura de segurança da sua organização quando se desenvolve no Azure.
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
ms.openlocfilehash: 4ab3697824ff4a47e7b8f281b531cae610ffdc3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187584"
---
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>Desenvolver uma infraestrutura segura para uma aplicação de PCI

## <a name="overview"></a>Descrição geral

Esta infraestrutura segura para uma aplicação da Indústria de Cartões de Pagamento (PCI) fornece orientação para a implementação de uma plataforma da Indústria de Cartões de Pagamento como um ambiente de serviço (PaaS) adequado para a recolha, armazenamento e recuperação de dados do titular do cartão. Esta solução automatiza a implantação e configuração de recursos Azure para uma arquitetura de referência comum, demonstrando formas de os clientes poderem satisfazer requisitos específicos de segurança e conformidade e servirem de base para os clientes construirem e configurarem as suas próprias soluções no Azure. A solução implementa um subconjunto de requisitos semelhantes às Normas de Segurança de Dados da Indústria de Cartões de Pagamento (PCI DSS 3.2).

Esta amostra implementa automaticamente uma arquitetura de referência de aplicação web PaaS com controlos de segurança pré-configurados para ajudar os clientes a alcançar a conformidade semelhante aos requisitos do PCI DSS 3.2. A solução consiste em modelos de Gestor de Recursos Azure e scripts PowerShell que guiam a implementação e configuração de recursos.

Deve seguir os passos descritos neste artigo sequencialmente para garantir que os componentes da aplicação estão corretamente configurados. A base de dados, o Azure App Service, a instância Azure Key Vault e a instância de Gateway de Aplicação Azure dependem umas das outras.

Os scripts de implantação configuram a infraestrutura. Depois de executar os scripts de implementação, terá de fazer alguma configuração manual no portal Azure para ligar os componentes e serviços em conjunto.

Esta amostra destina-se a programadores experientes no Azure que trabalham no setor de retalho e querem construir uma app de retalho com infraestruturas Azure seguras.

> [!NOTE]
> Esta arquitetura destina-se a servir de base para que os clientes se adaptem às suas necessidades específicas e não devem ser utilizadas como está num ambiente de produção.

A implementação de uma aplicação neste ambiente sem modificações não é suficiente para satisfazer completamente os requisitos do PCI DSS 3.2. Tenha em atenção o seguinte:

- Esta arquitetura fornece uma linha de base para ajudar os clientes a usar o Azure de forma compatível com o PCI DSS 3.2.
- Os clientes são responsáveis pela realização de uma avaliação adequada da segurança e conformidade de qualquer solução construída utilizando esta arquitetura, uma vez que os requisitos podem variar em função das especificidades da implementação de cada cliente.

Ao desenvolver e implementar esta aplicação, aprende-se a:

- Crie uma instância azure key vault e guarde e recupere segredos dele.
- Implementar base de dados Azure para O SQL Azure, configurar dados seguros e autorizar o acesso aos mesmos.
- Implemente a aplicação web Azure com o ambiente do App Service, que é um isolado dedicado com acesso frontal à firewall.
- Crie e configure uma instância de Gateway de aplicação Azure com uma firewall que utilize [o Top 10 Ruleset OWASP](https://coreruleset.org/).
- Ativar a encriptação de dados em trânsito e em repouso utilizando os serviços Azure.
- Criar a Política Azure e os Projetos Azure para avaliar a conformidade.

Depois de desenvolver e implementar esta aplicação, terá configurado a seguinte aplicação web de amostra, juntamente com as medidas de configuração e segurança descritas.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes de arquitetura
A aplicação é uma aplicação típica de n-tier com três níveis. A parte frontal, a parte traseira e a camada de base de dados com componentes de monitorização e gestão secreta integrados são mostradas aqui:

![Arquitetura de aplicações](./media/secure-pci-web-app/architecture.png)

A arquitetura é constituída por estes componentes:

- [Serviço de aplicações Ambiente v2](https://docs.microsoft.com/azure/app-service/environment/intro/). Fornece o App Service Environment e equilibra a carga para a nossa arquitetura de aplicação.
- [Gateway de aplicação Azure](https://docs.microsoft.com/azure/application-gateway/). Fornece o portal e firewall para a nossa arquitetura de aplicação.
- [Insights de aplicação](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Fornece um serviço extensível de Gestão de Desempenho de Aplicações (APM) em várias plataformas.
- [Cofre de Chaves Azure.](https://docs.microsoft.com/azure/key-vault/) Armazena e encripta os segredos da nossa aplicação e gere a criação de políticas de acesso à sua volta.
- [Diretório Azure Ative](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis/). Fornece serviço de gestão de identidade e acesso baseado na nuvem, inscreva-se e aceda aos recursos.
- [Azure DNS.](https://docs.microsoft.com/azure/dns/dns-overview) Fornece o serviço para hospedar o domínio.
- [Equilíbrio de carga azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview/). Dimensiona as suas aplicações e cria alta disponibilidade para os seus serviços
- [Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction/). Fornece solução para cenários modernos de armazenamento de dados.
- [Aplicação Web Azure.](https://docs.microsoft.com/azure/app-service/overview/)  Fornece um serviço baseado em HTTP para hospedar aplicações web.
- [Base de Dados Azure para AzureSQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview/). Armazena de forma segura os dados da nossa aplicação.
- [Plantas Azure.](https://docs.microsoft.com/azure/governance/blueprints/overview/) Fornece especificações e cumprimento de determinadas normas e requisitos.
- [Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/)
- [Política Azure.](https://docs.microsoft.com/azure/governance/policy/overview) Avalia os seus recursos para o incumprimento das políticas atribuídas.

## <a name="threat-model"></a>Modelo de ameaça
A modelação de ameaças é o processo de identificação de potenciais ameaças à segurança para o seu negócio e aplicação e, em seguida, garantir que um plano de mitigação adequado está em vigor.

Esta amostra utilizou a Ferramenta de [Modelação de Ameaças](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) da Microsoft para implementar o modelamento de ameaças para a aplicação de amostra segura. Ao diagramar os componentes e os fluxos de dados, pode identificar problemas e ameaças no início do processo de desenvolvimento. Isto poupa tempo e dinheiro mais tarde.

Este é o modelo de ameaça para a aplicação de amostras:

![Modelo de ameaça](./media/secure-pci-web-app/threat-model.png)

Algumas ameaças de amostra e potenciais vulnerabilidades que a ferramenta de modelação de ameaças gera são mostradas na seguinte imagem. O modelo de ameaça dá uma visão geral da superfície de ataque exposta e leva os desenvolvedores a pensar em como mitigar os problemas.

![Saída do modelo de ameaça](./media/secure-web-app/threat-model-output.png)

Por exemplo, a injeção SQL na saída do modelo de ameaça anterior é atenuada pela sanitização das inputs dos utilizadores e utilizando funções armazenadas na Base de Dados Azure para postgreSQL. Esta mitigação impede a execução arbitrária de consultas durante leituras e escritos de dados.

Os desenvolvedores melhoram a segurança geral do sistema atenuando cada uma das ameaças na saída do modelo de ameaça.

## <a name="deployment"></a>Implementação
### <a name="prerequisites"></a>Pré-requisitos
Para que a aplicação esteja a funcionar, é necessário instalar estas ferramentas:

- Um editor de código para modificar e ver o código de aplicação. [Visual Studio Code](https://code.visualstudio.com/) é uma opção de código aberto.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest) no seu computador de desenvolvimento.
- [Git](https://git-scm.com/) no seu sistema. Git é usado para clonar o código fonte localmente.
- [jq](https://stedolan.github.io/jq/), uma ferramenta UNIX para consulta jSON de uma forma amigável para o utilizador.

Esta amostra é composta por ficheiros de configuração JSON e scripts PowerShell que são tratados pelo serviço API do Gestor de Recursos Azure para implantar recursos dentro do Azure. As instruções de implementação detalhadas estão disponíveis [aqui](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM).

#### <a name="quickstart"></a>Início Rápido

1.  Clone ou descarregue [este](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) repositório GitHub para a sua estação de trabalho local.
2.  Reveja 0-Setup-AdministrativeAccountAndPermission.md e execute os comandos fornecidos.
3.  Implementar uma solução de teste com dados da amostra Contoso ou pilotar um ambiente de produção inicial.

    Este script implementa recursos Azure para uma demonstração de uma loja web usando dados da amostra Contoso.

Neste exemplo, executa o script de implementação que implementa a aplicação web no App Service e cria os recursos.

Existem muitas formas de implementar aplicações no Azure, incluindo:

- Modelos do Azure Resource Manager
- PowerShell
- CLI do Azure
- Portal do Azure
- DevOps do Azure

## <a name="guidance-and-recommendations"></a>Orientações e recomendações

### <a name="network"></a>Rede
A arquitetura define uma Rede Virtual Privada com um espaço de endereço de 10.200.0.0.0/16.
 ![Virtual_Network](./media/secure-pci-web-app/virtual-network.png)

### <a name="network-security-groups"></a>Grupos de segurança de rede
Os gruposhttps://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) de segurança da rede contêm Listas de Controlo de Acesso (ACLs) que permitem ou negam o tráfego dentro de uma rede virtual. Os grupos de segurança da rede podem ser utilizados para proteger o tráfego a nível de subnet ou vm individual. Existem os seguintes grupos de segurança da rede:

- 1 grupo de segurança de rede para Application Gateway
- 1 grupo de segurança de rede para App Service Environment
- 1 grupo de segurança de rede para a Base de Dados Azure SQL
- 1 grupo de segurança de rede para anfitrião de bastião

Cada um dos grupos de segurança da Rede tem portas e protocolos específicos abertos para que a solução possa funcionar de forma segura e correta. Além disso, estão ativadas as seguintes configurações para cada grupo de segurança da rede:

- Registos e eventos de diagnóstico,https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) estão habilitados e armazenados numa conta de armazenamento
- Os registos do Monitor Azure estão ligados aos diagnósticos do grupo de segurança da rede.https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

### <a name="nsg-config"></a>NSG Config
O config NSG para o App Service Environment deve ser configurado como mostrado na imagem abaixo.

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

Cada subnet está associada ao seu grupo de segurança de rede correspondente.

### <a name="config"></a>Configurar
As subredes são configuradas como mostrado na imagem abaixo.
 ![Configurar](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>DNS do Azure
O Sistema de Nome de Domínio (DNS) é responsável pela tradução (ou resolução) de um website ou nome de serviço para o seu endereço IP. [O Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) é um serviço de hospedagem para domínios DNS que fornece resolução de nome utilizando a infraestrutura Azure. Ao hospedar domínios no Azure, os utilizadores podem gerir os registos DNS usando as mesmas credenciais, APIs, ferramentas e faturação como outros serviços Azure. O Azure DNS também suporta domínios Privados de DNS.

### <a name="protect-data"></a>Proteger os dados
Para ajudar a proteger os dados na nuvem, é necessário prestar contas aos possíveis estados em que os seus dados podem ocorrer e quais os controlos disponíveis para esse estado. As melhores práticas para a segurança e encriptação de dados do Azure dizem respeito aos seguintes estados de dados:

- Em repouso: Isto inclui todos os objetos de armazenamento de informação, recipientes e tipos que existem estáticamente nos meios físicos, seja em disco magnético ou ótico.
- Em trânsito: Quando os dados estão a ser transferidos entre componentes, locais ou programas, está em trânsito. Exemplos são transferências através da rede, através de um ônibus de serviço (de instalações para nuvem e vice-versa, incluindo ligações híbridas como ExpressRoute), ou durante um processo de entrada/saída.

### <a name="azure-storage"></a>Storage do Azure
Para satisfazer os dados encriptados nos requisitos de repouso, todo o [Armazenamento Azure](https://azure.microsoft.com/services/storage/) utiliza o Azure Key Vault para manter o controlo das chaves que acedem e encriptam os dados. Isto ajuda a proteger e salvaguardar os dados dos titulares do cartão de apoio aos compromissos de segurança organizacional e aos requisitos de conformidade definidos pelo PCI DSS 3.2.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
A encriptação do disco Azure aproveita a funcionalidade BitLocker do Windows para fornecer encriptação de volume para discos de dados. A solução integra-se com o Azure Key Vault para ajudar a controlar e gerir as chaves de encriptação de discos.

### <a name="azure-sql-database"></a>Base de Dados SQL do Azure
A instância de base de dados Azure SQL utiliza as seguintes medidas de segurança da base de dados:

- [A autenticação e autorização do Diretório Ativo](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permite a gestão de identidade dos utilizadores de bases de dados e de outros serviços da Microsoft numa localização central.
- A auditoria da base de [dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started/) rastreia os eventos da base de dados e escreve-os num registo de auditoria numa conta de armazenamento Azure.
- A Base de Dados Azure SQL está configurada para utilizar [encriptação de dados transparente,](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)que executa encriptação e desencriptação em tempo real da base de dados, cópias de segurança associadas e ficheiros de registo de transações para proteger a informação em repouso. A encriptação transparente de dados garante que os dados armazenados não foram sujeitos a acesso não autorizado.
- [As regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem que todos os acessos aos servidores de base de dados até que sejam concedidas permissões adequadas. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.
- [A Deteção de Ameaças SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite a deteção e resposta a potenciais ameaças à medida que ocorrem, fornecendo alertas de segurança para atividades suspeitas de bases de dados, potenciais vulnerabilidades, ataques de injeção SQL e padrões de acesso anómalos à base de dados.
- [Colunas encriptadas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que os dados sensíveis nunca aparecem como texto simples dentro do sistema de base de dados. Depois de permitir a encriptação de dados, apenas aplicações de clientes ou servidores de aplicações com acesso às teclas podem aceder a dados de texto simples.
- A base de [dados dinâmica sQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados sensíveis, mascarando os dados a utilizadores ou aplicações não privilegiados. A máscara de dados dinâmicos pode descobrir automaticamente dados potencialmente sensíveis e sugerir a aplicação das máscaras apropriadas. Isto ajuda a identificar e reduzir o acesso a dados de modo a que não saia da base de dados através de acesso não autorizado. Os clientes são responsáveis por ajustar configurações dinâmicas de máscara de dados para aderir ao seu esquema de base de dados.

### <a name="identity-management"></a>Gestão de identidades
As seguintes tecnologias fornecem capacidades para gerir o acesso aos dados do titular do cartão no ambiente Azure:

- O Azure Ative Directory é o serviço de diretório e gestão de identidade multi-inquilinos da Microsoft. Todos os utilizadores desta solução são criados no Diretório Ativo Do Azure, incluindo utilizadores que acedem à Base de Dados Azure SQL.
- A autenticação da aplicação é realizada utilizando o Diretório Ativo Azure. Para obter mais informações, veja [Integrating applications with Azure Active Directory (Integrar aplicações com o Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration). Além disso, a encriptação da coluna de dados utiliza o Diretório Ativo Azure para autenticar a aplicação na Base de Dados Azure SQL. Para mais informações, consulte como proteger dados sensíveis na Base de [Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- O controlo de acesso baseado em funções azure permite aos administradores definir permissões de acesso de grãos finos para conceder apenas a quantidade de acesso que os utilizadores precisam para desempenhar em seus trabalhos. Em vez de dar a cada utilizador permissão sem restrições para os recursos do Azure, os administradores só podem permitir certas ações de acesso aos dados dos titulares do cartão. O acesso por subscrição está limitado ao administrador de subscrição.
- A Azure Ative Directory Privileged Identity Management permite aos clientes minimizar o número de utilizadores que têm acesso a determinadas informações, como dados do titular do cartão. Os administradores podem usar a Azure Ative Directory Privileged Identity Management para descobrir, restringir e monitorizar identidades privilegiadas e o seu acesso aos recursos. Esta funcionalidade também pode ser utilizada para impor o acesso administrativo a pedido e just-in-time quando necessário.
- A Azure Ative Directory Identity Protection deteta potenciais vulnerabilidades que afetam as identidades de uma organização, configura respostas automatizadas a ações suspeitas detetadas relacionadas com as identidades de uma organização, e investiga incidentes suspeitos para tomar as medidas adequadas para resolvê-las.

### <a name="secrets-management"></a>Gestão de segredos
A solução utiliza o Cofre chave Azure para a gestão de chaves e segredos. O cofre de chave do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em nuvem e pelos serviços. As seguintes capacidades do Cofre chave Azure ajudam os clientes a proteger e aceder a esses dados:

- As políticas avançadas de acesso são configuradas numa base de necessidade.
- As políticas de acesso ao Cofre Chave são definidas com permissões mínimas necessárias a chaves e segredos.
- Todas as chaves e segredos no Cofre Chave têm datas de validade.
- Todas as chaves no Cofre chave estão protegidas por módulos de segurança de hardware especializados. O tipo chave é uma chave RSA protegida de 2048 bits HSM.
- Com o Key Vault, pode encriptar chaves e segredos (tais como chaves de autenticação, chaves de conta de armazenamento, chaves de encriptação de dados, . Ficheiros PFX e palavras-passe) utilizando chaves protegidas por módulos de segurança de hardware (HSMs)
- Utilize o RBAC para atribuir permissões a utilizadores, grupos e aplicações num determinado âmbito.
- Utilize o Key Vault para gerir os seus certificados TLS com renovação automática.
- Os registos de diagnóstico do Cofre chave estão ativados com um período de retenção de pelo menos 365 dias.
- As operações criptográficas permitidas para as teclas são restritas às necessárias.

### <a name="azure-security-center"></a>Centro de Segurança do Azure
Com o Azure Security Center, os clientes podem aplicar centralmente e gerir políticas de segurança através de cargas de trabalho, limitar a exposição a ameaças e detetar e responder a ataques. Além disso, o Azure Security Center acede às configurações existentes dos serviços Azure para fornecer recomendações de configuração e serviço para ajudar a melhorar a postura de segurança e proteger os dados.

O Azure Security Center utiliza uma variedade de capacidades de deteção para alertar os clientes de potenciais ataques direcionados para os seus ambientes. Estes alertas contêm informações valiosas sobre o que acionou o alerta, os recursos afetados e a origem do ataque. O Azure Security Center tem um conjunto de alertas de segurança predefinidos, que são desencadeados quando ocorre uma ameaça, ou atividade suspeita. As regras de alerta personalizados no Azure Security Center permitem aos clientes definir novos alertas de segurança com base em dados que já são recolhidos do seu ambiente.

O Azure Security Center fornece alertas e incidentes de segurança prioritários, tornando mais simples para os clientes descobrir e resolver potenciais problemas de segurança. Um relatório de inteligência de ameaça é gerado por cada ameaça detetada para ajudar as equipas de resposta a incidentes na investigação e remediação de ameaças.

### <a name="azure-application-gateway"></a>Gateway de Aplicação do Azure
A arquitetura reduz o risco de vulnerabilidades de segurança usando um Portal de Aplicação Azure com uma firewall de aplicação web configurada, e o conjunto de regras OWASP ativado. As capacidades adicionais incluem:

- SSL de ponta a ponta
- Desativar TLS v1.0 e v1.1
- Ativar TLSv1.2
- Firewall de aplicação web (modo de prevenção)
- Modo de prevenção com conjunto de regras OWASP 3.0
- Ativar o registo de diagnósticos
- Sondas de saúde personalizadas
- O Azure Security Center e o Azure Advisor fornecem proteção e notificações adicionais. O Azure Security Center também fornece um sistema de reputação.

### <a name="logging-and-auditing"></a>Registo e auditoria
Os serviços Azure registam extensivamente o sistema de log e a atividade do utilizador, bem como a saúde do sistema:

- [Os registos de atividade](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações realizadas sobre os recursos numa subscrição. Os registos de atividade podem ajudar a determinar o iniciador de uma operação, a hora da ocorrência e o estado.
- [Os registos](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) de diagnóstico incluem todos os registos emitidos por cada recurso. Estes registos incluem registos do sistema de eventos windows, registos de armazenamento azure, registos de auditoria key vault e acesso de Gateway de aplicação e registos de Firewall. Todos os registos de diagnóstico escrevem para uma conta de armazenamento Azure centralizada e encriptada para arquivo. A retenção é configurável pelo utilizador, até 730 dias, para satisfazer os requisitos de retenção específicos da organização.

### <a name="azure-monitor-logs"></a>Registos do Azure Monitor
Estes registos são consolidados em [registos do Monitor Azure](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e reporte de dashboards. Uma vez recolhidos, os dados são organizados em tabelas separadas para cada tipo de dados dentro dos espaços de trabalho do Log Analytics, o que permite que todos os dados sejam analisados em conjunto, independentemente da sua origem original. Além disso, o Azure Security Center integra-se com os registos do Azure Monitor, permitindo aos clientes utilizarem consultas kusto para aceder em dados dos seus eventos de segurança e combiná-lo com dados de outros serviços.

As [seguintes soluções](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) de monitorização azure estão incluídas como parte desta arquitetura:

- [Avaliação](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)do Diretório Ativo : A solução Ative Directory Health Check avalia o risco e a saúde dos ambientes dos servidores num intervalo regular e fornece uma lista prioritária de recomendações específicas à infraestrutura de servidores implementadas.
- [Avaliação SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução SQL Health Check avalia o risco e a saúde dos ambientes do servidor num intervalo regular e fornece aos clientes uma lista prioritária de recomendações específicas da infraestrutura de servidores implementadas.
- [Agente Saúde](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução Agent Health informa quantos agentes são destacados e a sua distribuição geográfica, bem como quantos agentes não respondem e o número de agentes que estão a enviar dados operacionais.
- [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução Activity Log Analytics ajuda na análise dos registos de atividade do Azure em todas as subscrições do Azure para um cliente.

### <a name="azure-monitor"></a>Azure Monitor
[O Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os utilizadores a rastrear o desempenho, a manter a segurança e a identificar tendências, permitindo às organizações auditar, criar alertas e arquivar dados, incluindo rastrear chamadas de API nos seus recursos Azure.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) é um serviço extensível de Gestão de Desempenho de Aplicações para desenvolvedores web em várias plataformas. Aplicação Insights deteta anomalias de desempenho e os clientes podem usá-lo para monitorizar a aplicação web ao vivo. Inclui ferramentas de análise poderosas para ajudar os clientes a diagnosticar problemas e a entender o que os utilizadores realmente fazem com a sua aplicação. É projetado para ajudar os clientes a melhorar continuamente o desempenho e a usabilidade.

### <a name="azure-key-vault"></a>Azure Key Vault
Crie um cofre para a organização para armazenar chaves e manter a responsabilidade por tarefas operacionais como abaixo:

- Os dados armazenados no Cofre de Chaves incluem:

   - Chave de informação de aplicação
   - Chave de acesso ao armazenamento de dados
   - Cadeia de ligação
   - Nome da tabela de dados
   - Credenciais de utilizador

- As políticas avançadas de acesso são configuradas numa base de necessidade
- As políticas de acesso ao Cofre Chave são definidas com permissões mínimas necessárias a chaves e segredos
- Todas as chaves e segredos no Cofre Chave têm datas de validade
- Todas as teclas no Cofre chave estão protegidas por HSM [Tipo chave = Chave Protegida HSM 2048-bit RSA chave]
- Todos os utilizadores/identidades recebem permissões mínimas necessárias utilizando o Controlo de Acesso Baseado em Funções (RBAC)
- As aplicações não partilham um Cofre chave a menos que confiem uns nos outros e precisam de acesso aos mesmos segredos em tempo de execução
- Os registos de diagnóstico do Cofre chave estão ativados com um período de retenção de pelo menos 365 dias.
- As operações criptográficas permitidas para as chaves são restritas às necessárias

### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute
Um túnel VPN seguro ou [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) precisa de ser configurado para estabelecer uma ligação segura aos recursos implantados como parte desta arquitetura de referência de aplicação web PaaS. Ao configurar adequadamente uma VPN ou ExpressRoute, os clientes podem adicionar uma camada de proteção para dados em trânsito.

Através da implementação de um túnel VPN seguro com o Azure, pode ser criada uma ligação privada virtual entre uma rede no local e uma Rede Virtual Azure. Esta ligação ocorre através da Internet e permite que os clientes "túneis" de forma segura dentro de uma ligação encriptada entre a rede do cliente e o Azure. O Site-to-Site VPN é uma tecnologia segura e madura que tem sido implementada por empresas de todos os tamanhos durante décadas. O modo de túnel IPsec é usado nesta opção como um mecanismo de encriptação.

Uma vez que o tráfego dentro do túnel VPN atravessa a Internet com uma VPN site-to-site, a Microsoft oferece outra opção de ligação ainda mais segura. Azure ExpressRoute é uma ligação WAN dedicada entre o Azure e um local no local ou um fornecedor de hospedagem exchange. Como as ligações ExpressRoute não passam pela Internet, estas ligações oferecem mais fiabilidade, velocidades mais rápidas, latências mais baixas e maior segurança do que as ligações típicas através da Internet. Além disso, por se trata de uma ligação direta do fornecedor de telecomunicações do cliente, os dados não viajam pela Internet e, por conseguinte, não estão expostos a ele.

Estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)as melhores práticas para a implementação de uma rede híbrida segura que alarga uma rede no local ao Azure.

## <a name="cost-considerations"></a>Considerações de custos
Se ainda não tem uma conta Azure, pode criar uma gratuita. Vá à página de [conta gratuita](https://azure.microsoft.com/free/) para começar, veja o que pode fazer com uma conta Azure gratuita e saiba quais os produtos gratuitos durante 12 meses.

Para implementar os recursos na aplicação de amostracom as funcionalidades de segurança, precisa de pagar algumas funcionalidades premium. À medida que as escalas de aplicações e os níveis e testes gratuitos oferecidos pelo Azure precisam de ser atualizados para satisfazer os requisitos de aplicação, os seus custos podem aumentar. Utilize a [calculadora](https://azure.microsoft.com/pricing/calculator/) de preços Azure para estimar os seus custos.

## <a name="next-steps"></a>Passos seguintes
Os seguintes artigos podem ajudá-lo a projetar, desenvolver e implementar aplicações seguras.

- [Design](secure-design.md)
- [Programar](secure-develop.md)
- [Implementar](secure-deploy.md)
