---
title: A equipe de desenvolvimento com espaços de desenvolvimento do Azure com .NET Core e o Visual Studio | Documentos da Microsoft
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/09/2018
ms.topic: tutorial
description: Desenvolvimento rápido da Kubernetes com contentores e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores
ms.openlocfilehash: af0a4a719f964e400119be313842f385b410406c
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817427"
---
# <a name="team-development-with-azure-dev-spaces"></a>Desenvolvimento em equipa com o Azure Dev Spaces

Neste tutorial, irá aprender como uma equipe de desenvolvedores em simultâneo pode colaborar no mesmo cluster de Kubernetes através dos espaços de desenvolvimento.

## <a name="learn-about-team-development"></a>Saiba mais sobre desenvolvimento em equipa

Até aqui, executou o código da aplicação como se fosse o único programador a trabalhar na aplicação. Nesta secção, irá aprender até que ponto o Azure Dev Spaces simplifica o desenvolvimento em equipa ao:
* Ative uma equipa de programadores para trabalhar no mesmo ambiente, ao trabalhar num espaço de desenvolvimento compartilhado ou em espaços de desenvolvimento distintos, conforme necessário.
* Suportar que cada programador faça iterações no respetivo código de forma isolada e sem receio de danificar o código de terceiros.
* Testar o código ponto a ponto, antes de proceder à consolidação do código, sem ter de criar cenários fictícios ou simular dependências.

### <a name="challenges-with-developing-microservices"></a>Desafios relativamente ao desenvolvimento de microsserviços
O exemplo de aplicação não é complexo no momento. Todavia, num cenário de desenvolvimento real, os desafios começam a surgir à medida que são adicionados mais serviços e a equipa de desenvolvimento começa a aumentar.

* O computador de desenvolvimento pode não ter recursos suficientes para executar todos os serviços que precisa de uma só vez.
* Alguns serviços poderão ter de ser acessível publicamente. Por exemplo, um serviço pode tem de ter um ponto final que responde a um webhook.
* Se quiser executar um subconjunto de serviços, precisa saber a hierarquia de dependência completa entre todos os seus serviços. Determinar esta hierarquia pode ser difícil, especialmente à medida que aumentam o número de serviços.
* Alguns programadores recorrem à simulação ou criação de cenários fictícios de muitas das suas dependências de serviço. Esta abordagem pode ajudar, mas gerenciar esses objetos fictícios em breve pode afetar o custo de desenvolvimento. Além disso, essa abordagem nos leva ao seu ambiente de desenvolvimento está à procura diferente de produção, o que pode levar a bugs sutis que ocorrem.
* Ela segue o que fazer qualquer tipo de teste de integração torna-se difícil. Realisticamente, o teste de integração só pode ocorrer após uma consolidação, o que significa que irá ver os problemas numa fase posterior no ciclo de desenvolvimento.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Trabalhar num espaço de desenvolvimento partilhado
O Azure Dev Spaces permite-lhe configurar um espaço de desenvolvimento *partilhado* no Azure. Cada programador pode concentrar-se unicamente na respetiva parte da aplicação e, assim, desenvolver o *código de pré-consolidação* de forma iterativa num espaço de desenvolvimento que já contém todos os outros serviços e recursos na cloud de que os respetivos cenários dependem. As dependências estão sempre atualizadas e os programadores trabalham de uma forma que reflete a produção.

### <a name="work-in-your-own-space"></a>Trabalhar no seu próprio espaço
À medida que desenvolve o código para o seu serviço e até estar preparado para o implementar, é frequente o código não estar em bom estado. A formulação, os testes e as experiências com soluções ao nível do código são um processo iterativo contínuo. O Azure Dev Spaces fornece o conceito de **espaço**, onde pode trabalhar de forma isolada e sem receio de prejudicar os membros da sua equipa.

## <a name="use-dev-spaces-for-team-development"></a>Utilizar espaços de desenvolvimento de desenvolvimento em equipe
Vamos demonstrar essas idéias com um exemplo concreto usando nosso *webfrontend* -> *mywebapi* aplicação de exemplo. Podemos irá imagine um cenário em que um desenvolvedor, Scott, tem de fazer uma alteração para o *mywebapi* serviço, e *apenas* desse serviço. O *webfrontend* não terá de alterar como parte da atualização do Scott.

_Sem_ através de espaços de desenvolvimento, Scott teria algumas formas de desenvolver e testar a atualização, nenhuma delas são ideais:
* Executar localmente a todos os componentes, que requer um computador de desenvolvimento mais poderoso com o Docker instalado e, potencialmente, MiniKube.
* Execute todos os componentes num espaço de nomes isolado no cluster do Kubernetes. Uma vez que *webfrontend* não está mudando, usando um isolado espaço de nomes é um desperdício de recursos de cluster.
* Executar apenas *mywebapi*e fazer chamadas REST manuais para testar. Esse tipo de teste não testar o fluxo de completa-a-ponto.
* Adicione o código com foco no desenvolvimento para *webfrontend* que permite ao desenvolvedor enviar pedidos para uma instância diferente do *mywebapi*. Adicionar este código complica a *webfrontend* serviço.

