---
title: Desenvolvimento de equipa usando Java e Visual Studio Code
services: azure-dev-spaces
author: stepro
ms.custom: devx-track-java
ms.author: stephpr
ms.date: 08/01/2018
ms.topic: tutorial
description: Este tutorial mostra-lhe como usar Azure Dev Spaces e Visual Studio Code para fazer desenvolvimento de equipa numa aplicação Java no Serviço Azure Kubernetes
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s '
manager: gwallace
ms.openlocfilehash: 80544b497caf39ee45acb82ff644a5c06ac3ff7e
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327196"
---
# <a name="team-development-using-java-and-visual-studio-code-with-azure-dev-spaces"></a>Desenvolvimento de equipas usando Java e Código de Estúdio Visual com Espaços Azure Dev

Neste tutorial, você vai aprender como uma equipe de desenvolvedores pode simultaneamente colaborar no mesmo cluster Kubernetes usando Dev Spaces.

## <a name="learn-about-team-development"></a>Saiba mais sobre desenvolvimento em equipa
Até aqui, tem executado o código da aplicação como se fosse o único programador a trabalhar na aplicação. Nesta secção, irá aprender até que ponto o Azure Dev Spaces simplifica o desenvolvimento em equipa ao:
* Capacitar uma equipa de desenvolvedores a trabalhar no mesmo ambiente, trabalhando num espaço dev partilhado ou em espaços dev distintos, conforme necessário.
* Suportar que cada programador faça iterações no respetivo código de forma isolada e sem receio de danificar o código de terceiros.
* Testar o código ponto a ponto, antes de proceder à consolidação do código, sem ter de criar cenários fictícios ou simular dependências.

### <a name="challenges-with-developing-microservices"></a>Desafios relativamente ao desenvolvimento de microsserviços
Este exemplo de aplicação não apresenta índices de complexidade elevados até ao momento. Todavia, num cenário de desenvolvimento real, os desafios começam a surgir à medida que são adicionados mais serviços e a equipa de desenvolvimento começa a aumentar. A execução de todos os elementos para fins de desenvolvimento a nível local pode tornar-se uma perspetiva irrealista.

