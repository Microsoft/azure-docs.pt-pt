---
title: Perguntas frequentes sobre Azure Dev Spaces
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Encontre respostas para algumas das perguntas comuns sobre Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s '
ms.openlocfilehash: 9870ba52424f6f59cc908e4d70bf1560f7d69165
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91970304"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Perguntas frequentes sobre Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Isto aborda perguntas frequentes sobre Azure Dev Spaces.

## <a name="what-versions-of-kubernetes-are-supported-for-azure-dev-spaces"></a>Que versões de Kubernetes são suportadas para Azure Dev Spaces?

A Azure Dev Spaces suporta todas as [versões de disponibilidade geral (GA) suportadas atualmente de Kubernetes em AKS][aks-supported-k8s].

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Quais regiões de Azure atualmente fornecem espaços Azure Dev?

Consulte [as regiões apoiadas][supported-regions] para uma lista completa das regiões disponíveis.

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>Posso migrar o meu cluster AKS com a Azure Dev Spaces para outra região?

Sim, se quiser mover o seu cluster AKS com a Azure Dev Spaces para outra [região apoiada,][supported-regions]recomendamos que crie um novo cluster na outra região, em seguida, instale e configure os Espaços Azure Dev e implemente os seus recursos e aplicações para o seu novo cluster. Para obter mais informações sobre a migração da AKS, consulte [o Serviço Migração para Azure Kubernetes (AKS)][aks-migration].

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>Posso usar espaços Azure Dev com gráficos de Docker ou Helm existentes?

Sim, se o seu projeto já tiver um Dockerfile ou um gráfico Helm, pode usar esses ficheiros com Azure Dev Spaces. Quando `azds prep` correr, utilize o `--chart` parâmetro e especifique a localização do gráfico. A Azure Dev Spaces continuará a gerar um ficheiro *azds.yaml* e *Dockerfile.develop,* mas não substituirá ou modificará um Dockerfile existente ou um gráfico Helm. Poderá ser necessário modificar os *ficheiros azds.yaml* e *Dockerfile.develop* para que tudo funcione corretamente com a sua aplicação existente durante a execução `azds up` .

Ao utilizar o seu próprio gráfico Dockerfile ou Helm, existem as seguintes limitações:
* Se utilizar apenas um Dockerfile, deve incluir tudo o que precisa para permitir cenários de desenvolvimento, como o SDK linguístico e não apenas o tempo de execução. Se utilizar um Dockerfile separado para espaços Azure Dev, como um Dockerfile.develop, tudo o que precisa para permitir cenários de desenvolvimento deve ser incluído nesse Dockerfile.
* O seu gráfico Helm deve suportar a passagem de parte ou de toda a etiqueta de imagem como um valor de *values.yaml*.
* Se estiver a modificar alguma coisa com entrada, também pode atualizar a sua tabela Helm para utilizar a solução de entrada fornecida pela Azure Dev Spaces.
* Se pretender utilizar as [capacidades de encaminhamento fornecidas pela Azure Dev Spaces,][dev-spaces-routing]todos os serviços para um projeto individual devem caber num único espaço de nomes Kubernetes e devem ser implantados com um simples nome, por exemplo, *serviço-a*. Nos gráficos padrão helm, esta atualização de nomeação pode ser feita especificando um valor para a propriedade *fullnameOverride.*

## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Posso modificar os ficheiros gerados pela Azure Dev Spaces?

Sim, pode modificar o ficheiro *azds.yaml,* Dockerfile e [Helm, gerado pela Azure Dev Spaces ao preparar o seu projeto.][dev-spaces-prep] Modificar estes ficheiros altera a forma como o projeto é construído e executado.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Posso usar espaços Azure Dev sem endereço IP público?

Não, não podes providenciar espaços Azure Dev num Cluster AKS sem um IP público. Um IP público é [necessário pela Azure Dev Spaces para o encaminhamento][dev-spaces-routing].

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Posso usar a minha própria entrada com a Azure Dev Spaces?

Sim, pode configurar a sua própria entrada juntamente com a entrada que a Azure Dev Spaces cria. Por exemplo, pode utilizar [traefik][ingress-traefik] ou [NGINX][ingress-nginx].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Posso utilizar HTTPS com espaços Azure Dev?

Sim, pode configurar a sua própria entrada com HTTPS usando [traefik][ingress-https-traefik] ou [NGINX][ingress-https-nginx].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Posso usar a Azure Dev Spaces num cluster que usa CNI em vez de kubenet? 

