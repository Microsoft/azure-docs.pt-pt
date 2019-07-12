---
title: Introdução ao Serviço Kubernetes do Azure
description: Conheça as funcionalidades e os benefícios do Azure Kubernetes Service para implementar e gerir aplicações baseadas em contentores no Azure.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: overview
ms.date: 05/06/2019
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 5515951a9bde596935f4c603ffd9e088f74dee45
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615167"
---
# <a name="azure-kubernetes-service-aks"></a>Serviço de Kubernetes do Azure (AKS)

O Azure Kubernetes Service (AKS) torna fácil implementar um cluster Kubernetes gerido no Azure. O AKS reduz a complexidade e a sobrecarga operacional de gerir o Kubernetes ao descarregar grande parte dessa responsabilidade para o Azure. Enquanto serviço alojado do Kubernetes, o Azure lida com tarefas críticas para si, como a monitorização do estado de funcionamento e a manutenção. Os mestres do Kubernetes são geridos pelo Azure. Pode apenas gerir e manter os nós dos agentes. Como serviço Kubernetes gerido, o AKS é gratuito, paga apenas os nós dos agentes dentro dos seus clusters, não paga os mestres.

Pode criar um cluster do AKS no portal do Azure, com a CLI do Azure, ou um modelo controlado por opções de implementação, tais como modelos do Resource Manager e o Terraform. Ao implementar um cluster do AKS, o mestre do Kubernetes e todos os nós são implementados e configurados para si. As funcionalidades adicionais, como redes avançadas, integração do Azure Active Directory e monitorização também podem ser configuradas durante o processo de implementação. Suporte de contentores do Windows Server está atualmente em pré-visualização no AKS.

Para obter mais informações sobre noções básicas do Kubernetes, consulte [Kubernetes principais conceitos para o AKS][concepts-clusters-workloads].

Para começar a utilizar, conclua o guia de introdução do AKS [no portal do Azure][aks-portal] or [with the Azure CLI][aks-cli].

## <a name="access-security-and-monitoring"></a>Acesso, segurança e monitorização

Para maior segurança e gestão, o AKS permite a integração com o Azure Active Directory e a utilização dos controlos de acesso baseados em funções do Kubernetes. Também pode monitorizar o estado de funcionamento do seu cluster e recursos.

### <a name="identity-and-security-management"></a>Gestão de identidade e segurança

Para limitar o acesso aos recursos do cluster, o AKS suporta [aceder a Kubernetes baseada em funções (RBAC) do controle][kubernetes-rbac]. O RBAC permite-lhe controlar o acesso aos recursos e aos espaços de nomes do Kubernetes, e as permissões para esses recursos. Também pode configurar um cluster do AKS para integração com o Azure Active Directory (AD). Com a integração do Microsoft Azure AD, o acesso ao Kubernetes pode ser configurado com base na identidade e na associação de grupos existentes. Os seus utilizadores do Microsoft Azure AD e grupos existentes podem obter acesso aos recursos AKS com uma experiência de início de sessão integrada.

Para obter mais informações sobre identidade, consulte [opções de acesso e identidade para o AKS][concepts-identity].

Para proteger os seus clusters do AKS, consulte [integrar o Azure Active Directory com o AKS][aks-aad].

### <a name="integrated-logging-and-monitoring"></a>Registo e monitorização integrados

Para compreender o desempenho do cluster do AKS e das aplicações implementadas, o Azure Monitor do estado de funcionamento do contentor recolhe as métricas de memória e do processador a partir de contentores, nós e controladores. Registos de contentor estão disponíveis e também pode [rever os registos de mestres de Kubernetes][aks-master-logs]. Estes dados de monitorização são armazenados numa área de trabalho do Log Analytics do Azure e estão disponíveis através do portal do Azure, da CLI do Azure ou de um ponto final REST.

Para obter mais informações, consulte [estado de funcionamento do Monitor do Azure Kubernetes Service contentor][container-health].

## <a name="clusters-and-nodes"></a>Clusters e nós

Os nós AKS são executados nas máquinas virtuais do Azure. Pode ligar o armazenamento aos nós e aos pods, atualizar os componentes de cluster e utilizar GPUs. AKS suporta clusters do Kubernetes que são executados vários conjuntos de nós para dar suporte a sistemas de operativos mistos e contentores do Windows Server (atualmente em pré-visualização). Nós do Linux executam uma imagem personalizada do sistema operacional de Ubuntu e nós do servidor Windows executam uma imagem personalizada do SO do Windows Server de 2019.

### <a name="cluster-node-and-pod-scaling"></a>Dimensionamento dos pods e dos nós do cluster

À medida que a procura dos recursos se altera, o número de pods ou nós do cluster que executam os serviços pode aumentar ou reduzir verticalmente de forma automática. Pode utilizar o dimensionamento automático horizontal do pod ou o dimensionamento automático do cluster. Esta abordagem do dimensionamento permite que o cluster do AKS se ajuste automaticamente às exigências e execute apenas os recursos necessários.

Para obter mais informações, consulte [dimensionar um cluster do Azure Kubernetes Service (AKS)][aks-scale].

### <a name="cluster-node-upgrades"></a>Atualizações do nó de cluster

