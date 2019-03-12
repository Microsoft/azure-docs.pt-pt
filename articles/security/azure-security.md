---
title: Introdução à segurança do Azure | Documentos da Microsoft
description: Saiba mais sobre a segurança do Azure, seus serviços e como ele funciona.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 934046ac4be6c0e85ee687cefb46b61ec8affca6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57543372"
---
# <a name="introduction-to-azure-security"></a>Introdução à segurança do Azure
## <a name="overview"></a>Descrição geral
Sabemos que a segurança é terreno na cloud e como é importante que encontrará informações precisas e atualizadas sobre a segurança do Azure. Uma das razões para utilizar o Azure para as suas aplicações e serviços melhores é tirar proveito de sua vasta gama de ferramentas de segurança e capacidades. Essas ferramentas e recursos ajudam a que seja possível criar soluções seguras na plataforma do Azure segura. Microsoft Azure fornece confidencialidade, integridade e disponibilidade dos dados dos clientes, ao ativar também a responsabilidade transparente.

Para o ajudar a compreender melhor a coleção de segurança controlos implementados no Microsoft Azure da do cliente e o Microsoft operations perspectivas, este white paper, "Introdução ao Azure Security", é escrito para fornecer uma visão abrangente a segurança disponível com o Microsoft Azure.

### <a name="azure-platform"></a>Plataforma do Azure
O Azure é uma plataforma de serviço de nuvem pública que suporta uma seleção ampla de sistemas operativos, linguagens de programação, arquiteturas, ferramentas, bases de dados e dispositivos. Ele pode executar contentores do Linux com a integração de Docker; Crie aplicações com JavaScript, Python, .NET, PHP, Java e node. js; Crie back-ends para dispositivos iOS, Android e Windows dispositivos.

Serviços de cloud pública do Azure suportam as mesmas tecnologias milhões de desenvolvedores e profissionais de TI confiam e confiam. Quando criar ou migrar ativos de TI para, um fornecedor de serviços de nuvem pública está a depender capacidades dessa organização para proteger as suas aplicações e dados com os serviços e os controles fornecem para gerir a segurança dos seus ativos baseados na nuvem.

Infraestrutura do Azure foi concebida de instalação de aplicações para alojar milhões de clientes em simultâneo, e fornece uma Fundação fidedigna na qual as empresas podem satisfazer os seus requisitos de segurança.

Além disso, Azure disponibiliza um vasto leque de opções de segurança configuráveis e a capacidade para controlá-las para que possa personalizar a segurança para satisfazer os requisitos únicos das implementações da sua organização. Este documento ajuda-o a compreender como o Azure segurança recursos podem ajudá-lo a cumprir estes requisitos.

> [!Note]
> O foco principal deste documento é nos controles do lado do cliente que pode utilizar para personalizar e aumentar a segurança das suas aplicações e serviços.
>
> Podemos fornecer algumas informações de descrição geral, mas para obter informações detalhadas sobre como a Microsoft protege própria plataforma do Azure, consulte informações fornecidas a [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx).

### <a name="abstract"></a>Abstrato
Inicialmente, o migrações para a cloud pública eram orientadas por reduções de custos e agilidade para inovar. Segurança foi considerada uma grande preocupação para algum tempo e até mesmo um jeito show, para a migração da cloud pública. No entanto, segurança de cloud pública passou de uma grande preocupação para um dos fatores para migração para a cloud. A lógica por trás disso é a capacidade superior de fornecedores de serviços de nuvem pública grandes a proteger aplicações e os dados de recursos com base na cloud.

A infraestrutura do Azure foi concebida a partir da facilidade que as aplicações têm para alojar milhões de clientes em simultâneo e proporciona uma fundação fidedigna com a qual as empresas podem satisfazer as suas necessidades de segurança. Além disso, o Azure disponibiliza um vasto leque de opções de segurança configuráveis e a capacidade para controlá-las para que possa personalizar a segurança para satisfazer os requisitos únicos das suas implementações para satisfazer o seu departamento de TI controlar as políticas e respeitar externo regulamentações.

Este documento descreve a abordagem da Microsoft para segurança na plataforma de cloud do Microsoft Azure:
* Recursos de segurança implementados pela Microsoft para proteger a infraestrutura do Azure, os dados dos clientes e aplicações.
* Serviços do Azure e recursos de segurança disponíveis para que possa gerir a segurança dos serviços e os seus dados nas suas subscrições do Azure.

## <a name="summary-azure-security-capabilities"></a>Capacidades de resumo de segurança do Azure
A seguinte tabela fornece uma breve descrição dos recursos de segurança implementado pela Microsoft para proteger a infraestrutura do Azure, os dados dos clientes e aplicações seguras.
### <a name="security-features-implemented-to-secure-the-azure-platform"></a>Recursos de segurança implementados para proteger a plataforma do Azure:
Os recursos listados a seguir são capacidades, que pode rever para fornecer a garantia de que a plataforma do Azure é gerenciada de forma segura. Ligações foram fornecidas para desagregar ainda mais na forma como a Microsoft lida com questões de confiança dos clientes em quatro áreas: Proteger a plataforma, privacidade e controlos, conformidade e transparência.


