---
title: Tutorial - Grupo de recipientes de gatilho por função Azure
description: Criar uma função PowerShell acionada por HTTP para automatizar a criação de instâncias de contentores Azure
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ec4b2273f6be6ea4aabed2b660e0b7553f861d0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89072048"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Tutorial: Utilize uma função Azure acionada por HTTP para criar um grupo de contentores

[Azure Functions](../azure-functions/functions-overview.md) é um serviço de computação sem servidor que pode executar scripts ou código em resposta a uma variedade de eventos, como um pedido HTTP, um temporizador ou uma mensagem numa fila de Armazenamento Azure.

Neste tutorial, cria-se uma função Azure que requer um pedido HTTP e aciona a implantação de um grupo de [contentores.](container-instances-container-groups.md) Este exemplo mostra o básico da utilização de Funções Azure para criar automaticamente recursos em Instâncias de Contentores Azure. Modifique ou estenda o exemplo para cenários mais complexos ou outros gatilhos de eventos. 

Saiba como:

> [!div class="checklist"]
> * Utilize o Código do Estúdio Visual com a [extensão Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para criar uma função básica de PowerShell acionada por HTTP.
> * Ativar uma identidade na aplicação de função e dar-lhe permissões para criar recursos Azure.
> * Modifique e reescreva a função PowerShell para automatizar a colocação de um grupo de contentores de um único contentor.
> * Verifique a colocação do recipiente acionado por HTTP.

## <a name="prerequisites"></a>Pré-requisitos

Consulte [Criar a sua primeira função em Azure utilizando o Código do Estúdio Visual](../azure-functions/functions-create-first-function-vs-code.md?pivots=programming-language-powershell#configure-your-environment) para pré-requisitos para instalar e utilizar o Código do Estúdio Visual com a extensão de Funções Azure no seu SISTEMA.

Passos adicionais neste artigo use Azure PowerShell. Se precisar de instalar ou atualizar, consulte [instalar a Azure PowerShell][azure-powershell-install] e [iniciar súm em Azure](/powershell/azure/get-started-azureps#sign-in-to-azure).

## <a name="create-a-basic-powershell-function"></a>Criar uma função básica PowerShell

Siga os passos na [Criar a sua primeira função PowerShell em Azure](../azure-functions/functions-create-first-function-vs-code.md?pivots=programming-language-powershell) para criar uma função PowerShell utilizando o modelo de gatilho HTTP. Utilize o nome de função Azure padrão **HttpTrigger**. Como mostrado no quickstart, teste a função localmente e publique o projeto numa aplicação de função em Azure. Este exemplo é uma função básica desencadeada por HTTP que devolve uma cadeia de texto. Em etapas posteriores neste artigo, modifica a função para criar um grupo de contentores.

Este artigo pressupõe que publique o projeto usando o nome *myfunctionapp,* num grupo de recursos Azure automaticamente nomeado de acordo com o nome da aplicação de funções (também *myfunctionapp).* Substitua o nome da aplicação de funções única e o nome do grupo de recursos em etapas posteriores.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Ativar uma identidade gerida pelo Azure na aplicação de função

Os seguintes comandos permitem uma [identidade gerida](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity) atribuída pelo sistema na sua aplicação de função. O anfitrião PowerShell que executa a aplicação pode autenticar automaticamente para o Azure usando esta identidade, permitindo que as funções tomem ações nos serviços Azure aos quais a identidade é dada acesso. Neste tutorial, você concede as permissões de identidade geridas para criar recursos no grupo de recursos da app de função. 

[Adicione uma identidade](../app-service/overview-managed-identity.md?tabs=dotnet#using-azure-powershell-1) à aplicação de função:

```powershell
Update-AzFunctionApp -Name myfunctionapp `
    -ResourceGroupName myfunctionapp `
    -IdentityType SystemAssigned
```

Atribuir a identidade a função de contribuinte a cargo do grupo de recursos:

```powershell
$SP=(Get-AzADServicePrincipal -DisplayName myfunctionapp).Id
$RG=(Get-AzResourceGroup -Name myfunctionapp).ResourceId
New-AzRoleAssignment -ObjectId $SP -RoleDefinitionName "Contributor" -Scope $RG
```

## <a name="modify-httptrigger-function"></a>Modificar a função HttpTrigger

Modifique o código PowerShell para a função **HttpTrigger** para criar um grupo de contentores. No ficheiro `run.ps1` para a função, encontre o seguinte bloco de código. Este código apresenta um valor de nome, se um for passado como uma cadeia de consulta no URL de função:

```powershell
[...]
if ($name) {
    $body = "Hello, $name. This HTTP triggered function executed successfully."
}
[...]
```

Substitua este código pelo seguinte bloco de exemplo. Aqui, se um valor de nome é passado na cadeia de consulta, é usado para nomear e criar um grupo de contentores usando o cmdlet [New-AzContainerGroup.][new-azcontainergroup] Certifique-se de substituir o nome do grupo de recursos *myfunctionapp* pelo nome do grupo de recursos para a sua aplicação de função:

```powershell
[...]
if ($name) {
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    if ($?) {
        $body = "This HTTP triggered function executed successfully. Started container group $name"
    }
    else  {
        $body = "There was a problem starting the container group."
    }
[...]
```

Este exemplo cria um grupo de contentores constituído por uma única instância de contentor que executa a `alpine` imagem. O contentor executa um único `echo` comando e termina. Num exemplo real, pode desencadear a criação de um ou mais grupos de contentores para executar um trabalho de lote.
 
## <a name="test-function-app-locally"></a>App de função de teste local

Certifique-se de que a função funciona localmente antes de republicar o projeto da aplicação de funções para o Azure. Quando funciona localmente, a função não cria recursos Azure. No entanto, pode testar o fluxo de função com e sem passar um valor de nome numa cadeia de consulta. Para depurar a função, consulte [as funções de Debug PowerShell Azure localmente](../azure-functions/functions-debug-powershell-local.md).

## <a name="republish-azure-function-app"></a>Reubejar app de função Azure

Depois de verificar que a função funciona localmente, reedita o projeto para a aplicação de função existente em Azure.

1. No Código do Estúdio Visual, abra a Paleta de Comando. Procure e selecione `Azure Functions: Deploy to Function App...` .
1. Selecione a pasta de trabalho atual para fechar e implementar.
1. Selecione a subscrição e, em seguida, o nome da app de função existente *(myfunctionapp*). Confirme se pretende substituir a colocação anterior.

Depois de criar a aplicação de funções, é apresentada uma notificação e o pacote de implementação é aplicado. Selecione **Ver Saída** nesta notificação para visualizar os resultados da criação e implementação, incluindo os recursos Azure que atualizou.

## <a name="run-the-function-in-azure"></a>Executar a função em Azure

Após a implementação concluída com sucesso, obtenha o URL de função. Por exemplo, utilize a área **Azure: Funções** no Código do Estúdio Visual para copiar o URL de função **HttpTrigger** ou obtenha o URL de função no [portal Azure](../azure-functions/functions-create-first-azure-function.md#test-the-function).

O URL de função é do formulário:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger
```

### <a name="run-function-without-passing-a-name"></a>Executar função sem passar um nome

Como primeiro teste, executar o `curl` comando e passar o URL de função sem anexar uma cadeia de `name` consulta. 

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger"
```

A função devolve o código de estado 200 e o `This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response` texto:

```
[...]
> GET /api/HttpTrigger? HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/1.1 200 OK
< Content-Length: 135
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:50:27 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.* Closing connection 0
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>Executar função e passar o nome de um grupo de contentores

Agora executar o `curl` comando e anexar o nome de um grupo de contentores (*mycontainergroup*) como uma cadeia de `?name=mycontainergroup` consulta:

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?name=mycontainergroup"
```

A função devolve o código de estado 200 e desencadeia a criação do grupo de contentores:

```
[...]
> GET /api/HttpTrigger1?name=mycontainergroup HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
< HTTP/1.1 200 OK
< Content-Length: 92
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:54:31 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Started container group mycontainergroup* Closing connection 0
```

Verifique se o contentor funciona com o comando [Get-AzContainerInstanceLog:][get-azcontainerinstancelog]

```azurecli
Get-AzContainerInstanceLog -ResourceGroupName myfunctionapp `
  -ContainerGroupName mycontainergroup 
```

Resultado do exemplo:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se já não precisar de nenhum dos recursos que criou neste tutorial, pode executar o comando [az-group-delete] para remover o grupo de recursos e todos os recursos que contém. Este comando elimina a aplicação de função que criou, bem como o recipiente de funcionamento, e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma função Azure que aceita um pedido HTTP e desencadeia a implantação de um grupo de contentores. Aprendeu a:

> [!div class="checklist"]
> * Utilize o Código do Estúdio Visual com a extensão Azure Functions para criar uma função básica de PowerShell acionada por HTTP.
> * Ativar uma identidade na aplicação de função e dar-lhe permissões para criar recursos Azure.
> * Modificar o código de função PowerShell para automatizar a implantação de um grupo de contentores de um único contentor.
> * Verifique a colocação do recipiente acionado por HTTP.

Para obter um exemplo detalhado para lançar e monitorizar um trabalho contentorizado, consulte o blog post [Containers Serverless driven Event com Funções PowerShell Azure e Instâncias de Contentores Azure](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) e amostra de [código](https://github.com/anthonychu/functions-powershell-run-aci)de acompanhamento .

Consulte a documentação do [Azure Functions](../azure-functions/index.yml) para obter orientações detalhadas sobre a criação de funções Azure e a publicação de um projeto de funções. 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-powershell-install]: /powershell/azure/install-az-ps
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[get-azcontainerinstancelog]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
