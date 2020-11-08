---
title: Opções de desenvolvimento de Serviços Cognitivos Azure
description: Saiba como utilizar os Serviços Cognitivos Azure com diferentes opções de desenvolvimento e implementação, tais como bibliotecas de clientes, APIs DE REST, Aplicações Lógicas, Automatização, Funções Azure, Serviço de Aplicações Azure, Databricks Azure, entre muitos outros.
services: cognitive-services
manager: nitinme
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 4eaa33778287bfcda45547c24e6abe0606b6baa7
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368802"
---
# <a name="cognitive-services-development-options"></a>Opções de desenvolvimento dos Serviços Cognitivos

Este documento fornece uma visão geral de alto nível das opções de desenvolvimento e implantação para ajudá-lo a começar com os Serviços Cognitivos Azure.

Os Azure Cognitive Services são serviços de IA baseados na nuvem que permitem aos desenvolvedores construir inteligência nas suas aplicações e produtos sem conhecimento profundo de machine learning. Com os Serviços Cognitivos, você tem acesso a capacidades de IA ou modelos que são construídos, treinados e atualizados pela Microsoft - prontos para serem usados nas suas aplicações. Em muitos casos, também tem a opção de personalizar os modelos para as necessidades do seu negócio. 

Os Serviços Cognitivos são organizados em quatro categorias: Decisão, Linguagem, Fala e Visão. Normalmente, você acederia a estes serviços através de REST APIs, bibliotecas de clientes e ferramentas personalizadas (como interfaces de linha de comando) fornecidas pela Microsoft. No entanto, este é apenas um caminho para o sucesso. Através do Azure, você também tem acesso a várias opções de desenvolvimento, tais como:

* Ferramentas de automação e integração como Apps Lógicas e Power Automamate.
* Opções de implementação como as Funções Azure e o Serviço de Aplicações. 
* Serviços Cognitivos Docker contentores para acesso seguro.
* Ferramentas como Apache Spark, Azure Databricks, Azure Synapse Analytics e Azure Kubernetes Service para cenários de Big Data. 

Antes de entrarmos, é importante saber que os Serviços Cognitivos são usados principalmente para duas tarefas distintas. Com base na tarefa que pretende executar, tem diferentes opções de desenvolvimento e implementação para escolher. 

