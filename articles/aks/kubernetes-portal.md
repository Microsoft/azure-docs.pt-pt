---
title: Aceder aos recursos da Kubernetes a partir do portal Azure
description: Saiba como interagir com os recursos da Kubernetes para gerir um cluster Azure Kubernetes Service (AKS) a partir do portal Azure.
services: container-service
ms.topic: article
ms.date: 12/09/2020
ms.openlocfilehash: 8e31c41573ced403a034999de71a5595a54281df
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96921593"
---
# <a name="access-kubernetes-resources-from-the-azure-portal"></a>Aceder aos recursos da Kubernetes a partir do portal Azure

O portal Azure inclui uma vista de recursos de Kubernetes para facilitar o acesso aos recursos de Kubernetes no seu cluster Azure Kubernetes Service (AKS). Visualizar recursos de Kubernetes a partir do portal Azure reduz a troca de contexto entre o portal Azure e a `kubectl` ferramenta de linha de comando, racionalizando a experiência para visualizar e editar os recursos de Kubernetes. O visualizador de recursos inclui atualmente vários tipos de recursos, tais como implementações, cápsulas e conjuntos de réplicas.

A visão de recurso kubernetes do portal Azure substitui o addon do [dashboard AKS][kubernetes-dashboard], que foi depreciado.

## <a name="prerequisites"></a>Pré-requisitos

Para ver os recursos de Kubernetes no portal Azure, precisa de um cluster AKS. Qualquer cluster é suportado, mas se utilizar a integração do Azure Ative Directory (Azure AD), o seu cluster deve utilizar [a integração Azure AD gerida pela AKS.][aks-managed-aad] Se o seu cluster utilizar o legado Azure AD, pode atualizar o seu cluster no portal ou com o [Azure CLI][cli-aad-upgrade].

## <a name="view-kubernetes-resources"></a>Ver recursos de Kubernetes

Para ver os recursos de Kubernetes, navegue para o seu cluster AKS no portal Azure. O painel de navegação à esquerda é utilizado para aceder aos seus recursos. Os recursos incluem:

- **Os espaços de** nome exibem os espaços de nome do seu cluster. O filtro no topo da lista de espaços de nome fornece uma forma rápida de filtrar e exibir os recursos do seu espaço de identificação.
- **As cargas de trabalho** mostram informações sobre implementações, cápsulas, conjuntos de réplicas e conjuntos de daemon implantados no seu cluster. A imagem abaixo mostra as cápsulas de sistema predefinidos num cluster AKS exemplo.
- **Serviços e ingressess** mostram todo o serviço do seu cluster e recursos ingresss.

:::image type="content" source="media/kubernetes-portal/workloads.png" alt-text="Informações do pod kubernetes exibidas no portal Azure." lightbox="media/kubernetes-portal/workloads.png":::

### <a name="deploy-an-application"></a>Implementar uma aplicação

Neste exemplo, usaremos o nosso cluster AKS de amostra para implementar a aplicação Azure Vote a partir do [quickstart AKS][portal-quickstart].

1. **Selecione Adicionar** a partir de qualquer uma das vistas de recursos (Espaço de nome, cargas de trabalho ou serviços e ingresses).
1. Cole o YAML para o pedido de voto Azure do [quickstart AKS][portal-quickstart].
1. **Selecione Adicionar** na parte inferior do editor YAML para implementar a aplicação. 

Uma vez adicionado o ficheiro YAML, o visualizador de recursos mostra ambos os serviços Kubernetes que foram criados: o serviço interno (azure-vote-back) e o serviço externo (azure-vote-front) para aceder à aplicação Azure Vote. O serviço externo inclui um endereço IP externo ligado para que possa facilmente ver a aplicação no seu navegador.

:::image type="content" source="media/kubernetes-portal/portal-services.png" alt-text="Informações sobre a aplicação do Voto Azure apresentadas no portal Azure." lightbox="media/kubernetes-portal/portal-services.png":::

### <a name="monitor-deployment-insights"></a>Monitorar insights de implementação

Os agrupamentos AKS com [monitor Azure para recipientes][enable-monitor] ativados podem visualizar rapidamente os insights de implantação. A partir da visão de recursos de Kubernetes, os utilizadores podem ver o estado ao vivo de implementações individuais, incluindo CPU e uso de memória, bem como a transição para o monitor Azure para obter informações mais aprofundadas. Aqui está um exemplo de insights de implantação de um cluster AKS de amostra:

