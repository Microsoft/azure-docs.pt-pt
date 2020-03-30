---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: 16ce537a54fc77fc0f72b859d6d193501d86c1fc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "67184492"
---
## <a name="create-a-ruby-application"></a>Criar uma aplicação Ruby
Para obter instruções, consulte [Criar uma aplicação Ruby no Azure](../articles/virtual-machines/linux/classic/ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Configure a sua aplicação para usar o ônibus de serviço
Para utilizar o Service Bus, descarregue e use o pacote Azure Ruby, que inclui um conjunto de bibliotecas de conveniência que comunicam com os serviços REST de armazenamento.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizar RubyGems para obter o pacote
1. Utilize uma interface de linha de comandos, como **PowerShell** (Windows), **Terminal** (Mac), ou **Bash** (Unix).
2. Digite "gema instale azul" na janela de comando para instalar a gema e as dependências.

### <a name="import-the-package"></a>Importar o pacote
Utilizando o seu editor de texto favorito, adicione o seguinte ao topo do ficheiro Ruby no qual pretende utilizar o armazenamento:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Configurar uma ligação de ônibus de serviço
Utilize o seguinte código para definir os valores do espaço de nome, nome da chave, chave, signatário e anfitrião:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Delineie o valor do espaço de nome para o valor que criou em vez de todo o URL. Por exemplo, use **"your examplenamespace",** não "yourexamplenamespace.servicebus.windows.net".
