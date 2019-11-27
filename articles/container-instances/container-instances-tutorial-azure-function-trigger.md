---
title: Tutorial-disparar grupo de contêineres por função do Azure
description: Criar uma função do PowerShell sem servidor disparada por HTTP para automatizar a criação de instâncias de contêiner do Azure
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: ''
ms.openlocfilehash: 49eb0721972a92f33bda2532367bc78280b6e655
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533381"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Tutorial: usar uma função do Azure disparada por HTTP para criar um grupo de contêineres

[Azure Functions](../azure-functions/functions-overview.md) é um serviço de computação sem servidor que pode executar scripts ou código em resposta a uma variedade de eventos, como uma solicitação HTTP, um temporizador ou uma mensagem em uma fila de armazenamento do Azure.

Neste tutorial, você cria uma função do Azure que usa uma solicitação HTTP e dispara a implantação de um [grupo de contêineres](container-instances-container-groups.md). Este exemplo mostra as noções básicas do uso de Azure Functions para criar automaticamente recursos em instâncias de contêiner do Azure. Modifique ou estenda o exemplo para cenários mais complexos ou outros disparadores de eventos. 

Saiba como:

> [!div class="checklist"]
> * Use Visual Studio Code com a [extensão Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para criar uma função básica do PowerShell acionada por http.
> * Habilite uma identidade no aplicativo de funções e conceda a ela permissões para criar recursos do Azure.
> * Modifique e Republique a função do PowerShell para automatizar a implantação de um grupo de contêineres de contêiner único.
> * Verifique a implantação disparada por HTTP do contêiner.

> [!IMPORTANT]
> O PowerShell para Azure Functions está atualmente em visualização. As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="prerequisites"></a>Pré-requisitos

Consulte [criar sua primeira função do PowerShell no Azure](../azure-functions/functions-create-first-function-powershell.md#prerequisites) para obter os pré-requisitos para instalar e usar Visual Studio Code com o Azure Functions no seu sistema operacional.

Algumas etapas neste artigo usam o CLI do Azure. Você pode usar o Azure Cloud Shell ou uma instalação local do CLI do Azure para concluir essas etapas. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

## <a name="create-a-basic-powershell-function"></a>Criar uma função básica do PowerShell

Siga as etapas em [criar sua primeira função do PowerShell no Azure](../azure-functions/functions-create-first-function-powershell.md) para criar uma função do PowerShell usando o modelo de gatilho http. Use o nome da função do Azure padrão **HttpTrigger**. Conforme mostrado no guia de início rápido, teste a função localmente e publique o projeto em um aplicativo de funções no Azure. Este exemplo é uma função básica disparada por HTTP que retorna uma cadeia de texto. Em etapas posteriores neste artigo, você modifica a função para criar um grupo de contêineres.

Este artigo pressupõe que você publique o projeto usando o nome *myfunctionapp*, em um grupo de recursos do Azure automaticamente nomeado de acordo com o nome do aplicativo de funções (também *myfunctionapp*). Substitua o nome do aplicativo de funções exclusivo e o nome do grupo de recursos em etapas posteriores.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Habilitar uma identidade gerenciada pelo Azure no aplicativo de funções

Agora, habilite uma [identidade gerenciada](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#adding-a-system-assigned-identity) atribuída pelo sistema em seu aplicativo de funções. O host do PowerShell que executa o aplicativo pode se autenticar automaticamente usando essa identidade, permitindo que as funções executem ações nos serviços do Azure aos quais a identidade recebeu acesso. Neste tutorial, você concede as permissões de identidade gerenciadas para criar recursos no grupo de recursos do aplicativo de funções. 

Primeiro, use o comando [AZ Group show][az-group-show] para obter a ID do grupo de recursos do aplicativo de funções e armazená-lo em uma variável de ambiente. Este exemplo pressupõe que você execute o comando em um shell bash.

```azurecli
rgID=$(az group show --name myfunctionapp --query id --output tsv)
```

Execute [AZ functionapp Identity app Assign][az-functionapp-identity-app-assign] para atribuir uma identidade local ao aplicativo de funções e atribuir uma função de colaborador ao grupo de recursos. Essa função permite que a identidade crie recursos adicionais, como grupos de contêineres no grupo de recursos.

```azurecli
az functionapp identity assign \
  --name myfunctionapp \
  --resource-group myfunctionapp \
  --role contributor --scope $rgID
```

## <a name="modify-httptrigger-function"></a>Modificar função HttpTrigger

Modifique o código do PowerShell para a função **HttpTrigger** para criar um grupo de contêineres. No arquivo `run.ps1` para a função, localize o bloco de código a seguir. Esse código exibirá um valor de nome, se um for passado como uma cadeia de caracteres de consulta na URL da função:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
[...]
```

Substitua este código pelo bloco de exemplo a seguir. Aqui, se um valor de nome for passado na cadeia de caracteres de consulta, ele será usado para nomear e criar um grupo de contêineres usando o cmdlet [New-AzContainerGroup][new-azcontainergroup] . Certifique-se de substituir o nome do grupo de recursos *myfunctionapp* pelo nome do grupo de recursos para seu aplicativo de funções:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    $body = "Started container group $name"
}
[...]
```

Este exemplo cria um grupo de contêineres que consiste em uma única instância de contêiner que executa a imagem de `alpine`. O contêiner executa um único comando `echo` e, em seguida, termina. Em um exemplo do mundo real, você pode disparar a criação de um ou mais grupos de contêineres para executar um trabalho em lotes.
 
## <a name="test-function-app-locally"></a>Testar o aplicativo de funções localmente

Certifique-se de que a função seja executada corretamente localmente antes de republicar o projeto de aplicativo de funções no Azure. Conforme mostrado no [início rápido do PowerShell](../azure-functions/functions-create-first-function-powershell.md), insira um ponto de interrupção local no script do PowerShell e uma `Wait-Debugger` chamada acima dele. Para obter diretrizes de depuração, consulte [depurar o PowerShell Azure Functions localmente](../azure-functions/functions-debug-powershell-local.md).


## <a name="republish-azure-function-app"></a>Republicar o aplicativo de funções do Azure

Depois de verificar que a função é executada corretamente no computador local, é hora de republicar o projeto no aplicativo de funções existente no Azure.

> [!NOTE]
> Lembre-se de remover todas as chamadas para `Wait-Debugger` antes de publicar suas funções no Azure.

1. Em Visual Studio Code, abra a paleta de comandos. Procure e selecione `Azure Functions: Deploy to function app...`.
1. Selecione a pasta de trabalho atual para zip e implantar.
1. Selecione a assinatura e o nome do aplicativo de funções existente (*myfunctionapp*). Confirme que você deseja substituir a implantação anterior.

Depois de criar a aplicação de funções, é apresentada uma notificação e o pacote de implementação é aplicado. Selecione **Exibir saída** nesta notificação para exibir os resultados de criação e implantação, incluindo os recursos do Azure que você atualizou.

## <a name="run-the-function-in-azure"></a>Executar a função no Azure

Depois que a implantação for concluída com êxito, obtenha a URL da função. Por exemplo, use a área **Azure: Functions** no Visual Studio Code para copiar a URL da função **HttpTrigger** ou obter a url da função no [portal do Azure](../azure-functions/functions-create-first-azure-function.md#test-the-function).

A URL da função inclui um código exclusivo e está no formato:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==
```

### <a name="run-function-without-passing-a-name"></a>Executar função sem passar um nome

Como um primeiro teste, execute o comando `curl` e passe a URL da função sem acrescentar uma cadeia de caracteres de consulta `name`. Certifique-se de incluir o código exclusivo da função.

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M=="
```

A função retorna o código de status 400 e o texto `Please pass a name on the query string or in the request body`:

```
[...]
> GET /api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M== HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 400 
< content-length: 62
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:08:15 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Please pass a name on the query string or in the request body.
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>Executar a função e passar o nome de um grupo de contêineres

Agora, execute o comando `curl` acrescentando o nome de um grupo de contêineres (*MyContainer*Group) como uma cadeia de caracteres de consulta `&name=mycontainergroup`:

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup"
```

A função retorna o código de status 200 e dispara a criação do grupo de contêineres:

```
[...]
> GET /api/HttpTrigger?ode=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 200 
< content-length: 28
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:15:30 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Started container group mycontainergroup
```

Verifique se o contêiner foi executado com o comando [AZ container logs][az-container-logs] :

```azurecli
az container logs --resource-group myfunctionapp --name mycontainergroup
```

Resultado do exemplo:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais de nenhum dos recursos criados neste tutorial, poderá executar o comando [AZ Group Delete][az-group-delete] para remover o grupo de recursos e todos os recursos que ele contém. Este comando elimina o registo de contentor criado, bem como o contentor em execução, e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você criou uma função do Azure que usa uma solicitação HTTP e dispara a implantação de um grupo de contêineres. Aprendeu a:

> [!div class="checklist"]
> * Use Visual Studio Code com a extensão Azure Functions para criar uma função básica do PowerShell acionada por HTTP.
> * Habilite uma identidade no aplicativo de funções e conceda a ela permissões para criar recursos do Azure.
> * Modifique o código de função do PowerShell para automatizar a implantação de um grupo de contêineres de contêiner único.
> * Verifique a implantação disparada por HTTP do contêiner.

Para obter um exemplo detalhado para iniciar e monitorar um trabalho em contêiner, consulte a postagem no blog [contêineres sem servidor controlados por evento com o PowerShell Azure Functions e instâncias de contêiner do Azure](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) e o [exemplo de código](https://github.com/anthonychu/functions-powershell-run-aci)fornecido.

Consulte a [documentação do Azure Functions](/azure/azure-functions/) para obter orientações detalhadas sobre como criar o Azure Functions e publicar um projeto do functions. 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-functionapp-identity-app-assign]: /cli/azure/functionapp/identity#az-functionapp-identity-assign
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[az-container-logs]: /cli/azure/container#az-container-logs