Sim, você pode usar Azure Dev Spaces em um cluster AKS que usa CNI para networking. Por exemplo, pode utilizar espaços Azure Dev num cluster AKS com [recipientes Windows existentes][windows-containers], que utiliza CNI para networking. Mais informações sobre a utilização do CNI para networking com a Azure Dev Spaces estão disponíveis [aqui.](configure-networking.md#using-azure-cni)

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Posso usar espaços Azure Dev com recipientes Windows?

Atualmente, a Azure Dev Spaces destina-se a funcionar apenas em cápsulas e nós Linux, mas você pode executar Azure Dev Spaces em um cluster AKS com [recipientes Windows existentes][windows-containers].

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Posso utilizar espaços Azure Dev em clusters AKS com gamas de endereços IP autorizados por servidor API ativadas?

Sim, pode utilizar espaços Azure Dev em clusters AKS com [intervalos de endereço IP autorizados por servidor API][aks-auth-range] ativados. Mais informações sobre a utilização de um cluster AKS com gamas de endereços IP autorizados pelo servidor API ativadas com espaços Azure Dev estão disponíveis [aqui.](configure-networking.md#using-api-server-authorized-ip-ranges)

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Posso usar espaços Azure Dev em aglomerados AKS com tráfego restrito de saídas para nós de cluster?

Sim, você pode usar Azure Dev Spaces em clusters AKS com [tráfego de saída restrito para nós de cluster][aks-restrict-egress-traffic] ativados uma vez que os FQDNs corretos foram permitidos. Mais informações sobre a utilização de um cluster AKS com tráfego de saída restrito para nós de cluster habilitados com Azure Dev Spaces estão disponíveis [aqui.](configure-networking.md#ingress-and-egress-network-traffic-requirements)

## <a name="can-i-use-azure-dev-spaces-on-rbac-enabled-aks-clusters"></a>Posso usar espaços Azure Dev em clusters AKS ativados pela RBAC?

Sim, você pode usar Azure Dev Spaces em clusters AKS com ou sem RBAC ativado.

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>O que acontece quando eu habilita a entrada para o projeto no Estúdio Visual?

Ao utilizar o Visual Studio para preparar o seu projeto, tem a opção de ativar a entrada para o seu serviço. Ativar a entrada cria um ponto final público para aceder ao seu serviço quando está a correr no seu cluster AKS, o que é opcional. Se não ativar a entrada, o seu serviço só está acessível dentro do seu cluster AKS.

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>Posso usar identidades geridas em pod com Azure Dev Spaces?

Sim, pode utilizar [identidades geridas][aks-pod-managed-id] em grupos AKS com espaços Azure Dev ativados, mas [existem passos de configuração adicionais][dev-spaces-pod-managed-id-steps] depois de ativar Azure Dev Spaces no seu cluster com identidades geridas por cápsulas. Se tiver identidades geridas por pod instaladas e quiser desinstalá-la, pode encontrar mais detalhes nas [notas de desinstalar][aks-pod-managed-id-uninstall].

## <a name="can-i-use-azure-dev-spaces-with-multiple-microservices-in-an-application"></a>Posso usar espaços Azure Dev com vários microserviços numa aplicação?

Sim, você pode usar Azure Dev Spaces numa aplicação com múltiplos microserviços, mas você deve preparar e executar os microserviços individuais na sua raiz. A extensão do Código Azure Dev Spaces CLI, Azure Dev Spaces VS Code e a carga de trabalho de Desenvolvimento visual Azure Development esperam que o ficheiro *azds.yaml* esteja na raiz do micro serviço para funcionar e depurar. Consulte a [aplicação][bike-sharing] da amostra Bike Sharing para um exemplo de múltiplos microserviços numa única aplicação.

No Código do Estúdio Visual, é possível [abrir projetos separados num único espaço][vs-code-multi-root-workspaces] de trabalho e desordá-los separadamente através do Azure Dev Spaces. Cada um dos projetos deve ser independente e preparado para a Azure Dev Spaces.

No Visual Studio, é possível configurar soluções .NET Core para depuração através do Azure Dev Spaces.

## <a name="can-i-use-azure-dev-spaces-with-a-service-mesh"></a>Posso usar espaços Azure Dev com uma malha de serviço?

Neste momento, não é possível utilizar espaços Azure Dev com malhas de serviço como [Istio][istio] ou [Linkerd.][linkerd] Você pode executar Azure Dev Spaces e uma malha de serviço no mesmo cluster AKS, mas você não pode ter tanto Azure Dev Spaces como uma malha de serviço ativada no mesmo espaço de nome.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[aks-supported-k8s]: ../aks/supported-kubernetes-versions.md#azure-portal-and-cli-versions
[bike-sharing]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[dev-spaces-pod-managed-id-steps]: troubleshooting.md#error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state
[dev-spaces-prep]: how-dev-spaces-works-prep.md
[dev-spaces-routing]: how-dev-spaces-works-routing.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[istio]: https://istio.io/
[linkerd]: https://linkerd.io/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[vs-code-multi-root-workspaces]: https://code.visualstudio.com/docs/editor/multi-root-workspaces
[windows-containers]: how-to/run-dev-spaces-windows-containers.md