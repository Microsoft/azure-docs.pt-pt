---
title: Conceitos - Diagrama de networking para chapéu vermelho Azure em OpenShift 4
description: Diagrama de rede e visão geral para a rede Azure Red Hat OpenShift
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 11/23/2020
ms.openlocfilehash: b785a6b73f595072f5d723bad8c119ddc4dc0f11
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636342"
---
# <a name="network-concepts-for-azure-red-hat-openshift-aro"></a>Conceitos de rede para Azure Red Hat OpenShift (ARO)

Este guia cobre uma visão geral da ligação em rede em Azure Red Hat OpenShift em clusters OpenShift 4, juntamente com um diagrama e uma lista de pontos finais importantes. Para obter mais informações sobre os conceitos de rede openshift core, consulte a [documentação de networking Azure Red Hat OpenShift 4](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html).

![Diagrama de rede Azure Red Hat OpenShift 4](./media/concepts-networking/aro4-networking-diagram.png)

Quando coloca o Chapéu Vermelho Azure no OpenShift 4, todo o seu cluster está contido numa rede virtual. Dentro desta rede virtual, os seus nós mestres e trabalhadores acenam cada um com a sua própria sub-rede. Cada sub-rede utiliza um equilibrador de carga interno e um equilibrador de carga pública.

## <a name="networking-components"></a>Componentes de rede

A lista que se segue abrange componentes importantes de rede num cluster Azure Red Hat OpenShift.

* **aro-pls**
    * Este é um ponto final de Ligação Privada Azure, usado pelos engenheiros de fiabilidade do site Microsoft e Red Hat para gerir o cluster.
* **aro-interno-lb**
    * Este ponto final equilibra o tráfego para o servidor api. Para este equilibrador de carga, os nós mestres estão na piscina de backend.
* **aro-público-lb**
    * Se a api for pública, este ponto final encaminha e equilibra o tráfego para o servidor api. Este ponto final atribui um IP de saída pública para que os mestres possam aceder ao Azure Resource Manager e informar sobre a saúde do cluster.
* **aro-interno**
    * Este ponto final equilibra o tráfego interno do serviço. Para este equilibrador de carga, os nós dos trabalhadores estão na piscina de backend.
    * Este equilibrador de carga não é criado por padrão. Este equilibrador de carga é criado assim que criar um serviço de tipo LoadBalancer com as anotações corretas. Por exemplo: service.beta.kubernetes.io/azure-load-balancer-internal: "verdadeiro".
* **aro-interno-lb**
    * Este ponto final é utilizado para qualquer tráfego público. Quando cria e aplica e uma rota, este é o caminho para o tráfego de entrada.
    * Este equilibrador de carga também cobre a conectividade da Internet de qualquer casulo que entre nos nós dos trabalhadores através das Regras de Saída do Balancer de Carga Azure.
        * Atualmente, as regras de saída não são configuráveis. Atribuem 1.024 portas TCP a cada nó.
        * Desativar O OutboundSnat não está configurado nas regras LB, pelo que as cápsulas podem obter como IP de saída qualquer IP público configurado neste ALB.
        * Como consequência dos dois pontos anteriores, a única forma de adicionar portas efémeras de SNAT é adicionando serviços públicos do tipo LoadBalancer à ARO.
* **aro-out-pip**
    * Este ponto final serve como UM IP público (PIP) para os nós do trabalhador.
    * Este ponto final permite que os serviços adicionem um IP específico proveniente de um cluster Azure Red Hat OpenShift a uma lista de permitis.
* **aro-nsg**
    * Quando expõe um serviço, a API cria uma regra neste grupo de segurança de rede para que o tráfego flua e chegue ao avião de controlo e aos nós.
    * Por predefinição, este grupo de segurança de rede permite todo o tráfego de saída. Atualmente, o tráfego de saída só pode ser restringido ao avião de controlo Azure Red Hat OpenShift.
* **aro-controle-nsg**
  * Este ponto final só permite que o tráfego entre pelo porto 6443 para os nós principais.
* **Azure Container Registry**
    * Este é um registo de contentores que forneceu e utilizou internamente pela Microsoft. Este registo é apenas de leitura e não se destina a ser utilizado pelos utilizadores Azure Red Hat OpenShift.
        * Este registo fornece imagens de plataforma hospedeira e componentes de cluster. Por exemplo, monitorizar ou registar contentores.
        * As ligações a este registo ocorrem sobre o ponto final de serviço (conectividade interna entre os serviços Azure).
        * Este registo interno não está disponível fora do cluster por padrão.
* **Ligação Privada**
    * Permite a conectividade da rede do plano de gestão para um cluster para os engenheiros de fiabilidade do site Microsoft e Red Hat para ajudar a gerir o seu cluster.

## <a name="networking-policies"></a>Políticas de networking

* **Ingress**: A política de networking ingress é suportada como parte da [OpenShift SDN](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/about-openshift-sdn.html). Esta política de rede é ativada por padrão, e a execução é executada pelos utilizadores. Embora a política de rede de entrada seja compatível com a V1 NetworkPolicy, os Tipos Egress e IPBlock não são suportados.

* **Egress**: As políticas de rede de saídas são suportadas utilizando a funcionalidade [de firewall de saída](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/configuring-egress-firewall.html) em OpenShift. Há apenas uma política de saída por espaço de nome/projeto. As políticas de Egress não são suportadas no espaço de nome "predefinido" e são avaliadas por ordem (primeiro a último).

