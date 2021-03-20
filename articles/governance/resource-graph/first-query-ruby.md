---
title: 'Quickstart: A sua primeira consulta ruby'
description: Neste arranque rápido, siga os passos para ativar a gema de Gráfico de Recurso para Ruby e executar a sua primeira consulta.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 41769359eaa1f930d34b0a182f35da38a6f5109d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98920042"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-ruby"></a>Quickstart: Executar a sua primeira consulta de Gráfico de Recurso usando Ruby

O primeiro passo para a utilização do Azure Resource Graph é verificar se estão instaladas as joias necessárias para a Ruby. Este quickstart acompanha-o através do processo de adicionar as joias à sua instalação Ruby.

No final deste processo, terá adicionado as joias à sua instalação Ruby e executará a sua primeira consulta de Gráfico de Recursos.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
- Um diretor de serviço da Azure, incluindo o _clienteId_ e _o clienteSecret._

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-graph-project"></a>Criar o projeto Gráfico de Recursos

Para permitir que a Ruby questione o Azure Resource Graph, a gema deve ser adicionada ao `Gemfile` . Esta joia funciona onde quer que a Ruby possa ser usada, incluindo com [a Azure Cloud Shell,](https://shell.azure.com) [bater no Windows 10,](/windows/wsl/install-win10)ou instalada localmente.

1. Verifique se o último Rubi está instalado (pelo menos **2.7.1**). Se ainda não estiver instalado, descarregue-o em [Ruby-Lang.org](https://www.ruby-lang.org/en/downloads/).

1. No seu ambiente ruby de eleição, inicialize um pacote numa nova pasta de projeto:

   ```bash
   # Initialize a bundle to create a new Gemfile
   bundle init
   ```

1. Atualize a sua `Gemfile` com as joias necessárias para o Azure Resource Graph. O ficheiro atualizado deve ser semelhante a este exemplo:

   ```file
   # frozen_string_literal: true

   source "https://rubygems.org"

   git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

   # gem "rails"
   gem 'azure_mgmt_resourcegraph', '~> 0.17.2'
   ```

1. Da pasta do projeto, `bundle install` corra. Confirme se as joias foram instaladas com `bundle list` .

1. Na mesma pasta de projeto, crie `argQuery.rb` com o seguinte código e guarde o ficheiro atualizado:

   ```ruby
   #!/usr/bin/env ruby

   require 'azure_mgmt_resourcegraph'
   ARG = Azure::ResourceGraph::Profiles::Latest::Mgmt

   # Get arguments and set options
   options = {
       tenant_id: ARGV[0],
       client_id: ARGV[1],
       client_secret: ARGV[2],
       subscription_id: ARGV[3]
   }

   # Create Resource Graph client from options
   argClient = ARG::Client.new(options)

   # Create Resource Graph QueryRequest for subscription with query
   query_request = ARGModels::QueryRequest.new.tap do |qr|
       qr.subscriptions = [ARGV[3]]
       qr.query = ARGV[4]
   end

   # Get the resources from Resource Graph
   response = argClient.resources(query_request)

   # Convert data to JSON and output
   puts response.data.to_json
   ```

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta do Resource Graph

Com o script Ruby guardado e pronto a ser usado, é hora de experimentar uma consulta simples de Gráfico de Recurso. A consulta devolve os primeiros cinco recursos Azure com o **Nome** e Tipo de **Recurso** de cada recurso.

Em cada chamada `argQuery` para, existem variáveis que são usadas que você precisa substituir por seus próprios valores:

- `{tenantId}` - Substitua-o pela sua ID do seu inquilino
- `{clientId}` - Substitua pelo ID do cliente do seu principal de serviço
- `{clientSecret}` - Substitua pelo segredo do cliente do seu diretor de serviço
- `{subscriptionId}` - substituir pelo ID da subscrição

1. Altere os diretórios para a pasta do projeto onde criou os `Gemfile` `argClient.rb` ficheiros e ficheiros.

1. Execute a sua primeira consulta de gráfico de recursos Azure usando as joias e o `resources` método:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > Como este exemplo de consulta não fornece um modificador de ordenação como `order by`, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido.

1. Altere o parâmetro final para `argQuery.rb` alterar a consulta para a propriedade `order by` **Nome:**

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > Assim como na primeira consulta, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido. A ordem dos comandos da consulta é importante. Neste exemplo, `order by` vem depois de `limit`, Esta ordem de comando limita primeiro os resultados da consulta e depois ordena-os.

1. Altere o parâmetro final para `argQuery.rb` e altere a consulta para primeiro `order by` a propriedade **Nome** e, em seguida, para os `limit` cinco melhores resultados:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

Quando a consulta final é executada várias vezes, assumindo que nada no seu ambiente está a mudar, os resultados devolvidos são consistentes e encomendados pela propriedade **Name,** mas ainda limitados aos cinco melhores resultados.

## <a name="clean-up-resources"></a>Limpar os recursos

Se desejar remover as joias instaladas do seu ambiente Ruby, pode fazê-lo utilizando o seguinte comando:

```bash
# Remove the installed gems from the Ruby environment
gem uninstall azure_mgmt_resourcegraph
```

> [!NOTE]
> A joia `azure_mgmt_resourcegraph` tem dependências como `ms_rest` e que também podem ter sido `ms_rest_azure` instaladas dependendo do seu ambiente. Pode desinstalar estas joias também se já não for necessário.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, adicionou as joias do Gráfico de Recurso ao seu ambiente Ruby e executou a sua primeira consulta. Para saber mais sobre a linguagem Resource Graph, continue na página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obtenha mais informações sobre o idioma de consulta](./concepts/query-language.md)
