---
title: Políticas de apoio ao Serviço Azure Kubernetes (AKS)
description: Conheça as políticas de suporte do Serviço Azure Kubernetes (AKS), responsabilidade partilhada e funcionalidades que estão em pré-visualização (ou alfa ou beta).
services: container-service
author: jnoller
ms.topic: article
ms.date: 01/24/2020
ms.author: jenoller
ms.openlocfilehash: a5d90106a85a61cbf499c4c08130392b922a45f0
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593585"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Políticas de apoio ao Serviço Azure Kubernetes

Este artigo fornece detalhes sobre políticas e limitações técnicas de apoio ao Serviço Azure Kubernetes (AKS). O artigo detalha ainda a gestão do nó dos trabalhadores, componentes de planos de controlo geridos, componentes de fonte seleção de terceiros e gestão de segurança ou patch.

## <a name="service-updates-and-releases"></a>Atualizações e lançamentos de serviços

* Para obter informações de libertação, consulte as notas de [lançamento da AKS](https://github.com/Azure/AKS/releases).
* Para obter informações sobre funcionalidades na pré-visualização, consulte funcionalidades de [pré-visualização aks e projetos relacionados.](https://github.com/Azure/AKS/blob/master/previews.md)

## <a name="managed-features-in-aks"></a>Funcionalidades geridas em AKS

A infraestrutura base como um serviço (IaaS) componentes de nuvem, tais como componentes de computação ou de rede, dá aos utilizadores acesso a controlos de baixo nível e opções de personalização. Em contraste, a AKS fornece uma implementação chave na mão kubernetes que dá aos clientes o conjunto comum de configurações e capacidades de que necessitam. Os clientes AKS têm personalização limitada, implementação e outras opções. Estes clientes não precisam de se preocupar ou gerir diretamente os clusters kubernetes.

Com a AKS, o cliente obtém um plano de *controlo*totalmente gerido. O plano de controlo contém todos os componentes e serviços que o cliente precisa para operar e fornecer clusters Kubernetes aos utilizadores finais. Todos os componentes kubernetes são mantidos e operados pela Microsoft.

A Microsoft gere e monitoriza os seguintes componentes através do painel de controlo:

* Servidores API Kubelet ou Kubernetes
* Etcd ou uma loja de valor-chave compatível, proporcionando qualidade de serviço (QoS), escalabilidade e tempo de execução
* Serviços DNS (por exemplo, kube-dns ou CoreDNS)
* Procuração kubernetes ou networking

Aks não é uma solução de cluster completamente gerida. Alguns componentes, como os nós dos trabalhadores, têm *responsabilidade partilhada,* onde os utilizadores devem ajudar a manter o cluster AKS. A entrada do utilizador é necessária, por exemplo, para aplicar um patch de segurança do sistema operativo do nó do trabalhador (OS).

Os serviços são *geridos* no sentido em que a Microsoft e a equipa AKS implementam, operam e são responsáveis pela disponibilidade e funcionalidade do serviço. Os clientes não podem alterar estes componentes geridos. A Microsoft limita a personalização para garantir uma experiência consistente e escalável do utilizador. Para obter uma solução totalmente personalizável, consulte [o AkS Engine](https://github.com/Azure/aks-engine).

> [!NOTE]
> Os nódosos de trabalhadores da AKS aparecem no portal Azure como recursos regulares do Azure IaaS. Mas estas máquinas virtuais são implantadas num grupo de recursos Azure personalizado (pré-fixado com MC\\*). É possível mudar os nódosos dos trabalhadores da AKS. Por exemplo, pode utilizar secure Shell (SSH) para alterar os nós dos trabalhadores AKS da forma como muda as máquinas virtuais normais (não pode, no entanto, alterar a imagem base do OS, e as alterações podem não persistir através de uma atualização ou reboot), e pode anexar outros recursos Azure ao AKS nódosos operários. Mas quando se faz alterações na gestão da banda e na *personalização,* o cluster AKS pode tornar-se insuportável. Evite alterar os nódosos dos trabalhadores a menos que o Microsoft Support o direcione para fazer alterações.

A emissão de operações não apoiadas, tal como definida acima, como a desafetação da banda de todos os nós de agente, torna o cluster desapoiado. A AKS reserva-se o direito de arquivar aviões de controlo que tenham sido configurados fora das diretrizes de suporte por períodos prolongados iguais e superiores a 30 dias. O AKS mantém cópias de segurança de metadados de cluster etc e pode facilmente realocar o cluster. Esta redistribuição pode ser iniciada por qualquer operação PUT que recoloque o cluster em suporte, como uma atualização ou escala para nós de agente ativo.

## <a name="shared-responsibility"></a>Responsabilidade partilhada

Quando um cluster é criado, o cliente define os nós de trabalhador kubernetes que a AKS cria. As cargas de trabalho dos clientes são executadas nestes nódosos. Os clientes possuem e podem ver ou modificar os nódosos dos trabalhadores.

Uma vez que os nós do cluster de clientes executam código privado e armazenam dados sensíveis, o Microsoft Support pode acessá-los de forma limitada. O Microsoft Support não pode iniciar sessão, executar comandos ou ver registos para estes nós sem autorização ou assistência expressa do cliente.

Como os nós dos trabalhadores são sensíveis, a Microsoft tem o cuidado de limitar a sua gestão de antecedentes. Em muitos casos, a sua carga de trabalho continuará a funcionar mesmo que os nós mestres Kubernetes, etcd, e outros componentes geridos pela Microsoft falhem. Os nós de trabalhadores descuidado modificados podem causar perdas de dados e cargas de trabalho e podem tornar o cluster insuportável.

## <a name="aks-support-coverage"></a>Cobertura de suporte AKS

A Microsoft fornece suporte técnico para o seguinte:

* Conectividade a todos os componentes kubernetes que o serviço Kubernetes fornece e suporta, como o servidor API.
* Gestão, uptime, QoS e operações de serviços de aviões de controlo Kubernetes (kubernetes principais nós, servidor API, etcd e kube-dns, por exemplo).
* Etcd. O suporte inclui cópias de segurança automatizadas e transparentes de todos os dados etcd a cada 30 minutos para planeamento de desastres e restauro do estado de cluster. Estas cópias de segurança não estão diretamente disponíveis para clientes ou utilizadores. Garantem a fiabilidade e consistência dos dados.
* Qualquer ponto de integração no fornecedor de nuvem Azure para kubernetes. Estes incluem integrações em outros serviços Azure, tais como equilibradores de carga, volumes persistentes ou networking (Kubernetes e Azure CNI).
* Questões ou questões sobre a personalização de componentes de planos de controlo, tais como o servidor Kubernetes API, etcd e kube-dns.
* Questões sobre networking, como Azure CNI, kubenet ou outros problemas de acesso à rede e funcionalidades. As questões podem incluir resolução dNS, perda de pacotes, encaminhamento, e assim por diante. A Microsoft suporta vários cenários de networking:
  * Kubenet (básico) e avançado networking (Azure CNI) dentro do cluster e componentes associados
  * Conectividade com outros serviços e aplicações do Azure
  * Controladores de ingressos e configurações de equilíbrio de entrada ou de carga
  * Desempenho da rede e latência

A Microsoft não fornece suporte técnico para o seguinte:

* Perguntas sobre como usar Kubernetes. Por exemplo, o Microsoft Support não fornece conselhos sobre como criar controladores de ingresso personalizados, usar cargas de trabalho de aplicação ou aplicar pacotes ou ferramentas de software de terceiros ou de código aberto.
  > [!NOTE]
  > O Microsoft Support pode aconselhar sobre a funcionalidade do cluster AKS, personalização e afinação (por exemplo, problemas e procedimentos de operações da Kubernetes).
* Projetos de código aberto de terceiros que não são fornecidos como parte do plano de controlo Kubernetes ou implantados com clusters AKS. Estes projetos podem incluir Istio, Helm, Envoy, ou outros.
  > [!NOTE]
  > A Microsoft pode fornecer o melhor apoio para projetos de código aberto de terceiros, como Helm e Kured. Quando a ferramenta de código aberto de terceiros se integrar com o fornecedor de nuvem Kubernetes Azure ou outros bugs específicos da AKS, a Microsoft suporta exemplos e aplicações a partir da documentação da Microsoft.
* Software de origem fechada de terceiros. Este software pode incluir ferramentas de digitalização de segurança e dispositivos de networking ou software.
* Questões sobre construções multicloud ou multi-fornecedores. Por exemplo, a Microsoft não suporta problemas relacionados com a execução de uma solução de fornecedor de nuvem multipública federada.
* Personalizações de rede diferentes das listadas na [documentação AKS.](https://docs.microsoft.com/azure/aks/)
  > [!NOTE]
  > A Microsoft suporta problemas e bugs relacionados com grupos de segurança de rede (NSGs). Por exemplo, o Microsoft Support pode responder a perguntas sobre uma falha de atualização do NSG ou um comportamento de NSG ou balancer de carga inesperado.

## <a name="aks-support-coverage-for-worker-nodes"></a>AKS suporta cobertura para nódosos de trabalhadores

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Responsabilidades da Microsoft pelos nomes dos trabalhadores da AKS

A Microsoft e os clientes partilham a responsabilidade pelos nódos de trabalhadores da Kubernetes onde:

* A imagem base do OS exigiu adições (tais como agentes de monitorização e de rede).
* Os nódosos dos trabalhadores recebem patches de SO automaticamente.
* Os problemas com os componentes de plano de controlo kubernetes que funcionam nos nódosos dos trabalhadores são automaticamente remediados. Os componentes incluem o seguinte:
  * Procuração kube
  * Túneis de rede que fornecem caminhos de comunicação para os componentes principais kubernetes
  * Kubelet
  * Docker ou Moby daemon

> [!NOTE]
> Num nó de trabalhador, se um componente de plano de controlo não estiver operacional, a equipa aks poderá ter de reiniciar componentes individuais ou todo o nó do trabalhador. Estas operações de reinício são automatizadas e fornecem uma reparação automática para questões comuns. Estas reinicializações ocorrem apenas no nível do _nó_ e não no cluster, a menos que haja uma manutenção ou paragem de emergência.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Responsabilidades do cliente para os nódosos dos trabalhadores da AKS

A Microsoft não reinicia automaticamente os nódosos dos trabalhadores para aplicar patches de nível de OS. Embora os patches de OS sejam entregues aos nós dos trabalhadores, o *cliente* é responsável por reiniciar os nós dos trabalhadores para aplicar as alterações. Bibliotecas partilhadas, daemons como unidade híbrida de estado sólido (SSHD), e outros componentes ao nível do sistema ou OS são automaticamente corrigidos.

Os clientes são responsáveis pela execução das atualizações da Kubernetes. Podem executar atualizações através do painel de controlo Azure ou do Azure CLI. Isto aplica-se a atualizações que contenham melhorias de segurança ou funcionalidade sintetizam kubernetes.

> [!NOTE]
> Como o AKS é um *serviço gerido*, os seus objetivos finais incluem a remoção da responsabilidade por patches, atualizações e recolha de registos para tornar a gestão do serviço mais completa e prática. À medida que a capacidade do serviço para a gestão de ponta a ponta aumenta, futuras libertações podem omitir algumas funções (por exemplo, reinicialização do nó e remendos automáticos).

### <a name="security-issues-and-patching"></a>Questões de segurança e remendos

Se for encontrada uma falha de segurança em um ou mais componentes do AKS, a equipa aks irá corrigir todos os clusters afetados para mitigar o problema. Em alternativa, a equipa dará aos utilizadores orientação de upgrade.

Para os nós dos trabalhadores que uma falha de segurança afeta, se estiver disponível um patch de tempo zero, a equipa aks aplicará esse patch e notificará os utilizadores da alteração.

Quando um patch de segurança requer reinicialização do nó do trabalhador, a Microsoft notificará os clientes deste requisito. O cliente é responsável por reiniciar ou atualizar para obter o patch de cluster. Se os utilizadores não aplicarem os patches de acordo com a orientação da AKS, o seu cluster continuará vulnerável ao problema de segurança.

### <a name="node-maintenance-and-access"></a>Manutenção e acesso ao nó

Os nódoas operárias são uma responsabilidade partilhada e são propriedade dos clientes. Por isso, os clientes têm a capacidade de iniciar sessão nos seus nós de trabalhador e de fazer alterações potencialmente nocivas, como atualizações de kernel e instalação ou remoção de pacotes.

Se os clientes fizerem alterações destrutivas ou causarem que os componentes do plano de controlo fiquem offline ou se tornem infuncionais, o AKS detetará esta falha e restaurará automaticamente o nó do trabalhador para o estado de trabalho anterior.

Embora os clientes possam inscrever-se e mudar os nós dos trabalhadores, fazê-lo é desencorajado porque as mudanças podem tornar um cluster insuportável.

## <a name="network-ports-access-and-nsgs"></a>Portas de rede, acessos e NSGs

Como um serviço gerido, o AKS tem requisitos específicos de networking e conectividade. Estes requisitos são menos flexíveis do que os requisitos para os componentes normais do IaaS. No AKS, operações como personalizar regras de NSG, bloquear uma porta específica (por exemplo, usar regras de firewall que bloqueiam a porta de saída 443), e URLs de listagem branca podem tornar o seu cluster insuportável.

> [!NOTE]
> Atualmente, a AKS não permite bloquear completamente o tráfego do seu cluster. Para controlar a lista de URLs e portas que o seu cluster pode utilizar para tráfego de saída ver limitar o tráfego de [saída](limit-egress-traffic.md).

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Características alfa e beta kubernetes não suportadas

A AKS suporta apenas características estáveis dentro do projeto Kubernetes a montante. Salvo documentação em contrário, a AKS não suporta funcionalidades alfa e beta que estejam disponíveis no projeto Kubernetes a montante.

Em dois cenários, as funcionalidades alfa ou beta podem ser lançadas antes de estarem geralmente disponíveis:

* Os clientes reuniram-se com as equipas de produtos, suporte ou engenharia aks e foram convidados a experimentar estas novas funcionalidades.
* Estas funcionalidades foram [ativadas por uma bandeira](https://github.com/Azure/AKS/blob/master/previews.md)de recurso. Os clientes devem optar explicitamente por utilizar estas funcionalidades.

## <a name="preview-features-or-feature-flags"></a>Funcionalidades de pré-visualização ou bandeiras de características

Para funcionalidades e funcionalidades que requerem testes alargados e feedback do utilizador, a Microsoft lança novas funcionalidades ou funcionalidades de pré-visualização por detrás de uma bandeira de funcionalidade. Considere estas funcionalidades como funcionalidades pré-lançamento ou beta.

As funcionalidades de pré-visualização ou as funcionalidades de bandeira não se destinam à produção. Mudanças contínuas em APIs e comportamento, correções de bugs e outras alterações podem resultar em aglomerados instáveis e tempo de inatividade.

As funcionalidades na pré-visualização pública são enquadradas no apoio ao "melhor esforço", uma vez que estas funcionalidades estão em pré-visualização e não se destinam à produção e são apoiadas pelas equipas de suporte técnico da AKS apenas durante o horário comercial. Para mais informações, consulte:

* [FaQ de suporte azure](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> As funcionalidades de pré-visualização entram em vigor ao nível de *subscrição* do Azure. Não instale funcionalidades de pré-visualização numa subscrição de produção. Numa subscrição de produção, as funcionalidades de pré-visualização podem alterar o comportamento padrão da API e afetar as operações regulares.

## <a name="upstream-bugs-and-issues"></a>Bugs e problemas a montante

Dada a velocidade de desenvolvimento no projeto Kubernetes a montante, os insetos surgem invariavelmente. Alguns destes insetos não podem ser remendados ou trabalhados dentro do sistema AKS. Em vez disso, as correções de bugs requerem patches maiores para projetos a montante (como Kubernetes, sistemas operativos de nó ou trabalhadores, e núcleos). Para componentes que a Microsoft detém (como o fornecedor de nuvem Azure), o pessoal aks e azure está empenhado em corrigir problemas a montante na comunidade.

Quando um problema de suporte técnico é causado por um ou mais bugs a montante, as equipas de apoio e engenharia aks:

* Identifique e ligue os bugs a montante com quaisquer detalhes de suporte para ajudar a explicar porque é que este problema afeta o seu cluster ou carga de trabalho. Os clientes recebem links para os repositórios necessários para que possam ver os problemas e ver quando um novo lançamento irá fornecer correções.
* Fornecer potenciais soluções ou atenuações. Se a questão puder ser atenuada, uma [questão conhecida](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) será arquivada no repositório AKS. O arquivo de emissão conhecida explica:
  * A questão, incluindo ligações a bugs a montante.
  * A solução e detalhes sobre uma atualização ou outra persistência da solução.
  * Prazos brusos para a inclusão da edição, com base na cadência de libertação a montante.
