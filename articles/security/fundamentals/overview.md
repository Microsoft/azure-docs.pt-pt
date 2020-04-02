---
title: Introdução à segurança do Azure Microsoft Docs
description: Saiba mais sobre a Segurança Azure, os seus serviços e como funciona.
services: security
documentationcenter: na
author: UnifyCloud
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2019
ms.author: TomSh
ms.openlocfilehash: a3b738d95b5b777f1cf0329fb3c1bc3e2860421e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545977"
---
# <a name="introduction-to-azure-security"></a>Introdução à segurança do Azure
## <a name="overview"></a>Descrição geral
Sabemos que a segurança é o trabalho um na nuvem e como é importante que encontre informações precisas e oportunas sobre a segurança do Azure. Uma das melhores razões para utilizar o Azure para as suas aplicações e serviços é tirar partido da sua vasta gama de ferramentas e capacidades de segurança. Estas ferramentas e capacidades ajudam a tornar possível criar soluções seguras na plataforma Segura Azure. O Microsoft Azure fornece confidencialidade, integridade e disponibilidade de dados dos clientes, ao mesmo tempo que permite uma responsabilização transparente.

Este artigo fornece um olhar abrangente sobre a segurança disponível com o Azure.

### <a name="azure-platform"></a>Plataforma Azure
O Azure é uma plataforma pública de serviço na nuvem que suporta uma ampla seleção de sistemas operativos, linguagens de programação, quadros, ferramentas, bases de dados e dispositivos. Pode executar contentores Linux com integração Docker; construir aplicativos com JavaScript, Python, .NET, PHP, Java e Node.js; construir back-ends para dispositivos iOS, Android e Windows.

Os serviços de nuvem pública Azure suportam as mesmas tecnologias que milhões de desenvolvedores e profissionais de TI já confiam e confiam. Quando você constrói, ou migra ativos de TI para, um fornecedor público de serviços na nuvem você está confiando nas capacidades dessa organização para proteger suas aplicações e dados com os serviços e os controles que eles fornecem para gerir a segurança dos seus ativos baseados na nuvem.

A infraestrutura da Azure é projetada de instalações para aplicações para hospedagem de milhões de clientes simultaneamente, e fornece uma base confiável sobre a qual as empresas podem satisfazer os seus requisitos de segurança.

Além disso, o Azure oferece-lhe um vasto leque de opções de segurança configuráveis e a capacidade de controlá-las para que possa personalizar a segurança para satisfazer os requisitos únicos das implementações da sua organização. Este documento ajuda-o a entender como as capacidades de segurança do Azure podem ajudá-lo a cumprir estes requisitos.

> [!Note]
> O foco principal deste documento é nos controlos virados para o cliente que pode utilizar para personalizar e aumentar a segurança das suas aplicações e serviços.
>
> Para obter informações sobre como a Microsoft assegura a própria plataforma Azure, consulte a segurança da [infraestrutura Azure.](infrastructure.md)

## <a name="summary-of-azure-security-capabilities"></a>Resumo das capacidades de segurança do Azure

### <a name="features-to-secure-the-azure-platform"></a>Funcionalidades para proteger a plataforma Azure
As seguintes funcionalidades são capacidades que pode rever para garantir que a Plataforma Azure é gerida de forma segura. Foram fornecidas ligações para uma nova avaliação sobre a forma como a Microsoft aborda as questões de confiança dos clientes em quatro áreas: plataforma segura, controlos de privacidade &, conformidade e transparência.

