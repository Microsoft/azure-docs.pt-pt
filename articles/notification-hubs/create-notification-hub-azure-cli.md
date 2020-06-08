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
ms.reviewer: sethm
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: f39d5ca5e153da3d1644aabeb7e48b41d07fe253
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485155"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Quickstart: Criar um centro de notificação Azure utilizando o Azure CLI

Os Hubs de Notificação do Azure oferecem um motor de envio de escalamento horizontal e utilização fácil que lhe permite enviar notificações para qualquer plataforma (iOS, Android, Windows, Kindle, Baidu, etc.) a partir de qualquer back-end (cloud ou no local). Para mais informações sobre o serviço, consulte [o que é o Azure Notification Hubs?](notification-hubs-push-notification-overview.md)

Neste arranque rápido, cria-se um centro de notificação utilizando o Azure CLI. A primeira secção dá-lhe passos para criar um espaço de nome de centro de notificação.  A segunda secção dá-lhe passos para criar um centro de notificação num espaço de nome existente.  Também aprende a criar uma política de acesso personalizado.  

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Os Centros de Notificação requerem a versão 2.0.67 ou posterior do Azure CLI. Corra `az --version` para encontrar a versão e bibliotecas dependentes que estão instaladas. Para instalar ou atualizar, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Preparar o ambiente

