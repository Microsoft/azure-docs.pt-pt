---
title: Use piscina dedicada para executar tarefa - Tarefas
description: Crie um conjunto de cálculos dedicado (piscina de agente) no seu registo para executar uma tarefa de Registo de Contentores Azure.
ms.topic: article
ms.date: 10/12/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 94956af14aad2b62e6455f443329bcd3232095c0
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844919"
---
# <a name="run-an-acr-task-on-a-dedicated-agent-pool"></a>Executar uma tarefa ACR em uma piscina de agente dedicado

Crie um pool VM gerido pela Azure *(piscina* de agente) para permitir a execução das suas [tarefas de Registo de Contentores Azure][acr-tasks] num ambiente computacional dedicado. Depois de configurar uma ou mais piscinas no seu registo, pode escolher uma piscina para executar uma tarefa no lugar do ambiente computacional padrão do serviço.

Uma piscina de agentes fornece:

- **Suporte de rede virtual** - Atribua um pool de agente a um Azure VNet, proporcionando acesso a recursos no VNet, como um registo de contentores, cofre de chaves ou armazenamento.
- **Escala conforme necessário** - Aumente o número de casos num conjunto de agentes para tarefas intensivas de computação, ou escala para zero. A faturação baseia-se na atribuição de piscinas. Para mais detalhes, consulte [o Preço.](https://azure.microsoft.com/pricing/details/container-registry/)
- **Opções flexíveis** - Escolha entre [diferentes níveis de piscina](#pool-tiers) e opções de escala para atender às necessidades de carga de trabalho da sua tarefa.
- **Gestão Azure** - Os pools de tarefas são remendados e mantidos pela Azure, proporcionando uma dotação reservada sem a necessidade de manter os VM individuais.

Esta funcionalidade está disponível no nível de serviço de registo de contentores **Premium.** Para obter informações sobre os níveis e limites do serviço de registo, consulte [SKUs do Registo de Contentores Azure][acr-tiers].

> [!IMPORTANT]
> Esta funcionalidade encontra-se atualmente em pré-visualização, e [algumas limitações aplicam-se](#preview-limitations). As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).
>

## <a name="preview-limitations"></a>Limitações de pré-visualização

- Os grupos de agentes de tarefa suportam atualmente os nós Linux. Os nós windows não são suportados atualmente.
- Os grupos de agentes de tarefa estão disponíveis em pré-visualização nas seguintes regiões: West US 2, South Central US, East US 2, East US, Central US, USGov Arizona, USGov Texas e USGov Virginia.
- Para cada registo, a quota total de VCPU (core) por defeito é de 16 para todos os grupos de agentes padrão e é 0 para piscinas de agentes isolados. Abra um pedido de [apoio][open-support-ticket] para alocação adicional.
- Não pode cancelar uma tarefa numa piscina de agentes.

## <a name="prerequisites"></a>Pré-requisitos

* Para utilizar os passos do Azure CLI neste artigo, é necessária a versão 2.3.1 do Azure CLI. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli]. Ou correr em [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Se ainda não tiver um registo de contentores, [crie um][create-reg-cli] (nível Premium necessário) numa região de pré-visualização.

## <a name="pool-tiers"></a>Níveis de piscina

Os níveis de piscina do agente fornecem os seguintes recursos por exemplo na piscina.

|Escalão de serviço    | Tipo  |  CPU  |Memória (GB)  |
|---------|---------|---------|---------|
|S1     |  padrão    | 2       |    3     |
|S2     |  padrão    | 4       |    8     |
|S3     |  padrão    | 8       |   16     |
|I6     |  isolado    | 64     |   216     |


## <a name="create-and-manage-a-task-agent-pool"></a>Criar e gerir um pool de agentes de tarefa

### <a name="set-default-registry-optional"></a>Definir registo predefinido (opcional)

Para simplificar os comandos Azure CLI que se seguem, desafie o registo predefinido executando o comando [de configuração az:][az-configure]

```azurecli
az configure --defaults acr=<registryName>
```

Os exemplos a seguir assumem que definiu o registo predefinido. Caso contrário, passe um `--registry <registryName>` parâmetro em cada `az acr` comando.

### <a name="create-agent-pool"></a>Criar piscina de agentes

Crie uma piscina de agentes utilizando o comando [de agente az acr.][az-acr-agentpool-create] O exemplo a seguir cria uma piscina de nível S2 (4 CPU/instância). Por predefinição, a piscina contém 1 instância.

```azurecli
az acr agentpool create \
    --name myagentpool \
    --tier S2
```

> [!NOTE]
> A criação de uma piscina de agentes e outras operações de gestão de piscinas demoram vários minutos a ser concluídas.

### <a name="scale-pool"></a>Piscina de escala

Dimensione o tamanho da piscina para cima ou para baixo com o comando de atualização de [agente az acr.][az-acr-agentpool-update] O exemplo a seguir escala a piscina para 2 instâncias. Pode escalar para 0 casos.

```azurecli
az acr agentpool update \
    --name myagentpool \
    --count 2
```

## <a name="create-pool-in-a-virtual-network"></a>Criar piscina numa rede virtual

### <a name="add-firewall-rules"></a>Adicionar regras de firewall

Os grupos de agentes de tarefas requerem acesso aos seguintes serviços Azure. As seguintes regras de firewall devem ser adicionadas a quaisquer grupos de segurança de rede existentes ou rotas definidas pelo utilizador.

| Direção | Protocolo | Origem         | Porta de origem | Destino          | Dest Porto | Utilizada    |
|-----------|----------|----------------|-------------|----------------------|-----------|---------|
| Saída  | TCP      | VirtualNetwork | Qualquer         | AzureKeyVault        | 443       | Predefinição |
| Saída  | TCP      | VirtualNetwork | Qualquer         | Armazenamento              | 443       | Predefinição |
| Saída  | TCP      | VirtualNetwork | Qualquer         | EventHub             | 443       | Predefinição |
| Saída  | TCP      | VirtualNetwork | Qualquer         | AzureActiveDirectory | 443       | Predefinição |
| Saída  | TCP      | VirtualNetwork | Qualquer         | AzureMonitor         | 443       | Predefinição |

> [!NOTE]
> Se as suas tarefas necessitarem de recursos adicionais da internet pública, adicione as regras correspondentes. Por exemplo, são necessárias regras adicionais para executar uma tarefa de construção de estivadores que retira as imagens base do Docker Hub, ou restaura um pacote NuGet.

### <a name="create-pool-in-vnet"></a>Criar piscina em VNet

O exemplo a seguir cria uma piscina de agentes na *sub-rede mysubnet* da *rede myvnet:*

```azurecli
# Get the subnet ID
subnetId=$(az network vnet subnet show \
        --resource-grop myresourcegroup \
        --vnet-name myvnet \
        --name mysubnetname \
        --query id --output tsv)

az acr agentpool create \
    --name myagentpool \
    --tier S2 \
    --subnet-id $subnetId
```

## <a name="run-task-on-agent-pool"></a>Executar tarefa na piscina de agente

Os exemplos a seguir mostram como especificar um conjunto de agentes na fila de uma tarefa.

> [!NOTE]
> Para utilizar uma piscina de agente numa tarefa ACR, certifique-se de que a piscina contém pelo menos 1 instância.
>

### <a name="quick-task"></a>Tarefa rápida

Faça fila com uma tarefa rápida na piscina do agente utilizando o comando [de construção az acr][az-acr-build] e passe o `--agent-pool` parâmetro:

```azurecli
az acr build \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --file Dockerfile \
    https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

### <a name="automatically-triggered-task"></a>Tarefa desencadeada automaticamente

Por exemplo, crie uma tarefa programada no pool de agentes com [a tarefa az acr criar,][az-acr-task-create]passando o `--agent-pool` parâmetro.

```azurecli
az acr task create \
    --name mytask \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --schedule "0 21 * * *" \
    --file Dockerfile \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --commit-trigger-enabled false
```

Para verificar a configuração da tarefa, executar [a execução de tarefas az acr][az-acr-task-run]:

```azurecli
az acr task run \
    --name mytask
```

### <a name="query-pool-status"></a>Estado da piscina de consulta

Para encontrar o número de corridas atualmente agendadas na piscina de agentes, executar [az acr agentpool show][az-acr-agentpool-show].

```azurecli
az acr agentpool show \
    --name myagentpool \
    --queue-count
```

## <a name="next-steps"></a>Passos seguintes

Para mais exemplos de construção e manutenção de imagens de contentores na nuvem, confira a [série tutorial ACR Tasks](container-registry-tutorial-quick-task.md).



[acr-tasks]:           container-registry-tasks-overview.md
[acr-tiers]:           container-registry-skus.md
[azure-cli]:           /cli/azure/install-azure-cli
[open-support-ticket]: https://aka.ms/acr/support/create-ticket
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[az-configure]: /cli/azure#az-configure
[az-acr-agentpool-create]: /cli/azure/acr/agentpool#az-acr-agentpool-create
[az-acr-agentpool-update]: /cli/azure/acr/agentpool#az-acr-agentpool-update
[az-acr-agentpool-show]: /cli/azure/acr/agentpool#az-acr-agentpool-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[create-reg-cli]: container-registry-get-started-azure-cli.md
