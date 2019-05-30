---
title: Gestão de recursos de configuração de aplicações do Azure | Documentos da Microsoft
description: Uma visão geral de como a configuração de aplicações do Azure pode ser utilizada para ativar e desativar funcionalidades de aplicação a pedido.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: 46f39e87e4e4cf115cbc1fceeabf0dab38fade28
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393368"
---
# <a name="feature-management-overview"></a>Descrição geral da gestão de funcionalidade

Tradicionalmente, uma nova funcionalidade de aplicativo de envio requer uma reimplantação completa do aplicativo em si. Para testar uma funcionalidade, é provável que deve implementar a aplicação muitas vezes para controlo quando o recurso se torna visível ou que obtém para vê-lo.

Gestão de recursos é uma prática de desenvolvimento de software moderno que dissocia o lançamento da funcionalidade da implementação de código e permite que as alterações rápidas a disponibilidade de funcionalidades a pedido. Ele usa uma técnica chamada *sinalizadores de recursos* (também conhecido como *funcionalidade Ativa/desativa*, *parâmetros de funcionalidade*e assim por diante) para administrar dinamicamente o ciclo de vida de um recurso.

Gestão de recursos ajuda os desenvolvedores a lidar com os seguintes problemas:

* **Gestão de ramo de código**: Sinalizadores de recurso são frequentemente utilizadas para encapsular a nova funcionalidade do aplicativo que está em desenvolvimento. Essa funcionalidade é "oculto" por predefinição. Pode enviar em segurança a funcionalidade, muito embora tenha que ache, e ele permanecerá inativo em produção. Ao utilizar esta abordagem, chamado *implementação escura*, pode libertar todo o seu código no final de cada ciclo de desenvolvimento. Não precisa manter qualquer ramo do código em vários ciclos de devido a um recurso a demorar mais do que um ciclo para serem concluídas.
* **Teste em produção**: Pode usar os sinalizadores de recurso para conceder acesso atempado à funcionalidade nova em produção. Por exemplo, pode limitar este acesso a apenas os membros da Equipe ou a testers beta interno. Estes utilizadores irão receber a experiência de produção de fidelidade total, em vez de uma experiência simulada ou parcial num ambiente de teste.
* **Distribuição de pacotes piloto**: Também pode utilizar os sinalizadores de recurso de forma incremental distribuir nova funcionalidade para os utilizadores finais. Pode direcionar uma pequena porcentagem da população do utilizador pela primeira vez e aumentar esse percentual gradualmente ao longo do tempo, depois de ter obtido mais confiança na implementação.
* **Comutador de eliminação instantânea**: Sinalizadores de recurso fornecem uma rede de segurança inerente para lançar a nova funcionalidade. Com os mesmos, prontamente pode ativar funcionalidades de aplicações e desativar. Se necessário, pode desativar rapidamente um recurso sem a reconstruir e Reimplementar a sua aplicação.
* **Ativação seletiva**: Embora a maioria dos sinalizadores de recurso existam apenas até que as suas funcionalidades associadas foram lançadas com êxito, alguns podem sobreviver por muito tempo. Pode usar os sinalizadores de recurso para segmentar os utilizadores e fornecer um conjunto específico de recursos para cada grupo. Por exemplo, pode ter um recurso que funciona apenas num determinado navegador da web. Pode definir um sinalizador de funcionalidade para que apenas os utilizadores desse navegador possam ver e utilizar a funcionalidade. Com esta abordagem, pode expandir facilmente a lista de browser suportado mais tarde sem ter de fazer quaisquer alterações de código.

## <a name="basic-concepts"></a>Conceitos básicos

Seguem-se vários novos termos relacionados com a gestão de recursos:

* **Sinalizador de funcionalidade**: Um sinalizador de funcionalidade é uma variável com o estado de binário *nos* ou *desativar*. O sinalizador de funcionalidade também tem um bloco de código associado. O estado do sinalizador de funcionalidade aciona se o bloco de código é executado ou não.
* **Gestor de recursos**: Um Gestor de recursos é um pacote de aplicação que processa o ciclo de vida de todos os sinalizadores de recurso num aplicativo. O Gestor de recursos, normalmente, fornece funcionalidades adicionais, como a colocação em cache os sinalizadores de recurso e atualizar seus Estados.
* **Filtro**: Um filtro é uma regra para avaliar o estado de um sinalizador de funcionalidade. Um grupo de utilizadores, um tipo de dispositivo ou um browser, uma localização geográfica e uma janela de tempo são todos os exemplos do que pode representar um filtro.

Uma implementação eficaz da gestão de recursos é composta por, pelo menos, dois componentes que funcionam em conjunto:

* Um aplicativo que faz com que a utilização de sinalizadores de recurso.
* Um repositório separado que armazena os sinalizadores de recurso e seus Estados atuais.

Como esses componentes interagem está ilustrado nos exemplos a seguir.

## <a name="feature-flag-usage-in-code"></a>Utilização do sinalizador de funcionalidade no código

O padrão básico para a implementação de sinalizadores de recurso num aplicativo é simples. Pode considerar um sinalizador de funcionalidade como uma variável booleana estado utilizada com um `if` instrução condicional em seu código:

```csharp
if (featureFlag) {
    // Run the following code
}
```

Neste caso, se `featureFlag` está definido como `True`, o bloco de código incluídos é executada; caso contrário, é ignorada. Pode definir o valor de `featureFlag` estaticamente, como no seguinte exemplo de código:

```csharp
bool featureFlag = true;
```

Também pode avaliar o estado do sinalizador com base em determinadas regras:

```csharp
bool featureFlag = isBetaUser();
```

Um padrão de sinalizador de funcionalidade um pouco mais complicado inclui um `else` também a instrução:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

Pode reescrever esse comportamento no padrão básico, no entanto. [Utilizar os sinalizadores de recurso numa aplicação ASP.NET Core](./use-feature-flags-dotnet-core.md) mostra as vantagens de padronização num padrão de código simples. Por exemplo:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Repositório do sinalizador de funcionalidade

Para usar os sinalizadores de recurso com eficiência, terá de externalizar a todos os sinalizadores de recurso usados num aplicativo. Esta abordagem permite-lhe alterar Estados de sinalizador de funcionalidade sem modificação e Reimplementar a aplicação em si.

Configuração de aplicações do Azure foi concebida para ser um repositório centralizado de sinalizadores de recurso. Pode usá-lo para definir tipos diferentes de sinalizadores de recurso e manipular seus Estados com rapidez e confiança. Em seguida, pode utilizar as bibliotecas de configuração de aplicações para várias arquiteturas de linguagem de programação para aceder facilmente esses sinalizadores de recurso a partir da sua aplicação.

[Utilizar os sinalizadores de recurso numa aplicação ASP.NET Core](./use-feature-flags-dotnet-core.md) mostra como o fornecedor de configuração de aplicações do .NET Core e bibliotecas de gestão de recursos são utilizadas em conjunto para implementar os sinalizadores de recurso para a sua aplicação web do ASP.NET.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Adicionar sinalizadores de recurso para uma aplicação web ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)  
