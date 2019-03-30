---
title: Migração do Azure Container Service (ACS) para o serviço Kubernetes do Azure (AKS)
description: Migração do Azure Container Service (ACS) para o serviço Kubernetes do Azure (AKS)
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: 910c96988ec0a8b8aa7b6ac8ce287c4fdc59e177
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649973"
---
# <a name="migrating-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Migração do Azure Container Service (ACS) para o serviço Kubernetes do Azure (AKS)

O objetivo deste documento é que o ajudarão a planejar e executar uma migração bem-sucedida entre o Azure Container Service com Kubernetes (ACS) e o Azure Kubernetes Service (AKS). Este guia detalha as diferenças entre o ACS e o AKS, fornece uma descrição geral do processo de migração e deve ajudá-lo a tomar decisões importantes.

## <a name="differences-between-acs-and-aks"></a>Diferenças entre o ACS e o AKS

ACS e o AKS diferem em algumas áreas-chave que têm impacto sobre a migração. Deve rever e planear abordar as seguintes diferenças antes de qualquer migração.

* Utilizam nós do AKS [Managed Disks](../virtual-machines/windows/managed-disks-overview.md)
    * Discos não geridos, terá de ser convertidos antes que eles podem ser anexados a nós do AKS
    * Custom `StorageClass` objetos para os discos do Azure tem de ser alterado de `unmanaged` para `managed`
    * Qualquer `PersistentVolumes` terão de utilizar `kind: Managed`
