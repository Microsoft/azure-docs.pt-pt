---
title: Segurança operacional melhores práticas do Azure | Documentos da Microsoft
description: Este artigo fornece um conjunto de melhores práticas da segurança operacional do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: terrylan
ms.openlocfilehash: e2678eb7d75921f43a1e51b6a8cefc9925a9adc1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587024"
---
# <a name="azure-operational-security-best-practices"></a>Segurança operacional melhores práticas do Azure
Segurança operacional do Azure refere-se a serviços, controles e recursos disponíveis aos utilizadores para proteger seus dados, aplicativos e outros recursos no Azure. Segurança operacional do Azure baseia-se uma estrutura que incorpore os dados recolhidos através de capacidades que são exclusivas da Microsoft, incluindo o [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl), o [Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) programa e profundo entendimento do cenário de ameaças de cibersegurança.

Neste artigo, discutimos a uma coleção de melhores práticas de segurança. Essas práticas recomendadas são derivadas da nossa experiência com a segurança da base de dados do Azure e as experiências dos clientes, como mesmo.

Para cada melhor prática, Explicamos:
-   O que é a melhor prática
-   Por que pretende ativar essa prática recomendada
-   Se efetuar a ativação para ativar a melhor prática, o que pode ser o resultado
- Como pode aprender permitir a melhor prática

Este artigo Azure operacional melhores práticas de segurança baseia-se numa opinião de consenso e capacidades da plataforma Azure e conjuntos de recursos, tal como existem no momento que este artigo foi escrito. Opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Monitorizar os serviços de armazenamento para alterações de comportamento inesperados
Diagnosticar e resolver problemas numa aplicação distribuída alojada num ambiente de nuvem podem ser mais complexas do que está a ser ambientes tradicionais. Aplicativos podem ser implementados numa infraestrutura PaaS ou IaaS, no local, num dispositivo móvel, ou em alguma combinação destes ambientes. Tráfego de rede da sua aplicação poderá percorrer a redes públicas e privadas e seu aplicativo pode usar várias tecnologias de armazenamento.

Deve monitorizar continuamente os serviços de armazenamento que a aplicação utiliza para efetuar quaisquer alterações inesperadas no comportamento (por exemplo, tempos de resposta mais lentos). Utilize o registo para recolher dados de mais detalhados e analisar um problema em profundidade. As informações de diagnóstico que obter de monitorização e registo ajudam-o a determinar a causa de raiz do problema que encontrou a sua aplicação. Em seguida, pode resolver o problema e determinar os passos adequados para resolvê-lo.

[Análise de armazenamento do Azure](../storage/storage-analytics.md) efetua o registo e fornece dados de métricas para uma conta de armazenamento do Azure. Recomendamos que utilize estes dados para rastrear pedidos, analisar tendências de utilização e diagnosticar problemas com a sua conta de armazenamento.

## <a name="prevent-detect-and-respond-to-threats"></a>Prevenir, detetar e responder a ameaças
[Centro de segurança do Azure](../security-center/security-center-intro.md) ajuda-o a prevenir, detetar e responder a ameaças com maior visibilidade (e controlo sobre) a segurança dos seus recursos do Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com várias soluções de segurança.

O escalão gratuito do Centro de segurança oferece segurança limitada para apenas os recursos do Azure. O escalão Standard expande estas capacidades no local e noutras clouds. O Centro de Segurança Standard ajuda a localizar e corrigir vulnerabilidades de segurança, aplicar controlos de acesso e aplicação para bloquear atividades maliciosas, detetar ameaças com análise e inteligência, e a responder rapidamente quando sob ataque. Pode experimentar o Centro de Segurança Standard sem custos durante os primeiros 60 dias. Recomendamos que [carregar sua subscrição do Azure para o Centro de segurança Standard](../security-center/security-center-get-started.md).

Utilize o Centro de segurança para obter uma visão central do Estado de segurança de todos os seus recursos do Azure. Visão geral, certifique-se de que os controlos de segurança adequados estão implementados e configurado corretamente e identifiquem rapidamente quaisquer recursos que necessitam de atenção.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Monitorizar a monitorização de rede com base no cenário de ponto a ponto
Os clientes criam uma rede ponto a ponto no Azure através da combinação de recursos de rede, como uma rede virtual, ExpressRoute, o Gateway de aplicação e Balanceadores de carga. A monitorização está disponível em cada um dos recursos de rede.

[O observador de rede do Azure](../network-watcher/network-watcher-monitoring-overview.md) é um serviço regional. Utilize as suas ferramentas de diagnóstico e de visualização para monitorizar e diagnosticar condições a um nível de cenário de rede no, a e do Azure.

Seguem-se as práticas recomendadas para as ferramentas de monitorização e de disponibilidade de rede.

**Melhor prática**: Automatize a monitorização remota de redes com captura de pacotes.  
**Detalhe**: Monitorizar e diagnosticar problemas de rede sem iniciar sessão nas VMS com o observador de rede. Acionador [captura de pacotes](../network-watcher/network-watcher-alert-triggered-packet-capture.md) ao definir alertas e obter acesso a informações de desempenho em tempo real ao nível do pacote. Quando se deparar com um problema, pode investigar em pormenor para obter melhores diagnósticos.

