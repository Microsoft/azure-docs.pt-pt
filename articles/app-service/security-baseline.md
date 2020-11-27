---
title: Linha de base de segurança Azure para o Serviço de Aplicações
description: A linha de base de segurança do Serviço de Aplicações fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Azure Security Benchmark.
author: msmbaldwin
ms.service: app-service
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 691bb538ea0f46e26c318a68e9f66a9a986eb5d4
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301840"
---
# <a name="azure-security-baseline-for-app-service"></a>Linha de base de segurança Azure para o Serviço de Aplicações

O Azure Security Baseline for App Service contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação. A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](../security/benchmarks/overview-v1.md)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com a nossa orientação de boas práticas. O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao Serviço de Aplicações. Foram excluídos **os controlos** não aplicáveis ao Serviço de Aplicações.

Para ver como o Serviço de Aplicações mapeia completamente para o Azure Security Benchmark, consulte o [ficheiro de mapeamento de base de segurança do Serviço de Aplicações de Aplicação completo.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Segurança da rede

*Para obter mais informações, consulte o [Azure Security Benchmark: Segurança da rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: Ao utilizar o Serviço de Aplicações no nível de preços isolado, também chamado de Ambiente de Serviço de Aplicações (ASE) pode implantar-se diretamente numa sub-rede dentro da sua Rede Virtual Azure. Utilize grupos de segurança de rede para proteger o seu Azure App Service Environment bloqueando o tráfego de entrada e saída para recursos na sua rede virtual ou para restringir o acesso a apps num Ambiente de Serviço de Aplicações.

Por padrão, os grupos de segurança da rede incluem uma regra de negação implícita na menor prioridade, o que requer que adicione explicitamente regras de autorização. Adicione regras de permitir o seu grupo de segurança de rede com base numa abordagem de rede menos privilegiada. As máquinas virtuais subjacentes que são utilizadas para hospedar o App Service Environment não estão diretamente acessíveis porque se encontram numa subscrição gerida pela Microsoft.

Proteja um Ambiente de Serviço de Aplicações através de um Firewall de Aplicação Web (WAF) ativado pelo Gateway de aplicações Azure. Utilize pontos finais de serviço em conjunto com o Gateway de Aplicações para garantir o tráfego de publicação de entrada na sua aplicação.  

No Serviço de Aplicações multi-inquilinos (uma aplicação não isolada), utilize grupos de segurança de rede para bloquear o tráfego de saída da sua app. Habilitar as suas aplicações a aceder a recursos dentro ou através de uma Rede Virtual, com a funcionalidade de Integração de Rede Virtual. Esta funcionalidade também pode ser usada para bloquear o tráfego de saída para endereços públicos a partir da aplicação. A Integração de Rede Virtual não pode ser usada para fornecer acesso de entrada a uma aplicação.  

Proteja o tráfego de entrada para a sua aplicação com:
- Restrições de Acesso - uma série de regras de permitir ou negar que controlam o acesso à entrada
- Service Endpoints - pode negar o tráfego de entrada de fora de redes virtuais especificadas ou sub-redes
- Private Endpoints - exponha a sua aplicação à sua Rede Virtual com um endereço IP privado. Com os Pontos Finais Privados ativados na sua app, já não é acessível à Internet

Ao utilizar a funcionalidade de Integração de Rede Virtual com redes virtuais na mesma região, utilize grupos de segurança de rede e tabelas de rotas com rotas definidas pelo utilizador. As rotas definidas pelo utilizador podem ser colocadas na sub-rede de integração para enviar o tráfego de saída conforme pretendido.  

Considere implementar um Azure Firewall para criar, impor e registar políticas de aplicação e conectividade de rede em todas as suas subscrições e redes virtuais. O Azure Firewall utiliza um endereço IP público estático para recursos de rede virtuais, que permite que firewalls externos identifiquem tráfego originário da sua rede virtual. 

- [Bloqueando um Ambiente de Serviço de Aplicações](environment/firewall-integration.md)

- [Open Web Application Security Project (OWASP) Top 10 de proteção contra vulnerabilidades](https://owasp.org/www-project-top-ten/)

- [Grupos de segurança de rede](../virtual-network/network-security-groups-overview.md)

- [Integrar a aplicação numa rede virtual do Azure](web-sites-integrate-with-vnet.md)

- [Considerações sobre a rede para um Ambiente de Serviço de Aplicações](environment/network-info.md)

- [Como criar um ASE externo](environment/create-external-ase.md)

- [Como criar um ASE interno](environment/create-ilb-ase.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: Implementar recomendações de proteção de rede do Azure Security Center para garantir recursos de rede e configurações relacionadas com as suas aplicações de Serviço de Aplicações e APIs.

Utilize o Azure Firewall para enviar tráfego e criar, impor e registar políticas de aplicação e conectividade de rede em assinaturas e redes virtuais. O Azure Firewall utiliza um endereço IP público estático para os seus recursos de rede virtual, o que permite que firewalls externos identifiquem tráfego originário da sua Rede Virtual. O serviço Azure Firewall também está totalmente integrado com o Azure Monitor para registo e análise.

- [Visão geral da Firewall de Azure](../firewall/overview.md)

- [Compreender a Segurança da Rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Como Permitir a monitorização e proteção do serviço de aplicações](/azure/security-center/defender-for-app-service-intro)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Proteja uma aplicação acessível à Internet num Ambiente de Serviço de Aplicações (ASE) através de:
- Implementando uma Firewall de Aplicação Web (WAF) com gateway de aplicação Azure em frente a uma aplicação virada para a internet
- Utilize restrições de acesso para garantir o tráfego de entrada no Gateway de aplicações
- Proteja a aplicação com o Azure Ative Directory (Azure AD) para garantir a autenticação
- Definir a versão mínima TLS para 1.2
- Desaprote a app apenas para HTTPS

Conduza todo o tráfego de aplicações através de um dispositivo Azure Firewall e monitorize os registos. 

Para garantir uma aplicação acessível à Internet no Serviço de Aplicações multi-inquilinos (como, por exemplo, não no nível isolado)
- Implementar um dispositivo ativado por Firewall para aplicação web em frente a uma aplicação
- Utilize restrições de acesso ou pontos finais de serviço para garantir o tráfego de entrada no dispositivo Web Application Firewall (WAF)
- Proteja a aplicação com Azure AD para garantir a autenticação
- Definir a versão mínima TLS para 1.2
- Desaprote a app apenas para HTTPS
- Utilize a integração da rede virtual e a definição da aplicação WEBSITE_VIRTUAL NETWORK_ROUTE_ALL de sujeitar todo o tráfego de saída a grupos de segurança de rede e rotas definidas pelo utilizador na sub-rede de integração.

Semelhante à aplicação Application Service Environment, conduza todo o tráfego de aplicações através de um dispositivo Azure Firewall e monitorize os registos da aplicação.

Além disso, reveja e siga as recomendações no bloqueio de um documento do App Service Environment.

- [Bloqueando um Ambiente de Serviço de Aplicações](environment/firewall-integration.md)

- [Firewall de aplicação Azure Web no Gateway de aplicações Azure](../web-application-firewall/ag/ag-overview.md)

- [Restrições de acesso ao serviço de aplicações Azure](app-service-ip-restrictions.md)

- [Acompanhe os alertas da WAF e monitorize facilmente as tendências com o Azure Monitor ](../azure-monitor/overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Proteja o Ambiente de Serviço de Aplicações, conforme descrito no Bloqueio de uma documentação do Ambiente do Serviço de Aplicações. Aplique a funcionalidade integrada de Inteligência de Ameaças no Azure Security Center para negar comunicações com endereços IP públicos conhecidos ou não uusados. Utilize restrições de acesso para proteger o tráfego de entrada no Gateway de aplicações. 

Proteja o Serviço de Aplicações multi-inquilinos (uma aplicação não num nível isolado), com um ponto final virado para a internet pública. Permite o tráfego apenas a partir de uma sub-rede específica dentro da sua Rede Virtual e bloqueia tudo o resto. Utilize restrições de acesso para configurar listas de controlo de acesso à rede (restrições IP) para bloquear o tráfego de entrada permitido.

Defina a prioridade entre a lista de autorizações ou negações ordenadas para gerir o acesso à rede à sua aplicação. Esta lista pode incluir endereços IP ou sub-redes de Rede Virtual. Existe uma regra implícita de "negar tudo" no final da lista quando contém uma ou mais entradas. Esta capacidade funciona com todas as cargas de trabalho hospedadas no Serviço de Aplicações, incluindo, Web Apps, Apps API, aplicações Linux, aplicações de contentores Linux e Funções. 

Utilize pontos finais de serviço para restringir o acesso à sua aplicação web a partir de uma Rede Virtual Azure. Limite o acesso a um Serviço de Aplicações multi-arrendatário (uma aplicação não num nível isolado), a partir de sub-redes selecionadas com pontos finais de serviço. 

- [Restrições do IP Estático do Serviço de Aplicações do Azure](app-service-ip-restrictions.md)

- [Firewall de aplicação Azure Web no Gateway de aplicações Azure](../web-application-firewall/ag/ag-overview.md)

- [Como configurar uma Firewall de Aplicação Web (WAF) para o Ambiente de Serviço de Aplicações](environment/app-service-app-service-environment-web-application-firewall.md)

- [Proteja o ASE como descrito no Bloqueio de um Ambiente de Serviço de Aplicações](environment/firewall-integration.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Monitoriza pedidos e respostas enviadas para e a partir de aplicações do Serviço de Aplicações com o Security Center. Os ataques contra uma aplicação web podem ser monitorizados utilizando um Gateway de aplicações em tempo real que tenha Firewall de aplicação web, habilitado com registo integrado do Azure Monitor para rastrear alertas de Firewall de aplicações web e monitorizar facilmente as tendências.

- [Firewall de aplicação web Azure no Gateway de aplicações Azure](../web-application-firewall/ag/ag-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Gerir o tráfego para uma aplicação num Ambiente de Serviço de Aplicações:

- Proteja o Ambiente de Serviço de Aplicações como descrito no Bloqueio de um Ambiente de Serviço de Aplicações
- Implementar um Gateway de aplicações que tem um Azure Web Application Firewall em frente às suas aplicações viradas para a internet
- Desaprote a app para ser acessível apenas em HTTPS

Gerir o tráfego para uma aplicação acessível à Internet no Serviço de Aplicações multi-arrendatários (não no nível isolado): 

- Implementar um Gateway de aplicações que tem o Azure Web Application Firewall ativado em frente às suas aplicações viradas para a internet
- Utilize restrições de acesso ou pontos finais de serviço para garantir o tráfego de entrada na Firewall da Aplicação Web. A capacidade de restrições de acesso funciona com todas as cargas de trabalho hospedadas no Serviço de Aplicações, incluindo Aplicações Web, Apps API, aplicações Linux, aplicações de contentores Linux e Funções.

- Desaprote a app para ser acessível apenas em HTTPS
- Limite o acesso à sua aplicação De Serviço de Aplicações com restrições ip estáticas para que apenas receba tráfego do VIP num gateway de aplicações como o único endereço com acesso.

Reveja os links referenciados para obter informações adicionais.

- [Restrições do IP Estático do Serviço de Aplicações do Azure](app-service-ip-restrictions.md)

- [Firewall de aplicação Azure Web no Gateway de aplicações Azure](../web-application-firewall/ag/ag-overview.md)

- [Como configurar tLS de ponta a ponta utilizando o Gateway de Aplicação com o portal](../application-gateway/end-to-end-ssl-portal.md)

- [Proteja o ASE como descrito no Bloqueio de um Serviço de Aplicações](/azure/app-service/environment/firewall-integration)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: O Serviço de Aplicações tem uma série de pontos finais que são utilizados para gerir o serviço. Estes endereços de ponto final também estão incluídos na etiqueta de serviço IP appServiceManagement. A etiqueta AppServiceManagement é utilizada apenas com um Ambiente de Serviço de Aplicações para permitir esse tráfego. 

Pode permitir ou negar o tráfego do serviço correspondente especificando o nome da etiqueta de serviço no campo de origem ou destino apropriado de uma regra. Os endereços de entrada do Serviço de Aplicações são rastreados na etiqueta de serviço IP do AppService. Não existe uma etiqueta de serviço IP que contenha os endereços de saída utilizados pelo Serviço de Aplicações.

A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

- [Tags de serviço de rede virtual](../virtual-network/service-tags-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para definições de rede relacionadas com as suas aplicações de Serviço de Aplicações. 

Mantenha as configurações de segurança utilizando pseudónimos da Azure Policy nos espaços de nome "Microsoft.Web" e "Microsoft.Network". Crie políticas personalizadas para auditar ou impor a configuração de rede das suas aplicações de Serviço de Aplicações. 

Utilize definições de política incorporadas para o Serviço de Aplicações, tais como:
- A aplicação deve usar um ponto final de serviço de rede virtual
- A aplicação só deve estar acessível em HTTPS
- Desa esta versão TLS mínima para a versão atual

Reveja os links referenciados para obter informações adicionais.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

- [Como configurar tLS de ponta a ponta utilizando o Gateway de Aplicação com o portal](../application-gateway/end-to-end-ssl-portal.md)

- [Proteja o ASE como descrito no Bloqueio de um Serviço de Aplicações](/azure/app-service/environment/firewall-integration)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para grupos de segurança de rede e outros recursos relacionados, incluindo fluxo de tráfego no Serviço de Aplicações.

Especifique a necessidade, duração e assim por diante, com o campo "Descrição" para quaisquer regras, que permitem o tráfego de ou de uma rede para regras individuais de grupos de segurança de rede.

Aplique qualquer uma das definições da Política Azure incorporadas relacionadas com efeitos de marcação, tais como "Exigir etiqueta e seu valor", para garantir que todos os recursos são criados com etiquetas e para notificá-lo de quaisquer recursos não marcados existentes. Utilize a Azure PowerShell ou a Azure CLI para procurar ou executar ações em recursos baseados nas suas etiquetas.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

- [Restrições de acesso ao serviço de aplicações Azure](/azure/app-service/app-service-ip-restrictions)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o registo de atividade do Azure para monitorizar as configurações dos recursos de rede e detete alterações nas definições de rede e em quaisquer recursos relacionados com o Serviço de Aplicações. 

Aplique uma das várias definições de Azure Policy incorporadas para o Serviço de Aplicações, como uma política que audita aplicações para o uso do serviço de ponto final de rede virtual. Crie alertas dentro do Azure Monitor para desencadear quando ocorrerem alterações nas definições ou recursos críticos da rede. 

Reveja alertas e recomendações de segurança detalhadas no Security Center, no portal ou através de ferramentas programáticas. Exporte esta informação ou envie-a para outras ferramentas de monitorização no seu ambiente. As ferramentas estão disponíveis para exportar alertas e recomendações, manualmente ou de forma contínua e contínua. Com estas ferramentas, pode:
 
- Exportar continuamente para um espaço de trabalho Log Analytics
- Exportação contínua para Azure Event Hubs (para integrações com SIEMs de terceiros)
- Exportação para um ficheiro CSV (uma vez)

Recomenda-se que crie um processo com ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar rapidamente alterações.

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

- [Exportar alertas e recomendações de segurança](../security-center/continuous-export.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registar e monitorizar](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Integre o seu Ambiente de Serviço de Aplicações (ASE) com o Azure Monitor para enviar registos para Azure Storage, Azure Event Hubs ou Log Analytics. Ativar as definições de diagnóstico do registo de registo de atividades Azure para a verificação da auditoria do plano de controlo. Os alertas de segurança do Centro de Segurança são publicados no diário de atividades do Azure. Auditar dados de registo de atividades da Azure, que permitem determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para o Azure App Service e outros recursos Azure. Guarde as suas consultas para utilização futura, os resultados da consulta de pinos para Azure Dashboards e crie alertas de registo. Além disso, utilize a API do Acesso de Dados em Insights de Aplicação para aceder programaticamente à sua telemetria.

Utilize o Microsoft Azure Sentinel, um evento de informação de segurança (SIEM) escalável, nativo da nuvem, disponível para se conectar a várias fontes de dados e conectores, com base nos requisitos do seu negócio. Também pode ativar e a bordo dados de um sistema de gestão de eventos de informação de segurança de terceiros (SIEM), como Barracuda no Azure Marketplace.

- [Atividade de ASE de registo](environment/using-an-ase.md#logging)

- [Como ativar definições de diagnóstico para o serviço de aplicações Azure](troubleshoot-diagnostic-logs.md)

- [Como ativar insights de aplicações](../azure-monitor/app/app-insights-overview.md)

- [Exportar telemetria a partir do Application Insights](../azure-monitor/app/export-telemetry.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Ativar as definições de diagnóstico de registo de registo de atividade azure para o registo de auditoria do plano de controlo do Serviço de Aplicações. Envie os registos para um espaço de trabalho log analytics, Azure Event Hub ou uma conta de Armazenamento Azure.
O "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizados ao nível do plano de controlo pode ser determinado usando dados de registo de atividade Azure para o Serviço de Aplicações e outros recursos Azure.

Além disso, o Azure Key Vault fornece uma gestão secreta centralizada com políticas de acesso e histórico de auditoria. 

- [Como ativar definições de diagnóstico para registo de atividades azure](../azure-monitor/platform/activity-log.md)

- [Como ativar definições de diagnóstico para o serviço de aplicações Azure](troubleshoot-diagnostic-logs.md)

- [Operações do Gestor de Recursos](../role-based-access-control/resource-provider-operations.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: No Azure Monitor, detenva o período de retenção de registos para os espaços de trabalho do Log Analytics associados aos recursos do serviço de aplicações de acordo com os regulamentos de conformidade da sua organização.
- [Como definir parâmetros de retenção de registos](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Reveja as definições de diagnóstico de registo de atividade azure nos seus recursos do Serviço de Aplicações com os registos enviados para um espaço de trabalho log analytics. Realize consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados recolhidos.

Utilize insights de aplicações para as suas aplicações do Serviço de Aplicações e para recolher dados de registo, desempenho e erro. Consulte os dados de telemetria recolhidos pela Application Insights dentro do portal Azure.

Se tiver implementado uma Firewall de Aplicação Web (WAF), pode monitorizar os ataques contra as suas aplicações de Serviço de Aplicações utilizando um registo de Firewall de aplicações web em tempo real. O registo está integrado com o Azure Monitor para rastrear alertas de Firewall de aplicações web e monitorizar facilmente as tendências.

Utilize o Azure Sentinel, uma gestão de eventos de informação de segurança escalável e nativa da nuvem (SIEM), para integrar várias fontes de dados e conectores, de acordo com os requisitos. Opcionalmente, ativar e a bordo dados para uma solução de gestão de eventos de informação de segurança de terceiros no Azure Marketplace.

- [Como ativar as definições de diagnóstico para registo de atividades Azure](../azure-monitor/platform/activity-log.md)

- [Como ativar insights de aplicações](../azure-monitor/app/app-insights-overview.md)

- [Como integrar o seu Ambiente de Serviço de Aplicações com o Gateway de Aplicações Azure (WAF)](environment/integrate-with-application-gateway.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Configure o Centro de Segurança na sua subscrição Azure e reveja os alertas gerados. Utilize o Azure Monitor para obter os seus dados de registo de atividade para um Centro de Eventos onde pode ser lido por uma solução de gestão de eventos de informação de segurança (SIEM), como o Azure Sentinel. 

Monitorize os ataques contra as suas aplicações de Serviço de Aplicações utilizando um registo de Firewall de aplicação web em tempo real com uma Firewall de Aplicação Web Azure (WAF). O registo é integrado com o Azure Monitor para rastrear alertas de Firewall de Aplicações Web (WAF) e monitorizar facilmente as tendências.

- [Como integrar o seu Ambiente de Serviço de Aplicações com o Gateway de Aplicações Azure (WAF)](environment/integrate-with-application-gateway.md)

- [Exportar alertas e recomendações de segurança](../security-center/continuous-export.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e controlo de acessos](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O Azure Ative Directory (Azure AD) tem funções incorporadas que devem ser explicitamente atribuídas e consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?amp;preserve-view=true&view=azureadps-2.0)

- [Como utilizar identidades geridas para o Serviço de Aplicações e Funções Azure](overview-managed-identity.md?context=azure%2Factive-directory%2Fmanaged-identities-azure-resources%2Fcontext%2Fmsi-context&amp;tabs=dotnet)

- [Adicionar ou remover atribuições de funções do Azure com o portal do Azure](../role-based-access-control/role-assignments-portal.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: O Azure Ative Directory (Azure AD) não tem o conceito de palavras-passe padrão. Fornece acesso de avião de controlo ao Serviço de Aplicações.

Geralmente, evite implementar senhas padrão para acesso ao utilizador ao construir as suas próprias aplicações. Utilize um dos fornecedores de identidade disponíveis por padrão para o Serviço de Aplicações, como Azure AD, Microsoft Account, Facebook, Google ou Twitter.

Desative o acesso anónimo, a não ser que precise susteni-lo. 

- [Fornecedores de identidade disponíveis por padrão no Azure App Service](overview-authentication-authorization.md#identity-providers)

- [Autenticação e autorização no Azure App Service e Azure Functions](overview-authentication-authorization.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize as funcionalidades de Gestão de Identidade e Acesso no Centro de Segurança para monitorizar e rastrear o número de contas administrativas. 

Utilize recomendações do Security Center ou das políticas de Azure incorporadas, tais como:

- Deve haver mais de um proprietário atribuído à sua assinatura. 
- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição

Crie um processo para monitorizar as configurações de recursos de rede e detetar alterações nas contas administrativas.

- [Como usar o Centro de Segurança Azure para monitorizar a identidade e o acesso](../security-center/security-center-identity-access.md)

- [Como usar a Política Azure](../governance/policy/tutorials/create-and-manage.md)

- [Saiba mais sobre a concessão de acesso aos utilizadores às aplicações](../role-based-access-control/overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Utilize o Azure Ative Directory single sign-on (SSO)

**Orientação**: Autenticar o Serviço de Aplicações através do Azure Ative Directory (Azure AD). Fornece um serviço OAuth 2.0 para o seu fornecedor de identidade e permite o acesso autorizado a aplicações móveis e web. 

As aplicações do Serviço de Aplicações utilizam identidade federada, na qual um fornecedor de identidade de terceiros gere as identidades do utilizador e o fluxo de autenticação para si. Estes fornecedores de identidade estão disponíveis por padrão:

- Azure AD
- Conta Microsoft

- Facebook

- Google

- Twitter

Quando permite a autenticação e autorização com um destes fornecedores, o seu sinal no ponto final está disponível para autenticação do utilizador e para validação de fichas de autenticação do fornecedor.

- [Compreender a autenticação e autorização no Azure App Service](overview-authentication-authorization.md#identity-providers)

- [Saiba mais sobre autenticação e Autorização no Serviço de Aplicações Azure](overview-authentication-authorization.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação de vários fatores para todos os acessos baseados no Diretório Ativo Azure

**Orientação**: Ative a função de autenticação multifactor no Diretório Ativo Azure (Azure AD) e siga as recomendações de Gestão de Identidade e Acesso no Centro de Segurança.

Implementar a autenticação multifactor para a Azure AD. Os administradores devem garantir que as contas de subscrição no portal estão protegidas. A subscrição é vulnerável a ataques porque gere os recursos que criou. 

- [Azure Security MFA](/previous-versions/azure/security/develop/secure-aad-app)

- [Como ativar o MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Utilizar estações de trabalho seguras e geridas pelo Azure para tarefas administrativas

**Orientação**: Utilize estações de trabalho de acesso privilegiada (PAW) com autenticação multifactor configurada para iniciar sessão e configurar recursos Azure.

- [Saiba mais sobre estações de trabalho de acesso privilegiado](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Como ativar o MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize Gestão de Identidade Privilegiada (PIM) no Diretório Ativo do Azure (Azure AD) para a geração de registos e alertas quando ocorram atividades suspeitas ou inseguras no ambiente.

Além disso, utilize deteções de risco Azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco.

A proteção contra ameaças no Security Center fornece defesas abrangentes para o seu ambiente, que inclui proteção de ameaças para recursos de computação Azure, tais como máquinas Windows, máquinas Linux, Serviço de Aplicações e contentores Azure.

- [Como implementar gestão de identidade privilegiada (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Compreender deteções de risco Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Proteção contra ameaças para recursos computacional Azure](../security-center/azure-defender.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize locais nomeados de acesso condicional para permitir o acesso ao portal Azure a partir de agrupamentos lógicos específicos de intervalos de endereços IP, países ou regiões.

- [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como o sistema central de autenticação e autorização das suas aplicações do Serviço de Aplicações. O Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito e também sais, hashes e armazena de forma segura as credenciais dos utilizadores.

- [Como configurar as suas aplicações do Azure App Service para utilizar o login Azure AD](configure-authentication-provider-aad.md)

- [Como criar e configurar instâncias do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: Descubra contas velhas com os registos fornecidos pela Azure Ative Directory (Azure AD). Use a Azure Identity Access Reviews para gerir eficientemente os membros do grupo e o acesso a aplicações empresariais, bem como atribuições de funções. Reveja periodicamente o acesso do utilizador para garantir que apenas os utilizadores pretendidos têm acesso continuado. 

- [Compreenda a Azure AD reportando](../active-directory/reports-monitoring/index.yml)

- [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como o sistema central de autenticação e autorização das suas aplicações do Serviço de Aplicações. O Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito, sais, hashes e armazenamento seguro de credenciais de utilizador.

O acesso ao sinal Azure AD em fontes de log de atividade, auditoria e evento de risco permite-lhe integrar-se com a Azure Sentinel ou com uma solução de gestão de eventos de informação de segurança de terceiros (SIEM). Agilize o processo criando definições de diagnóstico para contas de utilizadores Azure AD e enviando a auditoria e assinando em registos para um espaço de trabalho Log Analytics. Os alertas de registo desejados podem ser configurados no Log Analytics.

- [Como configurar as suas aplicações do Azure App Service para utilizar o login Azure AD](configure-authentication-provider-aad.md)

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como o sistema central de autenticação e autorização das suas aplicações do Serviço de Aplicações. 

Utilize a Proteção de Identidade Azure AD para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades de utilizador, tais como desvio de comportamento de login de conta no plano de controlo com o portal Azure. Você também pode ingerir dados em Azure Sentinel para mais investigação. 

- [Como configurar a sua app Azure App Service para utilizar o login AZure AD](configure-authentication-provider-aad.md)

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Não disponível para o Serviço de Aplicações. O Lockbox do cliente não é suportado para o Serviço de Aplicações Azure.

- [Lista de serviços suportados pelo Cliente Lockbox](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos do Serviço de Aplicações que armazenam ou processam informações sensíveis.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Para um Ambiente de Serviço de Aplicações, implementar subscrições separadas, grupos de gestão, ou ambos, para ambientes de desenvolvimento, teste e produção. Pode isolar aplicações que processam informações sensíveis de outras aplicações da mesma forma. Implemente a sua aplicação de Serviço de Aplicações numa Rede Virtual. Utilize grupos de segurança de rede e sub-redes para posterior isolamento de aplicações. 

Existem dois tipos de implementação para um ambiente de Serviço de Aplicações (ASE). Ambos permitem isolar o tráfego com base nas suas necessidades comerciais.

- External Application Service Environment - Expõe as aplicações do Serviço de Aplicações num endereço IP acessível à Internet.

-  Ambiente de serviço de aplicação do equilibrador de carga interno (ILB) - Expõe as aplicações do Serviço de Aplicações num endereço IP dentro da sua Rede Virtual. O ponto final interno é um equilibrador de carga interno (ILB), razão pela qual é chamado de ILB ASE. 

Para o Serviço de Aplicações multi-inquilinos (uma aplicação não no nível isolado), utilize a Integração de Rede Virtual para o acesso da sua aplicação a recursos na sua rede Virtual.  Utilize o acesso ao site privado para tornar uma aplicação acessível apenas a partir de uma rede privada, como uma de dentro de uma rede Virtual Azure. A Integração de Rede Virtual é usada apenas para fazer chamadas de saída da sua app para a sua Rede Virtual. A funcionalidade de Integração de Rede Virtual comporta-se de forma diferente quando é utilizada com uma rede virtual na mesma região e com redes virtuais noutras regiões. 
 
- [Considerações sobre a rede para um Ambiente de Serviço de Aplicações](environment/network-info.md)

- [Como criar um ASE externo](environment/create-external-ase.md)

- [Como criar um ASE interno](environment/create-ilb-ase.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Embora as funcionalidades de identificação, classificação e prevenção de perdas ainda não estejam disponíveis para o Serviço de Aplicações, pode reduzir o risco de exfiltração de dados da rede virtual, removendo todas as regras em que o destino utiliza uma 'tag' para serviços de Internet ou Azure. 

A Microsoft gere a infraestrutura subjacente ao Serviço de Aplicações e implementou rigorosos controlos para evitar a perda ou exposição dos seus dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Partilhada

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Utilize a versão mínima padrão do TLS 1.2, configurado nas definições TLS/SSL, para encriptar todas as informações em trânsito. Certifique-se também de que todos os pedidos de ligação HTTP são redirecionados para HTTPS.

- [Compreenda a encriptação em trânsito para aplicações web do Azure App Service](security-recommendations.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: Atualmente não disponível. As funcionalidades de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Serviço de Aplicações. 

Marque aplicações do Serviço de Aplicações que podem estar a processar informações sensíveis. Implementar solução de terceiros, se necessário para efeitos de conformidade.

A Microsoft gere a plataforma subjacente e trata todos os dados dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Partilhada

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Utilizar o controlo de acesso baseado em funções para controlar o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções (Azure RBAC) no Azure Ative Directory (Azure AD) para controlar o acesso ao plano de controlo do Serviço de Aplicações no portal Azure.

- [Como configurar o RBAC no Azure](../role-based-access-control/role-assignments-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Os conteúdos do Web site numa aplicação do Serviço de Aplicações, como ficheiros, são armazenados no Azure Storage, que encripta automaticamente o conteúdo em repouso. Opte por armazenar segredos de aplicações no Key Vault e recuperá-los em tempo de execução.

Os segredos fornecidos pelo cliente são encriptados em repouso enquanto armazenados nas bases de dados de configuração do Serviço de Aplicações.

Note que enquanto os discos anexados localmente podem ser usados opcionalmente por websites como armazenamento temporário, (por exemplo, D:\local e %TMP), eles não são encriptados em repouso.

- [Compreender os controlos de proteção de dados para o Serviço de Aplicações Azure]()

- [Compreenda a encriptação do armazenamento Azure em repouso](../storage/common/storage-service-encryption.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com log de Atividade Azure para criar alertas sobre quaisquer alterações à produção de aplicações do Serviço de Aplicações de produção e outros recursos críticos ou relacionados.

- [Como criar alertas para eventos de Registo de Atividades Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades

*Para obter mais informações, consulte o [Azure Security Benchmark: Gestão de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Adote uma prática de DevSecOps para garantir que as suas aplicações de Serviço de Aplicações estão seguras e permanecem seguras durante todo o seu ciclo de vida. A DevSecOps incorpora a equipa de segurança da sua organização e as suas capacidades nas suas práticas de DevOps, tornando a segurança a responsabilidade de todos na equipa.

Reveja e siga as recomendações do Security Center para garantir as suas aplicações de Serviço de Aplicações.

- [Como adicionar validação contínua de segurança ao seu pipeline CI/CD](/azure/devops/migrate/security-validation-cicd-pipeline?amp;preserve-view=true&view=azure-devops)

- [Como implementar recomendações de avaliação de vulnerabilidade do Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: A Microsoft realiza a gestão de vulnerabilidades nos sistemas subjacentes que suportam o Serviço de Aplicações. No entanto, pode utilizar a gravidade das recomendações dentro do Security Center, bem como a Pontuação Segura para medir o risco dentro do seu ambiente. A sua Pontuação Segura baseia-se na quantidade de recomendações do Centro de Segurança que atenuou. Para priorizar as recomendações para resolver primeiro, considere a gravidade de cada um.

- [Guia de referência de recomendações de segurança](../security-center/recommendations-reference.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e gestão de ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar ou descobrir todos os recursos (tais como computação, armazenamento, rede, portas, protocolos, e assim por diante) dentro das suas subscrições. Certifique-se de que as permissões apropriadas são aplicadas ao seu inquilino e pode enumerar todas as subscrições do Azure, bem como recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Gráfico de Recursos, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription?amp;preserve-view=true&view=azps-4.8.0)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas nos recursos Azure utilizando metadados para organizar logicamente numa taxonomia.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize a marcação, grupos de gestão e assinaturas separadas conforme apropriado, para organizar e rastrear os recursos do Azure. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam removidos das suas subscrições como parte deste processo.

Escolha a Política Azure para impor restrições ao tipo de recursos que podem ser criados nas subscrições de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Reveja os links referenciados para obter informações adicionais.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter o inventário dos recursos aprovados da Azure

**Orientação**: Crie um inventário de recursos Azure aprovados e software aprovado para recursos de computação baseados nas suas necessidades organizacionais.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Use a Política Azure para impor restrições ao tipo de recursos que podem ser criados nas suas subscrições.

Utilize o Gráfico de Recursos Azure para consultar ou descobrir recursos dentro das suas subscrições.  Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados. 

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: Utilize o Azure Resource Graph para consultar ou descobrir recursos dentro das suas subscrições e certifique-se de que os recursos Azure descobertos são aprovados com base nas suas políticas organizacionais.

Utilize o WebJobs no Serviço de Aplicações para monitorizar aplicações de software não aprovadas que são implementadas dentro dos recursos computacional. Utilize WebJobs para executar um programa ou script no mesmo caso que uma aplicação web, app API ou aplicativo móvel. Defina configurações do WebJob e monitorização com registos. Na página 'Detalhes', selecione Toggle Output para ver o texto do conteúdo do registo. Note que os WebJobs ainda não estão suportados para o Serviço de Aplicações em Linux.

- [Executar tarefas de fundo com WebJobs no Azure App Service](webjobs-create.md)

- [Tutorial - Criar e gerir políticas para impor o cumprimento](../governance/policy/tutorials/create-and-manage.md)

- [Quickstart - Execute a sua primeira consulta de Gráfico de Recursos usando o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados. Use a Política Azure para impor restrições ao tipo de recursos que podem ser criados nas suas subscrições. Remova quaisquer aplicações de software implementadas que não tenham sido aprovadas de acordo com as suas políticas organizacionais.

- [Executar tarefas de fundo com WebJobs no Azure App Service](webjobs-create.md)

- [Tutorial - Criar e gerir políticas para impor o cumprimento](../governance/policy/tutorials/create-and-manage.md)

- [Quickstart - Execute a sua primeira consulta de Gráfico de Recursos usando o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados. Use a Política Azure para impor restrições ao tipo de recursos que podem ser criados nas suas subscrições. Remova quaisquer aplicações de software implementadas que não tenham sido aprovadas de acordo com as suas políticas organizacionais. 

- [Executar tarefas de fundo com WebJobs no Azure App Service](webjobs-create.md)

- [Tutorial - Criar e gerir políticas para impor o cumprimento](../governance/policy/tutorials/create-and-manage.md)

- [Quickstart - Execute a sua primeira consulta de Gráfico de Recursos usando o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Crie um processo para rever os serviços Azure não autorizados numa base periódica para garantir que apenas os serviços autorizados da Azure sejam utilizados nas suas subscrições.

Utilize o Azure Resource Graph, neste processo, para consultar ou descobrir recursos dentro das suas subscrições. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

Configure Azure Policy para impor restrições ao tipo de recursos que podem ser criados nas suas subscrições utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Utilize WebJobs no Serviço de Aplicações para monitorizar aplicações de software não aprovadas implementadas dentro de recursos informáticos . Utilize WebJobs para executar um programa ou script no mesmo caso que uma aplicação web, app API ou aplicativo móvel. Defina configurações do WebJob e monitorização com registos. Na página 'Detalhes', selecione Toggle Output para ver o texto do conteúdo do registo. Note que os WebJobs ainda não estão suportados para o Serviço de Aplicações em Linux.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/built-in-policies.md#general)

- [Executar tarefas de fundo com WebJobs no Azure App Service](webjobs-create.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Manter um inventário de títulos de software aprovados

**Orientação**: Implementar um processo de inventário e revisão de títulos de software nas suas subscrições numa base periódica para garantir que apenas os serviços autorizados da Azure são utilizados nas suas subscrições.

Utilize o Azure Resource Graph, neste processo, para consultar ou descobrir recursos dentro das suas subscrições. Certifique-se de que todos os recursos Azure descobertos no ambiente são aprovados.

Configure Azure Policy para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes usando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Da mesma forma, utilize webJobs no Serviço de Aplicações para inventar aplicações de software não aprovadas implementadas dentro de recursos informáticos. Defina a sua configuração e monitorização com registos. Na página 'Detalhes', selecione Toggle Output para ver o texto do conteúdo do registo. Note que os WebJobs ainda não estão suportados para o Serviço de Aplicações em Linux.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/built-in-policies.md#general)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Configurar acesso condicionado Azure para limitar a capacidade dos utilizadores de interagir com o Azure Resource Manager, configurando "Block access" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacional

**Orientação**: WebJobs in App Service permite que os clientes executem um programa ou script no mesmo caso que uma aplicação web, app API ou aplicativo móvel. É responsável por definir a sua configuração para restringir ou limitar quaisquer scripts, que não são permitidos pela organização. O Serviço de Aplicações não fornece um mecanismo para limitar a execução do script de forma nativa. Note que os WebJobs ainda não estão suportados para o Serviço de Aplicações em Linux.

- [Executar tarefas de fundo com WebJobs no Azure App Service](webjobs-create.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: Implementar subscrições ou grupos de gestão separados para proporcionar isolamento para aplicações de Serviço de Aplicações de alto risco. Implementar uma aplicação de maior risco na sua própria Rede Virtual, uma vez que a segurança do perímetro no Serviço de Aplicações é conseguida através do uso de redes virtuais. O App Service Environment é uma implantação do Serviço de Aplicações numa sub-rede na sua Rede Virtual Azure. 

Existem dois tipos de Ambiente de Serviço de Aplicação, Ambiente de Serviço de Aplicação Externa e ILB (Balançador de Carga Interna) Ambiente de Serviço de Aplicação. Escolha a melhor arquitetura com base nas suas necessidades.

- [Considerações sobre a rede para um Ambiente de Serviço de Aplicações](environment/network-info.md)

- [Criar um ambiente de Serviço de Aplicações Externas](environment/create-external-ase.md)

- [Criar e utilizar um Ambiente de Serviço de Aplicação do Balanceador interno](environment/create-ilb-ase.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Defina e implemente configurações de segurança padrão para as aplicações implementadas pelo Serviço de Aplicações com a Política Azure.

Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.Web" para criar políticas personalizadas para auditar ou impor a configuração das suas Aplicações Web do Serviço de Aplicações.

Aplicar definições políticas incorporadas tais como:
- O Serviço de Aplicações deve utilizar um ponto final de serviço de rede virtual
- As aplicações web só devem estar acessíveis em HTTPS

- Utilize a versão TLS mais recente nas suas apps

Recomenda-se que documente o processo para aplicar as definições de política incorporada para utilização normalizada.   

- [Como ver pseudónimos disponíveis da Azure Policy Aliases](/powershell/module/az.resources/get-azpolicyalias?amp;preserve-view=true&view=azps-4.8.0)

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] efeitos para impor definições seguras através das suas aplicações Azure App Service.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

- [Compreender efeitos da política do Azure](../governance/policy/concepts/effects.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Escolha Azure DevOps ou Azure Repos para armazenar e gerir o seu código de forma segura ao utilizar definições personalizadas da Política Azure.

Utilize o pipeline de Integração Contínua (CI) e entrega contínua (CD) existente para implementar uma configuração segura conhecida.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?amp;preserve-view=true&view=azure-devops)

- [Documentação de Azure Repos](/azure/devops/repos/?amp;preserve-view=true&view=azure-devops)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Utilize definições de política de Azure incorporadas, bem como pseudónimos da Política Azure no espaço de nomes "Microsoft.Web" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Desenvolver um processo e um oleoduto para gerir exceções políticas.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize definições de política de Azure incorporadas, bem como pseudónimos da Política Azure no espaço de nomes "Microsoft.Web" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. 

Aplicar a Política Azure [auditoria], [negar], e [implementar se não existir], efeitos para impor automaticamente configurações para os seus recursos Azure.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Utilize identidades geridas para fornecer às suas aplicações de Serviço de Aplicações uma identidade gerida automaticamente no Azure Ative Directory (Azure AD). Identidades geridas permitem que as suas aplicações autentem a autenticação a qualquer serviço que suporte a autenticação Azure AD, incluindo o Key Vault, sem quaisquer credenciais no seu código. Certifique-se de que a eliminação suave está ativada no Cofre da Chave Azure.

- [Como permitir a eliminação suave no Cofre da Chave Azure](../key-vault/general/key-vault-recovery.md)

- [Como utilizar identidades geridas para o Serviço de Aplicações](overview-managed-identity.md)

- [Como fornecer a autenticação do Cofre-Chave com uma identidade gerida](../key-vault/general/assign-access-policy-portal.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Utilize identidades geridas para fornecer às suas aplicações implementadas pelo Serviço de Aplicações uma identidade gerida automaticamente no Azure Ative Directory (Azure AD). Identidades geridas permitem que as suas aplicações autentem a autenticação a qualquer serviço que suporte a autenticação Azure AD, incluindo o Key Vault, sem quaisquer credenciais no seu código.

- [Como utilizar identidades geridas para o Serviço de Aplicações](overview-managed-identity.md)

- [Como fornecer a autenticação do Cofre-Chave com uma identidade gerida](../key-vault/general/assign-access-policy-portal.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner Credencial também encorajará a mudança de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [Azure Security Benchmark: Data Recovery](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir cópias de back-ups automáticas regulares

**Orientação**: A funcionalidade de backup e restauro no Serviço de Aplicações permite-lhe criar facilmente cópias de segurança de aplicações manualmente ou em horário. Pode configurar as cópias de segurança para serem mantidas até um tempo indefinido. Pode restaurar a aplicação para uma imagem instantânea de um estado anterior, sobressacando a aplicação existente ou restaurando para outra aplicação.

O Serviço de Aplicações pode fazer o back up das seguintes informações para uma conta de armazenamento Ezure e um contentor, que configuraram a sua aplicação para utilizar:
- Configuração de aplicações
- Conteúdo do ficheiro
- Base de dados ligada à sua aplicação

Certifique-se de que as cópias de back-ups regulares e automatizadas estão a ocorrer numa frequência definida pelas suas políticas organizacionais.

- [Compreenda a capacidade de backup do Serviço de Aplicações Azure](manage-backup.md)

- [Chaves geridas pelo cliente para encriptação de armazenamento Azure](../storage/common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Utilize a funcionalidade de backup e restauro do Serviço de Aplicações para fazer backup das suas aplicações. As funcionalidades de backup requerem uma Conta de Armazenamento Azure para armazenar as informações de backup da sua aplicação.

- O Azure Storage fornece encriptação em repouso - Utilize chaves fornecidas pelo sistema ou as suas próprias chaves geridas pelo cliente. É aqui que os dados da sua aplicação são armazenados quando não está a ser executada numa aplicação web em Azure.
- Executar a partir de um pacote de implementação é uma funcionalidade de implementação do Serviço de Aplicações. Permite-lhe implementar o conteúdo do seu site a partir de uma Conta de Armazenamento Azure utilizando um URL de Assinatura de Acesso Partilhado (SAS).

- As referências ao Cofre chave são uma funcionalidade de segurança do Serviço de Aplicações. Permite-lhe importar segredos no tempo de execução como configurações de aplicação. Utilize isto para encriptar o URL SAS da sua Conta de Armazenamento Azure.

Mais informações estão disponíveis nos links referenciados.

- [Efetuar cópia de segurança da sua aplicação no Azure](manage-backup.md)

- [Restaurar uma aplicação em execução no Azure App Service](web-sites-restore.md)

- [Compreender a encriptação de dados inativos no Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Modelo de encriptação e tabela de gestão chave](../security/fundamentals/encryption-atrest.md)

- [Encriptação em repouso usando chaves geridas pelo cliente](configure-encrypt-at-rest-using-cmk.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Teste periodicamente o processo de restauro para quaisquer cópias de segurança das suas aplicações do Serviço de Aplicações.

- [Efetuar cópia de segurança da sua aplicação no Azure](manage-backup.md)

- [Como restaurar uma aplicação web do Azure App Service](web-sites-restore.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: As cópias de segurança do Serviço de Aplicações são armazenadas numa conta de Armazenamento Azure. Os dados no Azure Storage são encriptados e desencriptados de forma transparente utilizando encriptação AES de 256 bits, uma das cifras de blocos mais fortes disponíveis, e é compatível com FIPS 140-2. A encriptação de armazenamento Azure é semelhante à encriptação BitLocker no Windows.

A encriptação de armazenamento Azure está ativada para todas as contas de armazenamento, incluindo gestor de recursos e contas de armazenamento clássicas. A encriptação de armazenamento Azure não pode ser desativada. Como os seus dados são protegidos por padrão, não precisa de modificar o seu código ou aplicações para tirar partido da encriptação do Azure Storage.

Por predefinição, os dados numa conta de armazenamento são encriptados com as teclas geridas pela Microsoft. Pode confiar nas teclas geridas pela Microsoft para a encriptação dos seus dados, ou pode gerir a encriptação com as suas próprias chaves. Certifique-se de que a eliminação suave está ativada no Cofre da Chave Azure.

- [Compreenda a encriptação do Armazenamento Azure para os dados em repouso](../storage/common/storage-service-encryption.md)

- [Como permitir a eliminação suave no Cofre da Chave Azure](../key-vault/general/key-vault-recovery.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para mais informações, consulte o [Benchmark de Segurança Azure: Resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Crie um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções de pessoal, bem como fases de tratamento/gestão de incidentes, desde a deteção até à revisão pós-incidente.

- [Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as subscrições (por exemplo, produção, não produção) e crie um sistema de nomeação para identificar e categorizar claramente os recursos da Azure.

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes do seu sistema numa cadência regular. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

- [Consulte a publicação do NIST - Guia para Testes, Formação e Programas de Exercício para Planos e Capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada.  Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Seu Centro de Segurança utilizando a função Exportação Contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Utilize o conector de dados do Security Center para transmitir os alertas Sentinel de acordo com as necessidades do negócio.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança para ativar automaticamente respostas através de "Aplicações lógicas" em alertas e recomendações de segurança.

- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios de equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Pode encontrar mais informações sobre a estratégia e execução da Red Teaming e testes de penetração em sites ao vivo contra infraestruturas, serviços e aplicações geridas pela Microsoft em nuvem.

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="next-steps"></a>Próximos passos

- Consulte a [referência de segurança Azure](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)