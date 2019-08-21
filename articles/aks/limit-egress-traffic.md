---
title: Restringir o tráfego de saída no serviço kubernetes do Azure (AKS)
description: Saiba quais portas e endereços são necessários para controlar o tráfego de saída no serviço de kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/06/2019
ms.author: mlearned
ms.openlocfilehash: 369729f10de4a55cd14bb866795ea1aa15b3d9da
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639790"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>Visualização-limite o tráfego de saída para nós de cluster e controle o acesso a portas e serviços necessários no AKS (serviço kubernetes do Azure)

Por padrão, os clusters AKS têm acesso irrestrito à Internet (saída). Esse nível de acesso à rede permite que os nós e serviços que você executa acessem recursos externos, conforme necessário. Se você quiser restringir o tráfego de saída, um número limitado de portas e endereços deve ser acessível para manter tarefas de manutenção de cluster íntegras. Em seguida, o cluster é configurado para usar apenas as imagens de contêiner do sistema base do MCR (registro de contêiner da Microsoft) ou do ACR (registro de contêiner do Azure), não de repositórios públicos externos. Você deve configurar o firewall e as regras de segurança preferenciais para permitir essas portas e endereços necessários.

Este artigo detalha quais portas de rede e FQDNs (nomes de domínio totalmente qualificados) são necessários e opcionais se você restringir o tráfego de saída em um cluster AKS.  Esta funcionalidade encontra-se em pré-visualização.

> [!IMPORTANT]
> Os recursos de visualização do AKS são consentimento de autoatendimento. As visualizações são fornecidas "no estado em que se encontram" e "como disponíveis" e são excluídas dos contratos de nível de serviço e da garantia limitada. As visualizações do AKS são parcialmente cobertas pelo suporte ao cliente com base no melhor esforço. Dessa forma, esses recursos não são destinados ao uso em produção. Para obter outras incompatibilidades, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS][aks-support-policies]
> * [Perguntas frequentes do suporte do Azure][aks-faq]

## <a name="before-you-begin"></a>Antes de começar

Você precisa do CLI do Azure versão 2.0.66 ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][install-azure-cli].

Para criar um cluster AKS que possa limitar o tráfego de saída, primeiro habilite um sinalizador de recurso em sua assinatura. Esse registro de recurso configura todos os clusters AKS que você criar para usar as imagens de contêiner do sistema base de MCR ou ACR. Para registrar o sinalizador de recurso *AKSLockingDownEgressPreview* , use o comando [AZ Feature Register][az-feature-register] , conforme mostrado no exemplo a seguir:

> [!CAUTION]
> Quando você registra um recurso em uma assinatura, não é possível cancelar o registro desse recurso no momento. Depois de habilitar alguns recursos de visualização, os padrões podem ser usados para todos os clusters AKS, em seguida, criados na assinatura. Não habilite os recursos de visualização em assinaturas de produção. Use uma assinatura separada para testar recursos de visualização e coletar comentários.

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

