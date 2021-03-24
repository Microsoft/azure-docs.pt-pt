---
ms.date: 02/16/2021
ms.topic: reference-architecture
author: kriation
title: Arquitetura de referência da Nuvem de primavera de Azure
ms.author: akaleshian
ms.service: spring-cloud
description: Esta arquitetura de referência é uma fundação usando um centro empresarial típico e design de fala para o uso de Azure Spring Cloud.
ms.openlocfilehash: 74183ca2decf8487e5c41cf36d5784538021077f
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878394"
---
# <a name="azure-spring-cloud-reference-architecture"></a>Arquitetura de referência da Nuvem de primavera de Azure

Esta arquitetura de referência é uma fundação usando um centro empresarial típico e design de fala para o uso de Azure Spring Cloud. No design, a Azure Spring Cloud é implantada num único porta-voz que é dependente de serviços partilhados hospedados no centro. A arquitetura é construída com componentes para alcançar os princípios no [Microsoft Azure Well-Architected Framework.][16]

Para uma implementação desta arquitetura, consulte o repositório de [Arquitetura de Referência da Nuvem de primavera de Azure][10] no GitHub.

As opções de implementação desta arquitetura incluem Azure Resource Manager (ARM), Terraform e Azure CLI. Os artefactos deste repositório fornecem uma base que você pode personalizar para o seu ambiente. Pode agrupar recursos como Azure Firewall ou Application Gateway em diferentes grupos de recursos ou subscrições. Este agrupamento ajuda a manter diferentes funções separadas, tais como infraestruturas de TI, segurança, equipas de aplicação de negócios, e assim por diante.

## <a name="planning-the-address-space"></a>Planejando o espaço de endereço

Azure Spring Cloud requer duas sub-redes dedicadas:

* Tempo de execução do serviço
* Aplicações de Boot de primavera

Cada uma destas subesetas requer um cluster dedicado. Vários agrupamentos não podem partilhar as mesmas sub-redes. O tamanho mínimo de cada sub-rede é /28. O número de casos de aplicação que a Azure Spring Cloud pode suportar varia em com base no tamanho da sub-rede. Pode encontrar os requisitos detalhados da Rede Virtual (VNET) na secção de [requisitos][11] de rede Virtual da Nuvem de [primavera de Deploy Azure numa rede virtual.][17]

> [!WARNING]
> O tamanho da sub-rede selecionada não pode sobrepor-se ao espaço de endereço VNET existente, e não deve sobrepor-se a quaisquer intervalos de endereços de sub-redes espreitados ou no local.

## <a name="use-cases"></a>Casos de utilização

Utilizações típicas desta arquitetura:

* Aplicações internas implantadas em ambientes de nuvem híbrida
* Aplicações viradas para o exterior

Estes casos de utilização são semelhantes, com exceção das suas regras de segurança e de tráfego de rede. Esta arquitetura é projetada para apoiar as nuances de cada um.

## <a name="private-applications"></a>Aplicações privadas

A lista que se segue descreve os requisitos de infraestrutura para aplicações privadas. Estes requisitos são típicos em ambientes altamente regulados.

* Não há saída direta para a Internet pública, exceto para controlar o tráfego de aviões.
* O tráfego de Egress deve passar por um aparelho virtual de rede central (NVA) (por exemplo, Azure Firewall).
* Os dados em repouso devem ser encriptados.
* Os dados em trânsito devem ser encriptados.
* Os oleodutos de implementação de DevOps podem ser utilizados (por exemplo, Azure DevOps) e requerem conectividade de rede à Nuvem de primavera de Azure.
* A abordagem de segurança Zero Trust da Microsoft requer segredos, certificados e credenciais para ser armazenado num cofre seguro. O serviço recomendado é Azure Key Vault.
* Os registos do Serviço de Nome de Domínio (DNS) do anfitrião da aplicação devem ser armazenados em DNS Privados Azure.
* A resolução de nomes dos anfitriões no local e na Nuvem deve ser bidirecional.
* A adesão a pelo menos um Benchmark de segurança deve ser aplicada.
* As dependências de serviços Azure devem comunicar através de Pontos finais de serviço ou link privado.
* Os grupos de recursos geridos pela implementação da Nuvem de primavera Azure não devem ser modificados.
* As sub-redes geridas pela implementação da Nuvem de primavera Azure não devem ser modificadas.
* Uma sub-rede deve ter apenas um exemplo de Azure Spring Cloud.
* Se [o Azure Spring Cloud Config Server][8] for utilizado para carregar propriedades config de um repositório, o repositório deve ser privado.

