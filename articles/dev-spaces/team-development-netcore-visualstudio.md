---
title: Desenvolvimento de equipe usando o .NET Core e o Visual Studio
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: DrEsteban
ms.author: stevenry
ms.date: 12/09/2018
ms.topic: tutorial
description: Desenvolvimento rápido do Kubernetes com contentores e microsserviços no Azure
keywords: 'Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S '
ms.openlocfilehash: 895d2edbb268eab9944909ecda7193ce945bbf39
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325540"
---
# <a name="team-development-using-net-core-and-visual-studio-with-azure-dev-spaces"></a>Desenvolvimento de equipe usando o .NET Core e o Visual Studio com Azure Dev Spaces

Neste tutorial, você aprenderá como uma equipe de desenvolvedores pode colaborar simultaneamente no mesmo cluster kubernetes usando espaços de desenvolvimento.

## <a name="learn-about-team-development"></a>Saiba mais sobre desenvolvimento em equipa

Até aqui, executou o código da aplicação como se fosse o único programador a trabalhar na aplicação. Nesta secção, irá aprender até que ponto o Azure Dev Spaces simplifica o desenvolvimento em equipa ao:
* Permita que uma equipe de desenvolvedores trabalhe no mesmo ambiente, trabalhando em um espaço de desenvolvimento compartilhado ou em espaços de desenvolvimento distintos, conforme necessário.
* Suportar que cada programador faça iterações no respetivo código de forma isolada e sem receio de danificar o código de terceiros.
* Testar o código ponto a ponto, antes de proceder à consolidação do código, sem ter de criar cenários fictícios ou simular dependências.

### <a name="challenges-with-developing-microservices"></a>Desafios relativamente ao desenvolvimento de microsserviços
O aplicativo de exemplo não é complexo no momento. Todavia, num cenário de desenvolvimento real, os desafios começam a surgir à medida que são adicionados mais serviços e a equipa de desenvolvimento começa a aumentar.

* Seu computador de desenvolvimento pode não ter recursos suficientes para executar todos os serviços necessários ao mesmo tempo.
* Alguns serviços podem precisar ser acessíveis publicamente. Por exemplo, um serviço pode precisar ter um ponto de extremidade que responda a um webhook.
* Se você quiser executar um subconjunto de serviços, precisará saber a hierarquia de dependência total entre todos os seus serviços. A determinação dessa hierarquia pode ser difícil, especialmente à medida que o número de serviços aumenta.
* Alguns programadores recorrem à simulação ou criação de cenários fictícios de muitas das suas dependências de serviço. Essa abordagem pode ajudar, mas o gerenciamento dessas simulações pode afetar o custo de desenvolvimento em breve. Além disso, essa abordagem leva a um ambiente de desenvolvimento que parece diferente da produção, o que pode levar a bugs sutis ocorrendo.
* Depois disso, é difícil fazer qualquer tipo de teste de integração. Realisticamente, o teste de integração só pode ocorrer após uma consolidação, o que significa que irá ver os problemas numa fase posterior no ciclo de desenvolvimento.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Trabalhar num espaço de desenvolvimento partilhado
O Azure Dev Spaces permite-lhe configurar um espaço de desenvolvimento *partilhado* no Azure. Cada programador pode concentrar-se unicamente na respetiva parte da aplicação e, assim, desenvolver o *código de pré-consolidação* de forma iterativa num espaço de desenvolvimento que já contém todos os outros serviços e recursos na cloud de que os respetivos cenários dependem. As dependências estão sempre atualizadas e os programadores trabalham de uma forma que reflete a produção.

### <a name="work-in-your-own-space"></a>Trabalhar no seu próprio espaço
À medida que desenvolve o código para o seu serviço e até estar preparado para o implementar, é frequente o código não estar em bom estado. A formulação, os testes e as experiências com soluções ao nível do código são um processo iterativo contínuo. O Azure Dev Spaces fornece o conceito de **espaço**, onde pode trabalhar de forma isolada e sem receio de prejudicar os membros da sua equipa.

## <a name="use-dev-spaces-for-team-development"></a>Usar espaços de desenvolvimento para desenvolvimento em equipe
Vamos demonstrar essas ideias com um exemplo concreto usando nosso aplicativo de exemplo -> *mywebapi* de nosso *front-end* . Vamos imaginar um cenário em que um desenvolvedor, Scott, precisa fazer uma alteração no serviço *mywebapi* e *apenas* esse serviço. O *WebFrontEnd* não precisará ser alterado como parte da atualização de Scott.

