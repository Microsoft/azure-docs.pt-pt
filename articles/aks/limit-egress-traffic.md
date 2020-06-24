---
title: Restringir o tráfego de saídas no Serviço Azure Kubernetes (AKS)
description: Saiba quais portas e endereços são necessários para controlar o tráfego de saídas no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 88be26bb22cf539309ce67716101d7386d9d0513
ms.sourcegitcommit: 51977b63624dfd3b4f22fb9fe68761d26eed6824
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84945567"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Controlo tráfego de saídas para nós de cluster no Serviço Azure Kubernetes (AKS)

Por padrão, os clusters AKS têm acesso ilimitado à internet de saída (saída). Este nível de acesso à rede permite que nós e serviços que você execute para aceder a recursos externos conforme necessário. Se desejar restringir o tráfego de saídas, um número limitado de portos e endereços devem estar acessíveis para manter tarefas saudáveis de manutenção do cluster. O seu cluster é configurado por padrão para utilizar apenas imagens de contentores do sistema base do Microsoft Container Registry (MCR) ou do Registo de Contentores Azure (ACR). Configure as suas regras de firewall e segurança preferidas para permitir as portas e endereços necessários.

Este artigo detalha quais as portas de rede e os nomes de domínio totalmente qualificados (FQDNs) são necessários e opcionais se restringir o tráfego de saídas num cluster AKS.

> [!IMPORTANT]
> Este documento cobre apenas como bloquear o tráfego que sai da sub-rede AKS. AKS não tem requisitos de entrada.  O bloqueio do tráfego interno da sub-rede utilizando grupos de segurança de rede (NSGs) e firewalls não é suportado. Para controlar e bloquear o tráfego dentro do cluster, utilize [políticas de rede][network-policy].

## <a name="before-you-begin"></a>Antes de começar

Precisa da versão 2.0.66 do Azure CLI ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

## <a name="egress-traffic-overview"></a>Visão geral do tráfego de Egress

Para fins de gestão e operacional, os nódes num cluster AKS precisam de aceder a determinadas portas e nomes de domínio totalmente qualificados (FQDNs). Estas ações podem ser para comunicar com o servidor API, ou para descarregar e, em seguida, instalar componentes de cluster de kubernetes e atualizações de segurança de nó. Por padrão, o tráfego de internet de saída (saída) não é restrito para nós num cluster AKS. O cluster pode retirar imagens de contentores do sistema de base de repositórios externos.

Para aumentar a segurança do seu cluster AKS, pode desejar restringir o tráfego de saídas. O cluster está configurado para retirar imagens de contentores do sistema base de MCR ou ACR. Se bloquear o tráfego de saída desta forma, defina portas específicas e FQDNs para permitir que os nós AKS comuniquem corretamente com os serviços externos necessários. Sem estas portas autorizadas e FQDNs, os seus nós AKS não podem comunicar com o servidor API ou instalar componentes centrais.

Pode utilizar [o Azure Firewall][azure-firewall] ou um aparelho de firewall de terceiros para proteger o tráfego da sua saída e definir as portas e endereços necessários. A AKS não cria automaticamente estas regras para si. As seguintes portas e endereços são para referência à medida que cria as regras apropriadas na sua firewall de rede.

