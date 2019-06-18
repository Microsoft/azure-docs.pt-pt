---
title: O que é o Conversation Learner? -Serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: Saiba mais sobre o aprendiz de conversação e como ela funciona.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: f8bc7590f2d7a622b4b1ffb21bfeccef89691fd5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66389505"
---
# <a name="what-is-conversation-learner"></a>O que é o Conversation Learner?

Aprendiz de conversação permite-lhe criar e ensinar interfaces conversacionais que aprender com as interações de exemplo. 

Diferentemente das abordagens tradicionais, o aprendiz de conversação considera o contexto de ponto-a-ponto de uma caixa de diálogo para melhorar as respostas e proporcionar experiências de usuário mais atraentes. Casos de utilização que abrange um vasto leque de orientados a tarefas, Aprendiz de conversação aplica-se em aprendizagem automática em segundo plano para tornar os bots e agentes inteligentes menos prováveis que frustre os usuários, incorrer em custos de serviço de cliente adicionais e spur interações mais intuitivas.

Os desenvolvedores comece por introduzir prototípica caixas de diálogo que deseja imitar. O modelo aprende como caixas de diálogo mais são introduzidas. O modelo está a funcionar bem, o Bot pode ser implementado para os utilizadores finais. Aprendiz de conversação registos conversas com os utilizadores e o desenvolvedor pode analisá-los. Se são identificou erros, o desenvolvedor pode fazer com que uma correção no lugar e o modelo é reestruturar e disponível para uso imediatamente.

Esta abordagem reduz a codificação manual de lógica de controle de caixa de diálogo e permite que os proprietários de empresas ou especialistas de domínio contribuir para uma interface conversacional sem conhecimento prévia de aprendizagem. Seja ela implantada como parte de um bot, o dispositivo inteligente ou o agente inteligente, o aprendiz de conversação pode iterar rapidamente novas competências, comportamentos ou competências e aumentar rapidamente a sua qualidade. 

Aprendiz de conversação capacita os desenvolvedores para aumentar a velocidade de comercialização e unidade dialogues com êxito em vários canais conversacionais através do Microsoft Bot Framework ou de forma autónoma utilizando a sua própria infraestrutura.

Resumo e os destaques:

- Aprendiz de conversação é uma forma de IA em primeiro lugar da criação de bots e orientados a tarefas.

- Ele se baseia numa rede neural recorrente (LSTM) do ponto-a-ponto e aprende diretamente a partir de exemplos de ativar multi de conversas. 

- Permite que designers, os desenvolvedores, os utilizadores empresariais e trabalhadores de call center criar e manter bots. 

- Fornece a capacidade para expressar as regras de negócios e de senso comum no código.

- Durante a ensinar sessões, o modelo de rede neural é utilizado para pontuar o próximo conjunto de ações esperados na conversação. O desenvolvedor de bot pode, em seguida, selecione a ação correta e treinar a rede para fornecer a resposta adequada.
 
- Após a conclusão do treinamento, o programador pode utilizar as caixas de diálogo de registo das interações de utilizador para efetuar as correções para as respostas de bot e voltar a preparar o modelo. 

- Pode chamar APIs específicas de domínio e de terceiros para concluir tarefas.

