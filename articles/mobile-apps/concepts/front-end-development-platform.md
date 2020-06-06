---
title: Escolha a plataforma de desenvolvimento frontal para a construção de aplicações de clientes com serviços Visual Studio e Azure
description: Conheça as línguas nativas e transversais suportadas para construir aplicações de clientes.
author: codemillmatt
ms.service: mobile-services
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 3fb8057d0619193237567b619ca93b92526a3496
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450844"
---
# <a name="choose-mobile-development-frameworks"></a>Escolha quadros de desenvolvimento móvel
Os desenvolvedores podem usar tecnologias do lado do cliente para construir aplicações móveis por si mesmos, usando quadros e padrões específicos para uma abordagem transversal. Com base nos seus fatores de decisão, os desenvolvedores podem construir:
- Aplicações de plataforma única nativas usando idiomas como Objetivo C e Java
- Aplicações cross-platform usando Xamarin, .NET e C #
- Aplicações híbridas utilizando a Cordova e as suas variantes

## <a name="native-platforms"></a>Plataformas nativas
A construção de uma aplicação nativa requer linguagens de programação específicas da plataforma, SDKs, ambientes de desenvolvimento e outras ferramentas fornecidas pelos fornecedores de SO.

### <a name="ios"></a>iOS
Criado e desenvolvido pela Apple, o iOS é usado para construir aplicações em dispositivos apple, nomeadamente o iPhone e o iPad.

- **Linguagens de programação**: Objectivo-C, Swift
- **IDE**: Xcode
- **SDK**: iOS SDK

### <a name="android"></a>Android
Projetado pela Google e pelo sistema operativo mais popular do mundo, o Android é utilizado para construir aplicações que podem funcionar numa série de smartphones e tablets.

- **Linguagem de programação**: Java, Kotlin 
- **IDE**: Ferramentas de desenvolvimento Android Studio e Android 
- **SDK**: Android SDK

### <a name="windows"></a>Windows
- **Linguagem de programação**: C #
- **IDE**: Estúdio Visual, Código do Estúdio Visual
- **SDK**: Windows SDK

#### <a name="pros"></a>Vantagens
- Boa experiência de utilizador
- Aplicações responsivas com alto desempenho e capacidade de interface com bibliotecas nativas
- Aplicações altamente seguras

#### <a name="cons"></a>Contras
- Aplicação funciona em apenas uma plataforma
- Mais recursos de desenvolvimento intensivos e caros para construir uma aplicação
- Reutilização de código inferior

## <a name="cross-platforms-and-hybrid-applications"></a>Plataformas cruzadas e aplicações híbridas
As aplicações multi-plataformas dão-lhe o poder de escrever aplicações móveis nativas uma vez, partilhar código e executá-las em iOS, Android e Windows.

### <a name="xamarin"></a>Xamarin
Propriedade da Microsoft, [a Xamarin](https://visualstudio.microsoft.com/xamarin/) é usada para construir aplicações móveis robustas e transversais em C#. Xamarin tem uma biblioteca de classes e tempo de execução que funciona em muitas plataformas, como iOS, Android e Windows. Também compila aplicações nativas (não interpretadas) que proporcionam um alto desempenho. Xamarin combina todas as habilidades das plataformas nativas e adiciona uma série de funcionalidades poderosas por si só.

- **Linguagem de programação**: C #
- **IDE**: Estúdio Visual no Windows ou Mac

### <a name="react-native"></a>Reagir Nativo
Lançado pelo Facebook em 2015, [o React Native](https://facebook.github.io/react-native/) é um quadro JavaScript de código [aberto](https://github.com/facebook/react-native) para escrever aplicações móveis reais e renderizadas de forma nativa para iOS e Android. Baseia-se no React, a biblioteca JavaScript do Facebook para a construção de interfaces de utilizador. Em vez de direcionar o navegador, tem como alvo plataformas móveis. Reagir Nativos usa componentes nativos em vez de componentes web como blocos de construção.
 
- **Linguagem de programação**: JavaScript
- **IDE**: Código do Estúdio Visual

### <a name="unity"></a>Unidade
 A unidade é um motor otimizado para a criação de jogos. Pode usá-lo para criar aplicações 2D ou 3D de alta qualidade com C# para plataformas como Windows, iOS, Android e Xbox.

### <a name="cordova"></a>Cordova
A Cordova permite-lhe construir aplicações híbridas utilizando ferramentas de estúdio visual para Apache Cordova ou Código de Estúdio Visual com extensões para Cordova. Com a abordagem híbrida, pode partilhar componentes com websites e reutilizar aplicações baseadas em servidores web com abordagens de aplicações web hospedadas com base na Cordova.

#### <a name="pros"></a>Vantagens
- Maior usabilidade do código criando uma base de código para várias plataformas
- Atender a um público mais vasto em muitas plataformas
- Redução dramática do tempo de desenvolvimento
- Fácil de lançar e atualizar

#### <a name="cons"></a>Contras
- Menor desempenho
- Falta de flexibilidade
- Cada plataforma tem um conjunto único de funcionalidades e funcionalidades para tornar a aplicação nativa mais criativa
- Aumento do tempo de design da UI
- Limitação da ferramenta
