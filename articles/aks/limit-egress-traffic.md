---
title: Restringir o tráfego de egress no Serviço Azure Kubernetes (AKS)
description: Saiba quais os portos e endereços necessários para controlar o tráfego de egress no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: d723f7b1e7331e65d17dca5873b891ec46d76c0e
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82207178"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Controlo egress tráfego para nós de cluster no Serviço Azure Kubernetes (AKS)

Por padrão, os clusters AKS têm acesso ilimitado à Internet (egress). Este nível de acesso à rede permite que nós e serviços que execute aceda mato para aceder aos recursos externos conforme necessário. Se pretender restringir o tráfego de saídas, um número limitado de portas e endereços deve ser acessível para manter tarefas saudáveis de manutenção do cluster. O seu cluster está configurado por padrão para utilizar apenas imagens de contentores do sistema base do Registo de Contentores da Microsoft (MCR) ou do Registo de Contentores Azure (ACR). Configure as suas regras de firewall e de segurança preferenciais para permitir estas portas e endereços necessários.

Este artigo detalha quais as portas de rede e nomes de domínio totalmente qualificados (FQDNs) são necessários e opcionais se restringir o tráfego de saída num cluster AKS.

> [!IMPORTANT]
> Este documento cobre apenas como bloquear o tráfego deixando a subnet AKS. A AKS não tem requisitos de ingresso.  O bloqueio do tráfego interno de subredes utilizando grupos de segurança de rede (NSGs) e firewalls não é suportado. Para controlar e bloquear o tráfego dentro do cluster, utilize políticas de [rede.][network-policy]

## <a name="before-you-begin"></a>Antes de começar

Precisa da versão Azure CLI 2.0.66 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

## <a name="egress-traffic-overview"></a>Visão geral do tráfego de Egress

Para fins de gestão e operacionais, os nós de um cluster AKS precisam de aceder a determinadas portas e nomes de domínio totalmente qualificados (FQDNs). Estas ações podem ser para comunicar com o servidor API, ou para descarregar e, em seguida, instalar componentes de cluster Core Kubernetes e atualizações de segurança de nó. Por padrão, o tráfego de internet de saída (outbound) não é restrito para nós num cluster AKS. O cluster pode retirar imagens de recipientes do sistema de base de repositórios externos.

Para aumentar a segurança do seu cluster AKS, pode querer restringir o tráfego de saída. O cluster está configurado para retirar imagens do recipiente do sistema base de MCR ou ACR. Se bloquear o tráfego de saída desta forma, defina portas específicas e FQDNs para permitir que os nós AKS se comuniquem corretamente com os serviços externos necessários. Sem estas portas autorizadas e FQDNs, os seus nós AKS não podem comunicar com o servidor API ou instalar componentes nucleares.

Pode utilizar o [Azure Firewall][azure-firewall] ou um aparelho de firewall de terceira parte para proteger o tráfego de saída seletiva e definir estas portas e endereços necessários. A AKS não cria automaticamente estas regras para si. As seguintes portas e endereços são para referência, uma vez que cria as regras adequadas na firewall da sua rede.

