---
title: Quickstart - Criar um centro de notificação Azure utilizando o Azure CLI Microsoft Docs
description: Neste tutorial, você aprende a criar um centro de notificação Azure usando o Azure CLI.
services: notification-hubs
author: dbradish-microsoft
manager: barbkess
editor: sethmanheim
ms.service: notification-hubs
ms.devlang: azurecli
ms.workload: mobile
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: dbradish
ms.reviewer: thsomasu
ms.lastreviewed: 03/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: ebed70a99e8e906db802099e052e69ab2575d737
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875037"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Quickstart: Criar um centro de notificação Azure utilizando o Azure CLI

Os Hubs de Notificação do Azure oferecem um motor de envio de escalamento horizontal e utilização fácil que lhe permite enviar notificações para qualquer plataforma (iOS, Android, Windows, Kindle, Baidu, etc.) a partir de qualquer back-end (cloud ou no local). Para mais informações sobre o serviço, consulte [o que é o Azure Notification Hubs?](notification-hubs-push-notification-overview.md)

Neste arranque rápido, cria-se um centro de notificação utilizando o Azure CLI. A primeira secção dá-lhe passos para criar um espaço de nomes de Centros de Notificação. A segunda secção dá-lhe passos para criar um centro de notificação num espaço de nome existente. Também aprende a criar uma política de acesso personalizado.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Os Centros de Notificação requerem a versão 2.0.67 ou posterior do Azure CLI. Executar [a versão az](/cli/azure/reference-index?#az_version) para encontrar a versão e bibliotecas dependentes que estão instaladas. Para atualizar para a versão mais recente, executar [atualização az](/cli/azure/reference-index?#az_upgrade).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os Hubs de Notificação Azure, como todos os recursos da Azure, devem ser implantados num grupo de recursos.  Os grupos de recursos permitem organizar e gerir recursos relacionados do Azure.  Consulte [o que é O Gestor de Recursos Azure](/azure/azure-resource-manager/management/overview) para saber mais sobre grupos de recursos.

Para este arranque rápido, crie um grupo de recursos chamado **spnhubrg** na localização **leste** com o [seguinte grupo az criar](/cli/azure/group#az-group-create) comando.

```azurecli
az group create --name spnhubrg --location eastus
```

## <a name="create-a-notification-hubs-namespace"></a>Criar um espaço de nomes de Centros de Notificação

1. Crie um espaço de nome para os seus centros de notificação.

   Um espaço de nome contém um ou mais hubs, e o nome deve ser único em todas as subscrições do Azure e ter pelo menos seis caracteres de comprimento. Para verificar a disponibilidade de um nome, utilize o comando [de disponibilidade de verificação do espaço de identificação az-hub.](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability)

   ```azurecli
   az notification-hub namespace check-availability --name spnhubns
   ```

   O Azure CLI responde ao seu pedido de disponibilidade exibindo a seguinte saída da consola:

   ```shell
   {
   "id": "/subscriptions/yourSubscriptionID/providers/Microsoft.NotificationHubs/checkNamespaceAvailability",
   "isAvailable": true,
   "location": null,
   "name": "spnhubns",
   "properties": false,
   "sku": null,
   "tags": null,
   "type": "Microsoft.NotificationHubs/namespaces/checkNamespaceAvailability"
   }
   ```

   Note a segunda linha na resposta Azure CLI, `"isAvailable": true` . Esta linha diz `false` se o nome pretendido especificado para o espaço de nome não estiver disponível. Assim que tiver confirmado a disponibilidade do nome, execute o [espaço de nome az notification-hub criar](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) comando para criar o seu espaço de nome.  

   ```azurecli
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

   Se o `--name` que forneceu ao comando não estiver `az notification-hub namespace create` disponível, ou não cumprir [as regras e restrições de Nomeação para recursos Azure,](../azure-resource-manager/management/resource-name-rules.md)o Azure CLI responde com a seguinte saída da consola:

   ```shell
   #the name is not available
   The specified name is not available. For more information visit https://aka.ms/eventhubsarmexceptions.

   #the name is invalid
   The specified service namespace is invalid.
   ```

   Se o primeiro nome que tentou não for bem sucedido, selecione um nome diferente para o seu novo espaço de nome e volte a executar o `az notification-hub namespace create` comando.

   > [!NOTE]
   > Deste passo em frente deve substituir o valor do `--namespace` parâmetro em cada comando Azure CLI que copiar deste arranque rápido.

2. Arranja uma lista de espaços para nomes.

   Para ver os detalhes sobre o seu novo espaço de nome, utilize o comando [da lista de nomes az notification-hub.](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-list) O `--resource-group` parâmetro é opcional se quiser ver todos os espaços de nome para uma subscrição.

   ```azurecli
   az notification-hub namespace list --resource-group spnhubrg
   ```

## <a name="create-notification-hubs"></a>Criar centros de notificação

1. Crie o seu primeiro centro de notificação.

   Um ou mais centros de notificação podem agora ser criados no seu novo espaço de nome. Executar o [centro de notificação az criar](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) comando para criar um centro de notificação.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Criar um segundo centro de notificação.

   Vários centros de notificação podem ser criados num único espaço com nomes. Para criar um segundo hub de notificação no mesmo espaço de nome, execute novamente o `az notification-hub create` comando usando um nome de hub diferente.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

3. Obtenha uma lista de centros de notificação.

   O Azure CLI devolve uma mensagem de sucesso ou erro a cada comando executado; no entanto, poder consultar uma lista de centros de notificação é reconfortante. O comando [da lista az-hub](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-list) foi projetado para este fim.

   ```azurecli
   az notification-hub list --resource-group spnhubrg --namespace-name spnhubns --output table
   ```

## <a name="work-with-access-policies"></a>Trabalhar com políticas de acesso

1. O Azure Notification Hubs utiliza [a segurança de assinatura](./notification-hubs-push-notification-security.md) de acesso partilhado através da utilização de políticas de acesso. Duas políticas são criadas automaticamente quando cria um centro de notificação. As cadeias de ligação destas políticas são necessárias para configurar notificações push. O comando [da lista de regras de autorização az-hub](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) fornece uma lista de nomes de política e respetivos grupos de recursos.

   ```azurecli
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Não utilize a política _de assinatura DefaultFulFuldAccesss na_ sua aplicação. Esta política deve ser usada apenas no seu back-end. Utilize apenas `Listen` políticas de acesso na aplicação do seu cliente.

2. Se pretender criar regras de autorização adicionais com nomes significativos, pode criar e personalizar a sua própria política de acesso utilizando o comando [de autorização az-hub de autorização do hub.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) O `--rights` parâmetro é uma lista de autorizações delimitadas pelo espaço que pretende atribuir.

   ```azurecli
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Manage Send
   ```

3. Existem dois conjuntos de chaves e cordas de ligação para cada política de acesso. Mais tarde, vai precisar deles para [configurar um centro de notificação.](./configure-notification-hub-portal-pns-settings.md) Para listar as chaves e as cordas de ligação para uma política de acesso a Centros de Notificação, utilize o comando [az notification-hub de regra de regra de lista-chaves.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys)

   ```azurecli
   # query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output table
   ```

   ```azurecli
   # query the keys and connection strings for a custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > Um [espaço de nome de Centros de Notificação](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) e um centro de [notificação](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) têm políticas de acesso separadas. Certifique-se de que está a utilizar a referência Azure CLI correta ao consultar as teclas e as cordas de ligação.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, utilize o comando de eliminação do [grupo AZ](/cli/azure/group) para remover o grupo de recursos e todos os recursos relacionados:

```azurecli
az group delete --name spnhubrg
```

## <a name="next-steps"></a>Passos seguintes

* Neste arranque rápido, criou um centro de notificação. Para saber como configurar o hub com as definições do sistema de notificação da plataforma (PNS), consulte [Configurar notificações push num centro de notificação](configure-notification-hub-portal-pns-settings.md)

* Descubra as extensas capacidades de gestão de centros de notificações com o Azure CLI:

  [Lista completa de referência dos Centros de Notificação](/cli/azure/ext/notification-hub/notification-hub)

  [Lista de referência de espaço de nome de Centros de Notificação](/cli/azure/ext/notification-hub/notification-hub/namespace)

  [Lista de referência de regras de autorização de Centros de Notificação](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)

  [Lista de referência credenciais de Centros de Notificação](/cli/azure/ext/notification-hub/notification-hub/credential)
