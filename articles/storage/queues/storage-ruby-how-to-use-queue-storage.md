---
title: Como utilizar o armazenamento de fila da Ruby - Armazenamento Azure
description: Aprenda a utilizar o serviço De Fila Azure para criar e apagar filas, e inserir, obter e apagar mensagens. Amostras escritas em Ruby.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: c7211bc805f4ed1d026faedbfdc9d53d3c1dfd93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68721298"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Como utilizar o Armazenamento de filas do Ruby
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral
Este guia mostra-lhe como realizar cenários comuns utilizando o serviço de armazenamento de filas Microsoft Azure. As amostras são escritas usando a API Ruby Azure.
Os cenários abordados incluem **a inserção,** **espreitar,** **receber**e **apagar** mensagens de fila, bem como criar e **apagar filas.**

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Criar uma aplicação Ruby
Crie uma aplicação Ruby. Para obter instruções, consulte [Criar uma aplicação Ruby no Serviço de Aplicações no Linux](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby).

## <a name="configure-your-application-to-access-storage"></a>Configure a sua aplicação para aceder ao armazenamento
Para utilizar o armazenamento Azure, é necessário descarregar e utilizar o pacote Ruby Azure, que inclui um conjunto de bibliotecas de conveniência que comunicam com os serviços REST de armazenamento.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizar RubyGems para obter o pacote
1. Utilize uma interface de linha de comandos, como **PowerShell** (Windows), **Terminal** (Mac), ou **Bash** (Unix).
2. Digite "gema instale azul" na janela de comando para instalar a gema e as dependências.

### <a name="import-the-package"></a>Importar o pacote
Use o seu editor de texto favorito, adicione o seguinte ao topo do ficheiro Ruby onde pretende utilizar o armazenamento:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Configurar uma ligação de armazenamento azure
O módulo azul irá ler as variáveis ambientais **AZURE\_\_STORAGE ACCOUNT** e **AZURE\_STORAGE\_ACCESS_KEY** para informações necessárias para se ligar à sua conta de armazenamento Azure. Se estas variáveis ambientais não forem definidas, deve especificar as informações da conta antes de utilizar **o Azure::QueueService** com o seguinte código:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Para obter estes valores a partir de uma conta de armazenamento do Resource Manager ou clássica no portal do Azure:

1. Faça login no [portal Azure.](https://portal.azure.com)
2. Navegue para a conta de armazenamento que pretende utilizar.
3. No painel Definições à direita, clique em **Chaves de Acesso**.
4. No painel Chaves de acesso que aparece, verá a chave de acesso 1 e a chave de acesso 2. Pode utilizar qualquer uma destas. 
5. Clique no ícone de cópia para copiar a chave para a área de transferência. 

## <a name="how-to-create-a-queue"></a>Como: Criar uma fila
O seguinte código cria um objeto **Azure::QueueService,** que lhe permite trabalhar com filas.

```ruby
azure_queue_service = Azure::QueueService.new
```

Utilize o método **create_queue()** para criar uma fila com o nome especificado.

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

## <a name="how-to-peek-at-the-next-message"></a>Como: Espreitar a próxima mensagem
Pode espreitar a mensagem na frente de uma fila sem a retirar da fila, ligando para o método das **mensagens de\_espreitar.** Por defeito, **as mensagens de espreitar()\_** espreitam uma única mensagem. Também pode especificar quantas mensagens pretende espreitar.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Como: Defilar a Próxima Mensagem
Pode remover uma mensagem de uma fila em dois passos.

1. Quando liga **\_para as mensagens da lista,()** recebe a próxima mensagem numa fila por defeito. Também pode especificar quantas mensagens pretende receber. As mensagens devolvidas a partir de **mensagens\_** de lista tornam-se invisíveis para quaisquer outras mensagens de leitura de código desta fila. Passas no tempo de visibilidade em segundos como parâmetro.
2. Para terminar de retirar a mensagem da fila, deve também ligar **para delete_message()**.

Este processo em duas etapas de remoção de uma mensagem garante que quando o seu código não processa uma mensagem devido a falha de hardware ou software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. As chamadas de código **eliminam\_a mensagem()** logo após a mensagem ter sido processada.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: Alterar o conteúdo de uma mensagem em fila
Pode alterar os conteúdos de uma mensagem no local na fila de espera. O código abaixo utiliza o método **update_message()** para atualizar uma mensagem. O método devolverá uma tuple que contém o recibo pop da mensagem de fila e um valor de data UTC que representa quando a mensagem será visível na fila.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Como: Opções adicionais para descodar mensagens
Existem duas formas através das quais pode personalizar a obtenção de mensagens a partir de uma fila.

1. Pode receber um lote de mensagens.
2. Pode definir um tempo de invisibilidade mais longo ou mais curto, permitindo ao seu código mais ou menos tempo para processar totalmente cada mensagem.

O exemplo de código que se segue utiliza o método **de mensagens da lista\_** para obter 15 mensagens numa chamada. Em seguida, imprime e elimina cada mensagem. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Como: Obter o comprimento da fila
Pode obter uma estimativa do número de mensagens na fila. O método de **\_obter metadados de fila()\_** pede ao serviço de fila que devolva a contagem de mensagens aproximada e metadados sobre a fila.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Como: Apagar uma fila
Para eliminar uma fila e todas as mensagens contidas na seleção, ligue para o método de **apagar\_a fila()** no objeto de fila.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu o básico do armazenamento de filas, siga estes links para aprender sobre tarefas de armazenamento mais complexas.

* Visite o Blog da Equipa de [Armazenamento Azure](https://blogs.msdn.com/b/windowsazurestorage/)
* Visite o [SDK Azure para](https://github.com/WindowsAzure/azure-sdk-for-ruby) o repositório Ruby no GitHub

Para uma comparação entre o Serviço de Fila Azure discutido neste artigo e as filas de ônibus de serviço Azure discutidas no artigo Como usar as filas de ônibus de [serviço,](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/) consulte filas de [ônibus Azure e filas de ônibus de serviço - Comparado e Contrastado](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
