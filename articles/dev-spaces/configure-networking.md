---
title: Configure a rede para espaços Azure Dev em diferentes topoologias de rede
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: Descreve os requisitos de networking para executar espaços Azure Dev nos Serviços Azure Kubernetes
keywords: Espaços Azure Dev, Espaços Dev, Docker, Kubernetes, Azure, AKS, Serviço Azure Kubernetes, contentores, CNI, kubenet, SDN, rede
ms.openlocfilehash: 3e344576caf276ae7cb5fe00395c84810a4e7d32
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262048"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Configure a rede para espaços Azure Dev em diferentes topoologias de rede

A Azure Dev Spaces funciona em clusters do Serviço Azure Kubernetes (AKS) com a configuração de rede padrão. Se pretender alterar a configuração de rede do seu cluster AKS, como colocar o cluster atrás de uma firewall, utilizar grupos de segurança de rede ou utilizar políticas de rede, tem de incorporar considerações adicionais para executar espaços Azure Dev.

![Configuração da rede virtual](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>Configurações de rede virtual ou sub-rede

O seu cluster AKS pode ter uma rede virtual diferente ou configuração de sub-rede para restringir o tráfego de ingresso ou fuga para o seu cluster AKS. Por exemplo, o seu cluster pode estar por detrás de uma firewall, como o Azure Firewall, ou pode utilizar grupos de segurança de rede ou funções personalizadas para restringir o tráfego de rede. Pode encontrar uma configuração de rede de exemplo no [repositório de amostras Dev Spaces azure no GitHub][sample-repo].

A Azure Dev Spaces tem determinados requisitos para o tráfego da rede *Ingress e Egress,* bem como apenas para o tráfego *de Ingress.* Se estiver a utilizar espaços Azure Dev num cluster AKS com uma rede virtual ou configuração de sub-rede que restringe o tráfego para o seu cluster AKS, deve seguir a seguinte entrada apenas e entrar e retirar os requisitos de tráfego para que os Espaços Azure Dev funcionem corretamente.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Requisitos de tráfego de rede de ingresso e de saída

A Azure Dev Spaces necessita de ingresso e de tráfego de saída para seguir FQDNs:

| FQDN                       | Porta       | Utilizar      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Para puxar imagens de estivador para espaços Azure Dev |
| gcr.io                     | HTTPS: 443 | Para puxar imagens de leme para espaços Azure Dev |
| storage.googleapis.com     | HTTPS: 443 | Para puxar imagens de leme para espaços Azure Dev |
| azds-*.azds.io             | HTTPS: 443 | Para comunicar com os serviços de backend da Azure Dev Spaces para o controlador Azure Dev Spaces. O FQDN exato pode ser encontrado em *dataplaneFqdn* em`USERPROFILE\.azds\settings.json` |

Atualize a sua firewall ou configuração de segurança para permitir o tráfego de rede de e para todas as FQDNs acima referidas. Por exemplo, se estiver a utilizar uma firewall para proteger a sua rede, as FQDNs acima devem ser adicionadas à regra de aplicação da firewall para permitir o tráfego de e para estes domínios.

### <a name="ingress-only-network-traffic-requirements"></a>Ingress apenas requisitos de tráfego de rede

A Azure Dev Spaces fornece o encaminhamento de nível de espaço kubernetes, bem como o acesso público aos serviços usando o seu próprio FQDN. Para que ambas as funcionalidades funcionem, atualize a sua firewall ou configuração de rede para permitir a entrada pública no endereço IP externo do controlador de entrada Dev Spaces Azure No seu cluster. Em alternativa, pode criar um [equilibrista][aks-internal-lb] interno de carga e adicionar uma regra NAT na sua firewall para traduzir o IP público da sua firewall para o IP do seu equilíbrio interno de carga. Também pode utilizar [traefik][traefik-ingress] ou [NGINX][nginx-ingress] para criar um controlador de entrada personalizado.

## <a name="aks-cluster-network-requirements"></a>Requisitos da rede de cluster AKS

O AKS permite-lhe utilizar políticas de [rede][aks-network-policies] para controlar a entrada e a fuga de tráfego entre as cápsulas num cluster, bem como a fuga de tráfego de uma cápsula. A Azure Dev Spaces tem determinados requisitos para o tráfego da rede *Ingress e Egress,* bem como apenas para o tráfego *de Ingress.* Se estiver a utilizar o Azure Dev Spaces num cluster AKS com políticas de rede AKS, deve seguir a seguinte entrada apenas e entrar e retirar os requisitos de tráfego para que os Espaços Azure Dev funcionem corretamente.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Requisitos de tráfego de rede de ingresso e de saída

A Azure Dev Spaces permite-lhe comunicar diretamente com uma cápsula num espaço de v no seu cluster para depuração. Para que esta funcionalidade funcione, adicione uma política de rede que permita a comunicação ingresse e a saída para os endereços IP da infraestrutura azure Dev Spaces, que [variam por região.][dev-spaces-ip-auth-range-regions]

### <a name="ingress-only-network-traffic-requirements"></a>Ingress apenas requisitos de tráfego de rede

A Azure Dev Spaces fornece o encaminhamento entre cápsulas através de espaços de nome. Por exemplo, espaços com nomes com espaços Azure Dev habilitados podem ter uma relação pai/filho, que permite que o tráfego de rede seja encaminhado entre cápsulas através dos espaços de nome dos pais e da criança. A Azure Dev Spaces também expõe pontos finais de serviço utilizando o seu próprio FQDN. Para configurar diferentes formas de expor serviços e como impacta o encaminhamento do nível do espaço de nome ver [usando diferentes opções de ponto final][endpoint-options].

## <a name="using-azure-cni"></a>Utilização de Azure CNI

Por padrão, os clusters AKS são configurados para usar [kubenet][aks-kubenet] para networking, que funciona com os Espaços Azure Dev. Também pode configurar o seu cluster AKS para utilizar a Interface de Rede de [Contentores Azure (CNI)][aks-cni]. Para utilizar espaços Azure Dev com Azure CNI no seu cluster AKS, permita a sua rede virtual e espaços de endereços de subnet até 10 endereços IP privados para cápsulas implantadas pela Azure Dev Spaces. Mais detalhes sobre a possibilidade de endereços IP privados estão disponíveis na [documentação AKS Azure CNI.][aks-cni-ip-planning]

## <a name="using-api-server-authorized-ip-ranges"></a>Utilização de gamas IP autorizadas pelo servidor API

Os clusters AKS permitem configurar a segurança adicional que limita o endereço IP que pode interagir com os seus clusters, por exemplo, utilizando redes virtuais personalizadas ou [garantindo o acesso ao servidor API utilizando gamas IP autorizadas][aks-ip-auth-ranges]. Para utilizar os Espaços Azure Dev ao utilizar esta segurança adicional enquanto [cria][aks-ip-auth-range-create] o seu cluster, deve [permitir gamas adicionais baseadas na sua região][dev-spaces-ip-auth-range-regions]. Também pode [atualizar][aks-ip-auth-range-update] um cluster existente para permitir essas gamas adicionais. Também precisa de permitir que o endereço IP de quaisquer máquinas de desenvolvimento que se conectem ao seu cluster AKS para depuração se ligue ao seu servidor API.

## <a name="using-aks-private-clusters"></a>Usando clusters privados AKS

Neste momento, a Azure Dev Spaces não é apoiada com [clusters privados AKS.][aks-private-clusters]

## <a name="using-different-endpoint-options"></a>Usando diferentes opções de ponto final

A Azure Dev Spaces tem a opção de expor pontos finais para os seus serviços em funcionamento no AKS. Ao ativar o Azure Dev Spaces no seu cluster, tem as seguintes opções para configurar o tipo de ponto final para o seu cluster:

* Um ponto final *público,* que é o padrão, implementa um controlador de ingresso com um endereço IP público. O endereço IP público está registado no DNS do cluster, permitindo o acesso público aos seus serviços através de um URL. Pode ver este `azds list-uris`URL usando .
* Um ponto final *privado* implementa um controlador de ingresso com um endereço IP privado. Com um endereço IP privado, o equilibrista de carga para o seu cluster só é acessível a partir de dentro da rede virtual do cluster. O endereço IP privado do equilibrista de carga está registado no DNS do cluster para que os serviços dentro da rede virtual do cluster possam ser acedidos através de um URL. Pode ver este `azds list-uris`URL usando .
* A definição *de nenhuma* para a opção de ponto final não faz com que nenhum controlador de entrada seja implantado. Sem o controlador de ingresso implantado, as [capacidades de encaminhamento dos Espaços Azure Dev][dev-spaces-routing] não funcionarão. Opcionalmente, pode implementar a sua própria solução de controlador de ingresso utilizando [traefik][traefik-ingress] ou [NGINX,][nginx-ingress]o que permitirá que as capacidades de encaminhamento funcionem novamente.

Para configurar a sua opção de ponto final, use *-e* ou *-endpoint* ao ativar o Azure Dev Spaces no seu cluster. Por exemplo:

> [!NOTE]
> A opção ponto final requer que esteja a executar a versão 2.2.0 do Azure CLI ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>Requisitos do cliente

A Azure Dev Spaces utiliza ferramentas do lado do cliente, como a extensão CLI dos Espaços Azure Dev, extensão do Código do Estúdio Visual e extensão do Estúdio Visual, para comunicar com o seu cluster AKS para depuração. Para utilizar a ferramenta do lado do cliente Azure Dev Spaces, permita o tráfego das máquinas de desenvolvimento para o domínio *azds-\*.azds.io.* Consulte *o dataplaneFqdn* para `USERPROFILE\.azds\settings.json` obter o FQDN exato. Se utilizar [intervalos IP autorizados pelo servidor API,][auth-range-section]também precisa de permitir o endereço IP de quaisquer máquinas de desenvolvimento que se conectem ao seu cluster AKS para depuração para se ligar ao seu servidor API.

## <a name="next-steps"></a>Passos seguintes

Saiba como o Azure Dev Spaces o ajuda a desenvolver aplicações mais complexas em vários recipientes e como pode simplificar o desenvolvimento colaborativo trabalhando com diferentes versões ou ramos do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Desenvolvimento de equipa em Espaços Azure Dev][team-quickstart]

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
[dev-spaces-ip-auth-range-regions]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[sample-repo]: https://github.com/Azure/dev-spaces/tree/master/advanced%20networking
[team-quickstart]: quickstart-team-development.md