---
title: Escolha a plataforma de desenvolvimento frontal para a construção de aplicações de clientes com serviços Visual Studio e Azure
description: Conheça as línguas nativas e transplataformas suportadas para construir aplicações de clientes.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 714916c755ce50f6e596cee4be85ceb0b142c789
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240867"
---
# <a name="choose-mobile-development-frameworks"></a>Escolha quadros de desenvolvimento móvel
Os desenvolvedores podem usar tecnologias do lado do cliente para construir aplicações móveis, usando quadros e padrões específicos para uma abordagem transversal. Com base nos seus fatores de decisão, os desenvolvedores podem construir:
- Aplicações nativas de plataforma única utilizando línguas como o Objetivo C e Java
- Aplicações cross-platform utilizando Xamarin, .NET e C #
- Aplicações híbridas utilizando a Cordova e as suas variantes

## <a name="native-platforms"></a>Plataformas nativas
A construção de uma aplicação nativa requer linguagens de programação específicas da plataforma, SDKs, ambientes de desenvolvimento e outras ferramentas fornecidas pelos fornecedores de SO.

### <a name="ios"></a>iOS
Criado e desenvolvido pela Apple, o iOS é usado para construir aplicações em dispositivos Apple, nomeadamente o iPhone e o iPad.

- **Linguagens de programação**: Objectivo-C, Swift
- **IDE**: Xcode
- **SDK**: iOS SDK

### <a name="android"></a>Android
Projetado pela Google e o SISTEMA mais popular do mundo, o Android é usado para construir aplicações que podem funcionar em uma gama de smartphones e tablets.

- **Linguagem de programação**: Java, Kotlin 
- **IDE**: Android Studio e ferramentas de desenvolvimento Android 
- **SDK**: Android SDK

### <a name="windows"></a>Windows
- **Linguagem de programação**: C #
- **IDE**: Estúdio Visual, Código de Estúdio Visual
- **SDK**: Windows SDK

#### <a name="pros"></a>Vantagens
- Boa experiência do utilizador
- Aplicações responsivas com alto desempenho e capacidade de interagir com bibliotecas nativas
- Aplicações altamente seguras

#### <a name="cons"></a>Contras
- Aplicação funciona em apenas uma plataforma
- Mais recursos de desenvolvimento intensivos e caros para construir uma aplicação
- Reutilização do código inferior

## <a name="cross-platforms-and-hybrid-applications"></a>Plataformas cruzadas e aplicações híbridas
As aplicações cross-platform dão-lhe o poder de escrever aplicações móveis nativas uma vez, partilhar código e executá-las em iOS, Android e Windows.

### <a name="xamarin"></a>Xamarin
Propriedade da Microsoft, [xamarin](https://visualstudio.microsoft.com/xamarin/) é usado para construir aplicações móveis robustas e cross-platform em C#. Xamarin tem uma biblioteca de classes e tempo de funcionamento que funciona em muitas plataformas, como iOS, Android e Windows. Também compila aplicações nativas (não interpretadas) que proporcionam alto desempenho. Xamarin combina todas as habilidades das plataformas nativas e adiciona uma série de características poderosas por si só.

- **Linguagem de programação**: C #
- **IDE**: Estúdio Visual no Windows ou Mac

### <a name="react-native"></a>Reagir Nativo
Lançado pelo Facebook em 2015, o [React Native](https://facebook.github.io/react-native/) é um quadro javaScript [de código aberto](https://github.com/facebook/react-native) para escrever aplicações móveis reais e nativas para iOS e Android. Baseia-se no React, biblioteca JavaScript do Facebook para construir interfaces de utilizador. Em vez de visar o navegador, tem como alvo plataformas móveis. Reagir Nativo usa componentes nativos em vez de componentes web como blocos de construção.
 
- **Linguagem de programação**: JavaScript
- **IDE**: Código de estúdio visual

### <a name="unity"></a>Unidade
 A unidade é um motor otimizado para criar jogos. Pode usá-lo para criar aplicações 2D ou 3D de alta qualidade com C# para plataformas como Windows, iOS, Android e Xbox.

### <a name="cordova"></a>Cordova
A Cordova permite-lhe construir aplicações híbridas utilizando ferramentas de estúdio visuais para Apache Cordova ou Visual Studio Code com extensões para cordova. Com a abordagem híbrida, pode partilhar componentes com websites e reutilizar aplicações baseadas em servidores web com abordagens de aplicações web hospedadas com base no Cordova.

#### <a name="pros"></a>Vantagens
- Maior usabilidade do código através da criação de uma base de código para várias plataformas
- Atender a um público mais vasto em muitas plataformas
- Redução dramática do tempo de desenvolvimento
- Fácil de lançar e atualizar

#### <a name="cons"></a>Contras
- Menor desempenho
- Falta de flexibilidade
- Cada plataforma tem um conjunto único de funcionalidades e funcionalidades para tornar a aplicação nativa mais criativa
- Aumento do tempo de design de UI
- Limitação da ferramenta