O Azure Kubernetes Service oferece várias versões de Kubernetes. À medida que novas versões ficam disponíveis no AKS, a versão do seu cluster pode ser atualizada através do portal do Azure ou da CLI do Azure. Durante o processo de atualização, os nós são cuidadosamente isolados e drenados para minimizar a interrupção das aplicações em execução.

Para saber mais sobre as versões do ciclo de vida, veja [versões suportadas Kubernetes no AKS][aks-supported versions]. For steps on how to upgrade, see [Upgrade an Azure Kubernetes Service (AKS) cluster][aks-upgrade].

### <a name="gpu-enabled-nodes"></a>Nós de GPU ativados

O AKS suporta a criação de conjuntos de nós ativados por GPU. O Azure oferece atualmente VMs ativadas por GPU, únicas ou várias. As VMs ativadas por GPU foram concebidas para cargas de trabalho de computação intensiva, gráficos intensivos e visualização.

Para obter mais informações, consulte [utilizar GPUs no AKS][aks-gpu].

### <a name="storage-volume-support"></a>Suporte para volume de armazenamento

Para suportar cargas de trabalho da aplicação, pode montar volumes de armazenamento para dados persistentes. Podem ser utilizados volumes estáticos e dinâmicos. Consoante o número de pods ligados que irão partilhar o armazenamento, pode utilizar o armazenamento suportado pelos Discos do Azure para acesso único ao pod ou pelos Ficheiros do Azure para vários acessos ao pod em simultâneo.

Para obter mais informações, consulte [opções de armazenamento para aplicações no AKS][concepts-storage].

Comece com volumes dinâmicos de persistentes usando [discos do Azure][azure-disk] or [Azure Files][azure-files].

## <a name="virtual-networks-and-ingress"></a>Redes virtuais e entrada

Um cluster do AKS pode ser implementado numa rede virtual existente. Nesta configuração, a cada pod do cluster é atribuído um endereço IP na rede virtual, e pode comunicar diretamente com outros pods no cluster e outros nós na rede virtual. Os pods também podem ligar a outros serviços numa rede virtual em modo de peering e a redes no local através do ExpressRoute ou ligações de rede de VPNs.

Para obter mais informações, consulte a [conceitos de aplicativos no AKS de rede][aks-networking].

Para começar a utilizar com o tráfego de entrada, consulte o artigo [encaminhamento de aplicações de HTTP][aks-http-routing].

### <a name="ingress-with-http-application-routing"></a>Entrar com encaminhamento de aplicações de HTTP

O suplemento de encaminhamento de aplicações de HTTP facilita o acesso às aplicações implementadas no cluster do AKS. Quando ativada, a solução de encaminhamento de aplicação de HTTP configura um controlador de entradas no seu cluster AKS. À medida que as aplicações são implementadas, os nomes DNS publicamente acessíveis são configurados automaticamente. O encaminhamento de aplicações de HTTP configura uma zona DNS e integra-a com o cluster do AKS. Em seguida, pode implementar os recursos de entrada do Kubernetes como habitualmente.

Para começar a utilizar com o tráfego de entrada, consulte o artigo [encaminhamento de aplicações de HTTP][aks-http-routing].

## <a name="development-tooling-integration"></a>Integração de ferramentas de desenvolvimento

O Kubernetes oferece um ecossistema rico de ferramentas de desenvolvimento e gestão, como o Helm, Draft e a extensão do Kubernetes para o Visual Studio Code. Essas ferramentas funcionam perfeitamente com o AKS.

Adicionalmente, os Espaços de Programador do Azure oferecem uma experiência de desenvolvimento do Kubernetes rápida e iterativa para equipas. Com a configuração mínima, pode executar e depurar contentores diretamente no AKS. Para começar a utilizar, veja [do Azure Dev espaços][azure-dev-spaces].

O projeto Azure DevOps fornece uma solução simples para trazer código existente e o repositório Git para o Azure. O projeto DevOps cria automaticamente os recursos do Azure, como o AKS, um pipeline de lançamento no Azure DevOps Services, que inclui um pipeline de compilação para CI, configura um pipeline de lançamento para CD e, em seguida, cria um recurso do Azure Application Insights para monitorização.

Para obter mais informações, consulte [projeto Azure DevOps][azure-devops].

## <a name="docker-image-support-and-private-container-registry"></a>Suporte de imagem do Docker e registo de contentor privado

O AKS suporta o formato de imagem do Docker. Para o armazenamento privado das suas imagens do Docker, pode integrar AKS com o Azure Container Registry (ACR).

Para criar o arquivo de imagem privada, veja [Azure Container Registry][acr-docs].

## <a name="kubernetes-certification"></a>Certificação Kubernetes

O Azure Kubernetes Service (AKS) foi certificado com CNCF como sendo compatível com o Kubernetes.

## <a name="regulatory-compliance"></a>Conformidade regulamentar

O Azure Kubernetes Service (AKS) está em conformidade com SOC, ISO, PCI DSS e HIPAA. Para obter mais informações, consulte [conformidade de descrição geral do Microsoft Azure][compliance-doc].

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre como implementar e gerir o AKS com o guia de introdução da CLI do Azure.

> [!div class="nextstepaction"]
> [Início rápido do AKS][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./azure-ad-integration.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/azure-dev-spaces
[azure-devops]: https://docs.microsoft.com/azure/devops-project/overview
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../monitoring/monitoring-container-health.md
[aks-master-logs]: view-master-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
