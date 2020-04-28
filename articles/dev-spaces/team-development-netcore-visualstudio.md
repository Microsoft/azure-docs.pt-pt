---
title: Desenvolvimento de equipa usando .NET Core e Visual Studio
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: DrEsteban
ms.author: stevenry
ms.date: 12/09/2018
ms.topic: tutorial
description: Este tutorial mostra-lhe como usar o Azure Dev Spaces e o Visual Studio para fazer o desenvolvimento da equipa numa aplicação .NET Core no Serviço Azure Kubernetes
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s '
ms.openlocfilehash: c84c77fe7a425318700903427ff1c4aaa4e73a11
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82166041"
---
# <a name="team-development-using-net-core-and-visual-studio-with-azure-dev-spaces"></a>Desenvolvimento de equipa utilizando .NET Core e Visual Studio com Espaços Azure Dev

Neste tutorial, você vai aprender como uma equipe de desenvolvedores pode simultaneamente colaborar no mesmo cluster Kubernetes usando Dev Spaces.

## <a name="learn-about-team-development"></a>Saiba mais sobre desenvolvimento em equipa

Até aqui, executou o código da aplicação como se fosse o único programador a trabalhar na aplicação. Nesta secção, irá aprender até que ponto o Azure Dev Spaces simplifica o desenvolvimento em equipa ao:
* Permitir que uma equipa de desenvolvedores trabalhe no mesmo ambiente, trabalhando num espaço de v partilhado ou em espaços de v distintos, conforme necessário.
* Suportar que cada programador faça iterações no respetivo código de forma isolada e sem receio de danificar o código de terceiros.
* Testar o código ponto a ponto, antes de proceder à consolidação do código, sem ter de criar cenários fictícios ou simular dependências.

### <a name="challenges-with-developing-microservices"></a>Desafios relativamente ao desenvolvimento de microsserviços
A sua aplicação de amostra não é complexa no momento. Todavia, num cenário de desenvolvimento real, os desafios começam a surgir à medida que são adicionados mais serviços e a equipa de desenvolvimento começa a aumentar.

* A sua máquina de desenvolvimento pode não ter recursos suficientes para executar todos os serviços que precisa ao mesmo tempo.
* Alguns serviços podem ter de ser acessíveis publicamente. Por exemplo, um serviço pode precisar de ter um ponto final que responda a um webhook.
* Se quiser executar um subconjunto de serviços, tem de conhecer toda a hierarquia de dependência entre todos os seus serviços. Determinar esta hierarquia pode ser difícil, especialmente à medida que o seu número de serviços aumenta.
* Alguns programadores recorrem à simulação ou criação de cenários fictícios de muitas das suas dependências de serviço. Esta abordagem pode ajudar, mas gerir essas simulações pode em breve ter impacto nos custos de desenvolvimento. Além disso, esta abordagem leva ao seu ambiente de desenvolvimento diferente da produção, o que pode levar a bugs subtis que ocorrem.
* Daí resulta que fazer qualquer tipo de teste de integração torna-se difícil. Realisticamente, o teste de integração só pode ocorrer após uma consolidação, o que significa que irá ver os problemas numa fase posterior no ciclo de desenvolvimento.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Trabalhar num espaço de desenvolvimento partilhado
O Azure Dev Spaces permite-lhe configurar um espaço de desenvolvimento *partilhado* no Azure. Cada programador pode concentrar-se unicamente na respetiva parte da aplicação e, assim, desenvolver o *código de pré-consolidação* de forma iterativa num espaço de desenvolvimento que já contém todos os outros serviços e recursos na cloud de que os respetivos cenários dependem. As dependências estão sempre atualizadas e os programadores trabalham de uma forma que reflete a produção.

### <a name="work-in-your-own-space"></a>Trabalhar no seu próprio espaço
À medida que desenvolve o código para o seu serviço e até estar preparado para o implementar, é frequente o código não estar em bom estado. A formulação, os testes e as experiências com soluções ao nível do código são um processo iterativo contínuo. O Azure Dev Spaces fornece o conceito de **espaço**, onde pode trabalhar de forma isolada e sem receio de prejudicar os membros da sua equipa.

## <a name="use-dev-spaces-for-team-development"></a>Use Espaços Dev para desenvolvimento de equipas
Vamos demonstrar estas ideias com um exemplo concreto usando a nossa aplicação de amostra*mywebapi* *webfrontend.* ->  Vamos imaginar um cenário em que um desenvolvedor, Scott, precisa de fazer uma mudança para o serviço *mywebapi,* e *só* para esse serviço. O *webfrontend* não vai precisar de mudar como parte da atualização do Scott.

