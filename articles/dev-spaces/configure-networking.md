---
title: Configurar a rede para Azure Dev Spaces em topologias de rede diferentes
services: azure-dev-spaces
ms.date: 01/10/2020
ms.topic: conceptual
description: Descreve os requisitos de rede para executar o Azure Dev Spaces nos serviços Kubernetess do Azure
keywords: Azure Dev Spaces, espaços de desenvolvimento, Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, CNI, kubenet, SDN, rede
ms.openlocfilehash: 51604e2862a4d2ff575906fa2ba480ddd10504ed
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897926"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Configurar a rede para Azure Dev Spaces em topologias de rede diferentes

O Azure Dev Spaces é executado em clusters do AKS (serviço kubernetes do Azure) com a configuração de rede padrão. Se você quiser alterar a configuração de rede do cluster AKS, como colocar o cluster atrás de um firewall, usar grupos de segurança de rede ou usar políticas de rede, você precisará incorporar considerações adicionais para a execução de Azure Dev Spaces.

![Configuração de rede virtual](media/configure-networking/virtual-network-clusters.svg)

## <a name="aks-clusters-with-different-virtual-network-or-subnet-configurations"></a>Clusters AKS com diferentes configurações de rede virtual ou sub-rede

O cluster AKS pode ter uma configuração de rede virtual ou de sub-rede diferente para restringir o tráfego de entrada ou saída para o cluster AKS. Por exemplo, o cluster pode estar protegido por um firewall, como o Firewall do Azure, ou você pode usar grupos de segurança de rede ou funções personalizadas para restringir o tráfego de rede.

Azure Dev Spaces tem determinados requisitos de tráfego de rede de *entrada e saída* , bem como tráfego *somente de entrada* . Se você estiver usando Azure Dev Spaces em um cluster AKS com uma configuração de rede virtual ou de sub-rede que restringe o tráfego para o cluster AKS, você deverá seguir os seguintes requisitos de tráfego de entrada e de entrada e saída para que o Azure Dev Spaces funcione corretamente.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Requisitos de tráfego de rede de entrada e saída

Azure Dev Spaces precisa de tráfego de entrada e saída para os seguintes FQDNs:

| FQDN                       | Porta       | Usar      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Para extrair imagens do Docker para Azure Dev Spaces |
| gcr.io                     | HTTPS: 443 | Para efetuar pull de imagens Helm para Azure Dev Spaces |
| storage.googleapis.com     | HTTPS: 443 | Para efetuar pull de imagens Helm para Azure Dev Spaces |
| azds-*. azds. Io             | HTTPS: 443 | Para se comunicar com os serviços de back-end Azure Dev Spaces para o controlador de Azure Dev Spaces. O FQDN exato pode ser encontrado em *dataplaneFqdn* no `USERPROFILE\.azds\settings.json` |

Atualize o firewall ou a configuração de segurança para permitir o tráfego de rede de e para todos os FQDNs acima. Por exemplo, se você estiver usando um firewall para proteger sua rede, os FQDNs acima deverão ser adicionados à regra de aplicativo do firewall para permitir o tráfego de e para esses domínios.

### <a name="ingress-only-network-traffic-requirements"></a>Requisitos de tráfego de rede somente de entrada

Azure Dev Spaces fornece roteamento de nível de namespace kubernetes, bem como acesso público a serviços usando seu próprio FQDN. Para que esses dois recursos funcionem, atualize sua configuração de firewall ou de rede para permitir a entrada pública para o endereço IP externo do controlador de entrada do Azure Dev Spaces no cluster. Como alternativa, você pode criar um [balanceador de carga interno][aks-internal-lb] e adicionar uma regra NAT em seu firewall para converter o IP público do firewall para o IP do balanceador de carga interno. Você também pode usar [traefik][traefik-ingress] ou [Nginx][nginx-ingress] para criar um controlador de entrada personalizado.

## <a name="aks-cluster-network-requirements"></a>Requisitos de rede de cluster AKS

O AKS permite que você use [políticas de rede][aks-network-policies] para controlar o tráfego de entrada e saída entre pods em um cluster, bem como o tráfego de saída de um pod. Azure Dev Spaces tem determinados requisitos de tráfego de rede de *entrada e saída* , bem como tráfego *somente de entrada* . Se você estiver usando Azure Dev Spaces em um cluster AKS com políticas de rede AKS, deverá seguir os seguintes requisitos de tráfego de entrada e entrada e saída para que os Azure Dev Spaces funcionem corretamente.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Requisitos de tráfego de rede de entrada e saída

