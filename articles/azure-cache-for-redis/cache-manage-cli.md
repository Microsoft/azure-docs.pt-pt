---
title: Gerenciar o cache do Azure para Redis usando a CLI clássica do Azure | Microsoft Docs
description: Saiba como instalar a CLI clássica do Azure em qualquer plataforma, como usá-la para se conectar à sua conta do Azure e como criar e gerenciar um cache do Azure para Redis da CLI clássica.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 964ff245-859d-4bc1-bccf-62e4b3c1169f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: yegu
ms.openlocfilehash: 3b4756635ae0ab0d282975a6376e60da5f148917
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755426"
---
# <a name="how-to-create-and-manage-azure-cache-for-redis-using-the-azure-classic-cli"></a>Como criar e gerenciar o cache do Azure para Redis usando a CLI clássica do Azure
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [CLI clássica do Azure](cache-manage-cli.md)
>

A CLI clássica do Azure é uma ótima maneira de gerenciar sua infraestrutura do Azure de qualquer plataforma. Este artigo mostra como criar e gerenciar o cache do Azure para instâncias Redis usando a CLI clássica do Azure.

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> Para obter os mais recentes CLI do Azure scripts de exemplo, consulte [CLI do Azure cache do Azure para exemplos de Redis](cli-samples.md).

## <a name="prerequisites"></a>Pré-requisitos
Para criar e gerenciar o cache do Azure para instâncias Redis usando a CLI clássica do Azure, você deve concluir as etapas a seguir.

* Tem de ter uma conta do Azure. Se você não tiver uma, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns instantes.
* [Instale a CLI clássica do Azure](../cli-install-nodejs.md).
* Conecte sua instalação do CLI do Azure com uma conta pessoal do Azure ou com uma conta corporativa ou de estudante do Azure e faça logon na CLI clássica usando o comando `azure login`.
* Antes de executar qualquer um dos comandos a seguir, alterne a CLI clássica para o modo do Gerenciador de recursos executando o comando `azure config mode arm`. Para obter mais informações, consulte [usar a CLI clássica do Azure para gerenciar recursos e grupos de recursos do Azure](../xplat-cli-azure-resource-manager.md).

## <a name="azure-cache-for-redis-properties"></a>Cache do Azure para propriedades Redis
As propriedades a seguir são usadas ao criar e atualizar o cache do Azure para instâncias Redis.

| Propriedade | Comutador | Descrição |
| --- | --- | --- |
| nome |-n,--nome |Nome do cache do Azure para Redis. |
| grupo de recursos |-g,--grupo de recursos |Nome do grupo de recursos. |
| localização |-l,--local |Local para criar o cache. |
| Tamanho |-z,--tamanho |Tamanho do cache do Azure para Redis. Valores válidos: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| SKU |-x,--SKU |Redis SKU. Deve ser um destes: [Basic, Standard, Premium] |
| EnableNonSslPort |-e,--Enable-não SSL-Port |Propriedade EnableNonSslPort do cache do Azure para Redis. Adicione esse sinalizador se você quiser habilitar a porta não SSL para seu cache |
| Configuração do Redis |-c,--Redis-configuração |Configuração do Redis. Insira uma cadeia de caracteres formatada em JSON de chaves de configuração e valores aqui. Formato: "{" ":" "," ":" "}" |
| Configuração do Redis |-f,--Redis-Configuration-File |Configuração do Redis. Insira o caminho de um arquivo que contém chaves de configuração e valores aqui. Formato para a entrada de arquivo: {"": "", "": ""} |
| Contagem de fragmentos |-r,--contagem de fragmentos |Número de fragmentos a serem criados em um cache de cluster Premium com clustering. |
| Rede Virtual |-v,--Virtual-Network |Ao hospedar o cache em uma VNET, especifica a ID de recurso ARM exata da rede virtual para implantar o cache do Azure para Redis no. Exemplo de formato:/subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| tipo de chave |-t,--tipo de chave |Tipo de chave a ser renovada. Valores válidos: [primário, secundário] |
| StaticIP |-p,--static-IP \<static-IP \> |Ao hospedar o cache em uma VNET, especifica um endereço IP exclusivo na sub-rede para o cache. Se não for fornecido, um será escolhido para você na sub-rede. |
| Subrede |t,--\<subnet de sub-rede \> |Ao hospedar o cache em uma VNET, especifica o nome da sub-rede na qual o cache será implantado. |
| VirtualNetwork |-v,--Virtual-Network \<virtual-Network \> |Ao hospedar o cache em uma VNET, especifica a ID de recurso ARM exata da rede virtual para implantar o cache do Azure para Redis no. Exemplo de formato:/subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Subscrição |-s,--assinatura |O identificador da assinatura. |

## <a name="see-all-azure-cache-for-redis-commands"></a>Ver todos os comandos do cache do Azure para Redis
Para ver todos os comandos do cache do Azure para Redis e seus parâmetros, use o comando `azure rediscache -h`.

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

## <a name="create-an-azure-cache-for-redis"></a>Criar um cache do Azure para Redis
Para criar um cache do Azure para Redis, use o seguinte comando:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Para obter mais informações sobre esse comando, execute o comando `azure rediscache create -h`.

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
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Azure Cache for Redis. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-azure-cache-for-redis"></a>Excluir um cache do Azure existente para Redis
Para excluir um cache do Azure para Redis, use o seguinte comando:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Para obter mais informações sobre esse comando, execute o comando `azure rediscache delete -h`.

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

## <a name="list-all-azure-cache-for-redis-within-your-subscription-or-resource-group"></a>Listar todo o cache do Azure para Redis em sua assinatura ou grupo de recursos
Para listar todo o cache do Azure para Redis em sua assinatura ou grupo de recursos, use o seguinte comando:

    azure rediscache list [options]

Para obter mais informações sobre esse comando, execute o comando `azure rediscache list -h`.

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

## <a name="show-properties-of-an-existing-azure-cache-for-redis"></a>Mostrar propriedades de um cache do Azure existente para Redis
Para mostrar as propriedades de um cache do Azure existente para Redis, use o seguinte comando:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Para obter mais informações sobre esse comando, execute o comando `azure rediscache show -h`.

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

## <a name="change-settings-of-an-existing-azure-cache-for-redis"></a>Alterar as configurações de um cache do Azure existente para Redis
Para alterar as configurações de um cache do Azure existente para Redis, use o seguinte comando:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Para obter mais informações sobre esse comando, execute o comando `azure rediscache set -h`.

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

## <a name="renew-the-authentication-key-for-an-existing-azure-cache-for-redis"></a>Renovar a chave de autenticação para um cache do Azure existente para Redis
Para renovar a chave de autenticação para um cache do Azure existente para Redis, use o seguinte comando:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Especifique `Primary` ou `Secondary` para `key-type`.

Para obter mais informações sobre esse comando, execute o comando `azure rediscache renew-key -h`.

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

## <a name="list-primary-and-secondary-keys-of-an-existing-azure-cache-for-redis"></a>Listar chaves primárias e secundárias de um cache do Azure existente para Redis
Para listar as chaves primárias e secundárias de um cache do Azure existente para Redis, use o seguinte comando:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Para obter mais informações sobre esse comando, execute o comando `azure rediscache list-keys -h`.

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
