---
title: Use piscina dedicada para executar tarefas - Tarefas
description: Instale uma piscina de cálculo dedicada (piscina de agente) no seu registo para executar uma tarefa de Registo de Contentores Azure.
ms.topic: article
ms.date: 04/30/2020
ms.openlocfilehash: 9eac8d620dd6ce268692494499ade39173fc9b75
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864659"
---
# <a name="run-an-acr-task-on-a-dedicated-agent-pool"></a>Executar uma tarefa ACR em um conjunto de agentes dedicado

Instale uma piscina VM gerida pelo Azure *(piscina*de agente) para permitir executar as suas tarefas de Registo de [Contentores Azure][acr-tasks] num ambiente computacional dedicado. Depois de configurar uma ou mais piscinas no seu registo, pode escolher uma piscina para executar uma tarefa no lugar do ambiente de computação padrão do serviço.

Uma piscina de agente fornece:

- **Suporte de rede virtual** - Atribuir um conjunto de agentes a um Azure VNet, proporcionando acesso a recursos no VNet, tais como um registo de contentores, cofre chave ou armazenamento.
- **Escala conforme necessário** - Aumente o número de instâncias num conjunto de agentes para tarefas intensivas em cálculo, ou escala para zero. A faturação baseia-se na atribuição.
- **Opções flexíveis** - Escolha entre [diferentes camadas](#pool-tiers) de piscina e opções de escala para atender às necessidades de carga de trabalho da sua tarefa.
- **Gestão azure** - Os conjuntos de tarefas são remendados e mantidos pelo Azure, proporcionando alocação reservada sem a necessidade de manter os VMs individuais.

Esta funcionalidade está disponível no nível de serviço de registo de contentores **Premium.** Para obter informações sobre os níveis e limites de serviço de registo, consulte o [Registo de Contentores Azure SKUs][acr-tiers].

> [!IMPORTANT]
> Esta funcionalidade encontra-se atualmente em pré-visualização, e [aplicam-se algumas limitações.](#preview-limitations) As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).
>

## <a name="preview-limitations"></a>Limitações de pré-visualização

- As piscinas do agente de tarefas suportam atualmente os nódoslinos Linux. Os nódos os si não suportam atualmente.
- Os grupos de agentes de tarefas estão disponíveis em pré-visualização nas seguintes regiões: West US 2, South Central US, East US 2 e East US.
- Para cada registo, a quota total de vCPU (núcleo) por defeito para todos os grupos de agentes é de 16. Abra um pedido de [apoio][open-support-ticket] para uma atribuição adicional.

## <a name="prerequisites"></a>Pré-requisitos

* Para utilizar os passos Do CLI Azure neste artigo, é necessária a versão 2.3.1 do Azure CLI. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli]. Ou correr em [Azure Cloud Shell.](../cloud-shell/quickstart.md)
* Se ainda não tiver um registo de contentores, [crie um][create-reg-cli] (nível Premium necessário) numa região de pré-visualização.

## <a name="pool-tiers"></a>Níveis de piscina

Os níveis de piscina de agentes fornecem os seguintes recursos por exemplo na piscina.

|Escalão  |CPU  |Memória (GB)  |
|---------|---------|---------|
|S1     |  2       |  3       |
|S2     |  4       |   8      |
|S3     |    8     |   16      |

## <a name="create-and-manage-a-task-agent-pool"></a>Criar e gerir um conjunto de agentes de tarefas

### <a name="set-default-registry-optional"></a>Definir registo predefinido (opcional)

Para simplificar os comandos Azure CLI que se seguem, detete o registo predefinido executando o comando [de configuração az:][az-configure]

```azurecli
az configure --defaults acr=<registryName>
```

Os seguintes exemplos pressupõem que definiu o registo predefinido. Caso contrário, `--registry <registryName>` passe um `az acr` parâmetro em cada comando.

### <a name="create-agent-pool"></a>Criar piscina de agente

Crie uma piscina de agente usando o [az acr agentpool criar][az-acr-agentpool-create] comando. O exemplo seguinte cria um pool de nível S2 (4 CPU/instância). Por padrão, a piscina contém 1 instância.

```azurecli
az acr agentpool create \
    --name myagentpool \
    --tier S2
```

A criação de uma piscina de agentes e outras operações de gestão de piscinas levará vários minutos para ser concluída.

### <a name="scale-pool"></a>Piscina de escala

Escala o tamanho da piscina para cima ou para baixo com o comando de [atualização acr acr agentpool.][az-acr-agentpool-update] O exemplo seguinte escala a piscina para 2 instâncias. Pode escalar para 0 instâncias.

```azurecli
az acr agentpool update \
    --name myagentpool \
    --count 2
```

## <a name="create-pool-in-a-virtual-network"></a>Criar piscina numa rede virtual

### <a name="add-firewall-rules"></a>Adicione regras de firewall

As piscinas de agentes de tarefas requerem acesso aos seguintes serviços Azure. As seguintes regras de firewall devem ser adicionadas a quaisquer grupos de segurança de rede existentes ou rotas definidas pelo utilizador.

| Direção | Protocolo | Origem         | Porta de origem | Destino          | Porto de Dest | Usado    |
|-----------|----------|----------------|-------------|----------------------|-----------|---------|
| Saída  | TCP      | VirtualNetwork | Qualquer         | Armazenamento              | 443       | Predefinição |
| Saída  | TCP      | VirtualNetwork | Qualquer         | EventHub             | 443       | Predefinição |
| Saída  | TCP      | VirtualNetwork | Qualquer         | AzureActiveDirectory | 443       | Predefinição |
| Saída  | TCP      | VirtualNetwork | Qualquer         | AzureMonitor         | 443       | Predefinição |

> [!NOTE]
> Se as suas tarefas necessitarem de recursos adicionais da internet pública, adicione as regras correspondentes. Por exemplo, estas regras são necessárias se executar uma tarefa de construção de estivador que precisa de retirar as imagens base do Docker Hub, ou restaurar um pacote NuGet.

### <a name="create-pool-in-vnet"></a>Criar piscina em VNet

O exemplo seguinte cria um conjunto de agentes na subnet *mysubnet* da *rede myvnet:*

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

Os seguintes exemplos mostram como especificar um conjunto de agentes na fila de uma tarefa.

> [!NOTE]
> Para utilizar uma piscina de agente numa tarefa ACR, certifique-se de que a piscina contém pelo menos 1 instância.
>

### <a name="quick-task-run"></a>Execução rápida de tarefas

Faça fila na piscina do agente utilizando o comando de `--agent-pool` [construção az acr][az-acr-build] e passe o parâmetro:

```azurecli
az acr build \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --file Dockerfile \
    https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

### <a name="automatically-triggered-task"></a>Tarefa ativada automaticamente

Por exemplo, criar uma tarefa programada na piscina de agente com [a z acr task create][az-acr-task-create], passando o `--agent-pool` parâmetro.

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

Para verificar a configuração da tarefa, executar [az acr task run:][az-acr-task-run]

```azurecli
az acr task run \
    --name mytask
```

### <a name="query-pool-status"></a>Estado da piscina de consulta

Para encontrar o número de execuções atualmente programadas na piscina de agentes, execute o [az acr agentpool show][az-acr-agentpool-show].

```azurecli
az acr agentpool show \
    --name myagentpool \
    --queue-count
```

## <a name="next-steps"></a>Passos seguintes

Para mais exemplos de construções de imagem de contentores e manutenção na nuvem, confira a [série tutorial ACR Tasks](container-registry-tutorial-quick-task.md).



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
