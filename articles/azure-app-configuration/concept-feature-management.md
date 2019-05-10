---
title: Gestão de recursos de configuração de aplicações do Azure | Documentos da Microsoft
description: Uma visão geral de como a configuração de aplicações do Azure pode ser utilizada para ativar e desativar funcionalidades de aplicação a pedido
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
ms.openlocfilehash: 304ca08275ccf4bf32d38e3fc89dd5cf07460fa4
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413774"
---
# <a name="feature-management"></a>Gestão de funcionalidades

Tradicionalmente, uma nova funcionalidade de aplicativo de envio requer uma reimplantação completa do aplicativo em si. Para testar uma funcionalidade, é provável que deve implementar a sua aplicação, muitas vezes para controlo de quando ele se torna visível ou que obtém para vê-lo. Gestão de recursos é uma prática de desenvolvimento de software moderno que dissocia o lançamento da funcionalidade da implementação de código e permite que as alterações rápidas a disponibilidade de funcionalidades a pedido. Ele usa uma técnica chamada *sinalizador de funcionalidade* (também conhecido como *alternância de funcionalidade*, *comutador da funcionalidade*e assim por diante) para administrar o ciclo de vida de um recurso dinamicamente. Ajuda os desenvolvedores a lidar com os seguintes problemas:

* **Gestão de ramo de código**: Sinalizadores de recurso são frequentemente utilizadas para encapsular a nova funcionalidade do aplicativo que está em desenvolvimento. Essa funcionalidade é "oculto" por predefinição. Com segurança pode transferi-lo, embora unfinished e permanecerá inativo em produção. Usando essa abordagem, chamado *implementação escura*, pode libertar todo o seu código no final de cada ciclo de desenvolvimento. Não precisa manter qualquer ramo do código em vários ciclos de devido a um recurso a demorar mais do que um período seja concluída.
* **Teste em produção**: Pode usar os sinalizadores de recurso para conceder acesso atempado à funcionalidade nova em produção. Por exemplo, pode limitar o acesso apenas os membros da Equipe ou testadores beta interno. Estes utilizadores irão receber a experiência de produção de fidelidade total, em vez de uma experiência simulada ou parcial num ambiente de teste.
* **Distribuição de pacotes piloto**: Também pode utilizar os sinalizadores de recurso para implementar novas funcionalidades para os utilizadores finais forma incremental. Pode direcionar uma pequena porcentagem da população do utilizador pela primeira vez e aumentar esse percentual gradualmente ao longo do tempo, depois de ter obtido mais confiança na implementação.
* **Comutador de eliminação instantânea**: Sinalizadores de recurso fornecem uma rede de segurança inerente para lançar a nova funcionalidade. Com os mesmos, pode não apenas ativar o mas também desativar prontamente funcionalidades da aplicação. Ganha a capacidade para desativar rapidamente um recurso, se necessário, sem a necessidade de reconstruir e Reimplementar a sua aplicação.
* **Ativação seletiva**: Embora a maioria dos sinalizadores de recurso existam apenas até que as suas funcionalidades associadas foram lançadas com êxito, alguns podem sobreviver por muito tempo. Pode usá-los como uma forma de segmentar os utilizadores e fornecer um conjunto específico de recursos para cada grupo. Por exemplo, pode ter um recurso que funciona num determinado navegador da web. Pode definir um sinalizador de funcionalidade, de modo a que apenas os utilizadores desse browser podem ver e utilizar a funcionalidade. A vantagem dessa abordagem é que pode expandir facilmente a lista de browser suportado mais tarde sem ter de fazer qualquer alteração de código.

## <a name="basic-concepts"></a>Conceitos básicos

Seguem-se vários novos termos relacionados com a gestão de recursos.

* **Sinalizador de funcionalidade**: Um sinalizador de funcionalidade é uma variável com o estado de binário *no* ou *desativar* e tem um bloco de código associado. Seu estado decide se o bloco de código é executada ou não.
* **Gestor de recursos**: Um Gestor de recursos é um pacote de aplicação que processa o ciclo de vida de todos os sinalizadores de recurso num aplicativo. Normalmente, ele fornece funcionalidades adicionais, como a colocação em cache de sinalizadores e atualização dos seus Estados.
* **Filtro**: Um filtro é uma regra para avaliar o estado de um sinalizador de funcionalidade. Grupo de utilizadores, tipo de dispositivo ou um browser, geolocalização, janela de tempo são todos os exemplos do que um filtro pode representam.

Uma implementação eficaz da gestão de recursos é composta por, pelo menos, dois componentes que funcionam em conjunto: uma aplicação que tira partido de sinalizadores de recurso e um repositório separado que armazena os sinalizadores de recurso e os respetivos Estados atuais. Suas interações estão ilustradas abaixo.

## <a name="feature-flag-usage-in-code"></a>Utilização do sinalizador de funcionalidade no código

O padrão básico para a implementação de sinalizadores de recurso num aplicativo é simples. Conceitualizar como uma variável booleana estado utilizada com um `if` instrução condicional em seu código.

```csharp
if (featureFlag) {
    // Run the following code
}
```

Neste caso, se `featureFlag` está definido como `True`, o bloco de código incluídos é executado; caso contrário, é ignorada. O valor de `featureFlag` pode ser definido estaticamente:

```csharp
bool featureFlag = true;
```

Ele também pode ser avaliado com base em determinadas regras:

```csharp
bool featureFlag = isBetaUser();
```

Um padrão de sinalizador de funcionalidade um pouco mais complicado inclui um `else` instrução também.

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

Isso pode ser reescrito no padrão básico, no entanto. [Utilizar os sinalizadores de recurso numa aplicação ASP.NET Core](./use-feature-flags-dotnet-core.md) mostra a vantagem de padronização num padrão de código simples.

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Repositório do sinalizador de funcionalidade

Para usar com eficiência, terá de externalizar a todos os sinalizadores de recurso usados num aplicativo. Isso permitirá alterar Estados de sinalizador de funcionalidade sem modificação e Reimplementar a aplicação em si. Configuração de aplicações do Azure foi concebida para ser um repositório centralizado de sinalizadores de recurso. Pode utilizar para definir tipos diferentes de sinalizadores de recurso e manipular seus Estados com rapidez e confiança. Em seguida, pode acessar esses sinalizadores de recurso facilmente da sua aplicação com as bibliotecas de configuração de aplicações para várias arquiteturas de linguagem de programação. [Utilizar os sinalizadores de recurso numa aplicação ASP.NET Core](./use-feature-flags-dotnet-core.md) mostra como o fornecedor de configuração de aplicações do .NET Core e bibliotecas de gestão de recursos são utilizadas em conjunto como uma solução completa para a implementação de sinalizadores de recurso para a sua aplicação web do ASP.NET.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Adicionar sinalizadores de recurso para uma aplicação web ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)  
