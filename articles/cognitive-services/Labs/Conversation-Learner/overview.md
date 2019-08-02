---
title: O que é o Conversation Learner? -Serviços cognitivas da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba mais sobre Conversation Learner e como ele funciona.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 725a1d3628fb320a58e073fe2d825af23b02c0bd
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707008"
---
# <a name="what-is-conversation-learner"></a>O que é o Conversation Learner?

Conversation Learner permite que você crie e ensine interfaces de conversação que aprendam com as interações de exemplo. 

Ao contrário das abordagens tradicionais, Conversation Learner considera o contexto de ponta a ponta de uma caixa de diálogo para melhorar as respostas e fornecer experiências de usuário mais atraentes. Abrangendo um amplo conjunto de casos de uso orientado a tarefas, Conversation Learner aplica o aprendizado de máquina nos bastidores para tornar os bots e agentes inteligentes menos propensos a frustrar os usuários, incorrer em custos adicionais de atendimento ao cliente e estimular interações mais intuitivas.

Os desenvolvedores começam a inserir caixas de diálogo de protótipos que desejam imitar. O modelo aprende conforme mais caixas de diálogo são inseridas. Depois que o modelo estiver funcionando bem, o bot poderá ser implantado para os usuários finais. Conversation Learner registra em log as conversas com os usuários e o desenvolvedor pode examiná-las. Se os erros forem extremos, o desenvolvedor poderá fazer uma correção no local e o modelo será retreinado e estará disponível para uso imediato.

Essa abordagem reduz a codificação manual da lógica de controle de caixa de diálogo e permite que proprietários de negócios ou especialistas em domínio contribuam para uma interface de conversação sem conhecimento prévio do aprendizado de máquina. Seja implantado como parte de um bot, dispositivo inteligente ou agente inteligente, Conversation Learner pode rapidamente iterar novas habilidades, comportamentos ou competências e melhorar sua qualidade rapidamente. 

Conversation Learner capacita os desenvolvedores a aumentarem a velocidade para o mercado e gerar diálogos bem-sucedidos em vários canais de conversação por meio do Microsoft bot Framework ou autônomos usando sua própria infraestrutura.

Resumo e destaques:

- Conversation Learner é uma forma de ia-primeira de criar bots orientados a tarefas.

- Ele se baseia em uma LSTM (rede neural recorrente de ponta a ponta) e aprende diretamente de exemplos de conversas de várias desativações. 

- Permite que designers, desenvolvedores, usuários empresariais e funcionários de Call Center criem e mantenham bots. 

- Fornece a capacidade de expressar regras de negócios e senso comum no código.

- Durante as sessões de ensino, o modelo de rede neural é usado para pontuar o próximo conjunto de ações esperadas na conversa. O desenvolvedor de bot pode selecionar a ação correta e treinar a rede para fornecer a resposta correta.
 
- Após a conclusão do treinamento, o desenvolvedor pode usar as caixas de diálogo de log das interações do usuário para fazer correções em respostas de bot e treinar novamente o modelo. 

- Pode chamar APIs específicas de domínio e de terceiros para concluir tarefas.