**Melhor prática**: Obtenha informações sobre o seu tráfego de rede ao utilizar os registos de fluxo.  
**Detalhe**: Criar padrões de tráfego de uma compreensão mais aprofundada da sua rede usando [registos de fluxo do grupo de segurança de rede](../network-watcher/network-watcher-nsg-flow-logging-overview.md). Informações nos registos de fluxo ajudam-o a recolher dados de conformidade, auditoria e monitorização do seu perfil de segurança de rede.

**Melhor prática**: Diagnostica problemas de conectividade VPN.  
**Detalhe**: Utilizar o observador de rede para [diagnosticar os problemas mais comuns de Gateway de VPN e ligação](../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Pode não só identificar o problema, mas também utilizar registos detalhados para continuar a investigar.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Proteger a implementação com as ferramentas de DevOps comprovadas
Utilize as seguintes práticas recomendadas do DevOps para se certificar de que suas equipas de enterprise e produtiva e eficiente.

**Melhor prática**: Automatize a compilação e implementação de serviços.  
**Detalhe**: [Infraestrutura como código](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) é um conjunto de técnicas e práticas recomendadas que ajudam os profissionais de TI remover o fardo da compilação diária e a gestão de infraestrutura modular. Permite que os profissionais de TI criar e manter seu ambiente de servidor modernas de forma que é semelhante a como os desenvolvedores de software criar e manter o código da aplicação.

Pode usar [do Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) para aprovisionar as suas aplicações ao utilizar um modelo declarativo. Num único modelo, pode implementar vários serviços, bem como as respetivas dependências. Utilize o mesmo modelo para implementar repetidamente a sua aplicação em cada fase do ciclo de vida do aplicativo.

**Melhor prática**: Automaticamente a criar e implementar aplicações web do Azure ou serviços em nuvem.  
**Detalhe**: Pode utilizar Pipelines do Azure para [automaticamente, criar e implementar](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts) para aplicações web do Azure ou serviços cloud. Pipelines do Azure implementa automaticamente os binários depois de fazer uma compilação para o Azure após cada check-in de código. O processo de compilação do pacote é equivalente ao comando do pacote no Visual Studio e os passos de publicação são equivalentes para o comando Publish no Visual Studio.

**Melhor prática**: Utilize a implementação contínua.  
**Detalhe**: [Pipelines do Azure](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts) é uma solução para automatizar a implementação de vários estágios e gerenciar o processo de lançamento. Crie pipelines de implementação contínua gerido para libertar rapidez, facilidade e, muitas vezes. Com os Pipelines do Azure, pode automatizar o processo de liberação e pode predefinida fluxos de trabalho de aprovação. Implemente no local e na nuvem, expandir e personalizar conforme necessário.

**Melhor prática**: Verifique o desempenho da sua aplicação antes de iniciá-lo ou implementar atualizações em produção.  
**Detalhe**: Executar com base na cloud [testes de carga](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) ao utilizar planos de teste do Azure para:

- Encontre problemas de desempenho na sua aplicação.
- Melhore a qualidade de implementação.
- Certifique-se de que a aplicação está sempre disponível.
- Certifique-se de que a aplicação consegue processar o tráfego para a sua campanha de lançamento ou de marketing seguinte.

**Melhor prática**: Monitorizar o desempenho do aplicativo.  
**Detalhe**: [O Azure Application Insights](../azure-monitor/app/app-insights-overview.md) é um serviço de gestão (APM) de desempenho de aplicações extensível para desenvolvedores da web em várias plataformas. Utilize o Application Insights para monitorizar a sua aplicação web em direto. Deteta automaticamente anomalias de desempenho. Ele inclui ferramentas de análise para ajudar a diagnosticar problemas e para compreender o que os utilizadores fazem realmente com a sua aplicação. Foi concebido para o ajudar a melhorar continuamente o desempenho e a usabilidade.

## <a name="mitigate-and-protect-against-ddos"></a>Mitigar e proteção contra DDoS
Ataques denial of service (DDoS) é um tipo de ataque que tenta a esgotar os recursos de aplicativos. O objetivo é afetar a disponibilidade da aplicação e a sua capacidade de processar pedidos legítimos. Esses ataques estão se tornando mais sofisticados e maior tamanho e o impacto. Eles podem ser direcionados para qualquer ponto final que está publicamente acessível através da internet.

Projetando e criando para resiliência contra DDoS requerem um planeamento e conceção para uma variedade de modos de falha.

Seguem-se as práticas recomendadas para criação de serviços de DDoS resilientes no Azure.

**Melhor prática**: Certifique-se de que a segurança é uma prioridade ao longo de todo o ciclo de vida de um aplicativo, desde a conceção e implementação para implantação e operações. As aplicações podem ter bugs que permitem que um volume relativamente baixo de pedidos para utilizar muitos recursos, resultando numa indisponibilidade do serviço.  
**Detalhe**: Para ajudar a proteger um serviço em execução no Microsoft Azure, deve ter uma boa compreensão da sua arquitetura de aplicativo e enfocar o [cinco pilares de qualidade de software](https://docs.microsoft.com/azure/architecture/guide/pillars). Deve saber volumes de tráfego típico, o modelo de conectividade entre a aplicação e outras aplicações e os pontos finais de serviço são expostos à Internet pública.

É mais importante garantir que um aplicativo é resiliente para lidar com uma negação de serviço que é direcionada para a própria aplicação. Segurança e a privacidade são incorporadas na plataforma do Azure, começando com o [Security Development Lifecycle (SDL)](https://www.microsoft.com/en-us/sdl). O SDL aborda a segurança em todas as fases de desenvolvimento e garante que o Azure é continuamente atualizado para que seja ainda mais seguro.

**Melhor prática**: Conceber a sua aplicação para [Dimensionar horizontalmente](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) para satisfazer a procura de uma carga amplified, especificamente no caso de um ataque DDoS. Se seu aplicativo depende de uma única instância de um serviço, ele cria um ponto único de falha. Várias instâncias de aprovisionamento torna o seu sistema, mais escalonável e mais resiliente.  
**Detalhe**: Para [App Service do Azure](../app-service/app-service-value-prop-what-is.md), selecione um [plano do App Service](../app-service/overview-hosting-plans.md) que oferece várias instâncias.

Serviços Cloud do Azure, configure cada uma das suas funções para utilizar [várias instâncias](../cloud-services/cloud-services-choose-me.md).

Para [máquinas virtuais do Azure](../virtual-machines/windows/overview.md), certifique-se de que a sua arquitetura VM inclui mais de uma VM e que cada VM está incluído num [conjunto de disponibilidade](../virtual-machines/virtual-machines-windows-manage-availability.md). É recomendável utilizar o dimensionamento de máquinas virtuais conjuntos para capacidades de dimensionamento automático.

**Melhor prática**: Colocação de defesas de segurança num aplicativo reduz a chance de um ataque bem-sucedido. Implementar designs seguros para as suas aplicações com os recursos internos da plataforma do Azure.  
**Detalhe**: O risco de ataque aumenta com o tamanho (área de superfície) do aplicativo. Pode reduzir a área de superfície ao utilizar listas de permissões para fechar o espaço de endereços IP exposto e escutar as portas que não são necessários em balanceadores de carga ([Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md) e [oGatewaydeaplicaçãodoAzure](../application-gateway/application-gateway-create-probe-portal.md)).

[Grupos de segurança de rede](../virtual-network/security-overview.md) são outra maneira de reduzir a superfície de ataque. Pode usar [etiquetas de serviço](../virtual-network/security-overview.md#service-tags) e [grupos de segurança de aplicativo](../virtual-network/security-overview.md#application-security-groups) para minimizar a complexidade para criar as regras de segurança e configuração de segurança de rede, como uma extensão natural da estrutura de uma aplicação.

Deve implantar os serviços do Azure numa [rede virtual](../virtual-network/virtual-networks-overview.md) sempre que possível. Esta prática permite que os recursos de serviço para comunicar através de endereços IP privados. Por predefinição, o tráfego de serviço do Azure de uma rede virtual utiliza endereços IP públicos como endereços IP de origem.

Usando [pontos finais de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) comutadores tráfego para utilizar endereços privados da rede virtual como endereços IP de origem, quando estão a aceder ao serviço do Azure de uma rede virtual do serviço.

Vemos com freqüência dos clientes recursos no local ataques juntamente com seus recursos no Azure. Se estiver a ligar um ambiente no local para o Azure, minimize a exposição dos recursos no local para a internet pública.

O Azure tem dois DDoS [ofertas de serviço](../virtual-network/ddos-protection-overview.md) que fornecer proteção contra ataques à rede:

- Proteção básica está integrada no Azure, por padrão, sem custos adicionais. O dimensionamento e a capacidade da rede implementada globalmente do Azure fornece defesa contra ataques comuns de camada de rede por meio de atenuação de monitorização e em tempo real de tráfego sempre ativa. Basic não requer nenhuma alteração de configuração ou aplicação do usuário e ajuda a proteger todos os serviços do Azure, incluindo serviços de PaaS como o DNS do Azure.
- Proteção padrão fornece capacidades avançadas de mitigação de DDoS contra ataques de rede. Ele automaticamente é ajustado para proteger os seus recursos do Azure específicos. Proteção é simples para ativar durante a criação de redes virtuais. Ele também pode ser feito após a criação e exige sem alterações de aplicação ou recurso.

## <a name="next-steps"></a>Passos Seguintes
Ver [padrões e práticas recomendadas de segurança do Azure](security-best-practices-and-patterns.md) para obter mais melhores práticas de segurança a utilizar quando estiver conceber, implementar e gerir soluções na cloud ao utilizar o Azure.

Os seguintes recursos estão disponíveis para fornecer informações mais gerais acerca da segurança do Azure e serviços Microsoft relacionados:
* [Blogue de equipa de segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – para obter informações atualizadas sobre as mais recentes da segurança do Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – onde podem ser comunicadas as vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, ou por e-mail para secure@microsoft.com
