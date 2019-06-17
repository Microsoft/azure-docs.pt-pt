---
title: Migrar do serviço de contentor do Azure (ACS) para o serviço Kubernetes do Azure (AKS)
description: Migre do serviço de contentor do Azure (ACS) para o serviço Kubernetes do Azure (AKS).
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: dcee8da943603fb0978caf9992be76347ca197d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65977721"
---
# <a name="migrate-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Migrar do serviço de contentor do Azure (ACS) para o serviço Kubernetes do Azure (AKS)

Este artigo ajuda-o a planear e executar uma migração bem-sucedida entre o Azure Container Service (ACS) com o Kubernetes e o Azure Kubernetes Service (AKS). Para ajudar a tomar decisões importantes, este guia detalha as diferenças entre o ACS e o AKS e fornece uma descrição geral do processo de migração.

## <a name="differences-between-acs-and-aks"></a>Diferenças entre o ACS e o AKS

ACS e o AKS diferem em algumas áreas-chave que afetam a migração. Antes de qualquer migração, deve rever e planear abordar as seguintes diferenças:

* Utilizam nós do AKS [discos geridos](../virtual-machines/windows/managed-disks-overview.md).
    * Discos não geridos têm de ser convertidos antes de pode anexá-las a nós do AKS.
    * Custom `StorageClass` objetos para os discos do Azure tem de ser alterados de `unmanaged` para `managed`.
    * Qualquer `PersistentVolumes` deve utilizar `kind: Managed`.
