---
title: 'Quickstart: A sua primeira consulta go'
description: Neste arranque rápido, siga os passos para ativar o pacote De Gráfico de Recurso para Go e executar a sua primeira consulta.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 4949801b3dc97904680d09e685fd225812a0e14d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98920067"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-go"></a>Quickstart: Executar a sua primeira consulta de Gráfico de Recurso usando Go

O primeiro passo para a utilização do Azure Resource Graph é verificar se as embalagens necessárias para o Go estão instaladas. Este quickstart acompanha-o através do processo de adicionar os pacotes à sua instalação Go.

No final deste processo, terá adicionado os pacotes à instalação go e executará a sua primeira consulta de Gráfico de Recurso.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="add-the-resource-graph-package"></a>Adicione o pacote de Gráfico de Recurso

Para ativar o Go consultar o Azure Resource Graph, o pacote deve ser adicionado. Este pacote funciona onde quer que o Go possa ser utilizado, incluindo [a bash no Windows 10](/windows/wsl/install-win10) ou instalada localmente.

1. Verifique se o último Go está instalado (pelo menos **1.14**). Se ainda não estiver instalado, descarregue-o em [Golang.org](https://golang.org/dl/).

1. Verifique se o último CLI do Azure está instalado (pelo menos **2.5.1**). Se ainda não estiver instalado, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

   > [!NOTE]
   > O Azure CLI é necessário para permitir que o Go utilize o `auth.NewAuthorizerFromCLI()` método no seguinte exemplo. Para obter informações sobre outras opções, consulte [Azure SDK para Go - Mais detalhes de autenticação](https://github.com/Azure/azure-sdk-for-go#more-authentication-details).

1. Autenticar através do Azure CLI.

   ```azurecli
   az login
   ```

1. No seu ambiente de eleição Go, instale os pacotes necessários para o Gráfico de Recursos Azure:

   ```bash
   # Add the Resource Graph package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta do Resource Graph

Com os pacotes Go adicionados ao seu ambiente de eleição, é hora de experimentar uma consulta simples de Gráfico de Recurso. A consulta devolve os primeiros cinco recursos Azure com o **Nome** e Tipo de **Recurso** de cada recurso.

1. Crie a aplicação Go e guarde a seguinte fonte `argQuery.go` como:

   ```Go
   package main
   
   import (
      "fmt"
      "os"
      "context"
      "strconv"
      arg "github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph"
      "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
       // Get variables from command line arguments
       var query = os.Args[1]
       var subList = os.Args[2:]
   
       // Create and authorize a ResourceGraph client
       argClient := arg.New()
       authorizer, err := auth.NewAuthorizerFromCLI()
       if err == nil {
           argClient.Authorizer = authorizer
       } else {
           fmt.Printf(err.Error())
       }
     
       // Set options
       RequestOptions := arg.QueryRequestOptions {
           ResultFormat: "objectArray",
       }
     
       // Create the query request
       Request := arg.QueryRequest {
           Subscriptions: &subList,
           Query: &query,
           Options: &RequestOptions,
       }
     
       // Run the query and get the results
       var results, queryErr = argClient.Resources(context.Background(), Request)
       if queryErr == nil {
           fmt.Printf("Resources found: " + strconv.FormatInt(*results.TotalRecords, 10) + "\n")
           fmt.Printf("Results: " + fmt.Sprint(results.Data) + "\n")
       } else {
           fmt.Printf(queryErr.Error())
       }
   }
   ```

1. Construa a aplicação Go:

   ```bash
   go build argQuery.go
   ```

1. Execute a sua primeira consulta de gráfico de recursos Azure utilizando a aplicação Go compilada. `<SubID>`Substitua-o pelo seu ID de subscrição:

   ```bash
   argQuery "Resources | project name, type | limit 5" "<SubID>"
   ```

   > [!NOTE]
   > Como este exemplo de consulta não fornece um modificador de ordenação como `order by`, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido.

1. Altere o primeiro parâmetro para `argQuery` e altere a consulta para `order by` a propriedade **Nome.** `<SubID>`Substitua-o pelo seu ID de subscrição:

   ```bash
   argQuery "Resources | project name, type | limit 5 | order by name asc" "<SubID>"
   ```

   > [!NOTE]
   > Assim como na primeira consulta, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido. A ordem dos comandos da consulta é importante. Neste exemplo, `order by` vem depois de `limit`, Esta ordem de comando limita primeiro os resultados da consulta e depois ordena-os.

1. Altere o primeiro parâmetro para `argQuery` e altere a consulta para primeiro `order by` a propriedade **Nome** e, em seguida, para os `limit` cinco melhores resultados. `<SubID>`Substitua-o pelo seu ID de subscrição:

   ```bash
   argQuery "Resources | project name, type | order by name asc | limit 5" "<SubID>"
   ```

Quando a consulta final é executada várias vezes, assumindo que nada no seu ambiente está a mudar, os resultados devolvidos são consistentes e encomendados pela propriedade **Name,** mas ainda limitados aos cinco melhores resultados.

## <a name="clean-up-resources"></a>Limpar os recursos

Se desejar remover as embalagens instaladas do ambiente Go, pode fazê-lo utilizando o seguinte comando:

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, adicionou os pacotes de Gráfico de Recurso ao ambiente Go e executou a sua primeira consulta. Para saber mais sobre a linguagem Resource Graph, continue na página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obtenha mais informações sobre o idioma de consulta](./concepts/query-language.md)