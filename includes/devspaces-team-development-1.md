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
ms.openlocfilehash: 40c1be20df845b975c023616e38cbb932c985735
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439527"
---
# <a name="team-development-with-azure-dev-spaces"></a>Desenvolvimento em equipa com o Azure Dev Spaces

Neste tutorial, irá aprender como uma equipe de desenvolvedores em simultâneo pode colaborar no mesmo cluster de Kubernetes através dos espaços de desenvolvimento.

## <a name="learn-about-team-development"></a>Saiba mais sobre desenvolvimento em equipa
Até aqui, tem executado o código da aplicação como se fosse o único programador a trabalhar na aplicação. Nesta secção, irá aprender até que ponto o Azure Dev Spaces simplifica o desenvolvimento em equipa ao:
* Ative uma equipa de programadores para trabalhar no mesmo ambiente, ao trabalhar num espaço de desenvolvimento compartilhado ou em espaços de desenvolvimento distintos, conforme necessário.
* Suportar que cada programador faça iterações no respetivo código de forma isolada e sem receio de danificar o código de terceiros.
* Testar o código ponto a ponto, antes de proceder à consolidação do código, sem ter de criar cenários fictícios ou simular dependências.

### <a name="challenges-with-developing-microservices"></a>Desafios relativamente ao desenvolvimento de microsserviços
Este exemplo de aplicação não apresenta índices de complexidade elevados até ao momento. Todavia, num cenário de desenvolvimento real, os desafios começam a surgir à medida que são adicionados mais serviços e a equipa de desenvolvimento começa a aumentar. A execução de todos os elementos para fins de desenvolvimento a nível local pode tornar-se uma perspetiva irrealista.

