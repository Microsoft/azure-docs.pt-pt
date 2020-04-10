---
title: Gerir o Azure Cache para Redis usando o CLÁSSICO CLI do Azure
description: Saiba como instalar o CLI clássico azure em qualquer plataforma, como usá-lo para ligar à sua conta Azure, e como criar e gerir um Azure Cache para Redis a partir do clássico CLI.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: yegu
ms.openlocfilehash: f71476d7d41ae45d2f1014ed1b257870622487e6
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010839"
---
# <a name="how-to-create-and-manage-azure-cache-for-redis-using-the-azure-classic-cli"></a>Como criar e gerir o Azure Cache para redis usando o CLÁSSICO CLI azure
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [ClI clássico azure](cache-manage-cli.md)
>

O ClI clássico azure é uma ótima maneira de gerir a sua infraestrutura Azure a partir de qualquer plataforma. Este artigo mostra-lhe como criar e gerir o seu Azure Cache para os casos Redis usando o CLI clássico azure.

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> Para obter as mais recentes scripts de amostra sinuosa do Azure CLI, consulte [Azure CLI Azure Cache para amostras Redis](cli-samples.md).

## <a name="prerequisites"></a>Pré-requisitos
Para criar e gerir o Azure Cache para os casos Redis utilizando o CLI clássico do Azure, deve completar os seguintes passos.

* Tem de ter uma conta do Azure. Se não tiver uma, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns momentos.
* [Instale o clássico CLI azure](../cli-install-nodejs.md).
* Ligue a sua instalação Azure CLI com uma conta Azure pessoal, ou com uma conta `azure login` Azure de trabalho ou escolar, e faça login no cli clássico utilizando o comando.
* Antes de executar qualquer um dos seguintes comandos, mude `azure config mode arm` o CLI clássico para o modo Gestor de Recursos executando o comando. Para mais informações, consulte [Use o CLI clássico azure para gerir os recursos e grupos de recursos azure.](../xplat-cli-azure-resource-manager.md)

## <a name="azure-cache-for-redis-properties"></a>Cache Azure para propriedades Redis
As seguintes propriedades são usadas ao criar e atualizar O Cache Azure para os casos Redis.

| Propriedade | Comutador | Descrição |
| --- | --- | --- |
| nome |-n, --nome |Nome do Cache Azure para Redis. |
| grupo de recursos |-g, -grupo de recursos |Nome do Grupo de Recursos. |
| localização |-l, --localização |Localização para criar cache. |
| size |-z, -tamanho |Tamanho do Cache Azure para Redis. Valores válidos: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| sku |-x, --sku |Redis SKU. Deve ser um dos: [Básico, Standard, Premium] |
| EnableNonSslPort |-e- -enable-non-ssl-port |Propriedade EnableNonSslPort do Azure Cache for Redis. Adicione esta bandeira se quiser ativar a porta não-TLS/SSL para a sua cache |
| Configuração Redis |-c, --redis-configuração |Configuração Redis. Introduza aqui uma cadeia de teclas e valores formados JSON. Formato: "{""""""""""}"} |
| Configuração Redis |-f, --redis-configuração-arquivo |Configuração Redis. Introduza aqui o caminho de um ficheiro que contenha chaves e valores de configuração. Formato para a entrada do ficheiro: {"":"":"""""}"} |
| Contagem de Fragmentos |-r, --shard-count |Número de Fragmentos para criar em um Cache cluster premium com clustering. |
| Rede Virtual |-v, -rede virtual |Ao hospedar a sua cache num VNET, especifica o ID de recurso ARM exato da rede virtual para implantar o Cache Azure para Redis. Formato de exemplo: /subscrições/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| tipo chave |-t, --tipo-chave |Tipo de chave para renovar. Valores válidos: [Primário, Secundário] |
| StaticIP |-p, --static-ip static-ip \<static-ip\> |Ao hospedar a sua cache num VNET, especifica um endereço IP único na sub-rede para a cache. Se não for fornecido, um é escolhido para si a partir da sub-rede. |
| Subrede |t, \<-subrede subnet\> |Ao hospedar a sua cache num VNET, especifica o nome da sub-rede para implantar a cache. |
| VirtualNetwork |-v, rede virtual \<de rede virtual\> |Ao hospedar a sua cache num VNET, especifica o ID de recurso ARM exato da rede virtual para implantar o Cache Azure para Redis. Formato de exemplo: /subscrições/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Subscrição |-s, --subscrição |O identificador de assinatura. |

## <a name="see-all-azure-cache-for-redis-commands"></a>Veja todos os comandos De Azure Cache para redis
Para ver todos os comandos Azure Cache para `azure rediscache -h` redis e seus parâmetros, use o comando.

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

## <a name="create-an-azure-cache-for-redis"></a>Criar uma Cache do Azure para Redis
Para criar um Cache Azure para Redis, utilize o seguinte comando:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Para mais informações sobre `azure rediscache create -h` este comando, dirija o comando.

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

## <a name="delete-an-existing-azure-cache-for-redis"></a>Eliminar um Azure Cache existente para Redis
Para eliminar um Cache Azure para Redis, utilize o seguinte comando:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Para mais informações sobre `azure rediscache delete -h` este comando, dirija o comando.

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

## <a name="list-all-azure-cache-for-redis-within-your-subscription-or-resource-group"></a>Listar todos os Cache Sinos Azure para Redis dentro do seu Grupo de Subscrição ou Recursos
Para listar todos os Cache Sinos De Redis no seu Grupo de Subscrição ou Recursos, utilize o seguinte comando:

    azure rediscache list [options]

Para mais informações sobre `azure rediscache list -h` este comando, dirija o comando.

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

## <a name="show-properties-of-an-existing-azure-cache-for-redis"></a>Mostrar propriedades de um Azure Cache existente para Redis
Para mostrar propriedades de um Azure Cache existente para Redis, utilize o seguinte comando:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Para mais informações sobre `azure rediscache show -h` este comando, dirija o comando.

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

<a name="scale"></a>

## <a name="change-settings-of-an-existing-azure-cache-for-redis"></a>Alterar as definições de um Azure Cache existente para Redis
Para alterar as definições de um Azure Cache existente para Redis, utilize o seguinte comando:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Para mais informações sobre `azure rediscache set -h` este comando, dirija o comando.

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

## <a name="renew-the-authentication-key-for-an-existing-azure-cache-for-redis"></a>Renovar a chave de autenticação para um Azure Cache existente para redis
Para renovar a chave de autenticação para um Azure Cache existente para Redis, utilize o seguinte comando:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Especificar `Primary` `Secondary` ou `key-type`para .

Para mais informações sobre `azure rediscache renew-key -h` este comando, dirija o comando.

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

## <a name="list-primary-and-secondary-keys-of-an-existing-azure-cache-for-redis"></a>Lista De Chaves Primárias e Secundárias de um Cache Azure existente para Redis
Para listar as chaves primárias e secundárias de um Azure Cache existente para Redis, utilize o seguinte comando:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Para mais informações sobre `azure rediscache list-keys -h` este comando, dirija o comando.

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
