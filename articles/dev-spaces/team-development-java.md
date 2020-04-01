---
title: Desenvolvimento de equipa usando Java e Visual Studio Code
services: azure-dev-spaces
author: stepro
ms.author: stephpr
ms.date: 08/01/2018
ms.topic: tutorial
description: Este tutorial mostra-lhe como usar o Azure Dev Spaces e o Visual Studio Code para fazer o desenvolvimento de equipa numa aplicação Java no Serviço Azure Kubernetes
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s '
manager: gwallace
ms.openlocfilehash: 352671b2fe31095b0ffcaffb49195071a456a892
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78245010"
---
# <a name="team-development-using-java-and-visual-studio-code-with-azure-dev-spaces"></a>Desenvolvimento de equipa usando Java e Visual Studio Code com Espaços Azure Dev

Neste tutorial, você vai aprender como uma equipe de desenvolvedores pode simultaneamente colaborar no mesmo cluster Kubernetes usando Dev Spaces.

## <a name="learn-about-team-development"></a>Saiba mais sobre desenvolvimento em equipa
Até aqui, tem executado o código da aplicação como se fosse o único programador a trabalhar na aplicação. Nesta secção, irá aprender até que ponto o Azure Dev Spaces simplifica o desenvolvimento em equipa ao:
* Permitir que uma equipa de desenvolvedores trabalhe no mesmo ambiente, trabalhando num espaço de v partilhado ou em espaços de v distintos, conforme necessário.
* Suportar que cada programador faça iterações no respetivo código de forma isolada e sem receio de danificar o código de terceiros.
* Testar o código ponto a ponto, antes de proceder à consolidação do código, sem ter de criar cenários fictícios ou simular dependências.

### <a name="challenges-with-developing-microservices"></a>Desafios relativamente ao desenvolvimento de microsserviços
Este exemplo de aplicação não apresenta índices de complexidade elevados até ao momento. Todavia, num cenário de desenvolvimento real, os desafios começam a surgir à medida que são adicionados mais serviços e a equipa de desenvolvimento começa a aumentar. A execução de todos os elementos para fins de desenvolvimento a nível local pode tornar-se uma perspetiva irrealista.

* A sua máquina de desenvolvimento pode não ter recursos suficientes para executar todos os serviços que precisa ao mesmo tempo.
* Alguns serviços podem ter de ser acessíveis publicamente. Por exemplo, um serviço pode precisar de ter um ponto final que responda a um webhook.
* Se quiser executar um subconjunto de serviços, tem de conhecer toda a hierarquia de dependência entre todos os seus serviços. Determinar isto pode ser difícil, especialmente porque o seu número de serviços aumenta.
* Alguns programadores recorrem à simulação ou criação de cenários fictícios de muitas das suas dependências de serviço. Esta abordagem pode ajudar, mas gerir essas simulações pode em breve ter impacto nos custos de desenvolvimento. Além disso, esta abordagem leva ao seu ambiente de desenvolvimento muito diferente da produção, o que permite que insetos subtis se agissem.
* Daí resulta que fazer qualquer tipo de teste de integração torna-se difícil. Realisticamente, o teste de integração só pode ocorrer após uma consolidação, o que significa que irá ver os problemas numa fase posterior no ciclo de desenvolvimento.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Trabalhar num espaço de desenvolvimento partilhado
O Azure Dev Spaces permite-lhe configurar um espaço de desenvolvimento *partilhado* no Azure. Cada programador pode concentrar-se unicamente na respetiva parte da aplicação e, assim, desenvolver o *código de pré-consolidação* de forma iterativa num espaço de desenvolvimento que já contém todos os outros serviços e recursos na cloud de que os respetivos cenários dependem. As dependências estão sempre atualizadas e os programadores trabalham de uma forma que reflete a produção.

### <a name="work-in-your-own-space"></a>Trabalhar no seu próprio espaço
À medida que desenvolve o código para o seu serviço e até estar preparado para o implementar, é frequente o código não estar em bom estado. A formulação, os testes e as experiências com soluções ao nível do código são um processo iterativo contínuo. O Azure Dev Spaces fornece o conceito de **espaço**, onde pode trabalhar de forma isolada e sem receio de prejudicar os membros da sua equipa.

## <a name="use-dev-spaces-for-team-development"></a>Use Espaços Dev para desenvolvimento de equipas
Vamos demonstrar estas ideias com um exemplo concreto usando a nossa aplicação de amostra*mywebapi* *webfrontend.* ->  Vamos imaginar um cenário em que um desenvolvedor, Scott, precisa de fazer uma mudança para o serviço *mywebapi,* e *só* para esse serviço. O *webfrontend* não vai precisar de mudar como parte da atualização do Scott.

