---
title: Como usar o armazenamento da fila da Ruby - Azure Storage
description: Aprenda a utilizar o serviço Azure Queue para criar e apagar filas e inserir, obter e apagar mensagens. Amostras escritas em Ruby.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: bb7619500cc142eca52ca0a1a6e0b670e6b8f51a
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425468"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Como utilizar o Armazenamento de filas do Ruby
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral
Este guia mostra-lhe como executar cenários comuns utilizando o serviço de armazenamento de fila microsoft Azure. As amostras são escritas usando a API Ruby Azure.
Os cenários abordados incluem **inserir,** **espreitar,** **receber**e **apagar** mensagens de fila, bem como **criar e apagar filas.**

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Criar uma aplicação Ruby
Crie uma aplicação Ruby. Para obter instruções, consulte [Criar uma Aplicação Ruby no Serviço de Aplicações em Linux](/azure/app-service/quickstart-ruby).

## <a name="configure-your-application-to-access-storage"></a>Configure a sua aplicação para aceder ao armazenamento
Para utilizar o armazenamento Azure, você precisa baixar e usar o pacote Ruby azure, que inclui um conjunto de bibliotecas de conveniência que comunicam com os serviços DE ARMAZENAMENTO REST.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizar RubyGems para obter o pacote
1. Utilize uma interface de linha de comandos, como **PowerShell** (Windows), **Terminal** (Mac), ou **Bash** (Unix).
2. Digite "gema instalar azul" na janela de comando para instalar a gema e as dependências.

### <a name="import-the-package"></a>Importar o pacote
Use o seu editor de texto favorito, adicione o seguinte ao topo do ficheiro Ruby onde pretende utilizar o armazenamento:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Configurar uma ligação de armazenamento Azure
O módulo azul irá ler as variáveis ambientais **AZURE \_ STORAGE \_ ACCOUNT** e **AZURE \_ STORAGE \_ ACCESS_KEY** para obter informações necessárias para ligar à sua conta de armazenamento Azure. Se estas variáveis ambientais não estiverem definidas, deve especificar as informações da conta antes de utilizar o **Azure::QueueService** com o seguinte código:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Para obter estes valores a partir de uma conta de armazenamento do Resource Manager ou clássica no portal do Azure:

1. Faça login no [portal Azure](https://portal.azure.com).
2. Navegue para a conta de armazenamento que pretende utilizar.
3. No painel Definições à direita, clique em **Chaves de Acesso**.
4. No painel Chaves de acesso que aparece, verá a chave de acesso 1 e a chave de acesso 2. Pode utilizar qualquer uma destas. 
5. Clique no ícone de cópia para copiar a chave para a área de transferência. 

## <a name="how-to-create-a-queue"></a>Como: Criar uma fila
O seguinte código cria um **Azure::QueueService** objeto, que lhe permite trabalhar com filas.

```ruby
azure_queue_service = Azure::QueueService.new
```

Utilize o método **create_queue** para criar uma fila com o nome especificado.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Como: Inserir uma mensagem numa fila
Para inserir uma mensagem numa fila, utilize o método **create_message()** para criar uma nova mensagem e adicioná-la à fila.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Como: Espreitar a Próxima Mensagem
Pode espreitar a mensagem na parte da frente de uma fila sem a retirar da fila, chamando o método de ** \_ espreitar mensagens().** Por predefinição, ** \_ as mensagens espreitam** uma única mensagem. Também pode especificar quantas mensagens pretende espreitar.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Como: Dequeue a Próxima Mensagem
Pode remover uma mensagem de uma fila em dois passos.

1. Quando liga ** \_ mensagens de lista()**, obtém a próxima mensagem numa fila por defeito. Também pode especificar quantas mensagens pretende receber. As mensagens devolvidas a partir de mensagens de ** \_ lista()** tornam-se invisíveis para qualquer outra mensagem de leitura de código desta fila. Passa-se o tempo limite de visibilidade em segundos como parâmetro.
2. Para terminar a remoção da mensagem da fila, também deve chamar **delete_message()**.

Este processo em duas etapas de remoção de uma mensagem assegura que quando o seu código não processa uma mensagem devido a falha de hardware ou software, outra instância do seu código pode obter a mesma mensagem e tentar novamente. As chamadas de código **apagam \_ a mensagem** imediatamente após o processo da mensagem.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: Alterar o conteúdo de uma mensagem em fila
Pode alterar os conteúdos de uma mensagem no local na fila de espera. O código abaixo utiliza o método **update_message()** para atualizar uma mensagem. O método devolverá um tuple que contenha o recibo pop da mensagem de fila e um valor de hora de data UTC que representa quando a mensagem será visível na fila.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Como: Opções adicionais para desescoding de mensagens
Existem duas formas através das quais pode personalizar a obtenção de mensagens a partir de uma fila.

1. Pode receber um lote de mensagem.
2. Pode definir um tempo de invisibilidade mais longo ou curto, permitindo ao seu código mais ou menos tempo para processar totalmente cada mensagem.

O exemplo de código a seguir utiliza o método ** \_ de mensagens de lista()** para obter 15 mensagens numa chamada. Em seguida, imprime e elimina cada mensagem. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Como: Obter o comprimento da fila
Pode obter uma estimativa do número de mensagens na fila. O método **get \_ fila \_ metadados()** solicita ao serviço de fila para devolver a contagem de mensagens e metadados aproximados sobre a fila.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Como: Eliminar uma fila
Para eliminar uma fila e todas as mensagens contidas nela, ligue para o método **de apagar a fila \_ no** objeto da fila.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu o básico do armazenamento de filas, siga estes links para aprender sobre tarefas de armazenamento mais complexas.

* Visite o [Azure Storage Team Blog](/archive/blogs/windowsazurestorage/)
* Visite o [Azure SDK para](https://github.com/WindowsAzure/azure-sdk-for-ruby) repositório ruby no GitHub

Para uma comparação entre o Azure Queue Service discutido neste artigo e as filas de autocarros da Azure Service discutidas no artigo como usar as [filas de autocarros](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/) de serviço, consulte [as filas de autocarros do Azure e as filas de autocarros de serviço - Comparadas e Contrastadas](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