* Atualmente, o AKS suporta o conjunto de apenas um agente
* Windows baseado em servidor nós estão atualmente nos [pré-visualização privada](https://azure.microsoft.com/blog/kubernetes-on-azure/)
* Verifique a lista de AKS [regiões suportadas](https://docs.microsoft.com/azure/aks/container-service-quotas)
* AKS é um serviço gerido com um plano de controlo alojado do Kubernetes. Poderá ter de modificar as aplicações se anteriormente tiver modificado a configuração do ACS principais

### <a name="differences-between-kubernetes-versions"></a>Diferenças entre as versões do Kubernetes

Se estiver a migrar para uma versão mais recente do Kubernetes (ex: 1.7.x para 1.9.x), existem algumas alterações para a API de k8s que exigirão sua atenção.

* [Migrar uma ThirdPartyResource para CustomResourceDefinition](https://kubernetes.io/docs/tasks/access-kubernetes-api/migrate-third-party-resource/)
* [Alterações de cargas de trabalho API nas versões 1.8 e 1.9](https://kubernetes.io/docs/reference/workloads-18-19/).

## <a name="migration-considerations"></a>Considerações sobre a migração

### <a name="agent-pools"></a>Conjuntos de agentes

Enquanto o AKS gere o plano de controlo do Kubernetes, ainda definem o tamanho e número de nós que pretende incluir no seu cluster novo. Partindo do princípio de que pretende que um mapeamento 1:1 do ACS para o AKS, convém capturar as informações de nó de ACS existentes. Irá utilizar estes dados ao criar o novo cluster do AKS.

Exemplo:

| Name | Contagem | Tamanho da VM | Sistema Operativo |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 | Windows |

Uma vez que outras máquinas virtuais são implementadas na sua subscrição durante a migração, deve verificar que as suas quotas e limites são suficientes para estes recursos. Pode saber mais, revendo [subscrição do Azure e limites do serviço](https://docs.microsoft.com/azure/azure-subscription-service-limits). Para verificar as suas quotas atuais, vá para o [painel de subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure, selecione a sua subscrição, em seguida, selecione `Usage + quotas`.

### <a name="networking"></a>Redes

Para aplicativos complexos, normalmente, irá migrar ao longo do tempo em vez de ao mesmo tempo. Isso significa que os antigos e novos ambientes poderão ter de se comunicar através da rede. Aplicativos que eram anteriormente capazes de usar `ClusterIP` serviços comuniquem poderão ter de ser expostos como tipo `LoadBalancer` e protegidos adequadamente.

Para concluir a migração, desejará apontar os clientes para os novos serviços em execução no AKS. A forma recomendada para redirecionar o tráfego é através da atualização de DNS para apontar para o Balanceador de carga situado à frente do seu cluster do AKS.

### <a name="stateless-applications"></a>Aplicativos sem monitoração de estado

Migração de aplicativos sem monitoração de estado é o caso mais simples. Irá aplicar as definições de YAML para o novo cluster, verificar que tudo o que está a funcionar conforme esperado e redirecionar o tráfego para tornar o novo cluster de Active Directory.

### <a name="stateful-applications"></a>Aplicações com monitorização de estado

Com monitorização de estado de migrar aplicações requer um planejamento cuidadoso para evitar a perda de dados ou tempo de inatividade inesperado.

#### <a name="highly-available-applications"></a>Aplicações de elevada disponibilidade

Algumas aplicações com monitorização de estado podem ser implementadas numa configuração de elevada disponibilidade e podem copiar dados de réplicas. Se isso descreve a sua implementação atual, poderá ser possível criar um novo membro no novo cluster do AKS e migrar com um impacto mínimo para os chamadores de downstream. Em geral são os passos de migração para este cenário:

1. Criar uma nova réplica secundária no AKS
2. Aguarde pela replicação dos dados
3. Falhar mais ao efetuar a réplica secundária a nova principal
4. Aponte o tráfego para o cluster do AKS

#### <a name="migrating-persistent-volumes"></a>Migrar Volumes do persistentes

Há vários fatores a considerar se está a migrar os Volumes existentes persistente AKS. Em geral, os passos envolvidos são:

1. (Opcional) Silenciar escreve para a aplicação (requer o tempo de inatividade)
2. Discos de instantâneo
3. Criar novos discos geridos a partir de instantâneos
4. Criar Volumes persistentes no AKS
5. Atualizar as especificações de Pod para [utilizar volumes existentes](https://docs.microsoft.com/azure/aks/azure-disk-volume) em vez de PersistentVolumeClaims (aprovisionamento estático)
6. Implementar aplicação no AKS
7. Validação
8. Aponte o tráfego para o cluster do AKS

> **Importante**: Se optar por não silenciar escritas, precisará replicar dados para a nova implementação, como os dados que foi escritos desde o instantâneo do disco vai estar em falta

Existem de ferramentas de código aberto que lhe permite criar discos geridos e migrar volumes entre clusters de Kubernetes.

* [noelbundick/azure-cli-disco-extension](https://github.com/noelbundick/azure-cli-disk-copy-extension) - copiar e converta os discos em grupos de recursos e regiões do Azure
* [yaron2/azure-kube-cli](https://github.com/yaron2/azure-kube-cli) - enumerar volumes de Kubernetes no ACS e migrá-los para um cluster do AKS

#### <a name="azure-files"></a>Ficheiros do Azure

Ao contrário de discos, ficheiros do Azure podem ser montados para vários anfitriões em simultâneo. Nem o Azure como o Kubernetes impede de criar um Pod no cluster do AKS que ainda está a ser utilizado pelo cluster do ACS. Para evitar a perda de dados e um comportamento inesperado, deve certificar-se de que ambos os clusters não estão gravando para os mesmos ficheiros ao mesmo tempo.

Se seu aplicativo pode alojar várias réplicas que aponta para a mesma partilha de ficheiros, pode seguir os passos de migração sem monitoração de estado e implementar as definições de YAML para o novo cluster.

Caso contrário, uma abordagem de possíveis de migração envolve os seguintes passos:

1. Implementar a sua aplicação para o AKS com uma contagem de réplicas de 0
2. Dimensionar a aplicação no ACS para 0 (requer o tempo de inatividade)
3. Dimensionar a aplicação no AKS até 1
4. Validação
5. Aponte o tráfego para o cluster do AKS

Em casos em que gostaria de começar com uma partilha de vazia, em seguida, faça uma cópia dos dados de origem, pode utilizar o [ `az storage file copy` ](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) comandos para migrar os seus dados.

### <a name="deployment-strategy"></a>Estratégia de implantação

O método recomendado consiste em utilizar o seu pipeline de CI/CD existente para implementar uma configuração boa conhecida no AKS. Irá clonar as suas tarefas de implementação existentes e certifique-se de que sua `kubeconfig` aponta para o novo cluster do AKS.

Em casos em que isso não possível, terá de exportar a definição do recurso do ACS e, em seguida, aplicá-las para o AKS. Pode usar `kubectl` para exportar os objetos.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Também existem várias ferramentas de código-fonte aberto que podem ajudar, consoante as suas necessidades:

* [heptio/ark](https://github.com/heptio/ark) -requer k8s 1.7
* [yaron2/azure-kube-cli](https://github.com/yaron2/azure-kube-cli)
* [mhausenblas/reshifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>Passos de migração

### <a name="1-create-an-aks-cluster"></a>1. Criar um cluster do AKS (Create an AKS cluster)

Pode seguir os documentos para [criar um cluster do AKS](https://docs.microsoft.com/azure/aks/create-cluster) através do portal do Azure, CLI do Azure ou modelo do Resource Manager.

> Pode encontrar modelos do Azure Resource Manager de exemplo para o AKS com o [do Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) repositório no GitHub

### <a name="2-modify-applications"></a>2. Modificação das aplicações

Fazer quaisquer modificações necessárias para as definições de YAML. Por exemplo: substituindo `apps/v1beta1` com `apps/v1` para `Deployments`

### <a name="3-optional-migrate-volumes"></a>3. (Opcional) Migrar volumes

Migre volumes do seu cluster do ACS para o seu cluster do AKS. Podem encontrar mais detalhes no [migrar Volumes persistente](#migrating-persistent-volumes) secção.

### <a name="4-deploy-applications"></a>4. Implementar aplicações

Utilize o seu sistema de CI/CD para implementar aplicações para o AKS ou utilizar o kubectl para aplicar as definições de YAML.

### <a name="5-validate"></a>5. Validação

Valide que as aplicações estão a funcionar conforme esperado e que todos os dados migrados foi copiados.

### <a name="6-redirect-traffic"></a>6. Redirecionar tráfego

Atualize o DNS para apontar os clientes para a implementação do AKS.

### <a name="7-post-migration-tasks"></a>7. Tarefas pós-migração

Se migrar volumes e não optou por silenciar escritas, terá de copiar esses dados para o novo cluster.
