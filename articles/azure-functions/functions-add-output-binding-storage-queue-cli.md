---
title: Ligue as funções azure ao armazenamento azure usando ferramentas de linha de comando
description: Aprenda a ligar as Funções Azure a uma fila de Armazenamento Azure adicionando uma ligação de saída ao seu projeto de linha de comando.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: f9d9573523083b6355f423b7b3db94b795d8657f
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673340"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Ligue as funções azure ao armazenamento azure usando ferramentas de linha de comando

Neste artigo, integra uma fila de Armazenamento Azure com a função e conta de armazenamento que criou no [quickstart anterior.](functions-create-first-azure-function-azure-cli.md) Obtém esta integração utilizando uma *ligação* de saída que escreve dados de um pedido http para uma mensagem na fila. A conclusão deste artigo não incorre em custos adicionais para além dos poucos cêntimos de USD do quickstart anterior. Para saber mais sobre encadernações, consulte o [Azure Functions desencadeia e encaderna conceitos.](functions-triggers-bindings.md)

## <a name="configure-your-local-environment"></a>Configure o seu ambiente local

Antes de começar, deve completar o artigo, [Quickstart: Crie um projeto de Funções Azure a partir da linha de comando](functions-create-first-azure-function-azure-cli.md). Se já limpou os recursos no final desse artigo, volte a passar pelos passos para recriar a app de funções e recursos relacionados no Azure.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end   

Para obter mais informações sobre os detalhes das ligações, consulte as [Funções Azure acionam e encaderna conceitos](functions-triggers-bindings.md) e [configuração](functions-bindings-storage-queue-output.md#configuration)de saída de fila .

## <a name="add-code-to-use-the-output-binding"></a>Adicione código para usar a ligação de saída

Com a ligação da fila definida, pode `msg` agora atualizar a sua função para receber o parâmetro de saída e escrever mensagens para a fila.

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

Observe que *não* precisa de escrever nenhum código para autenticação, obter uma referência na fila ou escrever dados. Todas estas tarefas de integração são convenientemente tratadas no tempo de funcionamento das Funções Azure e na ligação de saída da fila.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Veja a mensagem na fila do Armazenamento Azure

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

## <a name="redeploy-the-project-to-azure"></a>Reutilizar o projeto para o Azure

Agora que verificou localmente que a função escreveu uma mensagem para a fila de Armazenamento Azure, pode reimplantar o seu projeto para atualizar o ponto final em execução no Azure.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
Na pasta *LocalFunctionsProj,* [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) utilize o comando para reimplantar o projeto, substituindo`<APP_NAME>` o nome da sua aplicação.

```
func azure functionapp publish <APP_NAME>
```
::: zone-end  

::: zone pivot="programming-language-java" 

Na pasta do projeto local, utilize o seguinte comando Maven para reeditar o seu projeto:
```
mvn azure-functions:deploy
```
::: zone-end

## <a name="verify-in-azure"></a>Verifique em Azure

1. Tal como no quickstart anterior, utilize um navegador ou CURL para testar a função reimplantada.

    # <a name="browser"></a>[Browser](#tab/browser)
    
    Copie o **URL invocado** completo mostrado na saída do comando de publicação numa `&name=Functions`barra de endereços de navegador, anexando o parâmetro de consulta . O navegador deve apresentar uma saída semelhante à que executou a função localmente.

    ![A saída da função é executado em Azure em um navegador](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[caracol](#tab/curl)
    
    Corra [`curl`](https://curl.haxx.se/) com o **URL invocado,** afunilhe o parâmetro `&name=Functions`. A saída do comando deve ser o texto, "Olá Funções".
    
    ![A saída da função é executado em Azure usando CURL](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. Examine novamente a fila de armazenamento, conforme descrito na secção anterior, para verificar se contém a nova mensagem escrita na fila.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de terminar, use o seguinte comando para eliminar o grupo de recursos e todos os seus recursos contidos para evitar incorrer em custos adicionais.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Passos seguintes

Atualizou a sua função de http para escrever dados para uma fila de Armazenamento. Agora pode aprender mais sobre o desenvolvimento de Funções a partir da linha de comando usando Ferramentas Core e Azure CLI:

+ [Trabalhar com ferramentas centrais de funções azure](functions-run-local.md)  

::: zone pivot="programming-language-csharp"  
+ [Exemplos de projetos de função completa em C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Referência do desenvolvedor funções Azure C#](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Exemplos de projetos de função completa sintetmente em JavaScript](/samples/browse/?products=azure-functions&languages=javascript).

+ [Guia de desenvolvedores de funções azure JavaScript](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Exemplos de projetos de função completa supor script](/samples/browse/?products=azure-functions&languages=typescript).

+ [Guia de desenvolvedor de funções azure TypeScript](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Exemplos de projetos de função completa em Python.](/samples/browse/?products=azure-functions&languages=python)

+ [Guia de desenvolvimento de funções azure Python](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Exemplos de projetos de função completos no PowerShell](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Guia de desenvolvimento de funções Azure PowerShell](functions-reference-powershell.md) 
::: zone-end
+ [Funções Azure desencadeiam e encadernam](functions-triggers-bindings.md)

+ [Página de preços de funções](https://azure.microsoft.com/pricing/details/functions/)

+ [Estimativa dos custos do plano de consumo](functions-consumption-costs.md) 
