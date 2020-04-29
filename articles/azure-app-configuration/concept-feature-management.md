---
title: Compreender a gestão de funcionalidades usando a configuração da app Azure
description: Ligar e desligar as funcionalidades utilizando a configuração da aplicação Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 8227810c154078fc8424b2cadd373394d07e9730
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77523735"
---
# <a name="feature-management-overview"></a>Visão geral da gestão de recursos

Tradicionalmente, o envio de uma nova funcionalidade requer uma recolocação completa da aplicação em si. Testar uma funcionalidade requer frequentemente múltiplas implementações da aplicação.  Cada implementação pode alterar a funcionalidade ou expor a funcionalidade a diferentes clientes para testes.  

A gestão de funcionalidades é uma prática moderna de desenvolvimento de software que dissocia a versão da funcionalidade a partir da implementação de códigos e permite mudanças rápidas para apresentar disponibilidade a pedido. Utiliza uma técnica chamada *bandeiras* de recurso (também conhecidas como *toggles de recurso,* interruptores de *características,* e assim por diante) para administrar dinamicamente o ciclo de vida de uma característica.

A gestão de funcionalidades ajuda os desenvolvedores a resolver os seguintes problemas:

* **Gestão de sucursais**de código : Utilize bandeiras de recurso para embrulhar a nova funcionalidade de aplicação atualmente em desenvolvimento. Tal funcionalidade é "oculta" por padrão. Você pode enviar com segurança a funcionalidade, mesmo que esteja inacabada, e permanecerá adormecida na produção. Utilizando esta abordagem, chamada *implantação escura,* pode libertar todo o seu código no final de cada ciclo de desenvolvimento. Já não é necessário manter os ramos de código em vários ciclos de desenvolvimento porque uma determinada funcionalidade requer mais de um ciclo para ser concluída.
* **Teste em produção**: Utilize bandeiras para garantir o acesso antecipado a novas funcionalidades em produção. Por exemplo, pode limitar o acesso aos membros da equipa ou aos testadores beta internos. Estes utilizadores irão experimentar a experiência de produção de fidelidade completa em vez de uma experiência simulada ou parcial num ambiente de teste.
* **Voo**: Utilize bandeiras de recurso para lançar gradualmente novas funcionalidades para os utilizadores finais. Pode visar uma pequena percentagem da população utilizadora primeiro e aumentar essa percentagem gradualmente ao longo do tempo.
* **Interruptor de desativação instantâneo**: As bandeiras de características fornecem uma rede de segurança inerente para libertar uma nova funcionalidade. Pode ligar e desligar as funcionalidades da aplicação sem reimplantar qualquer código. Se necessário, pode desativar rapidamente uma funcionalidade sem reconstruir e recolocar a sua aplicação.
* **Ativação seletiva**: Utilize bandeiras para segmentar os seus utilizadores e fornecer um conjunto específico de funcionalidades a cada grupo. Pode ter uma funcionalidade que funciona apenas num determinado navegador web. Pode definir uma bandeira de funcionalidade para que apenas os utilizadores desse navegador possam ver e utilizar a funcionalidade. Com esta abordagem, pode facilmente expandir a lista de navegadorsuportado mais tarde sem ter que fazer quaisquer alterações de código.

## <a name="basic-concepts"></a>Conceitos básicos

Aqui estão vários novos termos relacionados com a gestão de funcionalidades:

* **Bandeira de características**: Uma bandeira de característica é uma variável com um estado binário de *dentro* ou *fora*. A bandeira de recurso também tem um bloco de código associado. O estado da bandeira de recurso dispara se o bloco de código funciona.
* **Gestor de funcionalidades**: Um gestor de funcionalidades é um pacote de aplicações que lida com o ciclo de vida de todas as bandeiras de recurso numa aplicação. O gestor de funcionalidades também fornece funcionalidades adicionais, incluindo bandeiras de recurso de cache e atualização dos seus estados.
* **Filtro**: Um filtro é uma regra para avaliar o estado de uma bandeira de características. Os filtros potenciais incluem grupos de utilizadores, tipos de dispositivos ou navegadores, localizações geográficas e janelas de tempo.

Uma implementação eficaz da gestão de recursos consiste em pelo menos dois componentes que trabalham em conjunto:

* Uma aplicação que faz uso de bandeiras de recurso.
* Um repositório separado que armazena as bandeiras de recurso e os seus estados atuais.

## <a name="using-feature-flags-in-your-code"></a>Usando bandeiras de recurso no seu código

O padrão básico para a implementação de bandeiras de recurso numa aplicação é simples. Uma bandeira de recurso é uma variável do estado booleano que controla uma declaração condicional no seu código:

```csharp
if (featureFlag) {
    // Run the following code
}
```

Pode definir o `featureFlag` valor de estática.

```csharp
bool featureFlag = true;
```

Pode avaliar o estado da bandeira com base em certas regras:

```csharp
bool featureFlag = isBetaUser();
```

Pode estender o condicional para definir o comportamento da aplicação para qualquer estado:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Repositório de bandeiras

Para utilizar as bandeiras de recurso de forma eficaz, é necessário externalizar todas as bandeiras de recurso utilizadas numa aplicação. Isto permite-lhe alterar os estados de bandeira de características sem modificar e reimplantar a própria aplicação.

A Configuração de Aplicações Azure fornece um repositório centralizado para bandeiras de recurso. Pode usá-lo para definir diferentes tipos de bandeiras de características e manipular os seus estados de forma rápida e confiante. Em seguida, pode utilizar as bibliotecas de Configuração de Aplicações para vários quadros linguísticos de programação para aceder facilmente a estas bandeiras da sua aplicação.

[Utilize bandeiras de recurso numa aplicação ASP.NET Core](./use-feature-flags-dotnet-core.md) mostra como o fornecedor de configuração de aplicações do Núcleo .NET e bibliotecas de Gestão de Funcionalidades são usados em conjunto para implementar bandeiras de funcionalidades para a sua aplicação web ASP.NET.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicione bandeiras de recurso a uma aplicação web ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)  