* Suporta do AKS [vários conjuntos de nós](https://docs.microsoft.com/azure/aks/use-multiple-node-pools) (atualmente em pré-visualização).
* Nós com base no Windows Server estão atualmente nos [pré-visualização no AKS](https://azure.microsoft.com/blog/kubernetes-on-azure/).
* AKS suporta um conjunto limitado de [regiões](https://docs.microsoft.com/azure/aks/quotas-skus-regions).
* AKS é um serviço gerido com um plano de controlo alojado do Kubernetes. Poderá ter de modificar as aplicações se modificar a configuração do ACS principais anteriormente.

## <a name="differences-between-kubernetes-versions"></a>Diferenças entre as versões do Kubernetes

Se estiver a migrar para uma versão mais recente do Kubernetes (por exemplo, a partir de 1.7.x para 1.9.x), reveja os seguintes recursos para compreender algumas alterações à API do Kubernetes:

* [Migrar uma ThirdPartyResource para CustomResourceDefinition](https://kubernetes.io/docs/tasks/access-kubernetes-api/migrate-third-party-resource/)
* [Alterações de cargas de trabalho API nas versões 1.8 e 1.9](https://kubernetes.io/docs/reference/workloads-18-19/)

## <a name="migration-considerations"></a>Considerações sobre a migração

### <a name="agent-pools"></a>Conjuntos de agentes

Embora o AKS gere o plano de controlo do Kubernetes, ainda definir o tamanho e número de nós para incluir no seu cluster novo. Partindo do princípio de que pretende que um mapeamento 1:1 do ACS para o AKS, convém capturar as informações de nó de ACS existentes. Utilize estes dados ao criar o novo cluster do AKS.

Exemplo:

| Name | Contagem | Tamanho da VM | Sistema operativo |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 | Windows |

Uma vez que outras máquinas virtuais são implementadas na sua subscrição durante a migração, deve verificar que as suas quotas e limites são suficientes para estes recursos. 

Para obter mais informações, consulte [subscrição do Azure e limites do serviço](https://docs.microsoft.com/azure/azure-subscription-service-limits). Para verificar as suas quotas atuais, no portal do Azure, aceda ao [painel de subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), selecione a sua subscrição e, em seguida, selecione **utilização + quotas**.

### <a name="networking"></a>Redes

Para aplicativos complexos, normalmente, irá migrar ao longo do tempo em vez de ao mesmo tempo. Isso significa que os antigos e novos ambientes poderão ter de se comunicar através da rede. Aplicativos que tenha utilizado `ClusterIP` serviços comuniquem talvez precisem de ser expostos como tipo `LoadBalancer` e ser protegida devidamente.

Para concluir a migração, desejará apontar os clientes para os novos serviços que estão em execução no AKS. Recomendamos que redirecionar o tráfego através da atualização de DNS para apontar para o Balanceador de carga situado à frente do seu cluster do AKS.

### <a name="stateless-applications"></a>Aplicativos sem monitoração de estado

Migração de aplicativos sem monitoração de estado é o caso mais simples. Irá aplicar as definições de YAML para o novo cluster, certifique-se de que tudo funciona conforme esperado e redirecionar o tráfego para ativar o novo cluster.

### <a name="stateful-applications"></a>Aplicações com monitorização de estado

Planeie cuidadosamente a migração de aplicações com monitorização de estado para evitar a perda de dados ou tempo de inatividade inesperado.

#### <a name="highly-available-applications"></a>Aplicações de elevada disponibilidade

Pode implementar alguns aplicativos com monitoração de estado numa configuração de elevada disponibilidade. Esses aplicativos podem copiar dados de réplicas. Se utilizar atualmente a esse tipo de implementação, poderá criar um novo membro no novo cluster do AKS e, em seguida, migrar com o mínimo efeito sobre os autores de chamadas de downstream. Em geral, são os passos de migração para este cenário:

1. Crie uma nova réplica secundária no AKS.
2. Aguarde pela replicação dos dados.
3. Falhe mais tornar uma réplica secundária a nova principal.
4. Aponte o tráfego para o cluster do AKS.

#### <a name="migrating-persistent-volumes"></a>Migrar volumes do persistentes

Se estiver a migrar volumes persistentes existentes para o AKS, geralmente executar os seguintes passos:

1. Silenciar escreve para a aplicação. (Este passo é opcional e exige período de indisponibilidade.)
2. Criar instantâneos de discos.
3. Crie novos discos geridos a partir de instantâneos.
4. Crie volumes persistentes no AKS.
5. Atualizar as especificações de pod para [utilizar volumes existentes](https://docs.microsoft.com/azure/aks/azure-disk-volume) em vez de PersistentVolumeClaims (aprovisionamento estático).
6. Implemente a aplicação no AKS.
7. Valide.
8. Aponte o tráfego para o cluster do AKS.

> [!IMPORTANT]
> Se optar por não silenciar escritas, terá de replicar os dados para a nova implementação. Caso contrário, perderá os dados que foi escritos depois que os instantâneos de disco de realizar.

Algumas ferramentas de código-fonte aberto podem ajudá-lo a criar discos geridos e migrar volumes entre clusters de Kubernetes:

* [Extensão de cópia de disco da CLI do Azure](https://github.com/noelbundick/azure-cli-disk-copy-extension) copia e converte os discos em grupos de recursos e regiões do Azure.
* [A extensão do Azure Kube CLI](https://github.com/yaron2/azure-kube-cli) enumera os volumes de Kubernetes no ACS e migra para um cluster do AKS.

#### <a name="azure-files"></a>Ficheiros do Azure

Ao contrário de discos, ficheiros do Azure podem ser montados para vários anfitriões em simultâneo. No cluster do AKS, Azure e o Kubernetes não impedem que crie um pod que ainda utiliza o seu cluster do ACS. Para evitar a perda de dados e um comportamento inesperado, certifique-se de que os clusters não são gravadas os mesmos ficheiros ao mesmo tempo.

Se seu aplicativo pode alojar várias réplicas que apontam para a mesma partilha de ficheiros, siga os passos de migração sem monitoração de estado e implementar as definições de YAML para o novo cluster. Caso contrário, uma abordagem de possíveis de migração envolve os seguintes passos:

1. Implemente a sua aplicação para o AKS com uma contagem de réplicas de 0.
2. Expanda a aplicação no ACS para 0. (Este passo requer o tempo de inatividade.)
3. Expanda a aplicação no AKS até 1.
4. Valide.
5. Aponte o tráfego para o cluster do AKS.

Se pretender começar com uma partilha vazia e faça uma cópia dos dados de origem, pode utilizar o [ `az storage file copy` ](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) comandos para migrar os seus dados.

### <a name="deployment-strategy"></a>Estratégia de implantação

Recomendamos que utilize o seu pipeline de CI/CD existente para implementar uma configuração boa conhecida no AKS. Clonar as suas tarefas de implementação existentes e certifique-se de que `kubeconfig` aponta para o novo cluster do AKS.

Se não for possível, exportar definições de recursos do ACS e, em seguida, aplicá-las para o AKS. Pode usar `kubectl` para exportar os objetos.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Várias ferramentas de código-fonte aberto podem ajudar, consoante as suas necessidades de implementação:

* [Velero](https://github.com/heptio/ark) (esta ferramenta requer Kubernetes 1.7.)
* [Extensão do Kube CLI do Azure](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>Passos de migração

1. [Criar um cluster do AKS](https://docs.microsoft.com/azure/aks/create-cluster) através do portal do Azure, CLI do Azure ou o modelo Azure Resource Manager.

   > [!NOTE]
   > Localizar modelos do Azure Resource Manager de exemplo para o AKS com o [do Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) repositório no GitHub.

2. Faça as alterações necessárias para as definições de YAML. Por exemplo, substitua `apps/v1beta1` com `apps/v1` para `Deployments`.

3. [Migrar volumes](#migrating-persistent-volumes) (opcional) do seu cluster de ACS de ao seu cluster do AKS.

4. Utilize o seu sistema de CI/CD para implementar aplicações em AKS. Ou utilizar o kubectl para aplicar as definições de YAML.

5. Valide. Certifique-se de que seus aplicativos funcionam conforme esperado e que todos os dados migrados foi copiados.

6. Redirecione o tráfego. Atualize o DNS para apontar os clientes para a implementação do AKS.

7. Tarefas pós-migração ser concluída. Se migrar volumes e não optou por silenciar escritas, copie esses dados para o novo cluster.
