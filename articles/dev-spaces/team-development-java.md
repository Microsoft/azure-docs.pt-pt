---
title: Desenvolvimento de equipe usando Java e Visual Studio Code
services: azure-dev-spaces
author: stepro
ms.author: stephpr
ms.date: 08/01/2018
ms.topic: tutorial
description: Este tutorial mostra como usar Azure Dev Spaces e Visual Studio Code para fazer o desenvolvimento em equipe em um aplicativo Java no serviço kubernetes do Azure
keywords: 'Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S '
manager: gwallace
ms.openlocfilehash: d81b0227a627593b16bd07f4cfcf4c1b6d61f84b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438160"
---
# <a name="team-development-using-java-and-visual-studio-code-with-azure-dev-spaces"></a>Desenvolvimento de equipe usando Java e Visual Studio Code com Azure Dev Spaces

Neste tutorial, você aprenderá como uma equipe de desenvolvedores pode colaborar simultaneamente no mesmo cluster kubernetes usando espaços de desenvolvimento.

## <a name="learn-about-team-development"></a>Saiba mais sobre desenvolvimento em equipa
Até aqui, tem executado o código da aplicação como se fosse o único programador a trabalhar na aplicação. Nesta secção, irá aprender até que ponto o Azure Dev Spaces simplifica o desenvolvimento em equipa ao:
* Permita que uma equipe de desenvolvedores trabalhe no mesmo ambiente, trabalhando em um espaço de desenvolvimento compartilhado ou em espaços de desenvolvimento distintos, conforme necessário.
* Suportar que cada programador faça iterações no respetivo código de forma isolada e sem receio de danificar o código de terceiros.
* Testar o código ponto a ponto, antes de proceder à consolidação do código, sem ter de criar cenários fictícios ou simular dependências.

### <a name="challenges-with-developing-microservices"></a>Desafios relativamente ao desenvolvimento de microsserviços
Este exemplo de aplicação não apresenta índices de complexidade elevados até ao momento. Todavia, num cenário de desenvolvimento real, os desafios começam a surgir à medida que são adicionados mais serviços e a equipa de desenvolvimento começa a aumentar. A execução de todos os elementos para fins de desenvolvimento a nível local pode tornar-se uma perspetiva irrealista.

* Seu computador de desenvolvimento pode não ter recursos suficientes para executar todos os serviços necessários ao mesmo tempo.
* Alguns serviços podem precisar ser acessíveis publicamente. Por exemplo, um serviço pode precisar ter um ponto de extremidade que responda a um webhook.
* Se você quiser executar um subconjunto de serviços, precisará saber a hierarquia de dependência total entre todos os seus serviços. Determinar isso pode ser difícil, especialmente à medida que o número de serviços aumenta.
* Alguns programadores recorrem à simulação ou criação de cenários fictícios de muitas das suas dependências de serviço. Essa abordagem pode ajudar, mas o gerenciamento dessas simulações pode afetar o custo de desenvolvimento em breve. Além disso, essa abordagem leva a um ambiente de desenvolvimento que parece muito diferente da produção, o que permite que Bugs sutis sejam deslizados.
* Depois disso, é difícil fazer qualquer tipo de teste de integração. Realisticamente, o teste de integração só pode ocorrer após uma consolidação, o que significa que irá ver os problemas numa fase posterior no ciclo de desenvolvimento.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Trabalhar num espaço de desenvolvimento partilhado
O Azure Dev Spaces permite-lhe configurar um espaço de desenvolvimento *partilhado* no Azure. Cada programador pode concentrar-se unicamente na respetiva parte da aplicação e, assim, desenvolver o *código de pré-consolidação* de forma iterativa num espaço de desenvolvimento que já contém todos os outros serviços e recursos na cloud de que os respetivos cenários dependem. As dependências estão sempre atualizadas e os programadores trabalham de uma forma que reflete a produção.

### <a name="work-in-your-own-space"></a>Trabalhar no seu próprio espaço
À medida que desenvolve o código para o seu serviço e até estar preparado para o implementar, é frequente o código não estar em bom estado. A formulação, os testes e as experiências com soluções ao nível do código são um processo iterativo contínuo. O Azure Dev Spaces fornece o conceito de **espaço**, onde pode trabalhar de forma isolada e sem receio de prejudicar os membros da sua equipa.

## <a name="use-dev-spaces-for-team-development"></a>Usar espaços de desenvolvimento para desenvolvimento em equipe
Vamos demonstrar essas ideias com um exemplo concreto usando nosso aplicativo de exemplo -> *mywebapi* de nosso *front-end* . Vamos imaginar um cenário em que um desenvolvedor, Scott, precisa fazer uma alteração no serviço *mywebapi* e *apenas* esse serviço. O *WebFrontEnd* não precisará ser alterado como parte da atualização de Scott.