Azure Dev Spaces permite que você se comunique diretamente com um pod em um espaço de desenvolvimento no cluster para depuração. Para que esse recurso funcione, adicione uma política de rede que permita a comunicação de entrada e saída para os endereços IP da infraestrutura de Azure Dev Spaces, que [variam por região][dev-spaces-ip-auth-range-regions].

### <a name="ingress-only-network-traffic-requirements"></a>Requisitos de tráfego de rede somente de entrada

Azure Dev Spaces fornece roteamento entre os pods entre os namespaces. Por exemplo, namespaces com Azure Dev Spaces habilitado podem ter uma relação pai/filho, que permite que o tráfego de rede seja roteado entre pods entre os namespaces pai e filho. Para que esse recurso funcione, adicione uma política de rede que permita o tráfego entre namespaces em que o tráfego de rede é roteado, como namespaces pai/filho. Além disso, se o controlador de entrada for implantado no namespace *azds* , o controlador de entrada precisará se comunicar com o pods instrumentado pelo espaço de desenvolvimento do Azure em um namespace diferente. Para que o controlador de entrada funcione corretamente, o tráfego de rede deve ser permitido do namespace *azds* para o namespace no qual os pods instrumentados estão em execução.

## <a name="using-azure-container-networking-with-azure-dev-spaces"></a>Usando a rede de contêiner do Azure com Azure Dev Spaces

Por padrão, os clusters AKS são configurados para usar o [kubenet][aks-kubenet] para rede, que funciona com Azure dev Spaces. Você também pode configurar o cluster AKS para usar o [CNI (interface de rede de contêiner do Azure)][aks-cni]. Para usar Azure Dev Spaces com o Azure CNI em seu cluster AKS, permita que seus espaços de rede virtual e de endereço de sub-rede tenham até 10 endereços IP privados para pods implantados pelo Azure Dev Spaces. Mais detalhes sobre como permitir endereços IP privados estão disponíveis na [documentação do AKS Azure CNI][aks-cni-ip-planning].

## <a name="using-api-server-authorized-ip-ranges-with-azure-dev-spaces"></a>Usando intervalos de IP autorizados do servidor de API com Azure Dev Spaces

Os clusters AKS permitem que você configure a segurança adicional que limita o endereço IP que pode interagir com seus clusters, por exemplo, usando redes virtuais personalizadas ou [protegendo o acesso ao servidor de API usando intervalos de IP autorizados][aks-ip-auth-ranges]. Para usar Azure Dev Spaces ao usar essa segurança adicional ao [criar][aks-ip-auth-range-create] o cluster, você deve [permitir intervalos adicionais com base em sua região][dev-spaces-ip-auth-range-regions]. Você também pode [Atualizar][aks-ip-auth-range-update] um cluster existente para permitir esses intervalos adicionais. Você também precisa permitir o endereço IP de qualquer computador de desenvolvimento que se conecta ao cluster AKS para depuração para se conectar ao servidor de API.

## <a name="using-aks-private-clusters-with-azure-dev-spaces"></a>Usando clusters privados AKS com Azure Dev Spaces

Neste momento, não há suporte para Azure Dev Spaces com [clusters privados AKs][aks-private-clusters].

## <a name="azure-dev-spaces-client-requirements"></a>Requisitos do cliente Azure Dev Spaces

Azure Dev Spaces usa ferramentas do lado do cliente, como a extensão da CLI do Azure Dev Spaces, a extensão Visual Studio Code e a extensão do Visual Studio, para se comunicar com o cluster AKS para depuração. Para usar a Azure Dev Spaces ferramentas do lado do cliente, permita o tráfego das máquinas de desenvolvimento para o domínio *azds-\*. azds.Io* . Consulte *dataplaneFqdn* em `USERPROFILE\.azds\settings.json` para obter o FQDN exato. Se estiver usando [intervalos de IP autorizados do servidor de API][auth-range-section], você também precisará permitir o endereço IP de qualquer computador de desenvolvimento que se conecta ao cluster AKs para depuração para se conectar ao servidor de API.

## <a name="next-steps"></a>Passos seguintes

Saiba como Azure Dev Spaces ajuda a desenvolver aplicativos mais complexos em vários contêineres e como você pode simplificar o desenvolvimento colaborativo trabalhando com diferentes versões ou branches do seu código em espaços diferentes.

> [!div class="nextstepaction"]
> [Desenvolvimento em equipe no Azure Dev Spaces][team-quickstart]

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges-with-azure-dev-spaces
[dev-spaces-ip-auth-range-regions]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[team-quickstart]: quickstart-team-development.md