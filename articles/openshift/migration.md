---
title: Migrar do Azure Red Hat OpenShift 3.11 para o Azure Red Hat OpenShift 4
description: Migrar do Azure Red Hat OpenShift 3.11 para o Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 08/13/2020
keywords: migração, aro, openshift, chapéu vermelho
ms.openlocfilehash: f9bfc924581d5dbe33c7c2683a0f6083cb2abc23
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071040"
---
# <a name="migrate-from-azure-red-hat-openshift-311-to-azure-red-hat-openshift-4"></a>Migrar do Azure Red Hat OpenShift 3.11 para Azure Red Hat OpenShift 4

O Azure Red Hat OpenShift no OpenShift 4 traz Kubernetes 1.16 no Red Hat Core OS, clusters privados, traz o seu próprio suporte de rede virtual e papel de administração de cluster completo. Além disso, muitas novas funcionalidades estão agora disponíveis, tais como o suporte para o quadro do operador, o Hub do Operador e a Malha de Serviço OpenShift.

Para a transição com sucesso do Azure Red Hat OpenShift 3.11 para O Azure Red Hat OpenShift 4, certifique-se de rever as [diferenças de armazenamento, networking, registo, segurança e monitorização](https://docs.openshift.com/container-platform/4.4/migration/migrating_3_4/planning-migration-3-to-4.html).

Neste artigo, vamos demonstrar como migrar de um aglomerado Azure Red Hat OpenShift 3.11 para um cluster Azure Red Hat 4.

> [!NOTE]
> As ferramentas de migração OpenShift do chapéu vermelho, tais como a Ferramenta de Assistência à Migração do Plano de Controlo e a Ferramenta de Migração da Aplicação de Cluster (CAM) não podem ser utilizadas com clusters Azure Red Hat OpenShift 3.11.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tem um cluster Azure Red Hat OpenShift 3.11.

## <a name="create-a-target-azure-red-hat-openshift-4-cluster"></a>Crie um cluster Azure Red Hat OpenShift 4

Em primeiro lugar, [crie o cluster Azure Red Hat OpenShift 4](tutorial-create-cluster.md) que gostaria de usar como cluster alvo. Aqui, usaremos a configuração básica. Se estiver interessado em diferentes configurações, consulte o [tutorial do Agrupamento Create azure Red Hat OpenShift 4](tutorial-create-cluster.md).

Crie uma rede virtual com duas sub-redes vazias para os nós do mestre e do trabalhador.

```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
```

Em seguida, use o seguinte comando para criar o cluster.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

## <a name="configure-the-target-openshift-4-cluster"></a>Configure o cluster openshift 4 alvo

### <a name="authentication"></a>Autenticação

Para que os utilizadores interajam com o Azure Red Hat OpenShift, devem primeiro autenticar-se no cluster. A camada de autenticação identifica o utilizador associado a pedidos para a API AZURE Red Hat OpenShift. A camada de autorização utiliza então informações sobre o utilizador que solicita para determinar se o pedido é permitido.

Quando um cluster Azure Red Hat OpenShift 4 é criado, é criado um utilizador administrativo temporário. [Conecte-se ao seu cluster,](tutorial-connect-cluster.md)adicione utilizadores e grupos e [configufique as permissões apropriadas](https://docs.openshift.com/container-platform/4.6/authentication/understanding-authentication.html) para ambos.

### <a name="networking"></a>Rede

O Azure Red Hat OpenShift 4 utiliza alguns operadores diferentes para configurar a rede no seu cluster: [Cluster Network Operator,](https://docs.openshift.com/container-platform/4.6/networking/cluster-network-operator.html#nw-cluster-network-operator_cluster-network-operator) [DNS Operator](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html), e o Operador de [Entrada](https://docs.openshift.com/container-platform/4.6/networking/ingress-operator.html). Para obter mais informações sobre a criação de redes num cluster Azure Red Hat OpenShift 4, consulte o [Diagrama de Rede](concepts-networking.md) e [a Rede de Compreensão](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html).

### <a name="storage"></a>Armazenamento
O Azure Red Hat OpenShift 4 suporta os seguintes plug-ins PersistentVolume:

- Loja de blocos elásticos AWS (EBS)
- Disco Azure
- Arquivo Azure
- Disco persistente da GCE
- HostPath
- iSCSI
- Volume local
- NFS
- Armazenamento de recipiente aberto de chapéu vermelho

Para obter informações sobre a configuração destes tipos de armazenamento, consulte [configurar o armazenamento persistente](https://access.redhat.com/documentation/azure_red_hat_openshift/4/html/storage/configuring-persistent-storage).

### <a name="registry"></a>Registo

O Azure Red Hat OpenShift 4 pode construir imagens a partir do seu código fonte, implantá-las e gerir o seu ciclo de vida. Para o permitir isto, o Azure Red Hat OpenShift fornece 4 um [registo de imagem de contentor integrado e interno](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html) que pode ser implantado no seu ambiente Azure Red Hat OpenShift para gerir localmente imagens.

Se estiver a utilizar registos externos como [registo de contentores Azure,](../container-registry/index.yml) [registos red hat quay,](ttps://docs.openshift.com/container-platform/4.6/registry/registry-options.html#registry-quay-overview_registry-options)ou uma [autenticação ativada pelo registo do Chapéu Vermelho,](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html#registry-authentication-enabled-registry-overview_registry-options)siga as medidas para fornecer credenciais ao cluster para permitir que o cluster aceda aos repositórios.

### <a name="monitoring"></a>Monitorização

O Azure Red Hat OpenShift inclui uma pilha de monitorização pré-configurada, pré-instalada e auto-actualizada que se baseia no projeto Prometheus open source e no seu ecossistema mais amplo. Fornece monitorização dos componentes do cluster e inclui um conjunto de alertas para notificar imediatamente o administrador do cluster sobre quaisquer problemas que ocorram e um conjunto de dashboards Grafana. A pilha de monitorização do cluster só é suportada para monitorizar os clusters Azure Red Hat OpenShift. Para obter mais informações, consulte [a monitorização do Cluster para O Azure Red Hat OpenShift](https://docs.openshift.com/container-platform/4.6/monitoring/understanding-the-monitoring-stack.html).

Se tiver usado [o Azure Monitor para contentores para o Azure Red Hat OpenShift 3.11,](../azure-monitor/insights/container-insights-azure-redhat-setup.md)também pode ativar o Monitor Azure para contentores para [aglomerados Azure Red Hat OpenShift 4](../azure-monitor/insights/container-insights-azure-redhat4-setup.md) e continuar a utilizar o mesmo espaço de trabalho Log Analytics.

## <a name="move-your-dns-or-load-balancer-configuration-to-the-new-cluster"></a>Mova a sua configuração DE DNS ou balanceador de carga para o novo cluster

Se estiver a utilizar o Azure Traffic Manager, adicione pontos finais para consultar o seu cluster alvo e priorize estes pontos finais.

## <a name="deploy-application-to-your-target-cluster"></a>Implementar aplicação para o seu cluster alvo

Assim que tiver o seu cluster alvo devidamente configurado para a sua carga de trabalho, [conecte-se ao seu cluster](tutorial-connect-cluster.md) e crie as aplicações, componentes ou serviços necessários para os seus projetos. O Azure Red Hat OpenShift permite-lhe criá-las a partir de Git, imagens de contentores, o Catálogo de Desenvolvedores de Chapéus Vermelhos, um Dockerfile, uma definição YAML/JSON, ou selecionando um serviço de base de dados do Catálogo.

## <a name="delete-your-source-cluster"></a>Elimine o seu cluster de origem
Depois de confirmar que o seu cluster Azure Red Hat OpenShift 4 está devidamente configurado, elimine o seu cluster Azure Red Hat OpenShift 3.11.

```
az openshift delete --name $CLUSTER_NAME
                    --resource-group $RESOURCE_GROUP
                    [--no-wait]
                    [--subscription]
                    [--yes]
```
## <a name="next-steps"></a>Passos seguintes
Consulte [aqui](https://docs.openshift.com/container-platform/4.6/welcome/index.html)a documentação red hat openshift.