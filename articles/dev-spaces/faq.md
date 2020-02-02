---
title: Perguntas frequentes sobre Azure Dev Spaces
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Encontre respostas para algumas das perguntas mais comuns sobre Azure Dev Spaces
keywords: 'Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S '
ms.openlocfilehash: 964fa9ec4948bf178c310af8e35913fda5f70c0f
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934187"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Perguntas frequentes sobre Azure Dev Spaces

Isso aborda as perguntas frequentes sobre Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Quais regiões do Azure atualmente fornecem Azure Dev Spaces?

Consulte [regiões com suporte][supported-regions] para obter uma lista completa de regiões disponíveis.

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>Posso usar espaços Azure Dev com ficheiros Dev existentes ou gráficos Helm?

Sim, se o seu projeto já tiver um Dockerfile ou um gráfico Helm, pode usar esses ficheiros com o Azure Dev Spaces. Quando executar `azds prep`, utilize o parâmetro `--chart` e especifique a localização do gráfico. A Azure Dev Spaces continuará a gerar um ficheiro *azds.yaml* e *Dockerfile.develop,* mas não substituirá ou modificará um Dockerfile existente ou um gráfico Helm. Pode ser necessário modificar os *ficheiros azds.yaml* e *Dockerfile.de forma* a que tudo funcione corretamente com a sua aplicação existente ao executar `azds up`.

Ao utilizar o seu próprio Dockerfile ou gráfico Helm, existem as seguintes limitações:
* Se utilizar apenas um Dockerfile, deve incluir tudo o que precisa para permitir cenários de desenvolvimento, como o SDK linguístico e não apenas o tempo de execução. Se utilizar um Dockerfile separado para espaços Azure Dev, como um Dockerfile.develop, tudo o que precisa para permitir cenários de desenvolvimento deve ser incluído nesse Dockerfile.
* O seu gráfico Helm deve apoiar a passagem de parte ou de toda a etiqueta de imagem como um valor a partir de *valores.yaml*.
* Se estiver a modificar qualquer coisa com ingresso, também pode atualizar o seu gráfico Helm para utilizar a solução de entrada fornecida pela Azure Dev Spaces.
* Se pretender utilizar as capacidades de [encaminhamento fornecidas pela Azure Dev Spaces,][dev-spaces-routing]todos os serviços para um projeto individual devem caber num único espaço de nome kubernetes e devem ser implantados com nomeação simples, por *exemplo, serviço-a*. Nos gráficos padrão helm, esta atualização de nomeação pode ser feita especificando um valor para a propriedade *fullnameOverride.*

Para comparar o seu próprio Dockerfile ou gráfico Helm com uma versão existente que funciona com a Azure Dev Spaces, reveja os ficheiros gerados no [arranque rápido][quickstart-cli].


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Posso modificar os ficheiros gerados pela Azure Dev Spaces?

Sim, pode modificar o ficheiro *azds.yaml,* Dockerfile e Helm [gráfico gerado pela Azure Dev Spaces ao preparar o seu projeto][dev-spaces-prep]. Modificar estes ficheiros altera a forma como o projeto é construído e executado.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Posso usar Azure Dev Spaces sem um endereço IP público?

Não, você não pode provisionar Azure Dev Spaces em um cluster AKS sem um IP público. Um IP público é [necessário pelo Azure dev Spaces para roteamento][dev-spaces-routing].

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Posso usar minha própria entrada com Azure Dev Spaces?

Sim, pode configurar a sua própria entrada juntamente com a entrada que o Azure Dev Spaces cria. Por exemplo, pode utilizar [traefik][ingress-traefik] ou [NGINX][ingress-nginx].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Posso usar HTTPS com Azure Dev Spaces?

Sim, pode configurar a sua própria entrada com HTTPS utilizando [traefik][ingress-https-traefik] ou [NGINX][ingress-https-nginx].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Posso usar Azure Dev Spaces em um cluster que usa CNI em vez de kubenet? 

Sim, você pode usar Azure Dev Spaces em um cluster AKS que usa o CNI para rede. Por exemplo, você pode usar Azure Dev Spaces em um cluster AKS com [contêineres do Windows existentes][windows-containers], que usa o CNI para rede. Mais informações sobre como usar o CNI para rede com o Azure Dev Spaces está disponível [aqui](configure-networking.md#using-azure-cni).

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Posso usar Azure Dev Spaces com contêineres do Windows?

Atualmente, o Azure Dev Spaces destina-se a ser executado somente em pods e nós do Linux, mas você pode executar Azure Dev Spaces em um cluster AKS com [contêineres do Windows existentes][windows-containers].

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Posso usar Azure Dev Spaces em clusters AKS com intervalos de endereços IP autorizados do servidor de API habilitado?

Sim, você pode usar Azure Dev Spaces em clusters AKS com [intervalos de endereços IP autorizados do servidor de API][aks-auth-range] habilitados. Mais informações sobre como usar clusters AKS com intervalos de endereços IP autorizados do servidor de API habilitados com o Azure Dev Spaces estão disponíveis [aqui](configure-networking.md#using-api-server-authorized-ip-ranges).

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Posso usar Azure Dev Spaces em clusters AKS com tráfego de saída restrito para nós de cluster?

Sim, você pode usar Azure Dev Spaces em clusters AKS com [tráfego de saída restrito para nós de cluster][aks-restrict-egress-traffic] habilitados assim que os FQDNs corretos tiverem sido permitidos. Mais informações sobre como usar clusters AKS com tráfego de saída restrito para nós de cluster habilitados com o Azure Dev Spaces estão disponíveis [aqui](configure-networking.md#ingress-and-egress-network-traffic-requirements).

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-prep]: how-dev-spaces-works.md#prepare-your-code
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[windows-containers]: how-to/run-dev-spaces-windows-containers.md