_Sem_ usar dev Spaces, Scott teria algumas maneiras de desenvolver e testar a sua atualização, nenhuma das quais é ideal:
* Executar os componentes ALL localmente, que requer uma máquina de desenvolvimento mais potente com Docker instalado, e potencialmente MiniKube.
* Executar todos os componentes num espaço de nome isolado no cluster Kubernetes. Como *o webfrontend* não está a mudar, usar um espaço de nome isolado é um desperdício de recursos de cluster.
* Só execute *mywebapi*e faça chamadas manuais de REST para testar. Este tipo de testes não testa o fluxo total de ponta a ponta.
* Adicione código focado no desenvolvimento ao *webfrontend* que permite ao desenvolvedor enviar pedidos para uma instância diferente de *mywebapi*. Adicionar este código complica o serviço *webfrontend.*

### <a name="set-up-your-baseline"></a>Configurar a sua linha de base
Primeiro, precisamos de implantar uma linha de base dos nossos serviços. Esta implementação representará o "último bem conhecido" para que possa facilmente comparar o comportamento do seu código local vs. a versão check-in. Criaremos então um espaço para crianças com base nesta linha de base para que possamos testar as nossas alterações ao *mywebapi* no contexto da aplicação maior.

1. Clone a [aplicação da amostra de Espaços Dev:](https://github.com/Azure/dev-spaces)`git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Confira o ramo remoto *azds_updates:*`git checkout -b azds_updates origin/azds_updates`
1. Feche as sessões de depuração/F5 de ambos os serviços, mas mantenha os projetos abertos nas respetivas janelas do Visual Studio.
1. Mude para a janela do Estúdio Visual com o projeto _mywebapi._
1. Clique com o botão direito do rato no projeto em **Solution Explorer** (Explorador de Soluções) e selecione **Properties** (Propriedades).
1. Selecione o separador **Debug** (Depurar) à esquerda para mostrar as definições do Azure Dev Spaces.
1. Selecione **Alterar** para criar o espaço que será utilizado quando f5 ou CTRL+F5 o serviço.
1. No dropdown do Espaço, selecione ** \<Criar Novo Espaço... \>**.
1. Certifique-se de que ** \<o\>** espaço dos pais está definido para nenhum , e introduza o nome de espaço **dev**. Clique em OK.
1. Pressione Ctrl+F5 para executar _mywebapi_ sem o descato ligado.
1. Mude para a janela do Estúdio Visual com o projeto _webfrontend_ e prima Ctrl+F5 para executá-lo também.

> [!Note]
> Por vezes, é necessário atualizar o browser depois de a página Web ser apresentada inicialmente após ter premido Ctrl+F5.

> [!TIP]
> Os passos acima configuram manualmente uma linha de base, mas recomendamos que as equipas utilizem CI/CD para manter automaticamente a sua linha de base atualizada com o código comprometido.
>
> Consulte o nosso guia para a configuração de [CI/CD com Azure DevOps](how-to/setup-cicd.md) para criar um fluxo de trabalho semelhante ao diagrama seguinte.
>
> ![Diagrama de CI/CD de exemplo](media/common/ci-cd-complex.png)

Qualquer pessoa que abra o URL público e navegue para a aplicação web invocará o caminho de código que escreveu que percorre ambos os serviços usando o espaço _de v_ padrão. Agora suponha que quer continuar a desenvolver *mywebapi* - como pode fazer isso e não interromper outros desenvolvedores que estão usando o espaço de v? Para tal, terá de configurar o seu próprio espaço.

### <a name="create-a-new-dev-space"></a>Criar um novo espaço de programador
A partir do Visual Studio, pode criar espaços adicionais que serão utilizados quando prime F5 ou Ctrl+F5 no seu serviço. Pode dar o nome que quiser a um espaço e pode ser flexível quanto ao seu significado (por exemplo, _sprint4_ ou _demonstração)._

Faça o seguinte para criar um novo espaço:
1. Mude para a janela do Estúdio Visual com o projeto *mywebapi.*
2. Clique com o botão direito do rato no projeto em **Solution Explorer** (Explorador de Soluções) e selecione **Properties** (Propriedades).
3. Selecione o separador **Debug** (Depurar) à esquerda para mostrar as definições do Azure Dev Spaces.
4. Aqui, pode alterar ou criar o cluster e/ou o espaço que será utilizado quando premir F5 ou Ctrl+F5. *Certifique-se de que o Azure Dev Space que criou anteriormente está selecionado*.
5. No dropdown do Espaço, selecione ** \<Criar Novo Espaço... \>**.

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. No diálogo **Add Space,** desloque o espaço dos pais para **dev,** e introduza um nome para o seu novo espaço. Pode utilizar um nome próprio (por exemplo, "scott") para o novo espaço para que os seus colegas o possam identificar como o espaço em que está a trabalhar. Clique em **OK**.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Já deverá conseguir ver o seu cluster do AKS e o novo espaço selecionado na página de propriedades do projeto.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Atualizar o código de *mywebapi*

1. No projeto *mywebapi* faça uma `string Get(int id)` alteração de `Controllers/ValuesController.cs` código para o método em ficheiro da seguinte forma:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Defina um ponto de interrupção neste bloco de código atualizado (pode já ter um definido anteriormente).
3. Acerte f5 para iniciar o serviço _mywebapi,_ que iniciará o serviço no seu cluster utilizando o espaço selecionado. O espaço selecionado neste caso é _scott_.

Segue-se um diagrama que o ajudará a compreender como funcionam os diferentes espaços. O caminho roxo mostra um pedido através do espaço _de v,_ que é o caminho padrão usado se nenhum espaço for preparado para o URL. O caminho cor-de-rosa mostra um pedido através do espaço _dev/scott._

![](media/common/Space-Routing.png)

Esta capacidade incorporada do Azure Dev Spaces permite-lhe testar o código ponto a ponto num ambiente partilhado sem que cada programador tenha de recriar a pilha completa de serviços no respetivo espaço. Este tipo de encaminhamento requer que os cabeçalhos de propagação sejam reencaminhados no código da aplicação, conforme ilustrado no passo anterior deste guia.

### <a name="test-code-running-in-the-_devscott_-space"></a>Código de teste em execução no espaço _dev/scott_
Para testar a sua nova versão do *mywebapi* em conjunto com o *webfrontend,* abra `http://dev.webfrontend.123456abcdef.eus.azds.io`o seu navegador para o URL do ponto de acesso público para *webfrontend* (por exemplo, ) e vá para a página Sobre. Deverá ver a mensagem original "Hello from webfrontend and Hello from mywebapi" (Olá de webfrontend e Olá de mywebapi).

Em seguida, adicione o elemento "scott.s." parte para o URL para que\:leia algo como http /scott.s.dev.webfrontend.123456abcdef.eus.azds.io e atualizar o navegador. O ponto de rutura que definiu no seu projeto *mywebapi* deve ser atingido. Clique em F5 para continuar. No browser, deverá ver a nova mensagem "Hello from webfrontend and mywebapi now says something new." (Olá de webfrontend e mywebapi agora indica algo de novo). Isto porque o caminho para o seu código atualizado em *mywebapi* está a correr no espaço _de v/scott._

Uma vez que você tem um espaço _de v_ que sempre contém as suas últimas alterações, e assumindo que a sua aplicação foi projetada para tirar partido do encaminhamento espacial da DevSpace como descrito nesta secção tutorial, espero que seja fácil ver como a Dev Spaces pode ajudar muito no teste de novas funcionalidades no contexto da aplicação maior. Em vez de ter de implantar _todos os_ serviços para o seu espaço privado, pode criar um espaço privado que deriva do _dev_, e apenas "subir" os serviços em que está realmente a trabalhar. A infraestrutura de encaminhamento de Dev Spaces irá lidar com o resto utilizando o maior número de serviços fora do seu espaço privado, enquanto não volta à versão mais recente que corre no espaço _de v._ E melhor ainda, _vários_ desenvolvedores podem desenvolver ativamente diferentes serviços ao mesmo tempo no seu próprio espaço sem perturbar uns aos outros.

### <a name="well-done"></a>Já está!
Concluiu o guia de introdução! Aprendeu a:

> [!div class="checklist"]
> * Configurar os Espaços de Programador do Azure com um cluster Kubernetes gerido no Azure.
> * Desenvolver iterativamente código em contentores.
> * Desenvolver de modo independente dois serviços separados e utilizar a deteção do serviço DNS de Kubernetes para fazer uma chamada para outro serviço.
> * Desenvolver e testar de forma produtiva o seu código num ambiente de equipa.
> * Estabelecer uma linha de base de funcionalidade utilizando espaços Dev para testar facilmente alterações isoladas no contexto de uma aplicação de microserviço maior

Agora que explorou os Espaços de Programação do Azure, [partilhe o seu espaço de programação com um membro da equipa](how-to/share-dev-spaces.md) e ajude-o a ver como é fácil colaborar em conjunto.

## <a name="clean-up"></a>Limpeza
Para eliminar completamente uma instância dos Espaços de Programador do Azure num cluster, incluindo todos os espaços de programador e serviços em execução, utilize o comando `az aks remove-dev-spaces`. Não se esqueça que esta ação é irreversível. Pode adicionar suporte aos Espaços de Programador do Azure novamente no cluster, mas será como se estivesse novamente a começar. Os seus serviços e espaços antigos não serão restaurados.

O exemplo a seguir lista os controladores dos Espaços de Programador do Azure na sua subscrição ativa e, em seguida, elimina o controlador do mesmo que está associado ao cluster do AKS "myaks" do AKS no grupo de recursos "myaks-rg".

```cmd
azds controller list
```

```azurecli
az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
