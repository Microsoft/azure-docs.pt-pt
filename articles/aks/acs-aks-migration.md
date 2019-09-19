---
title: Migrar do serviço de contêiner do Azure (ACS) para o serviço kubernetes do Azure (AKS)
description: Migre do serviço de contêiner do Azure (ACS) para o serviço kubernetes do Azure (AKS).
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: 66f76a8a706f60df786786cbd1ce00b7eafd8d7e
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097889"
---
# <a name="migrate-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Migrar do serviço de contêiner do Azure (ACS) para o serviço kubernetes do Azure (AKS)

Este artigo ajuda você a planejar e executar uma migração bem-sucedida entre o ACS (serviço de contêiner do Azure) com o kubernetes e o AKS (serviço kubernetes do Azure). Para ajudá-lo a tomar decisões importantes, este guia detalha as diferenças entre o ACS e o AKS e fornece uma visão geral do processo de migração.

## <a name="differences-between-acs-and-aks"></a>Diferenças entre o ACS e o AKS

O ACS e o AKS diferem em algumas áreas importantes que afetam a migração. Antes de qualquer migração, você deve examinar e planejar para resolver as seguintes diferenças:

* Os nós AKS usam [Managed disks](../virtual-machines/windows/managed-disks-overview.md).
    * Discos não gerenciados devem ser convertidos antes que você possa anexá-los a nós AKS.
    * Os `StorageClass` objetos personalizados para discos do Azure devem ser `unmanaged` alterados `managed`de para.
    * Qualquer `PersistentVolumes` um deve `kind: Managed`usar.
