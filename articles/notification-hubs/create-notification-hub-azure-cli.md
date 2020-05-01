---
title: Quickstart - Criar um hub de notificação Azure utilizando o Azure CLI [ Microsoft Docs
description: Neste tutorial, aprende-se a criar um centro de notificação Azure utilizando o Azure CLI.
services: notification-hubs
author: dbradish-microsoft
manager: barbkess
editor: sethmanheim
ms.service: notification-hubs
ms.devlang: azurecli
ms.workload: mobile
ms.topic: quickstart
ms.date: 03/17/2020
ms.author: dbradish
ms.reviewer: sethm
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 830fd33e19a10ec6472650e3d26fec677b82c3d7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80082452"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Quickstart: Criar um hub de notificação Azure utilizando o Azure CLI

Os Hubs de Notificação do Azure oferecem um motor de envio de escalamento horizontal e utilização fácil que lhe permite enviar notificações para qualquer plataforma (iOS, Android, Windows, Kindle, Baidu, etc.) a partir de qualquer back-end (cloud ou no local). Para mais informações sobre o serviço, consulte [o Que é o Azure Notification Hubs?](notification-hubs-push-notification-overview.md)

Neste arranque rápido, cria um centro de notificação utilizando o Azure CLI. A primeira secção dá-lhe passos para criar um espaço de nome de centro de notificação e informações de política de acesso à consulta para esse espaço de nome. A segunda secção dá-lhe passos para criar um centro de notificação num espaço de nome existente.  Também aprende a criar uma política de acesso personalizado.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

O Notification Hubs requer a versão 2.0.67 ou posterior do Azure CLI. Corra `az --version` para encontrar a versão e bibliotecas dependentes que estão instaladas. Para instalar ou atualizar, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Preparar o ambiente

1. Inicia sessão.

   Inicie o sessão utilizando o comando [de login az](/cli/azure/reference-index#az-login) se estiver a utilizar uma instalação local do CLI.

    ```azurecli-interactive
    az login
    ```

    Siga os passos apresentados no seu terminal para completar o processo de autenticação.

2. Instale a extensão da CLI do Azure.

   Para executar os comandos Azure CLI para centros de notificação, instale a extensão Azure CLI para Centros de [Notificação](/cli/azure/ext/notification-hub/notification-hub).  

    ```azurecli-interactive
    az extension add --name notification-hub
   ```

3. Crie um grupo de recursos.

   Os centros de notificação azure, como todos os recursos do Azure, devem ser implantados num grupo de recursos. Os grupos de recursos permitem organizar e gerir recursos relacionados do Azure.

   Para este arranque rápido, crie um grupo de recursos chamado *spnhubrg* no local *leste* com o seguinte [grupo az criar](/cli/azure/group#az-group-create) comando:

   ```azurecli-interactive
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>Criar um espaço de nome sem notificação

1. Crie um espaço de nome para os seus centros de notificação

   Um espaço de nome contém um ou mais hubs, e o nome deve ser único em todas as subscrições do Azure.  Para verificar a disponibilidade do espaço de nome do serviço, utilize o comando de disponibilidade [de check-availability az notification-hub.](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability)  Executar o espaço de [nome sinuoso az notification-hub criar](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) comando para criar um espaço de nome.  

   ```azurecli-interactive
   #check availability
   az notification-hub namespace check-availability --name spnhubns

   #create the namespace
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

2. Lista as chaves e as cordas de ligação para a sua política de acesso ao espaço de nome.

   Uma política de acesso chamada **RootManageSharedAccessKey** é criada automaticamente para um novo espaço de nome.  Todas as políticas de acesso têm dois conjuntos de chaves e cordas de ligação.  Para listar as teclas e as cordas de ligação para o espaço de nome, execute o comando de regras de autorização de ordem de autorização do espaço de [notificação az.hub.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys)

   ```azurecli-interactive
   az notification-hub namespace authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --name RootManageSharedAccessKey
   ```

## <a name="create-notification-hubs"></a>Criar centros de notificação

1. Crie o seu primeiro centro de notificação.

   Um centro de notificação pode agora ser criado no novo espaço de nome.  Executar o [centro de notificação az criar](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) comando para criar um centro de notificação.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Crie um segundo centro de notificação.

   Vários centros de notificação podem ser criados num único espaço de nome.  Para criar um segundo centro de notificação `az notification-hub create` no mesmo espaço de nome, volte a executar o comando usando um nome de hub diferente.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

## <a name="work-with-access-policies"></a>Trabalhar com políticas de acesso

1. Crie uma nova regra de autorização para um centro de notificação.

   É criada automaticamente uma política de acesso para cada novo centro de notificação.  Para criar e personalizar a sua própria política de acesso, utilize a regra de autorização do [centro de notificação az criar](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) comando.

   ```azurecli-interactive
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Send
   ```

2. Lista políticas de acesso para um centro de notificação.

   Para consultar quais as políticas de acesso para um centro de notificação, utilize o comando de [lista de regras de autorização do centro de notificação az.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list)

   ```azurecli-interactive
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Não utilize a política **DefaultFullSharedAccessSignature** na sua aplicação. Isto destina-se a ser utilizado apenas no seu back-end.  Utilize apenas **políticas de** acesso Ouvir na sua aplicação de cliente.

3. Lista de chaves e cordas de ligação para uma política de acesso ao centro de notificação

   Existem dois conjuntos de chaves e cordas de ligação para cada política de acesso.  Precisará deles mais tarde para lidar com notificações push.  Para listar as chaves e as cordas de ligação para uma política de acesso ao centro de notificação, utilize o comando de regras de regras de regras de [autorização az-hub.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys)

   ```azurecli-interactive
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output json

   #query the keys and connection strings for the custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > Um espaço de nome de centro de [notificação](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) e um centro de [notificação](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) têm políticas de acesso separadas.  Certifique-se de que está a utilizar a referência CLI azure correta ao consultar as teclas e as cordas de ligação.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, utilize o [grupo AZ eliminar](/cli/azure/group) o comando para remover o grupo de recursos e todos os recursos relacionados.

```azurecli-interactive
az group delete --name spnhubrg
```

## <a name="see-also"></a>Consulte também

Descubra todas as capacidades para gerir centros de notificações com o Azure CLI.

* [Lista de referência do Azure CLI full Hubs](/cli/azure/ext/notification-hub/notification-hub)
* [Lista de referência do Namespace Azure CLI do Hubs de Notificação](/cli/azure/ext/notification-hub/notification-hub/namespace)
* [Regras de autorização do Hubs de Notificação Azure CLI lista de referência](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)
* [Lista de referência seletória do Hubs de Notificação Azure CLI](/cli/azure/ext/notification-hub/notification-hub/credential)
