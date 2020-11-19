---
title: Ligue as funções Azure ao Armazenamento Azure utilizando ferramentas de linha de comando
description: Saiba como ligar as Funções Azure a uma fila de Armazenamento Azure adicionando uma ligação de saída ao projeto da linha de comando.
ms.date: 02/07/2020
ms.topic: quickstart
ms.custom: devx-track-python
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 6d0d559579e841553e04c26bf4dcc78934de3e95
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920207"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Ligue as funções Azure ao Armazenamento Azure utilizando ferramentas de linha de comando

Neste artigo, integra uma fila de Armazenamento Azure com a função e conta de armazenamento que criou no artigo de arranque rápido anterior. Obtém esta integração utilizando uma *ligação de saída* que escreve dados de um pedido HTTP para uma mensagem na fila. A conclusão deste artigo não incorre em custos adicionais para além dos poucos cêntimos usd do quickstart anterior. Para saber mais sobre encadernações, consulte [a Azure Functions triggers e encaderna os conceitos.](functions-triggers-bindings.md)

## <a name="configure-your-local-environment"></a>Configure o seu ambiente local

::: zone pivot="programming-language-csharp"  
Antes de começar, tem de completar o artigo, [Quickstart: Criar um projeto Azure Functions a partir da linha de comando](create-first-function-cli-csharp.md). Se já limpou recursos no final desse artigo, passe novamente pelos passos para recriar a app de função e recursos relacionados em Azure.  
::: zone-end  
::: zone pivot="programming-language-javascript"  
Antes de começar, tem de completar o artigo, [Quickstart: Criar um projeto Azure Functions a partir da linha de comando](create-first-function-cli-node.md). Se já limpou recursos no final desse artigo, passe novamente pelos passos para recriar a app de função e recursos relacionados em Azure.  
::: zone-end   
::: zone pivot="programming-language-java"  
Antes de começar, tem de completar o artigo, [Quickstart: Criar um projeto Azure Functions a partir da linha de comando](create-first-function-cli-java.md). Se já limpou recursos no final desse artigo, passe novamente pelos passos para recriar a app de função e recursos relacionados em Azure.  
::: zone-end   
::: zone pivot="programming-language-typescript"  
Antes de começar, tem de completar o artigo, [Quickstart: Criar um projeto Azure Functions a partir da linha de comando](create-first-function-cli-typescript.md). Se já limpou recursos no final desse artigo, passe novamente pelos passos para recriar a app de função e recursos relacionados em Azure.  
::: zone-end   
::: zone pivot="programming-language-python"  
Antes de começar, tem de completar o artigo, [Quickstart: Criar um projeto Azure Functions a partir da linha de comando](create-first-function-cli-python.md). Se já limpou recursos no final desse artigo, passe novamente pelos passos para recriar a app de função e recursos relacionados em Azure.  
::: zone-end   
::: zone pivot="programming-language-powershell"  
Antes de começar, tem de completar o artigo, [Quickstart: Criar um projeto Azure Functions a partir da linha de comando](create-first-function-cli-powershell.md). Se já limpou recursos no final desse artigo, passe novamente pelos passos para recriar a app de função e recursos relacionados em Azure.  
::: zone-end   

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end   

Para obter mais informações sobre os detalhes das ligações, consulte [o Azure Functions triggers e os conceitos de encadernação](functions-triggers-bindings.md) e [a configuração da saída da fila](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Adicione código para utilizar a ligação de saída

Com a ligação da fila definida, pode agora atualizar a sua função para receber o `msg` parâmetro de saída e escrever mensagens para a fila.

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

Observe que *não* precisa de escrever nenhum código para autenticação, obter uma referência de fila ou escrever dados. Todas estas tarefas de integração são manuseadas convenientemente no tempo de funcionamento e na ligação de saída da fila das funções Azure.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Veja a mensagem na fila de armazenamento do Azure

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

## <a name="redeploy-the-project-to-azure"></a>Recolocar o projeto para a Azure

Agora que verificou localmente que a função escreveu uma mensagem para a fila do Armazenamento Azure, pode recolocar o seu projeto para atualizar o ponto final em execução no Azure.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
Na pasta *LocalFunctionsProj,* utilize o [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) comando para recolocar o projeto, `<APP_NAME>` substituindo-o pelo nome da sua app.

```
func azure functionapp publish <APP_NAME>
```
::: zone-end  

::: zone pivot="programming-language-java" 

Na pasta do projeto local, utilize o seguinte comando Maven para republicar o seu projeto:
```
mvn azure-functions:deploy
```
::: zone-end

## <a name="verify-in-azure"></a>Verifique em Azure

1. Tal como no quickstart anterior, utilize um browser ou CURL para testar a função reafectada.

    # <a name="browser"></a>[Browser](#tab/browser)
    
    Copie o **URL completo de invocação** mostrado na saída do comando de publicação numa barra de endereços do navegador, anexando o parâmetro de consulta `&name=Functions` . O navegador deve apresentar uma saída semelhante à de quando executou a função localmente.

    ![A saída da função funciona no Azure num browser](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[caracóis](#tab/curl)
    
    Corra [`curl`](https://curl.haxx.se/) com o URL **invocado,** anexando o parâmetro `&name=Functions` . A saída do comando deve ser o texto"Olá Funções".
    
    ![A saída da função funciona em Azure usando CURL](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. Volte a examinar a fila de armazenamento, tal como descrito na secção anterior, para verificar se contém a nova mensagem escrita na fila.

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de terminar, use o seguinte comando para eliminar o grupo de recursos e todos os seus recursos contidos para evitar incorrer em custos adicionais.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Passos seguintes

Atualizou a sua função HTTP para escrever dados numa fila de armazenamento. Agora pode aprender mais sobre o desenvolvimento de funções a partir da linha de comando utilizando Ferramentas Core e Azure CLI:

+ [Trabalhar com o Azure Functions Core Tools](functions-run-local.md)  

+ [Funções Azure dispara e encaderna](functions-triggers-bindings.md)

::: zone pivot="programming-language-csharp"  
+ [Exemplos de projetos completos de funções em C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Referência do programador Azure Functions C#](functions-dotnet-class-library.md)  

[previous-quickstart]: create-first-function-cli-csharp.md

::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Exemplos de projetos de função completa no JavaScript.](/samples/browse/?products=azure-functions&languages=javascript)

+ [Azure Functions JavaScript guia de desenvolvedores](functions-reference-node.md)  

[previous-quickstart]: create-first-function-cli-javascript.md
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Exemplos de projetos de função completos no TypeScript](/samples/browse/?products=azure-functions&languages=typescript).

+ [Guia de desenvolvedores tipo de funções Azure](functions-reference-node.md#typescript)  

[previous-quickstart]: create-first-function-cli-typescript.md
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Exemplos de projetos completos de funções em Python.](/samples/browse/?products=azure-functions&languages=python)

+ [Guia de desenvolvedores de Azure Functions Python](functions-reference-python.md)  

[previous-quickstart]: create-first-function-cli-python.md
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Exemplos de projetos de função completos em PowerShell.](/samples/browse/?products=azure-functions&languages=azurepowershell)

+ [Guia de desenvolvedores powershell de funções Azure Functions](functions-reference-powershell.md) 

[previous-quickstart]: create-first-function-cli-powershell.md
::: zone-end