| [Plataforma segura](https://www.microsoft.com/en-us/trustcenter/Security/default.aspx)  | [Privacidade e controlos](https://www.microsoft.com/en-us/trustcenter/Privacy/default.aspx)  |[Conformidade](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)   | [Transparência](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [Ciclo de desenvolvimento de segurança](https://www.microsoft.com/en-us/sdl/)interno auditorias | [Gerir os seus dados sempre](https://www.microsoft.com/en-us/trustcenter/Privacy/You-own-your-data) | [Centro de Fidedignidade](https://www.microsoft.com/en-us/trustcenter/default.aspx) |[Como a Microsoft protege os dados do cliente nos serviços do Azure](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| [Treinamento de segurança obrigatório, verificações de segundo plano](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) |  [Controlar a localização de dados](https://www.microsoft.com/en-us/trustcenter/Privacy/Where-your-data-is-located) |  [Hub de controles comuns](https://www.microsoft.com/en-us/trustcenter/Common-Controls-Hub) |[Como o Microsoft gerir a localização dos dados nos serviços do Azure](http://azuredatacentermap.azurewebsites.net/)|
| [Os testes de penetração](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx), [detecção de intrusão, DDoS](https://www.microsoft.com/en-us/trustcenter/Security/ThreatManagement), [auditorias e Registro em log](https://www.microsoft.com/en-us/trustcenter/Security/AuditingAndLogging) | [Fornecer acesso a dados em seus próprios termos](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [Os serviços em nuvem devida diligência lista de verificação](https://www.microsoft.com/en-us/trustcenter/Compliance/Due-Diligence-Checklist) |[Quem na Microsoft podem aceder aos seus dados em quais condições](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [Centro de dados de ponta](https://www.microsoft.com/en-us/cloud-platform/global-datacenters), a segurança física, [proteger a rede](https://docs.microsoft.com/azure/security/security-network-overview) | [Responder às autoridades judiciárias](https://www.microsoft.com/en-us/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [Conformidade por serviço, a localização e a indústria](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx) |[Como a Microsoft protege os dados do cliente nos serviços do Azure](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx)|
|  [Resposta a incidentes de segurança](https://aka.ms/SecurityResponsepaper), [responsabilidade partilhada](https://aka.ms/sharedresponsibility) |[Normas de privacidade rigorosas](https://www.microsoft.com/en-us/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Certificação de revisão para serviços do Azure, hub de transparência](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)|



### <a name="security-features-offered-by-azure-to-secure-data-and-application"></a>Recursos de segurança oferecidos pelo Azure para proteger os dados e aplicações
Dependendo do modelo de serviço em nuvem, há variável responsabilidade de quem é responsável por gerenciar a segurança da aplicação ou serviço. Capacidades estão disponíveis na plataforma do Azure para ajudá-lo a cumprir essas responsabilidades por meio de recursos internos e soluções de parceiros que podem ser implementadas numa subscrição do Azure.

As capacidades incorporadas estão organizadas em seis (6) áreas funcionais: Operações, aplicativos, armazenamento, rede, computação e identidade. Detalhes adicionais sobre os recursos disponíveis na plataforma do Azure nessas áreas seis (6) são fornecidos através de informações de resumo.

## <a name="operations"></a>Operações
Esta seção fornece informações adicionais sobre os principais recursos nas operações de segurança e informações de resumo sobre esses recursos.

### <a name="security-and-audit-dashboard"></a>Dashboard de auditoria e segurança
O [solução de segurança e auditoria](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) fornece uma visão abrangente da sua organização postura de segurança IT com [consultas de pesquisa incorporada](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/) para problemas relevantes que necessitam da sua atenção. O [segurança e auditoria](https://technet.microsoft.com/library/mt484091.aspx) dashboard é o ecrã principal, tudo relacionado com a segurança nos registos do Azure Monitor. Ele fornece informações de alto nível sobre o estado de segurança de seus computadores. Também inclui a capacidade de ver todos os eventos das últimas 24 horas, 7 dias ou qualquer outro intervalo de tempo personalizado.

Além disso, pode configurar a segurança e conformidade para [automaticamente executar ações específicas](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/) quando é detetado um evento específico.

### <a name="azure-resource-manager"></a>Gestor de Recursos do Azure
[O Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) permite-lhe trabalhar com os recursos na sua solução como um grupo. Pode implementar, atualizar ou eliminar todos os recursos da sua solução numa operação única e coordenada. Utilizar um [modelo Azure Resource Manager](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/) para implementação e esse modelo podem funcionar para ambientes diferentes, tais como teste, transição e produção. O Resource Manager fornece funcionalidades de segurança, auditoria e etiquetagem para o ajudar a gerir os recursos após a implementação.

As implementações do Resource Manager de baseados em modelos do Azure ajudam a melhorar a segurança das soluções implementadas no Azure, porque a controlar as definições de segurança padrão e pode ser integrado ao padronizado implementações baseadas em modelos. Isso reduz o risco de erros de configuração de segurança que podem ocorrer durante as implementações manuais.

### <a name="application-insights"></a>Estatísticas das Aplicações
[O Application Insights](https://docs.microsoft.com/azure/application-insights/) é um serviço de gestão de desempenho de aplicações (APM) extensível para desenvolvedores da web. Com o Application Insights, pode monitorizar as aplicações web em direto e detetar automaticamente anomalias de desempenho. Ele inclui ferramentas de análise poderosas para ajudar a diagnosticar problemas e para compreender o que os utilizadores fazem realmente com as suas aplicações. Monitoriza a aplicação sempre que ele é executado, durante o teste e depois de ter publicado ou implementado.

O Application Insights cria gráficos e tabelas que mostram-lhe, por exemplo, que alturas do dia, obtém a maioria dos usuários, como capacidade de resposta a aplicação está e bem servida por todos os serviços externos que depende.

Se existirem panes, falhas ou problemas de desempenho, pode pesquisar através dos dados de telemetria em detalhes para diagnosticar a causa. E o serviço envia-lhe e-mails se há alguma alteração na disponibilidade e desempenho da sua aplicação. Application Insights, portanto, torna-se uma ferramenta de segurança importante porque ajuda com a disponibilidade na confidencialidade, integridade e triad de segurança de disponibilidade.

### <a name="azure-monitor"></a>Monitor do Azure
[O Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) oferece a visualização, consulta, encaminhamento, alertas, dimensionamento automático e automatização em ambos os dados da infraestrutura do Azure ([registo de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)) e cada recurso do Azure individual ([diagnóstico Regista](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)). Pode utilizar o Azure Monitor para o alertar sobre eventos relacionados à segurança que são gerados nos registos do Azure.

### <a name="azure-monitor-logs"></a>Registos de Monitor do Azure
[Registos de Monitor do Azure](https://azure.microsoft.com/documentation/services/log-analytics/) – dá uma solução de gestão de TI no local e de terceiros infraestrutura baseada na nuvem (por exemplo, o AWS), além de recursos do Azure. Dados do Azure Monitor podem ser roteados diretamente para os registos do Azure Monitor, para que possa ver métricas e registos de todo o ambiente num único local.

Registos de Monitor do Azure podem ser uma ferramenta útil na análise de segurança forense e de outros, como a ferramenta permite-lhe procurar rapidamente por meio de grandes quantidades de entradas relacionadas com segurança com uma abordagem de consulta flexíveis. Além disso, no local [registos de firewall e proxy podem ser exportados para o Azure e disponibilizados para análise com os registos do Azure Monitor.](https://docs.microsoft.com/azure/log-analytics/log-analytics-proxy-firewall)

### <a name="azure-advisor"></a>Azure Advisor
[O Assistente do Azure](https://docs.microsoft.com/azure/advisor/) é consultor da cloud personalizado que ajuda a otimizar as suas implementações do Azure. Analisa a configuração do recurso e a telemetria de utilização. Depois, recomenda soluções para ajudar a melhorar a [desempenho](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations), [security](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations), e [elevada disponibilidade](https://docs.microsoft.com/azure/advisor/advisor-high-availability-recommendations) dos seus recursos durante a procura de oportunidades para [reduzir sua gerais com o Azure gastar](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations). O Assistente do Azure fornece recomendações de segurança, que podem significativamente melhorar a sua postura de segurança geral para soluções de que implementar no Azure. Estas recomendações são obtidas de análise de segurança executada pelo [Centro de segurança do Azure.](https://docs.microsoft.com/azure/security-center/security-center-intro)

### <a name="azure-security-center"></a>Centro de Segurança do Azure
O [Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) ajuda-o a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

Além disso, o Centro de segurança do Azure ajuda a com operações de segurança ao fornecer-lhe um único dashboard que alertas superfícies e recomendações que podem ser utilizadas imediatamente. Muitas vezes, pode remediar problemas com um único clique dentro da consola do Centro de segurança do Azure.
## <a name="applications"></a>Aplicações
A seção fornece informações adicionais sobre os principais recursos nas informações de segurança e o resumo do aplicativo sobre esses recursos.

### <a name="web-application-vulnerability-scanning"></a>Análise de vulnerabilidades de aplicativos da Web
Uma das formas mais simples de começar com o teste quanto a vulnerabilidades de sua [aplicação de serviço de aplicações](https://docs.microsoft.com/azure/app-service/overview) consiste em utilizar o [integração com o Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) para efetuar a análise na sua aplicação de vulnerabilidades de um clique. Pode exibir os resultados de teste num relatório fácil de compreender e saiba como corrigir cada vulnerabilidade com instruções passo a passo.

### <a name="penetration-testing"></a>Testes de Penetração
Se preferir executar seus próprios testes de penetração ou quiser utilizar outro conjunto de scanner ou fornecedor, tem de seguir a [processo de aprovação de testes de penetração de Azure](https://docs.microsoft.com/azure/security/azure-security-pen-testing ) e obtenha aprovação anterior para executar os testes de penetração pretendido.

### <a name="web-application-firewall"></a>Firewall de aplicações Web
A firewall de aplicação web (WAF) no [Gateway de aplicação Azure](https://azure.microsoft.com/services/application-gateway/) ajuda a proteger aplicações web de ataques baseados na web comuns, como injeção SQL, ataques de script entre sites e seqüestro de sessão. Vem pré-configurada com proteção contra ameaças identificadas pelos [Open Web Application Security Project (OWASP) como as vulnerabilidades mais comuns de 10 principais](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Autenticação e autorização no Serviço de Aplicações do Azure
[Aplicação serviço de autenticação / autorização](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) é uma funcionalidade que fornece uma forma para a sua aplicação iniciar sessão dos utilizadores para que não precisa de alterar o código no back-end da aplicação. Ele fornece uma forma fácil de proteger a sua aplicação e trabalhar com dados de por utilizador.

### <a name="layered-security-architecture"></a>Arquitetura de segurança em camadas
Uma vez que [ambientes de serviço de aplicações](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-intro) proporcionam um ambiente de tempo de execução isolado implementado num [rede Virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview), os desenvolvedores podem criar uma arquitetura de segurança em camadas fornecer diferentes níveis de acesso de rede para cada camada da aplicação. É um desejo comum ocultar o back-ends de API de acesso geral de Internet e permitir apenas APIs deve ser chamado por aplicações web a montante. [Grupos de segurança (NSGs) de rede](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/) pode ser utilizado em sub-redes da rede Virtual do Azure que contém os ambientes de serviço de aplicações para restringir o acesso público a aplicações de API.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Diagnóstico do servidor Web e ao application diagnostics
Aplicações web do serviço de aplicações fornecem funcionalidade Diagnóstico de informações de registo do servidor web e a aplicação web. Estes são logicamente separados em [diagnóstico do servidor da web](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs) e [diagnóstico de aplicação](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx). Servidor Web inclui duas grandes avanços no diagnóstico e resolução de problemas de sites e aplicações.

O novo recurso de primeira é informações de estado em tempo real sobre pools de aplicativos, processos de trabalho, sites, domínios de aplicativo e pedidos em execução. As vantagens do novo segundo são os eventos de rastreamento detalhados que controlam um pedido ao longo do processo de solicitação e resposta completado.

Para ativar a recolha desses eventos de rastreio, o IIS 7 pode ser configurado para recolher automaticamente registos de rastreio completo, em formato XML, para qualquer determinada solicitação com base no tempo decorrido ou códigos de resposta de erro.

#### <a name="web-server-diagnostics"></a>Diagnóstico do servidor Web
Pode ativar ou desativar os seguintes tipos de registos:

-   Detalhadas de registo de erro - Erro informações detalhadas de códigos de estado HTTP que indicam uma falha (código de estado 400 ou superior). Isto pode conter informações que podem ajudar a determinar o motivo pelo qual o servidor devolveu o código de erro.

-   Falha do rastreio de pedidos - informações detalhadas sobre pedidos com falhas, incluindo um rastreamento dos componentes do IIS, usado para processar o pedido e o tempo decorrido em cada componente. Isso pode ser útil se está a tentar aumentar o desempenho do site ou isolar o que está a causar um erro HTTP específico a ser devolvido.

-   Web Server registo - informações sobre transações de HTTP utilizando o formato de ficheiro de registo expandido de W3C. Isto é útil ao determinar geral métrica do site, tais como o número de pedidos processados ou o número de pedidos é de um endereço IP específico.

#### <a name="application-diagnostics"></a>Application diagnostics
[Diagnóstico de aplicação](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs) permite-lhe capturar informações produzidas por um aplicativo web. As aplicações ASP.NET podem utilizar o [Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) classe para registar informações no registo de diagnóstico de aplicação. No Application Diagnostics, existem dois tipos principais de eventos, os relacionados com desempenho de aplicações e os relacionados com erros e falhas da aplicação. As falhas e os erros podem ser divididos em problemas de conectividade, segurança e de falha mais detalhes. Problemas de falha estão normalmente relacionados com um problema com o código do aplicativo.

No Application Diagnostics, pode ver eventos agrupados nas seguintes formas:

-   Todos (apresenta todos os eventos)
-   Erros de aplicações (apresenta eventos de exceção)
-   Desempenho (apresenta eventos de desempenho)

## <a name="storage"></a>Armazenamento
A seção fornece informações adicionais sobre os principais recursos do informações de segurança e o resumo de armazenamento do Azure sobre esses recursos.

### <a name="role-based-access-control-rbac"></a>Controlo de Acesso Baseado em Funções (RBAC)
Pode proteger a sua conta de armazenamento com controlo de acesso baseado em funções (RBAC). Restringir o acesso com base na [precisa de saber](https://en.wikipedia.org/wiki/Need_to_know) e [menor privilégio](https://en.wikipedia.org/wiki/Principle_of_least_privilege) princípios de segurança é fundamental para as organizações que desejam aplicar políticas de segurança para acesso a dados. Estes direitos de acesso são concedidos ao atribuir a função RBAC adequada a grupos e aplicações num determinado âmbito. Pode usar [funções RBAC incorporadas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles), tais como contribuinte de conta de armazenamento, para atribuir privilégios a utilizadores. Chaves de acesso ao armazenamento para uma conta de armazenamento com o [do Azure Resource Manager](https://docs.microsoft.com/azure/storage/storage-security-guide) modelo pode ser controlado por meio do controle de acesso baseado em funções (RBAC).

### <a name="shared-access-signature"></a>Assinatura de acesso partilhado
As [assinaturas de acesso partilhado (SAS)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) disponibilizam acesso delegado a recursos na sua conta de armazenamento. A SAS significa que pode conceder a que um cliente permissões limitadas a objetos na conta de armazenamento durante um período especificado e com um conjunto especificado de permissões. Pode conceder estas permissões limitadas sem ter de partilhar as chaves de acesso da conta.

### <a name="encryption-in-transit"></a>Encriptação em trânsito
Encriptação em trânsito é um mecanismo de proteção de dados quando ele é transmitido através de redes. Com o armazenamento do Azure, pode proteger os dados com:
-   [Encriptação de nível de transporte](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), por exemplo, HTTPS, quando a transferência de dados dentro ou fora do armazenamento do Azure.

-   [Ativar a encriptação](https://docs.microsoft.com/azure/storage/storage-security-guide#using-encryption-during-transit-with-azure-file-shares), tal como [encriptação SMB 3.0](https://docs.microsoft.com/azure/storage/storage-security-guide) para [partilhas de ficheiros do Azure](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-files).

-   Encriptação do lado do cliente, para encriptar os dados antes de é transferido para o armazenamento e para descriptografar os dados depois de serem transferido para fora do armazenamento.

### <a name="encryption-at-rest"></a>Encriptação inativa
Para muitas organizações, a encriptação de dados Inativos é um passo obrigatório para a privacidade dos dados, conformidade e soberania de dados. Existem três recursos de segurança de armazenamento do Azure que fornecem criptografia de dados que estão "Inativos":

-   [Encriptação do serviço de armazenamento](https://docs.microsoft.com/azure/storage/storage-service-encryption) permite-lhe pedir que o serviço de armazenamento encriptar dados automaticamente quando a escrevê-lo ao armazenamento do Azure.

-   [Encriptação do lado do cliente](https://docs.microsoft.com/azure/storage/storage-client-side-encryption) também fornece a funcionalidade de encriptação em repouso.

-   [O Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) permite que criptografe os discos de SO e discos de dados utilizados pela máquina virtual IaaS.

### <a name="storage-analytics"></a>Análise de Armazenamento
[Análise de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) efetua o registo e fornece dados de métricas para uma conta de armazenamento. Pode utilizar estes dados para rastrear pedidos, analisar tendências de utilização e diagnosticar problemas relacionados com a sua conta de armazenamento. Análise de armazenamento regista informações detalhadas sobre pedidos com êxito ou falhadas para um serviço de armazenamento. Estas informações podem ser utilizadas para monitorizar pedidos individuais e para diagnosticar problemas com um serviço de armazenamento. Pedidos são registados numa base de melhor esforço. Os seguintes tipos de pedidos autenticados são registados:
-   Pedidos com êxito.

-   Pedidos falhados, incluindo o tempo limite, limitação, rede, autorização e outros erros.

-   Pedidos com um acesso assinatura partilhado (SAS), incluindo pedidos de falhadas e bem-sucedidas.

-   Pedidos de dados de análise.

### <a name="enabling-browser-based-clients-using-cors"></a>Ativação baseada no Browser e clientes utilizando a CORS
[Cross-Origin Resource Sharing (CORS)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) é um mecanismo que-se entre si dar permissão para aceder a recursos uns dos outros. O agente de utilizador envia cabeçalhos extra para garantir que o código JavaScript carregado a partir de um determinado domínio tem permissão para aceder aos recursos localizados em outro domínio. O último domínio, em seguida, responde com cabeçalhos extra permitir ou negar o acesso de domínio original para seus recursos.

Serviços de armazenamento do Azure suportam agora CORS para que depois de definir as regras CORS para o serviço, um pedido devidamente autenticado efetuado para o serviço a partir de um domínio diferente é avaliado para determinar se é permitido de acordo com as regras que especificou.
## <a name="networking"></a>Funcionamento em Rede
A seção fornece informações adicionais sobre os principais recursos do informações de segurança e o resumo de rede do Azure sobre esses recursos.

### <a name="network-layer-controls"></a>Controlos de camada de rede
Controlo de acesso de rede é o ato de limitar a conectividade de e para dispositivos específicos ou sub-redes e representa o núcleo da segurança de rede. O objetivo de controlo de acesso de rede é certificar-se de que as máquinas virtuais e serviços estão acessíveis apenas a utilizadores e dispositivos aos quais pretende-los acessíveis.

#### <a name="network-security-groups"></a>Grupos de Segurança de Rede
R [grupo de segurança de rede (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) é um pacote com monitoração de estado básico, filtragem de firewall e permite-lhe controlar o acesso com base num [5 cadeias de identificação](https://www.techopedia.com/definition/28190/5-tuple). Os NSGs não fornecem inspeção da camada de aplicativo ou autenticado controlos de acesso. Eles podem ser usados para controlar o tráfego de mover entre sub-redes dentro de uma rede Virtual do Azure e o tráfego entre uma rede Virtual do Azure e a Internet.

#### <a name="route-control-and-forced-tunneling"></a>Controlo de rotas e o protocolo de túnel forçado
A capacidade de controlar o comportamento do encaminhamento em redes virtuais do Azure é uma capacidade de controlo de acesso e segurança da rede críticos. Por exemplo, se pretender certificar-se de que todo o tráfego de e para a rede Virtual do Azure vai por meio de que a aplicação virtual de segurança, terá de ser capaz de controlar e personalizar o comportamento do encaminhamento. Pode fazê-lo ao configurar rotas definidas pelo utilizador no Azure.

[Rotas definidas pelo utilizador](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) permitem-lhe personalizar os caminhos de entrada e saídos para mover para o tráfego e fora de máquinas virtuais individuais ou sub-redes para assegurar o máximo possível de rota de proteger. [Túnel forçado](https://www.petri.com/azure-forced-tunneling) é um mecanismo que pode utilizar para se certificar de que os seus serviços não têm permissão para iniciar uma ligação para dispositivos na Internet.

Isso é diferente do que está a ser capaz de aceitar ligações de entrada e, em seguida, responder a elas. Servidores web front-end tem de responder a pedidos a partir de anfitriões da Internet e, então, obtido de Internet o tráfego é permitido de entrada para estes servidores web e os servidores web podem responder.

O túnel forçado costuma é utilizado para forçar o tráfego de saída à Internet para passar por proxies de segurança no local e firewalls.

#### <a name="virtual-network-security-appliances"></a>Aplicações de segurança de rede virtual
Enquanto os grupos de segurança de rede, rotas definidas pelo utilizador e o túnel forçado fornecem-lhe um nível de segurança em camadas de transporte de rede e do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), pode haver ocasiões quando pretender ativar a segurança em níveis mais altos do pilha. Pode acessar esses recursos de segurança de rede aprimorada através da utilização de uma solução de aplicação de segurança de rede de parceiros do Azure. Pode encontrar a rede de parceiros do Azure mais recente soluções de segurança ao aceder a [do Azure Marketplace](https://azure.microsoft.com/marketplace/) e ao procurar por "security" e "segurança de rede".

### <a name="azure-virtual-network"></a>Rede Virtual do Azure

Uma rede virtual do Azure (VNet) é uma representação da sua própria rede na nuvem. É um isolamento lógico dos recursos de infraestrutura de rede do Azure dedicado à sua subscrição. Pode controlar totalmente os blocos de endereços IP, as definições de DNS, as políticas de segurança e as tabelas de rotas dentro desta rede. Pode segmentar a sua VNet em sub-redes e colocar máquinas virtuais (VMs) IaaS do Azure e/ou [(instâncias de função de PaaS) de serviços Cloud](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me) nas redes virtuais do Azure.

Além disso, pode ligar a rede virtual à sua rede no local através de uma das [opções de conectividade](https://docs.microsoft.com/azure/vpn-gateway/) disponíveis no Azure. Essencialmente, pode expandir a sua rede para o Azure, com controlo total sobre blocos de endereços IP com a vantagem da escala empresarial que o Azure oferece.

Redes do Azure suportam vários cenários de acesso remoto seguro. Alguns desses incluem:

-   [Ligue-se de estações de trabalho individuais a uma rede Virtual do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

-   [Ligar a rede no local a uma rede Virtual do Azure com uma VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

-   [Ligar a rede no local a uma rede Virtual do Azure com um link WAN dedicado](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)

-   [Ligar redes virtuais do Azure entre si](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps)

### <a name="vpn-gateway"></a>Gateway de VPN
Para enviar tráfego de rede entre a rede Virtual do Azure e o seu site no local, tem de criar um gateway de VPN para rede Virtual do Azure. R [gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) é um tipo de gateway de rede virtual que envia o tráfego encriptado através de uma ligação pública. Também pode utilizar gateways de VPN para enviar tráfego entre redes virtuais do Azure através de recursos de infraestrutura de rede do Azure.

### <a name="express-route"></a>Express Route
Microsoft Azure [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) é um link WAN dedicado que permite-lhe expandir as redes no local para a cloud da Microsoft através de uma ligação privada dedicada facilitada por um fornecedor de conectividade.

![Express Route](./media/azure-security/azure-security-fig1.png)

Com o ExpressRoute, pode estabelecer ligações aos serviços em nuvem da Microsoft, tais como o Microsoft Azure, Office 365 e o CRM Online. A conectividade pode ser a partir de uma rede qualquer a qualquer (VPN de IP), uma rede Ethernet de ponto a ponto ou uma ligação cruzada virtual através de um fornecedor de conectividade numa localização conjunta.

As ligações ExpressRoute não entram na Internet pública e, portanto, podem ser consideradas mais seguras do que soluções baseadas na VPN. Tal permite que as ligações do ExpressRoute ofereçam mais fiabilidade, velocidades superiores, latências inferiores e uma maior segurança do que as ligações típicas através da Internet.


### <a name="application-gateway"></a>Gateway da Aplicação
Microsoft [Gateway de aplicação do Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) fornece uma [controlador de entrega de aplicação (ADC)](https://en.wikipedia.org/wiki/Application_delivery_controller) como uma oferta de serviço, várias capacidades para a sua aplicação de balanceamento de carga layer 7.

![Gateway da Aplicação](./media/azure-security/azure-security-fig2.png)

Permite-lhe otimizem a produtividade do web farm ao descarregar a terminação SSL com utilização intensiva da CPU para o Gateway de aplicação (também conhecido como "Descarga de SSL" ou "A ponte SSL"). Ele fornece também outras capacidades de encaminhamento de camada 7 incluindo a distribuição round robin de tráfego de entrada, afinidade por sessões com base no cookie, encaminhamento baseado no caminho URL e a capacidade de alojar vários Web sites atrás de um Gateway de aplicação único. O Application Gateway do Azure é um balanceador de carga de 7 camadas.

Fornece ativação pós-falha, pedidos HTTP de encaminhamento de desempenho entre diversos servidores, quer estejam na nuvem ou no local.

Aplicação fornece balanceamento, com base no cookie de afinidade de sessão de carregamento de muitas funcionalidades de controlador de entrega de aplicação (ADC) incluindo HTTP [Secure Sockets Layer (SSL)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-powershell) descarga, sondas de estado de funcionamento personalizado, suporte para múltiplos sites, e muitos outros.

### <a name="web-application-firewall"></a>Firewall de Aplicações Web
Firewall de aplicações Web é uma funcionalidade do [Gateway de aplicação Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) que fornece proteção de aplicações web que utilizam o gateway de aplicação para as funções de controlo de entrega de aplicação (ADC) padrão. A Firewall de aplicações Web fá-lo ao protegê-las contra a maioria das 10 principais vulnerabilidades Web da OWASP.

![Firewall de Aplicações Web](./media/azure-security/azure-security-fig1.png)

-   Proteção contra injeção de SQL

-   Proteção contra Ataques Web comuns, como, por exemplo, injeção de comandos, contrabando de pedidos HTTP, divisão de respostas HTTP e ataques remotos de inclusão de ficheiros

-   Proteção contra violações de protocolo HTTP

-   Proteção contra anomalias de protocolo HTTP, como agente de utilizador de anfitrião e cabeçalhos de aceitação em falta

-   Prevenção de contra bots, crawlers e scanners

-   Deteção de aplicação incorretas comuns (ou seja, Apache, IIS, etc.)


Uma firewall de aplicações Web centralizada contra ataques Web simplifica em muito a gestão da segurança e confere à aplicação uma maior garantia de proteção contra as ameaças de intrusão. Uma solução WAF também pode reagir mais rapidamente a uma ameaça de segurança ao corrigir uma vulnerabilidade conhecida numa localização central, em vez de proteger cada uma das aplicações Web individualmente. Os gateways de aplicação existentes podem ser convertidos num gateway de aplicação com a firewall de aplicações Web facilmente.
### <a name="traffic-manager"></a>Gestor de Tráfego
Microsoft [Gestor de tráfego do Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) permite-lhe controlar a distribuição do tráfego de utilizador para pontos finais de serviço nos centros de dados diferentes. Pontos finais de serviço suportados pelo Gestor de tráfego incluem as VMs do Azure, aplicações Web e serviços Cloud. Também pode utilizar o Gestor de Tráfego com pontos finais externos, não pertencentes ao Azure. O Gestor de tráfego utiliza o sistema de nomes de domínio (DNS) para direcionar os pedidos de cliente para o ponto de final mais adequado com base numa [método de encaminhamento de tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) e o estado de funcionamento dos pontos finais.

Gestor de tráfego oferece uma variedade de métodos de encaminhamento de tráfego de mensagens em fila para atender às necessidades de aplicação diferente, o estado de funcionamento do ponto de extremidade [monitorização](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring)e a ativação pós-falha automática. O Gestor de Tráfego é resiliente a falhas, incluindo a falhas numa região do Azure inteira.
### <a name="azure-load-balancer"></a>Balanceador de Carga do Azure
O [Balanceador de Carga do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) oferece elevada disponibilidade e elevado desempenho de rede às suas aplicações. É um balanceador de carga de camada 4 (TCP, UDP) que distribui o tráfego de entrada entre instâncias de bom estado de funcionamento dos serviços definidos num conjunto com balanceamento de carga. O Balanceador de carga do Azure pode ser configurado para:

-   Carregar saldo tráfego da Internet para máquinas virtuais. Esta configuração é conhecida como [balanceamento de carga com acesso à Internet](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Balancear carga de tráfego entre máquinas virtuais numa rede virtual, entre as máquinas virtuais nos serviços cloud ou entre computadores no local e máquinas virtuais numa rede virtual em vários locais. Esta configuração é conhecida como [balanceamento de carga interno](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview). 

- Reencaminhe tráfego externo para uma máquina virtual específica

### <a name="internal-dns"></a>DNS interno
Pode gerir a lista de servidores DNS utilizados numa VNet no Portal de gestão ou no ficheiro de configuração de rede. Cliente pode adicionar até 12 servidores DNS para cada VNet. Ao especificar servidores DNS, é importante verificar que lista de servidores DNS do cliente na ordem correta para o ambiente do cliente. Listas de servidor DNS não funcionam round robin. Elas são usadas na ordem que estão especificadas. Se o primeiro servidor DNS na lista é capaz de ser contatado, o cliente utiliza esse servidor DNS, independentemente se o servidor DNS está a funcionar devidamente configurada ou não. Para alterar a ordem de servidor DNS para rede virtual do cliente, remova os servidores DNS da lista e adicioná-los novamente na ordem em que o cliente deseja. O DNS suporta o aspecto de disponibilidade da Tríade com segurança "As proteções CIA".

### <a name="azure-dns"></a>DNS do Azure
O [sistema de nomes de domínio](https://technet.microsoft.com/library/bb629410.aspx), ou o DNS, é responsável por converter (ou resolver) um nome de Web site ou serviço para o endereço IP. [O DNS do Azure](https://docs.microsoft.com/azure/dns/dns-overview) é um serviço de alojamento para domínios DNS que fornece resolução de nomes através da infraestrutura do Microsoft Azure. Ao alojar os seus domínios no Azure, pode gerir os recursos DNS com as mesmas credenciais, APIs, ferramentas e faturação dos seus outros serviços do Azure. O DNS suporta o aspecto de disponibilidade da Tríade com segurança "As proteções CIA".
### <a name="azure-monitor-logs-nsgs"></a>O Azure Monitor registos de NSGs
Pode ativar as seguintes categorias de registo de diagnóstico para NSGs:
-   Evento: Contém entradas para o qual NSG as regras são aplicadas a VMs e funções de instância com base no endereço MAC. O estado para estas regras é recolhido a cada 60 segundos.

-   Contador de regras: Contém entradas para quantas vezes cada regra NSG é aplicada a negar ou permitir tráfego.

### <a name="azure-security-center"></a>Centro de Segurança do Azure
Centro de segurança ajuda-o a prevenir, detetar e responder a ameaças, fornece que mais visibilidade e controlo sobre a segurança dos seus recursos do Azure. Fornece gerenciamento integrado de políticas e monitorização de segurança nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança. Centro de recomendações de rede em torno de firewalls, grupos de segurança de rede, configurando regras de tráfego de entrada e muito mais.

Recomendações de rede disponíveis são os seguintes:

-   [Adicionar uma Firewall da próxima geração](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall) recomenda que adiciona a Next Generation Firewall (NGFW) a partir de um parceiro da Microsoft para aumentar sua proteções de segurança

-   [Encaminhar o tráfego apenas através da NGFW](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall#route-traffic-through-ngfw-only) recomenda que configurar regras de grupo (NSG) de segurança que imponham o tráfego de entrada para a sua VM através da sua NGFW de rede.

-   [Ativar grupos de segurança de rede em sub-redes ou máquinas virtuais](https://docs.microsoft.com/azure/security-center/security-center-enable-network-security-groups) recomenda que ative NSGs em sub-redes ou VMs.

-   [Restringir o acesso através da Internet destinada ao ponto final](https://docs.microsoft.com/azure/security-center/security-center-restrict-access-through-internet-facing-endpoints) recomenda que configurar regras de tráfego de entrada para NSGs.


## <a name="compute"></a>Computação

A seção fornece informações adicionais sobre os principais recursos destas informações de área e o resumo sobre esses recursos.

### <a name="antimalware--antivirus"></a>Antimalware e antivírus
Com IaaS do Azure, pode utilizar o software antimalware dos fornecedores de segurança, como a Microsoft, Symantec, Trend Micro, McAfee e a Kaspersky para proteger as suas máquinas virtuais contra ficheiros maliciosos, adware e outras ameaças. [O Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para serviços Cloud do Azure e máquinas virtuais é uma funcionalidade de proteção que ajuda a identificar e remover vírus, spyware e outro software malicioso. Antimalware da Microsoft fornece alertas configuráveis quando conhecido software malicioso ou indesejável se tentar instalar-se ou executar em seus sistemas do Azure. Antimalware da Microsoft também podem ser implementado, o Centro de segurança do Azure

### <a name="hardware-security-module"></a>Módulo de hardware de segurança
Encriptação e autenticação melhora a segurança, a menos que as chaves propriamente ditas são protegidas. Pode simplificar a gestão e segurança dos seus segredos e chaves críticos, armazenando-os no [do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis). Key Vault oferece a opção de armazenar as suas chaves em módulos de segurança de hardware (HSMs) com certificação FIPS 140-2 nível 2 padrões. Chaves de encriptação do SQL Server para cópia de segurança ou [encriptação de dados transparente](https://msdn.microsoft.com/library/bb934049.aspx) podem ser armazenadas no Key Vault com quaisquer chaves ou segredos das suas aplicações. Permissões e acesso a estes itens protegidos são geridos através de [do Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

### <a name="virtual-machine-backup"></a>Cópia de segurança da máquina virtual
[O Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) é uma solução que protege os dados da aplicação sem investimento de capital e um mínimo os custos operacionais. Erros de aplicações podem danificar os dados e erros humanos podem introduzir erros nas suas aplicações que podem levar a problemas de segurança. Com o Azure Backup, as máquinas virtuais que executam o Windows e Linux são protegidas.

### <a name="azure-site-recovery"></a>Recuperação de Site do Azure
Uma parte importante da sua organização [business continuidade/recuperação após desastre (BCDR)](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) estratégia é descobrir como manter aplicações e cargas de trabalho empresariais em funcionamento quando planeada e falhas não planeadas ocorrerem. [O Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) ajuda a orquestrar a replicação, ativação pós-falha e recuperação de cargas de trabalho e aplicações para que eles estejam disponíveis de uma localização secundária se a sua localização principal ficar inativo.

### <a name="sql-vm-tde"></a>TDE DE VM DO SQL
[Encriptação de dados transparente (TDE)](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault) e encriptação de nível de coluna (CLE) são recursos de criptografia do SQL server. Esta forma de encriptação exige que os clientes gerir e armazenar as chaves criptográficas que utilizar para a encriptação.

O serviço do Azure Key Vault (AKV) foi concebido para melhorar a segurança e gestão destas chaves numa localização segura e de elevada disponibilidade. O SQL Server Connector permite que o SQL Server para utilizar estas chaves do Azure Key Vault.

Se estiver a executar o SQL Server com máquinas no local, existem passos que pode seguir para aceder ao Azure Key Vault da sua máquina do SQL Server no local. Mas, para o SQL Server em VMs do Azure, pode poupar tempo ao utilizar a funcionalidade de integração do Azure Key Vault. Com alguns cmdlets do PowerShell do Azure para ativar esta funcionalidade, pode automatizar a configuração necessários para uma VM do SQL aceder ao Cofre de chave.

### <a name="vm-disk-encryption"></a>Encriptação de discos VM
[O Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) é uma nova funcionalidade que lhe permite encriptar discos da máquina virtual Windows e Linux IaaS. Aplica-se a funcionalidade de BitLocker padrão da indústria do Windows e a funcionalidade de DM-Crypt do Linux para fornecer encriptação de volume para o sistema operacional e os discos de dados. A solução está integrada no Azure Key Vault para ajudar a controlar e gerir as chaves de encriptação de disco e segredos na sua subscrição do Cofre de chaves. A solução também garante que todos os dados nos discos de máquina virtual são encriptados em inatividade no armazenamento do Azure.

### <a name="virtual-networking"></a>Redes virtuais
As máquinas virtuais têm conectividade de rede. Para oferecer suporte a esse requisito, o Azure requer máquinas virtuais para ser ligada a uma rede Virtual do Azure. Uma rede Virtual do Azure é uma construção lógica criada sobre os recursos de infraestrutura do Azure de rede física. Cada lógico [rede Virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) é isolada de todas as outras redes virtuais do Azure. Esse isolamento ajuda a assegurar que o tráfego de rede das implementações não está acessível a outros clientes do Microsoft Azure.

### <a name="patch-updates"></a>Atualizações de patch
Atualizações de patch fornecem a base para encontrar e corrigir problemas potenciais e simplificar o processo de gestão de atualizações de software, ao reduzir o número de atualizações de software que tem de implementar na sua empresa e ao aumentar sua capacidade de monitorizar a conformidade.

### <a name="security-policy-management-and-reporting"></a>Gestão de políticas de segurança e relatórios
[Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) ajuda a prevenir, detetar e responder a ameaças e fornece mais visibilidade e controlo sobre a segurança dos seus recursos do Azure. Fornece gerenciamento integrado de políticas e monitorização de segurança nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

### <a name="azure-security-center"></a>Centro de Segurança do Azure
O Centro de Segurança ajuda-o a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

## <a name="identity-and-access-management"></a>Gestão de identidades e acesso

Proteger sistemas, aplicativos e dados começa com controlos de acesso com base na identidade. As funcionalidades de gestão de acessos e identidades incorporadas no serviços e produtos comerciais da Microsoft ajudam a proteger as suas informações pessoais e organizacionais contra acesso não autorizado ao mesmo tempo, tornando-a legítimos utilizadores onde e quando quiserem eles precisa dela.

### <a name="secure-identity"></a>Proteger a identidade
A Microsoft utiliza várias tecnologias e práticas de segurança em seus produtos e serviços para gerir identidades e acessos.
-   [Multi-factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) requer que os utilizadores a utilizar vários métodos para obter acesso, no local e na cloud. Ele fornece uma autenticação segura com uma variedade de opções de verificação simples, ao mesmo tempo, acomodar usuários com um simple processo de início de sessão.

-   [Microsoft Authenticator](https://aka.ms/authenticator) garante uma experiência acessível do multi-factor Authentication que funciona com o Microsoft Azure Active Directory e as contas Microsoft e inclui suporte para wearables e aprovações com base em impressões digitais.

-   [Imposição de políticas de palavra-passe](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/) aumenta a segurança de palavras-passe tradicionais por impor requisitos de comprimento e complexidade, forçados a rotação periódica e bloqueio de conta depois de tentativas falhadas de autenticação.

-   [Autenticação baseada em tokens](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) permite a autenticação através do Azure Active Directory.

-   [Controlo de acesso baseado em funções (RBAC)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) permite-lhe conceder o acesso com base na função atribuída ao utilizador, facilitando a dar aos utilizadores apenas a quantidade de acesso que necessitam para executar suas tarefas de trabalho. Pode personalizar o RBAC por modelo de negócio da sua organização e tolerância a riscos.

-   [Gestão de identidade (identidade híbrida) integrada](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/) permite-lhe manter o controlo de acesso dos utilizadores internas plataformas de datacenters e na cloud, criação de uma única identidade de utilizador para autenticação e autorização para todos os recursos.

### <a name="secure-apps-and-data"></a>Proteger aplicações e dados
[O Azure Active Directory](https://azure.microsoft.com/services/active-directory/), uma abrangente identidades e acessos na cloud solução de gestão, ajuda a proteger o acesso aos dados em aplicativos no site e na cloud e simplifica a gestão de utilizadores e grupos. Ele combina serviços de diretório centrais, avançada governação de identidade, segurança e gestão de acesso de aplicações e torna mais fácil para os desenvolvedores criem a gestão de identidades baseada em políticas nas aplicações. Para otimizar o Azure Active Directory, pode adicionar capacidades pagas com as edições Azure Active Directory Básico, Premium P1 e Premium P2.

| Recursos gratuitos / comuns     | Funcionalidades básicas    |Funcionalidades Premium P1 |Funcionalidades Premium P2 | Associação do Azure Active Directory – funcionalidades apenas relacionadas do Windows 10|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Objetos de diretório](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [(adicionar/atualizar/eliminar) do utilizador/grupo de gestão / baseada no utilizador aprovisionamento, o registo do dispositivo](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [único início de sessão (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [palavra-passe Self-Service A alteração para utilizadores em cloud](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Connect (motor de sincronização que expande diretórios no local para o Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [segurança / relatórios de utilização](https://docs.microsoft.com/azure/active-directory/active-directory-editions)       |     [Gestão de acesso baseado em grupo / provisionamento](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [reposição de palavra-passe Self-Service para utilizadores em cloud](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [(painel de páginas/acesso de início de sessão de personalização) de imagem corporativa da empresa](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [ Proxy de aplicações](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [SLA de 99,9%](https://docs.microsoft.com/azure/active-directory/active-directory-editions) |  [Grupo Self-Service e aplicação de gestão de Autoatendimento aplicativo adições/dinâmico grupos](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [palavra-passe reposição/alteração/desbloqueio do Self-Service, com locais de write-back](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [multi-factor Autenticação (na Cloud e no local (servidor MFA))](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [CAL de MIM + servidor MIM](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Connect Health](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Rollover de palavra-passe automático para contas de grupo](https://docs.microsoft.com/azure/active-directory/active-directory-editions)|  [Proteção de identidade](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)|  [Associe um dispositivo ao Azure AD, Desktop SSO, Microsoft Passport para o Azure AD, recuperação do administrador do BitLocker](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [MDM inscrição automática, recuperação BitLocker Self-Service, administradores locais adicionais para dispositivos Windows 10 através do Azure AD Junte-se](https://docs.microsoft.com/azure/active-directory/active-directory-editions)|


- [Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-cloudappdiscovery-whatis) é uma funcionalidade premium do Azure Active Directory permite-lhe identificar aplicações na cloud que são utilizadas pelos funcionários na sua organização.

- [O Azure Active Directory Identity Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) é um serviço de segurança que utiliza capacidades de deteção de anomalias do Azure Active Directory para fornecer uma vista consolidada de eventos de risco e potenciais vulnerabilidades que poderiam afetar o seu identidades da organização.

- [O Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) permite-lhe associar as VMs do Azure a um domínio sem a necessidade de implementar controladores de domínio. Os utilizadores iniciar sessão para estas VMs através das respetivas credenciais empresariais do Active Directory e podem acessar diretamente recursos.

- [O Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) é um serviço de gestão de identidades global altamente disponível para aplicações destinadas ao consumidor que pode ser dimensionado para centenas de milhões de identidades e integrar em mobile e plataformas da web. Os seus clientes podem iniciar sessão em todas as suas aplicações através de experiências personalizáveis que utilizam as contas de redes sociais existentes ou pode criar novas credenciais autónomo.

- [A colaboração B2B do Active Directory do Azure](https://aka.ms/aad-b2b-collaboration) é uma solução de integração de parceiros segura que suporta as relações entre empresas ao permitir que os parceiros aceder às suas aplicações empresariais e dados seletivamente, utilizando a respetiva gestão automática centrada no identidades.

- [O Azure Active Directory Join](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) permite-lhe expandir as capacidades de nuvem para dispositivos Windows 10 para o gerenciamento centralizado. Ele possibilita que os usuários se conectem à nuvem da organizacional ou empresarial através do Azure Active Directory e simplifica o acesso a aplicações e recursos.

- [Proxy de aplicação do Active Directory do Azure](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/) fornece o SSO e acesso remoto seguro para aplicações web alojadas no local.

## <a name="next-steps"></a>Próximos Passos
- [Introdução à segurança do Microsoft Azure](https://docs.microsoft.com/azure/security/azure-security-getting-started)

Serviços e funcionalidades do Azure que pode utilizar para o ajudar a proteger os seus serviços e dados no Azure

- [Centro de Segurança do Azure](https://azure.microsoft.com/services/security-center/)

Previna, detete e responda a ameaças com maior visibilidade e controlo sobre a segurança dos seus recursos do Azure

- [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-monitoring)

As capacidades de monitorização no Centro de segurança do Azure para monitorizar a conformidade com as políticas.
