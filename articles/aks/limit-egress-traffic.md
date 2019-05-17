---
title: Restringir o tráfego de saída no Azure Kubernetes Service (AKS)
description: Saiba quais portas e endereços são necessários para controlar o tráfego de saída no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: de0ba13a527569e446a44c275b7323d4487f53b6
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780303"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>Pré-visualização - o tráfego de saída de limite de nós de cluster e controlar o acesso a portas necessárias e serviços no Azure Kubernetes Service (AKS)

Por predefinição, os clusters do AKS têm irrestrito acesso à internet de saída (saída). Este nível de acesso à rede permite que nós e os serviços que executar para aceder a recursos externos, conforme necessário. Se pretender restringir o tráfego de saída, um número limitado de portas e endereços tem de ser acessível para manter as tarefas de manutenção de bom estado de funcionamento do cluster. O cluster, em seguida, está configurado para utilizar apenas imagens de contentor do sistema de base do registo de contentor da Microsoft (MCR) ou Azure Container Registry (ACR), não externos repositórios públicos.

Este artigo detalha quais portas de rede e os nomes de domínio completamente qualificado (FQDN) são obrigatórios e opcionais se restringir o tráfego de saída num cluster do AKS.  Esta funcionalidade encontra-se em pré-visualização.

> [!IMPORTANT]
> Funcionalidades de pré-visualização do AKS são self-service e participar. Pré-visualizações são fornecidas para recolher comentários e bugs de nossa Comunidade. No entanto, não são suportados pelo suporte técnico do Azure. Se cria um cluster ou adicionar esses recursos em clusters existentes, esse cluster não é suportado até que a funcionalidade não se encontra em pré-visualização e é formado para disponibilidade geral (GA).
>
> Se tiver problemas com funcionalidades de pré-visualização [abra um problema no repositório GitHub do AKS] [ aks-github] com o nome da funcionalidade de pré-visualização no título do bug.

## <a name="before-you-begin"></a>Antes de começar

Precisa da versão 2.0.61 da CLI do Azure ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

Para criar um cluster do AKS que pode limitar o tráfego de saída, ative primeiro um sinalizador de funcionalidade na sua subscrição. Esse registro de recurso configura quaisquer clusters do AKS que criar para utilizar imagens de contentor do sistema de base de MCR ou ACR. Para registar o *AKSLockingDownEgressPreview* sinalizador de funcionalidade, utilize o [Registre-se de funcionalidade de az] [ az-feature-register] comando conforme mostrado no exemplo a seguir:

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

Demora alguns minutos para que o estado a mostrar *registado*. Pode verificar o estado de registo utilizando o [lista de funcionalidades de az] [ az-feature-list] comando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registo do *containerservice* fornecedor de recursos, utilizando o [Registre-se fornecedor de az] [ az-provider-register] comando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>Descrição geral do tráfego de saída

Para fins operacionais e de gestão, nós num cluster do AKS têm de aceder a determinadas portas e os nomes de domínio completamente qualificado (FQDN). Essas ações poderiam ser para comunicar com o servidor de API, ou transferir e, em seguida, instalar componentes principais do cluster de Kubernetes e atualizações de segurança de nó. Por predefinição, o tráfego de internet de saída (saída) não é restrito para nós num cluster do AKS. O cluster pode extrair as sistema base imagens de contentor de repositórios externos.

Para aumentar a segurança do cluster do AKS, pode pretender restringir o tráfego de saída. O cluster está configurado para solicitar imagens de contentor do MCR ou ACR de base do sistema. Se bloquear o tráfego de saída dessa maneira, deve definir portas específicas e FQDNs para permitir que os nós do AKS comunicar corretamente com serviços externos necessários. Sem essas portas autorizadas e FQDNs, os nós do AKS não é possível comunicar com o servidor de API ou instalar os componentes principais.

Pode usar [Firewall do Azure] [ azure-firewall] ou uma aplicação de firewall de terceiros 3rd para proteger o tráfego de saída e defini-los necessárias portas e endereços.

