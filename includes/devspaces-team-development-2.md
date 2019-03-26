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
ms.openlocfilehash: e0f768b876b49ec006ce98decf121d73d334b6d8
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439524"
---
### <a name="run-the-service"></a>Executar o serviço

Para executar o serviço, pressionar F5 (ou tipo `azds up` na janela de Terminal) para executar o serviço. O serviço será executada automaticamente no seu espaço de recém-selecionados _dev/scott_. Confirme que o serviço está a executar no seu próprio espaço executando `azds list-up`:

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

Observe que uma instância do *mywebapi* agora está em execução _dev/scott_ espaço. A versão em execução _dev_ ainda está em execução, mas não estiver na lista.

Lista os URLs para o atual espaço executando `azds list-uris`.

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

Tenha em atenção o URL do ponto de acesso público para *webfrontend* é o prefixo *scott.s*. Este URL é exclusivo para o _dev/scott_ espaço. Este prefixo de URL informa o controlador de entrada para encaminhar pedidos para o _dev/scott_ versão de um serviço. Quando um pedido com este URL é manipulado por espaços de desenvolvimento, o controlador de entrada tenta pela primeira vez encaminhar o pedido para o *webfrontend* serviço no _dev/scott_ espaço. Se isso falhar, o pedido será encaminhado para o *webfrontend* serviço no _dev_ espaço como fallback. Repare também que existe um URL de localhost para aceder ao serviço através de localhost através do Kubernetes *porta-forward* funcionalidade. Para obter mais informações sobre URLs e encaminhamento nos espaços de desenvolvimento do Azure, consulte [como os espaços de desenvolvimento do Azure funciona e é configurado](../articles/dev-spaces/how-dev-spaces-works.md).



![Encaminhamento de espaço](../articles/dev-spaces/media/common/Space-Routing.png)

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

Agora que tiver explorado espaços de desenvolvimento do Azure, [partilhar o seu espaço de desenvolvimento com um membro da Equipe](../articles/dev-spaces/how-to/share-dev-spaces.md) e começar a colaborar.

## <a name="clean-up"></a>Limpeza
Para eliminar completamente uma instância dos Espaços de Programador do Azure num cluster, incluindo todos os espaços de programador e serviços em execução, utilize o comando `az aks remove-dev-spaces`. Não se esqueça que esta ação é irreversível. Pode adicionar suporte aos Espaços de Programador do Azure novamente no cluster, mas será como se estivesse novamente a começar. Os seus serviços e espaços antigos não serão restaurados.

O exemplo a seguir lista os controladores dos Espaços de Programador do Azure na sua subscrição ativa e, em seguida, elimina o controlador do mesmo que está associado ao cluster do AKS "myaks" do AKS no grupo de recursos "myaks-rg".

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```