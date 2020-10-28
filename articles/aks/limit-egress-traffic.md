---
title: Restringir o tráfego de saídas no Serviço Azure Kubernetes (AKS)
description: Saiba quais portas e endereços são necessários para controlar o tráfego de saídas no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.author: jpalma
ms.date: 06/29/2020
ms.custom: fasttrack-edit, devx-track-azurecli
author: palma21
ms.openlocfilehash: fe6907ac659b94494472a327ff0b47e630ed89a0
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735569"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Controlo tráfego de saídas para nós de cluster no Serviço Azure Kubernetes (AKS)

Este artigo fornece os detalhes necessários que lhe permitem proteger o tráfego de saída do seu Serviço Azure Kubernetes (AKS). Contém os requisitos de cluster para uma implantação de AKS base, e requisitos adicionais para addons e funcionalidades opcionais. [No final, será dado um exemplo sobre como configurar estes requisitos com o Azure Firewall](#restrict-egress-traffic-using-azure-firewall). No entanto, pode aplicar estas informações a qualquer método ou aparelho de restrição de saída.

## <a name="background"></a>Fundo

Os clusters AKS são implantados numa rede virtual. Esta rede pode ser gerida (criada por AKS) ou personalizada (pré-configurada previamente pelo utilizador). Em qualquer dos casos, o cluster tem dependências **de saída** de serviços fora dessa rede virtual (o serviço não tem dependências de entrada).

Para fins de gestão e operacional, os nódes num cluster AKS precisam de aceder a determinadas portas e nomes de domínio totalmente qualificados (FQDNs). Estes pontos finais são necessários para que os nós comuniquem com o servidor API, ou para descarregar e instalar componentes de cluster de kubernetes e atualizações de segurança de nó. Por exemplo, o cluster precisa de retirar imagens de contentores do sistema de base do Microsoft Container Registry (MCR).

As dependências de saída da AKS são quase totalmente definidas com FQDNs, que não têm endereços estáticos por trás deles. A falta de endereços estáticos significa que os Grupos de Segurança da Rede não podem ser usados para bloquear o tráfego de saída de um cluster AKS. 

Por padrão, os clusters AKS têm acesso ilimitado à internet de saída (saída). Este nível de acesso à rede permite que nós e serviços que você execute para aceder a recursos externos conforme necessário. Se desejar restringir o tráfego de saídas, um número limitado de portos e endereços devem estar acessíveis para manter tarefas saudáveis de manutenção do cluster. A solução mais simples para garantir endereços de saída reside na utilização de um dispositivo de firewall que pode controlar o tráfego de saída com base em nomes de domínio. O Azure Firewall, por exemplo, pode restringir o tráfego http e HTTPS de saída com base na FQDN do destino. Também pode configurar as suas regras de firewall e segurança preferidas para permitir as portas e endereços necessários.

> [!IMPORTANT]
> Este documento cobre apenas como bloquear o tráfego que sai da sub-rede AKS. AKS não tem requisitos de entrada por defeito.  O bloqueio **do tráfego interno da sub-rede** utilizando grupos de segurança de rede (NSGs) e firewalls não é suportado. Para controlar e bloquear o tráfego dentro do cluster, utilize [ * *_políticas de rede_* _][network-policy].

## <a name="required-outbound-network-rules-and-fqdns-for-aks-clusters"></a>Regras de rede de saída exigidas e FQDNs para clusters AKS

As seguintes regras de rede e FQDN/aplicação são necessárias para um cluster AKS, pode usá-las se desejar configurar uma solução diferente da Azure Firewall.

_ As dependências de endereços IP destinam-se ao tráfego não-HTTP/S (tráfego TCP e UDP)
* Os pontos finais FQDN HTTP/HTTPS podem ser colocados no seu dispositivo de firewall.
* Os pontos finais wildcard HTTP/HTTPS são dependências que podem variar com o seu cluster AKS com base em uma série de qualificações.
* A AKS usa um controlador de admissão para injetar o FQDN como uma variável ambiental para todas as implementações no sistema kube e sistema de gatekeeper, que garante que toda a comunicação do sistema entre nós e servidor API utiliza o servidor API FQDN e não o IP do servidor API. 
* Se tiver uma aplicação ou solução que precise de falar com o servidor API, tem de adicionar uma regra de rede **adicional** para permitir que *a comunicação TCP seja a porta 443 do IP do seu servidor API.*
* Em raras ocasiões, se houver uma operação de manutenção, o seu IP do servidor API pode mudar. As operações de manutenção planeadas que podem alterar o IP do servidor API são sempre comunicadas com antecedência.


### <a name="azure-global-required-network-rules"></a>Azure Global exigiu regras de rede

As regras de rede exigidas e as dependências de endereços IP são:

| Ponto final de destino                                                             | Protocolo | Porta    | Utilização  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Ou* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Ou* <br/> [CIDRs regionais](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Ou* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Para uma comunicação segura entre os nós e o avião de controlo. Isto não é necessário para [aglomerados privados](private-clusters.md)|
| **`*:9000`** <br/> *Ou* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Ou* <br/> [CIDRs regionais](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Ou* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Para uma comunicação segura entre os nós e o avião de controlo. Isto não é necessário para [aglomerados privados](private-clusters.md) |
| **`*:123`** ou **`ntp.ubuntu.com:123`** (se utilizar as regras da rede Azure Firewall)  | UDP      | 123     | Requerido para sincronização de tempo do Protocolo de Tempo de Rede (NTP) nos nós Linux.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Se estiver a utilizar servidores DNS personalizados, deve certificar-se de que estão acessíveis pelos nós do cluster. |
| **`APIServerIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Necessários se as cápsulas/implementações que acedam ao Servidor API, essas cápsulas/implementações utilizariam o IP API. Isto não é necessário para [aglomerados privados](private-clusters.md)  |

### <a name="azure-global-required-fqdn--application-rules"></a>AZure Global exigiu regras fQDN /aplicação 

São necessárias as seguintes regras de aplicação fQDN/ aplicação:

| Destino FQDN                 | Porta            | Utilização      |
|----------------------------------|-----------------|----------|
| **`*.hcp.<location>.azmk8s.io`** | **`HTTPS:443`** | Necessário para a comunicação do servidor API <->. *\<location\>* Substitua-a pela região onde o seu cluster AKS está implantado. |
| **`mcr.microsoft.com`**          | **`HTTPS:443`** | Obrigado a aceder a imagens no Microsoft Container Registry (MCR). Este registo contém imagens/gráficos de primeira parte (por exemplo, coreDNS, etc.). Estas imagens são necessárias para a correta criação e funcionamento do cluster, incluindo operações de escala e upgrade.  |
| **`*.cdn.mscr.io`**              | **`HTTPS:443`** | Necessário para armazenamento MCR apoiado pela Rede de Entrega de Conteúdos Azure (CDN). |
| **`*.data.mcr.microsoft.com`**   | **`HTTPS:443`** | Necessário para o armazenamento mcR apoiado pela rede de entrega de conteúdos Azure (CDN). |
| **`management.azure.com`**       | **`HTTPS:443`** | Necessário para as operações da Kubernetes contra a Azure API. |
| **`login.microsoftonline.com`**  | **`HTTPS:443`** | Requerido para a autenticação do Azure Ative Directory. |
| **`packages.microsoft.com`**     | **`HTTPS:443`** | Este endereço é o repositório de pacotes da Microsoft utilizado para operações em cache *apt-get.*  Os pacotes de exemplo incluem Moby, PowerShell e Azure CLI. |
| **`acs-mirror.azureedge.net`**   | **`HTTPS:443`** | Este endereço destina-se ao repositório necessário para descarregar e instalar binários necessários, como kubenet e Azure CNI. |

### <a name="azure-china-21vianet-required-network-rules"></a>Azure China 21Vianet exigiu regras de rede

As regras de rede exigidas e as dependências de endereços IP são:

| Ponto final de destino                                                             | Protocolo | Porta    | Utilização  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Ou* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.Region:1194`** <br/> *Ou* <br/> [CIDRs regionais](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Ou* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Para uma comunicação segura entre os nós e o avião de controlo. |
| **`*:9000`** <br/> *Ou* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Ou* <br/> [CIDRs regionais](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Ou* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Para uma comunicação segura entre os nós e o avião de controlo. |
| **`*:22`** <br/> *Ou* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:22`** <br/> *Ou* <br/> [CIDRs regionais](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:22`** <br/> *Ou* <br/> **`APIServerIP:22`** `(only known after cluster creation)`  | TCP           | 22      | Para uma comunicação segura entre os nós e o avião de controlo. |
| **`*:123`** ou **`ntp.ubuntu.com:123`** (se utilizar as regras da rede Azure Firewall)  | UDP      | 123     | Requerido para sincronização de tempo do Protocolo de Tempo de Rede (NTP) nos nós Linux.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Se estiver a utilizar servidores DNS personalizados, deve certificar-se de que estão acessíveis pelos nós do cluster. |
| **`APIServerIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Necessários se as cápsulas/implementações que acedam ao Servidor API, essas cápsulas/implementações utilizariam o IP API.  |

### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure China 21Vianet exigiu regras FQDN /aplicação

São necessárias as seguintes regras de aplicação fQDN/ aplicação:

| Destino FQDN                               | Porta            | Utilização      |
|------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Necessário para a comunicação do servidor API <->. *\<location\>* Substitua-a pela região onde o seu cluster AKS está implantado. |
| **`*.tun.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Necessário para a comunicação do servidor API <->. *\<location\>* Substitua-a pela região onde o seu cluster AKS está implantado. |
| **`mcr.microsoft.com`**                        | **`HTTPS:443`** | Obrigado a aceder a imagens no Microsoft Container Registry (MCR). Este registo contém imagens/gráficos de primeira parte (por exemplo, coreDNS, etc.). Estas imagens são necessárias para a correta criação e funcionamento do cluster, incluindo operações de escala e upgrade. |
| **`*.cdn.mscr.io`**                            | **`HTTPS:443`** | Necessário para armazenamento MCR apoiado pela Rede de Entrega de Conteúdos Azure (CDN). |
| **`.data.mcr.microsoft.com`**                  | **`HTTPS:443`** | Necessário para armazenamento MCR apoiado pela Rede de Entrega de Conteúdos Azure (CDN). |
| **`management.chinacloudapi.cn`**              | **`HTTPS:443`** | Necessário para as operações da Kubernetes contra a Azure API. |
| **`login.chinacloudapi.cn`**                   | **`HTTPS:443`** | Requerido para a autenticação do Azure Ative Directory. |
| **`packages.microsoft.com`**                   | **`HTTPS:443`** | Este endereço é o repositório de pacotes da Microsoft utilizado para operações em cache *apt-get.*  Os pacotes de exemplo incluem Moby, PowerShell e Azure CLI. |
| **`*.azk8s.cn`**                               | **`HTTPS:443`** | Este endereço destina-se ao repositório necessário para descarregar e instalar binários necessários, como kubenet e Azure CNI. |

### <a name="azure-us-government-required-network-rules"></a>Governo dos EUA exigiu regras de rede

As regras de rede exigidas e as dependências de endereços IP são:

| Ponto final de destino                                                             | Protocolo | Porta    | Utilização  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Ou* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Ou* <br/> [CIDRs regionais](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Ou* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Para uma comunicação segura entre os nós e o avião de controlo. |
| **`*:9000`** <br/> *Ou* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Ou* <br/> [CIDRs regionais](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Ou* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Para uma comunicação segura entre os nós e o avião de controlo. |
| **`*:123`** ou **`ntp.ubuntu.com:123`** (se utilizar as regras da rede Azure Firewall)  | UDP      | 123     | Requerido para sincronização de tempo do Protocolo de Tempo de Rede (NTP) nos nós Linux.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Se estiver a utilizar servidores DNS personalizados, deve certificar-se de que estão acessíveis pelos nós do cluster. |
| **`APIServerIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Necessários se as cápsulas/implementações que acedam ao Servidor API, essas cápsulas/implementações utilizariam o IP API.  |

### <a name="azure-us-government-required-fqdn--application-rules"></a>O Governo dos EUA exigiu regras de FQDN/ aplicação 

São necessárias as seguintes regras de aplicação fQDN/ aplicação:

| Destino FQDN                                        | Porta            | Utilização      |
|---------------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.aks.containerservice.azure.us`** | **`HTTPS:443`** | Necessário para a comunicação do servidor API <->. *\<location\>* Substitua-a pela região onde o seu cluster AKS está implantado.|
| **`mcr.microsoft.com`**                                 | **`HTTPS:443`** | Obrigado a aceder a imagens no Microsoft Container Registry (MCR). Este registo contém imagens/gráficos de primeira parte (por exemplo, coreDNS, etc.). Estas imagens são necessárias para a correta criação e funcionamento do cluster, incluindo operações de escala e upgrade. |
| **`*.cdn.mscr.io`**                                     | **`HTTPS:443`** | Necessário para armazenamento MCR apoiado pela Rede de Entrega de Conteúdos Azure (CDN). |
| **`*.data.mcr.microsoft.com`**                          | **`HTTPS:443`** | Necessário para o armazenamento mcR apoiado pela rede de entrega de conteúdos Azure (CDN). |
| **`management.usgovcloudapi.net`**                      | **`HTTPS:443`** | Necessário para as operações da Kubernetes contra a Azure API. |
| **`login.microsoftonline.us`**                          | **`HTTPS:443`** | Requerido para a autenticação do Azure Ative Directory. |
| **`packages.microsoft.com`**                            | **`HTTPS:443`** | Este endereço é o repositório de pacotes da Microsoft utilizado para operações em cache *apt-get.*  Os pacotes de exemplo incluem Moby, PowerShell e Azure CLI. |
| **`acs-mirror.azureedge.net`**                          | **`HTTPS:443`** | Este endereço destina-se ao repositório necessário para instalar binários necessários, como kubenet e Azure CNI. |

## <a name="optional-recommended-fqdn--application-rules-for-aks-clusters"></a>Regras de FQDN/aplicação recomendadas opcionais para clusters AKS

As seguintes regras de FQDN/aplicação são opcionais, mas recomendadas para clusters AKS:

| Destino FQDN                                                               | Porta          | Utilização      |
|--------------------------------------------------------------------------------|---------------|----------|
| **`security.ubuntu.com`, `azure.archive.ubuntu.com`, `changelogs.ubuntu.com`** | **`HTTP:80`** | Este endereço permite que os nós do cluster Linux descarreguem os patches e atualizações de segurança necessários. |

Se optar por bloquear/não permitir estes FQDNs, os nós só receberão atualizações de SO quando fizer uma [atualização de imagem de nó](node-image-upgrade.md) ou [atualização do cluster](upgrade-cluster.md).

## <a name="gpu-enabled-aks-clusters"></a>GPU habilitado clusters AKS

### <a name="required-fqdn--application-rules"></a>Regras de FQDN /aplicação necessárias

São necessárias as seguintes regras de FQDN/aplicação para clusters AKS que tenham GPU habilitado:

| Destino FQDN                        | Porta      | Utilização      |
|-----------------------------------------|-----------|----------|
| **`nvidia.github.io`**                  | **`HTTPS:443`** | Este endereço é utilizado para a correta instalação e operação do condutor nos nós baseados em GPU. |
| **`us.download.nvidia.com`**            | **`HTTPS:443`** | Este endereço é utilizado para a correta instalação e operação do condutor nos nós baseados em GPU. |
| **`apt.dockerproject.org`**             | **`HTTPS:443`** | Este endereço é utilizado para a correta instalação e operação do condutor nos nós baseados em GPU. |

## <a name="windows-server-based-node-pools"></a>Piscinas de nó baseados no Windows Server 

### <a name="required-fqdn--application-rules"></a>Regras de FQDN /aplicação necessárias

São necessárias as seguintes regras de FQDN / aplicação para a utilização de conjuntos de nó baseados no Windows Server:

| Destino FQDN                                                           | Porta      | Utilização      |
|----------------------------------------------------------------------------|-----------|----------|
| **`onegetcdn.azureedge.net, go.microsoft.com`**                            | **`HTTPS:443`** | Para instalar binários relacionados com janelas |
| **`*.mp.microsoft.com, www.msftconnecttest.com, ctldl.windowsupdate.com`** | **`HTTP:80`**   | Para instalar binários relacionados com janelas |

## <a name="aks-addons-and-integrations"></a>Addons e integrações AKS

### <a name="azure-monitor-for-containers"></a>Azure Monitor para contentores

Existem duas opções para fornecer acesso ao Azure Monitor para contentores, pode permitir o Azure Monitor [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) **ou** fornecer acesso às regras de FQDN/Aplicação necessárias.

#### <a name="required-network-rules"></a>Regras de rede exigidas

São necessárias as seguintes regras de aplicação fQDN/ aplicação:

| Ponto final de destino                                                             | Protocolo | Porta    | Utilização  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | Este ponto final é utilizado para enviar dados de métricas e registos para Azure Monitor e Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Regras de FQDN /aplicação necessárias

São necessárias as seguintes regras de FQDN/aplicação para os clusters AKS que tenham o Monitor Azure para contentores habilitados:

| FQDN                                    | Porta      | Utilização      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | **`HTTPS:443`**    | Este ponto final é utilizado para métricas e telemetria de monitorização utilizando o Monitor Azure. |
| *.ods.opinsights.azure.com    | **`HTTPS:443`**    | Este ponto final é utilizado pelo Azure Monitor para ingerir dados de análise de registo. |
| *.oms.opinsights.azure.com | **`HTTPS:443`** | Este ponto final é utilizado pelo omsagent, que é utilizado para autenticar o serviço de análise de registos. |
| *.monitoring.azure.com | **`HTTPS:443`** | Este ponto final é utilizado para enviar dados de métricas para o Azure Monitor. |

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

Atualize a sua configuração de firewall ou segurança para permitir o tráfego de rede de e para todos os serviços de infraestrutura de FQDNs e [Azure Dev Spaces][dev-spaces-service-tags]abaixo.

#### <a name="required-network-rules"></a>Regras de rede exigidas

| Ponto final de destino                                                             | Protocolo | Porta    | Utilização  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureDevSpaces`**  | TCP           | 443      | Este ponto final é utilizado para enviar dados de métricas e registos para Azure Monitor e Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Regras de FQDN /aplicação necessárias 

São necessárias as seguintes regras de FQDN/aplicação para clusters AKS que tenham os Espaços Azure Dev habilitados:

| FQDN                                    | Porta      | Utilização      |
|-----------------------------------------|-----------|----------|
| `cloudflare.docker.com` | **`HTTPS:443`** | Este endereço é usado para puxar o linux alpino e outras imagens do Azure Dev Spaces |
| `gcr.io` | **`HTTPS:443`** | Este endereço é usado para puxar imagens de leme/leme |
| `storage.googleapis.com` | **`HTTPS:443`** | Este endereço é usado para puxar imagens de leme/leme |


### <a name="azure-policy"></a>Azure Policy

#### <a name="required-fqdn--application-rules"></a>Regras de FQDN /aplicação necessárias 

São necessárias as seguintes regras de FQDN/aplicação para clusters AKS que tenham a Política Azure ativada.

| FQDN                                          | Porta      | Utilização      |
|-----------------------------------------------|-----------|----------|
| **`gov-prod-policy-data.trafficmanager.net`** | **`HTTPS:443`** | Este endereço é utilizado para o correto funcionamento da Política Azure. (atualmente em pré-visualização em AKS) |
| **`raw.githubusercontent.com`**               | **`HTTPS:443`** | Este endereço é utilizado para retirar as políticas incorporadas do GitHub para garantir o correto funcionamento da Política Azure. (atualmente em pré-visualização em AKS) |
| **`dc.services.visualstudio.com`**            | **`HTTPS:443`** | Addon Azure Policy que envia dados de telemetria para aplicações insights ponto final. |

## <a name="restrict-egress-traffic-using-azure-firewall"></a>Restringir o tráfego de saídas usando firewall Azure

A Azure Firewall fornece um Serviço Azure Kubernetes `AzureKubernetesService` () FQDN Tag para simplificar esta configuração. 

> [!NOTE]
> A tag FQDN contém todas as FQDNs listadas acima e é mantida automaticamente atualizada.
>
> Recomendamos ter um mínimo de 20 IPs frontend no Azure Firewall para cenários de produção para evitar incorrer em problemas de exaustão do porto SNAT.

Abaixo está um exemplo de arquitetura da implantação:

![Topologia bloqueada](media/limit-egress-traffic/aks-azure-firewall-egress.png)

* Public Ingress é forçado a fluir através de filtros de firewall
  * Os nós de agente AKS estão isolados numa sub-rede dedicada.
  * [O Azure Firewall](../firewall/overview.md) está implantado na sua própria sub-rede.
  * Uma regra de DNAT traduz o IP público FW no IP frontal LB.
* Os pedidos de saída começam de nós de agente para o IP interno do Azure Firewall usando uma [rota definida pelo utilizador](egress-outboundtype.md)
  * Os pedidos dos nós de agente da AKS seguem um UDR que foi colocado na sub-rede em que o cluster AKS foi implantado.
  * Azure Firewall sai da rede virtual a partir de um frontend IP público
  * O acesso à internet pública ou a outros serviços Azure flui de e para o endereço IP frontal de firewall
  * Opcionalmente, o acesso ao plano de controlo AKS está protegido por [gamas IP autorizadas](./api-server-authorized-ip-ranges.md)do servidor API , que inclui o endereço IP frontal do firewall público.
* Tráfego Interno
  * Opcionalmente, em vez disso ou além de um [Balanceador de Carga Pública,](load-balancer-standard.md) pode utilizar um [Balançador de Carga Interna](internal-lb.md) para tráfego interno, que também pode isolar na sua própria sub-rede.


Os passos abaixo utilizam a etiqueta FQDN da Azure Firewall `AzureKubernetesService` para restringir o tráfego de saída do cluster AKS e fornecer um exemplo de como configurar o tráfego de entrada pública através da firewall.


### <a name="set-configuration-via-environment-variables"></a>Definir configuração através de variáveis ambientais

Defina um conjunto de variáveis ambientais a serem usadas em criações de recursos.

```bash
PREFIX="aks-egress"
RG="${PREFIX}-rg"
LOC="eastus"
PLUGIN=azure
AKSNAME="${PREFIX}"
VNET_NAME="${PREFIX}-vnet"
AKSSUBNET_NAME="aks-subnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}-fw"
FWPUBLICIP_NAME="${PREFIX}-fwpublicip"
FWIPCONFIG_NAME="${PREFIX}-fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}-fwrt"
FWROUTE_NAME="${PREFIX}-fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}-fwinternet"
```

### <a name="create-a-virtual-network-with-multiple-subnets"></a>Criar uma rede virtual com várias sub-redes

Fornecendo uma rede virtual com duas sub-redes separadas, uma para o cluster, uma para a firewall. Opcionalmente, também pode criar um para entrada de serviço interno.

![Topologia de rede vazia](media/limit-egress-traffic/empty-network.png)

Criar um grupo de recursos para deter todos os recursos.

```azurecli
# Create Resource Group

az group create --name $RG --location $LOC
```

Crie uma rede virtual com duas sub-redes para hospedar o cluster AKS e o Azure Firewall. Cada um terá a sua própria sub-rede. Vamos começar com a rede AKS.

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --location $LOC \
    --address-prefixes 10.42.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 10.42.1.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 10.42.2.0/24
```

### <a name="create-and-set-up-an-azure-firewall-with-a-udr"></a>Criar e configurar uma Firewall Azure com um UDR

As regras de entrada e saída do Azure Firewall devem ser configuradas. O principal objetivo da firewall é permitir que as organizações configuram a entrada de granular e saiam das regras de tráfego dentro e fora do Cluster AKS.

![Firewall e UDR](media/limit-egress-traffic/firewall-udr.png)


> [!IMPORTANT]
> Se o seu cluster ou aplicação criar um grande número de ligações de saída direcionadas para o mesmo ou pequeno subconjunto de destinos, poderá necessitar de mais IPs frontend de firewall para evitar o máximo de portas por IP frontal.
> Para obter mais informações sobre como criar uma firewall Azure com vários IPs, consulte [ **aqui**](../firewall/quick-create-multiple-ip-template.md)

Crie um recurso IP público SKU padrão que será usado como o endereço frontal Azure Firewall.

```azurecli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Registe a pré-visualização da extensão cli para criar uma Firewall Azure.
```azurecli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC --enable-dns-proxy true
```
O endereço IP criado anteriormente pode agora ser atribuído ao frontend de firewall.

> [!NOTE]
> A configuração do endereço IP público para o Azure Firewall pode demorar alguns minutos.
> Para alavancar o FQDN nas regras de rede precisamos de procuração de DNS ativado, quando ativado a firewall ouvirá na porta 53 e irá encaminhar os pedidos dns para o servidor DNS especificado acima. Isto permitirá que a firewall traduza automaticamente esse FQDN.

```azurecli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Quando o comando anterior tiver sido bem sucedido, guarde o endereço IP frontal de firewall para configuração mais tarde.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

> [!NOTE]
> Se utilizar acesso seguro ao servidor AKS API com [intervalos de endereço IP autorizados,](./api-server-authorized-ip-ranges.md)tem de adicionar o IP público de firewall ao intervalo IP autorizado.

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Criar um UDR com um salto para Azure Firewall

O Azure liga automaticamente o tráfego entre sub-redes Azure, redes virtuais e redes no local. Se quiser alterar qualquer um dos roteamentos padrão do Azure, fá-lo criando uma tabela de rotas.

Crie uma tabela de rotas vazia para ser associada a uma determinada sub-rede. A tabela de rotas definirá o próximo salto como o Azure Firewall criado acima. Cada sub-rede pode ter zero ou uma tabela de rotas associada a si.

```azurecli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG -l $LOC --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Consulte [a documentação da tabela de rotas de rede virtual](../virtual-network/virtual-networks-udr-overview.md#user-defined) sobre como pode sobrepor as rotas do sistema padrão da Azure ou adicionar rotas adicionais à tabela de rotas de uma sub-rede.

### <a name="adding-firewall-rules"></a>Adicionar regras de firewall

Abaixo estão três regras de rede que pode usar para configurar a sua firewall, podendo precisar de adaptar estas regras com base na sua implementação. A primeira regra permite o acesso à porta 9000 via TCP. A segunda regra permite o acesso aos portos 1194 e 123 via UDP (se estiver a implantar para a Azure China 21Vianet, poderá necessitar de [mais).](#azure-china-21vianet-required-network-rules) Ambas as regras só permitirão o tráfego destinado à CIDR da Região de Azure que estamos a usar, neste caso, nos EUA. Finalmente, adicionaremos uma terceira regra de abertura da porta 123 à `ntp.ubuntu.com` FQDN via UDP (adicionar um FQDN como regra de rede é uma das características específicas do Azure Firewall, e você precisará adaptá-lo quando usar as suas próprias opções).

Depois de definir as regras de rede, também adicionaremos uma regra de aplicação usando o `AzureKubernetesService` que cobre todas as FQDNs necessárias acessíveis através da porta TCP 443 e porta 80.

```
# Add FW Network Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apiudp' --protocols 'UDP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 1194 --action allow --priority 100
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apitcp' --protocols 'TCP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 9000
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'time' --protocols 'UDP' --source-addresses '*' --destination-fqdns 'ntp.ubuntu.com' --destination-ports 123

# Add FW Application Rules

az network firewall application-rule create -g $RG -f $FWNAME --collection-name 'aksfwar' -n 'fqdn' --source-addresses '*' --protocols 'http=80' 'https=443' --fqdn-tags "AzureKubernetesService" --action allow --priority 100
```

Consulte a [documentação do Azure Firewall](../firewall/overview.md) para saber mais sobre o serviço Azure Firewall.

### <a name="associate-the-route-table-to-aks"></a>Associe a tabela de rotas à AKS

Para associar o cluster à firewall, a sub-rede dedicada para a sub-rede do cluster deve fazer referência à tabela de rotas criada acima. A associação pode ser feita através da emissão de um comando à rede virtual que mantém o cluster e a firewall para atualizar a tabela de rotas da sub-rede do cluster.

```azurecli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

### <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Implementar AKS com tipo de UDR de saída para a rede existente

Agora, um cluster AKS pode ser implantado na rede virtual existente. Também usaremos o [tipo `userDefinedRouting` de saída ](egress-outboundtype.md), esta funcionalidade garante que qualquer tráfego de saída será forçado através da firewall e não existirão outros caminhos de saída (por padrão, o tipo de saída do Balanceador de Carga poderá ser utilizado).

![aks-implementar](media/limit-egress-traffic/aks-udr-fw.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Criar um principal de serviço com acesso à prestação dentro da rede virtual existente

Um diretor de serviço é usado pela AKS para criar recursos de cluster. O principal de serviço que é passado no tempo de criação é usado para criar recursos AKS subjacentes, tais como recursos de armazenamento, IPs e Balancedores de Carga usados pela AKS (você também pode usar uma [identidade gerida](use-managed-identity.md) em vez disso). Se não for concedidas as permissões apropriadas abaixo, não poderá providenciar o Cluster AKS.

```azurecli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Agora substitua o `APPID` e `PASSWORD` abaixo pelo appid principal de serviço e senha principal de serviço autogerida pela saída de comando anterior. Vamos fazer referência ao ID de recursos VNET para conceder as permissões ao diretor de serviço para que a AKS possa implantar recursos nele.

```azurecli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role "Network Contributor"
```

Pode verificar as permissões detalhadas que são necessárias [aqui.](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)

> [!NOTE]
> Se estiver a utilizar o plugin da rede Kubenet, terá de dar ao chefe de serviço AKS ou a permissões de identidade geridas à tabela de rotas pré-criada, uma vez que o Kubenet necessita de uma tabela de rotas para adicionar regras de encaminhamento neccesário. 
> ```azurecli-interactive
> RTID=$(az network route-table show -g $RG -n $FWROUTE_TABLE_NAME --query id -o tsv)
> az role assignment create --assignee $APPID --scope $RTID --role "Network Contributor"
> ```

### <a name="deploy-aks"></a>Implementar AKS

Finalmente, o cluster AKS pode ser implantado na sub-rede existente que dedicámos para o cluster. A sub-rede-alvo a implantar é definida com a variável ambiental, `$SUBNETID` . Não definimos a `$SUBNETID` variável nos passos anteriores. Para definir o valor do ID da sub-rede, pode utilizar o seguinte comando:

```azurecli
SUBNETID=$(az network vnet subnet show -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --query id -o tsv)
```

Irá definir o tipo de saída para utilizar o UDR que já existe na sub-rede. Esta configuração permitirá que a AKS ignore a configuração e o provisionamento IP para o equilibrador de carga.

> [!IMPORTANT]
> Para obter mais informações sobre o UDR do tipo de saída, incluindo limitações, consulte [**a saída de saída do tipo UDR**](egress-outboundtype.md#limitations).


> [!TIP]
> Podem ser adicionadas funcionalidades adicionais à implantação do cluster, como [**o Private Cluster.**](private-clusters.md) 
>
> A funcionalidade AKS para [**gamas IP autorizadas pelo servidor API**](api-server-authorized-ip-ranges.md) pode ser adicionada para limitar o acesso do servidor API apenas ao ponto final público da firewall. A função de gama IP autorizada é denotada no diagrama como opcional. Ao permitir que a função de gama IP autorizada limite o acesso ao servidor API, as suas ferramentas de desenvolvedor devem utilizar uma caixa de salto a partir da rede virtual da firewall ou deve adicionar todos os pontos finais do desenvolvedor à gama IP autorizada.

```azurecli
az aks create -g $RG -n $AKSNAME -l $LOC \
  --node-count 3 --generate-ssh-keys \
  --network-plugin $PLUGIN \
  --outbound-type userDefinedRouting \
  --service-cidr 10.41.0.0/16 \
  --dns-service-ip 10.41.0.10 \
  --docker-bridge-address 172.17.0.1/16 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
```

### <a name="enable-developer-access-to-the-api-server"></a>Permitir o acesso do desenvolvedor ao servidor API

Se utilizou gamas IP autorizadas para o cluster no passo anterior, deve adicionar os endereços IP de ferramentas do seu desenvolvedor à lista de clusters AKS de gamas IP aprovadas para aceder ao servidor API a partir daí. Outra opção é configurar uma caixa de salto com a ferramenta necessária dentro de uma sub-rede separada na rede virtual do Firewall.

Adicione outro endereço IP às gamas aprovadas com o seguinte comando

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Use o comando [az aks get-credentials][az-aks-get-credentials] para configurar `kubectl` para se ligar ao seu cluster Kubernetes recém-criado. 

 ```azurecli
 az aks get-credentials -g $RG -n $AKSNAME
 ```

### <a name="deploy-a-public-service"></a>Implantar um serviço público
Pode agora começar a expor serviços e a implementar aplicações neste cluster. Neste exemplo, vamos expor um serviço público, mas também pode optar por expor um serviço interno através do [equilibrador de carga interno](internal-lb.md).

![DNAT do Serviço Público](media/limit-egress-traffic/aks-create-svc.png)

Implemente a aplicação de aplicação de voto Azure copiando o yaml abaixo para um ficheiro chamado `example.yaml` .

```yaml
# voting-storage-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-storage
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-storage
  template:
    metadata:
      labels:
        app: voting-storage
    spec:
      containers:
      - name: voting-storage
        image: mcr.microsoft.com/aks/samples/voting/storage:2.0
        args: ["--ignore-db-dir=lost+found"]
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
---
# voting-storage-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: voting-storage-secret
type: Opaque
data:
  MYSQL_USER: ZGJ1c2Vy
  MYSQL_PASSWORD: UGFzc3dvcmQxMg==
  MYSQL_DATABASE: YXp1cmV2b3Rl
  MYSQL_ROOT_PASSWORD: UGFzc3dvcmQxMg==
---
# voting-storage-pv-claim.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
# voting-storage-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-storage
  labels: 
    app: voting-storage
spec:
  ports:
  - port: 3306
    name: mysql
  selector:
    app: voting-storage
---
# voting-app-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-app
  template:
    metadata:
      labels:
        app: voting-app
    spec:
      containers:
      - name: voting-app
        image: mcr.microsoft.com/aks/samples/voting/app:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
        - name: ANALYTICS_HOST
          value: "voting-analytics"
---
# voting-app-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-app
  labels: 
    app: voting-app
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 8080
    name: http
  selector:
    app: voting-app
---
# voting-analytics-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-analytics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-analytics
      version: "2.0"
  template:
    metadata:
      labels:
        app: voting-analytics
        version: "2.0"
    spec:
      containers:
      - name: voting-analytics
        image: mcr.microsoft.com/aks/samples/voting/analytics:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
---
# voting-analytics-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-analytics
  labels: 
    app: voting-analytics
spec:
  ports:
  - port: 8080
    name: http
  selector:
    app: voting-analytics
```

Implementar o serviço executando:

```bash
kubectl apply -f example.yaml
```

### <a name="add-a-dnat-rule-to-azure-firewall"></a>Adicione uma regra de DNAT ao Azure Firewall

> [!IMPORTANT]
> Quando utilizar o Azure Firewall para restringir o tráfego de saídas e criar uma rota definida pelo utilizador (UDR) para forçar todo o tráfego de saídas, certifique-se de que cria uma regra de ADN apropriada na Firewall para permitir corretamente o tráfego de entrada. A utilização do Azure Firewall com um UDR quebra a configuração de entrada devido ao encaminhamento assimétrico. (O problema ocorre se a sub-rede AKS tiver uma rota padrão que vai para o endereço IP privado da firewall, mas está a utilizar um equilibrador de carga público - ingress ou serviço kubernetes do tipo: LoadBalancer). Neste caso, o tráfego do balançador de carga de entrada é recebido através do seu endereço IP público, mas a trajetória de retorno passa pelo endereço IP privado da firewall. Como a firewall é imponente, deixa cair o pacote de retorno porque a firewall não está ciente de uma sessão estabelecida. Para aprender a integrar o Azure Firewall com o seu equilibrador de carga de entrada ou serviço, consulte [Integrate Azure Firewall com o Azure Standard Load Balancer](../firewall/integrate-lb.md).


Para configurar a conectividade de entrada, uma regra de DNAT deve ser escrita para a Firewall Azure. Para testar a conectividade com o seu cluster, é definida uma regra para o endereço IP público frontend de firewall para encaminhar para o IP interno exposto pelo serviço interno.

O endereço de destino pode ser personalizado, uma vez que é a porta da firewall a ser acedida. O endereço traduzido deve ser o endereço IP do balançador de carga interno. A porta traduzida deve ser a porta exposta para o seu serviço Kubernetes.

Terá de especificar o endereço IP interno atribuído ao balançador de carga criado pelo serviço Kubernetes. Recupere o endereço executando:

```bash
kubectl get services
```

O endereço IP necessário será listado na coluna EXTERNAL-IP, semelhante ao seguinte.

```bash
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP      10.41.0.1       <none>        443/TCP        10h
voting-analytics   ClusterIP      10.41.88.129    <none>        8080/TCP       9m
voting-app         LoadBalancer   10.41.185.82    20.39.18.6    80:32718/TCP   9m
voting-storage     ClusterIP      10.41.221.201   <none>        3306/TCP       9m
```

Obtenha o IP de serviço executando:
```bash
SERVICE_IP=$(k get svc voting-app -o jsonpath='{.status.loadBalancer.ingress[*].ip}')
```

Adicione a regra NAT executando:
```azurecli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address $SERVICE_IP
```

### <a name="validate-connectivity"></a>Validar conectividade

Navegue para o endereço IP frontend Azure Firewall num browser para validar a conectividade.

Devia ver a aplicação de voto AKS. Neste exemplo, o IP público firewall foi `52.253.228.132` .


![A screenshot mostra a App de Voto A K S com botões para Gatos, Cães e Reset, e totais.](media/limit-egress-traffic/aks-vote.png)


### <a name="clean-up-resources"></a>Limpar os recursos

Para limpar os recursos do Azure, elimine o grupo de recursos AKS.

```azurecli
az group delete -g $RG
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu quais portas e moradas para permitir se quiser restringir o tráfego de saídas para o cluster. Também viu como proteger o tráfego de saída usando a Azure Firewall. 

Se necessário, pode generalizar os passos acima para encaminhar o tráfego para a sua solução de saída preferida, seguindo a [ `userDefinedRoute` documentação](egress-outboundtype.md)do Tipo de Saída .

Se pretender restringir a forma como as cápsulas comunicam entre si e East-West restrições de tráfego dentro do cluster, consulte [o tráfego seguro entre as cápsulas utilizando as políticas de rede em AKS][network-policy].

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