A lista a seguir mostra os componentes que compõem o design:

* Rede no local
  * Serviço de Nome de Domínio (DNS)
  * Gateway
* Assinatura do hub
  * Sub-rede Azure Firewall
  * Sub-rede gateway de aplicação
  * Sub-rede de serviços partilhados
* Assinatura conectada
  * Peer de rede virtual
  * Sub-rede do Bastião Azure

A seguinte lista descreve os serviços Azure nesta arquitetura de referência:

* [Azure Spring Cloud][1]: um serviço gerido que é projetado e otimizado especificamente para aplicações de Boot de primavera baseadas em Java e . Aplicações [Steeltoe][9] baseadas em NET.

* [Azure Key Vault][2]: um serviço de gestão de credenciais apoiado por hardware que tem uma integração apertada com os serviços de identidade da Microsoft e recursos de computação.

* [Azure Monitor][3]: um conjunto abrangente de serviços de monitorização para aplicações que se destacam tanto no Azure como no local.

* [Azure Security Center][4]: um sistema unificado de gestão de segurança e proteção de ameaças para cargas de trabalho em todos os locais, várias nuvens e Azure.

* [Pipelines Azure][5]: um serviço de integração contínua /desenvolvimento contínuo (CI/CD) totalmente apresentado que pode implementar automaticamente aplicações de Boot de primavera atualizadas para Azure Spring Cloud.

O diagrama a seguir representa um centro bem arquitetado e um desenho de fala que aborda os requisitos acima referidos:

![Diagrama de arquitetura de referência para aplicações privadas](./media/spring-cloud-reference-architecture/architecture-private.png)

## <a name="public-applications"></a>Candidaturas públicas

A lista que se segue descreve os requisitos de infraestrutura para aplicações públicas. Estes requisitos são típicos em ambientes altamente regulados.

* O tráfego de entrada deve ser gerido pelo menos por Gateway de aplicação ou porta frontal Azure.
* A norma de proteção Azure DDoS deve ser ativada.
* Não há saída direta para a Internet pública, exceto para controlar o tráfego de aviões.
* O tráfego de Egress deve atravessar um aparelho virtual de rede central (NVA) (por exemplo, Azure Firewall).
* Os dados em repouso devem ser encriptados.
* Os dados em trânsito devem ser encriptados.
* Os oleodutos de implementação de DevOps podem ser utilizados (por exemplo, Azure DevOps) e requerem conectividade de rede à Nuvem de primavera de Azure.
* A abordagem de segurança Zero Trust da Microsoft requer segredos, certificados e credenciais para ser armazenado num cofre seguro. O serviço recomendado é Azure Key Vault.
* Os registos DNS do anfitrião da aplicação devem ser armazenados no Azure Private DNS.
* Os endereços de encaminhamento de Internet devem ser armazenados em DNS públicos do Azure.
* A resolução de nomes dos anfitriões no local e na Nuvem deve ser bidirecional.
* A adesão a pelo menos um Benchmark de segurança deve ser aplicada.
* As dependências de serviços Azure devem comunicar através de Pontos finais de serviço ou link privado.
* Os grupos de recursos geridos pela implementação da Nuvem de primavera Azure não devem ser modificados.
* As sub-redes geridas pela implementação da Nuvem de primavera Azure não devem ser modificadas.
* Uma sub-rede deve ter apenas um exemplo de Azure Spring Cloud.

A lista a seguir mostra os componentes que compõem o design:

* Rede no local
  * Serviço de Nome de Domínio (DNS)
  * Gateway
* Assinatura do hub
  * Sub-rede Azure Firewall
  * Sub-rede gateway de aplicação
  * Sub-rede de serviços partilhados
* Assinatura conectada
  * Peer de rede virtual
  * Sub-rede do Bastião Azure

A seguinte lista descreve os serviços Azure nesta arquitetura de referência:

* [Azure Spring Cloud][1]: um serviço gerido que é projetado e otimizado especificamente para aplicações de Boot de primavera baseadas em Java e . Aplicações [Steeltoe][9] baseadas em NET.