### <a name="set-up-your-baseline"></a>Configurar a sua linha de base
Em primeiro lugar, tem de implementar uma linha de base dos nossos serviços. Esta implementação irá representar o "último conhecido é bom", para que possa comparar facilmente o comportamento do seu código local vs. a versão de check-in. Em seguida, vamos criar um espaço de subordinados com base nesta linha de base para que possamos testar as nossas alterações para *mywebapi* dentro do contexto do aplicativo maior.

1. Clone o [aplicação de exemplo de espaços de desenvolvimento](https://github.com/Azure/dev-spaces): `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Dar saída ao ramo remoto *azds_updates*: `git checkout -b azds_updates origin/azds_updates`
1. Feche as sessões de depuração/F5 de ambos os serviços, mas mantenha os projetos abertos nas respetivas janelas do Visual Studio.
1. Mude para a janela do Visual Studio com o _mywebapi_ projeto.
1. Clique com o botão direito do rato no projeto em **Solution Explorer** (Explorador de Soluções) e selecione **Properties** (Propriedades).
1. Selecione o separador **Debug** (Depurar) à esquerda para mostrar as definições do Azure Dev Spaces.
1. Selecione **alteração** para criar o espaço que será utilizado quando F5 ou CTRL+F5 o serviço.
1. Na lista pendente de espaço, selecione  **\<criar novo espaço de... \>**.
1. Certifique-se de que o espaço de principal está definido como  **\<none\>** e introduza o nome do espaço **dev**. Clique em OK.
1. Prima Ctrl + F5 para executar _mywebapi_ sem o depurador anexado.
1. Mude para a janela do Visual Studio com o _webfrontend_ do projeto e prima Ctrl + F5 para executá-lo também.

> [!Note]
> Por vezes, é necessário atualizar o browser depois de a página Web ser apresentada inicialmente após ter premido Ctrl+F5.

> [!TIP]
> Os passos acima configurar manualmente uma linha de base, mas recomendamos a utilização de equipes CI/CD para manter sua linha de base automaticamente atualizados com o código de compromisso.
>
> Confira nosso [guia para configurar o CI/CD com o Azure DevOps](how-to/setup-cicd.md) para criar um fluxo de trabalho semelhantes para o diagrama seguinte.
>
> ![Diagrama de CI/CD de exemplo](media/common/ci-cd-complex.png)

Qualquer pessoa que abre o URL público e navega para a aplicação web invocará o caminho de código que escreveu qual é executado através de ambos os serviços usando o padrão _dev_ espaço. Agora suponha que pretende continuar a desenvolver *mywebapi* -como pode fazê-lo e não interrompe o outros desenvolvedores que estão a utilizar o espaço de desenvolvimento? Para tal, terá de configurar o seu próprio espaço.

### <a name="create-a-new-dev-space"></a>Criar um novo espaço de programador
A partir do Visual Studio, pode criar espaços adicionais que serão utilizados quando prime F5 ou Ctrl+F5 no seu serviço. Pode dar o nome que quiser a um espaço e pode ser flexível quanto ao seu significado (por exemplo, _sprint4_ ou _demonstração_).

Faça o seguinte para criar um novo espaço:
1. Mude para a janela do Visual Studio com o *mywebapi* projeto.
2. Clique com o botão direito do rato no projeto em **Solution Explorer** (Explorador de Soluções) e selecione **Properties** (Propriedades).
3. Selecione o separador **Debug** (Depurar) à esquerda para mostrar as definições do Azure Dev Spaces.
4. Aqui, pode alterar ou criar o cluster e/ou o espaço que será utilizado quando premir F5 ou Ctrl+F5. *Certifique-se de que o Azure Dev Space que criou anteriormente está selecionado*.
5. Na lista pendente de espaço, selecione  **\<criar novo espaço de... \>**.

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. Na **adicionar espaço** caixa de diálogo, defina o espaço de principal para **dev**e introduza um nome para o seu espaço de novo. Pode utilizar um nome próprio (por exemplo, "scott") para o novo espaço para que os seus colegas o possam identificar como o espaço em que está a trabalhar. Clique em **OK**.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Já deverá conseguir ver o seu cluster do AKS e o novo espaço selecionado na página de propriedades do projeto.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Atualizar o código de *mywebapi*

1. Na *mywebapi* projeto tornar um código de alterar para o `string Get(int id)` método no arquivo `Controllers/ValuesController.cs` da seguinte forma:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Defina um ponto de interrupção neste bloco de código atualizado (pode já ter um definido anteriormente).
3. Pressione F5 para iniciar o _mywebapi_ serviço, que irá iniciar o serviço no seu cluster com o espaço selecionado. Neste caso é o espaço selecionado _scott_.

Segue-se um diagrama que o ajudará a compreender como funcionam os diferentes espaços. O caminho roxa mostra um pedido através da _dev_ espaço, que é o caminho predefinido utilizado se não existe espaço é precedido à URL. O caminho rosa mostra um pedido através da _dev/scott_ espaço.

![](media/common/Space-Routing.png)

Esta capacidade incorporada do Azure Dev Spaces permite-lhe testar o código ponto a ponto num ambiente partilhado sem que cada programador tenha de recriar a pilha completa de serviços no respetivo espaço. Este tipo de encaminhamento requer que os cabeçalhos de propagação sejam reencaminhados no código da aplicação, conforme ilustrado no passo anterior deste guia.

### <a name="test-code-running-in-the-devscott-space"></a>Testar o código em execução no _dev/scott_ espaço
Para testar a nova versão do *mywebapi* em conjunto com *webfrontend*, abra o browser para o URL do ponto de acesso público para *webfrontend* (por exemplo, http://dev.webfrontend.123456abcdef.eus.azds.io)e aceda à página About. Deverá ver a mensagem original "Hello from webfrontend and Hello from mywebapi" (Olá de webfrontend e Olá de mywebapi).

Em seguida, adicione o elemento "scott.s." ao URL de modo a mostrar algo semelhante a http://scott.s.dev.webfrontend.123456abcdef.eus.azds.io e atualize o browser. O ponto de interrupção definido no seu *mywebapi* projeto deve ser atingido. Clique em F5 para continuar. No browser, deverá ver a nova mensagem "Hello from webfrontend and mywebapi now says something new." (Olá de webfrontend e mywebapi agora indica algo de novo). Isto acontece porque o caminho para os seus códigos atualizados no *mywebapi* está em execução no _dev/scott_ espaço.

Depois de ter uma _dev_ espaço que contém sempre suas alterações mais recentes e supondo que a aplicação foi concebido para tirar partido do DevSpace baseados no espaço Encaminhamento conforme descrito nesta secção do tutorial, Espero que ele se torna mais fácil ver como os espaços de desenvolvimento muito pode ajudar no teste de novos recursos dentro do contexto do aplicativo maior. Em vez de precisar implantar _todos os_ serviços no seu espaço privado, pode criar um espaço privado que deriva de _dev_e "apenas up" os serviços que está realmente a trabalhar. A infraestrutura de encaminhamento de espaços de desenvolvimento irá processar o resto utilizando os serviços fora do seu espaço de privada pois pode encontrar, ao utilizar a predefinição para a versão mais recente em execução no _dev_ espaço. E ainda assim, melhor _vários_ os programadores podem ativamente desenvolver serviços diferentes ao mesmo tempo no seu próprio espaço sem interromper entre si.

### <a name="well-done"></a>Já está!
Concluiu o guia de introdução! Aprendeu a:

> [!div class="checklist"]
> * Configurar os Espaços de Programador do Azure com um cluster Kubernetes gerido no Azure.
> * Desenvolver iterativamente código em contentores.
> * Desenvolver de modo independente dois serviços separados e utilizar a deteção do serviço DNS de Kubernetes para fazer uma chamada para outro serviço.
> * Desenvolver e testar de forma produtiva o seu código num ambiente de equipa.
> * Estabelecer uma linha de base da funcionalidade através de espaços de programador para testar facilmente isoladas de alterações dentro do contexto de uma aplicação de microsserviços maior

Agora que explorou os Espaços de Programação do Azure, [partilhe o seu espaço de programação com um membro da equipa](how-to/share-dev-spaces.md) e ajude-o a ver como é fácil colaborar em conjunto.

## <a name="clean-up"></a>Limpeza
Para eliminar completamente uma instância dos Espaços de Programador do Azure num cluster, incluindo todos os espaços de programador e serviços em execução, utilize o comando `az aks remove-dev-spaces`. Não se esqueça que esta ação é irreversível. Pode adicionar suporte aos Espaços de Programador do Azure novamente no cluster, mas será como se estivesse novamente a começar. Os seus serviços e espaços antigos não serão restaurados.

O exemplo a seguir lista os controladores dos Espaços de Programador do Azure na sua subscrição ativa e, em seguida, elimina o controlador do mesmo que está associado ao cluster do AKS "myaks" do AKS no grupo de recursos "myaks-rg".

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```