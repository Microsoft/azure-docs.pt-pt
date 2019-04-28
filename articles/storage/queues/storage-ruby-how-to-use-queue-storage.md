---
title: Como utilizar o armazenamento de filas do Ruby | Documentos da Microsoft
description: Saiba como utilizar o serviço de fila do Azure para criar e eliminar filas, inserir, obter e eliminar mensagens. Exemplos de escritos em Ruby.
services: storage
author: tamram
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.subservice: queues
ms.openlocfilehash: 7ebb4326a8ec8a3382a5488ce3b966526bef446a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112009"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Como utilizar o Armazenamento de filas do Ruby
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral
Este guia mostra como executar tarefas comuns com o serviço de armazenamento de filas do Microsoft Azure. Os exemplos são escritos usando a API do Ruby do Azure.
Os cenários abrangidos incluem **inserindo**, **observação**, **introdução**, e **a eliminar** fila de mensagens, que  **criar e eliminar filas**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Criar uma aplicação Ruby
Crie uma aplicação Ruby. Para obter instruções, consulte [criar uma aplicação Ruby no serviço de aplicações no Linux](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby).

## <a name="configure-your-application-to-access-storage"></a>Configurar a sua aplicação para o armazenamento de acesso
Para utilizar o armazenamento do Azure, terá de transferir e utilizar o pacote Ruby do azure, que inclui um conjunto de bibliotecas de conveniência que comunicam com os serviços de REST de armazenamento.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizar RubyGems para obter o pacote
1. Utilize uma interface de linha de comandos, como **PowerShell** (Windows), **Terminal** (Mac), ou **Bash** (Unix).
2. Escreva "azure de instalação do gem" na janela de comando para instalar o gem e as dependências.

### <a name="import-the-package"></a>Importar o pacote
Utilizar o seu editor de texto favorito, adicione o seguinte na parte superior do ficheiro Ruby em que pretende usar o armazenamento:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Configurar uma ligação de armazenamento do Azure
O módulo do azure irá ler as variáveis de ambiente **AZURE\_armazenamento\_conta** e **AZURE\_armazenamento\_ACCESS_KEY** para obter informações necessário para ligar à sua conta de armazenamento do Azure. Se não forem definidas estas variáveis de ambiente, tem de especificar as informações da conta antes de utilizar **Azure::QueueService** com o código a seguir:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Para obter estes valores a partir de uma conta de armazenamento do Resource Manager ou clássica no portal do Azure:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Navegue para a conta de armazenamento que pretende utilizar.
3. No painel Definições à direita, clique em **Chaves de Acesso**.
4. No painel Chaves de acesso que aparece, verá a chave de acesso 1 e a chave de acesso 2. Pode utilizar qualquer uma destas. 
5. Clique no ícone de cópia para copiar a chave para a área de transferência. 

## <a name="how-to-create-a-queue"></a>Como: Criar uma fila
O código seguinte cria um **Azure::QueueService** objeto, que lhe permite trabalhar com as filas.

```ruby
azure_queue_service = Azure::QueueService.new
```

Utilize o **create_queue()** método para criar uma fila com o nome especificado.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Como: Introduzir uma mensagem numa fila
Para introduzir uma mensagem numa fila, utilize o **create_message()** método para criar uma nova mensagem e adicioná-lo para a fila.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Como: Pré-visualizar a mensagem seguinte
Pode pré-visualizar a mensagem no início de uma fila sem a remover da fila ao chamar o **peek\_messages()** método. Por predefinição, **peek\_messages()** peeks numa única mensagem. Também pode especificar quantas mensagens deseja observar.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Como: Remover da fila a mensagem seguinte
Pode remover uma mensagem da fila em dois passos.

1. Quando chama **lista\_messages()**, obterá a seguinte mensagem numa fila por predefinição. Também pode especificar quantas mensagens queira obter. As mensagens retornadas da **lista\_messages()** torna-se invisível para qualquer outro código lendo as mensagens desta fila. Passa o tempo limite de visibilidade em segundos, como um parâmetro.
2. Para concluir a remover a mensagem da fila, também tem de chamar **delete_message()**.

Este processo de dois passos da remoção de uma mensagem garante que quando o seu código não consegue processar uma mensagem devido uma falha de hardware ou software, outra instância do seu código pode obter a mesma mensagem e tente novamente. A código chama **elimine\_message()** imediatamente após a mensagem foi processada.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: Alterar os conteúdos de uma mensagem em fila
Pode alterar os conteúdos de uma mensagem no local na fila de espera. O código abaixo utiliza o **update_message()** método para atualizar uma mensagem. O método deve retornar uma cadeia de identificação que contém a receção de pop da mensagem de fila e um valor de hora de data UTC representa quando a mensagem estará visível na fila.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Como: Opções adicionais para remoção da fila de mensagens
Existem duas formas através das quais pode personalizar a obtenção de mensagens a partir de uma fila.

1. Pode obter um lote de mensagens.
2. Pode definir um tempo de limite de invisibilidade superiores ou inferiores, permitindo que seu código mais ou menos tempo para processar totalmente cada mensagem.

O seguinte código de exemplo utiliza a **lista\_messages()** método para obter 15 mensagens numa chamada. Em seguida, imprime e exclui cada mensagem. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Como: Obter o comprimento da fila
Pode obter uma estimativa do número de mensagens na fila. O **Obtenha\_fila\_metadata()** método pede ao serviço de fila para devolver o número aproximado de mensagens e os metadados sobre a fila.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Como: Eliminar uma fila
Para eliminar uma fila e todas as mensagens contidas no mesmo, chame o **elimine\_queue()** método no objeto de fila.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Próximos Passos
Agora que aprendeu as noções básicas do armazenamento de filas, siga estas ligações para saber mais sobre tarefas de armazenamento mais complexas.

* Visite o [blogue da equipa de armazenamento do Azure](https://blogs.msdn.com/b/windowsazurestorage/)
* Visite o [Azure SDK para Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) repositório no GitHub

Para obter uma comparação entre o Azure Queue Service discutidos neste artigo e discutidos de filas do Azure Service Bus a [como utilizar filas do Service Bus](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/) artigo, consulte [filas do Azure e filas do Service Bus - comparação e Contraste](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
