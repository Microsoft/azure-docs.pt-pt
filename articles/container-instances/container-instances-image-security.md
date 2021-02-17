---
title: Considerações de segurança para casos de contentores
description: Recomendações para garantir imagens e segredos para instâncias de contentores Azure, e considerações gerais de segurança para qualquer plataforma de contentores
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: cbceeea24501bc9815cb07e1b0a054914ba8e964
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579269"
---
# <a name="security-considerations-for-azure-container-instances"></a>Considerações de segurança para instâncias de contentores Azure

Este artigo introduz considerações de segurança para a utilização de instâncias de contentores Azure para executar aplicações de contentores. Os tópicos incluem:

> [!div class="checklist"]
> * **Recomendações de segurança** para a gestão de imagens e segredos para instâncias de contentores Azure
> * **Considerações para o ecossistema de contentores** em todo o ciclo de vida do contentor, para qualquer plataforma de contentores

Para recomendações abrangentes que o ajudarão a melhorar a postura de segurança da sua implantação, consulte a linha de base de [segurança Azure para instâncias de contentores](security-baseline.md).


## <a name="security-recommendations-for-azure-container-instances"></a>Recomendações de segurança para instâncias de contentores Azure

### <a name="use-a-private-registry"></a>Use um registo privado

Os contentores são criados a partir de imagens que estão armazenadas num ou mais repositórios. Estes repositórios podem pertencer a um registo público, como [Docker Hub,](https://hub.docker.com)ou a um registo privado. Um exemplo de um registo privado é o [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/), que pode ser instalado no local ou numa cloud privada virtual. Também pode utilizar serviços de registo de contentores privados baseados na nuvem, incluindo [o Registo de Contentores Azure.](../container-registry/container-registry-intro.md) 

Uma imagem de recipiente disponível ao público não garante a segurança. As imagens de contentores consistem em várias camadas de software, e cada camada de software pode ter vulnerabilidades. Para ajudar a reduzir a ameaça de ataques, deve armazenar e recuperar imagens de um registo privado, como o Registo de Contentores Azure ou o Registo Fidedigno de Docker. Além de fornecer um registo privado gerido, o Registo de Contentores Azure suporta [a autenticação principal do serviço](../container-registry/container-registry-authentication.md) através do Azure Ative Directory para fluxos básicos de autenticação. Esta autenticação inclui acesso baseado em funções para apenas leitura (puxar), escrever (empurrar) e outras permissões.

### <a name="monitor-and-scan-container-images"></a>Monitore e digitalize imagens do recipiente

Aproveite as soluções para digitalizar imagens de contentores num registo privado e identificar potenciais vulnerabilidades. É importante entender a profundidade da deteção de ameaças que as diferentes soluções fornecem.

Por exemplo, o Registo de Contentores Azure integra-se opcionalmente [com o Azure Security Center](../security-center/defender-for-container-registries-introduction.md) para digitalizar automaticamente todas as imagens Linux empurradas para um registo. O scanner integrado qualys do Azure Security Center deteta vulnerabilidades de imagem, classifica-as e fornece orientação de remediação.

Soluções de monitorização de segurança e de digitalização de imagens como [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) e [Aqua Security](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) também estão disponíveis através do Azure Marketplace.  

### <a name="protect-credentials"></a>Proteger credenciais

Os contentores podem espalhar-se por vários aglomerados e regiões de Azure. Por isso, deve obter credenciais necessárias para logins ou acesso apiísta, como palavras-passe ou fichas. Certifique-se de que apenas os utilizadores privilegiados podem aceder a esses contentores em trânsito e em repouso. Inventar todos os segredos credenciais e, em seguida, exigir que os desenvolvedores usem ferramentas emergentes de gestão de segredos que são projetadas para plataformas de contentores.  Certifique-se de que a sua solução inclui bases de dados encriptadas, encriptação TLS para dados de segredos em trânsito e [controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md). [O Azure Key Vault](../key-vault/general/secure-your-key-vault.md) é um serviço de nuvem que protege chaves e segredos de encriptação (tais como certificados, cadeias de conexão e palavras-passe) para aplicações contentorizadas. Como estes dados são sensíveis e críticos do negócio, o acesso seguro aos cofres chave para que apenas aplicações autorizadas e utilizadores possam aceder aos mesmos.

## <a name="considerations-for-the-container-ecosystem"></a>Considerações para o ecossistema de contentores

As seguintes medidas de segurança, implementadas bem e geridas de forma eficaz, podem ajudá-lo a proteger e a proteger o seu ecossistema de contentores. Estas medidas aplicam-se ao longo do ciclo de vida dos contentores, desde o desenvolvimento até à implantação de produção, e a uma série de orquestradores, anfitriões e plataformas de contentores. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Use a gestão da vulnerabilidade como parte do seu ciclo de vida de desenvolvimento de contentores 

Ao utilizar uma gestão eficaz da vulnerabilidade ao longo do ciclo de vida do desenvolvimento de contentores, melhora as probabilidades de identificar e resolver preocupações de segurança antes que se tornem um problema mais sério. 

### <a name="scan-for-vulnerabilities"></a>Scaneie para vulnerabilidades 

Novas vulnerabilidades são sempre descobertas, por isso pesquisar e identificar vulnerabilidades é um processo contínuo. Incorporar a digitalização da vulnerabilidade ao longo do ciclo de vida do contentor:

* Como verificação final no seu pipeline de desenvolvimento, deve efetuar uma verificação de vulnerabilidade em contentores antes de empurrar as imagens para um registo público ou privado. 
* Continue a digitalizar imagens de contentores no registo tanto para identificar quaisquer falhas que foram de alguma forma perdidas durante o desenvolvimento e para resolver quaisquer vulnerabilidades recentemente descobertas que possam existir no código utilizado nas imagens do contentor.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Vulnerabilidades de imagem de mapa para executar contentores 

É necessário ter um meio de mapear vulnerabilidades identificadas em imagens de contentores para os contentores em funcionamento, para que as questões de segurança possam ser atenuadas ou resolvidas.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Certifique-se de que apenas imagens aprovadas são usadas no seu ambiente 

Há mudanças e volatilidade suficientes num ecossistema de contentores sem permitir contentores desconhecidos também. Permitir apenas imagens de recipientes aprovados. Dispor de ferramentas e processos para monitorizar e impedir a utilização de imagens de recipientes não aprovados. 

Uma forma eficaz de reduzir a superfície de ataque e impedir que os desenvolvedores cometem erros críticos de segurança é controlar o fluxo de imagens de contentores para o seu ambiente de desenvolvimento. Por exemplo, você pode sancionar uma única distribuição Linux como uma imagem base, de preferência uma que seja magra (Alpina ou CoreOS em vez de Ubuntu), para minimizar a superfície para potenciais ataques. 

A assinatura de imagem ou a recolha de impressões digitais podem fornecer uma cadeia de custódia que lhe permite verificar a integridade dos recipientes. Por exemplo, o Registo de Contentores Azure suporta o modelo de confiança de [conteúdo](https://docs.docker.com/engine/security/trust/content_trust) de Docker, que permite que os editores de imagem assinem imagens que são empurradas para um registo, e os consumidores de imagem para puxar apenas imagens assinadas.

### <a name="permit-only-approved-registries"></a>Permitir apenas registos aprovados 

Uma extensão para garantir que o seu ambiente utiliza apenas imagens aprovadas é permitir apenas a utilização de registos de contentores aprovados. Exigir a utilização de registos de contentores aprovados reduz a sua exposição ao risco limitando o potencial de introdução de vulnerabilidades ou problemas de segurança desconhecidos. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Garantir a integridade das imagens ao longo do ciclo de vida 

Parte da gestão da segurança ao longo do ciclo de vida do contentor é garantir a integridade das imagens do contentor no registo e à medida que são alteradas ou implantadas na produção. 

* Imagens com vulnerabilidades, mesmo menores, não devem ser permitidas a funcionar num ambiente de produção. Idealmente, todas as imagens implementadas na produção devem ser guardadas num registo privado acessível a alguns. Mantenha o número de imagens de produção reduzido para garantir que podem ser geridas de forma eficaz.

* Porque é difícil identificar a origem do software a partir de uma imagem de recipiente publicamente disponível, construir imagens a partir da fonte para garantir o conhecimento da origem da camada. Quando surge uma vulnerabilidade numa imagem de contentor criada automaticamente, os clientes podem encontrar um caminho mais rápido para a resolução. Com uma imagem pública, os clientes teriam de encontrar a raiz de uma imagem pública para a corrigir ou obter outra imagem segura da editora. 

* Uma imagem cuidadosamente digitalizada implementada na produção não é garantida para estar atualizada para o tempo de vida da aplicação. Podem ser reportadas vulnerabilidades de segurança para camadas da imagem que não eram conhecidas anteriormente ou que foram introduzidas após a implementação em produção. 

  Periodicamente, auditam imagens implementadas na produção para identificar imagens desatualizadas ou que não tenham sido atualizadas há algum tempo. Pode utilizar metodologias de implantação azul-esverdeado e mecanismos de atualização rolante para atualizar imagens de contentores sem tempo de inatividade. Pode digitalizar as imagens utilizando ferramentas descritas na secção anterior. 

* Utilize um pipeline de integração contínua (CI) com uma verificação integrada de segurança para construir imagens seguras e empurrá-las para o seu registo privado. A análise de vulnerabilidade incorporada na solução de CI assegura que as imagens que passam em todos os testes são enviadas para o registo privado a partir do qual as cargas de trabalho em produção são implementadas. 

  Uma falha no gasoduto ci garante que imagens vulneráveis não são empurradas para o registo privado que é usado para implantações de carga de trabalho de produção. Também automatiza a verificação da segurança da imagem se houver um número significativo de imagens. Caso contrário, auditar manualmente as imagens relativamente a vulnerabilidades pode ser extremamente moroso e suscetível a erros. 

### <a name="enforce-least-privileges-in-runtime"></a>Impor os privilégios mínimos em tempo de execução 

O conceito de privilégios menos privilegiados é uma prática básica de segurança que também se aplica aos contentores. Quando uma vulnerabilidade é explorada, geralmente dá ao intruso acesso e privilégios iguais aos da aplicação ou processo comprometidos. Garantir que os contentores funcionam com os privilégios e acessos mais baixos necessários para fazer o trabalho reduz a sua exposição ao risco. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Reduzir a superfície de ataque do contentor removendo privilégios não-oferecidos 

Também pode minimizar a superfície de ataque potencial removendo quaisquer processos ou privilégios não usados ou desnecessários do tempo de execução do recipiente. Os recipientes privilegiados funcionam como raiz. Se um utilizador ou carga de trabalho malicioso escapar num recipiente privilegiado, o recipiente funcionará como raiz nesse sistema.

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Pré-aprovação de ficheiros e executáveis que o contentor pode aceder ou executar 

Reduzir o número de variáveis ou incógnitas ajuda a manter um ambiente estável e fiável. Limitar os contentores para que possam aceder ou executar apenas ficheiros pré-aprovados ou seguros e executáveis é um método comprovado de limitar a exposição ao risco.  

É muito mais fácil gerir uma lista de segurança quando é implementada desde o início. Uma lista de segurança fornece uma medida de controlo e gerenciabilidade à medida que aprende quais os ficheiros e execuções necessários para que a aplicação funcione corretamente. 

Uma lista de segurança não só reduz a superfície de ataque, como também pode fornecer uma linha de base para anomalias e prevenir a utilização de casos de "vizinho barulhento" e cenários de fuga de contentores. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Impor a segmentação da rede em contentores em funcionamento  

Para ajudar a proteger os contentores numa sub-rede contra riscos de segurança noutra sub-rede, mantenha a segmentação da rede (ou a nano-segmentação) ou a segregação entre os contentores em funcionamento. A manutenção da segmentação da rede pode também ser necessária para utilizar contentores em indústrias que sejam obrigadas a cumprir os mandatos de conformidade.  

Por exemplo, a ferramenta parceira [Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) fornece uma abordagem automatizada para a nano-segmentação. A aqua monitoriza as atividades da rede de contentores em tempo de funcionação. Identifica todas as ligações de rede de entrada e saída de/para outros contentores, serviços, endereços IP e internet pública. A nano-segmentação é criada automaticamente com base no tráfego monitorizado. 

### <a name="monitor-container-activity-and-user-access"></a>Monitorizar a atividade do contentor e o acesso ao utilizador 

Como em qualquer ambiente de TI, deve monitorizar consistentemente a atividade e o acesso do utilizador ao seu ecossistema de contentores para identificar rapidamente qualquer atividade suspeita ou maliciosa. A Azure fornece soluções de monitorização de contentores, incluindo:

* [O Azure Monitor para contentores](../azure-monitor/containers/container-insights-overview.md) monitoriza o desempenho das suas cargas de trabalho implantadas em ambientes Kubernetes alojados no Serviço Azure Kubernetes (AKS). O Azure Monitor para recipientes dá-lhe visibilidade de desempenho recolhendo métricas de memória e processador de controladores, nós e recipientes disponíveis em Kubernetes através da API métricas. 

* A [solução de monitorização do contentor Azure](../azure-monitor/containers/containers.md) ajuda-o a visualizar e a gerir outros anfitriões de contentores Docker e Windows num único local. Por exemplo:

  * Consulte informações detalhadas de auditoria que mostram comandos utilizados com contentores. 
  * Recipientes de resolução de problemas visualizando e pesquisando registos centralizados sem ter que ver remotamente anfitriões do Docker ou do Windows.  
  * Encontre recipientes que possam ser barulhentos e consumam recursos excedentários num hospedeiro.
  * Consulte cpu centralizado, memória, armazenamento e informações de utilização e desempenho da rede para recipientes.  

  A solução suporta orquestradores de contentores, incluindo Docker Swarm, DC/OS, Kubernetes não geridos, Tecido de Serviço e Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Monitorizar a atividade dos recursos de contentores 

Monitorize a sua atividade de recursos, como ficheiros, rede e outros recursos a que os seus contentores acedam. A monitorização da atividade e do consumo dos recursos é útil tanto para a monitorização do desempenho como como medida de segurança. 

[O Azure Monitor](../azure-monitor/overview.md) permite a monitorização do núcleo dos serviços Azure, permitindo a recolha de métricas, registos de atividades e registos de diagnóstico. Por exemplo, o registo de atividade indica quando são criados ou modificados os novos recursos. 

  Estão disponíveis métricas que fornecem estatísticas de desempenho de recursos diferentes e, mesmo, do sistema operativo no interior de uma máquina virtual. Pode ver estes dados com um dos exploradores no portal do Azure e criar alertas com base nestas métricas. O Azure Monitor fornece o pipeline de métricas mais rápido (de 5 minutos a 1 minuto), pelo que deve utilizá-lo para notificações e alertas críticos de tempo. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Registar todo o acesso administrativo do utilizador do contentor para auditoria 

Mantenha um rasto de auditoria preciso de acesso administrativo ao seu ecossistema de contentores, incluindo o seu cluster Kubernetes, registo de contentores e imagens de contentores. Estes registos podem ser necessários para fins de auditoria e serão úteis como provas forenses após qualquer incidente de segurança. As soluções Azure incluem:

* [Integração do Serviço Azure Kubernetes com o Azure Security Center](../security-center/defender-for-kubernetes-introduction.md) para monitorizar a configuração de segurança do ambiente de cluster e gerar recomendações de segurança
* [Solução de monitorização do contentor Azure](../azure-monitor/containers/containers.md)
* Registos de recursos para [instâncias de contentores Azure](container-instances-log-analytics.md) e [registo de contentores Azure](../container-registry/container-registry-diagnostics-audit-logs.md)

## <a name="next-steps"></a>Passos seguintes

* Consulte a [linha de base de segurança Azure para instâncias de contentores](security-baseline.md) para obter recomendações abrangentes que o ajudarão a melhorar a postura de segurança da sua implantação.

* Saiba mais sobre a utilização [do Azure Security Center](../security-center/container-security.md) para deteção de ameaças em tempo real nos seus ambientes contentorizados.

* Saiba mais sobre a gestão de vulnerabilidades de contentores com soluções da [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) e [da Aqua Security.](https://www.aquasec.com/solutions/azure-container-security/)