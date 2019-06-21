---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: 16ce537a54fc77fc0f72b859d6d193501d86c1fc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184492"
---
## <a name="create-a-ruby-application"></a>Criar uma aplicação Ruby
Para obter instruções, consulte [crie uma aplicação Ruby no Azure](../articles/virtual-machines/linux/classic/ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Configurar a sua aplicação para utilizar o Service Bus
Para utilizar o Service Bus, transfira e utilize o pacote de Ruby do Azure, que inclui um conjunto de bibliotecas de conveniência que comunicam com os serviços de REST de armazenamento.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizar RubyGems para obter o pacote
1. Utilize uma interface de linha de comandos, como **PowerShell** (Windows), **Terminal** (Mac), ou **Bash** (Unix).
2. Escreva "azure de instalação do gem" na janela de comando para instalar o gem e as dependências.

### <a name="import-the-package"></a>Importar o pacote
Com o seu editor de texto favorito, adicione o seguinte na parte superior do ficheiro Ruby no qual pretende usar o armazenamento:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Configurar uma ligação do Service Bus
Utilize o seguinte código para definir os valores de espaço de nomes, nome da chave, chave, signatário e anfitrião:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Defina o valor de espaço de nomes para o valor que criou em vez de todo o URL. Por exemplo, usar **"yourexamplenamespace"** , não "yourexamplenamespace.servicebus.windows.net".
