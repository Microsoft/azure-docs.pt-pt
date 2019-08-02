---
title: Como usar o armazenamento de fila do Ruby-armazenamento do Azure
description: Saiba como usar o serviço Fila do Azure para criar e excluir filas e inserir, obter e excluir mensagens. Exemplos escritos em Ruby.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: c7211bc805f4ed1d026faedbfdc9d53d3c1dfd93
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721298"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Como utilizar o Armazenamento de filas do Ruby
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral
Este guia mostra como executar cenários comuns usando o serviço de armazenamento de fila Microsoft Azure. Os exemplos são escritos usando a API do Azure do Ruby.
Os cenários cobertos incluem **Inserir**, **inspecionar**, **obter**e **excluir** mensagens da fila, bem como **criar e excluir filas**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Criar um aplicativo Ruby
Crie um aplicativo Ruby. Para obter instruções, consulte [criar um aplicativo Ruby no serviço de aplicativo no Linux](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby).

## <a name="configure-your-application-to-access-storage"></a>Configurar seu aplicativo para acessar o armazenamento
Para usar o armazenamento do Azure, você precisa baixar e usar o pacote do Azure do Ruby, que inclui um conjunto de bibliotecas de conveniência que se comunicam com os serviços REST de armazenamento.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizar RubyGems para obter o pacote
1. Utilize uma interface de linha de comandos, como **PowerShell** (Windows), **Terminal** (Mac), ou **Bash** (Unix).
2. Digite "gem install Azure" na janela de comando para instalar a gem e as dependências.

### <a name="import-the-package"></a>Importar o pacote
Use seu editor de texto favorito, adicione o seguinte à parte superior do arquivo Ruby em que você pretende usar o armazenamento:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Configurar uma conexão de armazenamento do Azure
O módulo do Azure lerá as variáveis de **ambiente\_Azure\_Storage Account** e **\_Azure\_Storage ACCESS_KEY** para obter informações necessárias para se conectar à sua conta de armazenamento do Azure. Se essas variáveis de ambiente não estiverem definidas, você deverá especificar as informações da conta antes de usar **Azure:: QueueService** com o seguinte código:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Para obter estes valores a partir de uma conta de armazenamento do Resource Manager ou clássica no portal do Azure:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Navegue até a conta de armazenamento que você deseja usar.
3. No painel Definições à direita, clique em **Chaves de Acesso**.
4. No painel Chaves de acesso que aparece, verá a chave de acesso 1 e a chave de acesso 2. Pode utilizar qualquer uma destas. 
5. Clique no ícone de cópia para copiar a chave para a área de transferência. 

## <a name="how-to-create-a-queue"></a>Como: Criar uma fila
O código a seguir cria um objeto **Azure:: QueueService** , que permite que você trabalhe com filas.

```ruby
azure_queue_service = Azure::QueueService.new
```

Use o método **create_queue ()** para criar uma fila com o nome especificado.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Como: Inserir uma mensagem em uma fila
Para inserir uma mensagem em uma fila, use o método **create_message ()** para criar uma nova mensagem e adicioná-la à fila.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Como: Inspecionar a próxima mensagem
Você pode inspecionar a mensagem na frente de uma fila sem removê-la da fila chamando o método **Peek\_Messages ()** . Por padrão, **inspecionar\_mensagens ()** exibe uma única mensagem. Você também pode especificar quantas mensagens deseja inspecionar.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Como: Remover a próxima mensagem da fila
Você pode remover uma mensagem de uma fila em duas etapas.

1. Quando você chama **listar\_mensagens ()** , você obtém a próxima mensagem em uma fila por padrão. Você também pode especificar quantas mensagens deseja obter. As mensagens retornadas **de\_listas de mensagens ()** tornam-se invisíveis para qualquer outra mensagem de leitura de código dessa fila. Você passa o tempo limite de visibilidade em segundos como um parâmetro.
2. Para concluir a remoção da mensagem da fila, você também deve chamar **Delete_message ()** .

Esse processo de duas etapas para remover uma mensagem garante que quando o código não processar uma mensagem devido a uma falha de hardware ou de software, outra instância do seu código poderá obter a mesma mensagem e tentar novamente. Seu código chama **excluir\_mensagem ()** logo após a mensagem ser processada.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: Alterar o conteúdo de uma mensagem em fila
Pode alterar os conteúdos de uma mensagem no local na fila de espera. O código a seguir usa o método **update_message ()** para atualizar uma mensagem. O método retornará uma tupla que contém o recebimento pop da mensagem da fila e um valor de data e hora UTC que representa quando a mensagem será visível na fila.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Como: Opções adicionais para o enfileiramento de mensagens
Existem duas formas através das quais pode personalizar a obtenção de mensagens a partir de uma fila.

1. Você pode obter um lote de mensagens.
2. Você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo que seu código tenha mais ou menos tempo para processar totalmente cada mensagem.

O exemplo de código a seguir usa o método **List\_Messages ()** para obter 15 mensagens em uma chamada. Em seguida, ele imprime e exclui cada mensagem. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Como: Obter o comprimento da fila
Você pode obter uma estimativa do número de mensagens na fila. O **método\_obter\_metadados de fila ()** solicita que o serviço fila retorne a contagem aproximada de mensagens e metadados sobre a fila.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Como: Excluir uma fila
Para excluir uma fila e todas as mensagens contidas nela, chame o **método\_Delete Queue ()** no objeto Queue.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Próximos Passos
Agora que você aprendeu os conceitos básicos do armazenamento de filas, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

* Visite o [blog da equipe de armazenamento do Azure](https://blogs.msdn.com/b/windowsazurestorage/)
* Visite o repositório [SDK do Azure para Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) no github

Para obter uma comparação entre o serviço fila do Azure abordado neste artigo e as filas do barramento de serviço do Azure discutidas no artigo [como usar filas do barramento de serviço](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/) , consulte [filas do Azure e filas do barramento de serviço – comparações e contrastes](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
