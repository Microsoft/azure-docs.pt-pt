---
title: Escolha a plataforma de desenvolvimento de front-end para criar aplicativos cliente com o Visual Studio e os serviços do Azure
description: Saiba mais sobre as linguagens nativas e de plataforma cruzada com suporte para criar aplicativos cliente.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 891967e74e52c1311d73cbe13fff8c95e479478c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795500"
---
# <a name="choose-mobile-development-framework"></a>Escolha a estrutura de desenvolvimento móvel
Os desenvolvedores podem usar tecnologias do lado do cliente para criar aplicativos móveis, usando estruturas e padrões específicos para uma abordagem de plataforma cruzada. Os desenvolvedores podem criar aplicativos nativos (nativos de plataforma única usando linguagens como Objective-C e Java), aplicativos de plataforma cruzada usando Xamarin, C#.net e) e híbrido (usando Cordova) e suas variantes, dependendo de sua decisão fatores.

## <a name="native-platforms"></a>Plataformas nativas
A criação de um aplicativo nativo requer linguagens de programação específicas da plataforma, SDKs, ambiente de desenvolvimento e outras ferramentas fornecidas pelos fornecedores do sistema operacional.

### <a name="ios"></a>iOS
Criado e desenvolvido pela Apple, os aplicativos criados no iOS são executados em dispositivos Apple, ou seja, iPhone e iPad.

- **Linguagens de programação** – Objective-C, Swift
- **IDE** -Xcode
- **SDK-SDK** do IOS

### <a name="android"></a>Android
Projetado pelo Google e pelo sistema operacional mais popular do mundo, os aplicativos criados no Android podem ser executados em uma variedade de smartphones e tablets.

- **Linguagem de programação** -Java, Kotlin 
- Android Studio **IDE** e Android ferramentas para desenvolvedores 
- **SDK** -SDK do Android

### <a name="windows"></a>Windows
- **Linguagem de programação** -C#
- **IDE** -Visual Studio, Visual Studio Code
- **SDK** -SDK do Windows

#### <a name="pros"></a>Prós
- Boa experiência do usuário
- Aplicativos responsivos com alto desempenho e capacidade de fazer interface com bibliotecas nativas
- Aplicativos altamente seguros

#### <a name="cons"></a>Contras
- O aplicativo é executado somente em uma plataforma
- Mais recursos para desenvolvedores e caros para criar um aplicativo
- Menor reutilização de código

## <a name="cross-platforms-and-hybrid-applications"></a>Plataformas cruzadas e aplicativos híbridos
Aplicativos de plataforma cruzada oferecem a você o poder de escrever aplicativos móveis nativos uma vez, compartilhar código e executá-los no iOS, no Android e no Windows.

### <a name="xamarin"></a>Xamarin
Pertencente à Microsoft, o [Xamarin](https://visualstudio.microsoft.com/xamarin/) permite que você crie aplicativos móveis robustos e C#entre plataformas no, com uma biblioteca de classes e tempo de execução que funciona em várias plataformas, incluindo Ios, Android e Windows, enquanto ainda compila nativo (não interpretado ) aplicativos com alto desempenho. O Xamarin combina todas as capacidades das plataformas nativas e adiciona vários recursos avançados.

- **Linguagem de programação** -C#
- **IDE** -Visual Studio no Windows ou Mac

### <a name="react-native"></a>React Native
Lançado pelo Facebook em 2015, a [reagem nativa](https://facebook.github.io/react-native/) [é uma estrutura](https://github.com/facebook/react-native) JavaScript de software livre para escrever aplicativos móveis reais e de processamento nativo para IOS e Android. Ele é baseado em reagir, a biblioteca JavaScript do Facebook para a criação de interfaces do usuário, mas em vez de direcionar o navegador, ele visa plataformas móveis. Reagir nativos usa componentes nativos em vez de componentes da Web como blocos de construção.
 
- **Linguagem de programação** -JavaScript
- Visual Studio Code **IDE**

### <a name="unity"></a>Unity
 O Unity é um mecanismo otimizado para a criação de jogos que permite aos desenvolvedores criar aplicativos 2D/3D C# de alta qualidade com plataformas diferentes, incluindo Windows, Ios, Android e Xbox.

### <a name="cordova"></a>Cordova
O Cordova permite criar aplicativos híbridos usando Ferramentas do Visual Studio para Apache Codova ou Visual Studio Code com extensões para Cordova. Com a abordagem híbrida, você pode compartilhar componentes com sites e reutilizar aplicativos baseados em servidor Web com abordagens de aplicativos Web hospedados com base no Cordova.

#### <a name="pros"></a>Prós
- Maior usabilidade de código com a criação de uma codebase para várias plataformas
- Atenda a um público mais amplo em várias plataformas
- Redução drástica no tempo de desenvolvimento
- Fácil de iniciar e atualizar

#### <a name="cons"></a>Contras
- Desempenho inferior
- Falta de flexibilidade
- Cada plataforma tem um conjunto exclusivo de recursos e funcionalidade para tornar o aplicativo nativo mais criativo
- Tempo de design de interface do usuário aumentado
- Limitação de ferramenta
