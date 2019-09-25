---
title: Introdução à segurança do Azure | Microsoft Docs
description: Saiba mais sobre a segurança do Azure, seus serviços e como ele funciona.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: c01931268642aebbf87c54080c292b105af15665
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262764"
---
# <a name="introduction-to-azure-security"></a>Introdução à segurança do Azure
## <a name="overview"></a>Descrição geral
Sabemos que a segurança é o trabalho um na nuvem e como é importante que você encontre informações precisas e oportunas sobre a segurança do Azure. Um dos melhores motivos para usar o Azure para seus aplicativos e serviços é aproveitar sua ampla variedade de ferramentas e recursos de segurança. Essas ferramentas e recursos ajudam a tornar possível criar soluções seguras na plataforma segura do Azure. Microsoft Azure fornece confidencialidade, integridade e disponibilidade dos dados do cliente, ao mesmo tempo em que também permite a responsabilidade transparente.

Para ajudá-lo a entender melhor a coleção de controles de segurança implementada em Microsoft Azure das perspectivas do cliente e das operações da Microsoft, essa white paper, "introdução à segurança do Azure", foi escrita para fornecer uma aparência abrangente na segurança disponível com Microsoft Azure.

### <a name="azure-platform"></a>Plataforma do Azure
O Azure é uma plataforma de serviço de nuvem pública que dá suporte a uma ampla seleção de sistemas operacionais, linguagens de programação, estruturas, ferramentas, bancos de dados e dispositivos. Ele pode executar contêineres do Linux com a integração do Docker; Crie aplicativos com JavaScript, Python, .NET, PHP, Java e node. js; Crie back-ends para dispositivos iOS, Android e Windows.

Os serviços de nuvem pública do Azure dão suporte às mesmas tecnologias que milhões de desenvolvedores e profissionais de ti já contam e confiam. Ao se basear ou migrar ativos de ti para um provedor de serviços de nuvem pública, você está confiando na capacidade dessa organização de proteger seus aplicativos e dados com os serviços e os controles que eles fornecem para gerenciar a segurança de seus ativos baseados em nuvem.

A infraestrutura do Azure foi projetada de instalações para aplicativos para hospedar milhões de clientes simultaneamente, além de fornecer uma base confiável sobre a qual as empresas podem atender aos seus requisitos de segurança.

Além disso, o Azure fornece uma ampla gama de opções de segurança configuráveis e a capacidade de controlá-las para que você possa personalizar a segurança para atender aos requisitos exclusivos das implantações de sua organização. Este documento ajuda você a entender como os recursos de segurança do Azure podem ajudá-lo a atender a esses requisitos.