> [!IMPORTANT]
> Quando utilizar o Azure Firewall para restringir o tráfego de saídas e criar uma rota definida pelo utilizador (UDR) para forçar todo o tráfego de saídas, certifique-se de que cria uma regra de ADN apropriada na Firewall para permitir corretamente o tráfego de entrada. A utilização do Azure Firewall com um UDR quebra a configuração de entrada devido ao encaminhamento assimétrico. (O problema ocorre se a sub-rede AKS tiver uma rota padrão que vai para o endereço IP privado da firewall, mas está a utilizar um equilibrador de carga público - ingress ou serviço kubernetes do tipo: LoadBalancer). Neste caso, o tráfego do balançador de carga de entrada é recebido através do seu endereço IP público, mas a trajetória de retorno passa pelo endereço IP privado da firewall. Como a firewall é imponente, deixa cair o pacote de retorno porque a firewall não está ciente de uma sessão estabelecida. Para aprender a integrar o Azure Firewall com o seu equilibrador de carga de entrada ou serviço, consulte [Integrate Azure Firewall com o Azure Standard Load Balancer](https://docs.microsoft.com/azure/firewall/integrate-lb).
> Pode bloquear o tráfego da porta TCP 9000, da porta TCP 22 e da porta UDP 1194 utilizando uma regra de rede entre o nó de trabalho de saída IP(s) e o IP para o servidor API.

Em AKS, existem dois conjuntos de portas e endereços:

* As [portas e endereços necessários para os clusters AKS](#required-ports-and-addresses-for-aks-clusters) detalham os requisitos mínimos para o tráfego autorizado de saídas.
* Os [endereços e portas recomendados opcionalmente para clusters AKS](#optional-recommended-addresses-and-ports-for-aks-clusters) não são necessários para todos os cenários, mas a integração com outros serviços como o Azure Monitor não funcionará corretamente. Reveja esta lista de portas e FQDNs opcionais e autorize qualquer um dos serviços e componentes utilizados no seu cluster AKS.

> [!NOTE]
> Limitar o tráfego de saídas só funciona em novos clusters AKS. Para os aglomerados existentes, [efetue uma operação de atualização][aks-upgrade] do cluster utilizando o `az aks upgrade` comando antes de limitar o tráfego de saída.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Portas e endereços necessários para clusters do AKS

São necessárias as seguintes portas de saída/regras de rede para um cluster AKS:

* Porta TCP *443*
* TCP [IPAddrOfYourAPIServer]:443 é necessário se tiver uma aplicação que precisa de falar com o servidor API.  Esta alteração pode ser definida após a criação do cluster.
* Porta *TCP 9000*, porta TCP *22* e porta UDP *1194* para a cápsula frontal do túnel comunicar com a extremidade do túnel no servidor API.
    * Para obter mais específico, consulte o ** \<location\> .hcp. azmk8s.io* e .*. \<location\> azmk8s.io* endereços na mesa seguinte.
* Porta UDP *123* para sincronização de tempo do Protocolo de Tempo de Rede (NTP) (nóles Linux).
* A porta *UDP 53* para DNS também é necessária se tiver cápsulas de acesso direto ao servidor API.

São necessárias as seguintes regras de aplicação fQDN/ aplicação:

> [!IMPORTANT]
> ***.blob.core.windows.net e aksrepos.azurecr.io** já não são necessárias regras FQDN para o bloqueio de saídas.  Para os clusters existentes, [efetue uma operação de atualização][aks-upgrade] do cluster utilizando o `az aks upgrade` comando para remover estas regras.

> [!IMPORTANT]
> *.cdn.mscr.io foi substituído por *.data.mcr.microsoft.com para as regiões de nuvens públicas de Azure. Por favor, atualize as suas regras de firewall existentes para que as alterações entrem em vigor.

- Azure Global

| FQDN                       | Porta      | Utilizar      |
|----------------------------|-----------|----------|
| *.hcp. \<location\> azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Este endereço é necessário para a comunicação do servidor API <->. *\<location\>* Substitua-a pela região onde o seu cluster AKS está implantado. |
| *.tun. \<location\> . azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Este endereço é necessário para a comunicação do servidor API <->. *\<location\>* Substitua-a pela região onde o seu cluster AKS está implantado. |
| *.cdn.mscr.io       | HTTPS:443 | Este endereço é necessário para o armazenamento mcR apoiado pela Rede de Entrega de Conteúdos Azure (CDN). |
| mcr.microsoft.com          | HTTPS:443 | Este endereço é necessário para aceder a imagens no Microsoft Container Registry (MCR). Este registo contém imagens/gráficos de primeira parte (por exemplo, moby, etc.) necessários para o funcionamento do cluster durante a atualização e escala do cluster |
| *.data.mcr.microsoft.com             | HTTPS:443 | Este endereço é necessário para o armazenamento mcR apoiado pela rede de entrega de conteúdos Azure (CDN). |
| management.azure.com       | HTTPS:443 | Este endereço é necessário para operações Kubernetes GET/PUT. |
| login.microsoftonline.com  | HTTPS:443 | Este endereço é necessário para a autenticação do Azure Ative Directory. |
| ntp.ubuntu.com             | UDP:123   | Este endereço é necessário para sincronização de tempo NTP nos nós Linux. |
| packages.microsoft.com     | HTTPS:443 | Este endereço é o repositório de pacotes da Microsoft utilizado para operações em cache *apt-get.*  Os pacotes de exemplo incluem Moby, PowerShell e Azure CLI. |
| acs-mirror.azureedge.net      | HTTPS:443 | Este endereço destina-se ao repositório necessário para instalar binários necessários, como kubenet e Azure CNI. |

- Azure China 21Vianet

| FQDN                       | Porta      | Utilizar      |
|----------------------------|-----------|----------|
| *.hcp. \<location\> cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Este endereço é necessário para a comunicação do servidor API <->. *\<location\>* Substitua-a pela região onde o seu cluster AKS está implantado. |
| *.tun. \<location\> . cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Este endereço é necessário para a comunicação do servidor API <->. *\<location\>* Substitua-a pela região onde o seu cluster AKS está implantado. |
| *.azk8s.cn        | HTTPS:443 | Este endereço é necessário para descarregar binários e imagens necessários|
| mcr.microsoft.com          | HTTPS:443 | Este endereço é necessário para aceder a imagens no Microsoft Container Registry (MCR). Este registo contém imagens/gráficos de primeira parte (por exemplo, moby, etc.) necessários para o funcionamento do cluster durante a atualização e escala do cluster |
| *.cdn.mscr.io       | HTTPS:443 | Este endereço é necessário para o armazenamento mcR apoiado pela Rede de Entrega de Conteúdos Azure (CDN). |
| *.data.mcr.microsoft.com             | HTTPS:443 | Este endereço é necessário para o armazenamento mcR apoiado pela rede de entrega de conteúdos Azure (CDN). |
| management.chinacloudapi.cn       | HTTPS:443 | Este endereço é necessário para operações Kubernetes GET/PUT. |
| login.chinacloudapi.cn  | HTTPS:443 | Este endereço é necessário para a autenticação do Azure Ative Directory. |
| ntp.ubuntu.com             | UDP:123   | Este endereço é necessário para sincronização de tempo NTP nos nós Linux. |
| packages.microsoft.com     | HTTPS:443 | Este endereço é o repositório de pacotes da Microsoft utilizado para operações em cache *apt-get.*  Os pacotes de exemplo incluem Moby, PowerShell e Azure CLI. |

- Azure Government

| FQDN                       | Porta      | Utilizar      |
|----------------------------|-----------|----------|
| *.hcp. \<location\> cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Este endereço é necessário para a comunicação do servidor API <->. *\<location\>* Substitua-a pela região onde o seu cluster AKS está implantado. |
| *.tun. \<location\> . cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Este endereço é necessário para a comunicação do servidor API <->. *\<location\>* Substitua-a pela região onde o seu cluster AKS está implantado. |
| mcr.microsoft.com          | HTTPS:443 | Este endereço é necessário para aceder a imagens no Microsoft Container Registry (MCR). Este registo contém imagens/gráficos de primeira parte (por exemplo, moby, etc.) necessários para o funcionamento do cluster durante a atualização e escala do cluster |
|*.cdn.mscr.io              | HTTPS:443 | Este endereço é necessário para o armazenamento mcR apoiado pela Rede de Entrega de Conteúdos Azure (CDN). |
| *.data.mcr.microsoft.com             | HTTPS:443 | Este endereço é necessário para o armazenamento mcR apoiado pela rede de entrega de conteúdos Azure (CDN). |
| management.usgovcloudapi.net       | HTTPS:443 | Este endereço é necessário para operações Kubernetes GET/PUT. |
| login.microsoftonline.us  | HTTPS:443 | Este endereço é necessário para a autenticação do Azure Ative Directory. |
| ntp.ubuntu.com             | UDP:123   | Este endereço é necessário para sincronização de tempo NTP nos nós Linux. |
| packages.microsoft.com     | HTTPS:443 | Este endereço é o repositório de pacotes da Microsoft utilizado para operações em cache *apt-get.*  Os pacotes de exemplo incluem Moby, PowerShell e Azure CLI. |
| acs-mirror.azureedge.net      | HTTPS:443 | Este endereço destina-se ao repositório necessário para instalar binários necessários, como kubenet e Azure CNI. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Endereços e portas recomendados opcionais para clusters AKS

As seguintes portas de saída/regras de rede são opcionais para um cluster AKS:

Recomenda-se que os seguintes regulamentos FQDN/ aplicação funcionem corretamente:

| FQDN                                    | Porta      | Utilizar      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com | HTTP:80   | Este endereço permite que os nós do cluster Linux descarreguem os patches e atualizações de segurança necessários. |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>Endereços e portas necessários para clusters AKS habilitados para GPU

São necessárias as seguintes regras de FQDN/aplicação para clusters AKS que tenham GPU habilitado:

| FQDN                                    | Porta      | Utilizar      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS:443 | Este endereço é utilizado para a correta instalação e operação do condutor nos nós baseados em GPU. |
| us.download.nvidia.com | HTTPS:443 | Este endereço é utilizado para a correta instalação e operação do condutor nos nós baseados em GPU. |
| apt.dockerproject.org | HTTPS:443 | Este endereço é utilizado para a correta instalação e operação do condutor nos nós baseados em GPU. |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>Endereços e portas necessários com monitor Azure para contentores ativados

São necessárias as seguintes regras de FQDN/aplicação para os clusters AKS que tenham o Monitor Azure para contentores habilitados:

| FQDN                                    | Porta      | Utilizar      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS:443    | Isto é para métricas corretas e telemetria de monitorização utilizando o Monitor Azure. |
| *.ods.opinsights.azure.com    | HTTPS:443    | Isto é usado pelo Azure Monitor para ingerir dados de análise de registo. |
| *.oms.opinsights.azure.com | HTTPS:443 | Este endereço é utilizado pelo omsagent, que é utilizado para autenticar o serviço de análise de registos. |
|*.microsoftonline.com | HTTPS:443 | Isto é utilizado para autenticar e enviar métricas para o Azure Monitor. |
|*.monitoring.azure.com | HTTPS:443 | Isto é usado para enviar dados de métricas para O Azure Monitor. |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>Endereços e portas necessários com espaços Azure Dev habilitados

São necessárias as seguintes regras de FQDN/aplicação para clusters AKS que tenham os Espaços Azure Dev habilitados:

| FQDN                                    | Porta      | Utilizar      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Este endereço é usado para puxar o linux alpino e outras imagens do Azure Dev Spaces |
| gcr.io | HTTPS:443 | Este endereço é usado para puxar imagens de leme/leme |
| storage.googleapis.com | HTTPS:443 | Este endereço é usado para puxar imagens de leme/leme |

Atualize a sua configuração de firewall ou segurança para permitir o tráfego de rede de e para todos os serviços de infraestrutura de FQDNs e [Azure Dev Spaces][dev-spaces-service-tags]acima referidos .

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Endereços e portas necessários para clusters AKS com Política Azure (em pré-visualização pública) habilitados

> [!CAUTION]
> Algumas das funcionalidades abaixo estão em pré-visualização.  As sugestões deste artigo estão sujeitas a alterações à medida que a funcionalidade passa para as fases de pré-visualização e lançamento futuras.

São necessárias as seguintes regras de FQDN/aplicação para clusters AKS que tenham a Política Azure ativada.

| FQDN                                    | Porta      | Utilizar      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Este endereço é utilizado para o correto funcionamento da Política Azure. (atualmente em pré-visualização em AKS) |
| raw.githubusercontent.com | HTTPS:443 | Este endereço é utilizado para retirar as políticas incorporadas do GitHub para garantir o correto funcionamento da Política Azure. (atualmente em pré-visualização em AKS) |
| *.gk. \<location\> azmk8s.io | HTTPS:443    | Azure Policy add-on que fala com gatekeeper audit ponto final em execução no servidor principal para obter os resultados da auditoria. |
| dc.services.visualstudio.com | HTTPS:443 | Addon Azure Policy que envia dados de telemetria para aplicações insights ponto final. |

## <a name="required-by-windows-server-based-nodes-enabled"></a>Exigido pelos nosdes baseados no Windows Server

São necessárias as seguintes regras de FQDN / aplicação para a utilização de conjuntos de nó baseados no Windows Server:

| FQDN                                    | Porta      | Utilizar      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net, winlayers.blob.core.windows.net, winlayers.cdn.mscr.io go.microsoft.com | HTTPS:443 | Para instalar binários relacionados com janelas |
| *.mp.microsoft.com, <span></span> www.msftconnecttest.com, ctldl.windowsupdate.com | HTTP:80 | Para instalar binários relacionados com janelas |
| kms.core.windows.net | TCP:1688 | Para instalar binários relacionados com janelas |

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu que portas e endereços permitir se você restringir o tráfego de saída para o cluster. Também pode definir como as próprias cápsulas podem comunicar e quais as restrições que têm dentro do cluster. Para obter mais informações, consulte [o tráfego seguro entre as cápsulas utilizando as políticas de rede em AKS][network-policy].

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
[dev-spaces-service-tags]: ../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations
