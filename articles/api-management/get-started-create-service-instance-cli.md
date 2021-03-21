---
title: Quickstart - Criar exemplo de Gestão API Azure usando O CLI
description: Crie uma nova instância de serviço de gestão Azure API utilizando o Azure CLI.
author: dlepow
ms.service: api-management
ms.topic: quickstart
ms.custom: ''
ms.date: 09/10/2020
ms.author: apimpm
ms.openlocfilehash: 72d1faac02a21f23d46eb992af1d501bca89e71f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101688081"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-cli"></a>Quickstart: Criar uma nova instância de serviço de gestão API Azure utilizando o Azure CLI

A Gestão de API (APIM) do Azure ajuda as organizações a publicar APIs para programadores externos, parceiros e internos, de modo a que estes possam explorar ao máximo o potencial dos seus dados e serviços. A Gestão de API fornece as competências essenciais para garantir um programa de API bem-sucedido através do envolvimento dos programadores, das informações de negócio, da análise, da segurança e da proteção. A APIM permite-lhe criar e gerir os modernos gateways API para serviços de backend existentes hospedados em qualquer lugar. Para obter mais informações, consulte [Descrição Geral](api-management-key-concepts.md).

Este quickstart descreve os passos para criar uma nova instância de Gestão de API usando comandos [az apim](/cli/azure/apim) no CLI Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.11.1 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os casos de Gestão da API da Azure, como todos os recursos da Azure, devem ser implantados num grupo de recursos. Os grupos de recursos permitem organizar e gerir recursos relacionados do Azure.

Em primeiro lugar, crie um grupo de recursos chamado *myResourceGroup* na localização central dos EUA com o seguinte [grupo az criar](/cli/azure/group#az-group-create) comando:

```azurecli-interactive
az group create --name myResourceGroup --location centralus
```

## <a name="create-a-new-service"></a>Criar um novo serviço

Agora que tem um grupo de recursos, pode criar uma instância de serviço de Gestão API. Crie um utilizando o [az apim criar](/cli/azure/apim#az-apim-create) comando e fornecer um nome de serviço e detalhes do editor. O nome de serviço deve ser único dentro de Azure. 

No exemplo seguinte, *o myapim* é utilizado para o nome de serviço. Atualize o nome para um valor único. Atualize também o nome da organização da editora API e o endereço de e-mail para receber notificações. 

```azurecli-interactive
az apim create --name myapim --resource-group myResourceGroup \
  --publisher-name Contoso --publisher-email admin@contoso.com \
  --no-wait
```

Por predefinição, o comando cria a instância no nível de Desenvolvedor, uma opção económica para avaliar a Gestão API da Azure. Este nível não é para uso de produção. Para obter mais informações sobre o dimensionamento dos escalões da Gestão de API, consulte [Atualização de versão do software e dimensionamento](upgrade-and-scale.md). 

> [!TIP]
> Pode levar entre 30 a 40 minutos para criar e ativar um serviço de Gestão API neste nível. O comando anterior utiliza a `--no-wait` opção para que o comando retorne imediatamente enquanto o serviço é criado.

Verifique o estado da implantação executando o comando [az apim show:](/cli/azure/apim#az-apim-show)

```azurecli-interactive
az apim show --name myapim --resource-group myResourceGroup --output table
```

Inicialmente, a produção é semelhante à seguinte, mostrando o `Activating` estado:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR    PUBLIC IP    PRIVATE IP    STATUS      TIER       UNITS
-----------  ----------------  ----------  --------------  -----------  ------------  ----------  ---------  -------
myapim       myResourceGroup   Central US                                             Activating  Developer  1
```

Após a ativação, o estado é `Online` e a instância de serviço tem um endereço de gateway e endereço IP público. Por enquanto, estes endereços não expõem nenhum conteúdo. Por exemplo:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR                       PUBLIC IP     PRIVATE IP    STATUS    TIER       UNITS
-----------  ----------------  ----------  ---------------------------------  ------------  ------------  --------  ---------  -------
myapim       myResourceGroup   Central US  https://myapim.azure-api.net       203.0.113.1                 Online    Developer  1
```

Quando o seu serviço de gestão API estiver online, está pronto para usá-lo. Comece com o tutorial para [importar e publicar a sua primeira API.](import-and-publish.md)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode utilizar o comando de eliminação do [grupo AZ](/cli/azure/group#az-group-delete) para remover o grupo de recursos e a instância do serviço de gestão da API.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Importar e publicar a sua primeira API](import-and-publish.md)