_Sem_ usar espaços de desenvolvimento, Scott teria algumas maneiras de desenvolver e testar sua atualização, nenhuma das quais é ideal:
* Executar todos os componentes localmente. Isso requer uma máquina de desenvolvimento mais potente com o Docker instalado e potencialmente MiniKube.
* Execute todos os componentes em um namespace isolado no cluster kubernetes. Como o *WebFrontEnd* não está mudando, isso é um desperdício de recursos de cluster.
* SOMENTE execute *mywebapi*e faça as chamadas de REST manuais para teste. Isso não testa o fluxo completo de ponta a ponta.
* Adicione código centrado no desenvolvimento ao *WebFrontEnd* que permite ao desenvolvedor enviar solicitações para uma instância diferente do *mywebapi*. Isso complica o serviço de *WebFrontEnd* .

### <a name="set-up-your-baseline"></a>Configurar sua linha de base
Primeiro, precisaremos implantar uma linha de base de nossos serviços. Essa implantação representará a "última boa conhecida" para que você possa comparar facilmente o comportamento do seu código local versus a versão com check-in. Em seguida, criaremos um espaço filho com base nessa linha de base para que possamos testar nossas alterações em *mywebapi* no contexto do aplicativo maior.

1. Clonar o [aplicativo de exemplo de espaços de desenvolvimento](https://github.com/Azure/dev-spaces): `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Fazer check-out do *azds_updates*de Branch remoto: `git checkout -b azds_updates origin/azds_updates`
1. Selecione o espaço de _desenvolvimento_ : `azds space select --name dev`. Quando solicitado a selecionar um espaço de desenvolvimento pai, selecione _\<nenhum\>_ .
1. Navegue até o diretório _mywebapi_ e execute: `azds up -d`
1. Navegue até o diretório _WebFrontEnd_ e execute: `azds up -d`
1. Execute `azds list-uris` para ver o ponto de extremidade público para o _WebFrontEnd_

> [!TIP]
> As etapas acima configuram manualmente uma linha de base, mas recomendamos que as equipes usem o CI/CD para manter a linha de base atualizada com o código confirmado.
>
> Confira nosso [guia para configurar o CI/CD com o Azure DevOps](how-to/setup-cicd.md) para criar um fluxo de trabalho semelhante ao diagrama a seguir.
>
> ![Diagrama de CI/CD de exemplo](media/common/ci-cd-complex.png)

Neste ponto, sua linha de base deve estar em execução. Execute o comando `azds list-up --all` e verá um resultado semelhante ao seguinte:

```
$ azds list-up --all

Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      dev       Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      dev       Pod      3m ago   Running
webfrontend                   dev       Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  dev       Pod      1m ago   Running
```

A coluna DevSpace mostra que ambos os serviços estão sendo executados em um espaço chamado _dev_. Qualquer pessoa que abrir a URL pública e navegar até o aplicativo Web invocará o caminho de código com check-in que é executado por meio de ambos os serviços. Agora suponha que você deseja continuar desenvolvendo o _mywebapi_. Como pode fazer alterações ao código e testá-las, sem interromper outros programadores que estejam a utilizar o ambiente de desenvolvimento? Para tal, terá de configurar o seu próprio espaço.

### <a name="create-a-dev-space"></a>Criar um espaço de programador
Para executar sua própria versão do _mywebapi_ em um espaço que não seja o _dev_, você pode criar seu próprio espaço usando o seguinte comando:

```cmd
azds space select --name scott
```

Quando solicitado, selecione _desenvolvimento_ como o **espaço de desenvolvimento pai**. Isso significa que nosso novo espaço, _dev/Scott_, será derivado do espaço de _desenvolvimento_. Em breve verá como isto nos irá ajudar com os testes.

Acompanhando nossa hipotética de introdução, usamos o nome _Scott_ para o novo espaço para que os colegas possam identificar quem está trabalhando nele. Mas ele pode ser chamado de qualquer coisa que você desejar e ser flexível sobre o que isso significa, como _sprint4_ ou _demo_. Seja qual for o caso, o _dev_ serve como a linha de base para todos os desenvolvedores que trabalham em uma parte deste aplicativo:

![](media/common/ci-cd-space-setup.png)

Execute o comando `azds space list` para ver uma lista de todos os espaços no ambiente de desenvolvimento. A coluna _selecionada_ indica qual espaço você selecionou atualmente (true/false). No seu caso, o espaço chamado _dev/Scott_ foi selecionado automaticamente quando foi criado. Pode selecionar outro espaço em qualquer altura com o comando `azds space select`.

Vamos vê-lo em ação.

### <a name="make-a-code-change"></a>Fazer uma alteração de código
Vá para a janela de VS Code por `mywebapi` e faça uma edição de código para o método `String index()` em `src/main/java/com/ms/sample/mywebapi/Application.java`, por exemplo:

```java
@RequestMapping(value = "/", produces = "text/plain")
public String index() {
    return "Hello from mywebapi says something new";
}
```

### <a name="run-the-service"></a>Executar o serviço

Para executar o serviço, pressione F5 (ou digite `azds up` na janela do terminal) para executar o serviço. O serviço será executado automaticamente no seu desenvolvimento de espaço recentemente selecionado _/Scott_. Confirme se o serviço está em execução em seu próprio espaço executando `azds list-up`:

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

Observe que uma instância do *mywebapi* agora está em execução no espaço _desenvolvimento/Scott_ . A versão em execução no _dev_ ainda está em execução, mas não está listada.

Liste as URLs para o espaço atual executando `azds list-uris`.

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

Observe que a URL do ponto de acesso público para o *WebFrontEnd* é prefixada com *Scott. s*. Essa URL é exclusiva para o espaço de _desenvolvimento/Scott_ . Esse prefixo de URL informa o controlador de entrada para rotear solicitações para a versão de _desenvolvimento/Scott_ de um serviço. Quando uma solicitação com essa URL é manipulada por espaços de desenvolvimento, o controlador de entrada primeiro tenta rotear a solicitação para o serviço de *WebFrontEnd* no espaço de _desenvolvimento/Scott_ . Se isso falhar, a solicitação será roteada para o serviço de *WebFrontEnd* no espaço de _desenvolvimento_ como um fallback. Observe também que há uma URL de localhost para acessar o serviço por meio de localhost usando a funcionalidade de *encaminhamento de porta* kubernetes. Para obter mais informações sobre URLs e roteamento no Azure Dev Spaces, consulte [como o Azure dev Spaces funciona e está configurado](how-dev-spaces-works.md).

![Roteamento de espaço](media/common/Space-Routing.png)

Esta funcionalidade incorporada dos Espaços de Programador do Azure permite-lhe testar código num espaço partilhado sem que cada programador tenha de recriar a pilha completa de serviços no respetivo espaço. Este tipo de encaminhamento requer que o código da aplicação reencaminhe os cabeçalhos de propagação, conforme ilustrado no passo anterior deste guia.

### <a name="test-code-in-a-space"></a>Testar código num espaço
Para testar sua nova versão do *mywebapi* com o *WebFrontEnd*, abra o navegador para a URL do ponto de acesso público para o *WebFrontEnd* e vá para a página sobre. Deverá ver sua nova mensagem apresentada.

Agora, remova a parte do URL "scott.s." e atualize o browser. Você deve ver o comportamento antigo (com a versão *mywebapi* em execução no _dev_).

Depois de ter um espaço de _desenvolvimento_ , que sempre contém as alterações mais recentes, e supondo que seu aplicativo foi projetado para tirar proveito do roteamento baseado em espaço do DevSpace, conforme descrito nesta seção do tutorial, espero que seja fácil ver como os espaços de desenvolvimento podem auxiliar muito no teste de novos recursos dentro do contexto do aplicativo maior. Em vez de precisar implantar _todos os_ serviços em seu espaço privado, você pode criar um espaço privado que deriva de um _dev_e apenas "para cima" os serviços nos quais você está trabalhando. A infraestrutura de roteamento de espaços de desenvolvimento manipulará o restante utilizando o máximo de serviços de seu espaço privado que pode ser encontrado, ao mesmo tempo que retorna à versão mais recente em execução no espaço de _desenvolvimento_ . E, melhor ainda, _vários_ desenvolvedores podem desenvolver ativamente diferentes serviços ao mesmo tempo em seu próprio espaço, sem interromper uns aos outros.

### <a name="well-done"></a>Já está!
Concluiu o guia de introdução! Aprendeu a:

> [!div class="checklist"]
> * Configurar os Espaços de Programador do Azure com um cluster Kubernetes gerido no Azure.
> * Desenvolver iterativamente código em contentores.
> * Desenvolver de modo independente dois serviços separados e utilizar a deteção do serviço DNS de Kubernetes para fazer uma chamada para outro serviço.
> * Desenvolver e testar de forma produtiva o seu código num ambiente de equipa.
> * Estabeleça uma linha de base de funcionalidade usando espaços de desenvolvimento para testar facilmente as alterações isoladas no contexto de um aplicativo de microatendimento maior

Agora que você explorou Azure Dev Spaces, [Compartilhe seu espaço de desenvolvimento com um membro da equipe](how-to/share-dev-spaces.md) e comece a colaborar.

## <a name="clean-up"></a>Limpeza
Para eliminar completamente uma instância dos Espaços de Programador do Azure num cluster, incluindo todos os espaços de programador e serviços em execução, utilize o comando `az aks remove-dev-spaces`. Não se esqueça que esta ação é irreversível. Pode adicionar suporte aos Espaços de Programador do Azure novamente no cluster, mas será como se estivesse novamente a começar. Os seus serviços e espaços antigos não serão restaurados.

O exemplo a seguir lista os controladores dos Espaços de Programador do Azure na sua subscrição ativa e, em seguida, elimina o controlador do mesmo que está associado ao cluster do AKS "myaks" do AKS no grupo de recursos "myaks-rg".

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
