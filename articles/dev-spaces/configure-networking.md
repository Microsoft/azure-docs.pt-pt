---
title: Configurar a rede para espaços Azure Dev em diferentes topologias de rede
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: Descreve os requisitos de networking para executar Azure Dev Spaces em Serviços Azure Kubernetes
keywords: Espaços Azure Dev, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, CNI, kubenet, SDN, rede
ms.openlocfilehash: 09114ab13555cbf9ef42b37c86ffb76a8fe3ab3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91970343"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Configurar a rede para espaços Azure Dev em diferentes topologias de rede

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

O Azure Dev Spaces funciona em clusters Azure Kubernetes Service (AKS) com a configuração de rede padrão. Se pretender alterar a configuração de rede do seu cluster AKS, como colocar o cluster atrás de uma firewall, utilizar grupos de segurança de rede ou utilizar políticas de rede, tem de incorporar considerações adicionais para executar a Azure Dev Spaces.

![Configuração de rede virtual](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>Configurações de rede virtual ou sub-redes

O seu cluster AKS pode ter uma rede virtual diferente ou configuração de sub-rede para restringir o tráfego de entrada ou saída para o seu cluster AKS. Por exemplo, o seu cluster pode estar por trás de uma firewall, como a Azure Firewall, ou pode usar Grupos de Segurança de Rede ou funções personalizadas para restringir o tráfego de rede. Pode encontrar uma configuração de rede de exemplo no [repositório de amostras Azure Dev Spaces no GitHub][sample-repo].

A Azure Dev Spaces tem certos requisitos para o tráfego da rede *Ingress e Egress,* bem como apenas o tráfego *de Ingress.* Se estiver a utilizar espaços Azure Dev num cluster AKS com uma configuração de rede virtual ou sub-rede que restringe o tráfego para o seu cluster AKS, deve seguir apenas os seguintes requisitos de entrada e de entrada e saída para que os Espaços Azure Dev funcionem corretamente.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Requisitos de tráfego de rede de entradas e saídas

A Azure Dev Spaces necessita de tráfego de entrada e saída para os seguintes FQDNs:

| FQDN                       | Porta       | Utilização      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Para tirar imagens de estivadores para Azure Dev Spaces |
| gcr.io                     | HTTPS: 443 | Para puxar imagens de leme para Azure Dev Spaces |
| storage.googleapis.com     | HTTPS: 443 | Para puxar imagens de leme para Azure Dev Spaces |

Atualize a sua configuração de firewall ou segurança para permitir o tráfego de rede de e para todos os serviços de infraestrutura de FQDNs e [Azure Dev Spaces][service-tags]acima referidos . Por exemplo, se estiver a utilizar uma firewall para proteger a sua rede, as FQDNs acima devem ser adicionadas à regra de aplicação da firewall e a etiqueta de serviço Azure Dev Spaces também deve ser [adicionada à firewall][firewall-service-tags]. Ambas as atualizações para a firewall são necessárias para permitir o tráfego de e para estes domínios.

### <a name="ingress-only-network-traffic-requirements"></a>Agregação apenas requisitos de tráfego de rede

A Azure Dev Spaces fornece o encaminhamento de nível de espaço de nome Kubernetes, bem como o acesso público a serviços usando o seu próprio FQDN. Para que ambas as funcionalidades funcionem, atualize a sua firewall ou configuração de rede para permitir a entrada pública no endereço IP externo do controlador de entrada de Azure Dev Spaces no seu cluster. Em alternativa, pode criar um [equilibrador de carga interno][aks-internal-lb] e adicionar uma regra NAT na sua firewall para traduzir o IP público da sua firewall para o IP do seu equilibrador de carga interno. Também pode usar [traefik][traefik-ingress] ou [NGINX][nginx-ingress] para criar um controlador de entrada personalizado.

## <a name="aks-cluster-network-requirements"></a>Requisitos de rede de cluster AKS

A AKS permite-lhe usar políticas de [rede][aks-network-policies] para controlar o tráfego de entradas e saídas entre as cápsulas de um cluster, bem como o tráfego de saída de uma vagem. A Azure Dev Spaces tem certos requisitos para o tráfego da rede *Ingress e Egress,* bem como apenas o tráfego *de Ingress.* Se estiver a utilizar espaços Azure Dev num cluster AKS com políticas de rede AKS, deve seguir apenas os seguintes requisitos de entrada e saída e saída para que os Espaços Azure Dev funcionem corretamente.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Requisitos de tráfego de rede de entradas e saídas

O Azure Dev Spaces permite-lhe comunicar diretamente com uma cápsula num espaço dev no seu cluster para depuração. Para que esta funcionalidade funcione, adicione uma política de rede que permite a comunicação de entradas e saídas aos endereços IP da infraestrutura Azure Dev Spaces, que [variam por região.][service-tags]

### <a name="ingress-only-network-traffic-requirements"></a>Agregação apenas requisitos de tráfego de rede

A Azure Dev Spaces fornece encaminhamento entre cápsulas através de espaços de nome. Por exemplo, os espaços de nome com espaços Azure Dev habilitados podem ter uma relação pai/filho, o que permite que o tráfego de rede seja encaminhado entre cápsulas através dos espaços de nome dos pais e filhos. A Azure Dev Spaces também expõe pontos finais de serviço usando o seu próprio FQDN. Para configurar diferentes formas de expor serviços e como impacta o encaminhamento de nível de nome do espaço ver [Usando diferentes opções de ponto final][endpoint-options].

## <a name="using-azure-cni"></a>Usando Azure CNI

Por padrão, os clusters AKS são configurados para usar [kubenet][aks-kubenet] para networking, que funciona com Azure Dev Spaces. Também pode configurar o seu cluster AKS para utilizar a [Interface de Rede de Contentores Azure (CNI)][aks-cni]. Para utilizar espaços Azure Dev com CNI Azure no seu cluster AKS, permita que a sua rede virtual e espaços de endereço de sub-rede até 10 endereços IP privados para cápsulas implantadas pela Azure Dev Spaces. Mais detalhes sobre a possibilidade de permitir endereços IP privados estão disponíveis na [documentação AKS Azure CNI][aks-cni-ip-planning].

## <a name="using-api-server-authorized-ip-ranges"></a>Utilizando gamas IP autorizadas pelo servidor API

Os clusters AKS permitem-lhe configurar uma segurança adicional que limita quais os endereços IP que podem interagir com os seus clusters, por exemplo, utilizando redes virtuais personalizadas ou [garantindo o acesso ao servidor API utilizando intervalos IP autorizados.][aks-ip-auth-ranges] Para utilizar o Azure Dev Spaces ao utilizar esta segurança adicional enquanto [cria][aks-ip-auth-range-create] o seu cluster, deve [permitir gamas adicionais com base na sua região.][service-tags] Também pode [atualizar][aks-ip-auth-range-update] um cluster existente para permitir essas gamas adicionais. Também precisa de permitir o endereço IP de quaisquer máquinas de desenvolvimento que se conectem ao seu cluster AKS para depurar para se ligar ao seu servidor API.

## <a name="using-aks-private-clusters"></a>Utilizando clusters privados AKS

Neste momento, a Azure Dev Spaces não é suportada com [clusters privados AKS.][aks-private-clusters]

## <a name="using-different-endpoint-options"></a>Usando diferentes opções de ponto final

A Azure Dev Spaces tem a opção de expor pontos finais para os seus serviços em execução na AKS. Ao ativar os Espaços Azure Dev no seu cluster, tem as seguintes opções para configurar o tipo de ponto final para o seu cluster:

* Um ponto final *público,* que é o padrão, implanta um controlador de entrada com um endereço IP público. O endereço IP público está registado no DNS do cluster, permitindo o acesso do público aos seus serviços através de um URL. Pode ver este URL utilizando `azds list-uris` .
* Um ponto final *privado* implanta um controlador de entrada com um endereço IP privado. Com um endereço IP privado, o balanceador de carga do seu cluster só está acessível a partir de dentro da rede virtual do cluster. O endereço IP privado do equilibrador de carga está registado no DNS do cluster para que os serviços dentro da rede virtual do cluster possam ser acedidos através de um URL. Pode ver este URL utilizando `azds list-uris` .
* A definição *de nenhuma* para a opção ponto final não provoca a implantação de nenhum controlador de entrada. Sem controlador de entrada implantado, as [capacidades de encaminhamento Azure Dev Spaces][dev-spaces-routing] não funcionarão. Opcionalmente, pode implementar a sua própria solução de controlador de entrada utilizando [traefik][traefik-ingress] ou [NGINX,][nginx-ingress]o que permitirá que as capacidades de encaminhamento voltem a funcionar.

Para configurar a sua opção ponto final, utilize *-e* ou *--ponto final* ao ativar os espaços Azure Dev no seu cluster. Por exemplo:

> [!NOTE]
> A opção ponto final requer que esteja a executar a versão 2.2.0 ou posterior do Azure CLI. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>Requisitos do cliente

O Azure Dev Spaces utiliza ferramentas do lado do cliente, como a extensão CLI do Azure Dev Spaces, a extensão do Código do Estúdio Visual e a extensão do Visual Studio, para comunicar com o seu cluster AKS para depuração. Para utilizar a ferramenta do lado do cliente da Azure Dev Spaces, permita o tráfego das máquinas de desenvolvimento até à [infraestrutura Azure Dev Spaces][dev-spaces-allow-infrastructure]. Se utilizar [os intervalos IP autorizados pelo servidor API,][auth-range-section]também precisa de permitir o endereço IP de quaisquer máquinas de desenvolvimento que se conectem ao seu cluster AKS para depurar para se ligar ao seu servidor API.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o funcionamento da Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Como funciona o Azure Dev Spaces](how-dev-spaces-works.md)

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges
[azure-cli-install]: /cli/azure/install-azure-cli
[dev-spaces-allow-infrastructure]: #virtual-network-or-subnet-configurations
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[firewall-service-tags]: ../firewall/service-tags.md
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[sample-repo]: https://github.com/Azure/dev-spaces/tree/master/advanced%20networking
[service-tags]: ../virtual-network/service-tags-overview.md#available-service-tags