## <a name="networking-basics-in-openshift"></a>Básicos de networking em OpenShift

O OpenShift Software Defined Networking [(SDN)](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/about-openshift-sdn.html) é utilizado para configurar uma rede de sobreposição utilizando Open vSwitch [(OVS)](https://www.openvswitch.org/), uma implementação OpenFlow baseada na especificação de Interface de Rede de Contentores (CNI). O SDN suporta diferentes plugins -- A Política de Rede é o plugin usado no Azure Red Hat no OpenShift 4. Todas as comunicações de rede são geridas pela SDN, pelo que não são necessárias rotas extra nas suas redes virtuais para conseguir a comunicação pod to pod.

## <a name="networking--for-azure-red-hat-openshift"></a>Networking para Azure Red Hat OpenShift

As seguintes funcionalidades de networking são específicas do Azure Red Hat OpenShift:  
* Os utilizadores podem criar o seu cluster ARO numa rede virtual existente ou criar uma rede virtual ao criar o seu cluster ARO.
* Os CIDRs da Rede pod e serviço são configuráveis.
* Nós e mestres estão em diferentes sub-redes.
* As sub-redes de rede virtual de nós e masters devem ser mínimas /27.
* O CIDR do Casulo Padrão é de 10.128.0.0/14.
* O CIDR de Serviço Predefinido é de 172.30.0.0/16.
* Os CIDRs da Rede pod e serviço não devem sobrepor-se a outros intervalos de endereços utilizados na sua rede, e não devem estar dentro do intervalo de endereços IP da rede virtual do seu cluster.
* O POD CIDR deve ter um tamanho mínimo /18. (A rede de cápsulas é iPs não-encaminháveis, e só é usada dentro do SDN OpenShift.)
* Cada nó é atribuído /23 sub-redes (512 IPs) para as suas cápsulas. Este valor não pode ser alterado.
* Não é possível anexar uma cápsula a várias redes.
* Não é possível configurar o IP estático da Egress. (Esta é uma funcionalidade OpenShift. Para obter informações, consulte [configurar iPs](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/assigning-egress-ips.html)de saída de edição ).

## <a name="network-settings"></a>Definições de rede

As seguintes definições de rede estão disponíveis para clusters Azure Red Hat OpenShift 4:

* **Visibilidade API** - Desaprote a visibilidade da API ao executar o [comando az aro create](tutorial-create-cluster.md#create-the-cluster).
    * "Público" - O Servidor API é acessível por redes externas.
    * "Private" - API Server atribuiu um IP privado à sub-rede masters, apenas acessível através de redes conectadas (redes virtuais espreitadas, outras sub-redes no cluster). Será criada uma Zona DE DNS privada em nome do cliente.
* **Ingress Visibilidade** - Desemita a visibilidade da API ao executar o [comando az aro create](tutorial-create-cluster.md#create-the-cluster).
    * As rotas "públicas" serão infringidas por um Balanceador de Carga Padrão público (isto pode ser alterado).
    * As rotas "privadas" serão incumpridoras para o balanceador interno de carga (isto pode ser alterado).

## <a name="network-security-groups"></a>Grupos de segurança de rede
Os grupos de segurança da rede são criados no grupo de recursos do nó, que está bloqueado para os utilizadores. Os grupos de segurança da rede são atribuídos diretamente às sub-redes, não aos NICs do nó. Os grupos de segurança da rede são imutáveis e os utilizadores não têm as permissões para alterá-los.

Com um servidor API publicamente visível, não é possível criar grupos de segurança de rede e atribuí-los aos NICs.

## <a name="domain-forwarding"></a>Encaminhamento de domínio
O Azure Red Hat OpenShift utiliza o CoreDNS. O encaminhamento de domínio pode ser configurado. Não pode trazer o seu próprio DNS para as suas redes virtuais. Para obter mais informações, consulte a documentação sobre [a utilização do reencaminhamento de DNS](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html#nw-dns-forward_dns-operator).

## <a name="whats-new-in-openshift-45"></a>Novidades em OpenShift 4.5

Com o apoio do OpenShift 4.5, o Azure Red Hat OpenShift introduziu algumas mudanças arquitetónicas significativas. Estas alterações aplicam-se apenas aos clusters recém-criados que executam o OpenShift 4.5. Os clusters existentes que foram atualizados para OpenShift 4.5 não terão a sua arquitetura de networking alterada pelo processo de upgrade. Os utilizadores terão de recriar os seus clusters para utilizar esta nova arquitetura.

![Diagrama de rede Azure Red Hat OpenShift 4.5](./media/concepts-networking/aro-4-5-networking-diagram.png)

Como incluído no diagrama acima, você vai notar algumas alterações:
* Anteriormente, a ARO usou dois LoadBalancers públicos: um para o servidor API e outro para o conjunto de nós operários. Com esta atualização de arquitetura, esta foi consolidada sob um único LoadBalancer. 
* Para reduzir a complexidade, foram removidos os recursos de endereço IP dedicados ao outboard.
* O avião de controlo da ARO partilha agora o mesmo grupo de segurança de rede que os nós operários da ARO.

Para obter mais informações sobre o OpenShift 4.5, consulte as [notas de lançamento OpenShift 4.5](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o tráfego de saída e o que o Azure Red Hat OpenShift suporta para a saída, consulte a documentação das políticas de [apoio.](support-policies-v4.md)