* [Azure Key Vault][2]: um serviço de gestão de credenciais apoiado por hardware que tem uma integração apertada com os serviços de identidade da Microsoft e recursos de computação.

* [Azure Monitor][3]: um conjunto abrangente de serviços de monitorização para aplicações que se destacam tanto no Azure como no local.

* [Azure Security Center][4]: um sistema unificado de gestão de segurança e proteção de ameaças para cargas de trabalho em todos os locais, várias nuvens e Azure.

* [Pipelines Azure][5]: um serviço de integração contínua /desenvolvimento contínuo (CI/CD) totalmente apresentado que pode implementar automaticamente aplicações de Boot de primavera atualizadas para Azure Spring Cloud.

* [Azure Application Gateway][6]: um equilibrador de carga responsável pelo tráfego de aplicações com segurança da camada de transporte (TLS) que opera na camada 7.

* [Azure Application Firewall][7]: uma característica do Azure Application Gateway que fornece proteção centralizada de aplicações contra explorações e vulnerabilidades comuns.

O diagrama a seguir representa um centro bem arquitetado e um desenho de fala que aborda os requisitos acima referidos:

![Diagrama de arquitetura de referência para aplicações públicas](./media/spring-cloud-reference-architecture/architecture-public.png)

## <a name="azure-spring-cloud-on-premises-connectivity"></a>Conectividade Azure Spring Cloud no local

As aplicações em execução em Azure Spring Cloud podem comunicar a vários Azure, no local e recursos externos. Utilizando o design do hub e da fala, as aplicações podem encaminhar o tráfego externamente ou para a rede no local utilizando a Via Expresso ou a Rede Virtual Privada Local-A-Site (VPN).

## <a name="azure-well-architected-framework-considerations"></a>Considerações do Quadro Azure Well-Architected

O [Quadro Azure Well-Architected][16] é um conjunto de princípios orientadores a seguir na criação de uma forte base de infraestruturas. O quadro contém as seguintes categorias: otimização de custos, excelência operacional, eficiência de desempenho, fiabilidade e segurança.

### <a name="cost-optimization"></a>Otimização de custos

Devido à natureza do design do sistema distribuído, a expansão das infraestruturas é uma realidade. Esta realidade resulta em custos inesperados e incontroláveis. A Azure Spring Cloud é construída usando componentes que escalam para que possa atender a procura e otimizar o custo. O núcleo desta arquitetura é o Serviço Azure Kubernetes (AKS). O serviço destina-se a reduzir a complexidade e a sobrecarga operacional da gestão da Kubernetes, que inclui eficiências no custo operacional do cluster.

Pode implementar diferentes aplicações e tipos de aplicações para uma única instância de Azure Spring Cloud. O serviço suporta a autoscalização de aplicações desencadeadas por métricas ou horários que podem melhorar a utilização e a eficiência de custos.

Também pode utilizar o Application Insights e o Azure Monitor para reduzir os custos operacionais. Com a visibilidade fornecida pela solução de registo abrangente, pode implementar a automatização para escalar os componentes do sistema em tempo real. Também pode analisar dados de registo para revelar ineficiências no código de aplicação que pode abordar para melhorar o custo geral e o desempenho do sistema.

### <a name="operational-excellence"></a>Excelência operacional

A Azure Spring Cloud aborda vários aspetos da excelência operacional. Pode combinar estes aspetos para garantir que o serviço funciona de forma eficiente em ambientes de produção, conforme descrito na seguinte lista:

* Pode utilizar os Gasodutos Azure para garantir que as implementações são fiáveis e consistentes, ajudando-o a evitar erros humanos.
* Pode utilizar o Azure Monitor e Application Insights para armazenar dados de registo e telemetria.
  Pode avaliar os dados de registo e métrica recolhidos para garantir a saúde e o desempenho das suas aplicações. A Monitorização do Desempenho da Aplicação (APM) está totalmente integrada no serviço através de um agente Java. Este agente fornece visibilidade em todas as aplicações e dependências implementadas sem exigir código extra. Para obter mais informações, consulte o blog post [Sem esforço monitorizar aplicações e dependências em Azure Spring Cloud][15].