| [Plataforma Segura](https://www.microsoft.com/trustcenter/Security/default.aspx)  | [Controlos de & de Privacidade](https://www.microsoft.com/trustcenter/Privacy/default.aspx)  |[Conformidade](https://www.microsoft.com/trustcenter/Compliance/default.aspx)   | [Transparência](https://www.microsoft.com/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [Ciclo de Desenvolvimento de Segurança](https://www.microsoft.com/sdl/), Auditorias Internas | [Gerencie sempre os seus dados](https://www.microsoft.com/trustcenter/Privacy/You-own-your-data) | [Centro de Fidedignidade](https://www.microsoft.com/trustcenter/default.aspx) |[Como a Microsoft assegura os dados dos clientes nos serviços do Azure](https://www.microsoft.com/trustcenter/Transparency/default.aspx) |
| [Formação obrigatória de segurança, verificação de antecedentes](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) |  [Controlo da localização dos dados](https://www.microsoft.com/trustcenter/Privacy/Where-your-data-is-located) |  [Centro de ControloS Comuns](https://www.microsoft.com/trustcenter/Common-Controls-Hub) |[Como a Microsoft gere a localização de dados nos serviços do Azure](https://azuredatacentermap.azurewebsites.net/)|
| [Teste de penetração,](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) [deteção de intrusões, DDoS,](https://www.microsoft.com/trustcenter/Security/ThreatManagement) [Auditorias & exploração](https://www.microsoft.com/trustcenter/Security/AuditingAndLogging) | [Fornecer acesso de dados nos seus termos](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [A lista de verificação de due diligence dos serviços da nuvem](https://www.microsoft.com/trustcenter/Compliance/Due-Diligence-Checklist) |[Quem na Microsoft pode aceder aos seus dados em que termos](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [Centro de dados de última geração,](https://www.microsoft.com/cloud-platform/global-datacenters)segurança física, [Rede Segura](network-overview.md) | [Responder à aplicação da lei](https://www.microsoft.com/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [Conformidade por serviço, localização & Indústria](https://www.microsoft.com/trustcenter/Compliance/default.aspx) |[Como a Microsoft assegura os dados dos clientes nos serviços do Azure](https://www.microsoft.com/trustcenter/Transparency/default.aspx)|
|  [Resposta a Incidentede Segurança](https://aka.ms/SecurityResponsepaper), [Responsabilidade Partilhada](https://aka.ms/sharedresponsibility) |[Rigorosos padrões de privacidade](https://www.microsoft.com/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Rever certificação para serviços Azure, Centro transparência](https://www.microsoft.com/trustcenter/Compliance/default.aspx)|

### <a name="features-to-secure-data-and-application"></a>Funcionalidades para proteger dados e aplicação
Dependendo do modelo de serviço na nuvem, existe a responsabilidade variável para quem é responsável pela gestão da segurança da aplicação ou serviço. Existem capacidades disponíveis na Plataforma Azure para o ajudar a cumprir estas responsabilidades através de funcionalidades incorporadas, e através de soluções parceiras que podem ser implementadas numa subscrição do Azure.

As capacidades incorporadas estão organizadas em seis áreas funcionais: Operações, Aplicações, Armazenamento, Networking, Compute e Identidade. Detalhes adicionais sobre as funcionalidades e capacidades disponíveis na Plataforma Azure nestas seis áreas são fornecidos através de informações sumárias.

## <a name="operations"></a>Operações
Esta secção fornece informações adicionais sobre as principais funcionalidades nas operações de segurança e informações sumárias sobre estas capacidades.

### <a name="security-and-audit-dashboard"></a>Painel de Segurança e Auditoria
A [solução de Segurança e Auditoria](../../security-center/security-center-intro.md) proporciona uma visão abrangente da postura de segurança de TI da sua organização com consultas de pesquisa [incorporadas](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/) para questões notáveis que requerem a sua atenção. O dashboard [de Segurança e Auditoria](https://technet.microsoft.com/library/mt484091.aspx) é o ecrã principal de tudo o que está relacionado com a segurança nos registos do Monitor Azure. Fornece informações de alto nível sobre o estado de segurança dos seus computadores. Também inclui a capacidade de ver todos os eventos das últimas 24 horas, 7 dias ou qualquer outro intervalo de tempo personalizado.

Além disso, pode configurar a Segurança & Compliance para [realizar automaticamente ações específicas](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/) quando um evento específico é detetado.

### <a name="azure-resource-manager"></a>Azure Resource Manager
[O Azure Resource Manager](../../azure-resource-manager/management/deployment-models.md) permite-lhe trabalhar com os recursos da sua solução como grupo. Pode implementar, atualizar ou eliminar todos os recursos da sua solução numa operação única e coordenada. Você usa um modelo de Gestor de [Recursos Azure](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/) para implantação e esse modelo pode funcionar para diferentes ambientes, tais como testes, encenação e produção. O Resource Manager fornece funcionalidades de segurança, auditoria e etiquetagem para o ajudar a gerir os recursos após a implementação.

As implementações baseadas no modelo do Gestor de Recursos Azure ajudam a melhorar a segurança das soluções implementadas no Azure porque as configurações padrão de controlo de segurança e podem ser integradas em implementações padronizadas baseadas em modelos. Isto reduz o risco de erros de configuração de segurança que podem ocorrer durante as implementações manuais.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/) é um serviço extensível de Gestão de Desempenho de Aplicações (APM) para desenvolvedores web. Com os Insights da Aplicação, pode monitorizar as suas aplicações web ao vivo e detetar automaticamente anomalias de desempenho. Inclui ferramentas de análise poderosas para ajudá-lo a diagnosticar problemas e a entender o que os utilizadores realmente fazem com as suas apps. Monitoriza a sua aplicação durante todo o tempo em que está a funcionar, tanto durante os testes como depois de a publicar ou implementar.

Application Insights cria gráficos e tabelas que mostram, por exemplo, em que horas do dia obtém a maioria dos utilizadores, quão responsiva é a app, e quão bem é servida por quaisquer serviços externos de que depende.

Se houver falhas, falhas ou problemas de desempenho, pode pesquisar os dados da telemetria em detalhe para diagnosticar a causa. E o serviço envia-lhe e-mails se houver alguma alteração na disponibilidade e desempenho da sua aplicação. Application Insight torna-se assim uma valiosa ferramenta de segurança porque ajuda na disponibilidade na tríade de segurança de confidencialidade, integridade e disponibilidade.

### <a name="azure-monitor"></a>Azure Monitor
O [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) oferece visualização, consulta, encaminhamento, alerta, escala automática e automatização em dados tanto da infraestrutura Azure ([Registo de Atividade)](../../azure-monitor/platform/platform-logs-overview.md)como de cada recurso Azure individual[(Registos de Diagnóstico).](../../azure-monitor/platform/platform-logs-overview.md) Pode utilizar o Monitor Azure para alertá-lo sobre os eventos relacionados com a segurança que são gerados em registos Do Azure.

### <a name="azure-monitor-logs"></a>Registos do Azure Monitor
[Os registos do Azure Monitor](https://azure.microsoft.com/documentation/services/log-analytics/) – Fornece uma solução de gestão de TI tanto para as instalações como para infraestruturas baseadas em nuvem de terceiros (como a AWS) para além dos recursos do Azure. Os dados do Monitor Azure podem ser encaminhados diretamente para os registos do Monitor Azure para que possa ver métricas e registos para todo o seu ambiente num só local.

Os registos do Monitor Azure podem ser uma ferramenta útil na análise forense e outra de segurança, uma vez que a ferramenta permite pesquisar rapidamente através de grandes quantidades de entradas relacionadas com a segurança com uma abordagem de consulta flexível. Além disso, os registos de firewall e proxy no local [podem ser exportados para OT e disponibilizados para análise utilizando registos do Monitor Azure.](../../log-analytics/log-analytics-agent-windows.md)

### <a name="azure-advisor"></a>Azure Advisor
[O Azure Advisor](../../advisor/index.yml) é um consultor personalizado de nuvem que o ajuda a otimizar as suas implementações azure. Analisa a configuração do recurso e a telemetria de utilização. Em seguida, recomenda soluções para ajudar a melhorar o [desempenho,](../../advisor/advisor-performance-recommendations.md) [segurança,](../../advisor/advisor-security-recommendations.md)e [alta disponibilidade](../../advisor/advisor-high-availability-recommendations.md) dos seus recursos, procurando oportunidades para [reduzir o seu gasto total do Azure.](../../advisor/advisor-cost-recommendations.md) O Azure Advisor fornece recomendações de segurança, que podem melhorar significativamente a sua postura de segurança global para soluções que implementa no Azure. Estas recomendações são retiradas da análise de segurança realizada pelo [Azure Security Center.](../../security-center/security-center-intro.md)

### <a name="azure-security-center"></a>Centro de Segurança do Azure
[O Centro](../../security-center/security-center-intro.md) de Segurança ajuda-o a prevenir, detetar e responder a ameaças com maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

Além disso, o Security Center ajuda nas operações de segurança fornecendo-lhe um único dashboard que surge alertas e recomendações que podem ser atuadas imediatamente. Muitas vezes, pode remediar problemas com um único clique dentro da consola do Security Center.
## <a name="applications"></a>Aplicações
A secção fornece informações adicionais sobre as principais funcionalidades na segurança da aplicação e informações sumárias sobre estas capacidades.

### <a name="web-application-vulnerability-scanning"></a>Digitalização de vulnerabilidade de aplicação web
Uma das formas mais fáceis de começar com testes de vulnerabilidades na sua [aplicação App Service](../../app-service/overview.md) é utilizar a integração com a [Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) para realizar uma verificação de vulnerabilidade de um clique na sua aplicação. Pode ver os resultados dos testes num relatório fácil de entender e aprender a corrigir cada vulnerabilidade com instruções passo a passo.

### <a name="penetration-testing"></a>Teste de Penetração
Se preferir realizar os seus próprios testes de penetração ou pretender utilizar outro scanner ou fornecedor, deve seguir o processo de aprovação de testes de [penetração azure](https://docs.microsoft.com/azure/security/fundamentals/pen-testing ) e obter aprovação prévia para realizar os testes de penetração desejados.

### <a name="web-application-firewall"></a>Firewall de aplicação web
A firewall de aplicação web (WAF) no Portal de [Aplicações Azure](https://azure.microsoft.com/services/application-gateway/) ajuda a proteger aplicações web de ataques comuns baseados na web, como injeção DeSQL, ataques de scripts transporitais e sequestro de sessão. Vem reconfigurado com proteção contra ameaças identificadas pelo [Open Web Application Security Project (OWASP) como as 10 principais vulnerabilidades comuns](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Autenticação e autorização no Serviço de Aplicações do Azure
[A autenticação/autorização](../../app-service/overview-authentication-authorization.md) do serviço de aplicações é uma funcionalidade que fornece uma forma de a sua aplicação assinar nos utilizadores para que não tenha de alterar o código no backend da aplicação. Fornece uma forma fácil de proteger a sua aplicação e trabalhar com dados por utilizador.

### <a name="layered-security-architecture"></a>Arquitetura de segurança em camadas
Uma vez que o [App Service Environments](../../app-service/environment/app-service-app-service-environment-intro.md) fornece um ambiente de tempo de execução isolado implantado numa [Rede Virtual Azure,](../../virtual-network/virtual-networks-overview.md)os desenvolvedores podem criar uma arquitetura de segurança em camadas que fornece diferentes níveis de acesso à rede para cada nível de aplicação. Um desejo comum é esconder back-ends da API do acesso geral à Internet, e apenas permitir que as APIs sejam chamadas por aplicações web a montante. [Os grupos de Segurança da Rede (NSGs)](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/) podem ser utilizados em subredes da Rede Virtual Azure que contêm Ambientes de Serviço sapáticos para restringir o acesso público a aplicações API.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Diagnósticos de servidores web e diagnósticos de aplicações
As aplicações web do Serviço de Aplicações fornecem funcionalidades de diagnóstico para registar informações tanto do servidor web como da aplicação web. Estes são logicamente separados em diagnósticos de [servidores web](../../app-service/troubleshoot-diagnostic-logs.md) e diagnósticos de [aplicações](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx). O servidor web inclui dois grandes avanços no diagnóstico e resolução de problemas sites e aplicações.

A primeira novidade é a informação do Estado em tempo real sobre piscinas de aplicações, processos de trabalhadores, sites, domínios de aplicação e pedidos de execução. As segundas novas vantagens são os rastreios detalhados que acompanham um pedido durante todo o processo completo de pedido e resposta.

Para permitir a recolha destes eventos de rastreio, o IIS 7 pode ser configurado para capturar automaticamente registos completos de vestígios, em formato XML, para qualquer pedido específico baseado em códigos de resposta de tempo ou erro decorridos.

#### <a name="web-server-diagnostics"></a>Diagnósticos de servidores web
Pode ativar ou desativar os seguintes tipos de registos:

-   Registo de erros detalhados - Informações detalhadas de erro para códigos de estado HTTP que indicam uma falha (código de estado 400 ou superior). Isto pode conter informações que podem ajudar a determinar por que o servidor devolveu o código de erro.

-   Rastreio de Pedido Falhado - Informações detalhadas sobre pedidos falhados, incluindo um vestígio dos componentes IIS utilizados para processar o pedido e o tempo decorrido em cada componente. Isto pode ser útil se estiver a tentar aumentar o desempenho do site ou isolar o que está a causar a derrode ser devolvida a um erro http específico.

-   Registo do servidor web - Informações sobre transações HTTP utilizando o formato de ficheiro de registo alargado W3C. Isto é útil para determinar métricas globais do site, tais como o número de pedidos tratados ou quantos pedidos são de um endereço IP específico.

#### <a name="application-diagnostics"></a>Diagnósticos de aplicações
[O diagnóstico de aplicações](../../app-service/troubleshoot-diagnostic-logs.md) permite-lhe capturar informações produzidas por uma aplicação web. ASP.NET aplicações podem utilizar o [Sistema.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) class para registar informações no registo de diagnósticos da aplicação. No Application Diagnostics, existem dois tipos principais de eventos, os relacionados com o desempenho de aplicações e os relacionados com falhas de aplicações e erros. As falhas e os erros podem ainda ser divididos em problemas de conectividade, de segurança e de falha. Os problemas de falha estão normalmente relacionados com um problema com o código da aplicação.

No Application Diagnostics é possível ver eventos agrupados nas seguintes formas:

-   Todos (apresenta todos os eventos)
-   Erros de Aplicações (apresenta eventos de exceção)
-   Desempenho (apresenta eventos de desempenho)

## <a name="storage"></a>Armazenamento
A secção fornece informações adicionais sobre as principais funcionalidades na segurança de armazenamento do Azure e informações sumárias sobre estas capacidades.

### <a name="role-based-access-control-rbac"></a>Controlo de Acesso Baseado em Funções (RBAC)
Pode assegurar a sua conta de armazenamento com controlo de acesso baseado em funções (RBAC). Restringir o acesso com base na [necessidade de conhecer](https://en.wikipedia.org/wiki/Need_to_know) e menos [privilegiar](https://en.wikipedia.org/wiki/Principle_of_least_privilege) os princípios de segurança é imperativo para as organizações que querem impor políticas de segurança para o acesso de dados. Estes direitos de acesso são concedidos atribuindo o papel rbac adequado a grupos e aplicações num determinado âmbito. Pode utilizar [funções RBAC incorporadas](../../role-based-access-control/built-in-roles.md), como o Contribuinte de Conta de Armazenamento, para atribuir privilégios aos utilizadores. O acesso às chaves de armazenamento para uma conta de armazenamento utilizando o modelo Do Gestor de [Recursos Azure](../../storage/blobs/security-recommendations.md) pode ser controlado através do Controlo de Acesso baseado em Funções (RBAC).

### <a name="shared-access-signature"></a>Assinatura de acesso partilhado
As [assinaturas de acesso partilhado (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) disponibilizam acesso delegado a recursos na sua conta de armazenamento. O SAS significa que pode conceder permissões limitadas a objetos na sua conta de armazenamento por um período determinado e com um conjunto de permissões especificado. Pode conceder estas permissões limitadas sem ter de partilhar as chaves de acesso à sua conta.

### <a name="encryption-in-transit"></a>Encriptação em Trânsito
A encriptação em trânsito é um mecanismo de proteção de dados quando é transmitido através das redes. Com o Armazenamento Azure, pode proteger os dados utilizando:
-   [Encriptação ao nível do transporte](../../storage/blobs/security-recommendations.md), como HTTPS quando transfere dados para ou para fora do Armazenamento Azure.

-   [Encriptação de arame](../../storage/blobs/security-recommendations.md), como [encriptação SMB 3.0](../../storage/blobs/security-recommendations.md) para [ações do Ficheiro Azure](../../storage/files/storage-dotnet-how-to-use-files.md).

-   Encriptação do lado do cliente, para encriptar os dados antes de ser transferido para o armazenamento e desencriptar os dados após a sua transferência para fora do armazenamento.

### <a name="encryption-at-rest"></a>Encriptação inativa
Para muitas organizações, a encriptação de dados em repouso é um passo obrigatório para a privacidade dos dados, conformidade e soberania de dados. Existem três funcionalidades de segurança de armazenamento Azure que fornecem encriptação de dados que está "em repouso":

-   [A encriptação](../../storage/common/storage-service-encryption.md) do Serviço de Armazenamento permite-lhe solicitar que o serviço de armazenamento criptografe automaticamente os dados ao escrevê-lo no Armazenamento Azure.

-   [A encriptação do lado do cliente](../../storage/common/storage-client-side-encryption.md) também fornece a funcionalidade de encriptação em repouso.

-   [A Encriptação do Disco Azure](../azure-security-disk-encryption-overview.md) permite-lhe encriptar os discos e discos de dados do SO utilizados por uma máquina virtual IaaS.

### <a name="storage-analytics"></a>Análise de Armazenamento
[O Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) realiza a exploração madeireira e fornece dados métricos para uma conta de armazenamento. Pode utilizar estes dados para rastrear pedidos, analisar tendências de utilização e diagnosticar problemas com a sua conta de armazenamento. A Análise de Armazenamento regista informações detalhadas sobre os pedidos com êxito e com falha feitos a um serviço de armazenamento. Estas informações podem ser utilizadas para monitorizar os pedidos individuais e diagnosticar problemas num serviço de armazenamento. Os pedidos são registados com o melhor esforço. São registados os seguintes tipos de pedidos autenticados:
-   Pedidos bem sucedidos.

-   Pedidos falhados, incluindo timeout, estrangulamento, rede, autorização e outros erros.

-   Pedidos que utilizem uma Assinatura de Acesso Partilhado (SAS), incluindo pedidos falhados e bem sucedidos.

-   Pedidos de dados de análise.

### <a name="enabling-browser-based-clients-using-cors"></a>Ativar clientes baseados no navegador usando cors
[A Partilha de Recursos De Origem Cruzada (CORS)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) é um mecanismo que permite que os domínios dêem permissão uns aos outros para acederem aos recursos uns dos outros. O Agente utilizador envia cabeçalhos extrapara garantir que o código JavaScript carregado a partir de um determinado domínio é permitido aceder a recursos localizados noutro domínio. Este último domínio responde então com cabeçalhos extra permitindo ou negando o acesso original ao domínio aos seus recursos.

Os serviços de armazenamento Azure agora suportam o CORS de modo que uma vez estabelecido as regras CORS para o serviço, um pedido devidamente autenticado feito contra o serviço de um domínio diferente é avaliado para determinar se é permitido de acordo com as regras que especificou.

## <a name="networking"></a>Redes
A secção fornece informações adicionais sobre as principais funcionalidades na segurança da rede Azure e informações sumárias sobre estas capacidades.

### <a name="network-layer-controls"></a>Controlos de camadas de rede
O controlo de acesso à rede é o ato de limitar a conectividade de e para dispositivos ou subredes específicos e representa o núcleo da segurança da rede. O objetivo do controlo de acesso à rede é garantir que as suas máquinas e serviços virtuais são acessíveis apenas a utilizadores e dispositivos aos quais deseja que sejam acessíveis.

#### <a name="network-security-groups"></a>Grupos de Segurança de Rede
Um Grupo de Segurança de [Rede (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) é uma firewall de filtragem de pacotes básicos e estatais e permite-lhe controlar o acesso com base num [túnica de 5-tuple](https://www.techopedia.com/definition/28190/5-tuple). Os NSGs não fornecem inspeção da camada de aplicação ou controlos de acesso autenticados. Podem ser utilizados para controlar o tráfego que se desloca entre as subredes dentro de uma Rede Virtual Azure e o tráfego entre uma Rede Virtual Azure e a Internet.

#### <a name="route-control-and-forced-tunneling"></a>Controlo de Rotas e Túneis Forçados
A capacidade de controlar o comportamento de encaminhamento nas suas Redes Virtuais Azure é uma capacidade crítica de segurança e controlo de acesso. Por exemplo, se quiser certificar-se de que todo o tráfego de e para a sua Rede Virtual Azure passa por esse aparelho de segurança virtual, precisa de ser capaz de controlar e personalizar o comportamento de encaminhamento. Pode fazê-lo configurando rotas definidas pelo utilizador em Azure.

[As Rotas Definidas pelo Utilizador](../../virtual-network/virtual-networks-udr-overview.md) permitem personalizar os caminhos de entrada e saída para o tráfego que se desloca dentro e fora de máquinas virtuais individuais ou subredes para garantir a rota mais segura possível. [A escavação forçada](https://www.petri.com/azure-forced-tunneling) é um mecanismo que pode utilizar para garantir que os seus serviços não estão autorizados a iniciar uma ligação a dispositivos na Internet.

Isto é diferente de ser capaz de aceitar ligações de entrada e, em seguida, responder a elas. Os servidores web front-end precisam responder aos pedidos dos anfitriões da Internet, pelo que o tráfego de origem na Internet é permitido entrar nestes servidores web e os servidores web podem responder.

A escavação forçada é comumente usada para forçar o tráfego de saída para a Internet para passar por proxies de segurança no local e firewalls.

#### <a name="virtual-network-security-appliances"></a>Aparelhos de Segurança de Rede Virtual
Enquanto grupos de segurança de rede, rotas definidas pelo utilizador e túneis forçados proporcionam-lhe um nível de segurança na rede e camadas de transporte do [modelo OSI,](https://en.wikipedia.org/wiki/OSI_model)pode haver momentos em que você quer ativar a segurança em níveis mais altos da pilha. Pode aceder a estas funcionalidades de segurança de rede melhoradas utilizando uma solução de segurança de rede de parceiros Azure. Pode encontrar as soluções de segurança de rede de parceiros mais atuais do Azure, visitando o [Azure Marketplace](https://azure.microsoft.com/marketplace/) e procurando "segurança" e "segurança de rede".

### <a name="azure-virtual-network"></a>Rede Virtual do Azure
Uma rede virtual do Azure (VNet) é uma representação da sua própria rede na nuvem. É um isolamento lógico do tecido da rede Azure dedicado à sua subscrição. Pode controlar totalmente os blocos de endereços IP, as definições de DNS, as políticas de segurança e as tabelas de rotas dentro desta rede. Pode segmentar o seu VNet em subredes e colocar máquinas virtuais Azure IaaS (VMs) e/ou [serviços de Cloud (instâncias de papel PaaS)](../../cloud-services/cloud-services-choose-me.md) em Redes Virtuais Azure.

Além disso, pode ligar a rede virtual à sua rede no local através de uma das [opções de conectividade](../../vpn-gateway/index.yml) disponíveis no Azure. Essencialmente, pode expandir a sua rede para o Azure, com controlo total sobre blocos de endereços IP com a vantagem da escala empresarial que o Azure oferece.

A rede Azure suporta vários cenários de acesso remoto seguro. Algumas delas incluem:

-   [Ligar estações de trabalho individuais a uma Rede Virtual Azure](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

-   [Ligue a rede no local a uma Rede Virtual Azure com uma VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

-   [Ligue a rede no local a uma Rede Virtual Azure com uma ligação WAN dedicada](../../expressroute/expressroute-introduction.md)

-   [Ligar redes virtuais Azure uns aos outros](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

### <a name="vpn-gateway"></a>Gateway de VPN
Para enviar tráfego de rede entre a sua Rede Virtual Azure e o seu site no local, tem de criar uma porta de entrada VPN para a sua Rede Virtual Azure. Um [gateway VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) é um tipo de gateway de rede virtual que envia tráfego encriptado através de uma ligação pública. Também pode utilizar gateways VPN para enviar tráfego entre redes virtuais Azure através do tecido da rede Azure.

### <a name="express-route"></a>ExpressRoute
O Microsoft Azure [ExpressRoute](../../expressroute/expressroute-introduction.md) é um link WAN dedicado que permite estender as suas redes no local para a nuvem da Microsoft através de uma ligação privada dedicada facilitada por um fornecedor de conectividade.

![ExpressRoute](./media/overview/azure-security-fig1.png)

Com o ExpressRoute, pode estabelecer ligações aos serviços em nuvem da Microsoft, tais como o Microsoft Azure, Office 365 e o CRM Online. A conectividade pode ser a partir de uma rede qualquer a qualquer (VPN de IP), uma rede Ethernet de ponto a ponto ou uma ligação cruzada virtual através de um fornecedor de conectividade numa localização conjunta.

As ligações ExpressRoute não passam pela Internet pública e, portanto, podem ser consideradas mais seguras do que as soluções baseadas em VPN. Tal permite que as ligações do ExpressRoute ofereçam mais fiabilidade, velocidades superiores, latências inferiores e uma maior segurança do que as ligações típicas através da Internet.


### <a name="application-gateway"></a>Gateway de Aplicação
O Microsoft [Azure Application Gateway](../../application-gateway/overview.md) fornece um controlador de entrega de [aplicações (ADC)](https://en.wikipedia.org/wiki/Application_delivery_controller) como um serviço, oferecendo várias capacidades de equilíbrio de carga de camada 7 para a sua aplicação.

![Gateway de Aplicação](./media/overview/azure-security-fig2.png)

Permite-lhe otimizar a produtividade da quinta web descarregando a rescisão intensiva de TLS cpu para o Gateway de Aplicação (também conhecido como "descarregamento tLS" ou "ponte TLS"). Também fornece outras capacidades de encaminhamento da Camada 7, incluindo distribuição de robin redondo do tráfego de entrada, afinidade de sessão baseada em cookies, encaminhamento baseado em caminhos de URL, e a capacidade de hospedar vários websites por trás de um único Gateway de aplicação. O Application Gateway do Azure é um balanceador de carga de 7 camadas.

Fornece ativação pós-falha, pedidos HTTP de encaminhamento de desempenho entre diversos servidores, quer estejam na nuvem ou no local.

A aplicação fornece muitas funcionalidades do Controlador de Entrega de Aplicações (ADC), incluindo equilíbrio de carga HTTP, afinidade de sessão baseada em cookies, [descarregamento de TLS,](../../application-gateway/tutorial-restrict-web-traffic-powershell.md)sondas de saúde personalizadas, suporte para vários sites, e muitos outros.

### <a name="web-application-firewall"></a>Firewall de Aplicação Web
O Firewall de aplicação web é uma funcionalidade do Portal de [Aplicações Azure](../../application-gateway/overview.md) que fornece proteção às aplicações web que utilizam o gateway de aplicações para funções padrão de Controlo de Entrega de Aplicações (ADC). A Firewall de aplicações Web fá-lo ao protegê-las contra a maioria das 10 principais vulnerabilidades Web da OWASP.

![Firewall de Aplicação Web](./media/overview/azure-security-fig1.png)

-   Proteção contra injeção de SQL

-   Proteção contra Ataques Web comuns, como, por exemplo, injeção de comandos, contrabando de pedidos HTTP, divisão de respostas HTTP e ataques remotos de inclusão de ficheiros

-   Proteção contra violações de protocolo HTTP

-   Proteção contra anomalias de protocolo HTTP, como agente de utilizador de anfitrião e cabeçalhos de aceitação em falta

-   Prevenção de contra bots, crawlers e scanners

-   Deteção de configurações de aplicações comuns (isto é, Apache, IIS, etc.)

Uma firewall de aplicações Web centralizada contra ataques Web simplifica em muito a gestão da segurança e confere à aplicação uma maior garantia de proteção contra as ameaças de intrusão. Uma solução WAF também pode reagir mais rapidamente a uma ameaça de segurança ao corrigir uma vulnerabilidade conhecida numa localização central, em vez de proteger cada uma das aplicações Web individualmente. Os gateways de aplicação existentes podem ser convertidos num gateway de aplicação com a firewall de aplicações Web facilmente.

### <a name="traffic-manager"></a>Gestor de Tráfego
O Microsoft [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) permite controlar a distribuição do tráfego de utilizadores para pontos finais de serviço em diferentes centros de dados. Os pontos finais de serviço suportados pelo Traffic Manager incluem VMs Azure, Web Apps e serviços Cloud. Também pode utilizar o Gestor de Tráfego com pontos finais não Azure externos. O Traffic Manager utiliza o Sistema de Nome de Domínio (DNS) para direcionar os pedidos dos clientes para o ponto final mais adequado com base num [método de encaminhamento de tráfego](../../traffic-manager/traffic-manager-routing-methods.md) e na saúde dos pontos finais.

O Traffic Manager fornece uma gama de métodos de encaminhamento de tráfego para atender às diferentes necessidades de aplicação, [monitorização](../../traffic-manager/traffic-manager-monitoring.md)da saúde de ponto final e falha automática. O Gestor de Tráfego é resiliente a falhas, incluindo a falhas numa região do Azure inteira.

### <a name="azure-load-balancer"></a>Azure Load Balancer
O [Balanceador de Carga do Azure](../../load-balancer/load-balancer-overview.md) oferece elevada disponibilidade e elevado desempenho de rede às suas aplicações. Trata-se de um equilibrista de carga da Camada 4 (TCP, UDP) que distribui o tráfego de entrada entre casos saudáveis de serviços definidos num conjunto equilibrado em carga. O Equilíbrio de Carga Azure pode ser configurado para:

-   Carregar o tráfego de Internet de entrada de tráfego de Internet para máquinas virtuais. Esta configuração é conhecida como [equilíbrio de carga virado para a Internet.](../../load-balancer/concepts-limitations.md#publicloadbalancer)

-   Equilibre o tráfego entre máquinas virtuais numa rede virtual, entre máquinas virtuais em serviços na nuvem, ou entre computadores no local e máquinas virtuais numa rede virtual transversal. Esta configuração é conhecida como equilíbrio interno de [carga.](../../load-balancer/concepts-limitations.md#internalloadbalancer)

- Encaminhar tráfego externo para uma máquina virtual específica

### <a name="internal-dns"></a>DNS Internos
Pode gerir a lista de servidores DNS utilizados num VNet no Portal de Gestão ou no ficheiro de configuração da rede. O cliente pode adicionar até 12 servidores DNS para cada VNet. Ao especificar servidores DNS, é importante verificar se lista os servidores DNS do cliente na ordem correta para o ambiente do cliente. As listas de servidores DNS não funcionam em rodada. São utilizados na ordem de especificação. Se o primeiro servidor DNS da lista for capaz de ser alcançado, o cliente utiliza esse servidor DNS independentemente de o servidor DNS estar a funcionar corretamente ou não. Para alterar a encomenda do servidor DNS para a rede virtual do cliente, remova os servidores DNS da lista e adicione-os de volta na ordem que o cliente quer. O DNS apoia o aspeto de disponibilidade da tríade de segurança "CIA".

### <a name="azure-dns"></a>DNS do Azure
O Sistema de [Nomede Domínio](https://technet.microsoft.com/library/bb629410.aspx), ou DNS, é responsável pela tradução (ou resolução) de um website ou nome de serviço para o seu endereço IP. O [DNS do Azure](../../dns/dns-overview.md) é um serviço de alojamento dos domínios DNS que fornece resolução de nomes através da infraestrutura do Microsoft Azure. Ao alojar os seus domínios no Azure, pode gerir os recursos DNS com as mesmas credenciais, APIs, ferramentas e faturação dos seus outros serviços do Azure. O DNS apoia o aspeto de disponibilidade da tríade de segurança "CIA".

### <a name="azure-monitor-logs-nsgs"></a>Azure Monitor regista NSGs
Pode ativar as seguintes categorias de registode diagnóstico para NSGs:

-   Evento: Contém entradas para as quais as regras de NSG são aplicadas a VMs e funções de exemplo com base no endereço MAC. O estatuto destas regras é recolhido a cada 60 segundos.

-   Contador de regras: Contém entradas para quantas vezes cada regra do NSG é aplicada para negar ou permitir o tráfego.

### <a name="security-center"></a>Centro de Segurança
[O Azure Security Center](../../security-center/security-center-intro.md) analisa continuamente o estado de segurança dos seus recursos Azure para as melhores práticas de segurança da rede. Quando o Security Center identifica potenciais vulnerabilidades de segurança, cria [recomendações](../../security-center/security-center-recommendations.md) que o guiam através do processo de configuração dos controlos necessários para endurecer e proteger os seus recursos.

## <a name="compute"></a>Computação
A secção fornece informações adicionais sobre as principais funcionalidades nesta área e informações sumárias sobre estas capacidades.

### <a name="antimalware--antivirus"></a>Antimalware & Antivírus
Com o Azure IaaS, pode utilizar software antimalware de fornecedores de segurança como microsoft, Symantec, Trend Micro, McAfee e Kaspersky para proteger as suas máquinas virtuais de ficheiros maliciosos, adware e outras ameaças. O [Microsoft Antimalware](antimalware.md) para Serviços de Nuvem Azure e Máquinas Virtuais é uma capacidade de proteção que ajuda a identificar e remover vírus, spyware e outros softwares maliciosos. O Microsoft Antimalware fornece alertas configuráveis quando conhecidos software malicioso ou indesejado tenta instalar-se ou funcionar nos seus sistemas Azure. O Microsoft Antimalware também pode ser implementado usando o Azure Security Center

### <a name="hardware-security-module"></a>Módulo de Segurança de Hardware
A encriptação e a autenticação não melhoram a segurança a menos que as chaves estejam protegidas. Pode simplificar a gestão e segurança dos seus segredos e chaves críticos armazenando-os no [Cofre de Chaves Azure.](../../key-vault/key-vault-overview.md) A Key Vault oferece a opção de armazenar as suas chaves em módulos de segurança de hardware (HSMs) certificados para os padrões FIPS 140-2 Level 2. As chaves de encriptação do SQL Server para [encriptação](https://msdn.microsoft.com/library/bb934049.aspx) de dados de backup ou transparente podem ser armazenadas no Key Vault com quaisquer chaves ou segredos das suas aplicações. As permissões e o acesso a estes itens protegidos são geridos através do [Diretório Ativo Azure.](https://azure.microsoft.com/documentation/services/active-directory/)

### <a name="virtual-machine-backup"></a>Backup de máquina virtual
[A Azure Backup](../../backup/backup-overview.md) é uma solução que protege os dados da sua aplicação com investimento de capital zero e custos operacionais mínimos. Erros de aplicação podem corromper os seus dados, e erros humanos podem introduzir bugs nas suas aplicações que podem levar a problemas de segurança. Com o Azure Backup, as suas máquinas virtuais que executam windows e Linux estão protegidas.

### <a name="azure-site-recovery"></a>Azure Site Recovery
Uma parte importante da estratégia de [continuidade de negócios/recuperação](../../best-practices-availability-paired-regions.md) de desastres da sua organização é descobrir como manter as cargas de trabalho corporativas e as aplicações em funcionamento quando ocorrem interrupções planeadas e não planeadas. [A Azure Site Recovery](../../site-recovery/site-recovery-overview.md) ajuda a orquestrar a replicação, a falha e a recuperação de cargas de trabalho e apps para que estejam disponíveis a partir de um local secundário se a sua localização primária descer.

### <a name="sql-vm-tde"></a>SQL VM TDE
[A encriptação transparente de dados (TDE)](../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md) e a encriptação do nível da coluna (CLE) são funcionalidades de encriptação do servidor SQL. Esta forma de encriptação requer que os clientes gerem e armazenem as chaves criptográficas que utilizam para encriptação.

O serviço Azure Key Vault (AKV) foi concebido para melhorar a segurança e gestão destas chaves num local seguro e altamente disponível. O Conector do Servidor SQL permite ao Servidor SQL utilizar estas teclas a partir do Cofre de Chaves Azure.

Se estiver a executar o SQL Server com máquinas no local, existem passos que pode seguir para aceder ao Cofre de Chaves Azure a partir da sua máquina de servidor SQL no local. Mas para o SQL Server em VMs Azure, pode economizar tempo utilizando a funcionalidade de integração do cofre de chaves Azure. Com alguns cmdlets Azure PowerShell para ativar esta funcionalidade, pode automatizar a configuração necessária para que um VM SQL aceda ao seu cofre chave.

### <a name="vm-disk-encryption"></a>Encriptação do disco VM
[A Encriptação azure Disk](../azure-security-disk-encryption-overview.md) é uma nova capacidade que o ajuda a encriptar os discos de máquinavirtual Windows e Linux IaaS. Aplica a funcionalidade BitLocker padrão da indústria do Windows e a funcionalidade DM-Crypt do Linux para fornecer encriptação de volume para o OS e os discos de dados. A solução está integrada com o Azure Key Vault para o ajudar a controlar e gerir as chaves e segredos de encriptação de discos na subscrição do Key Vault. A solução também garante que todos os dados dos discos da máquina virtual estão encriptados em repouso no seu armazenamento Azure.

### <a name="virtual-networking"></a>Redes virtuais
As máquinas virtuais precisam de conectividade de rede. Para suportar esta exigência, o Azure exige que as máquinas virtuais sejam ligadas a uma Rede Virtual Azure. Uma Rede Virtual Azure é uma construção lógica construída em cima do tecido de rede azul e física. Cada [rede virtual azure](../../virtual-network/virtual-networks-overview.md) lógica está isolada de todas as outras Redes Virtuais Azure. Este isolamento ajuda a garantir que o tráfego de rede nas suas implementações não é acessível a outros clientes do Microsoft Azure.

### <a name="patch-updates"></a>Atualizações de Patch
As Atualizações de Patch fornecem a base para encontrar e corrigir potenciais problemas e simplificar o processo de gestão de atualizações de software, tanto reduzindo o número de atualizações de software que deve implementar na sua empresa e aumentando a sua capacidade de monitorizar a conformidade.

### <a name="security-policy-management-and-reporting"></a>Gestão e relatório de políticas de segurança
[O Centro](../../security-center/security-center-intro.md) de Segurança ajuda-o a prevenir, detetar e responder a ameaças, e proporciona-lhe uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece monitorização integrada de Segurança e gestão de políticas em todas as suas subscrições do Azure, ajuda a detetar ameaças que de outra forma podem passar despercebidas, e trabalha com um vasto ecossistema de soluções de segurança.

## <a name="identity-and-access-management"></a>Gestão de identidades e acessos
A segurança de sistemas, aplicações e dados começa com controlos de acesso baseados na identidade. As funcionalidades de gestão de identidade e acesso que estão incorporadas em produtos e serviços empresariais da Microsoft ajudam a proteger as suas informações organizacionais e pessoais do acesso não autorizado, ao mesmo tempo que as disponibilizam a utilizadores legítimos sempre e onde quer que precisem.

### <a name="secure-identity"></a>Identidade Segura
A Microsoft utiliza múltiplas práticas e tecnologias de segurança em todos os seus produtos e serviços para gerir a identidade e o acesso.

-   [A autenticação multi-factor](https://azure.microsoft.com/services/multi-factor-authentication/) requer que os utilizadores utilizem múltiplos métodos de acesso, no local e na nuvem. Fornece uma autenticação forte com um leque de opções de verificação fáceis, ao mesmo tempo que acomoda os utilizadores com um processo de início simples.

-   [O Microsoft Authenticator](https://aka.ms/authenticator) fornece uma experiência de autenticação multi-factor amigável que funciona com as contas do Microsoft Azure Ative Directory e microsoft, e inclui suporte para wearables e aprovações baseadas em impressões digitais.

-   [A aplicação da política](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/) de passwords aumenta a segurança das palavras-passe tradicionais, impondo requisitos de comprimento e complexidade, rotação periódica forçada e bloqueio de conta após tentativas de autenticação falhadas.

-   [A autenticação baseada em token](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) permite a autenticação via Diretório Ativo Azure.

-   O [controlo de acesso baseado em funções (RBAC)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) permite-lhe conceder acesso com base na função atribuída pelo utilizador, facilitando a doação de acesso aos utilizadores apenas à quantidade de acesso de que necessitam para desempenharem as suas funções. Pode personalizar o RBAC de acordo com o modelo de negócio da sua organização e tolerância ao risco.

-   [A gestão integrada da identidade (identidade híbrida)](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/) permite manter o controlo do acesso dos utilizadores através de datacenters internos e plataformas cloud, criando uma identidade única de utilizador para autenticação e autorização a todos os recursos.

### <a name="secure-apps-and-data"></a>Aplicativos e dados seguros
O [Azure Ative Directory](https://azure.microsoft.com/services/active-directory/), uma solução abrangente de gestão de identidade e de gestão de acesso, ajuda a garantir o acesso aos dados em aplicações no local e na nuvem, e simplifica a gestão de utilizadores e grupos. Combina serviços de diretório sinuosos, governação avançada de identidade, segurança e gestão de acesso a aplicações, e facilita a construção de uma gestão de identidade baseada em políticas nas suas apps. Para otimizar o Azure Active Directory, pode adicionar capacidades pagas com as edições Azure Active Directory Básico, Premium P1 e Premium P2.

| Funcionalidades gratuitas / comuns     | Características Básicas    |Características Premium P1 |Características Premium P2 | Azure Ative Directory Join – Windows 10 apenas funcionalidades relacionadas|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Objetos de diretório,](../../active-directory/active-directory-whatis.md)Gestão do [Utilizador/Grupo (add/update/delete)/ Fornecimento baseado no utilizador, registo do dispositivo](../../active-directory/active-directory-whatis.md), Entrada Única [(SSO)](../../active-directory/active-directory-whatis.md), Alteração de [palavra-passe self-service para utilizadores de nuvem,](../../active-directory/active-directory-whatis.md)Connect (motor Sync que estende os [diretórios no local ao Diretório Ativo do Azure)](../../active-directory/active-directory-whatis.md), Relatórios de [Segurança / Utilização](../../active-directory/active-directory-whatis.md)       |   [Gestão/provisionamento de acesso baseado em grupo,](../../active-directory/active-directory-whatis.md) [Reset de palavra-passe self-service para utilizadores em nuvem,](../../active-directory/active-directory-whatis.md) [Marca da Empresa (Logon Pages/Personalização do Painel](../../active-directory/active-directory-whatis.md)de Acesso) , [Procuração de Aplicações,](../../active-directory/active-directory-whatis.md) [SLA 99,9%](../../active-directory/active-directory-whatis.md) |  [Self-Service Group e app Management/Self-Service application additions/Dynamic Groups,](../../active-directory/active-directory-whatis.md) [Self-Service Password Reset/Change/Unlock with on-premises write-back,](../../active-directory/active-directory-whatis.md) [Multi-Factor Authentication (Cloud and On-premises (MFA Server))](../../active-directory/active-directory-whatis.md), [MIM CAL + MIM Server,](../../active-directory/active-directory-whatis.md)Cloud App [Discovery,](../../active-directory/active-directory-whatis.md) [Connect Health](../../active-directory/active-directory-whatis.md), [Rollover automático de passwords para contas](../../active-directory/active-directory-whatis.md) de grupo|    [Proteção de Identidade](../../active-directory/identity-protection/overview.md), [Gestão privilegiada de Identidade](../../active-directory/privileged-identity-management/pim-configure.md)|   [Junte-se a um dispositivo para Azure AD, Desktop SSO, Microsoft Passport para Azure AD, Recuperação do Administrador BitLocker,](../../active-directory/active-directory-whatis.md) [auto-inscrição do MDM, recuperação de BitLocker self-service, administradores locais adicionais para dispositivos Windows 10 via Azure AD Join](../../active-directory/active-directory-whatis.md)|


- [Cloud App Discovery](../../active-directory/cloudappdiscovery-get-started.md) é uma funcionalidade premium do Azure Ative Directory que lhe permite identificar aplicações em nuvem que são usadas pelos colaboradores da sua organização.

- [A Azure Ative Directory Identity Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) é um serviço de segurança que utiliza capacidades de deteção de anomalias do Diretório Ativo Azure para fornecer uma visão consolidada sobre deteções de riscos e potenciais vulnerabilidades que possam afetar as identidades da sua organização.

- [O Azure Ative Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) permite-lhe juntar VMs Azure a um domínio sem a necessidade de implantar controladores de domínio. Os utilizadores acedem a estes VMs utilizando as suas credenciais de Diretório Ativo corporativo, e podem aceder sem problemas aos recursos.

- [O Azure Ative Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) é um serviço de gestão de identidade global altamente disponível para aplicações viradas para o consumidor que pode escalar centenas de milhões de identidades e integrar-se em plataformas móveis e web. Os seus clientes podem iniciar sessão em todas as suas apps através de experiências personalizáveis que utilizam as contas de redes sociais existentes, ou pode criar novas credenciais autónomas.

- [A Azure Ative Directory B2B Collaboration](https://aka.ms/aad-b2b-collaboration) é uma solução segura de integração de parceiros que suporta as suas relações entre empresas, permitindo aos parceiros aceder em seletivamente às suas aplicações corporativas e dados utilizando as suas identidades autogeridas.

- [O Azure Ative Directory Join](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) permite-lhe alargar as capacidades da nuvem aos dispositivos Windows 10 para gestão centralizada. Permite aos utilizadores conectarem-se à nuvem corporativa ou organizacional através do Azure Ative Directory e simplificando o acesso a apps e recursos.

- [A Procuração de Aplicação de Diretório Ativo Azure](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/) fornece SSO e acesso remoto seguro para aplicações web alojadas no local.

## <a name="next-steps"></a>Passos Seguintes

- Compreenda a sua [responsabilidade partilhada na nuvem.](shared-responsibility.md)

- Saiba como o [Azure Security Center](https://azure.microsoft.com/services/security-center/) pode ajudá-lo a prevenir, detetar e responder a ameaças com maior visibilidade e controlo sobre a segurança dos seus recursos Azure.
