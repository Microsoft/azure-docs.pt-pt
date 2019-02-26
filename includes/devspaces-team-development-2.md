---
title: incluir ficheiro
description: incluir ficheiro
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: include
manager: yuvalm
ms.openlocfilehash: 7dddf7e48913aea9d84e0f8b66c2c9d29449ee6f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825661"
---
### <a name="run-the-service"></a>Executar o serviço

1. Prima F5 (ou escreva `azds up` na Janela do Terminal) para executar o serviço. O serviço será executada automaticamente no seu espaço de recém-selecionados _dev/scott_. 
1. Pode confirmar se o serviço está a executar no seu próprio espaço ao executar `azds list-up` novamente. Observará que uma instância do *mywebapi* agora está em execução _dev/scott_ espaço (a versão em execução _dev_ ainda está em execução, mas não estiver na lista).

    ```
    Name                      DevSpace  Type     Updated  Status
    mywebapi                  scott     Service  3m ago   Running
    mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
    webfrontend               dev       Service  26m ago  Running
    ```

1. Execute `azds list-uris`e observe o acesso ao URL de ponto *webfrontend*.

    ```
    Uri                                                                        Status
    -------------------------------------------------------------------------  ---------
    http://localhost:53831 => mywebapi.scott:80                                Tunneled
    http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
    ```

1. Utilize o URL com o *scott.s* prefixo para navegar para a sua aplicação. Observe que isso atualizado URL ainda resolve. Este URL é exclusivo para o _dev/scott_ espaço. O URL especial significa que os pedidos enviados para o URL"Scott" tentará primeiro rota para serviços no _dev/scott_ espaço, mas se isso falhar, irão reverter para serviços no _dev_ espaço.

<!--
TODO: replace 2 & 3 with below once bug#753164 and PR#158827 get pushed to production.

You can confirm that your service is running in its own space by running `azds list-up` again. First, you'll notice an instance of *mywebapi* is now running in the _dev/scott_ space (the version running in _dev_ is still running but it is not listed). If you run `azds list-uris`, you will notice that the access point URL for *webfrontend* is prefixed with the text "scott.s.". This URL is unique to the _dev/scott_ space. The special URL signifies that requests sent to the "Scott URL" will try to first route to services in the _dev/scott_ space, but if that fails, they will fall back to services in the _dev_ space.

```
Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

```
Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```
-->

![](../articles/dev-spaces/media/common/space-routing.png)

Esta funcionalidade incorporada dos Espaços de Programador do Azure permite-lhe testar código num espaço partilhado sem que cada programador tenha de recriar a pilha completa de serviços no respetivo espaço. Este tipo de encaminhamento requer que o código da aplicação reencaminhe os cabeçalhos de propagação, conforme ilustrado no passo anterior deste guia.

### <a name="test-code-in-a-space"></a>Testar código num espaço
Para testar a nova versão do *mywebapi* com *webfrontend*, abra o browser para o URL do ponto de acesso público para *webfrontend* e aceda à página About. Deverá ver sua nova mensagem apresentada.

Agora, remova a parte do URL "scott.s." e atualize o browser. Deverá ver o comportamento antigo (com o *mywebapi* versão em execução _dev_).

Depois de ter uma _dev_ espaço, que contém sempre suas alterações mais recentes e supondo que a aplicação foi concebida tirar partido DevSpace baseados no espaço de encaminhamento como descrito nesta secção do tutorial, Espero que seja fácil Veja como os espaços de desenvolvimento muito pode auxiliar nos testes novos recursos dentro do contexto do aplicativo maior. Em vez de precisar implantar _todos os_ serviços no seu espaço privado, pode criar um espaço privado que deriva de _dev_e "apenas up" os serviços que está realmente a trabalhar. A infraestrutura de encaminhamento de espaços de desenvolvimento irá processar o resto utilizando os serviços fora do seu espaço de privada pois pode encontrar, ao utilizar a predefinição para a versão mais recente em execução no _dev_ espaço. E ainda assim, melhor _vários_ os programadores podem ativamente desenvolver serviços diferentes ao mesmo tempo no seu próprio espaço sem interromper entre si.

### <a name="well-done"></a>Já está!
Concluiu o guia de introdução! Aprendeu a:

> [!div class="checklist"]
> * Configurar os Espaços de Programador do Azure com um cluster Kubernetes gerido no Azure.
> * Desenvolver iterativamente código em contentores.
> * Desenvolver de modo independente dois serviços separados e utilizar a deteção do serviço DNS de Kubernetes para fazer uma chamada para outro serviço.
> * Desenvolver e testar de forma produtiva o seu código num ambiente de equipa.
> * Estabelecer uma linha de base da funcionalidade através de espaços de programador para testar facilmente isoladas de alterações dentro do contexto de uma aplicação de microsserviços maior

Agora que explorou os Espaços de Programação do Azure, [partilhe o seu espaço de programação com um membro da equipa](../articles/dev-spaces/how-to/share-dev-spaces.md) e ajude-o a ver como é fácil colaborar em conjunto.

## <a name="clean-up"></a>Limpeza
Para eliminar completamente uma instância dos Espaços de Programador do Azure num cluster, incluindo todos os espaços de programador e serviços em execução, utilize o comando `az aks remove-dev-spaces`. Não se esqueça que esta ação é irreversível. Pode adicionar suporte aos Espaços de Programador do Azure novamente no cluster, mas será como se estivesse novamente a começar. Os seus serviços e espaços antigos não serão restaurados.

O exemplo a seguir lista os controladores dos Espaços de Programador do Azure na sua subscrição ativa e, em seguida, elimina o controlador do mesmo que está associado ao cluster do AKS "myaks" do AKS no grupo de recursos "myaks-rg".

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```