1. Inicia sessão.

   Inicie sessão utilizando o comando [de login az](/cli/azure/reference-index#az-login) se estiver a utilizar uma instalação local do CLI.

    ```azurecli
    az login
    ```

    Siga os passos apresentados no seu terminal para completar o processo de autenticação.

2. Instale a extensão da CLI do Azure.

   Ao trabalhar com referências de extensão para o Azure CLI, deve primeiro instalar a extensão.  As extensões Azure CLI dão-lhe acesso a comandos experimentais e pré-lançamento que ainda não foram enviados como parte do núcleo CLI.  Para saber mais sobre extensões, incluindo atualização e desinstalação, consulte [extensões de utilização com Azure CLI](/cli/azure/azure-cli-extensions-overview).

   Instale a [extensão para Centros de Notificação](/cli/azure/ext/notification-hub/notification-hub) executando o seguinte comando:

    ```azurecli
    az extension add --name notification-hub
   ```

3. Crie um grupo de recursos.

   Os Hubs de Notificação Azure, como todos os recursos da Azure, devem ser implantados num grupo de recursos. Os grupos de recursos permitem organizar e gerir recursos relacionados do Azure.

   Para este arranque rápido, crie um grupo de recursos chamado _spnhubrg_ na localização _este_ com o seguinte [grupo az criar](/cli/azure/group#az-group-create) comando:

   ```azurecli
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>Criar um espaço de nome de centro de notificação

1. Crie um espaço de nome para os seus centros de notificação.

   Um espaço com nomes contém um ou mais hubs, e **o nome deve ser único em todas as subscrições do Azure e ter pelo menos seis caracteres de comprimento**.  Para verificar a disponibilidade de um nome, utilize o comando [de disponibilidade de verificação do espaço de identificação az-hub.](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability)

   ```azurecli
   az notification-hub namespace check-availability --name spnhubns
   ```

   O Azure CLI responderá ao seu pedido de disponibilidade exibindo a seguinte saída da consola:

   ```output
   {
   "id": "/subscriptions/yourSubscriptionID/providers/Microsoft.NotificationHubs/checkNamespaceAvailability",
   "isAvailiable": true,
   "location": null,
   "name": "spnhubns",
   "properties": false,
   "sku": null,
   "tags": null,
   "type": "Microsoft.NotificationHubs/namespaces/checkNamespaceAvailability"
   }
   ```

   Note a segunda linha na resposta Azure CLI, `"isAvailable": true` .  Esta linha será lida `false` se o nome pretendido especificado para o espaço de nome estiver disponível.  Assim que tiver confirmado a disponibilidade do nome, execute o [espaço de nome az notification-hub criar](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) comando para criar o seu espaço de nome.  

   ```azurecli
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

   Se o `--name` que forneceu ao comando não estiver `az notification-hub namespace create` disponível, ou não cumprir [as regras de nomeação e as restrições para os recursos Azure,](/azure/azure-resource-manager/management/resource-name-rules)o Azure CLI responderá com a seguinte saída da consola:

   ```output
   #the name is not available
   The specified name is not available. For more information visit https://aka.ms/eventhubsarmexceptions.

   #the name is invalied
   The specified service namespace is invalid.
   ```

   Se o primeiro nome que tentou não for bem sucedido, selecione um nome diferente para o seu novo espaço de nome e volte a executar o `az notification-hub namespace create` comando.

   > [!NOTE]
   > Deste passo em frente terá de substituir o valor do `--namespace` parâmetro em cada comando Azure CLI que copiar deste arranque rápido.

2. Arranja uma lista de espaços para nomes.

   Para ver os detalhes sobre o seu novo espaço de nome, utilize o comando [da lista de nomes az notification-hub.](/cli/azure/ext/notification-hub/notification-hub/namespace?view=azure-cli-latest#ext-notification-hub-az-notification-hub-namespace-list)  O `--resource-group` parâmetro é opcional se quiser ver todos os espaços de nome para uma subscrição.

   ```azurecli
   az notification-hub namespace list --resource-group spnhubrg
   ```

## <a name="create-notification-hubs"></a>Criar centros de notificação

1. Crie o seu primeiro centro de notificação.

   Um ou mais centros de notificação podem agora ser criados no seu novo espaço de nome.  Executar o [centro de notificação az criar](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) comando para criar um centro de notificação.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Criar um segundo centro de notificação.

   Vários centros de notificação podem ser criados num único espaço com nomes.  Para criar um segundo hub de notificação no mesmo espaço de nome, execute novamente o `az notification-hub create` comando usando um nome de hub diferente.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

3. Obtenha uma lista de centros de notificação.

   O Azure CLI devolve uma mensagem de sucesso ou erro a cada comando executado; no entanto, poder consultar uma lista de centros de notificação é reconfortante.  O comando [da lista az-hub](/cli/azure/ext/notification-hub/notification-hub?view=azure-cli-latest#ext-notification-hub-az-notification-hub-list) foi projetado para este fim.

   ```azurecli
   az notification-hub list --resource-group spnhubrg --namespace-name spnhubns --output table
   ```

## <a name="work-with-notification-hub-access-policies"></a>Trabalhar com políticas de acesso ao centro de notificação

1. Enuse as políticas de acesso a um centro de notificação.

   O Azure Notification Hubs utiliza [a segurança de assinatura](/azure/notification-hubs/notification-hubs-push-notification-security) de acesso partilhado através da utilização de políticas de acesso.  Duas políticas são criadas automaticamente quando cria um centro de notificação.  As cadeias de ligação destas políticas são necessárias para configurar notificações push.  O comando [da lista de regras de autorização az-hub](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) fornece uma lista de nomes de política e respetivos grupos de recursos.

   ```azurecli
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Não utilize a política _de assinatura DefaultFulFuldAccesss na_ sua aplicação. Isto destina-se a ser utilizado apenas no seu back-end.  Utilize apenas `Listen` políticas de acesso na aplicação do seu cliente.

2. Crie uma nova regra de autorização para um centro de notificação.

   Se desejar criar regras de autorização adicionais com nomes significativos, pode criar e personalizar a sua própria política de acesso utilizando o comando [de autorização az-hub de autorização do hub.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create)  O `--rights` parâmetro é uma lista de autorizações delimitadas pelo espaço que pretende atribuir.

   ```azurecli
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Manage Send
   ```

3. Listar chaves e cadeias de conexão para uma política de acesso ao centro de notificação

   Existem dois conjuntos de chaves e cordas de ligação para cada política de acesso.  Mais tarde, vai precisar deles para [configurar um centro de notificação.](/azure/notification-hubs/configure-notification-hub-portal-pns-settings)  Para listar as cordas das chaves e ligações para uma política de acesso ao centro de notificação, utilize o comando [az notification-hub de regra de regra de lista-chaves.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys)

   ```azurecli
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output table
   ```

   ```azurecli
   #query the keys and connection strings for a custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > Um [espaço de nome de centro de notificação](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) e um centro de [notificação](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) têm políticas de acesso separadas.  Certifique-se de que está a utilizar a referência Azure CLI correta ao consultar as teclas e as cordas de ligação.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, utilize o [comando de eliminação](/cli/azure/group) do grupo AZ para remover o grupo de recursos e todos os recursos relacionados.

```azurecli
az group delete --name spnhubrg
```

## <a name="next-steps"></a>Próximos passos

* Neste arranque rápido, criou um centro de notificação. Para saber como configurar o hub com as definições do sistema de notificação da plataforma (PNS), consulte [Configurar notificações push num centro de notificação](configure-notification-hub-portal-pns-settings.md)

* Descubra as extensas capacidades de gestão de centros de notificações com o Azure CLI.

  [Lista completa de referência dos Centros de Notificação](/cli/azure/ext/notification-hub/notification-hub)

  [Lista de referência de espaço de nome de Centros de Notificação](/cli/azure/ext/notification-hub/notification-hub/namespace)

  [Lista de referência de regras de autorização de Centros de Notificação](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)

  [Lista de referência credenciais de Centros de Notificação](/cli/azure/ext/notification-hub/notification-hub/credential)