Leva alguns minutos para que o status seja mostrado *registrado*. Você pode verificar o status do registro usando o comando [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro do provedor de recursos *Microsoft. ContainerService* usando o comando [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>Visão geral do tráfego de saída

Para fins operacionais e de gerenciamento, os nós em um cluster AKS precisam acessar determinadas portas e FQDNs (nomes de domínio totalmente qualificados). Essas ações podem ser comunicadas com o servidor de API ou para baixar e instalar os componentes principais do cluster kubernetes e as atualizações de segurança do nó. Por padrão, o tráfego de saída da Internet (saída) não é restrito para nós em um cluster AKS. O cluster pode extrair imagens de contêiner do sistema base de repositórios externos.

Para aumentar a segurança do cluster AKS, talvez você queira restringir o tráfego de saída. O cluster está configurado para extrair imagens de contêiner do sistema base de MCR ou ACR. Se você bloquear o tráfego de saída dessa maneira, deverá definir portas e FQDNs específicos para permitir que os nós AKS se comuniquem corretamente com os serviços externos necessários. Sem essas portas autorizadas e FQDNs, seus nós AKS não podem se comunicar com o servidor de API nem instalar componentes principais.

Você pode usar o [Firewall do Azure][azure-firewall] ou um dispositivo de firewall de terceiros para proteger o tráfego de saída e definir as portas e os endereços necessários. O AKS não cria essas regras automaticamente para você. As portas e os endereços a seguir são para referência à medida que você cria as regras apropriadas no firewall de rede.

> [!IMPORTANT]
> Quando você usa o Firewall do Azure para restringir o tráfego de saída e criar uma UDR (rota definida pelo usuário) para forçar todo o tráfego de saída, certifique-se de criar uma regra DNAT apropriada no firewall para permitir corretamente o tráfego de entrada. Usar o Firewall do Azure com um UDR interrompe a configuração de entrada devido ao roteamento assimétrico. (O problema ocorre porque a sub-rede AKS tem uma rota padrão que vai para o endereço IP privado do firewall, mas você está usando um serviço de entrada de balanceador de carga público ou kubernetes do tipo: Balanceador de carga). Nesse caso, o tráfego do balanceador de carga de entrada é recebido por meio de seu endereço IP público, mas o caminho de retorno passa pelo endereço IP privado do firewall. Como o firewall tem monitoração de estado, ele descarta o pacote de retorno porque o firewall não está ciente de uma sessão estabelecida. Para saber como integrar o Firewall do Azure com o balanceador de carga de entrada ou serviço, consulte [integrar o Firewall do Azure com o Azure Standard Load Balancer](https://docs.microsoft.com/en-us/azure/firewall/integrate-lb).
>

No AKS, há dois conjuntos de portas e endereços:

* As [portas e o endereço necessários para clusters AKs](#required-ports-and-addresses-for-aks-clusters) detalham os requisitos mínimos para o tráfego de saída autorizado.
* Os [endereços e portas recomendados opcionais para clusters AKs](#optional-recommended-addresses-and-ports-for-aks-clusters) não são necessários para todos os cenários, mas a integração com outros serviços, como o Azure monitor, não funcionará corretamente. Examine esta lista de portas opcionais e FQDNs e autorize qualquer um dos serviços e componentes usados em seu cluster AKS.

> [!NOTE]
> Limitar o tráfego de saída só funciona em novos clusters AKS criados depois que você habilita o registro do sinalizador de recurso. Para clusters existentes, [Execute uma operação de atualização][aks-upgrade] de cluster `az aks upgrade` usando o comando antes de limitar o tráfego de saída.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Portas e endereços necessários para clusters AKS

As seguintes regras de rede/portas de saída são necessárias para um cluster AKS:

* Porta TCP *443*
* A porta TCP *9000* e a porta TCP *22* para o Pod frontal do túnel se comunicam com a extremidade do túnel no servidor de API.
    * Para obter mais especificações, consulte o * *. HCP.\< Location\>. azmk8s.Io* e * *. Execute.\< endereços\>Location. azmk8s.Io* na tabela a seguir.

As seguintes regras de FQDN/aplicativo são necessárias:

| FQDN                       | Port      | Utilizar      |
|----------------------------|-----------|----------|
| *. HCP. \<local\>. azmk8s.Io | HTTPS:443, TCP:22, TCP:9000 | Esse endereço é o ponto de extremidade do servidor de API. *Substitua\<olocal\>* pela região onde o cluster AKs está implantado. |
| *. Execute. \<local\>. azmk8s.Io | HTTPS:443, TCP:22, TCP:9000 | Esse endereço é o ponto de extremidade do servidor de API. *Substitua\<olocal\>* pela região onde o cluster AKs está implantado. |
| aksrepos.azurecr.io        | HTTPS:443 | Esse endereço é necessário para acessar imagens no ACR (registro de contêiner do Azure). |
| *.blob.core.windows.net    | HTTPS:443 | Esse endereço é o armazenamento de back-end para imagens armazenadas no ACR. |
| mcr.microsoft.com          | HTTPS:443 | Esse endereço é necessário para acessar imagens no registro de contêiner da Microsoft (MCR). |
| *.cdn.mscr.io              | HTTPS:443 | Esse endereço é necessário para o armazenamento MCR apoiado pela CDN (rede de distribuição de conteúdo) do Azure. |
| management.azure.com       | HTTPS:443 | Esse endereço é necessário para operações GET/PUT do kubernetes. |
| login.microsoftonline.com  | HTTPS:443 | Esse endereço é necessário para Azure Active Directory autenticação. |
| api.snapcraft.io           | HTTPS:443, HTTP:80 | Esse endereço é necessário para instalar pacotes snap em nós do Linux. |
| ntp.ubuntu.com             | UDP:123   | Esse endereço é necessário para a sincronização de horário do NTP em nós do Linux. |
| *. docker.io                | HTTPS:443 | Esse endereço é necessário para efetuar pull das imagens de contêiner necessárias para a frente do túnel. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Portas e endereços recomendados opcionais para clusters AKS

* Porta UDP *53* para DNS

As seguintes regras de FQDN/aplicativo são recomendadas para que os clusters AKS funcionem corretamente:

| FQDN                                    | Port      | Utilizar      |
|-----------------------------------------|-----------|----------|
| *.ubuntu.com                            | HTTP:80   | Esse endereço permite que os nós de cluster do Linux baixem as atualizações e os patches de segurança necessários. |
| packages.microsoft.com                  | HTTPS:443 | Esse endereço é o repositório de pacotes da Microsoft usado para operações *apt-get* em cache. |
| dc.services.visualstudio.com            | HTTPS:443 | Recomendado para métricas e monitoramentos corretos usando Azure Monitor. |
| *.opinsights.azure.com                  | HTTPS:443 | Recomendado para métricas e monitoramentos corretos usando Azure Monitor. |
| *.monitoring.azure.com                  | HTTPS:443 | Recomendado para métricas e monitoramentos corretos usando Azure Monitor. |
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Esse endereço é usado para a operação correta do Azure Policy (atualmente em visualização no AKS). |
| apt.dockerproject.org                   | HTTPS:443 | Esse endereço é usado para a instalação e operação corretas do driver em nós baseados em GPU. |
| nvidia.github.io                        | HTTPS:443 | Esse endereço é usado para a instalação e operação corretas do driver em nós baseados em GPU. |

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu quais portas e endereços permitir se você restringir o tráfego de saída para o cluster. Você também pode definir como o próprio pods pode se comunicar e quais restrições eles têm dentro do cluster. Para obter mais informações, consulte [proteger o tráfego entre pods usando as políticas de rede no AKs][network-policy].

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
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
