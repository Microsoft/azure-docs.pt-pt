---
title: Gerenciamento de recursos de configuração do Azure App
description: Uma visão geral de como Azure App configuração pode ser usada para ativar e desativar recursos de aplicativo sob demanda.
author: yegu-ms
ms.author: yegu
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 48aebfe1ba6af348e98e5600075f3a9e9dce1a8e
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495768"
---
# <a name="feature-management-overview"></a>Visão geral do gerenciamento de recursos

Tradicionalmente, o envio de um novo recurso de aplicativo requer uma reimplantação completa do próprio aplicativo. Para testar um recurso, você provavelmente deve implantar seu aplicativo muitas vezes para controlar quando o recurso se torna visível ou quem consegue vê-lo.

O gerenciamento de recursos é uma prática moderna de desenvolvimento de software que separa o lançamento de recursos da implantação de código e permite alterações rápidas na disponibilidade de recursos sob demanda. Ele usa uma técnica chamada *sinalizadores de recursos* (também conhecidos como *alternâncias de recursos*, opções de *recursos*e assim por diante) para administrar dinamicamente o ciclo de vida de um recurso.

O gerenciamento de recursos ajuda os desenvolvedores a lidar com os seguintes problemas:

* **Gerenciamento de ramificação de código**: os sinalizadores de recursos são usados frequentemente para encapsular a nova funcionalidade do aplicativo que está em desenvolvimento. Essa funcionalidade é "oculta" por padrão. Você pode enviar o recurso com segurança, embora ele não seja concluído, e ele permanecerá inativo na produção. Ao usar essa abordagem, chamada *implantação escura*, você pode liberar todo o seu código ao final de cada ciclo de desenvolvimento. Você não precisa mais manter qualquer ramificação de código entre vários ciclos devido a um recurso que leva mais de um ciclo para ser concluído.
* **Teste em produção**: você pode usar sinalizadores de recurso para conceder acesso antecipado a novas funcionalidades na produção. Por exemplo, você pode limitar esse acesso apenas a membros da equipe ou a testadores beta internos. Esses usuários receberão a experiência de produção de fidelidade total, em vez de uma experiência simulada ou parcial em um ambiente de teste.
* **Comprovando**: você também pode usar sinalizadores de recurso para distribuir incrementalmente novas funcionalidades para os usuários finais. Você pode direcionar uma pequena porcentagem da população do usuário primeiro e aumentar essa porcentagem gradualmente ao longo do tempo, depois de ter obtido mais confiança na implementação.
* **Interruptor de eliminação instantânea**: os sinalizadores de recursos fornecem uma rede de segurança inerente para liberar novas funcionalidades. Com eles, você pode rapidamente ativar e desativar recursos do aplicativo. Se necessário, você pode desabilitar rapidamente um recurso sem recompilar e reimplantar seu aplicativo.
* **Ativação seletiva**: embora a maioria dos sinalizadores de recurso existam somente até que suas funcionalidades associadas tenham sido liberadas com êxito, alguns podem durar muito tempo. Você pode usar os sinalizadores de recurso para segmentar seus usuários e fornecer um conjunto específico de recursos para cada grupo. Por exemplo, você pode ter um recurso que funciona apenas em um determinado navegador da Web. Você pode definir um sinalizador de recurso para que somente os usuários desse navegador possam ver e usar o recurso. Com essa abordagem, você pode expandir facilmente a lista de navegadores com suporte mais tarde sem precisar fazer nenhuma alteração de código.

## <a name="basic-concepts"></a>Conceitos básicos

Aqui estão vários novos termos relacionados ao gerenciamento de recursos:

* **Sinalizador de recurso**: um sinalizador de recurso é uma variável com um estado binário de *ativado* ou *desativado*. O sinalizador de recurso também tem um bloco de código associado. O estado do sinalizador de recurso dispara se o bloco de código é executado ou não.
* **Gerenciador de recursos**: um Gerenciador de recursos é um pacote de aplicativos que manipula o ciclo de vida de todos os sinalizadores de recurso em um aplicativo. Normalmente, o Gerenciador de recursos fornece funcionalidade adicional, como sinalizadores de recurso de cache e atualização de seus Estados.
* **Filtro**: um filtro é uma regra para avaliar o estado de um sinalizador de recurso. Um grupo de usuários, um tipo de dispositivo ou navegador, uma localização geográfica e uma janela de tempo são exemplos do que um filtro pode representar.

Uma implementação eficaz do gerenciamento de recursos consiste em pelo menos dois componentes trabalhando em conjunto:

* Um aplicativo que usa sinalizadores de recurso.
* Um repositório separado que armazena os sinalizadores de recurso e seus Estados atuais.

A maneira como esses componentes interagem é ilustrada nos exemplos a seguir.

## <a name="feature-flag-usage-in-code"></a>Uso do sinalizador de recurso no código

O padrão básico para a implementação de sinalizadores de recursos em um aplicativo é simples. Você pode considerar um sinalizador de recurso como uma variável de estado booliana usada com uma `if` instrução condicional em seu código:

```csharp
if (featureFlag) {
    // Run the following code
}
```

Nesse caso, se `featureFlag` for definido como `True`, o bloco de código incluído será executado; caso contrário, ele será ignorado. Você pode definir o valor de `featureFlag` estaticamente, como no exemplo de código a seguir:

```csharp
bool featureFlag = true;
```

Você também pode avaliar o estado do sinalizador com base em determinadas regras:

```csharp
bool featureFlag = isBetaUser();
```

Um padrão de sinalizador de recurso um pouco mais complicado também inclui uma instrução `else`:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

No entanto, é possível reescrever esse comportamento no padrão básico. [Usar sinalizadores de recurso em um aplicativo ASP.NET Core](./use-feature-flags-dotnet-core.md) mostra as vantagens de padronizar em um padrão de código simples. Por exemplo:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Repositório de sinalizador de recurso

Para usar sinalizadores de recurso com eficiência, você precisa externalizar todos os sinalizadores de recurso usados em um aplicativo. Essa abordagem permite alterar os Estados de sinalizador de recursos sem modificar e reimplantar o próprio aplicativo.

Azure App configuração foi projetada para ser um repositório centralizado para sinalizadores de recursos. Você pode usá-lo para definir diferentes tipos de sinalizadores de recursos e manipular seus Estados de forma rápida e confiável. Você pode usar as bibliotecas de configuração de aplicativo para várias estruturas de linguagem de programação para acessar facilmente esses sinalizadores de recursos do seu aplicativo.

[Usar sinalizadores de recurso em um aplicativo ASP.NET Core](./use-feature-flags-dotnet-core.md) mostra como as bibliotecas de gerenciamento de recursos e o provedor de configuração de aplicativo .NET Core são usadas em conjunto para implementar sinalizadores de recurso para seu aplicativo Web ASP.net.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicionar sinalizadores de recurso a um aplicativo Web ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)  
