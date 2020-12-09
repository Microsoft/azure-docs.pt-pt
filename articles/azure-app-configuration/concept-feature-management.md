---
title: Compreender a gestão de funcionalidades usando a Configuração da Aplicação Azure
description: Ligue e desligue as funcionalidades utilizando a configuração da aplicação Azure
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-dotnet
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: cfd676092bcaede58909a3ec1eefeabb4c80f86b
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930233"
---
# <a name="feature-management-overview"></a>Visão geral da gestão de recursos

Tradicionalmente, o envio de uma nova funcionalidade de aplicação requer uma redistribuição completa da própria aplicação. Testar uma funcionalidade requer frequentemente múltiplas implementações da aplicação.  Cada implementação pode alterar a funcionalidade ou expor a funcionalidade a diferentes clientes para testes.  

A gestão de recursos é uma prática moderna de desenvolvimento de software que separa a versão de funcionalidades da implementação de código e permite mudanças rápidas para a disponibilidade de recursos a pedido. Utiliza uma técnica chamada *bandeiras de recurso* (também conhecidas como *óculos de recurso,* *interruptores de recurso*, e assim por diante) para administrar dinamicamente o ciclo de vida de uma característica.

A gestão de recursos ajuda os desenvolvedores a resolver os seguintes problemas:

* **Gestão de agências de** código : Utilize bandeiras de características para envolver a nova funcionalidade da aplicação atualmente em desenvolvimento. Esta funcionalidade é "oculta" por defeito. Pode enviar a funcionalidade com segurança, mesmo que esteja inacabada, e permanecerá inativa na produção. Utilizando esta abordagem, chamada *dark deployment,* pode libertar todo o seu código no final de cada ciclo de desenvolvimento. Já não é necessário manter os ramos de código em vários ciclos de desenvolvimento porque uma determinada funcionalidade requer mais de um ciclo para ser concluída.
* **Teste na produção**: Utilize bandeiras de características para garantir o acesso antecipado a novas funcionalidades na produção. Por exemplo, pode limitar o acesso aos membros da equipa ou aos testadores beta internos. Estes utilizadores irão experimentar a experiência de produção de fidelidade total em vez de uma experiência simulada ou parcial num ambiente de teste.
* **Voo :** Utilize bandeiras de características para lançar gradualmente uma nova funcionalidade para os utilizadores finais. Primeiro, pode direcionar uma pequena percentagem da sua população utilizadora e aumentar essa percentagem gradualmente ao longo do tempo.
* **Interruptor de desativação instantâneo**: As bandeiras de recurso fornecem uma rede de segurança inerente para libertar novas funcionalidades. Pode ligar e desligar as funcionalidades da aplicação sem recolocar qualquer código. Se necessário, pode desativar rapidamente uma funcionalidade sem reconstruir e recolocar a sua aplicação.
* **Ativação seletiva**: Utilize bandeiras de características para segmentar os seus utilizadores e entregar um conjunto específico de funcionalidades a cada grupo. Você pode ter uma funcionalidade que funciona apenas em um determinado navegador web. Pode definir uma bandeira de funcionalidade para que apenas os utilizadores desse navegador possam ver e utilizar a funcionalidade. Com esta abordagem, pode facilmente expandir a lista de navegadores suportados mais tarde sem ter de escoar quaisquer alterações de código.

## <a name="basic-concepts"></a>Conceitos básicos

Aqui estão vários novos termos relacionados com a gestão de recursos:

* **Bandeira de características**: Uma bandeira de características é uma variável com um estado binário de *dentro* ou *fora*. A bandeira de recurso também tem um bloco de código associado. O estado da bandeira de recurso despoleta se o bloco de código funciona.
* **Gestor de recursos**: Um gestor de funcionalidades é um pacote de aplicações que lida com o ciclo de vida de todas as bandeiras de funcionalidades numa aplicação. O gestor de funcionalidades também fornece funcionalidades adicionais, incluindo bandeiras de funcionalidades de caching e atualização dos seus estados.
* **Filtro**: Um filtro é uma regra para avaliar o estado de uma bandeira de características. Os filtros potenciais incluem grupos de utilizadores, tipos de dispositivos ou navegadores, localizações geográficas e janelas de tempo.

Uma implementação eficaz da gestão de recursos consiste em pelo menos dois componentes que trabalham em conjunto:

* Uma aplicação que faz uso de bandeiras de recurso.
* Um repositório separado que armazena as bandeiras de características e os seus estados atuais.

## <a name="using-feature-flags-in-your-code"></a>Usando bandeiras de recurso no seu código

O padrão básico para implementar bandeiras de características numa aplicação é simples. Uma bandeira de recurso é uma variável do estado booleano que controla uma declaração condicional no seu código:

```csharp
if (featureFlag) {
    // Run the following code
}
```

Pode definir o valor da `featureFlag` estática.

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

## <a name="feature-flag-repository"></a>Repositório de bandeira de recurso

Para utilizar as bandeiras de funcionalidades de forma eficaz, é necessário externalizar todas as bandeiras de funcionalidades utilizadas numa aplicação. Isto permite-lhe alterar estados de bandeira de funcionalidade sem modificar e redistribuir a própria aplicação.

A Configuração de Aplicativos Azure fornece um repositório centralizado para bandeiras de características. Você pode usá-lo para definir diferentes tipos de bandeiras de características e manipular os seus estados de forma rápida e confiante. Em seguida, pode utilizar as bibliotecas de Configuração de Aplicações para várias estruturas linguísticas de programação para aceder facilmente a estas bandeiras de funcionalidades a partir da sua aplicação.

[Utilize bandeiras de funcionalidades numa aplicação core ASP.NET](./use-feature-flags-dotnet-core.md) mostra como o fornecedor de configuração de aplicação de aplicação de aplicação .NET Core e as bibliotecas de Gestão de Recursos são usadas em conjunto para implementar bandeiras de funcionalidades para a sua aplicação web ASP.NET.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicione bandeiras de recurso a uma aplicação web core ASP.NET](./quickstart-feature-flag-aspnet-core.md)  
