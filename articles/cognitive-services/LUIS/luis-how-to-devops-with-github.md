---
title: Como aplicar DevOps com GitHub - LUIS
titleSuffix: Azure Cognitive Services
description: Aplicar DevOps com Compreensão linguística (LUIS) e GitHub.
services: cognitive-services
author: andycw
manager: cmayo
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.author: anwigley
ms.openlocfilehash: 6df65040277ac61cca5fb4bf7fce5b5a7b2f3afe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84783763"
---
# <a name="apply-devops-to-luis-app-development-using-github-actions"></a>Aplicar DevOps ao desenvolvimento de aplicativos LUIS usando ações do GitHub

Vá ao [repo de modelo LUIS DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template) para uma solução completa que implemente DevOps e as melhores práticas de engenharia de software para o LUIS. Você pode usar este repo de modelo para criar o seu próprio repositório com suporte incorporado para fluxos de trabalho CI/CD e práticas que permitem o controlo de [fontes, construções automatizadas,](luis-concept-devops-automation.md) [testes](luis-concept-devops-testing.md)e [gestão de lançamento](luis-concept-devops-automation.md#release-management) com LUIS para o seu próprio projeto. [source control](luis-concept-devops-sourcecontrol.md)

## <a name="the-luis-devops-template-repo"></a>O repo de modelo LUIS DevOps

O [repo do modelo LUIS DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template) percorre como:

* **Clone o repo do modelo** - Copie o modelo para o seu próprio repositório GitHub.
* **Configure recursos LUIS** - Criar os [recursos de autoria e previsão luis em Azure](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-azure-subscription#create-resources-in-azure-cli) que serão utilizados pelos fluxos de trabalho de integração contínua.
* **Configure os fluxos de trabalho CI/CD** - Configurar parâmetros para os fluxos de trabalho CI/CD e armazená-los em [GitHub Secrets](https://help.github.com/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets).
* **Anda pelo ["dev inner loop"](https://mitchdenny.com/the-inner-loop/) ** - O desenvolvedor faz atualizações para uma aplicação LUIS de amostra enquanto trabalha num ramo de desenvolvimento, testa as atualizações e, em seguida, levanta um pedido de pull para propor alterações e para pedir aprovação de revisão.
* **Executar fluxos de trabalho CI/CD** - Execute [fluxos de trabalho de integração contínua para construir e testar uma aplicação LUIS](luis-concept-devops-automation.md) usando As ações do GitHub.
* **Realizar testes automatizados** - Realizar [testes automatizados de lote para uma aplicação LUIS](luis-concept-devops-testing.md) para avaliar a qualidade da app.
* **Implementar a aplicação LUIS** - Execute um [trabalho de entrega contínua (CD)](luis-concept-devops-automation.md#continuous-delivery-cd) para publicar a aplicação LUIS.
* **Use o repo com o seu próprio projeto** - Explica como usar o repo com a sua própria aplicação LUIS.

## <a name="next-steps"></a>Passos seguintes

* Use o [repo de modelo LUIS DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template) para aplicar DevOps com o seu próprio projeto.
* [Estratégias de controlo de fontes e de sucursais para o LUIS](luis-concept-devops-sourcecontrol.md)
* [Teste para LUIS DevOps](luis-concept-devops-testing.md)
* [Fluxos de trabalho de automatização para LUIS DevOps](luis-concept-devops-automation.md)