* A sua máquina de desenvolvimento pode não ter recursos suficientes para executar todos os serviços que necessita ao mesmo tempo.
* Alguns serviços podem ter de ser publicamente contactáveis. Por exemplo, um serviço pode precisar de ter um ponto final que responda a um webhook.
* Se deseja executar um subconjunto de serviços, tem de conhecer toda a hierarquia de dependência entre todos os seus serviços. Determinar isto pode ser difícil, especialmente à medida que o seu número de serviços aumenta.
* Alguns programadores recorrem à simulação ou criação de cenários fictícios de muitas das suas dependências de serviço. Esta abordagem pode ajudar, mas gerir essas zombarias pode em breve ter impacto nos custos de desenvolvimento. Além disso, esta abordagem leva ao seu ambiente de desenvolvimento muito diferente da produção, o que permite que insetos subtis entrem.
* Daí resulta que fazer qualquer tipo de testes de integração torna-se difícil. Realisticamente, o teste de integração só pode ocorrer após uma consolidação, o que significa que irá ver os problemas numa fase posterior no ciclo de desenvolvimento.

    ![Diagrama que retrata os desafios que enfrenta ao desenvolver microserviços.](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Trabalhar num espaço de desenvolvimento partilhado
O Azure Dev Spaces permite-lhe configurar um espaço de desenvolvimento *partilhado* no Azure. Cada programador pode concentrar-se unicamente na respetiva parte da aplicação e, assim, desenvolver o *código de pré-consolidação* de forma iterativa num espaço de desenvolvimento que já contém todos os outros serviços e recursos na cloud de que os respetivos cenários dependem. As dependências estão sempre atualizadas e os programadores trabalham de uma forma que reflete a produção.

### <a name="work-in-your-own-space"></a>Trabalhar no seu próprio espaço
À medida que desenvolve o código para o seu serviço e até estar preparado para o implementar, é frequente o código não estar em bom estado. A formulação, os testes e as experiências com soluções ao nível do código são um processo iterativo contínuo. O Azure Dev Spaces fornece o conceito de **espaço**, onde pode trabalhar de forma isolada e sem receio de prejudicar os membros da sua equipa.

## <a name="use-dev-spaces-for-team-development"></a>Use espaços Dev para o desenvolvimento de equipas
Vamos demonstrar estas ideias com um exemplo concreto usando a nossa aplicação *de*amostra  ->  *mywebapi* da webfrontend. Vamos imaginar um cenário em que um desenvolvedor, Scott, precisa de fazer uma mudança para o serviço *mywebapi,* e *só* aquele serviço. A *frente web* não precisará de ser alterada como parte da atualização do Scott.

_Sem_ usar Dev Spaces, Scott teria algumas formas de desenvolver e testar a sua atualização, nenhuma das quais é ideal:
* Executar TODOS os componentes localmente. Isto requer uma máquina de desenvolvimento mais poderosa com Docker instalado, e potencialmente MiniKube.
* Executar TODOS os componentes num espaço de nome isolado no cluster Kubernetes. Como *a webfrontend* não está a mudar, isto é um desperdício de recursos de fragmentação.
* Basta executar *mywebapi,* e fazer chamadas de DESCANSO manuais para testar. Isto não testa o fluxo de ponta a ponta.
* Adicione código focado no desenvolvimento à *webfrontend* que permite ao desenvolvedor enviar pedidos para uma instância diferente do *mywebapi*. Isto complica o serviço *de webfrontend.*

### <a name="set-up-your-baseline"></a>Configurar a sua linha de base
Primeiro, temos de implantar uma linha de base dos nossos serviços. Esta implementação representará o "último bem conhecido" para que possa facilmente comparar o comportamento do seu código local vs. a versão check-in. Em seguida, criaremos um espaço para crianças com base nesta linha de base para que possamos testar as nossas alterações no *mywebapi* no contexto da aplicação maior.

1. Clone a [aplicação de amostra de Dev Spaces:](https://github.com/Azure/dev-spaces)`git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Check-out o *azds_updates*do ramo remoto:`git checkout -b azds_updates origin/azds_updates`
1. Selecione o _espaço dev:_ `azds space select --name dev` . Quando solicitado para selecionar um espaço dev dos pais, selecione _\<none\>_ .
1. Navegue para o _diretório mywebapi_ e execute:`azds up -d`
1. Navegue para o _diretório de frontales_ e execute:`azds up -d`
1. Execute `azds list-uris` para ver o ponto final público para _webfrontend_

> [!TIP]
> Os passos acima configuram manualmente uma linha de base, mas recomendamos que as equipas utilizem CI/CD para manter automaticamente a sua linha de base atualizada com o código comprometido.
>
> Consulte o nosso [guia para configurar o CI/CD com a Azure DevOps](how-to/setup-cicd.md) para criar um fluxo de trabalho semelhante ao diagrama seguinte.
>
> ![Diagrama de EXEMPLO CI/CD](media/common/ci-cd-complex.png)

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

A coluna DevSpace mostra que ambos os serviços estão a funcionar num espaço chamado _dev_. Qualquer pessoa que abra o URL público e navegue para a aplicação web irá invocar o caminho do código de check-in que passa por ambos os serviços. Agora suponha que queira continuar a desenvolver _mywebapi._ Como pode fazer alterações ao código e testá-las, sem interromper outros programadores que estejam a utilizar o ambiente de desenvolvimento? Para tal, terá de configurar o seu próprio espaço.

### <a name="create-a-dev-space"></a>Criar um espaço de programador
Para executar a sua própria versão do _mywebapi_ num espaço diferente _do dev,_ pode criar o seu próprio espaço utilizando o seguinte comando:

```cmd
azds space select --name scott
```

Quando solicitado, selecione _dev_ como o **espaço dev dos pais**. Isto significa que o nosso novo espaço, _dev/scott,_ derivará do _espaço dev._ Em breve verá como isto nos irá ajudar com os testes.

Mantendo a nossa hipótese introdutória, usamos o nome _Scott_ para o novo espaço para que os pares possam identificar quem está a trabalhar nele. Mas pode ser chamado o que quiser, e ser flexível sobre o que significa, como _sprint4_ ou _demo._ Seja como for, o _dev_ serve de base para todos os desenvolvedores que trabalham numa peça desta aplicação:

![Um diagrama mostrando uma configuração espacial C I C D simples.](media/common/ci-cd-space-setup.png)

Execute o comando `azds space list` para ver uma lista de todos os espaços no ambiente de desenvolvimento. A coluna _Selecionada_ indica qual o espaço que selecionou atualmente (verdadeiro/falso). No seu caso, o espaço chamado _dev/scott_ foi automaticamente selecionado quando foi criado. Pode selecionar outro espaço em qualquer altura com o comando `azds space select`.

Vamos vê-lo em ação.

### <a name="make-a-code-change"></a>Faça uma mudança de código
Vá à janela do Código VS `mywebapi` e faça uma edição de código para o `String index()` método `src/main/java/com/ms/sample/mywebapi/Application.java` em, por exemplo:

```java
@RequestMapping(value = "/", produces = "text/plain")
public String index() {
    return "Hello from mywebapi says something new";
}
```

### <a name="run-the-service"></a>Executar o serviço

Para executar o serviço, bata em F5 (ou digite `azds up` na Janela do Terminal) para executar o serviço. O serviço será executado automaticamente no seu _novo espaço dev/scott_. Confirme que o seu serviço está a funcionar no seu próprio espaço, `azds list-up` executando:

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

Reparem que um exemplo do *mywebapi* está agora a correr no espaço _dev/scott._ A versão em execução no _dev_ ainda está em execução, mas não está listada.

Listar os URLs para o espaço atual funcionando `azds list-uris` .

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

Note que o URL do ponto de acesso público para *webfrontend* está prefixado com *scott.s*. Este URL é exclusivo do espaço _dev/scott._ Este prefixo URL diz ao controlador Ingress para encaminhar os pedidos para a versão _dev/scott_ de um serviço. Quando um pedido com este URL é tratado pela Dev Spaces, o Controlador Ingress tenta primeiro encaminhar o pedido para o serviço *de frontend web* no espaço _dev/scott._ Se isso falhar, o pedido será encaminhado para o serviço *de frontend web no* espaço _dev_ como um retorno. Note também que existe um URL local para aceder ao serviço através da localidade utilizando a funcionalidade *porta-a-frente* de Kubernetes. Para obter mais informações sobre URLs e encaminhamento em Azure Dev Spaces, consulte [como funciona a Azure Dev Spaces e está configurado.](how-dev-spaces-works.md)

![Encaminhamento espacial](media/common/Space-Routing.png)

Esta funcionalidade incorporada dos Espaços de Programador do Azure permite-lhe testar código num espaço partilhado sem que cada programador tenha de recriar a pilha completa de serviços no respetivo espaço. Este tipo de encaminhamento requer que o código da aplicação reencaminhe os cabeçalhos de propagação, conforme ilustrado no passo anterior deste guia.

### <a name="test-code-in-a-space"></a>Testar código num espaço
Para testar a sua nova versão do *mywebapi* com *webfrontend,* abra o seu navegador para o URL do ponto de acesso público para *webfrontend* e vá para a página Sobre. Deverá ver sua nova mensagem apresentada.

Agora, remova a parte do URL "scott.s." e atualize o browser. Você deve ver o comportamento antigo (com a versão *mywebapi* em execução em _dev)._

Uma vez que você tem um espaço _dev,_ que contém sempre as suas últimas alterações, e assumindo que a sua aplicação foi projetada para aproveitar o encaminhamento espacial da DevSpace como descrito nesta secção tutorial, espero que se torne fácil ver como dev Spaces pode ajudar muito a testar novas funcionalidades no contexto da aplicação maior. Em vez de ter de implantar _todos os_ serviços para o seu espaço privado, pode criar um espaço privado que deriva do _dev_, e apenas "up" dos serviços em que está realmente a trabalhar. A infraestrutura de encaminhamento Dev Spaces irá lidar com o resto utilizando o maior número de serviços fora do seu espaço privado que possa encontrar, enquanto está de volta à versão mais recente em execução no espaço _dev._ E melhor ainda, _vários_ desenvolvedores podem desenvolver ativamente diferentes serviços ao mesmo tempo no seu próprio espaço sem perturbar uns aos outros.

### <a name="well-done"></a>Parabéns!
Concluiu o guia de introdução! Aprendeu a:

> [!div class="checklist"]
> * Configurar os Espaços de Programador do Azure com um cluster Kubernetes gerido no Azure.
> * Desenvolver iterativamente código em contentores.
> * Desenvolver de modo independente dois serviços separados e utilizar a deteção do serviço DNS de Kubernetes para fazer uma chamada para outro serviço.
> * Desenvolver e testar de forma produtiva o seu código num ambiente de equipa.
> * Estabeleça uma linha de base de funcionalidade utilizando espaços Dev para testar facilmente alterações isoladas no contexto de uma aplicação de microserviço maior

Agora que explorou a Azure Dev Spaces, [partilhe o seu espaço dev com um membro da equipa](how-to/share-dev-spaces.md) e comece a colaborar.

## <a name="clean-up"></a>Limpeza
Para eliminar completamente uma instância dos Espaços de Programador do Azure num cluster, incluindo todos os espaços de programador e serviços em execução, utilize o comando `az aks remove-dev-spaces`. Não se esqueça que esta ação é irreversível. Pode adicionar suporte aos Espaços de Programador do Azure novamente no cluster, mas será como se estivesse novamente a começar. Os seus serviços e espaços antigos não serão restaurados.

O exemplo a seguir lista os controladores dos Espaços de Programador do Azure na sua subscrição ativa e, em seguida, elimina o controlador do mesmo que está associado ao cluster do AKS "myaks" do AKS no grupo de recursos "myaks-rg".

```cmd
azds controller list
```

```azurecli
az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