> [!IMPORTANT]
> Quando utilizar o Azure Firewall para restringir o tráfego de saída e criar uma rota definida pelo utilizador (UDR) para forçar todo o tráfego de saída, certifique-se de que cria uma regra de ADN apropriada na Firewall para permitir corretamente a entrada de tráfego. A utilização do Firewall Azure com um UDR quebra a configuração de ingresso devido ao encaminhamento assimétrico. (O problema ocorre se a subnet AKS tiver uma rota padrão que vai para o endereço IP privado da firewall, mas está a usar um equilibrador de carga público - ingressou ou serviço Kubernetes do tipo: LoadBalancer). Neste caso, o tráfego de balanceadores de carga de entrada é recebido através do seu endereço IP público, mas a rota de retorno passa pelo endereço IP privado da firewall. Como a firewall é imponente, deixa cair o pacote de retorno porque a firewall não está ciente de uma sessão estabelecida. Para aprender a integrar o Azure Firewall com o seu equilibrador de entrada ou de carga de serviço, consulte [Integrar o Firewall Azure com](https://docs.microsoft.com/azure/firewall/integrate-lb)o Equilíbrio de Carga Padrão Azure .
> Pode bloquear o tráfego para a porta 9000, porta TCP 22 e porta UDP 1194 utilizando uma regra de rede entre o nó ip(s) do trabalhador e o IP para o servidor API.

No AKS, existem dois conjuntos de portas e endereços:

* As [portas e endereços necessários para os clusters AKS](#required-ports-and-addresses-for-aks-clusters) detalham os requisitos mínimos para o tráfego autorizado de saída.
* Os [endereços e portas recomendados opcionais para os clusters AKS](#optional-recommended-addresses-and-ports-for-aks-clusters) não são necessários para todos os cenários, mas a integração com outros serviços como o Azure Monitor não funcionará corretamente. Reveja esta lista de portas opcionais e FQDNs e autorize qualquer um dos serviços e componentes utilizados no seu cluster AKS.

> [!NOTE]
> Limitar o tráfego de esgress só funciona em novos aglomerados AKS. Para os clusters existentes, execute `az aks upgrade` uma operação de [atualização][aks-upgrade] de cluster utilizando o comando antes de limitar o tráfego de saída.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Portas e endereços necessários para clusters do AKS

São necessárias as seguintes portas/regras de rede para um cluster AKS:

* Porta TCP *443*
* TCP [IPAddrOfYourAPIServer]:443 é necessário se tiver uma aplicação que precise de falar com o servidor API.  Esta alteração pode ser definida após a criação do cluster.
* Porta *TCP 9000,* porta TCP *22* e porta UDP *1194* para a cápsula frontal do túnel comunicar com a extremidade do túnel no servidor API.
    * Para obter mais detalhes, consulte o **.hcp.\< localização\>.azmk8s.io* e **.tun.\< localização\>.azmk8s.io* endereços na tabela seguinte.
* Porta UDP *123* para sincronização do tempo do Protocolo de Tempo de Rede (NTP) (nós linux).
* A porta *UDP 53* para DNS também é necessária se tiver cápsulas de acesso direto ao servidor API.

São necessárias as seguintes regras de aplicação FQDN/aplicação:

> [!IMPORTANT]
> ***.blob.core.windows.net e aksrepos.azurecr.io** já não são necessárias regras fQDN para o bloqueio de saídas.  Para os clusters existentes, execute `az aks upgrade` uma operação de [atualização][aks-upgrade] de cluster utilizando o comando para remover estas regras.

> [!IMPORTANT]
> *.cdn.mscr.io foi substituído por *.data.mcr.microsoft.com para as regiões de nuvem pública de Azure. Por favor, atualize as suas regras de firewall existentes para que as alterações entrem em vigor.

- Azure Global

| FQDN                       | Porta      | Utilizar      |
|----------------------------|-----------|----------|
| *.hcp. \<localização\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Este endereço é necessário para a comunicação do servidor API no nó <->. Substitua * \<\> * a localização pela região onde o seu cluster AKS está implantado. |
| *.tun. \<localização\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Este endereço é necessário para a comunicação do servidor API no nó <->. Substitua * \<\> * a localização pela região onde o seu cluster AKS está implantado. |
| *.cdn.mscr.io       | HTTPS:443 | Este endereço é necessário para armazenamento MCR apoiado pela Rede de Entrega de Conteúdos Azure (CDN). |
| mcr.microsoft.com          | HTTPS:443 | Este endereço é necessário para aceder a imagens no Registo de Contentores da Microsoft (MCR). Este registo contém imagens/gráficos de primeira parte (por exemplo, moby, etc.) necessários para o funcionamento do cluster durante a atualização e escala do cluster |
| *.data.mcr.microsoft.com             | HTTPS:443 | Este endereço é necessário para armazenamento MCR apoiado pela rede de entrega de conteúdos Azure (CDN). |
| management.azure.com       | HTTPS:443 | Este endereço é necessário para as operações Kubernetes GET/PUT. |
| login.microsoftonline.com  | HTTPS:443 | Este endereço é necessário para autenticação do Diretório Ativo Azure. |
| ntp.ubuntu.com             | UDP:123   | Este endereço é necessário para a sincronização do tempo NTP nos nós Linux. |
| packages.microsoft.com     | HTTPS:443 | Este endereço é o repositório de pacotes da Microsoft utilizado para operações de *aptidão* em cache.  Os pacotes de exemplo incluem Moby, PowerShell e Azure CLI. |
| acs-mirror.azureedge.net      | HTTPS:443 | Este endereço destina-se ao repositório necessário para instalar binários necessários como kubenet e Azure CNI. |

- Azure China 21Vianet

| FQDN                       | Porta      | Utilizar      |
|----------------------------|-----------|----------|
| *.hcp. \<localização\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Este endereço é necessário para a comunicação do servidor API no nó <->. Substitua * \<\> * a localização pela região onde o seu cluster AKS está implantado. |
| *.tun. \<localização\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Este endereço é necessário para a comunicação do servidor API no nó <->. Substitua * \<\> * a localização pela região onde o seu cluster AKS está implantado. |
| *.azk8s.cn        | HTTPS:443 | Este endereço é necessário para descarregar binários e imagens necessários|
| mcr.microsoft.com          | HTTPS:443 | Este endereço é necessário para aceder a imagens no Registo de Contentores da Microsoft (MCR). Este registo contém imagens/gráficos de primeira parte (por exemplo, moby, etc.) necessários para o funcionamento do cluster durante a atualização e escala do cluster |
| *.cdn.mscr.io       | HTTPS:443 | Este endereço é necessário para armazenamento MCR apoiado pela Rede de Entrega de Conteúdos Azure (CDN). |
| *.data.mcr.microsoft.com             | HTTPS:443 | Este endereço é necessário para armazenamento MCR apoiado pela rede de entrega de conteúdos Azure (CDN). |
| management.chinacloudapi.cn       | HTTPS:443 | Este endereço é necessário para as operações Kubernetes GET/PUT. |
| login.chinacloudapi.cn  | HTTPS:443 | Este endereço é necessário para autenticação do Diretório Ativo Azure. |
| ntp.ubuntu.com             | UDP:123   | Este endereço é necessário para a sincronização do tempo NTP nos nós Linux. |
| packages.microsoft.com     | HTTPS:443 | Este endereço é o repositório de pacotes da Microsoft utilizado para operações de *aptidão* em cache.  Os pacotes de exemplo incluem Moby, PowerShell e Azure CLI. |

- Azure Government

| FQDN                       | Porta      | Utilizar      |
|----------------------------|-----------|----------|
| *.hcp. \<localização\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Este endereço é necessário para a comunicação do servidor API no nó <->. Substitua * \<\> * a localização pela região onde o seu cluster AKS está implantado. |
| *.tun. \<localização\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Este endereço é necessário para a comunicação do servidor API no nó <->. Substitua * \<\> * a localização pela região onde o seu cluster AKS está implantado. |
| mcr.microsoft.com          | HTTPS:443 | Este endereço é necessário para aceder a imagens no Registo de Contentores da Microsoft (MCR). Este registo contém imagens/gráficos de primeira parte (por exemplo, moby, etc.) necessários para o funcionamento do cluster durante a atualização e escala do cluster |
|*.cdn.mscr.io              | HTTPS:443 | Este endereço é necessário para armazenamento MCR apoiado pela Rede de Entrega de Conteúdos Azure (CDN). |
| *.data.mcr.microsoft.com             | HTTPS:443 | Este endereço é necessário para armazenamento MCR apoiado pela rede de entrega de conteúdos Azure (CDN). |
| management.usgovcloudapi.net       | HTTPS:443 | Este endereço é necessário para as operações Kubernetes GET/PUT. |
| login.microsoftonline.us  | HTTPS:443 | Este endereço é necessário para autenticação do Diretório Ativo Azure. |
| ntp.ubuntu.com             | UDP:123   | Este endereço é necessário para a sincronização do tempo NTP nos nós Linux. |
| packages.microsoft.com     | HTTPS:443 | Este endereço é o repositório de pacotes da Microsoft utilizado para operações de *aptidão* em cache.  Os pacotes de exemplo incluem Moby, PowerShell e Azure CLI. |
| acs-mirror.azureedge.net      | HTTPS:443 | Este endereço destina-se ao repositório necessário para instalar binários necessários como kubenet e Azure CNI. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Endereços e portas recomendados opcionais para clusters AKS

As seguintes portas/regras de rede são opcionais para um cluster AKS:

Recomenda-se às seguintes regras de aplicação FQDN/aplicação para que os clusters AKS funcionem corretamente:

| FQDN                                    | Porta      | Utilizar      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com | HTTP:80   | Este endereço permite que os nós do cluster Linux descarreguem as correções e atualizações de segurança necessárias. |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>Endereços e portas necessários para clusters AKS habilitados para GPU

São necessárias as seguintes regras de aplicação FQDN/aplicação para os clusters AKS que tenham GPU habilitado:

| FQDN                                    | Porta      | Utilizar      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS:443 | Este endereço é utilizado para a correta instalação e funcionamento do condutor em nós baseados em GPU. |
| us.download.nvidia.com | HTTPS:443 | Este endereço é utilizado para a correta instalação e funcionamento do condutor em nós baseados em GPU. |
| apt.dockerproject.org | HTTPS:443 | Este endereço é utilizado para a correta instalação e funcionamento do condutor em nós baseados em GPU. |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>Endereços e portas necessários com Monitor Azure para contentores habilitados

São necessárias as seguintes regras de aplicação FQDN/aplicação para os clusters AKS que tenham o Monitor Azure para contentores habilitados:

| FQDN                                    | Porta      | Utilizar      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS:443    | Isto é para métricas corretas e monitorização da telemetria utilizando o Monitor Azure. |
| *.ods.opinsights.azure.com    | HTTPS:443    | Isto é utilizado pelo Azure Monitor para ingerir dados de análise de registo. |
| *.oms.opinsights.azure.com | HTTPS:443 | Este endereço é utilizado pelo omsagent, que é utilizado para autenticar o serviço de análise de registos. |
|*.microsoftonline.com | HTTPS:443 | Isto é usado para autenticar e enviar métricas para o Monitor Azure. |
|*.monitoring.azure.com | HTTPS:443 | Isto é usado para enviar dados de métricas para o Monitor Azure. |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>Endereços e portos necessários com espaços Azure Dev habilitados

São necessárias as seguintes regras de aplicação FQDN/aplicação para os clusters AKS que tenham os Espaços Azure Dev habilitados:

| FQDN                                    | Porta      | Utilizar      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Este endereço é usado para puxar imagens alpinas de linux e outras imagens azure Dev Spaces |
| gcr.io | HTTP:443 | Este endereço é usado para puxar imagens de leme/leme |
| storage.googleapis.com | HTTP:443 | Este endereço é usado para puxar imagens de leme/leme |
| \<azds-guia\>. \<localização\>.azds.io | HTTPS:443 | Para comunicar com os serviços de backend da Azure Dev Spaces para o seu controlador. O FQDN exato pode ser encontrado no "dataplaneFqdn" em %USERPROFILE%\.azds\settings.json |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Endereços e portas necessários para clusters AKS com Política Azure (em pré-visualização pública) habilitados

> [!CAUTION]
> Algumas das funcionalidades abaixo estão em pré-visualização.  As sugestões deste artigo estão sujeitas a alterações à medida que a funcionalidade avança para a pré-visualização pública e futuras fases de lançamento.

São necessárias as seguintes regras de aplicação FQDN/aplicação para os clusters AKS que tenham a Política Azure ativada.

| FQDN                                    | Porta      | Utilizar      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Este endereço é utilizado para o correto funcionamento da Política Azure. (atualmente em pré-visualização no AKS) |
| raw.githubusercontent.com | HTTPS:443 | Este endereço é usado para retirar as políticas incorporadas do GitHub para garantir o correto funcionamento da Política Azure. (atualmente em pré-visualização no AKS) |
| *.gk. \<localização\>.azmk8s.io | HTTPS:443    | O add-on Azure Policy que fala com o ponto final da auditoria gatekeeper a funcionar no servidor principal para obter os resultados da auditoria. |
| dc.services.visualstudio.com | HTTPS:443 | Add-on Azure Policy que envia dados de telemetria para aplicações insights ponto final. |

## <a name="required-by-windows-server-based-nodes-enabled"></a>Exigido por nós baseados no Windows Server ativados

As seguintes regras de aplicação FQDN/aplicação são necessárias para utilizar piscinas de nós baseadas no Windows Server:

| FQDN                                    | Porta      | Utilizar      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net, winlayers.blob.core.windows.net, winlayers.cdn.mscr.io, go.microsoft.com | HTTPS:443 | Para instalar binários relacionados com janelas |
| mp.microsoft.com,<span></span>www.msftconnecttest.com, ctldl.windowsupdate.com | HTTP:80 | Para instalar binários relacionados com janelas |
| kms.core.windows.net | TCP:1688 | Para instalar binários relacionados com janelas |

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu que portas e endereços permitir se você restringir o tráfego de saída para o cluster. Também pode definir como as próprias cápsulas podem comunicar e quais as restrições que têm dentro do cluster. Para obter mais informações, consulte [O tráfego seguro entre cápsulas utilizando políticas][network-policy]de rede no AKS .

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
