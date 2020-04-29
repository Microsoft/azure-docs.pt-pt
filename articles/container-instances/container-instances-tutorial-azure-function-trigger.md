---
title: Tutorial - Grupo de contentores de gatilho por função Azure
description: Criar uma função PowerShell ativada por HTTP para automatizar a criação de instâncias de contentores Azure
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: ''
ms.openlocfilehash: 9dbb22a2449e4c41bff802ab827da4489fc7ffeb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78331030"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Tutorial: Utilize uma função Azure desencadeada por HTTP para criar um grupo de contentores

[O Azure Functions](../azure-functions/functions-overview.md) é um serviço de computação sem servidor que pode executar scripts ou código em resposta a uma variedade de eventos, tais como um pedido HTTP, um temporizador ou uma mensagem numa fila de Armazenamento Azure.

Neste tutorial, cria-se uma função Azure que requer um pedido HTTP e desencadeie a implantação de um grupo de [contentores](container-instances-container-groups.md). Este exemplo mostra o básico da utilização de Funções Azure para criar automaticamente recursos em instâncias de contentores Azure. Modifique ou estenda o exemplo para cenários mais complexos ou outros gatilhos de eventos. 

Saiba como:

> [!div class="checklist"]
> * Utilize o Código de Estúdio Visual com a [extensão funções Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para criar uma função básica de PowerShell ativada por HTTP.
> * Ative uma identidade na aplicação de funções e dê-lhe permissões para criar recursos Azure.
> * Modificar e reeditar a função PowerShell para automatizar a implantação de um grupo de contentores de um único contentor.
> * Verifique a implantação do recipiente ativado em HTTP.

> [!IMPORTANT]
> PowerShell para Funções Azure está atualmente em pré-visualização. As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="prerequisites"></a>Pré-requisitos

Consulte [Criar a sua primeira função em Azure](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-powershell#configure-your-environment) para pré-requisitos para instalar e utilizar o Código de Estúdio Visual com as Funções Azure no seu SISTEMA.

Alguns passos neste artigo usam o Azure CLI. Pode utilizar a Casca de Nuvem Azure ou uma instalação local do Azure CLI para completar estes passos. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="create-a-basic-powershell-function"></a>Criar uma função básica powerShell

Siga os passos em Criar a [sua primeira função PowerShell em Azure](../azure-functions/functions-create-first-function-powershell.md) para criar uma função PowerShell utilizando o modelo http Trigger. Utilize o nome de função de função Azure padrão **HttpTrigger**. Como mostrado no arranque rápido, teste a função localmente e publique o projeto numa aplicação de função em Azure. Este exemplo é uma função básica desencadeada por HTTP que devolve uma cadeia de texto. Em etapas posteriores neste artigo, modifica-se a função para criar um grupo de contentores.

Este artigo assume que publica o projeto usando o nome *myfunctionapp*, num grupo de recursos Azure automaticamente nomeado de acordo com o nome da app de função (também *myfunctionapp).* Substitua o nome da sua aplicação de função única e o nome do grupo de recursos em etapas posteriores.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Ativar uma identidade gerida pelo Azure na aplicação de função

Agora, ative uma [identidade gerida](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity) atribuída pelo sistema na sua aplicação de função. O anfitrião powerShell que executa a aplicação pode autenticar automaticamente usando esta identidade, permitindo que as funções tomem ações sobre os serviços Do Azure a que a identidade foi dada acesso. Neste tutorial, você concede as permissões de identidade geridas para criar recursos no grupo de recursos da app de função. 

Primeiro use o comando de show do [grupo AZ][az-group-show] para obter a identificação do grupo de recursos da aplicação de função e armazená-lo em uma variável ambiental. Este exemplo pressupõe que dirige o comando numa concha bash.

```azurecli
rgID=$(az group show --name myfunctionapp --query id --output tsv)
```

Executar app [de identidade az functionapp atribuir][az-functionapp-identity-app-assign] para atribuir uma identidade local à aplicação de função e atribuir uma função de contribuinte ao grupo de recursos. Esta função permite que a identidade crie recursos adicionais, como grupos de contentores no grupo de recursos.

```azurecli
az functionapp identity assign \
  --name myfunctionapp \
  --resource-group myfunctionapp \
  --role contributor --scope $rgID
```

## <a name="modify-httptrigger-function"></a>Modificar função HttpTrigger

Modifique o código PowerShell para a função **HttpTrigger** para criar um grupo de contentores. No `run.ps1` ficheiro da função, encontre o seguinte bloco de código. Este código apresenta um valor de nome, se um for passado como uma cadeia de consulta no URL de função:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
[...]
```

Substitua este código pelo seguinte bloco de exemplo. Aqui, se um valor de nome for passado na cadeia de consulta, é usado para nomear e criar um grupo de contentores usando o cmdlet [New-AzContainerGroup.][new-azcontainergroup] Certifique-se de substituir o nome de grupo de recursos *myfunctionapp* pelo nome do grupo de recursos para a sua aplicação de função:

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

Este exemplo cria um grupo de contentores `alpine` constituído por uma única instância de contentores que executa a imagem. O contentor executa um único `echo` comando e termina. Num exemplo do mundo real, você pode desencadear a criação de um ou mais grupos de contentores para executar um trabalho de lote.
 
## <a name="test-function-app-locally"></a>App de função de teste local

Certifique-se de que a função funciona corretamente localmente antes de reeditar o projeto da aplicação de funções para o Azure. Como mostrado no [quickstart PowerShell,](../azure-functions/functions-create-first-function-powershell.md)insira um ponto de `Wait-Debugger` rutura local no script PowerShell e uma chamada acima dele. Para obter orientação de depuração, consulte [debug PowerShell Azure Functions localmente](../azure-functions/functions-debug-powershell-local.md).


## <a name="republish-azure-function-app"></a>Republique a aplicação de função Azure

Depois de verificar que a função funciona corretamente no seu computador local, é hora de reeditar o projeto para a aplicação de funções existente em Azure.

> [!NOTE]
> Lembre-se de `Wait-Debugger` remover quaisquer chamadas antes de publicar as suas funções no Azure.

1. No Código do Estúdio Visual, abra a Paleta de Comando. Procure e `Azure Functions: Deploy to function app...`selecione .
1. Selecione a atual pasta de trabalho para fechar e implementar.
1. Selecione a subscrição e, em seguida, o nome da aplicação de funções existente *(myfunctionapp).* Confirme que pretende substituir a implantação anterior.

Depois de criar a aplicação de funções, é apresentada uma notificação e o pacote de implementação é aplicado. Selecione **Ver Output** nesta notificação para visualizar os resultados da criação e implementação, incluindo os recursos Azure que atualizou.

## <a name="run-the-function-in-azure"></a>Executar a função em Azure

Depois de a implementação completar com sucesso, obtenha o URL de função. Por exemplo, utilize a área **Azure: Funções** no código estúdio visual para copiar o URL de função **HttpTrigger** ou obter o URL de função no [portal Azure](../azure-functions/functions-create-first-azure-function.md#test-the-function).

O URL de função inclui um código único e é do formulário:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==
```

### <a name="run-function-without-passing-a-name"></a>Executar função sem passar um nome

Como primeiro teste, `curl` execute o comando e passe `name` o URL de função sem apencarar uma corda de consulta. Certifique-se de incluir o código único da sua função.

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M=="
```

A função devolve o código `Please pass a name on the query string or in the request body`de estado 400 e o texto:

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

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>Executar função e passar o nome de um grupo de contentores

Agora, `curl` execute o comando, anexando o nome de um grupo `&name=mycontainergroup`de*contentores (mycontainergroup*) como uma corda de consulta:

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup"
```

A função devolve o código de estado 200 e desencadeia a criação do grupo de contentores:

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

Verifique se o recipiente funcionava com o comando de troncos de [contentora az:][az-container-logs]

```azurecli
az container logs --resource-group myfunctionapp --name mycontainergroup
```

Resultado do exemplo:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisar de nenhum dos recursos que criou neste tutorial, pode executar o comando de eliminação do [grupo AZ][az-group-delete] para remover o grupo de recursos e todos os recursos que contém. Este comando elimina o registo de contentor criado, bem como o contentor em execução, e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma função Azure que requer um pedido HTTP e desencadeia a implantação de um grupo de contentores. Aprendeu a:

> [!div class="checklist"]
> * Utilize o Código de Estúdio Visual com a extensão funções Azure para criar uma função básica de PowerShell ativada por HTTP.
> * Ative uma identidade na aplicação de funções e dê-lhe permissões para criar recursos Azure.
> * Modificar o código de função PowerShell para automatizar a implantação de um grupo de contentores de um único contentor.
> * Verifique a implantação do recipiente ativado em HTTP.

Para um exemplo detalhado para lançar e monitorizar um trabalho contentorizado, consulte o blog post [Serverless Containers com funções PowerShell Azure e instâncias de contentores Azure](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) e [a amostra](https://github.com/anthonychu/functions-powershell-run-aci)de código de acompanhamento .

Consulte a documentação das [Funções Azure](/azure/azure-functions/) para obter orientações detalhadas sobre a criação de funções do Azure e a publicação de um projeto de funções. 

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
