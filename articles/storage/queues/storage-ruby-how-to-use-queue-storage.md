---
title: Como utilizar o armazenamento de fila da Ruby - Azure Storage
description: Aprenda a utilizar o Azure Queue Storage para criar e apagar filas e inserir, obter e apagar mensagens. Amostras escritas em Ruby.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 12/08/2016
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 8558949e49bcf551c9276458d375fb9ac9636184
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97587667"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Como usar o armazenamento de fila da Ruby

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral

Este guia mostra-lhe como executar cenários comuns utilizando o serviço de armazenamento de fila microsoft Azure. As amostras são escritas usando a API Ruby Azure. Os cenários abordados incluem **inserir,** **espreitar,** **receber** e **apagar** mensagens de fila, bem como **criar e apagar filas.**

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Criar uma aplicação Ruby

Crie uma aplicação Ruby. Para obter instruções, consulte [Criar uma aplicação Ruby no Serviço de Aplicações em Linux](../../app-service/quickstart-ruby.md).

## <a name="configure-your-application-to-access-storage"></a>Configure a sua aplicação para aceder ao armazenamento

Para utilizar o Azure Storage, você precisa baixar e usar o pacote Ruby Azure, que inclui um conjunto de bibliotecas de conveniência que comunicam com os serviços DE ARMAZENAMENTO REST.

<!-- docutune:ignore Terminal -->

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizar RubyGems para obter o pacote

1. Utilize uma interface de linha de comandos, como PowerShell (Windows), Terminal (Mac), ou Bash (Unix).
2. Digite `gem install Azure` na janela de comando para instalar a gema e as dependências.

### <a name="import-the-package"></a>Importar o pacote

Use o seu editor de texto favorito, adicione o seguinte ao topo do ficheiro Ruby onde pretende utilizar o armazenamento:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Configurar uma ligação de armazenamento Azure

O módulo Azure irá ler as variáveis ambientais `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_ACCESS_KEY` para obter informações necessárias para ligar à sua conta de Armazenamento Azure. Se estas variáveis ambientais não estiverem definidas, deve especificar as informações da conta antes de utilizar `Azure::QueueService` com o seguinte código:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Para obter estes valores a partir de uma conta de armazenamento do Resource Manager ou clássica no portal do Azure:

1. Faça login no [portal Azure](https://portal.azure.com).
2. Navegue para a conta de armazenamento que pretende utilizar.
3. Na lâmina **de Definições** à direita, clique em **Teclas de acesso**.
4. Na lâmina **'Teclas de acesso'** que aparece, verá a chave de acesso 1 e a chave de acesso 2. Pode utilizar qualquer uma destas.
5. Clique no ícone de cópia para copiar a chave para a área de transferência.

## <a name="how-to-create-a-queue"></a>Como: Criar uma fila

O código a seguir cria um `Azure::QueueService` objeto, que lhe permite trabalhar com filas.

```ruby
azure_queue_service = Azure::QueueService.new
```

Utilize o `create_queue()` método para criar uma fila com o nome especificado.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Como: Inserir uma mensagem numa fila

Para inserir uma mensagem numa fila, utilize o `create_message()` método para criar uma nova mensagem e adicione-a à fila.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Como: Espreitar a próxima mensagem

Pode espreitar a mensagem na frente de uma fila sem a retirar da fila, chamando o `peek_messages()` método. Por padrão, `peek_messages()` espreita uma única mensagem. Também pode especificar quantas mensagens pretende espreitar.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Como: Dequeue a próxima mensagem

Pode remover uma mensagem de uma fila em dois passos.

1. Quando `list_messages()` ligar, recebe a próxima mensagem numa fila por defeito. Também pode especificar quantas mensagens pretende receber. As mensagens devolvidas `list_messages()` tornam-se invisíveis a qualquer outra mensagem de leitura de código desta fila. Passa-se o tempo limite de visibilidade em segundos como parâmetro.
2. Para terminar de remover a mensagem da fila, também deve ligar `delete_message()` .

Este processo em duas etapas de remoção de uma mensagem assegura que quando o seu código não processa uma mensagem devido a falha de hardware ou software, outra instância do seu código pode obter a mesma mensagem e tentar novamente. O seu código liga `delete_message()` logo após a mensagem ter sido processada.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue",
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: Alterar o conteúdo de uma mensagem em fila

Pode alterar os conteúdos de uma mensagem no local na fila de espera. O seguinte código utiliza o `update_message()` método para atualizar uma mensagem. O método devolverá um tuple que contenha o recibo pop da mensagem de fila e um valor UTC `DateTime` que representa quando a mensagem será visível na fila.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message",
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Como: Opções adicionais para desescodução de mensagens

Existem duas formas através das quais pode personalizar a obtenção de mensagens a partir de uma fila.

1. Pode receber um lote de mensagem.
2. Pode definir um tempo de invisibilidade mais longo ou curto, permitindo ao seu código mais ou menos tempo para processar totalmente cada mensagem.

O seguinte exemplo de código utiliza o `list_messages()` método para obter 15 mensagens numa única chamada. Em seguida, imprime e elimina cada mensagem. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Como: Obter o comprimento da fila

Pode obter uma estimativa do número de mensagens na fila. O `get_queue_metadata()` método devolve a contagem de mensagens aproximada e outros metadados de fila.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Como: Apagar uma fila

Para eliminar uma fila e todas as mensagens contidas, ligue para o `delete_queue()` método no objeto da fila.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu o básico do Armazenamento de Fila, siga estes links para aprender sobre tarefas de armazenamento mais complexas.

- Visite o blog da equipa de [armazenamento Azure](/archive/blogs/windowsazurestorage/)
- Visite o [Azure SDK para](https://github.com/WindowsAzure/azure-sdk-for-ruby) repositório ruby no GitHub

Para uma comparação entre a Azure Queue Storage discutida neste artigo e as filas de autocarros da Azure Service discutidas em [Como usar as filas de Service Bus](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/), ver [Azure Queue Storage e Service Bus - em comparação e contraste](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