_Sem_ usar espaços de desenvolvimento, Scott teria algumas maneiras de desenvolver e testar sua atualização, nenhuma das quais é ideal:
* Executar todos os componentes localmente, o que exige uma máquina de desenvolvimento mais potente com o Docker instalado e potencialmente MiniKube.
* Execute todos os componentes em um namespace isolado no cluster kubernetes. Como o *WebFrontEnd* não está mudando, usar um namespace isolado é um desperdício de recursos de cluster.
* SOMENTE execute *mywebapi*e faça as chamadas de REST manuais para teste. Esse tipo de teste não testa o fluxo completo de ponta a ponta.
* Adicione código centrado no desenvolvimento ao *WebFrontEnd* que permite ao desenvolvedor enviar solicitações para uma instância diferente do *mywebapi*. Adicionar esse código complica o serviço de *WebFrontEnd* .

### <a name="set-up-your-baseline"></a>Configurar sua linha de base
Primeiro, precisaremos implantar uma linha de base de nossos serviços. Essa implantação representará a "última boa conhecida" para que você possa comparar facilmente o comportamento do seu código local versus a versão com check-in. Em seguida, criaremos um espaço filho com base nessa linha de base para que possamos testar nossas alterações em *mywebapi* no contexto do aplicativo maior.

1. Clonar o [aplicativo de exemplo de espaços de desenvolvimento](https://github.com/Azure/dev-spaces): `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Confira o *azds_updates*de Branch remoto: `git checkout -b azds_updates origin/azds_updates`
1. Feche as sessões de depuração/F5 de ambos os serviços, mas mantenha os projetos abertos nas respetivas janelas do Visual Studio.
1. Alterne para a janela do Visual Studio com o projeto _mywebapi_ .
1. Clique com o botão direito do rato no projeto em **Solution Explorer** (Explorador de Soluções) e selecione **Properties** (Propriedades).
1. Selecione o separador **Debug** (Depurar) à esquerda para mostrar as definições do Azure Dev Spaces.
1. Selecione **alterar** para criar o espaço que será usado quando você F5 ou CTRL + F5 o serviço.
1. Na lista suspensa espaço, selecione **\<criar novo espaço...\>** .
1. Verifique se o espaço pai está definido como **\<nenhum\>** e digite **desenvolvimento**de nome de espaço. Clique em OK.
1. Pressione CTRL + F5 para executar _mywebapi_ sem o depurador anexado.
1. Alterne para a janela do Visual Studio com o projeto de _WebFrontEnd_ e pressione CTRL + F5 para executá-lo também.

> [!Note]
> Por vezes, é necessário atualizar o browser depois de a página Web ser apresentada inicialmente após ter premido Ctrl+F5.

> [!TIP]
> As etapas acima configuram manualmente uma linha de base, mas recomendamos que as equipes usem o CI/CD para manter a linha de base atualizada com o código confirmado.
>
> Confira nosso [guia para configurar o CI/CD com o Azure DevOps](how-to/setup-cicd.md) para criar um fluxo de trabalho semelhante ao diagrama a seguir.
>
> ![Diagrama de CI/CD de exemplo](media/common/ci-cd-complex.png)

Qualquer pessoa que abrir a URL pública e navegar até o aplicativo Web invocará o caminho de código que você escreveu, que é executado por meio dos dois serviços usando o espaço de _desenvolvimento_ padrão. Agora suponha que você deseja continuar desenvolvendo o *mywebapi* – como é possível fazer isso e não interromper outros desenvolvedores que estão usando o espaço de desenvolvimento? Para tal, terá de configurar o seu próprio espaço.

### <a name="create-a-new-dev-space"></a>Criar um novo espaço de programador
A partir do Visual Studio, pode criar espaços adicionais que serão utilizados quando prime F5 ou Ctrl+F5 no seu serviço. Pode dar o nome que quiser a um espaço e pode ser flexível quanto ao seu significado (por exemplo, _sprint4_ ou _demo_).

Faça o seguinte para criar um novo espaço:
1. Alterne para a janela do Visual Studio com o projeto *mywebapi* .
2. Clique com o botão direito do rato no projeto em **Solution Explorer** (Explorador de Soluções) e selecione **Properties** (Propriedades).
3. Selecione o separador **Debug** (Depurar) à esquerda para mostrar as definições do Azure Dev Spaces.
4. Aqui, pode alterar ou criar o cluster e/ou o espaço que será utilizado quando premir F5 ou Ctrl+F5. *Certifique-se de que o Azure Dev Space que criou anteriormente está selecionado*.
5. Na lista suspensa espaço, selecione **\<criar novo espaço...\>** .

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. Na caixa de diálogo **adicionar espaço** , defina o espaço pai como **dev**e insira um nome para o novo espaço. Pode utilizar um nome próprio (por exemplo, "scott") para o novo espaço para que os seus colegas o possam identificar como o espaço em que está a trabalhar. Clique em **OK**.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Já deverá conseguir ver o seu cluster do AKS e o novo espaço selecionado na página de propriedades do projeto.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Atualizar o código de *mywebapi*

1. No projeto *mywebapi* , faça uma alteração de código no método `string Get(int id)` no arquivo `Controllers/ValuesController.cs` da seguinte maneira:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Defina um ponto de interrupção neste bloco de código atualizado (pode já ter um definido anteriormente).
3. Pressione F5 para iniciar o serviço _mywebapi_ , que iniciará o serviço no cluster usando o espaço selecionado. Nesse caso, o espaço selecionado é _Scott_.

Segue-se um diagrama que o ajudará a compreender como funcionam os diferentes espaços. O caminho roxo mostra uma solicitação por meio do espaço de _desenvolvimento_ , que é o caminho padrão usado se nenhum espaço for anexado à URL. O caminho rosa mostra uma solicitação por meio do espaço de _desenvolvimento/Scott_ .

![](media/common/Space-Routing.png)

Esta capacidade incorporada do Azure Dev Spaces permite-lhe testar o código ponto a ponto num ambiente partilhado sem que cada programador tenha de recriar a pilha completa de serviços no respetivo espaço. Este tipo de encaminhamento requer que os cabeçalhos de propagação sejam reencaminhados no código da aplicação, conforme ilustrado no passo anterior deste guia.

### <a name="test-code-running-in-the-_devscott_-space"></a>Código de teste em execução no espaço de _desenvolvimento/Scott_
Para testar sua nova versão do *mywebapi* em conjunto com o *WebFrontEnd*, abra o navegador para a URL do ponto de acesso público para o *WebFrontEnd* (por exemplo, http://dev.webfrontend.123456abcdef.eus.azds.io) e vá para a página sobre. Deverá ver a mensagem original "Hello from webfrontend and Hello from mywebapi" (Olá de webfrontend e Olá de mywebapi).

Em seguida, adicione o elemento "scott.s." parte da URL para que ela leia algo como http\://scott.s.dev.webfrontend.123456abcdef.eus.azds.io e atualize o navegador. O ponto de interrupção que você definiu em seu projeto *mywebapi* deve ser atingido. Clique em F5 para continuar. No browser, deverá ver a nova mensagem "Hello from webfrontend and mywebapi now says something new." (Olá de webfrontend e mywebapi agora indica algo de novo). Isso ocorre porque o caminho para o código atualizado no *mywebapi* está em execução no espaço de _desenvolvimento/Scott_ .

Quando você tiver um espaço de _desenvolvimento_ que sempre contém as alterações mais recentes e supondo que seu aplicativo foi projetado para tirar proveito do roteamento baseado em espaço do DevSpace, conforme descrito nesta seção do tutorial, espero que seja fácil ver como os espaços de desenvolvimento podem auxiliar muito no teste de novos recursos no contexto do aplicativo maior. Em vez de precisar implantar _todos os_ serviços em seu espaço privado, você pode criar um espaço privado que deriva de um _dev_e apenas "para cima" os serviços nos quais você está trabalhando. A infraestrutura de roteamento de espaços de desenvolvimento manipulará o restante utilizando o máximo de serviços de seu espaço privado que pode ser encontrado, ao mesmo tempo que retorna à versão mais recente em execução no espaço de _desenvolvimento_ . E, melhor ainda, _vários_ desenvolvedores podem desenvolver ativamente diferentes serviços ao mesmo tempo em seu próprio espaço, sem interromper uns aos outros.

### <a name="well-done"></a>Já está!
Concluiu o guia de introdução! Aprendeu a:

> [!div class="checklist"]
> * Configurar os Espaços de Programador do Azure com um cluster Kubernetes gerido no Azure.
> * Desenvolver iterativamente código em contentores.
> * Desenvolver de modo independente dois serviços separados e utilizar a deteção do serviço DNS de Kubernetes para fazer uma chamada para outro serviço.
> * Desenvolver e testar de forma produtiva o seu código num ambiente de equipa.
> * Estabeleça uma linha de base de funcionalidade usando espaços de desenvolvimento para testar facilmente as alterações isoladas no contexto de um aplicativo de microatendimento maior

Agora que explorou os Espaços de Programação do Azure, [partilhe o seu espaço de programação com um membro da equipa](how-to/share-dev-spaces.md) e ajude-o a ver como é fácil colaborar em conjunto.

## <a name="clean-up"></a>Limpeza
Para eliminar completamente uma instância dos Espaços de Programador do Azure num cluster, incluindo todos os espaços de programador e serviços em execução, utilize o comando `az aks remove-dev-spaces`. Não se esqueça que esta ação é irreversível. Pode adicionar suporte aos Espaços de Programador do Azure novamente no cluster, mas será como se estivesse novamente a começar. Os seus serviços e espaços antigos não serão restaurados.

O exemplo a seguir lista os controladores dos Espaços de Programador do Azure na sua subscrição ativa e, em seguida, elimina o controlador do mesmo que está associado ao cluster do AKS "myaks" do AKS no grupo de recursos "myaks-rg".

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
