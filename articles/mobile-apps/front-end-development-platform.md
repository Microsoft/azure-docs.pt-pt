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
ms.openlocfilehash: c55c9c36d84c8545f4192e1c9a65d8af0c580396
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453156"
---
# <a name="choose-mobile-development-frameworks"></a>Escolher estruturas de desenvolvimento móvel
Os desenvolvedores podem usar tecnologias do lado do cliente para criar aplicativos móveis usando estruturas e padrões específicos para uma abordagem de plataforma cruzada. Com base em seus fatores de decisão, os desenvolvedores podem criar:
- Aplicativos nativos de plataforma única usando linguagens como Objective C e Java
- Aplicativos de plataforma cruzada usando o Xamarin, o .NET e oC#
- Aplicativos híbridos usando o Cordova e suas variantes

## <a name="native-platforms"></a>Plataformas nativas
A criação de um aplicativo nativo requer linguagens de programação específicas da plataforma, SDKs, ambientes de desenvolvimento e outras ferramentas fornecidas pelos fornecedores do sistema operacional.

### <a name="ios"></a>iOS
Criado e desenvolvido pela Apple, o iOS é usado para criar aplicativos em dispositivos Apple, ou seja, o iPhone e o iPad.

- **Linguagens de programação**: Objective-C, Swift
- **IDE**: Xcode
- **SDK**: SDK do IOS

### <a name="android"></a>Android
Projetado pelo Google e pelo sistema operacional mais popular do mundo, o Android é usado para criar aplicativos que podem ser executados em uma variedade de smartphones e tablets.

- **Linguagem de programação**: Java, Kotlin 
- **IDE**: ferramentas de desenvolvedor de Android Studio e Android 
- **SDK**: SDK do Android

### <a name="windows"></a>Windows
- **Linguagem de programação**:C#
- **IDE**: Visual Studio, Visual Studio Code
- **SDK**: SDK do Windows

#### <a name="pros"></a>Profissionais de TI
- Boa experiência do usuário
- Aplicativos responsivos com alto desempenho e a capacidade de interagir com bibliotecas nativas
- Aplicativos altamente seguros

#### <a name="cons"></a>Contras
- O aplicativo é executado em apenas uma plataforma
- Mais recursos para desenvolvedores com uso intensivo e dispendioso para criar um aplicativo
- Menor reutilização de código

## <a name="cross-platforms-and-hybrid-applications"></a>Plataformas cruzadas e aplicativos híbridos
Aplicativos de plataforma cruzada oferecem a você a capacidade de escrever aplicativos móveis nativos uma vez, compartilhar código e executá-los no iOS, no Android e no Windows.

### <a name="xamarin"></a>Xamarin
Pertencente à Microsoft, o [Xamarin](https://visualstudio.microsoft.com/xamarin/) é usado para criar aplicativos móveis robustos e entre C#plataformas no. O Xamarin tem uma biblioteca de classes e tempo de execução que funciona em várias plataformas, como iOS, Android e Windows. Ele também compila aplicativos nativos (não interpretados) que oferecem alto desempenho. O Xamarin combina todas as capacidades das plataformas nativas e adiciona vários recursos avançados.

- **Linguagem de programação**:C#
- **IDE**: Visual Studio no Windows ou Mac

### <a name="react-native"></a>React Native
Lançado pelo Facebook em 2015, [reagir nativo](https://facebook.github.io/react-native/) [é uma estrutura JavaScript de software](https://github.com/facebook/react-native) livre para escrever aplicativos móveis reais e de processamento nativo para IOS e Android. Ele é baseado em reagir, a biblioteca JavaScript do Facebook para a criação de interfaces do usuário. Em vez de direcionar o navegador, ele se destina a plataformas móveis. Reagir nativos usa componentes nativos em vez de componentes da Web como blocos de construção.
 
- **Linguagem de programação**: JavaScript
- **IDE**: Visual Studio Code

### <a name="unity"></a>Unity
 O Unity é um mecanismo otimizado para a criação de jogos. Você pode usá-lo para criar aplicativos 2D ou 3D de alta qualidade C# com plataformas como Windows, Ios, Android e Xbox.

### <a name="cordova"></a>Cordova
O Cordova permite criar aplicativos híbridos usando Ferramentas do Visual Studio para Apache Cordova ou Visual Studio Code com extensões para Cordova. Com a abordagem híbrida, você pode compartilhar componentes com sites e reutilizar aplicativos baseados em servidor Web com abordagens de aplicativos Web hospedados com base no Cordova.

#### <a name="pros"></a>Profissionais de TI
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
