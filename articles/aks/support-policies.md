---
title: Políticas de apoio ao Serviço Azure Kubernetes (AKS)
description: Conheça as políticas de suporte do Serviço Azure Kubernetes (AKS), responsabilidade partilhada e funcionalidades que estão em pré-visualização (ou alfa ou beta).
services: container-service
author: jnoller
ms.topic: article
ms.date: 01/24/2020
ms.author: jenoller
ms.openlocfilehash: 775992d090b951c8de6fce36377dc91d6e017399
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85558076"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Políticas de apoio ao Serviço Azure Kubernetes

Este artigo fornece detalhes sobre políticas de suporte técnico e limitações para o Serviço Azure Kubernetes (AKS). O artigo também detalha a gestão do nó do trabalhador, componentes de avião de controlo geridos, componentes de código aberto de terceiros e gestão de segurança ou patch.

## <a name="service-updates-and-releases"></a>Atualizações e lançamentos de serviços

* Para obter informações sobre a libertação, consulte [as notas de lançamento da AKS](https://github.com/Azure/AKS/releases).
* Para obter informações sobre as funcionalidades na pré-visualização, consulte [as funcionalidades de pré-visualização da AKS e os projetos relacionados.](https://github.com/Azure/AKS/blob/master/previews.md)

## <a name="managed-features-in-aks"></a>Funcionalidades geridas em AKS

A infraestrutura base como componentes de nuvem de serviço (IaaS), como componentes de computação ou de rede, dão aos utilizadores acesso a controlos de baixo nível e opções de personalização. Em contraste, a AKS fornece uma implementação de Kubernetes chave na mão que dá aos clientes o conjunto comum de configurações e capacidades de que necessitam. Os clientes AKS têm poucas opções de personalização, implementação e outras opções. Estes clientes não precisam de se preocupar ou gerir os clusters Kubernetes diretamente.

Com a AKS, o cliente obtém um *avião de controlo*totalmente gerido. O plano de controlo contém todos os componentes e serviços que o cliente precisa para operar e fornecer clusters Kubernetes aos utilizadores finais. Todos os componentes kubernetes são mantidos e operados pela Microsoft.

A Microsoft gere e monitoriza os seguintes componentes através do painel de controlo:

* Servidores API kubelet ou Kubernetes
* Etcd ou uma loja de valor-chave compatível, fornecendo Qualidade de Serviço (QoS), escalabilidade e tempo de execução
* Serviços DNS (por exemplo, kube-dns ou CoreDNS)
* Procuração de Kubernetes ou networking

AKS não é uma solução de cluster completamente gerida. Alguns componentes, como os nós dos trabalhadores, têm *responsabilidade partilhada,* onde os utilizadores devem ajudar a manter o cluster AKS. A entrada do utilizador é necessária, por exemplo, para aplicar um patch de segurança do sistema operativo do nó de trabalhador (OS).

Os serviços são *geridos* no sentido em que a Microsoft e a equipa AKS implementam, operam e são responsáveis pela disponibilidade e funcionalidade do serviço. Os clientes não podem alterar estes componentes geridos. A Microsoft limita a personalização para garantir uma experiência consistente e escalável do utilizador. Para obter uma solução totalmente personalizável, consulte [o Motor AKS.](https://github.com/Azure/aks-engine)

## <a name="shared-responsibility"></a>Responsabilidade partilhada

Quando um cluster é criado, o cliente define os nós de trabalhador kubernetes que a AKS cria. As cargas de trabalho dos clientes são executadas nestes nós. Os clientes são próprios e podem ver ou modificar os nós do trabalhador.

Como os nós de cluster de clientes executam código privado e armazenam dados sensíveis, o Microsoft Support só pode aceder aos mesmos de forma limitada. O Microsoft Support não pode iniciar sessão, executar comandos ou visualizar registos para estes nós sem autorização ou assistência expressa do cliente.

Como os nós dos trabalhadores são sensíveis, a Microsoft tem o cuidado de limitar a sua gestão de antecedentes. Em muitos casos, a sua carga de trabalho continuará a funcionar mesmo que os nós principais de Kubernetes, etcd, e outros componentes geridos pela Microsoft falhem. Os nós de trabalhador modificados descuidadamente podem causar perdas de dados e cargas de trabalho e podem tornar o cluster insuportável.

## <a name="aks-support-coverage"></a>Cobertura de suporte AKS

A Microsoft fornece suporte técnico para o seguinte:

> [!NOTE]
> Quaisquer ações de cluster tomadas pelo suporte da Microsoft são feitas com o consentimento do utilizador sob um papel de ["editar"](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) do nome embutido de Kubernetes `aks-support-rolebinding` . Com esta função, o suporte AKS é habilitado a editar a configuração do cluster e os recursos para resolver problemas e diagnosticar problemas de cluster, mas o papel não pode modificar permissões nem criar papéis ou encadernações de papéis. O acesso à função só é ativado em bilhetes de apoio ativos com acesso just-in-time (JIT).

* Conectividade a todos os componentes Kubernetes que o serviço Kubernetes fornece e suporta, como o servidor API.
* Gestão, uptime, QoS, e operações de kubernetes controlam serviços de avião (Kubernetes master nodes, servidor API, etcd, e kube-dns, por exemplo).
* Etc. O suporte inclui cópias de segurança automatizadas e transparentes de todos os dados etcd a cada 30 minutos para o planeamento de desastres e restauro do estado do cluster. Estas cópias de segurança não estão diretamente disponíveis para clientes ou utilizadores. Garantem a fiabilidade e consistência dos dados. Etc. a pedido, retrocedendo ou restabelecendo não é suportado como uma característica.
* Quaisquer pontos de integração no controlador de cloud Azure para Kubernetes. Estes incluem integrações em outros serviços Azure, tais como equilibradores de carga, volumes persistentes ou networking (Kubernetes e Azure CNI).
* Perguntas ou problemas sobre a personalização de componentes do plano de controlo, tais como o servidor API de Kubernetes, etcd, e kube-dns.
* Questões sobre networking, tais como Azure CNI, kubenet, ou outros problemas de acesso à rede e funcionalidade. As questões podem incluir resolução de DNS, perda de pacotes, encaminhamento, e assim por diante. A Microsoft suporta vários cenários de networking:
  * Kubenet (básico) e rede avançada (Azure CNI) dentro do cluster e componentes associados
  * Conectividade com outros serviços e aplicações da Azure
  * Ingress controladores e configurações de entradas ou equilibradores de carga
  * Desempenho da rede e latência

A Microsoft não fornece suporte técnico para o seguinte:

* Perguntas sobre como usar Kubernetes. Por exemplo, o Microsoft Support não fornece conselhos sobre como criar controladores de entrada personalizados, usar cargas de trabalho de aplicação ou aplicar pacotes ou ferramentas de software de terceiros ou de código aberto.
  > [!NOTE]
  > O Microsoft Support pode aconselhar sobre a funcionalidade do cluster AKS, personalização e afinação (por exemplo, problemas e procedimentos de operações da Kubernetes).
* Projetos de código aberto de terceiros que não são fornecidos como parte do avião de controlo Kubernetes ou implantados com clusters AKS. Estes projetos podem incluir Istio, Helm, Envoy, ou outros.
  > [!NOTE]
  > A Microsoft pode fornecer o melhor suporte para projetos de código aberto de terceiros, como Helm e Kured. Quando a ferramenta de código aberto de terceiros se integra com o fornecedor de nuvem Kubernetes Azure ou outros bugs específicos da AKS, a Microsoft suporta exemplos e aplicações a partir da documentação da Microsoft.
* Software de origem fechada de terceiros. Este software pode incluir ferramentas de verificação de segurança e dispositivos de rede ou software.
* Problemas sobre multicloud ou construções multivendores. Por exemplo, a Microsoft não suporta problemas relacionados com a execução de uma solução de fornecedor de nuvem multipública federada.
* Personalizações de rede que não as listadas na [documentação AKS](https://docs.microsoft.com/azure/aks/).
  > [!NOTE]
  > A Microsoft suporta problemas e bugs relacionados com grupos de segurança de rede (NSGs). Por exemplo, o Microsoft Support pode responder a perguntas sobre uma falha NSG na atualização ou um comportamento inesperado de NSG ou balanceador de carga.

## <a name="aks-support-coverage-for-worker-nodes"></a>Cobertura de apoio da AKS para os nóns dos trabalhadores

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Responsabilidades da Microsoft para os acenos dos trabalhadores da AKS

A Microsoft e os clientes partilham a responsabilidade pelos nóns de trabalhadores da Kubernetes onde:

* A imagem base do SO requer adições (tais como agentes de monitorização e de rede).
* Os nós dos trabalhadores recebem patches de SO automaticamente.
* Os problemas com os componentes do avião de controlo Kubernetes que funcionam nos nós dos trabalhadores são automaticamente remediados. Os componentes incluem:
  * Kube-proxy
  * Túneis de rede que fornecem caminhos de comunicação para os componentes mestres de Kubernetes
  * Kubelet
  * Docker ou Daemon Moby

> [!NOTE]
> Num nó de trabalhador, se um componente do avião de controlo não estiver operacional, a equipa AKS poderá ter de reiniciar componentes individuais ou todo o nó do trabalhador. Estas operações de reinicialização são automatizadas e fornecem remediação automática para questões comuns. Estas reinicializações ocorrem apenas ao nível do _nó_ e não ao cluster, a menos que haja uma manutenção de emergência ou uma paragem.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Responsabilidades dos clientes dos acenos dos trabalhadores da AKS

A Microsoft não reinicia automaticamente os nós dos trabalhadores para aplicar patches de nível OS. Embora os patches de SO sejam entregues aos nós dos trabalhadores, o *cliente* é responsável por reiniciar os nós do trabalhador para aplicar as alterações. Bibliotecas partilhadas, daemons como unidade híbrida de estado sólido (SSHD), e outros componentes ao nível do sistema ou do SO são automaticamente remendados.

Os clientes são responsáveis pela execução de upgrades da Kubernetes. Podem executar atualizações através do painel de controlo Azure ou do Azure CLI. Isto aplica-se a atualizações que contenham melhorias de segurança ou funcionalidades em Kubernetes.

#### <a name="user-customization-of-worker-nodes"></a>Personalização do utilizador dos nosdes dos trabalhadores
> [!NOTE]
> Os nós operários da AKS aparecem no portal Azure como recursos regulares do Azure IaaS. Mas estas máquinas virtuais são implantadas num grupo de recursos Azure personalizado (prefixado com MC \\ *). É possível aumentar os nós dos trabalhadores AKS a partir das suas configurações base. Por exemplo, pode utilizar a Secure Shell (SSH) para alterar os nós dos trabalhadores AKS da forma como muda as máquinas virtuais normais. Não é possível, no entanto, alterar a imagem base do SO. Quaisquer alterações personalizadas podem não persistir através de uma atualização, escala, atualização ou reinicialização. **No entanto,** fazer alterações *fora da banda e fora do âmbito da AKS API* leva a que o cluster AKS fique sem suporte. Evite alterar os nós dos trabalhadores, a menos que o Microsoft Support o direcione para fazer alterações.

A emissão de operações não apoiadas, tal como acima definidas, como a saída de todos os nós de agente, torna o cluster sem apoio. A AKS reserva-se o direito de arquivar aviões de controlo que tenham sido configurados fora das diretrizes de apoio por períodos prolongados iguais e além de 30 dias. AKS mantém cópias de segurança de metadados etcd cluster e pode facilmente realocar o cluster. Esta reafectação pode ser iniciada por qualquer operação PUT que recoloe o cluster no suporte, como uma atualização ou escala para nós de agente ativo.

A AKS gere o ciclo de vida e as operações dos nós dos trabalhadores em nome dos clientes - não é **suportado**a alteração dos recursos iaaS associados aos nós operários. Um exemplo de uma operação não suportada é personalizar um conjunto de conjunto de nós VM Scale set alterando manualmente configurações no VMSS através do portal VMSS ou VMSS API.
 
Para configurações ou pacotes específicos de carga de trabalho, a AKS recomenda a utilização [de daemonsets Kubernetes](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/).

A utilização de daemonsets privilegiados da Kubernetes e recipientes init permite que os clientes afinam/modifiquem ou instalem software de terceiros em nós de trabalhadores de cluster. Exemplos de tais personalizações incluem adicionar software de verificação de segurança personalizado ou atualizar definições de sysctl.

Embora este seja um caminho recomendado se os requisitos acima referidos se aplicarem, a engenharia e suporte da AKS não podem ajudar na resolução de problemas ou no diagnóstico de modificações partidas/não funcionais ou aquelas que tornam o nó indisponível devido a um daemonset implantado pelo cliente.

> [!NOTE]
> A AKS como *um serviço gerido* tem objetivos finais, tais como remover a responsabilidade por patches, atualizações e recolha de registos para tornar a gestão do serviço mais completa e prática. À medida que a capacidade do serviço para a gestão de ponta a ponta aumenta, as futuras versões podem omitir algumas funções (por exemplo, reinicialização do nó e remendos automáticos).

### <a name="security-issues-and-patching"></a>Questões de segurança e remendos

Se uma falha de segurança for encontrada num ou mais componentes da AKS, a equipa AKS irá corrigir todos os clusters afetados para mitigar o problema. Em alternativa, a equipa dará orientação de upgrade aos utilizadores.

Para os nós dos trabalhadores que uma falha de segurança afeta, se estiver disponível um patch de tempo de inatividade zero, a equipa AKS aplicará esse patch e notificará os utilizadores da alteração.

Quando um patch de segurança requer que o nó do trabalhador seja reiniciado, a Microsoft notificará os clientes deste requisito. O cliente é responsável por reiniciar ou atualizar para obter o patch de cluster. Se os utilizadores não aplicarem os patches de acordo com a orientação da AKS, o seu cluster continuará vulnerável à questão de segurança.

### <a name="node-maintenance-and-access"></a>Manutenção e acesso do nó

Os nós dos trabalhadores são uma responsabilidade partilhada e são propriedade dos clientes. Por isso, os clientes têm a capacidade de se inscrever nos seus nós de trabalho e fazer alterações potencialmente nocivas, como atualizações de kernel e instalar ou remover pacotes.

Se os clientes fizerem alterações destrutivas ou fizerem com que os componentes do avião de controlo fiquem offline ou se tornem não funcionais, a AKS detetará esta falha e restaurará automaticamente o nó do trabalhador para o estado de trabalho anterior.

Embora os clientes possam inscrever-se e alterar os nós dos trabalhadores, fazê-lo é desencorajado porque as mudanças podem tornar um cluster insuportável.

## <a name="network-ports-access-and-nsgs"></a>Portas de rede, acesso e NSGs

Como um serviço gerido, a AKS tem requisitos específicos de networking e conectividade. Estes requisitos são menos flexíveis do que os requisitos para os componentes normais do IAAS. Em AKS, operações como personalizar regras NSG, bloquear uma porta específica (por exemplo, usar regras de firewall que bloqueiam a porta de saída 443), e URLs de whitelisting podem tornar o seu cluster insuportável.

> [!NOTE]
> Atualmente, a AKS não permite que bloqueie completamente o tráfego de saída do seu cluster. Para controlar a lista de URLs e portas o seu cluster pode usar para tráfego de saída ver [tráfego de saída limite](limit-egress-traffic.md).

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Características alfa e beta não suportadas

A AKS suporta apenas funcionalidades estáveis dentro do projeto Kubernetes a montante. Salvo documentação em contrário, a AKS não suporta funcionalidades alfa e beta que estejam disponíveis no projeto Kubernetes a montante.

Em dois cenários, as funcionalidades alfa ou beta podem ser lançadas antes de estarem geralmente disponíveis:

* Os clientes reuniram-se com o produto AKS, suporte ou equipas de engenharia e foram convidados a experimentar estas novas funcionalidades.
* Estas características foram [ativadas por uma bandeira de recurso.](https://github.com/Azure/AKS/blob/master/previews.md) Os clientes devem optar explicitamente pela utilização destas funcionalidades.

## <a name="preview-features-or-feature-flags"></a>Funcionalidades de pré-visualização ou bandeiras de recurso

Para funcionalidades e funcionalidades que requerem testes prolongados e feedback do utilizador, a Microsoft lança novas funcionalidades de pré-visualização ou funcionalidades por trás de uma bandeira de funcionalidade. Considere estas funcionalidades como funcionalidades pré-lançados ou beta.

As funcionalidades de pré-visualização ou as características da bandeira não são para produção. Mudanças contínuas em APIs e comportamento, correções de erros e outras alterações podem resultar em aglomerados instáveis e tempo de inatividade.

As funcionalidades de pré-visualização pública são enquadradas no suporte de "melhor esforço", uma vez que estas funcionalidades estão em pré-visualização e não se destinam à produção e são apoiadas pelas equipas de suporte técnico da AKS apenas durante o horário de trabalho. Para mais informações consulte:

* [FAQ de suporte Azure](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> As funcionalidades de pré-visualização têm efeito ao nível de *subscrição* do Azure. Não instale funcionalidades de pré-visualização numa subscrição de produção. Numa subscrição de produção, as funcionalidades de pré-visualização podem alterar o comportamento padrão da API e afetar as operações regulares.

## <a name="upstream-bugs-and-issues"></a>Bugs e problemas a montante

Dada a rapidez de desenvolvimento no projeto Kubernetes a montante, surgem invariavelmente bugs. Alguns destes bugs não podem ser remendados ou trabalhados dentro do sistema AKS. Em vez disso, as correções de bugs requerem patches maiores para projetos a montante (como Kubernetes, sistemas operativos de nó ou trabalhadores e núcleos). Para os componentes que a Microsoft detém (como o fornecedor de nuvem Azure), o pessoal da AKS e da Azure está empenhado em corrigir problemas a montante na comunidade.

Quando um problema de suporte técnico é causado por um ou mais bugs a montante, as equipas de suporte e engenharia da AKS:

* Identifique e ligue os bugs a montante com quaisquer detalhes de suporte para ajudar a explicar por que este problema afeta o seu cluster ou carga de trabalho. Os clientes recebem links para os repositórios necessários para que possam ver os problemas e ver quando um novo lançamento irá fornecer correções.
* Fornecer potenciais soluções ou mitigações. Se a questão puder ser atenuada, uma [questão conhecida](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) será arquivada no repositório AKS. O arquivo conhecido explica:
  * A questão, incluindo links para bugs a montante.
  * A solução e detalhes sobre uma atualização ou outra persistência da solução.
  * Prazos ásperos para a inclusão da edição, com base na cadência de libertação a montante.