* O computador de desenvolvimento pode não ter recursos suficientes para executar todos os serviços que precisa de uma só vez.
* Alguns serviços poderão ter de ser acessível publicamente. Por exemplo, um serviço pode tem de ter um ponto final que responde a um webhook.
* Se quiser executar um subconjunto de serviços, precisa saber a hierarquia de dependência completa entre todos os seus serviços. Determinar que isso pode ser difícil, especialmente porque o número de serviços aumentar.
* Alguns programadores recorrem à simulação ou criação de cenários fictícios de muitas das suas dependências de serviço. Esta abordagem pode ajudar, mas gerenciar esses objetos fictícios em breve pode afetar o custo de desenvolvimento. Além disso, esta abordagem leva à aparência muito diferente da produção, o que permite que os bugs sutis aumentar seu ambiente de desenvolvimento.
* Ela segue o que fazer qualquer tipo de teste de integração torna-se difícil. Realisticamente, o teste de integração só pode ocorrer após uma consolidação, o que significa que irá ver os problemas numa fase posterior no ciclo de desenvolvimento.

    ![](../articles/dev-spaces/media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Trabalhar num espaço de desenvolvimento partilhado
O Azure Dev Spaces permite-lhe configurar um espaço de desenvolvimento *partilhado* no Azure. Cada programador pode concentrar-se unicamente na respetiva parte da aplicação e, assim, desenvolver o *código de pré-consolidação* de forma iterativa num espaço de desenvolvimento que já contém todos os outros serviços e recursos na cloud de que os respetivos cenários dependem. As dependências estão sempre atualizadas e os programadores trabalham de uma forma que reflete a produção.

### <a name="work-in-your-own-space"></a>Trabalhar no seu próprio espaço
À medida que desenvolve o código para o seu serviço e até estar preparado para o implementar, é frequente o código não estar em bom estado. A formulação, os testes e as experiências com soluções ao nível do código são um processo iterativo contínuo. O Azure Dev Spaces fornece o conceito de **espaço**, onde pode trabalhar de forma isolada e sem receio de prejudicar os membros da sua equipa.

## <a name="use-dev-spaces-for-team-development"></a>Utilizar espaços de desenvolvimento de desenvolvimento em equipe
Vamos demonstrar essas idéias com um exemplo concreto usando nosso *webfrontend* -> *mywebapi* aplicação de exemplo. Podemos irá imagine um cenário em que um desenvolvedor, Scott, tem de fazer uma alteração para o *mywebapi* serviço, e *apenas* desse serviço. O *webfrontend* não terá de alterar como parte da atualização do Scott.

_Sem_ através de espaços de desenvolvimento, Scott teria algumas formas de desenvolver e testar a atualização, nenhuma delas são ideais:
* Execute todos os componentes localmente. Isto requer um computador de desenvolvimento mais poderoso com o Docker instalado e, potencialmente, MiniKube.
* Execute todos os componentes num espaço de nomes isolado no cluster do Kubernetes. Uma vez que *webfrontend* não está a mudar, este é um desperdício de recursos do cluster.
* Executar apenas *mywebapi*e fazer chamadas REST manuais para testar. Isso não testar o fluxo de completa-a-ponto.
* Adicione o código com foco no desenvolvimento para *webfrontend* que permite ao desenvolvedor enviar pedidos para uma instância diferente do *mywebapi*. Isso complica o *webfrontend* serviço.

### <a name="set-up-your-baseline"></a>Configurar a sua linha de base
Em primeiro lugar, tem de implementar uma linha de base dos nossos serviços. Esta implementação irá representar o "último conhecido é bom", para que possa comparar facilmente o comportamento do seu código local vs. a versão de check-in. Em seguida, vamos criar um espaço de subordinados com base nesta linha de base para que possamos testar as nossas alterações para *mywebapi* dentro do contexto do aplicativo maior.

1. Clone o [aplicação de exemplo de espaços de desenvolvimento](https://github.com/Azure/dev-spaces): `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. O ramo remoto de Check-out *azds_updates*: `git checkout -b azds_updates origin/azds_updates`
1. Selecione o _dev_ espaço: `azds space select --name dev`. Quando lhe for pedido para selecionar um espaço de desenvolvimento principal, selecione  _\<none\>_.
1. Navegue para o _mywebapi_ diretório e execute: `azds up -d`
1. Navegue para o _webfrontend_ diretório e execute: `azds up -d`
1. Execute `azds list-uris` para ver o ponto final público para _webfrontend_

> [!TIP]
> Os passos acima configurar manualmente uma linha de base, mas recomendamos a utilização de equipes CI/CD para manter sua linha de base automaticamente atualizados com o código de compromisso.
>
> Confira nosso [guia para configurar o CI/CD com o Azure DevOps](../articles/dev-spaces/how-to/setup-cicd.md) para criar um fluxo de trabalho semelhantes para o diagrama seguinte.
>
> ![Diagrama de CI/CD de exemplo](../articles/dev-spaces/media/common/ci-cd-complex.png)

Neste ponto deve estar em execução sua linha de base. Execute o comando `azds list-up --all` e verá um resultado semelhante ao seguinte:

```
$ azds list-up --all

Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      dev       Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      dev       Pod      3m ago   Running
webfrontend                   dev       Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  dev       Pod      1m ago   Running
```

A coluna de DevSpace mostra que ambos os serviços estão em execução num espaço com o nome _dev_. Qualquer pessoa que abre o URL público e navega para a aplicação web invocará o caminho de check-in do código que é executado através de ambos os serviços. Agora suponha que pretende continuar a desenvolver _mywebapi_. Como pode fazer alterações ao código e testá-las, sem interromper outros programadores que estejam a utilizar o ambiente de desenvolvimento? Para tal, terá de configurar o seu próprio espaço.

### <a name="create-a-dev-space"></a>Criar um espaço de programador
Para executar a sua própria versão do _mywebapi_ num espaço que _dev_, pode criar seu próprio espaço utilizando o seguinte comando:

```cmd
azds space select --name scott
```

Quando lhe for pedido, selecione _dev_ como o **principais do espaço de desenvolvimento em**. Isso significa que o nosso novo espaço, _dev/scott_, irá derivar a partir do espaço de _dev_. Em breve verá como isto nos irá ajudar com os testes.

Acompanhando o nosso hipotético introdutório, utilizámos o nome _scott_ para o novo espaço para itens de mesmo nível podem identificar quem está trabalhando no mesmo. Mas pode ser chamado qualquer coisa, como e ser flexível sobre o que significa que, como _sprint4_ ou _demonstração_. Seja qual for o caso _dev_ serve como a linha de base para todos os desenvolvedores trabalhando numa parte desta aplicação:

![](../articles/dev-spaces/media/common/ci-cd-space-setup.png)

Execute o comando `azds space list` para ver uma lista de todos os espaços no ambiente de desenvolvimento. O _selecionados_ coluna indica que do espaço selecionadas atualmente (verdadeiro/falso). No seu caso, o espaço com o nome _dev/scott_ automaticamente selecionado quando foi criado. Pode selecionar outro espaço em qualquer altura com o comando `azds space select`.

Vamos vê-lo em ação.