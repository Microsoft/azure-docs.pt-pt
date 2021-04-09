---
title: Políticas de apoio ao Serviço Azure Kubernetes (AKS)
description: Conheça as políticas de suporte do Serviço Azure Kubernetes (AKS), responsabilidade partilhada e funcionalidades que estão em pré-visualização (ou alfa ou beta).
services: container-service
ms.topic: article
ms.date: 09/18/2020
ms.openlocfilehash: 86b1c0bba30b41a2ee17cfbdf05286c4d2b3fb8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91892715"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Políticas de apoio ao Serviço Azure Kubernetes

Este artigo fornece detalhes sobre políticas de suporte técnico e limitações para o Serviço Azure Kubernetes (AKS). O artigo também detalha a gestão do noses de agente, componentes de avião de controlo geridos, componentes de código aberto de terceiros e gestão de segurança ou patch.

## <a name="service-updates-and-releases"></a>Atualizações e lançamentos de serviços

* Para obter informações sobre a libertação, consulte [as notas de lançamento da AKS](https://github.com/Azure/AKS/releases).
* Para obter informações sobre as funcionalidades na pré-visualização, consulte [as funcionalidades de pré-visualização da AKS e os projetos relacionados.](https://awesomeopensource.com/projects/aks?categoryPage=11)

## <a name="managed-features-in-aks"></a>Funcionalidades geridas em AKS

A infraestrutura base como componentes de nuvem de serviço (IaaS), como componentes de computação ou de rede, permitem-lhe o acesso a controlos de baixo nível e opções de personalização. Em contraste, a AKS fornece uma implementação de Kubernetes chave na mão que lhe dá o conjunto comum de configurações e capacidades que precisa para o seu cluster. Como utilizador da AKS, tem opções limitadas de personalização e implementação. Em troca, não precisa de se preocupar ou gerir os clusters kubernetes diretamente.

Com a AKS, você obtém um *avião de controlo* totalmente gerido. O plano de controlo contém todos os componentes e serviços necessários para operar e fornecer clusters Kubernetes aos utilizadores finais. Todos os componentes kubernetes são mantidos e operados pela Microsoft.

A Microsoft gere e monitoriza os seguintes componentes através do painel de controlo:

* Servidores API kubelet ou Kubernetes
* Etcd ou uma loja de valor-chave compatível, fornecendo Qualidade de Serviço (QoS), escalabilidade e tempo de execução
* Serviços DNS (por exemplo, kube-dns ou CoreDNS)
* Procuração de Kubernetes ou networking
* Qualquer addon adicional ou componente do sistema em execução no espaço de nome do sistema kube

AKS não é uma solução plataforma-as-a-service (PaaS). Alguns componentes, como os nós de agente, têm *responsabilidade partilhada,* onde os utilizadores devem ajudar a manter o cluster AKS. A entrada do utilizador é necessária, por exemplo, para aplicar um patch de segurança do sistema operativo de nó de número de agente (OS).

Os serviços são *geridos* no sentido em que a Microsoft e a equipa AKS implementam, operam e são responsáveis pela disponibilidade e funcionalidade do serviço. Os clientes não podem alterar estes componentes geridos. A Microsoft limita a personalização para garantir uma experiência consistente e escalável do utilizador. Para obter uma solução totalmente personalizável, consulte [o Motor AKS.](https://github.com/Azure/aks-engine)

## <a name="shared-responsibility"></a>Responsabilidade partilhada

Quando um cluster é criado, você define os nós de agente Kubernetes que a AKS cria. As suas cargas de trabalho são executadas nestes nós.

Como os nós do seu agente executam código privado e armazenam dados sensíveis, o Microsoft Support só pode aceder aos mesmos de forma muito limitada. O Microsoft Support não pode iniciar sessão, executar comandos ou visualizar registos para estes nós sem a sua permissão ou assistência expressa.

Qualquer modificação feita diretamente aos nós do agente utilizando qualquer uma das APIs da IaaS torna o cluster insuportável. Qualquer modificação feita aos nós do agente deve ser feita utilizando mecanismos nativos de kubernetes, tais como `Daemon Sets` .

Da mesma forma, embora possa adicionar quaisquer metadados ao cluster e aos nós, tais como tags e etiquetas, alterar qualquer um dos metadados criados no sistema tornará o cluster sem suporte.

## <a name="aks-support-coverage"></a>Cobertura de suporte AKS

A Microsoft fornece suporte técnico para os seguintes exemplos:

* Conectividade a todos os componentes Kubernetes que o serviço Kubernetes fornece e suporta, como o servidor API.
* Gestão, uptime, QoS, e operações de kubernetes controlam serviços de avião (avião de controlo Kubernetes, servidor API, etcd, e coreDNS, por exemplo).
* Loja de dados etc. O suporte inclui cópias de segurança automatizadas e transparentes de todos os dados etcd a cada 30 minutos para o planeamento de desastres e restauro do estado do cluster. Estas cópias de segurança não estão diretamente disponíveis para si ou para qualquer utilizadores. Garantem a fiabilidade e consistência dos dados. Etc. o reversão ou restauro a pedido não é suportado como uma característica.
* Quaisquer pontos de integração no controlador de cloud Azure para Kubernetes. Estes incluem integrações em outros serviços Azure, tais como equilibradores de carga, volumes persistentes ou networking (Kubernetes e Azure CNI).
* Perguntas ou problemas sobre a personalização de componentes do plano de controlo, tais como o servidor API de Kubernetes, etcd e coreDNS.
* Questões sobre networking, tais como Azure CNI, kubenet, ou outros problemas de acesso à rede e funcionalidade. As questões podem incluir resolução de DNS, perda de pacotes, encaminhamento, e assim por diante. A Microsoft suporta vários cenários de networking:
  * Kubenet e Azure CNI usando VNETs geridos ou com sub-redes personalizadas (traga as suas próprias).
  * Conectividade com outros serviços e aplicações da Azure
  * Ingress controladores e configurações de entradas ou equilibradores de carga
  * Desempenho da rede e latência


> [!NOTE]
> Quaisquer ações de cluster tomadas pela Microsoft/AKS são feitas com o consentimento do utilizador sob uma função incorporada de Kubernetes `aks-service` e encadernação de funções `aks-service-rolebinding` incorporadas . Esta função permite que a AKS resolva problemas e diagnostice problemas de cluster, mas não pode modificar permissões, criar papéis ou encadernações de papéis, ou outras ações de alto privilégio. O acesso à função só é ativado em bilhetes de apoio ativos com acesso just-in-time (JIT).

A Microsoft não fornece suporte técnico para os seguintes exemplos:

* Perguntas sobre como usar Kubernetes. Por exemplo, o Microsoft Support não fornece conselhos sobre como criar controladores de entrada personalizados, usar cargas de trabalho de aplicação ou aplicar pacotes ou ferramentas de software de terceiros ou de código aberto.
  > [!NOTE]
  > O Microsoft Support pode aconselhar sobre a funcionalidade do cluster AKS, personalização e afinação (por exemplo, problemas e procedimentos de operações da Kubernetes).
* Projetos de código aberto de terceiros que não são fornecidos como parte do avião de controlo Kubernetes ou implantados com clusters AKS. Estes projetos podem incluir Istio, Helm, Envoy, ou outros.
  > [!NOTE]
  > A Microsoft pode fornecer o melhor suporte de esforço para projetos de código aberto de terceiros, como o Helm. Quando a ferramenta de código aberto de terceiros se integra com o fornecedor de nuvem Kubernetes Azure ou outros bugs específicos da AKS, a Microsoft suporta exemplos e aplicações a partir da documentação da Microsoft.
* Software de origem fechada de terceiros. Este software pode incluir ferramentas de verificação de segurança e dispositivos de rede ou software.
* Personalizações de rede que não as listadas na [documentação AKS](./index.yml).


## <a name="aks-support-coverage-for-agent-nodes"></a>Cobertura de suporte da AKS para nós de agente

### <a name="microsoft-responsibilities-for-aks-agent-nodes"></a>Responsabilidades da Microsoft por nós de agente AKS

A Microsoft e os utilizadores partilham a responsabilidade pelos nós de agente da Kubernetes onde:

* A imagem base do SO requer adições (tais como agentes de monitorização e de rede).
* Os nós do agente recebem patches de SO automaticamente.
* Os problemas com os componentes do avião de controlo Kubernetes que funcionam nos nós do agente são automaticamente remediados. Estes componentes incluem o seguinte:
  * `Kube-proxy`
  * Túneis de rede que fornecem caminhos de comunicação para os componentes mestres de Kubernetes
  * `Kubelet`
  * `Moby` ou `ContainerD`

> [!NOTE]
> Se um nó de agente não estiver operacional, a AKS pode reiniciar componentes individuais ou todo o nó do agente. Estas operações de reinício são automatizadas e fornecem remediação automática para questões comuns. Se quiser saber mais sobre os mecanismos de remediação automática, consulte [a Reparação Automática do Nó](node-auto-repair.md)

### <a name="customer-responsibilities-for-aks-agent-nodes"></a>Responsabilidades do cliente para nós de agente AKS

A Microsoft fornece patches e novas imagens para os seus nós de imagem semanalmente, mas não as corrige automaticamente por padrão. Para manter o seu sistema de identificação do agente e os componentes de tempo de execução remendados, deve manter um calendário regular [de atualização de imagem de nó](node-image-upgrade.md) ou automatizar.º.

Da mesma forma, a AKS lança regularmente novos patches de kubernetes e versões menores. Estas atualizações podem conter melhorias de segurança ou funcionalidades em Kubernetes. É responsável por manter a versão kubernetes dos seus clusters atualizada e de acordo com a [Política de Versão de Suporte AKS Kubernetes](supported-kubernetes-versions.md).

#### <a name="user-customization-of-agent-nodes"></a>Personalização do utilizador dos nós de agente
> [!NOTE]
> Os nós de agente da AKS aparecem no portal Azure como recursos regulares do Azure IaaS. Mas estas máquinas virtuais são implantadas num grupo de recursos Azure personalizado (geralmente pré-fixado com \* MC_). Não é possível alterar a imagem base do SO ou fazer quaisquer personalizações diretas a estes nós utilizando as APIs ou recursos da IaaS. Quaisquer alterações personalizadas que não sejam feitas através da AKS API não persistirão através de uma atualização, escala, atualização ou reinicialização. Evite efetuar alterações nos nós do agente, a menos que o Microsoft Support o direcione para fazer alterações.

A AKS gere o ciclo de vida e as operações dos nós de agente em seu nome - modificando os recursos iaaS associados aos nós do agente não é **suportado**. Um exemplo de uma operação não suportada é personalizar uma balança de máquina virtual de piscina de nós definida alterando manualmente configurações através do portal de conjunto de escala de máquina virtual ou API.
 
Para configurações ou pacotes específicos da carga de trabalho, a AKS recomenda a utilização [de Kubernetes `daemon sets` ](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/).

A utilização de recipientes privilegiados e init da Kubernetes `daemon sets` permite-lhe sintonizar/modificar ou instalar software de terceiros nos nós do agente de cluster. Exemplos de tais personalizações incluem adicionar software de verificação de segurança personalizado ou atualizar definições de sysctl.

Embora este caminho seja recomendado se os requisitos acima referidos se aplicarem, a engenharia e suporte da AKS não podem ajudar na resolução de problemas ou no diagnóstico de modificações que tornem o nó indisponível devido a um personalizado implantado `daemon set` .

### <a name="security-issues-and-patching"></a>Questões de segurança e remendos

Se uma falha de segurança for encontrada em um ou mais dos componentes geridos da AKS, a equipa AKS irá corrigir todos os clusters afetados para mitigar o problema. Em alternativa, a equipa dará orientação de upgrade aos utilizadores.

Para os nós de agente afetados por uma falha de segurança, a Microsoft irá notificá-lo com detalhes sobre o impacto e os passos para corrigir ou mitigar o problema de segurança (normalmente uma atualização de imagem de nó ou uma atualização de patch de cluster).

### <a name="node-maintenance-and-access"></a>Manutenção e acesso do nó

Embora possa entrar e alterar os nós de agente, fazer esta operação é desencorajado porque as alterações podem tornar um cluster insuportável.

## <a name="network-ports-access-and-nsgs"></a>Portas de rede, acesso e NSGs

Só pode personalizar os NSGs em sub-redes personalizadas. Não pode personalizar NSGs em sub-redes geridas ou ao nível nic dos nós do agente. A AKS tem requisitos de saída para pontos finais específicos, para controlar as saídas e garantir a conectividade necessária, ver [o tráfego de saídas limite](limit-egress-traffic.md).

## <a name="stopped-or-de-allocated-clusters"></a>Aglomerados parados ou desatribuidos

Como indicado anteriormente, a desalominação manual de todos os nós do cluster através do IAAS APIs/CLI/portal torna o cluster fora de suporte. A única forma suportada de parar/desatribuir todos os nós é [parar o cluster AKS,](start-stop-cluster.md#stop-an-aks-cluster)que preserva o estado de cluster por até 12 meses.

Os aglomerados que estão parados por mais de 12 meses deixarão de preservar o estado. 

Os agrupamentos que são desatribuidos fora das APIs da AKS não têm garantias de preservação do Estado. Os aviões de controlo para aglomerados neste estado serão arquivados após 30 dias, e apagados após 12 meses.

A AKS reserva-se o direito de arquivar aviões de controlo que tenham sido configurados fora das diretrizes de apoio por períodos prolongados iguais e além de 30 dias. AKS mantém cópias de segurança de metadados etcd cluster e pode facilmente realocar o cluster. Esta reafectação pode ser iniciada por qualquer operação PUT que recoloe o cluster no suporte, como uma atualização ou escala para nós de agente ativo.

Se a sua subscrição for suspensa ou eliminada, o plano de controlo e estado do seu cluster será eliminado após 90 dias.

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Características alfa e beta não suportadas

A AKS suporta apenas funcionalidades estáveis e beta dentro do projeto Kubernetes a montante. Salvo documentação em contrário, a AKS não suporta qualquer funcionalidade alfa disponível no projeto Kubernetes a montante.

## <a name="preview-features-or-feature-flags"></a>Funcionalidades de pré-visualização ou bandeiras de recurso

Para funcionalidades e funcionalidades que requerem testes alargados e feedback do utilizador, a Microsoft lança novas funcionalidades de pré-visualização ou funcionalidades por trás de uma bandeira de funcionalidade. Considere estas funcionalidades como funcionalidades pré-lançados ou beta.

As funcionalidades de pré-visualização ou as características da bandeira não são para produção. Mudanças contínuas em APIs e comportamento, correções de erros e outras alterações podem resultar em aglomerados instáveis e tempo de inatividade.

As funcionalidades de pré-visualização pública são enquadradas no suporte de "melhor esforço", uma vez que estas funcionalidades estão em pré-visualização e não se destinam à produção e são apoiadas pelas equipas de suporte técnico da AKS apenas durante o horário de trabalho. Para obter mais informações, consulte:

* [FAQ de suporte Azure](https://azure.microsoft.com/support/faq/)

## <a name="upstream-bugs-and-issues"></a>Bugs e problemas a montante

Dada a rapidez de desenvolvimento no projeto Kubernetes a montante, surgem invariavelmente bugs. Alguns destes bugs não podem ser remendados ou trabalhados dentro do sistema AKS. Em vez disso, as correções de bugs requerem patches maiores para projetos a montante (como Kubernetes, sistemas operativos de nó ou agente e kernel). Para os componentes que a Microsoft detém (como o fornecedor de nuvem Azure), o pessoal da AKS e da Azure está empenhado em corrigir problemas a montante na comunidade.

Quando um problema de suporte técnico é causado por um ou mais bugs a montante, as equipas de suporte e engenharia da AKS:

* Identifique e ligue os bugs a montante com quaisquer detalhes de suporte para ajudar a explicar por que este problema afeta o seu cluster ou carga de trabalho. Os clientes recebem links para os repositórios necessários para que possam ver os problemas e ver quando um novo lançamento irá fornecer correções.
* Fornecer potenciais soluções ou mitigação. Se a questão puder ser atenuada, uma [questão conhecida](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) será arquivada no repositório AKS. O arquivo conhecido explica:
  * A questão, incluindo links para bugs a montante.
  * A solução e detalhes sobre uma atualização ou outra persistência da solução.
  * Prazos ásperos para a inclusão da edição, com base na cadência de libertação a montante.
