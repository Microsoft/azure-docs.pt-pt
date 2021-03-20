---
title: Gerir a cache Azure para Redis usando CLI clássico do Azure
description: Saiba como instalar o CLI clássico do Azure em qualquer plataforma, como usá-lo para ligar à sua conta Azure, e como criar e gerir um Cache Azure para Redis a partir do CLI clássico.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: yegu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7643f882d5ac330046c169e0a3f2fa4920331d4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92537699"
---
# <a name="how-to-create-and-manage-azure-cache-for-redis-using-the-azure-classic-cli"></a>Como criar e gerir a Cache Azure para Redis usando o CLI clássico do Azure
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [CLI clássico de Azure](cache-manage-cli.md)
>

O Azure classic CLI é uma ótima maneira de gerir a sua infraestrutura Azure a partir de qualquer plataforma. Este artigo mostra-lhe como criar e gerir o seu Azure Cache para instâncias Redis usando o CLI clássico do Azure.

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> Para obter as mais recentes scripts de amostra do Azure CLI, consulte [a cache Azure Azure Azure cache para amostras de Redis](cli-samples.md).

## <a name="prerequisites"></a>Pré-requisitos
Para criar e gerir a Cache Azure para instâncias Redis usando O CLI clássico do Azure, deve completar os seguintes passos.

* Tem de ter uma conta do Azure. Se não tiver uma, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em poucos momentos.
* [Instale o CLI clássico do Azure](/cli/azure/install-classic-cli).
* Ligue a sua instalação Azure CLI a uma conta Azure pessoal, ou a uma conta Azure de trabalho ou escola, e faça login a partir do CLI clássico usando o `azure login` comando.
* Antes de executar qualquer um dos seguintes comandos, mude o clássico CLI para o modo Gestor de Recursos executando o `azure config mode arm` comando. Para obter mais informações, consulte [o CLI clássico do Azure para gerir os recursos e grupos de recursos da Azure.](../azure-resource-manager/management/manage-resources-cli.md)

## <a name="azure-cache-for-redis-properties"></a>Cache Azure para propriedades redis
As seguintes propriedades são usadas ao criar e atualizar a Cache Azure para instâncias Redis.

