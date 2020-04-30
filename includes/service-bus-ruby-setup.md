---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: aec13c6beb8dbfcdd5f38e7f96b86bf03e42fa37
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80986711"
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

Ao trabalhar com vários espaços de nome, pode passar a `SharedAccessSigner` chave e o seu nome ao construtor enquanto cria objetos

```ruby
sb_namespace = '<your azure service bus namespace>'
sb_sas_key_name = '<your azure service bus access keyname>'
sb_sas_key = '<your azure service bus access key>'

signer = Azure::ServiceBus::Auth::SharedAccessSigner.new(sb_sas_key_name, sb_sas_key)
sb_host = "https://#{sb_namespace}.servicebus.windows.net"
```
