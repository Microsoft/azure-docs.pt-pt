---
title: Segurança para casos de contentores
description: Recomendações para garantir imagens e segredos para as instâncias de contentores do Azure e considerações gerais de segurança para qualquer plataforma de contentores
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: 87fa28cf9bdb546a5f108284023a9f787645a1fd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457999"
---
# <a name="security-considerations-for-azure-container-instances"></a>Considerações de segurança para instâncias de contentores azure

Este artigo introduz considerações de segurança para usar as instâncias de contentores Azure para executar aplicações de contentores. Os tópicos incluem:

> [!div class="checklist"]
> * **Recomendações de segurança** para gestão de imagens e segredos para instâncias de contentores do Azure
> * **Considerações para o ecossistema de contentores** ao longo do ciclo de vida dos contentores, para qualquer plataforma de contentores

## <a name="security-recommendations-for-azure-container-instances"></a>Recomendações de segurança para instâncias de contentores azure

### <a name="use-a-private-registry"></a>Use um registo privado

Os contentores são criados a partir de imagens que estão armazenadas num ou mais repositórios. Estes repositórios podem pertencer a um registo público, como [o Docker Hub,](https://hub.docker.com)ou a um registo privado. Um exemplo de um registo privado é o [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/), que pode ser instalado no local ou numa cloud privada virtual. Também pode utilizar serviços de registo de contentores privados baseados na nuvem, incluindo o Registo de [Contentores Azure.](../container-registry/container-registry-intro.md) 

Uma imagem de contentor publicamente disponível não garante a segurança. As imagens de contentores consistem em várias camadas de software, e cada camada de software pode ter vulnerabilidades. Para ajudar a reduzir a ameaça de ataques, deve armazenar e recuperar imagens de um registo privado, como o Registo de Contentores Azure ou o Registo De Confiança de Docker. Além de fornecer um registo privado gerido, o Registo de Contentores Azure suporta a [autenticação baseada no serviço principal](../container-registry/container-registry-authentication.md) através do Diretório Ativo Azure para fluxos básicos de autenticação. Esta autenticação inclui acesso baseado em papéis para leitura (puxar), escrever (empurrar) e outras permissões.

### <a name="monitor-and-scan-container-images"></a>Monitor e digitalizam imagens de contentores

Aproveite as soluções para digitalizar imagens de contentores num registo privado e identificar potenciais vulnerabilidades. É importante entender a profundidade da deteção de ameaças que as diferentes soluções fornecem.

Por exemplo, o Registo de Contentores Azure [integra-se](../security-center/azure-container-registry-integration.md) opcionalmente com o Azure Security Center para digitalizar automaticamente todas as imagens linux empurradas para um registo. O scanner integrado da Qualys do Azure Security Center deteta vulnerabilidades de imagem, classifica-as e fornece orientação de reparação.

Soluções de monitorização de segurança e digitalização de imagens, como [twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) e [Aqua Security,](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) também estão disponíveis através do Azure Marketplace.  

### <a name="protect-credentials"></a>Proteger credenciais

Os contentores podem espalhar-se por vários aglomerados e regiões de Azure. Por isso, deve assegurar as credenciais necessárias para logins ou acesso a API, tais como senhas ou fichas. Certifique-se de que apenas os utilizadores privilegiados podem aceder a esses contentores em trânsito e em repouso. Inventarie todos os segredos credenciais e, em seguida, exija que os desenvolvedores utilizem ferramentas emergentes de gestão de segredos que são projetadas para plataformas de contentores.  Certifique-se de que a sua solução inclui bases de dados encriptadas, encriptação TLS para dados de segredos em trânsito e controlo de [acesso baseado em papéis](../role-based-access-control/overview.md)de menor privilégio . [Azure Key Vault](../key-vault/general/secure-your-key-vault.md) é um serviço na nuvem que salvaguarda chaves e segredos de encriptação (tais como certificados, cordas de ligação e senhas) para aplicações contentorizadas. Como estes dados são sensíveis e empresariais críticos, o acesso seguro aos seus cofres chave para que apenas as aplicações autorizadas e os utilizadores possam aceder aos mesmos.

## <a name="considerations-for-the-container-ecosystem"></a>Considerações para o ecossistema de contentores

As seguintes medidas de segurança, implementadas bem e geridas de forma eficaz, podem ajudá-lo a proteger e proteger o seu ecossistema de contentores. Estas medidas aplicam-se ao longo do ciclo de vida dos contentores, desde o desenvolvimento até à implantação da produção, bem como a uma série de orquestradores de contentores, anfitriões e plataformas. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Utilize a gestão da vulnerabilidade como parte do seu ciclo de vida de desenvolvimento de contentores 

Ao utilizar uma gestão eficaz da vulnerabilidade ao longo do ciclo de vida do desenvolvimento de contentores, melhora as probabilidades de identificar e resolver preocupações de segurança antes que se tornem um problema mais sério. 

### <a name="scan-for-vulnerabilities"></a>Scaneie por vulnerabilidades 

Novas vulnerabilidades são descobertas a toda a hora, por isso procurar e identificar vulnerabilidades é um processo contínuo. Incorporar a verificação de vulnerabilidades ao longo do ciclo de vida do recipiente:

* Como verificação final no seu pipeline de desenvolvimento, deve efetuar uma verificação de vulnerabilidade em recipientes antes de empurrar as imagens para um registo público ou privado. 
* Continue a digitalizar imagens de contentores no registo, tanto para identificar quaisquer falhas que tenham sido de alguma forma perdidas durante o desenvolvimento como para resolver quaisquer vulnerabilidades recentemente descobertas que possam existir no código utilizado nas imagens do contentor.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Map evulnerabilidades de imagem para contentores em execução 

É necessário ter um meio de mapear vulnerabilidades identificadas em imagens de contentores para executar contentores, para que as questões de segurança possam ser atenuadas ou resolvidas.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Certifique-se de que apenas as imagens aprovadas são usadas no seu ambiente 

Há mudanças e volatilidade suficientes num ecossistema de contentores sem permitir recipientes desconhecidos também. Permita apenas imagens de contentores aprovadas. Dispor de ferramentas e processos para monitorizar e impedir a utilização de imagens de contentores não aprovadas. 

Uma forma eficaz de reduzir a superfície de ataque e impedir que os desenvolvedores cometam erros críticos de segurança é controlar o fluxo de imagens de contentores para o seu ambiente de desenvolvimento. Por exemplo, você pode sancionar uma única distribuição Linux como uma imagem base, de preferência uma que é magra (Alpina ou CoreOS em vez de Ubuntu), para minimizar a superfície para potenciais ataques. 

A assinatura de imagem ou a impressão digital podem fornecer uma cadeia de custódia que lhe permite verificar a integridade dos recipientes. Por exemplo, o Registo de Contentores Azure suporta o modelo de confiança de [conteúdo](https://docs.docker.com/engine/security/trust/content_trust) do Docker, que permite que os editores de imagem assinem imagens que são empurradas para um registo, e os consumidores de imagem apenas puxem imagens assinadas.

### <a name="permit-only-approved-registries"></a>Permitir apenas registos aprovados 

Uma extensão de garantir que o seu ambiente utiliza apenas imagens aprovadas é permitir autilização apenas de registos de contentores aprovados. Exigir a utilização de registos de contentores aprovados reduz a sua exposição ao risco limitando o potencial de introdução de vulnerabilidades desconhecidas ou problemas de segurança. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Garantir a integridade das imagens ao longo do ciclo de vida 

Parte da gestão da segurança ao longo do ciclo de vida do contentor é garantir a integridade das imagens do contentor no registo e à medida que são alteradas ou implantadas em produção. 

* Imagens com vulnerabilidades, mesmo pequenas, não devem ser autorizadas a funcionar num ambiente produtivo. Idealmente, todas as imagens implantadas em produção devem ser guardadas num registo privado acessível a alguns selecionados. Mantenha o número de imagens de produção pequenas para garantir que podem ser geridas de forma eficaz.

* Porque é difícil identificar a origem do software a partir de uma imagem de contentor publicamente disponível, construir imagens a partir da fonte para garantir o conhecimento da origem da camada. Quando surge uma vulnerabilidade numa imagem de contentor criada automaticamente, os clientes podem encontrar um caminho mais rápido para a resolução. Com uma imagem pública, os clientes teriam de encontrar a raiz de uma imagem pública para a corrigir ou obter outra imagem segura da editora. 

* Não é garantida uma imagem completamente digitalizada implantada na produção para o tempo de vida da aplicação. Podem ser reportadas vulnerabilidades de segurança para camadas da imagem que não eram conhecidas anteriormente ou que foram introduzidas após a implementação em produção. 

  Audite periodicamente imagens implementadas na produção para identificar imagens que estão desatualizadas ou que não foram atualizadas há algum tempo. Pode utilizar metodologias de implementação azul-verde e mecanismos de atualização de rolos para atualizar as imagens dos contentores sem tempo de inatividade. Pode digitalizar imagens utilizando ferramentas descritas na secção anterior. 

* Utilize um pipeline de integração contínua (CI) com digitalização de segurança integrada para construir imagens seguras e empurrá-las para o seu registo privado. A análise de vulnerabilidade incorporada na solução de CI assegura que as imagens que passam em todos os testes são enviadas para o registo privado a partir do qual as cargas de trabalho em produção são implementadas. 

  Uma falha no gasoduto CI garante que as imagens vulneráveis não são empurradas para o registo privado que é usado para a implantação da carga de trabalho de produção. Também automatiza a verificação da segurança da imagem se houver um número significativo de imagens. Caso contrário, auditar manualmente as imagens relativamente a vulnerabilidades pode ser extremamente moroso e suscetível a erros. 

### <a name="enforce-least-privileges-in-runtime"></a>Impor menos privilégios em tempo de execução 

O conceito de menos privilégios é uma boa prática básica de segurança que também se aplica aos contentores. Quando uma vulnerabilidade é explorada, geralmente dá ao intruso acesso e privilégios iguais aos da aplicação ou processo comprometidos. Garantir que os contentores funcionam com os privilégios e acessos mais baixos necessários para fazer o trabalho reduz a sua exposição ao risco. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Reduza a superfície de ataque do contentor removendo privilégios desnecessários 

Também pode minimizar a superfície de ataque potencial removendo quaisquer processos ou privilégios não utilizados ou desnecessários do tempo de execução do recipiente. Os contentores privilegiados funcionam como raiz. Se um utilizador mal-intencionado ou a carga de trabalho escapar num recipiente privilegiado, o recipiente funcionará como raiz nesse sistema.

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Pré-aprovar ficheiros e executíveis que o recipiente é autorizado a aceder ou executar 

Reduzir o número de variáveis ou incógnitas ajuda-o a manter um ambiente estável e fiável. Limitar os contentores para que possam aceder ou executar apenas ficheiros e executíveis pré-aprovados ou seguros é um método comprovado de limitar a exposição ao risco.  

É muito mais fácil gerir uma lista de segurança quando é implementada desde o início. Um lista de segurança fornece uma medida de controlo e capacidade de gestão à medida que você descobre quais os ficheiros e executáveis necessários para que a aplicação funcione corretamente. 

Uma lista de segurança não só reduz a superfície de ataque, como também pode fornecer uma base para anomalias e impedir os casos de utilização dos cenários de fuga de contentores e "vizinhos barulhentos". 

### <a name="enforce-network-segmentation-on-running-containers"></a>Impor segmentação da rede em contentores de corrida  

Para ajudar a proteger os contentores numa subnet a partir de riscos de segurança noutra subneta, mantenha a segmentação da rede (ou nano segmentação) ou a segregação entre contentores em funcionamento. A manutenção da segmentação da rede também pode ser necessária para utilizar contentores em indústrias que são obrigadas a cumprir os mandatos de conformidade.  

Por exemplo, a ferramenta parceira [Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) fornece uma abordagem automatizada para a nanosegmentação. A Aqua monitoriza as atividades da rede de contentores em tempo de execução. Identifica todas as ligações de rede de entrada e saída de/para outros contentores, serviços, endereços IP e internet pública. A nanosegmentação é criada automaticamente com base no tráfego monitorizado. 

### <a name="monitor-container-activity-and-user-access"></a>Monitorizar a atividade do contentor e o acesso ao utilizador 

Como em qualquer ambiente de TI, deve monitorizar consistentemente a atividade e o acesso do utilizador ao seu ecossistema de contentores para identificar rapidamente qualquer atividade suspeita ou maliciosa. O Azure fornece soluções de monitorização de contentores, incluindo:

* [O Monitor Azure para contentores](../azure-monitor/insights/container-insights-overview.md) monitoriza o desempenho das suas cargas de trabalho implantadas em ambientes Kubernetes hospedados no Serviço Azure Kubernetes (AKS). O Monitor Azure para recipientes dá-lhe visibilidade de desempenho recolhendo métricas de memória e processador esqueléticos de controladores, nós e contentores que estão disponíveis em Kubernetes através da API métricas. 

* A solução de monitorização de [contentores Azure](../azure-monitor/insights/containers.md) ajuda-o a visualizar e gerir outros anfitriões de contentores Docker e Windows num único local. Por exemplo:

  * Consulte informações detalhadas sobre auditorias que mostram comandos utilizados com contentores. 
  * Contentores de resolução de problemas visualizando e pesquisando registos centralizados sem ter que ver remotamente os anfitriões do Docker ou do Windows.  
  * Encontre recipientes que possam ser barulhentos e consumam recursos em excesso num hospedeiro.
  * Ver CPU centralizado, memória, armazenamento e utilização da rede e informações de desempenho para recipientes.  

  A solução suporta orquestradores de contentores, incluindo Docker Swarm, DC/OS, Kubernetes não geridos, Tecido de Serviço e Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Monitorizar a atividade de recursos de contentores 

Monitorize a sua atividade de recursos, como ficheiros, rede e outros recursos a que os seus contentores acedem. O acompanhamento da atividade e do consumo dos recursos é útil tanto para a monitorização do desempenho como para uma medida de segurança. 

O [Azure Monitor](../azure-monitor/overview.md) permite a monitorização central dos serviços Azure, permitindo a recolha de métricas, registos de atividade e registos de diagnóstico. Por exemplo, o registo de atividade indica quando são criados ou modificados os novos recursos. 

  Estão disponíveis métricas que fornecem estatísticas de desempenho de recursos diferentes e, mesmo, do sistema operativo no interior de uma máquina virtual. Pode ver estes dados com um dos exploradores no portal do Azure e criar alertas com base nestas métricas. O Azure Monitor fornece o pipeline de métricas mais rápido (de 5 minutos a 1 minuto), pelo que deve utilizá-lo para notificações e alertas críticos de tempo. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Registar todos os acessos administrativos do utilizador do contentor para auditoria 

Mantenha um rasto de auditoria preciso de acesso administrativo ao seu ecossistema de contentores, incluindo o seu cluster Kubernetes, registo de contentores e imagens de contentores. Estes registos podem ser necessários para fins de auditoria e serão úteis como prova forense após qualquer incidente de segurança. As soluções Azure incluem:

* [Integração do Serviço Azure Kubernetes com o Azure Security Center](../security-center/azure-kubernetes-service-integration.md) para monitorizar a configuração de segurança do ambiente de cluster e gerar recomendações de segurança
* [Solução de Monitorização de Contentores Azure](../azure-monitor/insights/containers.md)
* Registos de recursos para instâncias de [contentores azure](container-instances-log-analytics.md) e [registo de contentores azure](../container-registry/container-registry-diagnostics-audit-logs.md)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a utilização do [Azure Security Center](../security-center/container-security.md) para deteção de ameaças em tempo real nos seus ambientes contentorizados.

* Saiba mais sobre como gerir vulnerabilidades de contentores com soluções da [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) e [da Aqua Security.](https://www.aquasec.com/solutions/azure-container-security/)