| Propriedade | Comutador | Description |
| --- | --- | --- |
| name |-n, -- nome |Nome da Cache Azure para Redis. |
| grupo de recursos |-g, --grupo de recursos |Nome do Grupo de Recursos. |
| localização |-l, --localização |Localização para criar cache. |
| size |-z, --tamanho |Tamanho da Cache Azure para Redis. Valores válidos: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| sku |-x, -sku |Redis SKU. Deve ser um dos seguintes: [Básico, Standard, Premium] |
| EnableNonSslPort |-e, ---enable-non-ssl-porta |EnableNonSslPort propriedade do Azure Cache para Redis. Adicione esta bandeira se quiser ativar a porta não-TLS/SSL para a sua cache |
| Configuração redis |-c, --redis-configuração |Configuração Redis. Introduza uma cadeia de chaves e valores de configuração formatadas por JSON aqui. Formato:"{"""""""}" |
| Configuração redis |-f, --redis-configuração-arquivo |Configuração Redis. Introduza o caminho de um ficheiro contendo teclas e valores de configuração aqui. Formato para a entrada do ficheiro: {"""} |
| Contagem de Fragmentos |-r, --shard-count |Número de Fragmentos para criar numa Cache de Cluster Premium com clustering. |
| Rede Virtual |-v, --rede virtual |Ao hospedar a sua cache num VNET, especifica o ID de recurso ARM exato da rede virtual para implantar a Cache Azure para Redis. Formato de exemplo: /subscrições/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| tipo chave |-t, --tipo chave |Tipo de chave para renovar. Valores válidos: [Primário, Secundário] |
| StaticIP |-p, --estática-ip \<static-ip\> |Ao hospedar o seu cache num VNET, especifica um endereço IP único na sub-rede para a cache. Se não for fornecido, um é escolhido para si a partir da sub-rede. |
| Sub-rede |t, --sub-rede \<subnet\> |Ao hospedar o seu cache num VNET, especifica o nome da sub-rede em que deve colocar a cache. |
| VirtualNetwork |-v, --rede virtual \<virtual-network\> |Ao hospedar a sua cache num VNET, especifica o ID de recurso ARM exato da rede virtual para implantar a Cache Azure para Redis. Formato de exemplo: /subscrições/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Subscrição |-s, --subscrição |O identificador de assinatura. |

## <a name="see-all-azure-cache-for-redis-commands"></a>Ver todos os comandos Azure Cache para redis
Para ver todos os Azure Cache para comandos Redis e seus parâmetros, use o `azure rediscache -h` comando.

```azurecli
C:\>azure rediscache -h
help:    Commands to manage your Azure Cache for Redis(s)
help:
help:    Create an Azure Cache for Redis
help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
help:
help:    Delete an existing Azure Cache for Redis
help:      rediscache delete [--name <name> --resource-group <resource-group> ]
help:
help:    List all Azure Cache for Redis within your Subscription or Resource Group
help:      rediscache list [options]
help:
help:    Show properties of an existing Azure Cache for Redis
help:      rediscache show [--name <name> --resource-group <resource-group>]
help:
help:    Change settings of an existing Azure Cache for Redis
help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
help:
help:    Renew the authentication key for an existing Azure Cache for Redis
help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
help:
help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help  output usage information
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="create-an-azure-cache-for-redis"></a>Criar uma Cache do Azure para Redis
Para criar uma Cache Azure para Redis, utilize o seguinte comando:

```azurecli
    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
```

Para mais informações sobre este comando, executar o `azure rediscache create -h` comando.

```azurecli
C:\>azure rediscache create -h
help:    Create an Azure Cache for Redis
help:
help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
help:
help:    Options:
help:      -h, --help                                               output usage information
help:      -v, --verbose                                            use verbose output
help:      -vv                                                      more verbose with debug output
help:      --json                                                   use json output
help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>                    Name of the Resource Group
help:      -l, --location <location>                                Location to create cache.
help:      -z, --size <size>                                        Size of the Azure Cache for Redis. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Azure Cache for Redis. Add this flag if you want to enable the non-TLS/SSL Port for your cache
help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
help:      -t, --subnet <subnet>                                    Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
help:      -p, --static-ip <static-ip>                              Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
help:      -s, --subscription <id>                                  the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="delete-an-existing-azure-cache-for-redis"></a>Elimine uma cache Azure existente para Redis
Para eliminar uma Cache Azure para Redis, utilize o seguinte comando:

```azurecli
    azure rediscache delete [--name <name> --resource-group <resource-group> ]
```

Para mais informações sobre este comando, executar o `azure rediscache delete -h` comando.

```azurecli
C:\>azure rediscache delete -h
help:    Delete an existing Azure Cache for Redis
help:
help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="list-all-azure-cache-for-redis-within-your-subscription-or-resource-group"></a>Enuda toda a Cache Azure para Redis dentro da sua Subscrição ou Grupo de Recursos
Para listar todos os Azure Cache para Redis dentro do seu Grupo de Subscrição ou Recursos, utilize o seguinte comando:

```azurecli
    azure rediscache list [options]
```

Para mais informações sobre este comando, executar o `azure rediscache list -h` comando.

```azurecli
C:\>azure rediscache list -h
help:    List all Azure Cache for Redis within your Subscription or Resource Group
help:
help:    Usage: rediscache list [options]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -g, --resource-group <resource-group>  Name of the Resource Group
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="show-properties-of-an-existing-azure-cache-for-redis"></a>Mostrar propriedades de uma cache Azure existente para Redis
Para mostrar as propriedades de uma cache Azure existente para Redis, utilize o seguinte comando:

```azurecli
    azure rediscache show [--name <name> --resource-group <resource-group>]
```

Para mais informações sobre este comando, executar o `azure rediscache show -h` comando.

```azurecli
C:\>azure rediscache show -h
help:    Show properties of an existing Azure Cache for Redis
help:
help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

<a name="scale"></a>

## <a name="change-settings-of-an-existing-azure-cache-for-redis"></a>Alterar as definições de uma cache Azure existente para Redis
Para alterar as definições de uma cache Azure existente para Redis, utilize o seguinte comando:

```azurecli
    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
```

Para mais informações sobre este comando, executar o `azure rediscache set -h` comando.

```azurecli
C:\>azure rediscache set -h
help:    Change settings of an existing Azure Cache for Redis
help:
help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
help:
help:    Options:
help:      -h, --help                                               output usage information
help:      -v, --verbose                                            use verbose output
help:      -vv                                                      more verbose with debug output
help:      --json                                                   use json output
help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>                    Name of the Resource Group
help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
help:      -s, --subscription <subscription>                        the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="renew-the-authentication-key-for-an-existing-azure-cache-for-redis"></a>Renovar a chave de autenticação para uma Cache Azure existente para Redis
Para renovar a chave de autenticação de um Cache Azure existente para Redis, utilize o seguinte comando:

```azurecli
    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]
```

Especificar `Primary` ou `Secondary` para `key-type` .

Para mais informações sobre este comando, executar o `azure rediscache renew-key -h` comando.

```azurecli
C:\>azure rediscache renew-key -h
help:    Renew the authentication key for an existing Azure Cache for Redis
help:
help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="list-primary-and-secondary-keys-of-an-existing-azure-cache-for-redis"></a>Lista chaves primárias e secundárias de uma cache Azure existente para Redis
Para listar as chaves primárias e secundárias de uma cache Azure existente para Redis, utilize o seguinte comando:

```azurecli
    azure rediscache list-keys [--name <name> --resource-group <resource-group>]
```

Para mais informações sobre este comando, executar o `azure rediscache list-keys -h` comando.

```azurecli
C:\>azure rediscache list-keys -h
help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
help:
help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```