* O AKS dá suporte a [vários pools de nó](https://docs.microsoft.com/azure/aks/use-multiple-node-pools) (atualmente em visualização).
* Os nós baseados no Windows Server estão atualmente em [Visualização no AKs](https://azure.microsoft.com/blog/kubernetes-on-azure/).
* O AKS dá suporte a um conjunto limitado de [regiões](https://docs.microsoft.com/azure/aks/quotas-skus-regions).
* AKS é um serviço gerenciado com um plano de controle kubernetes hospedado. Talvez seja necessário modificar seus aplicativos se você tiver modificado anteriormente a configuração de seus mestres do ACS.

## <a name="differences-between-kubernetes-versions"></a>Diferenças entre as versões do kubernetes

Se você estiver migrando para uma versão mais recente do kubernetes, examine os seguintes recursos para entender as estratégias de controle de versão do kubernetes:

* [Versão do kubernetes e política de suporte de distorção de versão](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)

## <a name="migration-considerations"></a>Considerações sobre a migração

### <a name="agent-pools"></a>Agrupamentos de agentes

Embora o AKS gerencie o plano de controle kubernetes, você ainda define o tamanho e o número de nós a serem incluídos no novo cluster. Supondo que você queira um mapeamento 1:1 do ACS para o AKS, você desejará capturar as informações existentes do nó do ACS. Use esses dados quando você criar o novo cluster AKS.

Exemplo:

| Name | Count | Tamanho da VM | Sistema operativo |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 | Windows |

Como máquinas virtuais adicionais serão implantadas em sua assinatura durante a migração, você deve verificar se suas cotas e limites são suficientes para esses recursos. 

Para obter mais informações, consulte [limites de serviço e assinatura do Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits). Para verificar suas cotas atuais, na portal do Azure, vá para a [folha assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), selecione sua assinatura e, em seguida, selecione **uso + cotas**.

### <a name="networking"></a>Redes

Para aplicativos complexos, normalmente você migrará ao longo do tempo em vez de todos de uma vez. Isso significa que os ambientes novos e antigos podem precisar se comunicar pela rede. Os aplicativos que usaram `ClusterIP` anteriormente serviços para comunicação podem precisar ser expostos como tipo `LoadBalancer` e ser protegidos adequadamente.

Para concluir a migração, você desejará apontar os clientes para os novos serviços em execução no AKS. Recomendamos que você redirecione o tráfego atualizando o DNS para apontar para o Load Balancer que fica na frente do cluster AKS.

### <a name="stateless-applications"></a>Aplicativos sem estado

A migração de aplicativos sem estado é o caso mais direto. Você aplicará suas definições de YAML ao novo cluster, verificará se tudo funciona conforme o esperado e redirecionará o tráfego para ativar o novo cluster.

### <a name="stateful-applications"></a>Aplicativos com estado

Planeje cuidadosamente a migração de aplicativos com estado para evitar perda de dados ou tempo de inatividade inesperado.

#### <a name="highly-available-applications"></a>Aplicativos altamente disponíveis

Você pode implantar alguns aplicativos com estado em uma configuração de alta disponibilidade. Esses aplicativos podem copiar dados entre réplicas. Se você usar esse tipo de implantação no momento, poderá criar um novo membro no novo cluster AKS e, em seguida, migrar com um efeito mínimo sobre os chamadores downstream. Em geral, as etapas de migração para esse cenário são:

1. Crie uma nova réplica secundária em AKS.
2. Aguarde até que os dados sejam replicados.
3. Faça failover para tornar uma réplica secundária a nova primária.
4. Aponte o tráfego para o cluster AKS.

#### <a name="migrating-persistent-volumes"></a>Migrando volumes persistentes

Se estiver migrando volumes persistentes existentes para o AKS, você geralmente seguirá estas etapas:

1. Desative as gravações no aplicativo. (Essa etapa é opcional e requer tempo de inatividade.)
2. Tire instantâneos dos discos.
3. Crie novos Managed disks a partir dos instantâneos.
4. Crie volumes persistentes no AKS.
5. Atualize as especificações de pod para [usar volumes existentes](https://docs.microsoft.com/azure/aks/azure-disk-volume) em vez de PersistentVolumeClaims (provisionamento estático).
6. Implante o aplicativo no AKS.
7. Verifica.
8. Aponte o tráfego para o cluster AKS.

> [!IMPORTANT]
> Se optar por não desativar as gravações, você precisará replicar os dados para a nova implantação. Caso contrário, você perderá os dados que foram gravados depois de obter os instantâneos de disco.

Algumas ferramentas de código-fonte aberto podem ajudá-lo a criar discos gerenciados e a migrar volumes entre clusters kubernetes:

* [CLI do Azure extensão de cópia de disco](https://github.com/noelbundick/azure-cli-disk-copy-extension) copia e converte discos em grupos de recursos e regiões do Azure.
* A [extensão da CLI do Azure Kube](https://github.com/yaron2/azure-kube-cli) enumera volumes do ACS kubernetes e os migra para um cluster AKs.

#### <a name="azure-files"></a>Ficheiros do Azure

Ao contrário dos discos, os arquivos do Azure podem ser montados em vários hosts simultaneamente. No cluster do AKS, o Azure e o kubernetes não impedem que você crie um pod que o cluster do ACS ainda usa. Para evitar a perda de dados e o comportamento inesperado, verifique se os clusters não gravam nos mesmos arquivos ao mesmo tempo.

Se seu aplicativo puder hospedar várias réplicas que apontam para o mesmo compartilhamento de arquivos, siga as etapas de migração sem monitoração de estado e implante suas definições de YAML no novo cluster. Caso contrário, uma possível abordagem de migração envolve as seguintes etapas:

1. Implante seu aplicativo no AKS com uma contagem de réplicas de 0.
2. Dimensione o aplicativo no ACS para 0. (Esta etapa requer tempo de inatividade.)
3. Dimensione o aplicativo no AKS até 1.
4. Verifica.
5. Aponte o tráfego para o cluster AKS.

Se você quiser iniciar com um compartilhamento vazio e fazer uma cópia dos dados de origem, poderá usar os [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) comandos para migrar seus dados.

### <a name="deployment-strategy"></a>Estratégia de implantação

Recomendamos que você use seu pipeline de CI/CD existente para implantar uma configuração válida em AKS. Clone suas tarefas de implantação existentes e certifique `kubeconfig` -se de que aponta para o novo cluster AKs.

Se isso não for possível, exporte as definições de recursos do ACS e aplique-as ao AKS. Você pode usar `kubectl` para exportar objetos.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Várias ferramentas de código-fonte aberto podem ajudar, dependendo das suas necessidades de implantação:

* [Velero](https://github.com/heptio/ark) (Essa ferramenta requer o kubernetes 1,7.)
* [Extensão da CLI do Azure Kube](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>Passos de migração

1. [Crie um cluster AKs](https://docs.microsoft.com/azure/aks/create-cluster) por meio do modelo portal do Azure, CLI do Azure ou Azure Resource Manager.

   > [!NOTE]
   > Encontre modelos de Azure Resource Manager de exemplo para AKS no repositório [do Azure/AKs](https://github.com/Azure/AKS/tree/master/examples/vnet) no github.

2. Faça as alterações necessárias nas definições do YAML. Por exemplo, substitua `apps/v1beta1` `apps/v1` por para `Deployments`.

3. [Migrar volumes](#migrating-persistent-volumes) (opcional) de seu cluster do ACS para o cluster AKS.

4. Use seu sistema de CI/CD para implantar aplicativos no AKS. Ou use kubectl para aplicar as definições de YAML.

5. Verifica. Verifique se seus aplicativos funcionam conforme o esperado e se todos os dados migrados foram copiados.

6. Redirecionar o tráfego. Atualize o DNS para apontar clientes para a implantação do AKS.

7. Conclua as tarefas de pós-migração. Se você migrou volumes e optou por não fechar as gravações, copie esses dados para o novo cluster.