* Pode utilizar o Azure Security Center para garantir que as aplicações mantêm a segurança, fornecendo uma plataforma para analisar e avaliar os dados fornecidos.
* O serviço suporta vários padrões de implementação. Para obter mais informações, consulte [Configurar um ambiente de preparação na Nuvem de primavera de Azure.][14]

### <a name="reliability"></a>Fiabilidade

Azure Spring Cloud é projetado com AKS como um componente fundamental. Enquanto a AKS proporciona um nível de resiliência através do agrupamento, esta arquitetura de referência incorpora serviços e considerações arquitetónicas para aumentar a disponibilidade da aplicação em caso de falha de componentes.

Construindo em cima de um centro bem definido e design de fala, a fundação desta arquitetura garante que você pode implantá-lo em várias regiões. Para o caso de uso de aplicações privadas, a arquitetura utiliza o Azure Private DNS para garantir a disponibilidade contínua durante uma falha geográfica. Para o caso de uso de aplicações públicas, Azure Front Door e Azure Application Gateway asseguram a disponibilidade.

### <a name="security"></a>Segurança

A segurança desta arquitetura é abordada pela sua adesão a controlos e referências definidos pela indústria. Os controlos nesta arquitetura são da Matriz de Controlo de [Nuvens][19] (CCM) pela [Cloud Security Alliance][18] (CSA) e do Microsoft [Azure Foundations Benchmark][20] (MAFB) pelo Center for Internet [Security][21] (CIS). Nos controlos aplicados, o foco está nos princípios primários de conceção de segurança da governação, networking e segurança das aplicações. É da sua responsabilidade lidar com os princípios de design de Identidade, Gestão de Acesso e Armazenamento, uma vez que se relacionam com a sua infraestrutura-alvo.

#### <a name="governance"></a>Governação

O principal aspeto da governação que esta arquitetura aborda é a segregação através do isolamento dos recursos de rede. No CCM, o DCS-08 recomenda o controlo de entradas e saídas para o datacenter. Para satisfazer o controlo, a arquitetura usa um hub e um design de fala usando Grupos de Segurança de Rede (NSGs) para filtrar o tráfego leste-oeste entre recursos. A arquitetura também filtra o tráfego entre os serviços centrais no centro e os recursos na fala. A arquitetura usa um exemplo de Azure Firewall para gerir o tráfego entre a Internet e os recursos dentro da arquitetura.

A lista a seguir mostra o controlo que aborda a segurança do datacenter nesta referência:

| CSA CCM Control ID | Domínio de controlo do CCM CSA |
| :----------------- | :----------------------|
| DCS-08 | Entrada de Pessoas Não Autorizadas de Segurança do Datacenter |

#### <a name="network"></a>Rede

O design de rede que suporta esta arquitetura é derivado do centro tradicional e do modelo de fala. Esta decisão garante que o isolamento da rede é uma construção fundamental. O controlo CCM IVS-06 recomenda que o tráfego entre redes e máquinas virtuais seja restringido e monitorizado entre ambientes fidedignos e não fidedignos. Esta arquitetura adota o controlo através da implementação dos NSGs para o tráfego este-oeste, e o Firewall Azure para o tráfego norte-sul. O controlo da CCM IPY-04 recomenda que a infraestrutura utilize protocolos de rede seguros para a troca de dados entre serviços. Os serviços Azure que suportam esta arquitetura utilizam protocolos padrão seguros, tais como TLS para HTTP e SQL.

A lista que se segue mostra os controlos de CCM que abordam a segurança da rede nesta referência:

| CSA CCM Control ID | Domínio de controlo do CCM CSA |
| :----------------- | :----------------------|
| IPY-04             | Protocolos de Rede      |
| IVS-06             | Segurança de Rede       |

A implementação da rede é ainda assegurada através da definição de controlos do MAFB. Os controlos asseguram que o tráfego para o ambiente seja restringido à Internet pública.

A lista que se segue mostra os controlos do SIA que abordam a segurança da rede nesta referência:

| ID de controlo do CIS | Descrição do controlo do CIS |
| :------------- | :---------------------- |
| 6.2 | Certifique-se de que o acesso ao SSH é restrito à internet. |
| 6.3 | Certifique-se de que nenhuma base de dados SQL permite a entrada 0.0.0.0/0 (QUALQUER IP). |
| 6.5 | Certifique-se de que o Observador de Rede está "Ativado". |
| 6.6 | Certifique-se de que a entrada que utiliza a UDP é restrita da internet. |