* [Opções de desenvolvimento para previsão e análise](#development-options-for-prediction-and-analysis)
* [Ferramentas para personalizar e configurar modelos](#tools-to-customize-and-configure-models)

## <a name="development-options-for-prediction-and-analysis"></a>Opções de desenvolvimento para previsão e análise 

As ferramentas que irá utilizar para personalizar e configurar modelos são diferentes das que vai usar para chamar os Serviços Cognitivos. Fora da caixa, a maioria dos Serviços Cognitivos permitem-lhe enviar dados e receber insights sem qualquer personalização. Por exemplo: 

* Pode enviar uma imagem para o serviço de Visão de Computador para detetar palavras e frases ou contar o número de pessoas no quadro
* Pode enviar um ficheiro áudio para o serviço de discurso e obter transcrições e traduzir o discurso para texto ao mesmo tempo
* Você pode enviar um PDF para o serviço de Reconhecimento de Formulários e detetar tabelas, células e texto dentro dessas células, e você obtém uma saída JSON com coordenadas e detalhes

O Azure oferece uma vasta gama de ferramentas que são projetadas para diferentes tipos de utilizadores, muitas das quais podem ser usadas com Serviços Cognitivos. As ferramentas orientadas pelo designer são as mais fáceis de usar, e são rápidas a configurar e automatizar, mas podem ter limitações no que diz respeito à personalização. As nossas APIs rest e bibliotecas de clientes proporcionam aos utilizadores mais controlo e flexibilidade, mas requerem mais esforço, tempo e experiência para construir uma solução. Se utilizar APIs rest e bibliotecas de clientes, existe a expectativa de que se sinta confortável a trabalhar com linguagens de programação modernas como C#, Java, Python, JavaScript ou outra linguagem de programação popular. 

Vamos ver as diferentes formas de trabalhar com os Serviços Cognitivos.

### <a name="client-libraries-and-rest-apis"></a>Bibliotecas de cliente e APIs REST

As bibliotecas de clientes dos Serviços Cognitivos e as APIs REST proporcionam-lhe acesso direto ao seu serviço. Estas ferramentas fornecem acesso programático aos Serviços Cognitivos, seus modelos de base, e em muitos casos permitem-lhe personalizar programáticamente os seus modelos e soluções. 

* **Utilizador-alvo:** Desenvolvedores e cientistas de dados
* **Benefícios** : Proporciona a maior flexibilidade para chamar os serviços de qualquer língua e ambiente. 
* **UI** : N/A - Código apenas
* **Subscrição(s)** : Conta azul + recursos de Serviços Cognitivos

Se quiser saber mais sobre bibliotecas de clientes disponíveis e APIs REST, use a nossa [visão geral dos Serviços Cognitivos](index.yml) para escolher e servir e começar com um dos nossos quickstarts para visão, decisão, linguagem e fala.

### <a name="cognitive-services-for-big-data"></a>Serviços Cognitivos para Macrodados

Com serviços cognitivos para big data pode incorporar continuamente melhorando, modelos inteligentes diretamente em computações Apache Spark &trade; e SQL. Estas ferramentas libertam os desenvolvedores de detalhes de redes de baixo nível, para que possam concentrar-se na criação de aplicações inteligentes e distribuídas. Os Serviços Cognitivos para Big Data suportam as seguintes plataformas e conectores: Azure Databricks, Azure Synapse, Azure Kubernetes Service e Data Connectors.

* **Utilizador-alvo:** Cientistas de dados e engenheiros de dados
* **Benefícios** : Os Serviços Cognitivos Azure para Big Data permitem aos utilizadores canalizar terabytes de dados através dos Serviços Cognitivos utilizando o Apache &trade; Spark. É fácil criar aplicações inteligentes em larga escala com qualquer datastore.
* **UI** : N/A - Código apenas
* **Subscrição(s)** : Conta azul + recursos de Serviços Cognitivos

Se quiser saber mais sobre Big Data para Serviços Cognitivos, um bom lugar para começar é com a [visão geral.](./big-data/cognitive-services-for-big-data.md) Se está pronto para começar a construir, experimente as nossas amostras [de Python](./big-data/samples-python.md) ou [Scala.](./big-data/samples-scala.md)

### <a name="azure-functions-and-azure-service-web-jobs"></a>Funções Azure e Trabalhos Web de Serviço Azure

[As Azure Functions](../azure-functions/index.yml) e [a Azure App Service Web Jobs](../app-service/index.yml) fornecem serviços de integração de códigos concebidos para programadores e são construídos em [Serviços de Aplicações Azure](../app-service/index.yml). Estes produtos fornecem infraestruturas sem servidor para escrever código. Dentro desse código pode escrutinar chamadas para os nossos serviços utilizando as bibliotecas dos nossos clientes e as APIs REST. 

* **Utilizador-alvo:** Desenvolvedores e cientistas de dados
* **Benefícios** : Serviço de computação sem servidor que permite executar código desencadeado por eventos. 
* **UI** : Sim
* **Subscrição(s)** : Conta Azure + Recurso de Serviços Cognitivos + Assinatura de Funções Azure

### <a name="azure-logic-apps"></a>Azure Logic Apps 

[A Azure Logic Apps](../logic-apps/index.yml) partilha o mesmo designer de fluxo de trabalho e conectores que o Power Automamate, mas fornece mais avançados e controlo, incluindo integrações com o Visual Studio e DevOps. O Power Automamate facilita a integração com os seus recursos de serviços cognitivos através de conectores específicos de serviço que fornecem um proxy ou invólucro em torno das APIs. Estes são os mesmos conectores que os disponíveis na Power Automamate. 

* **Utilizador-alvo:s:** Desenvolvedores, integradores, profissionais de TI, DevOps
* **Benefícios** : Modelo de desenvolvimento designer-primeiro (declarativo) que proporciona opções avançadas e integração numa solução de baixo código
* **UI** : Sim
* **Subscrição(s)** : Conta Azure + Recurso de Serviços Cognitivos + Implementação de Aplicações Lógicas

### <a name="power-automate"></a>Power Automate 

Power automamate é um serviço na [Plataforma de Energia](/power-platform/) que o ajuda a criar fluxos de trabalho automatizados entre apps e serviços sem código de escrita. Oferecemos vários conectores para facilitar a interação com o seu recurso de Serviços Cognitivos numa solução Power Automamate. Power Automamate é construído em cima de Aplicações Lógicas. 

* **Utilizador-alvo:** Utilizadores empresariais (analistas) e administradores do Sharepoint
* **Benefícios** : Automatize tarefas manuais repetitivas simplesmente gravando cliques de rato, teclas e passos de pasta de cópia do seu ambiente de trabalho!
* **Ferramentas de UI** : Sim - Apenas UI
* **Subscrição(s)** : Conta Azure + Recurso de Serviços Cognitivos + Assinatura de Automação de Energia + Assinatura Office 365

### <a name="ai-builder"></a>AI Builder 

[O AI Builder](/ai-builder/overview) é uma capacidade da Microsoft Power Platform que pode utilizar para melhorar o desempenho do negócio, automatizando processos e prevendo resultados. O construtor de IA traz o poder da IA às suas soluções através de uma experiência de ponto e clique. Muitos serviços cognitivos como o Form Recogniser, Text Analytics e Computer Vision foram integrados diretamente aqui e você não precisa criar os seus próprios Serviços Cognitivos. 

* **Utilizador-alvo:** Utilizadores empresariais (analistas) e administradores do Sharepoint
* **Benefícios** : Uma solução chave na mão que traz o poder da IA através de uma experiência de ponto e clique. Não são necessárias competências de codificação ou de ciência de dados.
* **Ferramentas de UI** : Sim - Apenas UI
* **Subscrição(s)** : AI Builder

### <a name="continuous-integration-and-deployment"></a>Integração e implementação contínua

Pode utilizar as ações do Azure DevOps e do GitHub para gerir as suas implementações. Na [secção abaixo](#continuous-integration-and-delivery-with-devops-and-github-actions) que se discute, temos dois exemplos de integrações de CI/CD para formar e implementar modelos personalizados para o serviço Desemação e Compreensão da Língua (LUIS). 

* **Utilizador(s)-alvo:** Desenvolvedores, cientistas de dados e engenheiros de dados
* **Benefícios** : Permite-lhe ajustar, atualizar e implementar continuamente aplicações e modelos programáticamente. Existe um benefício significativo quando se utiliza regularmente os seus dados para melhorar e atualizar modelos para a Fala, Visão, Linguagem e Decisão. 
* **Ferramentas UI** : N/A - Código apenas 
* **Subscrição(s)** : Conta Azure + Recurso de Serviços Cognitivos + conta GitHub

## <a name="tools-to-customize-and-configure-models"></a>Ferramentas para personalizar e configurar modelos

À medida que progride na sua jornada construindo uma aplicação ou fluxo de trabalho com os Serviços Cognitivos, poderá descobrir que precisa de personalizar o modelo para alcançar o desempenho pretendido. Muitos dos nossos serviços permitem-lhe construir em cima dos modelos pré-construídos para atender às suas necessidades específicas de negócio. Para todos os nossos serviços personalizáveis, fornecemos uma experiência orientada para a UI para caminhar através do processo, bem como APIs para formação orientada por códigos. Por exemplo:

* Você quer treinar um modelo de Discurso Personalizado para reconhecer corretamente termos médicos com uma taxa de erro de palavra (WER) abaixo de 3%
* Você quer construir um classificador de imagem com Visão Personalizada que pode distinguir entre árvores coníferas e caducas
* Você quer construir uma voz neural personalizada com os seus dados de voz pessoal para uma melhor experiência automatizada do cliente

As ferramentas que vai usar para treinar e configurar modelos são diferentes das que vai usar para chamar os Serviços Cognitivos. Em muitos casos, os Serviços Cognitivos que suportam a personalização fornecem portais e ferramentas de UI projetadas para ajudá-lo a treinar, avaliar e implementar modelos. Vamos rapidamente dar uma olhada em algumas opções:<br><br>

| Pilar | Serviço | UI de personalização | Início Rápido |
|--------|---------|------------------|------------|
| Visão | Visão Personalizada | https://www.customvision.ai/ | [Início rápido](./custom-vision-service/quickstarts/image-classification.md?pivots=programming-language-csharp) | 
| Visão | Reconhecedor de Formato | Ferramenta de rotulagem de amostra | [Início rápido](./form-recognizer/quickstarts/label-tool.md?tabs=v2-0) |
| Decisão | Content Moderator | https://contentmoderator.cognitive.microsoft.com/dashboard | [Início rápido](./content-moderator/review-tool-user-guide/human-in-the-loop.md) |
| Decisão | Assistente de Métricas | https://metricsadvisor.azurewebsites.net/  | [Início rápido](./metrics-advisor/quickstarts/web-portal.md) |
| Decisão | Personalizador | A UI está disponível no portal Azure sob o seu recurso Personalizer. | [Início rápido](./personalizer/quickstart-personalizer-sdk.md) |
| Linguagem | Compreensão de Idiomas (LUIS) | https://www.luis.ai/ | |
| Linguagem | Criador de FAQ | https://www.qnamaker.ai/ | [Início rápido](./qnamaker/quickstarts/create-publish-knowledge-base.md) |
| Linguagem | Tradutor/Tradutor Personalizado | https://portal.customtranslator.azure.ai/ | [Início rápido](./translator/custom-translator/quickstart-build-deploy-custom-model.md) |
| Voz | Comandos Personalizados | https://speech.microsoft.com/ | [Início rápido](./speech-service/custom-commands.md) |
| Voz | Discurso personalizado | https://speech.microsoft.com/ | [Início rápido](./speech-service/how-to-custom-speech.md) |
| Voz | Voz Personalizada | https://speech.microsoft.com/ | [Início rápido](./speech-service/how-to-custom-voice.md) |  

### <a name="continuous-integration-and-delivery-with-devops-and-github-actions"></a>Integração contínua e entrega com DevOps e GitHub Actions

A Compreensão da Linguagem e o serviço de Fala oferecem soluções de integração contínua e implementação contínua que são alimentadas por ações de Azure DevOps e GitHub. Estas ferramentas são utilizadas para treino automatizado, teste e gestão de lançamento de modelos personalizados. 

* [CI/CD para Voz Personalizada](./speech-service/how-to-custom-speech-continuous-integration-continuous-deployment.md)
* [CI/CD para LUIS](./luis/luis-concept-devops-automation.md)

## <a name="on-prem-containers"></a>Recipientes em pré-pré 

Muitos dos Serviços Cognitivos podem ser implantados em contentores para acesso e utilização pré-pré-m. A utilização destes recipientes confere-lhe a flexibilidade para aproximar os Serviços Cognitivos dos seus dados por razões de conformidade, segurança ou outras razões operacionais. Para obter uma lista completa dos recipientes dos Serviços Cognitivos, consulte [os recipientes on-prem para serviços cognitivos.](./cognitive-services-container-support.md)

## <a name="next-steps"></a>Passos seguintes
<!--
* Learn more about low code development options for Cognitive Services -->
* [Crie um recurso de Serviços Cognitivos e comece a construir](./cognitive-services-apis-create-account.md?tabs=multiservice%252clinux)