> [!Note]
> O foco principal deste documento é nos controles voltados para o cliente que você pode usar para personalizar e aumentar a segurança para seus aplicativos e serviços.
>
> Fornecemos algumas informações gerais, mas para obter informações detalhadas sobre como a Microsoft protege a plataforma do Azure em si, consulte as informações fornecidas na [central de confiabilidade da Microsoft](https://www.microsoft.com/TrustCenter/default.aspx).

### <a name="abstract"></a>Abstrato
Inicialmente, as migrações de nuvem pública foram impulsionadas por economia de custos e agilidade para inovar. A segurança foi considerada uma preocupação importante por algum tempo, e até mesmo uma mostrar o fixador para a migração da nuvem pública. No entanto, a segurança de nuvem pública fez a transição de uma grande preocupação para um dos drivers para migração na nuvem. A lógica por trás dessa é a capacidade superior de provedores de serviço de nuvem pública de grande porte para proteger aplicativos e os dados de ativos baseados em nuvem.

A infraestrutura do Azure foi concebida a partir da facilidade que as aplicações têm para alojar milhões de clientes em simultâneo e proporciona uma fundação fidedigna com a qual as empresas podem satisfazer as suas necessidades de segurança. Além disso, o Azure fornece uma ampla gama de opções de segurança configuráveis e a capacidade de controlá-las para que você possa personalizar a segurança para atender aos requisitos exclusivos de suas implantações a fim de atender às suas políticas de controle de ti e aderir aos recursos externos regula.

Este documento descreve a abordagem da Microsoft sobre segurança na plataforma de nuvem Microsoft Azure:
* Recursos de segurança implementados pela Microsoft para proteger a infraestrutura do Azure, os dados do cliente e os aplicativos.
* Os serviços do Azure e os recursos de segurança disponíveis para gerenciar a segurança dos serviços e seus dados em suas assinaturas do Azure.

## <a name="summary-azure-security-capabilities"></a>Resumo dos recursos de segurança do Azure
A tabela a seguir fornece uma breve descrição dos recursos de segurança implementados pela Microsoft para proteger a infraestrutura do Azure, os dados do cliente e os aplicativos seguros.
### <a name="security-features-implemented-to-secure-the-azure-platform"></a>Recursos de segurança implementados para proteger a plataforma Azure:
Os recursos listados a seguir são recursos que você pode examinar para fornecer a garantia de que a plataforma Azure é gerenciada de maneira segura. Foram fornecidos links para busca detalhada sobre como a Microsoft aborda as perguntas de confiança do cliente em quatro áreas: Proteger a plataforma, os controles de privacidade &, a conformidade e a transparência.


| [Plataforma segura](https://www.microsoft.com/en-us/trustcenter/Security/default.aspx)  | [Controles de & de privacidade](https://www.microsoft.com/en-us/trustcenter/Privacy/default.aspx)  |[Conformidade](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)   | [Transparência](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [Ciclo de desenvolvimento de segurança](https://www.microsoft.com/en-us/sdl/), auditorias internas | [Gerencie seus dados o tempo todo](https://www.microsoft.com/en-us/trustcenter/Privacy/You-own-your-data) | [Centro de Fidedignidade](https://www.microsoft.com/en-us/trustcenter/default.aspx) |[Como a Microsoft protege os dados do cliente nos serviços do Azure](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| [Treinamento de segurança obrigatório, verificações em segundo plano](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) |  [Controle no local dos dados](https://www.microsoft.com/en-us/trustcenter/Privacy/Where-your-data-is-located) |  [Hub de controles comuns](https://www.microsoft.com/en-us/trustcenter/Common-Controls-Hub) |[Como a Microsoft gerencia o local de dados nos serviços do Azure](https://azuredatacentermap.azurewebsites.net/)|
| [Teste de penetração](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx), [detecção de intrusão, DDoS](https://www.microsoft.com/en-us/trustcenter/Security/ThreatManagement), [auditorias & log](https://www.microsoft.com/en-us/trustcenter/Security/AuditingAndLogging) | [Fornecer acesso a dados em seus termos](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [A lista de verificação de auditoria detalhada dos serviços de nuvem](https://www.microsoft.com/en-us/trustcenter/Compliance/Due-Diligence-Checklist) |[Quem na Microsoft pode acessar seus dados em quais termos](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [Estado da arte Data Center](https://www.microsoft.com/en-us/cloud-platform/global-datacenters), segurança física, [rede segura](network-overview.md) | [Respondendo à imposição de leis](https://www.microsoft.com/en-us/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [Conformidade por serviço, local & setor](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx) |[Como a Microsoft protege os dados do cliente nos serviços do Azure](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx)|
|  [Resposta a incidentes de segurança](https://aka.ms/SecurityResponsepaper), [responsabilidade compartilhada](https://aka.ms/sharedresponsibility) |[Padrões de privacidade rígidos](https://www.microsoft.com/en-us/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Examinar a certificação para os serviços do Azure, Hub de transparência](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)|



### <a name="security-features-offered-by-azure-to-secure-data-and-application"></a>Recursos de segurança oferecidos pelo Azure para proteger dados e aplicativos
Dependendo do modelo de serviço de nuvem, há responsabilidade variável para quem é responsável por gerenciar a segurança do aplicativo ou serviço. Há recursos disponíveis na plataforma Azure para ajudá-lo a cumprir essas responsabilidades por meio de recursos internos e soluções de parceiros que podem ser implantadas em uma assinatura do Azure.

Os recursos internos são organizados em seis (6) áreas funcionais: Operações, aplicativos, armazenamento, rede, computação e identidade. Detalhes adicionais sobre os recursos e funcionalidades disponíveis na plataforma do Azure nessas seis (6) áreas são fornecidas por meio de informações de resumo.

## <a name="operations"></a>Operações
Esta seção fornece informações adicionais sobre os principais recursos em operações de segurança e informações de resumo sobre esses recursos.

### <a name="security-and-audit-dashboard"></a>Painel do Segurança e Auditoria
A [solução segurança e auditoria](../../security-center/security-center-intro.md) fornece uma visão abrangente da postura de segurança de ti de sua organização com [consultas de pesquisa internas](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/) para problemas importantes que exigem atenção. O painel de [segurança e auditoria](https://technet.microsoft.com/library/mt484091.aspx) é a tela inicial para tudo relacionado à segurança em logs de Azure monitor. Ele fornece informações de alto nível sobre o estado de segurança de seus computadores. Também inclui a capacidade de ver todos os eventos das últimas 24 horas, 7 dias ou qualquer outro intervalo de tempo personalizado.

Além disso, você pode configurar Segurança e Conformidade para [realizar automaticamente ações específicas](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/) quando um evento específico é detectado.

### <a name="azure-resource-manager"></a>Azure Resource Manager
[Azure Resource Manager](../../azure-resource-manager/resource-manager-deployment-model.md) permite que você trabalhe com os recursos em sua solução como um grupo. Pode implementar, atualizar ou eliminar todos os recursos da sua solução numa operação única e coordenada. Você usa um [modelo de Azure Resource Manager](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/) para implantação e esse modelo pode funcionar para ambientes diferentes, como teste, preparo e produção. O Resource Manager fornece funcionalidades de segurança, auditoria e etiquetagem para o ajudar a gerir os recursos após a implementação.

Azure Resource Manager implantações baseadas em modelo ajudam a melhorar a segurança das soluções implantadas no Azure, pois as configurações de controle de segurança padrão e podem ser integradas em implantações padronizadas com base em modelos. Isso reduz o risco de erros de configuração de segurança que podem ocorrer durante implantações manuais.

### <a name="application-insights"></a>Application Insights
O [Application insights](https://docs.microsoft.com/azure/application-insights/) é um serviço de gerenciamento de desempenho de aplicativos (APM) extensível para desenvolvedores da Web. Com o Application Insights, você pode monitorar seus aplicativos Web em tempo real e detectar automaticamente as anomalias de desempenho. Ele inclui ferramentas de análise poderosas para ajudá-lo a diagnosticar problemas e entender o que os usuários realmente fazem com seus aplicativos. Ele monitora o aplicativo todo o tempo em que está sendo executado, tanto durante o teste quanto quando você o publicou ou implantou.

Application Insights cria gráficos e tabelas que mostram, por exemplo, quais horas do dia você obtém a maioria dos usuários, a resposta do aplicativo e o quão bem ele é atendido por quaisquer serviços externos dos quais depende.

Se houver falhas, falhas ou problemas de desempenho, você poderá pesquisar os dados de telemetria em detalhes para diagnosticar a causa. E o serviço enviará emails se houver alterações na disponibilidade e no desempenho do seu aplicativo. Assim, o Application Insight torna-se uma ferramenta de segurança valiosa, pois ajuda com a disponibilidade no Tríade de segurança, integridade e confiabilidade de disponibilidade.

### <a name="azure-monitor"></a>Azure Monitor
O [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) oferece visualização, consulta, roteamento, alertas, dimensionamento automático e automação nos dados da infraestrutura do Azure (log de[atividades](../../azure-monitor/platform/activity-logs-overview.md)) e de cada recurso individual do Azure ([logs de diagnóstico](../../azure-monitor/platform/resource-logs-overview.md)). Você pode usar Azure Monitor para alertá-lo sobre eventos relacionados à segurança que são gerados nos logs do Azure.

### <a name="azure-monitor-logs"></a>Registos do Azure Monitor
[Logs de Azure monitor](https://azure.microsoft.com/documentation/services/log-analytics/) – fornece uma solução de gerenciamento de ti para infraestrutura local e de terceiros baseada em nuvem (como AWS), além dos recursos do Azure. Os dados de Azure Monitor podem ser roteados diretamente para Azure Monitor logs para que você possa ver as métricas e os logs de todo o seu ambiente em um único lugar.

Os logs de Azure Monitor podem ser uma ferramenta útil em análise forense e outras análises de segurança, pois a ferramenta permite que você pesquise rapidamente por grandes quantidades de entradas relacionadas à segurança com uma abordagem de consulta flexível. Além disso, [os logs de firewall e proxy locais podem ser exportados para o Azure e disponibilizados para análise usando logs de Azure monitor.](../../log-analytics/log-analytics-agent-windows.md)

### <a name="azure-advisor"></a>Azure Advisor
O [Azure Advisor](../../advisor/index.yml) é um consultor de nuvem personalizado que ajuda você a otimizar suas implantações do Azure. Analisa a configuração do recurso e a telemetria de utilização. Em seguida, ele recomenda soluções para ajudar a melhorar o [desempenho](../../advisor/advisor-performance-recommendations.md), a [segurança](../../advisor/advisor-security-recommendations.md)e a [alta disponibilidade](../../advisor/advisor-high-availability-recommendations.md) de seus recursos, ao mesmo tempo em que procura oportunidades para [reduzir o gasto geral do Azure](../../advisor/advisor-cost-recommendations.md). O Azure Advisor fornece recomendações de segurança, que podem melhorar significativamente sua postura de segurança geral para soluções implantadas no Azure. Essas recomendações são desenhadas da análise de segurança realizada pela [central de segurança do Azure.](../../security-center/security-center-intro.md)

### <a name="azure-security-center"></a>Centro de Segurança do Azure
O [Centro de Segurança do Azure](../../security-center/security-center-intro.md) ajuda-o a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

Além disso, a central de segurança do Azure ajuda com as operações de segurança, fornecendo um único painel que superfícies de alertas e recomendações que podem ser acionadas imediatamente. Geralmente, você pode corrigir problemas com um único clique dentro do console da central de segurança do Azure.
## <a name="applications"></a>Aplicações
A seção fornece informações adicionais sobre os principais recursos de segurança de aplicativo e informações de resumo sobre esses recursos.

### <a name="web-application-vulnerability-scanning"></a>Verificação de vulnerabilidade do aplicativo Web
Uma das maneiras mais fáceis de começar a testar vulnerabilidades no aplicativo do serviço de [aplicativo](../../app-service/overview.md) é usar a [integração com o tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) para executar a verificação de vulnerabilidades com um clique em seu aplicativo. Você pode exibir os resultados do teste em um relatório fácil de entender e saber como corrigir cada vulnerabilidade com instruções passo a passo.

### <a name="penetration-testing"></a>Teste de Penetração
Se preferir executar seus próprios testes de penetração ou desejar usar outro pacote de scanner ou provedor, você deverá seguir o [processo de aprovação de teste de penetração do Azure](https://docs.microsoft.com/azure/security/fundamentals/pen-testing ) e obter aprovação anterior para executar os testes de penetração desejados.

### <a name="web-application-firewall"></a>Firewall do aplicativo Web
O Firewall do aplicativo Web (WAF) no [Gateway aplicativo Azure](https://azure.microsoft.com/services/application-gateway/) ajuda a proteger aplicativos Web contra ataques comuns baseados na Web, como injeção de SQL, ataques de script entre sites e seqüestro de sessão. Ele vem pré-configurado com proteção contra ameaças identificadas pelo [OWASP (Open Web Application Security Project) como as 10 principais vulnerabilidades mais comuns](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Autenticação e autorização no Serviço de Aplicações do Azure
A [autenticação/autorização do serviço de aplicativo](../../app-service/overview-authentication-authorization.md) é um recurso que fornece uma maneira para seu aplicativo conectar usuários para que você não precise alterar o código no back-end do aplicativo. Ele fornece uma maneira fácil de proteger seu aplicativo e trabalhar com dados por usuário.

### <a name="layered-security-architecture"></a>Arquitetura de segurança em camadas
Como os [ambientes do serviço de aplicativo](../../app-service/environment/app-service-app-service-environment-intro.md) fornecem um ambiente de tempo de execução isolado implantado em uma [rede virtual do Azure](../../virtual-network/virtual-networks-overview.md), os desenvolvedores podem criar uma arquitetura de segurança em camadas, fornecendo diferentes níveis de acesso à rede para cada camada de aplicativo. Um desejo comum é ocultar os back-ends de API do acesso geral à Internet e permitir que as APIs sejam chamadas por aplicativos Web upstream. Os [NSGs (grupos de segurança de rede)](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/) podem ser usados em sub-redes de rede virtual do Azure que contêm ambientes de serviço de aplicativo para restringir o acesso público a aplicativos de API.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Diagnóstico de servidor Web e diagnóstico de aplicativo
Os aplicativos Web do serviço de aplicativo fornecem funcionalidade de diagnóstico para registrar informações do servidor Web e do aplicativo Web. Eles são separados logicamente no [diagnóstico do servidor Web](../../app-service/troubleshoot-diagnostic-logs.md) e no [Application Diagnostics](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx). O servidor Web inclui dois avanços importantes no diagnóstico e na solução de problemas de sites e aplicativos.

O primeiro novo recurso são informações de estado em tempo real sobre pools de aplicativos, processos de trabalho, sites, domínios de aplicativo e solicitações em execução. A segunda nova vantagem são os eventos de rastreamento detalhados que rastreiam uma solicitação durante todo o processo de solicitação e resposta.

Para habilitar a coleta desses eventos de rastreamento, o IIS 7 pode ser configurado para capturar automaticamente os logs de rastreamento completos, em formato XML, para qualquer solicitação específica com base no tempo decorrido ou códigos de resposta de erro.

#### <a name="web-server-diagnostics"></a>Diagnóstico do servidor Web
Você pode habilitar ou desabilitar os seguintes tipos de logs:

-   Log de erros Detalhado-informações detalhadas de erro para códigos de status HTTP que indicam uma falha (código de status 400 ou superior). Isso pode conter informações que podem ajudar a determinar por que o servidor retornou o código de erro.

-   Rastreamento de solicitação com falha-informações detalhadas sobre solicitações com falha, incluindo um rastreamento dos componentes do IIS usados para processar a solicitação e o tempo gasto em cada componente. Isso pode ser útil se você estiver tentando aumentar o desempenho do site ou isolar o que está causando o retorno de um erro HTTP específico.

-   Log de servidor Web-informações sobre transações HTTP usando o formato de arquivo de log estendido W3C. Isso é útil ao determinar as métricas gerais do site, como o número de solicitações tratadas ou quantas solicitações são de um endereço IP específico.

#### <a name="application-diagnostics"></a>Diagnóstico de aplicativos
O [Application Diagnostics](../../app-service/troubleshoot-diagnostic-logs.md) permite que você capture informações produzidas por um aplicativo Web. Os aplicativos ASP.NET podem usar a classe [System. Diagnostics. Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) para registrar informações no log do Application Diagnostics. Em Application Diagnostics, há dois tipos principais de eventos, aqueles relacionados ao desempenho do aplicativo e os relacionados a erros e falhas do aplicativo. As falhas e erros podem ser divididos em problemas de conectividade, segurança e falha. Problemas de falha geralmente estão relacionados a um problema com o código do aplicativo.

No Application Diagnostics, você pode exibir eventos agrupados das seguintes maneiras:

-   Todos (exibe todos os eventos)
-   Erros de aplicativo (exibe eventos de exceção)
-   Desempenho (exibe eventos de desempenho)

## <a name="storage"></a>Armazenamento
A seção fornece informações adicionais sobre os principais recursos de segurança de armazenamento do Azure e informações de resumo sobre esses recursos.

### <a name="role-based-access-control-rbac"></a>Controlo de Acesso Baseado em Funções (RBAC)
Você pode proteger sua conta de armazenamento com RBAC (controle de acesso baseado em função). Restringir o acesso com base na [necessidade de conhecer](https://en.wikipedia.org/wiki/Need_to_know) e aos princípios de segurança de [privilégios mínimos](https://en.wikipedia.org/wiki/Principle_of_least_privilege) é imperativo para organizações que desejam impor políticas de segurança para acesso a dados. Esses direitos de acesso são concedidos atribuindo a função RBAC apropriada a grupos e aplicativos em um determinado escopo. Você pode usar [funções RBAC internas](../../role-based-access-control/built-in-roles.md), como colaborador da conta de armazenamento, para atribuir privilégios aos usuários. O acesso às chaves de armazenamento para uma conta de armazenamento usando o modelo de [Azure Resource Manager](../../storage/common/storage-security-guide.md) pode ser controlado por meio do controle de acesso baseado em função (RBAC).

### <a name="shared-access-signature"></a>Assinatura de Acesso Partilhado
As [assinaturas de acesso partilhado (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) disponibilizam acesso delegado a recursos na sua conta de armazenamento. A SAS significa que você pode conceder a um cliente permissões limitadas para objetos em sua conta de armazenamento por um período especificado e com um conjunto especificado de permissões. Você pode conceder essas permissões limitadas sem precisar compartilhar as chaves de acesso da conta.

### <a name="encryption-in-transit"></a>Criptografia em trânsito
A criptografia em trânsito é um mecanismo de proteção de dados quando eles são transmitidos entre redes. Com o armazenamento do Azure, você pode proteger dados usando:
-   [Criptografia no nível de transporte](../../storage/common/storage-security-guide.md), como https, quando você transfere dados para dentro ou para fora do armazenamento do Azure.

-   [Criptografia de transmissão](../../storage/common/storage-security-guide.md), como a [criptografia SMB 3,0](../../storage/common/storage-security-guide.md) para [compartilhamentos de arquivos do Azure](../../storage/files/storage-dotnet-how-to-use-files.md).

-   Criptografia do lado do cliente, para criptografar os dados antes que eles sejam transferidos para o armazenamento e descriptografar os dados depois que eles forem transferidos para fora do armazenamento.

### <a name="encryption-at-rest"></a>Encriptação inativa
Para muitas organizações, a criptografia de dados em repouso é uma etapa obrigatória para a privacidade de dados, a conformidade e a soberania de dados. Há três recursos de segurança de armazenamento do Azure que fornecem criptografia de dados que estão "em repouso":

-   [Criptografia do serviço de armazenamento](../../storage/common/storage-service-encryption.md) permite solicitar que o serviço de armazenamento criptografe automaticamente os dados ao gravá-los no armazenamento do Azure.

-   A [criptografia do lado do cliente](../../storage/common/storage-client-side-encryption.md) também fornece o recurso de criptografia em repouso.

-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) permite criptografar os discos do sistema operacional e os discos de dados usados por uma máquina virtual IaaS.

### <a name="storage-analytics"></a>Análise de Armazenamento
[Análise de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) executa o registro em log e fornece dados de métrica para uma conta de armazenamento. Pode utilizar estes dados para rastrear pedidos, analisar tendências de utilização e diagnosticar problemas relacionados com a sua conta de armazenamento. Análise de Armazenamento registra informações detalhadas sobre solicitações bem-sucedidas e com falha em um serviço de armazenamento. Essas informações podem ser usadas para monitorar solicitações individuais e diagnosticar problemas com um serviço de armazenamento. As solicitações são registradas em uma base de melhor esforço. Os seguintes tipos de solicitações autenticadas são registrados em log:
-   Solicitações bem-sucedidas.

-   Solicitações com falha, incluindo tempo limite, limitação, rede, autorização e outros erros.

-   Solicitações que usam uma SAS (assinatura de acesso compartilhado), incluindo solicitações com falha e bem-sucedidas.

-   Solicitações para dados de análise.

### <a name="enabling-browser-based-clients-using-cors"></a>Habilitando clientes baseados em navegador usando CORS
O [CORS (compartilhamento de recursos entre origens)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) é um mecanismo que permite que os domínios forneçam a cada permissão para acessar os recursos uns dos outros. O agente do usuário envia cabeçalhos extras para garantir que o código JavaScript carregado de um determinado domínio tenha permissão para acessar recursos localizados em outro domínio. O último domínio responde com cabeçalhos extras, permitindo ou negando o acesso de domínio original a seus recursos.

Os serviços de armazenamento do Azure agora dão suporte a CORS para que, depois de definir as regras de CORS para o serviço, uma solicitação autenticada corretamente feita no serviço de um domínio diferente seja avaliada para determinar se ele é permitido de acordo com as regras que você especificou.
## <a name="networking"></a>Redes
A seção fornece informações adicionais sobre os principais recursos de segurança de rede do Azure e informações de resumo sobre esses recursos.

### <a name="network-layer-controls"></a>Controles de camada de rede
O controle de acesso à rede é o ato de limitar a conectividade de e para dispositivos ou sub-redes específicas e representa o núcleo da segurança de rede. O objetivo do controle de acesso à rede é certificar-se de que suas máquinas virtuais e serviços estejam acessíveis a apenas usuários e dispositivos aos quais você deseja acessá-los.

#### <a name="network-security-groups"></a>Grupos de Segurança de Rede
Um [NSG (grupo de segurança de rede)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) é um Firewall básico de filtragem de pacotes com estado e permite que você controle o acesso com base em uma [tupla de 5](https://www.techopedia.com/definition/28190/5-tuple). NSGs não fornecem inspeção de camada de aplicativo ou controles de acesso autenticados. Eles podem ser usados para controlar o tráfego que se move entre sub-redes em uma rede virtual do Azure e o tráfego entre uma rede virtual do Azure e a Internet.

#### <a name="route-control-and-forced-tunneling"></a>Controle de rota e túnel forçado
A capacidade de controlar o comportamento de roteamento em suas redes virtuais do Azure é uma funcionalidade de controle de acesso e segurança de rede crítica. Por exemplo, se você quiser ter certeza de que todo o tráfego de e para sua rede virtual do Azure passa por esse dispositivo de segurança virtual, você precisa ser capaz de controlar e personalizar o comportamento de roteamento. Você pode fazer isso Configurando rotas definidas pelo usuário no Azure.

As [rotas definidas pelo usuário](../../virtual-network/virtual-networks-udr-overview.md) permitem personalizar caminhos de entrada e saída para o tráfego que passa para dentro e para fora de máquinas virtuais individuais ou sub-redes para garantir a rota mais segura possível. O [túnel forçado](https://www.petri.com/azure-forced-tunneling) é um mecanismo que você pode usar para garantir que seus serviços não tenham permissão para iniciar uma conexão com dispositivos na Internet.

Isso é diferente de ser capaz de aceitar as conexões de entrada e, em seguida, respondê-las. Os servidores Web front-end precisam responder às solicitações de hosts da Internet e, portanto, o tráfego de origem da Internet tem permissão de entrada para esses servidores Web e os servidores Web podem responder.

O túnel forçado é normalmente usado para forçar o tráfego de saída para a Internet a passar por proxies e firewalls de segurança locais.

#### <a name="virtual-network-security-appliances"></a>Dispositivos de segurança de rede virtual
Embora os grupos de segurança de rede, as rotas definidas pelo usuário e o túnel forçado forneçam um nível de segurança nas camadas de rede e de transporte do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), pode haver ocasiões em que você queira habilitar a segurança em níveis mais altos da pilha. Você pode acessar esses recursos de segurança de rede aprimorados usando uma solução de dispositivo de segurança de rede de parceiros do Azure. Você pode encontrar as soluções de segurança de rede do parceiro do Azure mais atuais visitando o [Azure Marketplace](https://azure.microsoft.com/marketplace/) e procurando por "segurança" e "segurança de rede".

### <a name="azure-virtual-network"></a>Rede Virtual do Azure

Uma rede virtual do Azure (VNet) é uma representação da sua própria rede na nuvem. É um isolamento lógico da malha de rede do Azure dedicada à sua assinatura. Pode controlar totalmente os blocos de endereços IP, as definições de DNS, as políticas de segurança e as tabelas de rotas dentro desta rede. Você pode segmentar sua VNet em sub-redes e posicionar VMs (máquinas virtuais) de IaaS do Azure e/ou [serviços de nuvem (instâncias de função de PaaS)](../../cloud-services/cloud-services-choose-me.md) em redes virtuais do Azure.

Além disso, pode ligar a rede virtual à sua rede no local através de uma das [opções de conectividade](../../vpn-gateway/index.yml) disponíveis no Azure. Essencialmente, pode expandir a sua rede para o Azure, com controlo total sobre blocos de endereços IP com a vantagem da escala empresarial que o Azure oferece.

A rede do Azure dá suporte a vários cenários de acesso remoto seguro. Alguns deles incluem:

-   [Conectar estações de trabalho individuais a uma rede virtual do Azure](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

-   [Conectar a rede local a uma rede virtual do Azure com uma VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

-   [Conectar a rede local a uma rede virtual do Azure com um link WAN dedicado](../../expressroute/expressroute-introduction.md)

-   [Conectar redes virtuais do Azure entre si](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

### <a name="vpn-gateway"></a>Gateway de VPN
Para enviar o tráfego de rede entre a rede virtual do Azure e o site local, você deve criar um gateway de VPN para sua rede virtual do Azure. Um [Gateway de VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) é um tipo de gateway de rede virtual que envia tráfego criptografado em uma conexão pública. Você também pode usar gateways de VPN para enviar tráfego entre redes virtuais do Azure pela malha de rede do Azure.

### <a name="express-route"></a>ExpressRoute
Microsoft Azure [ExpressRoute](../../expressroute/expressroute-introduction.md) é um link WAN dedicado que permite estender suas redes locais para a nuvem da Microsoft por meio de uma conexão privada dedicada, facilitada por um provedor de conectividade.

![ExpressRoute](./media/overview/azure-security-fig1.png)

Com o ExpressRoute, pode estabelecer ligações aos serviços em nuvem da Microsoft, tais como o Microsoft Azure, Office 365 e o CRM Online. A conectividade pode ser a partir de uma rede qualquer a qualquer (VPN de IP), uma rede Ethernet de ponto a ponto ou uma ligação cruzada virtual através de um fornecedor de conectividade numa localização conjunta.

As conexões do ExpressRoute não passam pela Internet pública e, portanto, podem ser consideradas mais seguras do que as soluções baseadas em VPN. Tal permite que as ligações do ExpressRoute ofereçam mais fiabilidade, velocidades superiores, latências inferiores e uma maior segurança do que as ligações típicas através da Internet.


### <a name="application-gateway"></a>Gateway de Aplicação
O [Gateway](../../application-gateway/overview.md) do Microsoft aplicativo Azure fornece um [ADC (controlador de entrega de aplicativos)](https://en.wikipedia.org/wiki/Application_delivery_controller) como um serviço, oferecendo vários recursos de balanceamento de carga de camada 7 para seu aplicativo.

![Gateway de Aplicação](./media/overview/azure-security-fig2.png)

Ele permite que você otimize a produtividade web farm descarregando a terminação SSL com uso intensivo de CPU para o gateway de aplicativo (também conhecido como "descarregamento SSL" ou "ponte SSL"). Ele também fornece outros recursos de roteamento de camada 7, incluindo a distribuição Round Robin de tráfego de entrada, afinidade de sessão baseada em cookie, roteamento baseado em caminho de URL e a capacidade de hospedar vários sites por trás de um único gateway de aplicativo. O Application Gateway do Azure é um balanceador de carga de 7 camadas.

Fornece ativação pós-falha, pedidos HTTP de encaminhamento de desempenho entre diversos servidores, quer estejam na nuvem ou no local.

O aplicativo fornece muitos recursos do ADC (controlador de entrega de aplicativos), incluindo balanceamento de carga HTTP, afinidade de sessão baseada em cookie, descarregamento de [protocolo SSL (SSL)](../../application-gateway/tutorial-restrict-web-traffic-powershell.md) , investigações de integridade personalizadas, suporte para vários sites e muitos outros.

### <a name="web-application-firewall"></a>Firewall de Aplicações Web
O Firewall do aplicativo Web é um recurso do [Gateway de aplicativo Azure](../../application-gateway/overview.md) que fornece proteção para aplicativos Web que usam o gateway de aplicativo para funções de ADC (controle de entrega de aplicativos) padrão. A Firewall de aplicações Web fá-lo ao protegê-las contra a maioria das 10 principais vulnerabilidades Web da OWASP.

![Firewall de Aplicações Web](./media/overview/azure-security-fig1.png)

-   Proteção contra injeção de SQL

-   Proteção contra Ataques Web comuns, como, por exemplo, injeção de comandos, contrabando de pedidos HTTP, divisão de respostas HTTP e ataques remotos de inclusão de ficheiros

-   Proteção contra violações de protocolo HTTP

-   Proteção contra anomalias de protocolo HTTP, como agente de utilizador de anfitrião e cabeçalhos de aceitação em falta

-   Prevenção de contra bots, crawlers e scanners

-   Detecção de incorretas configurações de aplicativo comuns (isto é, Apache, IIS, etc.)


Uma firewall de aplicações Web centralizada contra ataques Web simplifica em muito a gestão da segurança e confere à aplicação uma maior garantia de proteção contra as ameaças de intrusão. Uma solução WAF também pode reagir mais rapidamente a uma ameaça de segurança ao corrigir uma vulnerabilidade conhecida numa localização central, em vez de proteger cada uma das aplicações Web individualmente. Os gateways de aplicação existentes podem ser convertidos num gateway de aplicação com a firewall de aplicações Web facilmente.

### <a name="traffic-manager"></a>Gestor de Tráfego
O [Gerenciador de tráfego](../../traffic-manager/traffic-manager-overview.md) do Microsoft Azure permite que você controle a distribuição do tráfego do usuário para pontos de extremidade de serviço em diferentes data centers. Os pontos de extremidade de serviço com suporte do Gerenciador de tráfego incluem VMs do Azure, aplicativos Web e serviços de nuvem. Também pode utilizar o Gestor de Tráfego com pontos finais externos, não pertencentes ao Azure. O Gerenciador de tráfego usa o DNS (sistema de nomes de domínio) para direcionar solicitações de cliente para o ponto de extremidade mais apropriado com base em um [método de roteamento de tráfego](../../traffic-manager/traffic-manager-routing-methods.md) e a integridade dos pontos.

O Gerenciador de tráfego fornece uma variedade de métodos de roteamento de tráfego para atender a necessidades de aplicativos diferentes, [monitoramento](../../traffic-manager/traffic-manager-monitoring.md)de integridade do ponto de extremidade e failover automático. O Gestor de Tráfego é resiliente a falhas, incluindo a falhas numa região do Azure inteira.
### <a name="azure-load-balancer"></a>Balanceador de Carga do Azure
O [Balanceador de Carga do Azure](../../load-balancer/load-balancer-overview.md) oferece elevada disponibilidade e elevado desempenho de rede às suas aplicações. É um balanceador de carga de camada 4 (TCP, UDP) que distribui o tráfego de entrada entre instâncias íntegras de serviços definidos em um conjunto de balanceamento de carga. Azure Load Balancer pode ser configurado para:

-   Balancear a carga do tráfego de entrada da Internet para máquinas virtuais. Essa configuração é conhecida como [balanceamento de carga voltado para a Internet](../../load-balancer/load-balancer-overview.md#publicloadbalancer).

-   Balancear a carga do tráfego entre máquinas virtuais em uma rede virtual, entre máquinas virtuais em serviços de nuvem ou entre computadores locais e máquinas virtuais em uma rede virtual entre locais. Essa configuração é conhecida como [balanceamento de carga interno](../../load-balancer/load-balancer-overview.md#internalloadbalancer). 

- Encaminhar tráfego externo para uma máquina virtual específica

### <a name="internal-dns"></a>DNS interno
Você pode gerenciar a lista de servidores DNS usados em uma VNet no Portal de Gerenciamento ou no arquivo de configuração de rede. O cliente pode adicionar até 12 servidores DNS para cada VNet. Ao especificar servidores DNS, é importante verificar se você lista os servidores DNS do cliente na ordem correta para o ambiente do cliente. As listas de servidores DNS não funcionam em rodízio. Eles são usados na ordem em que são especificados. Se o primeiro servidor DNS na lista puder ser alcançado, o cliente usará esse servidor DNS, independentemente de o servidor DNS estar funcionando corretamente ou não. Para alterar a ordem do servidor DNS para a rede virtual do cliente, remova os servidores DNS da lista e adicione-os de volta na ordem que o cliente deseja. O DNS dá suporte ao aspecto de disponibilidade do Tríade de segurança "CIA".

### <a name="azure-dns"></a>DNS do Azure
O [sistema de nomes de domínio](https://technet.microsoft.com/library/bb629410.aspx), ou DNS, é responsável por converter (ou resolver) um site ou nome de serviço para seu endereço IP. O [DNS do Azure](../../dns/dns-overview.md) é um serviço de hospedagem para domínios DNS, fornecendo resolução de nomes usando a infraestrutura do Microsoft Azure. Ao alojar os seus domínios no Azure, pode gerir os recursos DNS com as mesmas credenciais, APIs, ferramentas e faturação dos seus outros serviços do Azure. O DNS dá suporte ao aspecto de disponibilidade do Tríade de segurança "CIA".
### <a name="azure-monitor-logs-nsgs"></a>Logs de Azure Monitor NSGs
Você pode habilitar as seguintes categorias de log de diagnóstico para NSGs:
-   Circunstância Contém entradas para as quais as regras de NSG são aplicadas às VMs e funções de instância com base no endereço MAC. O status dessas regras é coletado a cada 60 segundos.

-   Contador de regras: Contém entradas para quantas vezes cada regra NSG é aplicada para negar ou permitir o tráfego.

### <a name="azure-security-center"></a>Centro de Segurança do Azure

A [central de segurança do Azure](../../security-center/security-center-intro.md) analisa continuamente o estado de segurança de seus recursos do Azure para práticas recomendadas de segurança de rede. Quando a central de segurança identifica possíveis vulnerabilidades de segurança, ele cria [recomendações](../../security-center/security-center-recommendations.md) que orientam você pelo processo de configuração dos controles necessários para proteger e proteger seus recursos.

## <a name="compute"></a>Computação

A seção fornece informações adicionais sobre os principais recursos nessa área e informações resumidas sobre esses recursos.

### <a name="antimalware--antivirus"></a>AntiMalware & antivírus
Com o IaaS do Azure, você pode usar o software antimalware de fornecedores de segurança, como Microsoft, Symantec, Trend Micro, McAfee e Kaspersky, para proteger suas máquinas virtuais contra arquivos mal-intencionados, adware e outras ameaças. O [Microsoft Antimalware](antimalware.md) para serviços de nuvem do Azure e máquinas virtuais é um recurso de proteção que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados. O Microsoft Antimalware fornece alertas configuráveis quando um software mal-intencionado ou indesejado conhecido tenta se instalar ou executar em seus sistemas do Azure. O Microsoft Antimalware também pode ser implantado usando a central de segurança do Azure

### <a name="hardware-security-module"></a>Módulo de segurança de hardware
A criptografia e a autenticação não melhoram a segurança, a menos que as próprias chaves estejam protegidas. Você pode simplificar o gerenciamento e a segurança de seus segredos e chaves críticos armazenando-os em [Azure Key Vault](../../key-vault/key-vault-overview.md). Key Vault fornece a opção de armazenar suas chaves em HSMs (módulos de segurança de hardware) certificados para os padrões FIPS 140-2 nível 2. Suas chaves de criptografia SQL Server para backup ou [Transparent Data Encryption](https://msdn.microsoft.com/library/bb934049.aspx) podem ser armazenadas em Key Vault com quaisquer chaves ou segredos de seus aplicativos. As permissões e o acesso a esses itens protegidos são gerenciados por meio de [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

### <a name="virtual-machine-backup"></a>Cópia de segurança da máquina virtual
O [backup do Azure](../../backup/backup-overview.md) é uma solução que protege os dados do aplicativo com zero investimento de capital e custos operacionais mínimos. Erros de aplicativo podem corromper seus dados, e erros humanos podem introduzir bugs em seus aplicativos que podem levar a problemas de segurança. Com o backup do Azure, suas máquinas virtuais executando Windows e Linux são protegidas.

### <a name="azure-site-recovery"></a>Azure Site Recovery
Uma parte importante da estratégia de [BCDR (continuidade de negócios/recuperação de desastre)](../../best-practices-availability-paired-regions.md) de sua organização é descobrir como manter as cargas de trabalho e os aplicativos corporativos em funcionamento quando ocorrem interrupções planejadas e não planejadas. [Azure site Recovery](../../site-recovery/site-recovery-overview.md) ajuda a orquestrar a replicação, o failover e a recuperação de cargas de trabalho e aplicativos para que fiquem disponíveis em um local secundário se o seu local principal ficar inativo.

### <a name="sql-vm-tde"></a>TDE DE VM DO SQL
[TDE (Transparent Data Encryption)](../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md) e cle (criptografia de nível de coluna) são recursos de criptografia do SQL Server. Essa forma de criptografia exige que os clientes gerenciem e armazenem as chaves de criptografia usadas para criptografia.

O serviço de Azure Key Vault (AKV) foi projetado para melhorar a segurança e o gerenciamento dessas chaves em um local seguro e altamente disponível. O Conector do SQL Server permite que SQL Server use essas chaves de Azure Key Vault.

Se você estiver executando SQL Server com computadores locais, há etapas que podem ser seguidas para acessar Azure Key Vault do computador SQL Server local. Mas, para SQL Server em VMs do Azure, você pode economizar tempo usando o recurso de integração de Azure Key Vault. Com alguns cmdlets Azure PowerShell para habilitar esse recurso, você pode automatizar a configuração necessária para que uma VM do SQL acesse o cofre de chaves.

### <a name="vm-disk-encryption"></a>Criptografia de disco de VM
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) é um novo recurso que ajuda a criptografar seus discos de máquina virtual IaaS Windows e Linux. Ele aplica o recurso de BitLocker padrão do setor do Windows e o recurso DM-cript do Linux para fornecer criptografia de volume para o sistema operacional e os discos de dados. A solução é integrada com Azure Key Vault para ajudá-lo a controlar e gerenciar as chaves de criptografia de disco e os segredos em sua assinatura do Key Vault. A solução também garante que todos os dados nos discos da máquina virtual sejam criptografados em repouso no armazenamento do Azure.

### <a name="virtual-networking"></a>Redes virtuais
As máquinas virtuais precisam de conectividade de rede. Para dar suporte a esse requisito, o Azure exige que as máquinas virtuais estejam conectadas a uma rede virtual do Azure. Uma rede virtual do Azure é uma construção lógica criada sobre a malha de rede física do Azure. Cada [rede virtual do Azure](../../virtual-network/virtual-networks-overview.md) lógica é isolada de todas as outras redes virtuais do Azure. Esse isolamento ajuda a garantir que o tráfego de rede em suas implantações não seja acessível a outros clientes Microsoft Azure.

### <a name="patch-updates"></a>Atualizações de patch
As atualizações de patch fornecem a base para localizar e corrigir possíveis problemas e simplificar o processo de gerenciamento de atualizações de software, tanto reduzindo o número de atualizações de software que devem ser implantadas em sua empresa quanto aumentando sua capacidade de monitorar a conformidade.

### <a name="security-policy-management-and-reporting"></a>Gerenciamento e relatórios de política de segurança
A [central de segurança do Azure](../../security-center/security-center-intro.md) ajuda você a prevenir, detectar e responder a ameaças, além de oferecer maior visibilidade e controle sobre a segurança de seus recursos do Azure. Ele fornece monitoramento de segurança integrado e gerenciamento de políticas em suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com um amplo ecossistema de soluções de segurança.

### <a name="azure-security-center"></a>Centro de Segurança do Azure
O Centro de Segurança ajuda-o a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

A proteção de sistemas, aplicativos e dados começa com controles de acesso baseados em identidade. Os recursos de gerenciamento de identidade e acesso incorporados aos produtos e serviços comerciais da Microsoft ajudam a proteger suas informações pessoais e organizacionais contra o acesso não autorizado e, ao mesmo tempo, disponibilizá-las para usuários legítimos sempre que e onde estiverem necessário.

### <a name="secure-identity"></a>Identidade segura
A Microsoft usa várias práticas e tecnologias de segurança em seus produtos e serviços para gerenciar a identidade e o acesso.
-   A [autenticação multifator](https://azure.microsoft.com/services/multi-factor-authentication/) exige que os usuários usem vários métodos de acesso, locais e na nuvem. Ele fornece autenticação forte com uma variedade de opções de verificação fáceis, ao mesmo tempo que acomoda aos usuários um processo de entrada simples.

-   O [Microsoft Authenticator](https://aka.ms/authenticator) fornece uma experiência de autenticação multifator amigável que funciona com contas de Microsoft Azure Active Directory e da Microsoft e inclui suporte para wearables e aprovações baseadas em impressão digital.

-   A [imposição de política de senha](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/) aumenta a segurança das senhas tradicionais, impondo requisitos de comprimento e complexidade, rotação periódica forçada e bloqueio de conta após tentativas de autenticação com falha.

-   [A autenticação baseada em token](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) permite a autenticação via Azure Active Directory.

-   O [RBAC (controle de acesso baseado em função)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) permite que você conceda acesso com base na função atribuída do usuário, facilitando a concessão dos usuários somente da quantidade de acesso de que eles precisam para realizar suas tarefas de trabalho. Você pode personalizar o RBAC de acordo com o modelo de negócios e a tolerância a riscos de sua organização.

-   O [Gerenciamento de identidade integrado (identidade híbrida)](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/) permite que você mantenha o controle do acesso dos usuários em datacenters internos e plataformas de nuvem, criando uma identidade de usuário único para autenticação e autorização para todos os recursos.

### <a name="secure-apps-and-data"></a>Proteger aplicativos e dados
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/), uma solução de nuvem abrangente de gerenciamento de identidade e acesso, ajuda a proteger o acesso aos dados em aplicativos no site e na nuvem, além de simplificar o gerenciamento de usuários e grupos. Ele combina os principais serviços de diretório, governança avançada de identidade, segurança e gerenciamento de acesso a aplicativos, além de facilitar para os desenvolvedores a criação de gerenciamento de identidade baseado em políticas em seus aplicativos. Para otimizar o Azure Active Directory, pode adicionar capacidades pagas com as edições Azure Active Directory Básico, Premium P1 e Premium P2.

| Recursos gratuitos/comuns     | Recursos básicos    |Recursos premium P1 |Recursos do Premium P2 | Junção de Azure Active Directory – somente recursos relacionados ao Windows 10|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Objetos de diretório](../../active-directory/active-directory-whatis.md), [Gerenciamento de usuário/grupo (adicionar/atualizar/excluir)/provisionamento baseado em usuário, registro de dispositivo](../../active-directory/active-directory-whatis.md), [SSO (logon único)](../../active-directory/active-directory-whatis.md), [alteração de senha de autoatendimento para usuários de nuvem](../../active-directory/active-directory-whatis.md), [conexão (mecanismo de sincronização que estende os diretórios locais para Azure Active Directory)](../../active-directory/active-directory-whatis.md), [relatórios de segurança/uso](../../active-directory/active-directory-whatis.md)       |   [Gerenciamento/provisionamento de acesso baseado em grupo](../../active-directory/active-directory-whatis.md), [redefinição de senha de autoatendimento para usuários de nuvem](../../active-directory/active-directory-whatis.md), [identidade visual da empresa (personalização de páginas de logon/painel de acesso)](../../active-directory/active-directory-whatis.md), [proxy de aplicativo](../../active-directory/active-directory-whatis.md), [SLA 99,9%](../../active-directory/active-directory-whatis.md) |  [Gerenciamento de grupo e aplicativo de autoatendimento/adições de aplicativo de autoatendimento/grupos dinâmicos](../../active-directory/active-directory-whatis.md), [redefinição/alteração/desbloqueio de senha de autoatendimento com write-back local](../../active-directory/active-directory-whatis.md), [autenticação multifator (nuvem e local (servidor MFA) )](../../active-directory/active-directory-whatis.md), [Mim Cal + servidor mim](../../active-directory/active-directory-whatis.md), [Cloud app Discovery](../../active-directory/active-directory-whatis.md), [Connect Health](../../active-directory/active-directory-whatis.md), [substituição automática de senha para contas de grupo](../../active-directory/active-directory-whatis.md)|    [Proteção de identidade](../../active-directory/identity-protection/overview.md), [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)|   [Ingressar um dispositivo no Azure AD, desktop SSO, Microsoft Passport para Azure AD, recuperação do administrador do BitLocker](../../active-directory/active-directory-whatis.md), [registro automático do MDM, recuperação do BitLocker de autoatendimento, administradores locais adicionais para dispositivos Windows 10 por meio do ingresso no Azure ad](../../active-directory/active-directory-whatis.md)|


- [Cloud app Discovery](../../active-directory/cloudappdiscovery-get-started.md) é um recurso premium do Azure Active Directory que permite identificar aplicativos de nuvem que são usados pelos funcionários em sua organização.

- O [Azure Active Directory Identity Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) é um serviço de segurança que usa Azure Active Directory recursos de detecção de anomalias para fornecer uma visão consolidada das detecções de risco e possíveis vulnerabilidades que podem afetar a sua organização Identities.

- [Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) permite que você ingresse VMs do Azure em um domínio sem a necessidade de implantar controladores de domínio. Os usuários entram nessas VMs usando suas credenciais de Active Directory corporativas e podem acessar os recursos diretamente.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) é um serviço de gerenciamento de identidade global altamente disponível para aplicativos voltados para o consumidor que podem ser dimensionados para centenas de milhões de identidades e se integrarem em plataformas móveis e da Web. Seus clientes podem entrar em todos os seus aplicativos por meio de experiências personalizáveis que usam contas de mídia social existentes ou você pode criar novas credenciais autônomas.

- [Azure Active Directory colaboração B2B](https://aka.ms/aad-b2b-collaboration) é uma solução de integração de parceiros segura que dá suporte a relações entre empresas, permitindo que os parceiros acessem seus aplicativos e dados corporativos seletivamente usando suas identidades autogerenciadas .

- [Azure Active Directory Join](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) permite que você estenda os recursos de nuvem para dispositivos Windows 10 para gerenciamento centralizado. Ele possibilita que os usuários se conectem à nuvem corporativa ou organizacional por meio de Azure Active Directory e simplifica o acesso a aplicativos e recursos.

- O [proxy de aplicativo do Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/) fornece SSO e acesso remoto seguro para aplicativos Web hospedados localmente.

## <a name="next-steps"></a>Próximos Passos
- [Introdução à segurança do Microsoft Azure](https://docs.microsoft.com/azure/security)

Serviços e funcionalidades do Azure que pode utilizar para o ajudar a proteger os seus serviços e dados no Azure

- [Centro de Segurança do Azure](https://azure.microsoft.com/services/security-center/)

Previna, detete e responda a ameaças com maior visibilidade e controlo sobre a segurança dos seus recursos do Azure

- [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](../../security-center/security-center-monitoring.md)

Os recursos de monitoramento na central de segurança do Azure para monitorar a conformidade com as políticas.