_Sem_ usar dev Spaces, Scott teria algumas maneiras de desenvolver e testar a sua atualização, nenhuma das quais é ideal:
* Executar todos os componentes localmente. Isto requer uma máquina de desenvolvimento mais poderosa com docker instalado, e potencialmente MiniKube.
* Executar todos os componentes num espaço de nome isolado no cluster Kubernetes. Como *o webfrontend* não está a mudar, isto é um desperdício de recursos de cluster.
* Só execute *mywebapi*e faça chamadas manuais de REST para testar. Isto não testa o fluxo total de ponta a ponta.
* Adicione código focado no desenvolvimento ao *webfrontend* que permite ao desenvolvedor enviar pedidos para uma instância diferente de *mywebapi*. Isto complica o serviço *webfrontend.*

### <a name="set-up-your-baseline"></a>Configurar a sua linha de base
Primeiro, precisamos de implantar uma linha de base dos nossos serviços. Esta implementação representará o "último bem conhecido" para que possa facilmente comparar o comportamento do seu código local vs. a versão check-in. Criaremos então um espaço para crianças com base nesta linha de base para que possamos testar as nossas alterações ao *mywebapi* no contexto da aplicação maior.

1. Clone a [aplicação da amostra de Espaços Dev:](https://github.com/Azure/dev-spaces)`git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Check-out o ramo remoto *azds_updates:*`git checkout -b azds_updates origin/azds_updates`
1. Selecione o `azds space select --name dev`espaço _de v:_ . Quando for solicitado para selecionar um _ \<espaço\>_ de dev dos pais, selecione nenhum .
1. Navegue para o diretório _mywebapi_ e execute:`azds up -d`
1. Navegue para o diretório _webfrontend_ e execute:`azds up -d`
1. Execute `azds list-uris` para ver o ponto final público para _webfrontend_

> [!TIP]
> Os passos acima configuram manualmente uma linha de base, mas recomendamos que as equipas utilizem CI/CD para manter automaticamente a sua linha de base atualizada com o código comprometido.
>
> Consulte o nosso guia para a configuração de [CI/CD com Azure DevOps](how-to/setup-cicd.md) para criar um fluxo de trabalho semelhante ao diagrama seguinte.
>
> ![Diagrama de CI/CD de exemplo](media/common/ci-cd-complex.png)

Neste momento, a sua linha de base deve estar a funcionar. Execute o comando `azds list-up --all` e verá um resultado semelhante ao seguinte:

```
$ azds list-up --all

Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      dev       Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      dev       Pod      3m ago   Running
webfrontend                   dev       Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  dev       Pod      1m ago   Running
```

A coluna DevSpace mostra que ambos os serviços estão a funcionar num espaço chamado _dev_. Quem abrir o URL público e navegar para a aplicação web invocará o caminho de código check-in que percorre ambos os serviços. Agora suponha que quer continuar a desenvolver _mywebapi._ Como pode fazer alterações ao código e testá-las, sem interromper outros programadores que estejam a utilizar o ambiente de desenvolvimento? Para tal, terá de configurar o seu próprio espaço.

### <a name="create-a-dev-space"></a>Criar um espaço de programador
Para executar a sua própria versão de _mywebapi_ num espaço diferente do _dev,_ você pode criar o seu próprio espaço usando o seguinte comando:

```cmd
azds space select --name scott
```

Quando solicitado, selecione _dev_ como o **espaço de v dos pais**. Isto significa que o nosso novo espaço, _dev/scott,_ derivará do espaço _dev._ Em breve verá como isto nos irá ajudar com os testes.

Mantendo a nossa hipótese introdutória, usamos o nome _Scott_ para o novo espaço para que os pares possam identificar quem está a trabalhar nele. Mas pode ser chamado de tudo o que quiser, e ser flexível sobre o que significa, como _sprint4_ ou _demonstração._ Seja como for, o _Dev_ serve de base para todos os desenvolvedores que trabalham numa parte desta aplicação:

![](media/common/ci-cd-space-setup.png)

Execute o comando `azds space list` para ver uma lista de todos os espaços no ambiente de desenvolvimento. A coluna _Selecionada_ indica qual o espaço que selecionou atualmente (verdadeiro/falso). No seu caso, o espaço chamado _dev/scott_ foi automaticamente selecionado quando foi criado. Pode selecionar outro espaço em qualquer altura com o comando `azds space select`.

Vamos vê-lo em ação.

### <a name="make-a-code-change"></a>Faça uma alteração de código
Vá à janela do `mywebapi` Código VS e `String index()` faça `src/main/java/com/ms/sample/mywebapi/Application.java`uma edição de código ao método em, por exemplo:

```java
@RequestMapping(value = "/", produces = "text/plain")
public String index() {
    return "Hello from mywebapi says something new";
}
```

### <a name="run-the-service"></a>Executar o serviço

Para executar o serviço, bata `azds up` em F5 (ou digite na Janela do Terminal) para executar o serviço. O serviço será executado automaticamente no seu novo espaço _dev/scott._ Confirme que o seu serviço está `azds list-up`a funcionar no seu próprio espaço, executando:

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

Note que um exemplo de *mywebapi* está agora correndo no espaço _dev/scott._ A versão em execução em _dev_ ainda está em execução, mas não está listada.

Enumerar os URLs para `azds list-uris`o espaço atual, executando .

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

Note que o URL do ponto de acesso público para *webfrontend* é pré-fixado com *scott.s*. Este URL é único no espaço _de v/scott._ Este prefixo DE URL diz ao controlador Ingress para encaminhar pedidos para a versão _dev/scott_ de um serviço. Quando um pedido com este URL é tratado pela Dev Spaces, o Controlador De Entrada tenta primeiro encaminhar o pedido para o serviço *webfrontend* no espaço _dev/scott._ Se isso falhar, o pedido será encaminhado para o serviço *webfrontend* no espaço _de v_ como um recuo. Note também que existe um URL local para aceder ao serviço sobre o hospedeiro local usando a funcionalidade kubernetes *port-forward.* Para mais informações sobre URLs e encaminhamento em Espaços Azure Dev, consulte [como funciona o Azure Dev Spaces e está configurado](how-dev-spaces-works.md).

![Encaminhamento espacial](media/common/Space-Routing.png)

Esta funcionalidade incorporada dos Espaços de Programador do Azure permite-lhe testar código num espaço partilhado sem que cada programador tenha de recriar a pilha completa de serviços no respetivo espaço. Este tipo de encaminhamento requer que o código da aplicação reencaminhe os cabeçalhos de propagação, conforme ilustrado no passo anterior deste guia.

### <a name="test-code-in-a-space"></a>Testar código num espaço
Para testar a sua nova versão do *mywebapi* com *webfrontend,* abra o seu navegador para o URL do ponto de acesso público para *webfrontend* e vá para a página About. Deverá ver sua nova mensagem apresentada.

Agora, remova a parte do URL "scott.s." e atualize o browser. Você deve ver o comportamento antigo (com a versão *mywebapi* em _dev)._

Uma vez que você tem um espaço _de v,_ que contém sempre as suas últimas alterações, e assumindo que a sua aplicação foi projetada para tirar partido do encaminhamento espacial da DevSpace como descrito nesta secção tutorial, espero que seja fácil ver como a Dev Spaces pode ajudar muito no teste de novas funcionalidades no contexto da aplicação maior. Em vez de ter de implantar _todos os_ serviços para o seu espaço privado, pode criar um espaço privado que deriva do _dev_, e apenas "subir" os serviços em que está realmente a trabalhar. A infraestrutura de encaminhamento de Dev Spaces irá lidar com o resto utilizando o maior número de serviços fora do seu espaço privado, enquanto não volta à versão mais recente que corre no espaço _de v._ E melhor ainda, _vários_ desenvolvedores podem desenvolver ativamente diferentes serviços ao mesmo tempo no seu próprio espaço sem perturbar uns aos outros.

### <a name="well-done"></a>Já está!
Concluiu o guia de introdução! Aprendeu a:

> [!div class="checklist"]
> * Configurar os Espaços de Programador do Azure com um cluster Kubernetes gerido no Azure.
> * Desenvolver iterativamente código em contentores.
> * Desenvolver de modo independente dois serviços separados e utilizar a deteção do serviço DNS de Kubernetes para fazer uma chamada para outro serviço.
> * Desenvolver e testar de forma produtiva o seu código num ambiente de equipa.
> * Estabelecer uma linha de base de funcionalidade utilizando espaços Dev para testar facilmente alterações isoladas no contexto de uma aplicação de microserviço maior

Agora que explorou a Azure Dev Spaces, [partilhe o seu espaço de dev com um membro da equipa](how-to/share-dev-spaces.md) e comece a colaborar.

## <a name="clean-up"></a>Limpeza
Para eliminar completamente uma instância dos Espaços de Programador do Azure num cluster, incluindo todos os espaços de programador e serviços em execução, utilize o comando `az aks remove-dev-spaces`. Não se esqueça que esta ação é irreversível. Pode adicionar suporte aos Espaços de Programador do Azure novamente no cluster, mas será como se estivesse novamente a começar. Os seus serviços e espaços antigos não serão restaurados.

O exemplo a seguir lista os controladores dos Espaços de Programador do Azure na sua subscrição ativa e, em seguida, elimina o controlador do mesmo que está associado ao cluster do AKS "myaks" do AKS no grupo de recursos "myaks-rg".

```cmd
azds controller list
```

```azurecli
az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
