---
title: O que é Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer do Azure é um serviço de API com base na cloud que permite-lhe escolher a melhor experiência para mostrar aos seus utilizadores, aprendendo com o seu comportamento em tempo real.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: c969029bcc0412267507efe81549ec6f8b2988ce
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027118"
---
# <a name="what-is-personalizer"></a>O que é Personalizer?

Personalizer do Azure é um serviço de API com base na cloud que permite-lhe escolher a melhor experiência para mostrar aos seus utilizadores, aprendendo com o seu comportamento em tempo real.

* Fornecer informações sobre os seus utilizadores e o conteúdo e receber a ação superior para mostrar os seus utilizadores. 
* Não é necessário para limpar e Etiquetar dados antes de utilizar Personalizer.
* Fornece comentários Personalizer quando é conveniente para. 
* Ver análise em tempo real. 
* Utilize Personalizer como parte de um maior esforço de ciência de dados para validar experimentações existentes.

## <a name="how-does-personalizer-work"></a>Como funciona a Personalizer?

Personalizer utiliza modelos de machine learning para descobrir qual a ação a classificação mais elevada num contexto. A aplicação de cliente fornece uma lista de ações possíveis, com informações sobre os mesmos; e informações sobre o contexto, que pode incluir informações sobre o utilizador, dispositivo, etc. Personalizer determina a ação a tomar. Assim que a aplicação cliente utiliza a ação escolhida, fornece comentários para Personalizer na forma de uma pontuação de recompensa. Após a conclusão do ciclo de comentários, Personalizer atualiza automaticamente seu próprio modelo utilizado para classificações futuras.

## <a name="how-do-i-use-the-personalizer"></a>Como faço para usar o Personalizer?

![Usando Personalizer para escolher o vídeo para mostrar a um utilizador](media/what-is-personalizer/personalizer-example-highlevel.png)

1. Escolha uma experiência na sua aplicação para personalizar.
1. Criar e configurar o serviço de personalização no portal do Azure
1. Utilizar o SDK para chamar Personalizer com informações (_funcionalidades_) sobre os seus utilizadores e o conteúdo (_ações_). Não precisa de fornecer limpo e rotulado como os dados antes de utilizar Personalizer. 
1. No aplicativo cliente, mostre o usuário a ação selecionada por Personalizer.
1. Utilize o SDK para fornecer comentários aos Personalizer que indica se o usuário selecionou ação do Personalizer. Este é um _recompensar pontuação_, normalmente entre -1 e 1.
1. Ver análise no portal do Azure para avaliar como o sistema está a funcionar e como seus dados está a ajudar personalização.

## <a name="where-can-i-use-personalizer"></a>Onde posso utilizar Personalizer?

Por exemplo, a aplicação cliente pode adicionar Personalizer para:

* Personalize o artigo é realçado num Web site de notícias.    
* Otimize a colocação do ad num Web site.
* Exiba um personalizadas recomendado "item" num site de compra.
* Sugerir elementos de interface do usuário, como filtros a aplicar a uma foto específica.
* Escolha a resposta de um chatbot para esclarecer a intenção do utilizador ou sugerir uma ação.
* Priorize sugestões do que um utilizador deve fazer como a próxima etapa num processo comercial.

## <a name="personalization-for-developers"></a>Personalização para desenvolvedores

Serviço de personalizer tem duas APIs:

* Enviar informações (_funcionalidades_) sobre os seus utilizadores e o conteúdo (_ações_) para personalizar. Personalizer responde com a ação superior.
* Enviar comentários à Personalizer sobre a eficiência com que a classificação trabalhou como um número, normalmente, entre 0 e 1 (a seção anterior disse -1 e 1). 

![Sequência básica de eventos para personalização](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>Passos Seguintes

[Quickstart: Criar um ciclo de comentários noC#](csharp-quickstart-commandline-feedback-loop.md)
