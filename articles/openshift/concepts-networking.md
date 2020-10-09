---
title: Conceitos - Diagrama de networking para chapéu vermelho Azure em OpenShift 4
description: Diagrama de rede e visão geral para a rede Azure Red Hat OpenShift
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 06/22/2020
ms.openlocfilehash: fb81405e85d6e2653e0cf6c007c363493992161a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87419976"
---
# <a name="networking-in-azure-red-hat-on-openshift-4"></a>Networking em Azure Red Hat no OpenShift 4

Este guia cobre uma visão geral da ligação em rede em Azure Red Hat em clusters OpenShift 4, juntamente com um diagrama e uma lista de pontos finais importantes.

Para obter mais detalhes sobre os conceitos de rede openshift core, consulte a [documentação de networking Azure Red Hat OpenShift 4](https://docs.openshift.com/aro/4/networking/understanding-networking.html).

## <a name="networking-concepts-in-azure-red-hat-openshift"></a>Conceitos de networking em Azure Red Hat OpenShift

![Diagrama de rede Azure Red Hat OpenShift 4](./media/concepts-networking/aro4-networking-diagram.png)

Quando coloca o Chapéu Vermelho Azure no OpenShift 4, todo o seu cluster está contido numa rede virtual. Dentro desta rede virtual, os seus nós mestres e trabalhadores acenam cada um com a sua própria sub-rede. Cada sub-rede utiliza um equilibrador de carga público e interno.

## <a name="explanation-of-endpoints"></a>Explicação dos pontos finais

A lista que se segue abrange pontos finais importantes num cluster Azure Red Hat OpenShift.

* **aro-pls**
    * Este é um ponto final de Ligação Privada Azure, usado pelos engenheiros de fiabilidade do site Microsoft e Red Hat para ajudar a gerir o cluster.
* **aro-interno-lb**
    * Este ponto final equilibra o tráfego para o servidor api. Para este equilibrador de carga, os nós mestres estão na piscina de backend.
* **aro-público-lb**
    * Se a api for pública, este ponto final encaminha e equilibra o tráfego para o servidor api. Este ponto final atribui um IP de saída pública para que os mestres possam aceder ao Azure Resource Manager e informar sobre a saúde do cluster.
* **aro-interno**
    * Este ponto final equilibra o tráfego interno do serviço. Para este equilibrador de carga, os nós dos trabalhadores estão na piscina de backend.
    * Este equilibrador de carga não é criado por padrão. Este equilibrador de carga é criado assim que criar um serviço de tipo LoadBalancer com as anotações corretas. Por exemplo: service.beta.kubernetes.io/azure-load-balancer-internal: "verdadeiro".
* **Políticas de Rede (entrada)**
    * Suportado como parte do SDN OpenShift
    * Habilitado por defeito, execução feita pelos clientes
    * V1 NetworkPolicy em conformidade, no entanto, os tipos "Egress e IPBlock" ainda não são suportados
    * **aro**
    * Este ponto final equilibra o tráfego para o servidor api. Para este equilibrador de carga, os nós mestres estão na piscina de backend.
  * **aro-interno-lb**
    * Este ponto final é utilizado para qualquer tráfego público. Quando cria e aplica e uma rota, este é o caminho para o tráfego de entrada.
    * Este equilibrador de carga também cobre a conectividade da Internet de qualquer casulo que entre nos nós dos trabalhadores através das Regras de Saída do Balancer de Carga Azure.
        * Atualmente, as regras de saída não são configuráveis. Atribuem 1.024 portas TCP a cada nó.
        * Desativar O OutboundSnat não está configurado nas regras LB, pelo que as cápsulas podem obter como IP de saída qualquer IP público configurado neste ALB.
        * Como consequência dos dois pontos anteriores, a única forma de adicionar portas efémeras de SNAT é adicionando serviços públicos do tipo LoadBalancer à ARO.
* **Políticas de Rede (Egress)**
    * As Políticas Egress são suportadas utilizando a funcionalidade Egress Firewall em OpenShift.
    * Apenas um por espaço/projeto de nome.
    * As políticas de Egress não são suportadas no espaço de nome "predefinido".
    * As regras de política da Egress são avaliadas por ordem (primeiro a durar).
    * **aro-out-pip**
        * Este ponto final serve como UM IP público (PIP) para os nós do trabalhador.
        * Este ponto final permite que os serviços adicionem um IP específico proveniente de um cluster Azure Red Hat OpenShift a uma lista de permitis.
* **aro-node-nsg**
    * Quando expõe um serviço, a API cria uma regra neste grupo de segurança de rede para que o tráfego flua e chegue aos nós.
    * Por predefinição, este grupo de segurança de rede permite todo o tráfego de saída. Atualmente, o tráfego de saída só pode ser restringido ao avião de controlo Azure Red Hat OpenShift.
* **aro-controle-nsg**
    * Este ponto final só permite que o tráfego entre pelo porto 6443 para os nós principais.
* **Azure Container Registry**
    * Este é um registo de contentores que forneceu e utilizou internamente pela Microsoft.
        * Este registo fornece imagens de plataforma hospedeira e componentes de cluster. Por exemplo, monitorizar ou registar contentores.
        * Não se destina a ser utilizado pelos clientes Azure Red Hat OpenShift.  
        * Leia apenas.
        * As ligações a este registo ocorrem sobre o ponto final de serviço (conectividade interna entre os serviços Azure).
        * Este registo interno não está disponível fora do cluster por padrão.
* **Ligação Privada**
    * Permite a conectividade da rede do plano de gestão para um cluster para gestão de clusters.
    * Engenheiros de fiabilidade do site Microsoft e Red Hat para ajudar a gerir o seu cluster.

## <a name="networking-basics-in-openshift"></a>Básicos de networking em OpenShift

O OpenShift Software Defined Networking (SDN) é utilizado para configurar uma rede de sobreposição utilizando Open vSwitch (OVS), uma implementação OpenFlow baseada na especificação de Interface de Rede de Contentores (CNI). O SDN suporta diferentes plugins, e a Política de Rede é o plugin usado no Azure Red Hat no OpenShift 4. Todas as comunicações de rede são geridas pela SDN, pelo que não são necessárias rotas extra nas suas redes virtuais para conseguir a comunicação pod to pod.

## <a name="azure-red-hat-openshift-networking-specifics"></a>Azure Red Hat OpenShift especificidades de rede

As seguintes funcionalidades são específicas do Azure Red Hat OpenShift:
* Traga a sua própria rede virtual é suportada.
* Os CIDRs da Rede pod e serviço são configuráveis.
* Nós e mestres estão em diferentes sub-redes.
* As sub-redes de rede virtual de nós e masters devem ser mínimas /27.
* O POD CIDR deve ter um tamanho mínimo /18 (a rede de cápsulas é iPs não roteable, e só é utilizada dentro do SDN OpenShift).
* Cada nó é atribuído /23 sub-redes (512 IPs) para as suas cápsulas. Este valor não pode ser alterado.
* Não é possível anexar uma cápsula a várias redes.
* Não é possível configurar o IP estático da Egress. (Esta é uma funcionalidade OpenShift. Para obter informações, consulte [configurar iPs](https://docs.openshift.com/aro/4/networking/openshift_sdn/assigning-egress-ips.html)de saída de edição ).

## <a name="network-settings"></a>Definições de rede

As seguintes definições de rede estão disponíveis no Azure Red Hat OpenShift 4:

* **Visibilidade API** - Desaprote a visibilidade da API ao executar o [comando az aro create](tutorial-create-cluster.md#create-the-cluster).
    * "Público" - O Servidor API é acessível por redes externas.
    * "Private" - API Server atribuiu um IP privado à sub-rede masters, apenas acessível através de redes conectadas (redes virtuais espreitadas, outras sub-redes no cluster). Será criada uma Zona DE DNS privada em nome do cliente.
* **Ingress Visibilidade** - Desemita a visibilidade da API ao executar o [comando az aro create](tutorial-create-cluster.md#create-the-cluster).
    * As rotas "públicas" serão indefinidas para o Esquilibr de Carga Padrão Azure (isto pode ser alterado).
    * As rotas "privadas" serão incumpridoras para o balanceador interno de carga (isto pode ser alterado).

## <a name="network-security-groups"></a>Grupos de segurança de rede
Os grupos de segurança da rede serão criados no grupo de recursos do nó, que está bloqueado. Os grupos de segurança da rede são atribuídos diretamente às sub-redes, não aos NICs do nó. Os grupos de segurança da rede são imutáveis, o que significa que não tem permissões para alterá-los. 

No entanto, com um servidor API publicamente visível, não é possível criar grupos de segurança de rede e atribuí-los aos NICs.

## <a name="domain-forwarding"></a>Encaminhamento de domínio
O Azure Red Hat OpenShift utiliza o CoreDNS. O encaminhamento de domínios pode ser configurado (consulte a documentação sobre a utilização do [encaminhamento de DNS](https://docs.openshift.com/aro/4/networking/dns-operator.html#nw-dns-forward_dns-operator) para mais detalhes).

Atualmente, não pode trazer o seu próprio DNS para as suas redes virtuais.


Para obter mais informações sobre o tráfego de saída e o que o Azure Red Hat OpenShift suporta para a saída, consulte a documentação das políticas de [apoio.](support-policies-v4.md)