No AKS, existem dois conjuntos de endereços e portas:

* O [portas e endereços de necessários para os clusters do AKS](#required-ports-and-addresses-for-aks-clusters) fornece detalhes sobre os requisitos mínimos para o tráfego de saída autorizados.
* O [recomendado opcional endereços e portas para os clusters do AKS](#optional-recommended-addresses-and-ports-for-aks-clusters) não são necessários para todos os cenários, mas a integração com outros serviços, como o Azure Monitor não funcionará corretamente. Reveja esta lista de portas opcionais e FQDNs e autorizar qualquer um dos serviços e componentes utilizados no seu cluster do AKS.

> [!NOTE]
> Limitar o tráfego de saída só funciona em clusters do AKS nova criados depois de ativar o registo do sinalizador de funcionalidade. Para clusters existentes, [efetuar uma operação de atualização do cluster] [ aks-upgrade] usando o `az aks upgrade` antes de limitar o tráfego de saída de comando.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>As portas necessárias e os endereços para os clusters do AKS

As seguintes portas de saída / regras de rede são necessárias para um cluster do AKS:

* A porta TCP *443*
* A porta TCP *9000*

O seguinte FQDN / regras de aplicações são necessárias:

| FQDN                      | Port      | Utilização      |
|---------------------------|-----------|----------|
| *.azmk8s.io               | HTTPS:443 | Este endereço é o ponto de final do servidor de API. |
| aksrepos.azurecr.io       | HTTPS:443 | Este endereço é necessário para imagens de acesso no Azure Container Registry (ACR). |
| *.blob.core.windows.net   | HTTPS:443 | Este endereço é o armazenamento de back-end para imagens armazenadas no ACR. |
| mcr.microsoft.com         | HTTPS:443 | Este endereço é necessário para acesso de imagens no registo de contentor da Microsoft (MCR). |
| management.azure.com      | HTTPS:443 | Este endereço é necessário para operações do Kubernetes GET/PUT. |
| login.microsoftonline.com | HTTPS:443 | Este endereço é necessário para a autenticação do Azure Active Directory. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Opcional recomendado endereços e portas para os clusters do AKS

As seguintes portas de saída / regras de rede não são necessárias para clusters do AKS funcionar corretamente, mas são recomendadas:

* A porta UDP *123* para sincronização de hora NTP
* A porta UDP *53* para DNS

O seguinte FQDN / regras de aplicações são recomendadas para clusters do AKS funcionar corretamente:

| FQDN                                    | Port      | Utilização      |
|-----------------------------------------|-----------|----------|
| *.ubuntu.com                            | HTTP:80   | Este endereço permite que os nós de cluster do Linux transferir as atualizações e patches de segurança necessário. |
| packages.microsoft.com                  | HTTPS:443 | Este endereço é o repositório de pacotes do Microsoft utilizado para armazenados em cache *apt-get* operações. |
| dc.services.visualstudio.com            | HTTPS:443 | Recomendado para métricas corretas e monitorização com o Azure Monitor. |
| *.opinsights.azure.com                  | HTTPS:443 | Recomendado para métricas corretas e monitorização com o Azure Monitor. |
| *.monitoring.azure.com                  | HTTPS:443 | Recomendado para métricas corretas e monitorização com o Azure Monitor. |
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Este endereço é utilizado para o correto funcionamento de política do Azure (atualmente em pré-visualização no AKS). |
| apt.dockerproject.org                   | HTTPS:443 | Este endereço é utilizado para a instalação de driver correto e de operação em nós baseada em GPU. |
| nvidia.github.io                        | HTTPS:443 | Este endereço é utilizado para a instalação de driver correto e de operação em nós baseada em GPU. |

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu quais portas e endereços para permitir que se restringir o tráfego de saída para o cluster. Também pode definir como os pods próprios podem comunicar e que restrições eles têm dentro do cluster. Para obter mais informações, consulte [proteger o tráfego entre pods através de políticas de rede no AKS][network-policy].

<!-- LINKS - external -->
[aks-github]: https://github.com/azure/aks/issues]

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