A Azure Spring Cloud requer tráfego de gestão para a saída de Azure quando implantado em um ambiente seguro. Para isso, tem de permitir as regras de rede e aplicação enumeradas nas [responsabilidades do Cliente para executar a Azure Spring Cloud em VNET](./spring-cloud-vnet-customer-responsibilities.md).

#### <a name="application-security"></a>Segurança de aplicações

Este diretor de design abrange os componentes fundamentais da identidade, proteção de dados, gestão de chaves e configuração de aplicação. Por design, uma aplicação implementada em Azure Spring Cloud funciona com o mínimo privilégio necessário para funcionar. O conjunto de controlos de autorização está diretamente relacionado com a proteção de dados durante a utilização do serviço. A gestão chave fortalece esta abordagem de segurança de aplicações em camadas.

A lista que se segue mostra os controlos de CCM que abordam a gestão das chaves nesta referência:

| CSA CCM Control ID | Domínio de controlo do CCM CSA |
| :----------------- | :--------------------- |
| EKM-01 | Direito à encriptação e gestão de chaves |
| EKM-02 | Geração chave de encriptação e gestão de chaves |
| EKM-03 | Encriptação e Proteção de Dados Sensíveis à Gestão de Chaves |
| EKM-04 | Encriptação e Armazenamento e Acesso à Gestão de Chaves |

A partir do CCM, EKM-02 e EKM-03 recomendam políticas e procedimentos para gerir chaves e usar protocolos de encriptação para proteger dados sensíveis. O EKM-01 recomenda que todas as chaves criptográficas tenham proprietários identificáveis para que possam ser geridas. O EKM-04 recomenda a utilização de algoritmos padrão.

A lista que se segue mostra os controlos do SEI que abordam a gestão das chaves nesta referência:

| ID de controlo do CIS | Descrição do controlo do CIS |
| :------------- | :---------------------- |
| 8.1 | Certifique-se de que a data de validade está definida em todas as teclas. |
| 8.2 | Certifique-se de que a data de validade está definida em todos os segredos. |
| 8,4 | Certifique-se de que o cofre da chave é recuperável. |

Os controlos do SIA 8.1 e 8.2 recomendam que sejam fixadas datas de validade para credenciais que garantam a aplicação da rotação. O controlo do CIS 8.4 garante que o conteúdo do cofre chave pode ser restaurado para manter a continuidade do negócio.

Os aspetos da segurança das aplicações estabeleceram as bases para a utilização desta arquitetura de referência para apoiar uma carga de trabalho da primavera em Azure.

## <a name="next-steps"></a>Passos seguintes

Explore esta arquitetura de referência através das implementações ARM, Terraform e Azure CLI disponíveis no repositório de Referência da Nuvem de [primavera Azure.][10]

<!-- Reference links in article -->
[1]: ./index.yml
[2]: ../key-vault/index.yml
[3]: ../azure-monitor/index.yml
[4]: ../security-center/index.yml
[5]: /azure/devops/pipelines/
[6]: ../application-gateway/index.yml
[7]: ../web-application-firewall/index.yml
[8]: ./spring-cloud-tutorial-config-server.md
[9]: https://steeltoe.io/
[10]: https://github.com/Azure/azure-spring-cloud-reference-architecture
[11]: ./spring-cloud-tutorial-deploy-in-azure-virtual-network.md#virtual-network-requirements
[12]: ./spring-cloud-vnet-customer-responsibilities.md#azure-spring-cloud-network-requirements
[13]: ./spring-cloud-vnet-customer-responsibilities.md#azure-spring-cloud-fqdn-requirements--application-rules
[14]: ./spring-cloud-howto-staging-environment.md
[15]: https://devblogs.microsoft.com/java/monitor-applications-and-dependencies-in-azure-spring-cloud/
[16]: /azure/architecture/framework/
[17]: ./spring-cloud-tutorial-deploy-in-azure-virtual-network.md#virtual-network-requirements
[18]: https://cloudsecurityalliance.org/
[19]: https://cloudsecurityalliance.org/research/working-groups/cloud-controls-matrix
[20]: /azure/security/benchmarks/v2-cis-benchmark
[21]: https://www.cisecurity.org/