:::image type="content" source="media/kubernetes-portal/deployment-insights.png" alt-text="Insights de implementação apresentados no portal Azure." lightbox="media/kubernetes-portal/deployment-insights.png":::

## <a name="edit-yaml"></a>Editar YAML

A vista de recursos de Kubernetes também inclui um editor YAML. Um editor YAML incorporado significa que pode atualizar ou criar serviços e implementações a partir do portal e aplicar alterações imediatamente.

:::image type="content" source="media/kubernetes-portal/service-editor.png" alt-text="Editor yaml para um serviço Kubernetes exibido no portal Azure.":::

Após a edição do YAML, as alterações são aplicadas selecionando **Review + save**, confirmando as alterações e, em seguida, economizando novamente.

>[!WARNING]
> Não é aconselhável realizar alterações de produção diretas via UI ou CLI, deve aproveitar as [melhores práticas de integração contínua (CI) e de implantação contínua (CD).](kubernetes-action.md) As capacidades de gestão do Azure Portal Kubernetes e o editor yaML são construídos para aprender e voar novas implementações num ambiente de desenvolvimento e teste.

## <a name="troubleshooting"></a>Resolução de problemas

Esta secção aborda problemas comuns e etapas de resolução de problemas.

### <a name="unauthorized-access"></a>Acesso não autorizado

Para aceder aos recursos de Kubernetes, você deve ter acesso ao cluster AKS, à API de Kubernetes e aos objetos Kubernetes. Certifique-se de que é um administrador de cluster ou um utilizador com as permissões adequadas para aceder ao cluster AKS. Para obter mais informações sobre a segurança do cluster, consulte [opções de acesso e identidade para AKS.][concepts-identity]

>[!NOTE]
> A visão de recurso de kubernetes no Portal Azure é suportada apenas por [clusters geridos](managed-aad.md) por AAD ou clusters não-AAD habilitados. Se estiver a utilizar um cluster ativado por AAD gerido, o utilizador ou identidade da AAD necessita de ter as respetivas funções/encadernações de funções para aceder à API dos kubernetes, além da permissão para puxar o [utilizador `kubeconfig` ](control-kubeconfig-access.md).

### <a name="enable-resource-view"></a>Ativar a vista de recursos

Para os clusters existentes, poderá ser necessário ativar a vista de recursos de Kubernetes. Para ativar a visualização do recurso, siga as indicações no portal para o seu cluster.

:::image type="content" source="media/kubernetes-portal/enable-resource-view.png" alt-text="Mensagem do portal Azure para ativar a visualização do recurso Kubernetes." lightbox="media/kubernetes-portal/enable-resource-view.png":::

> [!TIP]
> A funcionalidade AKS para [**gamas IP autorizadas pelo servidor API**](api-server-authorized-ip-ranges.md) pode ser adicionada para limitar o acesso do servidor API apenas ao ponto final público da firewall. Outra opção para estes clusters é a atualização `--api-server-authorized-ip-ranges` para incluir o acesso a um computador cliente local ou gama de endereços IP (a partir do qual o portal está a ser navegado). Para permitir este acesso, precisa do endereço IPv4 público do computador. Pode encontrar este endereço com o comando abaixo ou pesquisando "qual é o meu endereço IP" num navegador de Internet.
```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

## <a name="next-steps"></a>Passos seguintes

Este artigo mostrou-lhe como aceder aos recursos da Kubernetes para o seu cluster AKS. Consulte [as Implementações e os manifestos yaml][deployments] para uma compreensão mais profunda dos recursos do cluster e dos ficheiros YAML que são acedidos com o visualizador de recursos Kubernetes.

<!-- LINKS - internal -->
[kubernetes-dashboard]: kubernetes-dashboard.md
[concepts-identity]: concepts-identity.md
[portal-quickstart]: kubernetes-walkthrough-portal.md#run-the-application
[deployments]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[aks-managed-aad]: managed-aad.md
[cli-aad-upgrade]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
[enable-monitor]: ../azure-monitor/insights/container-insights-enable-existing-clusters.md