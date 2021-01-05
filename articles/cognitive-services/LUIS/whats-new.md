---
title: O que é novo - Compreensão linguística (LUIS)
description: Este artigo é atualizado regularmente com notícias sobre a Azure Cognitive Services Language Understanding API.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 06/15/2020
ms.openlocfilehash: 52f8aad0a429d3378d46265fab719b1cb9f07fd0
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97813195"
---
# <a name="whats-new-in-language-understanding"></a>Quais as novidades na compreensão da linguagem

Saiba o que há de novo no serviço. Estes itens incluem notas de lançamento, vídeos, posts de blog e outros tipos de informação. Marque esta página para manter-se atualizado com o serviço.

## <a name="release-notes"></a>Notas de versão

### <a name="december-2020"></a>Dezembro de 2020

* Todos os utilizadores de LUIS são obrigados a [migrar para um recurso de autoria LUIS](luis-migration-authoring.md)

### <a name="june-2020"></a>Junho de 2020

* [Pré-visualização 3.0 Autoria](luis-migration-authoring-entities.md) SDK -
    * Versão 3.2.0-pré-visualização.3 - [.NET - NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)
    * Versão 4.0.0-pré-visualização.3 - [JS - NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)
* Aplicação de práticas de DevOps com LUIS
    * Conceitos
        * [Práticas de DevOps para LUIS](luis-concept-devops-sourcecontrol.md)
        * [Integração Contínua e Fluxos de Trabalho de Entrega Contínua para DEVOps LUIS](luis-concept-devops-automation.md)
        * [Teste para LUIS DevOps](luis-concept-devops-testing.md)
    * Procedimentos
        * [Aplicar DevOps ao desenvolvimento de aplicativos LUIS usando ações do GitHub](luis-how-to-devops-with-github.md)
    * [Código completo GitHub repo](https://github.com/Azure-Samples/LUIS-DevOps-Template)

### <a name="may-2020---build"></a>Maio 2020 - //Construir

* Lançado como **geralmente disponível** (GA):
    * [Recipiente de compreensão linguística](luis-container-howto.md)
    * Portal de pré-visualização promovido ao [portal atual](https://www.luis.ai), portal [anterior](https://previous.luis.ai) ainda disponível
    * Nova experiência de criação e rotulagem de novas entidades de aprendizagem automática
    * [Processo](migrate-from-composite-entity.md) de upgrade de entidades compósitas e simples para entidades de aprendizagem automática
    * [Definição](how-to-application-settings-portal.md) de suporte para normalizar variantes de palavras
* Pré-visualização de alterações da API
    * Esquema de aplicativo 7.x para entidades aninhadas de aprendizagem automática
    * [Migração para recurso necessário](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)
* Novos recursos para programadores
    * [Ferramentas de integração contínua](developer-reference-resource.md#continuous-integration-tools)
    * Workshop - aprenda as melhores práticas para [ _a Compreensão da Linguagem Natural_ (NLU) utilizando o LUIS](developer-reference-resource.md#workshops)
* [Teclas geridas pelo cliente](luis-encryption-of-data-at-rest.md) - criptografe todos os dados que utiliza no LUIS usando a sua própria chave
* [AI show](https://channel9.msdn.com/Shows/AI-Show/New-Features-in-Language-Understanding) (vídeo) - veja as novas funcionalidades no LUIS



### <a name="march-2020"></a>Março de 2020

* O TLS 1.2 passa a ser aplicado para todos os pedidos HTTP a este serviço. Para mais informações, consulte [a segurança dos Serviços Cognitivos Azure.](../cognitive-services-security.md)

### <a name="november-4-2019---ignite"></a>4 de novembro de 2019 - Ignite

* Vídeo - [Modelos avançados de compreensão da linguagem natural (NLU) utilizando os Serviços Cognitivos LUIS e Azure ! BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Melhoria da produtividade dos desenvolvedores
    * Disponibilidade geral do nosso [ponto final de previsão V3](luis-migration-api-v3.md).
    * Capacidade de importar e exportar aplicativos com `.lu` formato[LUDown.](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown) Isto abre caminho para um processo eficaz de CI/CD.
* Expansão linguística
    * [Árabe e hindi](luis-language-support.md) em pré-visualização pública.
* Modelos pré-construídos
    * [Os domínios pré-construídos](luis-reference-prebuilt-domains.md) estão agora geralmente disponíveis (GA)
    * As [entidades pré-construídas japonesas](luis-reference-prebuilt-entities.md#japanese-entity-support) - idade, moeda, número e percentagem não são apoiadas em V3.
    * Entidades [pré-construídas italianas](luis-reference-prebuilt-entities.md#italian-entity-support) - idade, moeda, dimensão, número e resolução percentual alteradas de V2.
* Melhor experiência de utilizador no [portal preview.luis.ai](https://preview.luis.ai) - experiência de rotulagem renovada para permitir a construção e depuração de modelos complexos. Experimente os tutoriais do portal de pré-visualização:
    * [Apenas intençãos](tutorial-intents-only.md)
    * [Entidade de aprendizagem automática decompor-se](tutorial-machine-learned-entity.md)
* Capacidades de compreensão de linguagem avançadas - [construção de modelos linguísticos sofisticados](luis-concept-entity-types.md) com menos esforço.
* Defina funcionalidades de machine learning ao nível do modelo e permita que os modelos sejam usados como sinais para outros modelos, por exemplo, utilizando as entidades como funcionalidades para as intenções e para outras entidades.
* Novos [limites alargados](luis-limits.md) - máximo mais elevado para listas de frases e frases totais, novo modelo como limites de recurso
* Extrair informação do texto no formato de estrutura de hierarquia profunda, tornando as aplicações de conversação mais poderosas.

    ![imagem de entidade de aprendizagem automática](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3 de setembro de 2019

* Recurso de autoria Azure - [migrar agora](luis-migration-authoring.md).
    * 500 aplicações por recurso Azure
    * 100 versões por app
* Apoio turco a entidades pré-construídas
* Apoio italiano para datav2

### <a name="july-23-2019"></a>23 de julho de 2019

* Atualizar o [Texto dos Reconhecedores](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) para 1.2.3
    * Idade, Temperatura, Dimensão e Moeda reconhecem em italiano.
    * Melhoria no reconhecimento de férias em inglês para calcular corretamente as datas baseadas no Dia de Ação de Graças.
    * Melhorias no Tempo de Data Francês para reduzir falsos positivos de entidades não datas e não-tempo.
    * Apoio ao calendário/escola/ano fiscal e siglas em Inglês DateRange.
    * Reconhecimento de TelefoneNumber melhorado em chinês e japonês.
    * Melhor suporte para NumberRange em inglês.
    * Melhorias de desempenho.

### <a name="june-24-2019"></a>24 de junho de 2019

* [Entidade pré-construída OrdinalV2](luis-reference-prebuilt-ordinal-v2.md) para apoiar encomendas como a próxima, anterior e última. Só a cultura inglesa.

### <a name="may-6-2019---build-conference"></a>6 de maio de 2019 - //Build Conference

As seguintes funcionalidades foram lançadas na Conferência Build 2019:

* [Pré-visualização do guia de migração da API V3](luis-migration-api-v3.md)
* [Painel de análise melhorado](luis-how-to-use-dashboard.md)
* [Domínios pré-construídos melhorados](luis-reference-prebuilt-domains.md)
* [Entidades de lista dinâmica](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Entidades externas](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blogues

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>Vídeos

### <a name="2019-ignite-videos"></a>Vídeos Ignite 2019

[Modelos avançados de compreensão da linguagem natural (NLU) utilizando os serviços cognitivos LUIS e Azure BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 Construir vídeos

[Como usar a Azure Conversational IA para escalar o seu negócio para a próxima geração](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Atualizações de serviço

[Anúncios de atualização da Azure para Serviços Cognitivos](https://azure.microsoft.com/updates/?product=cognitive-services)
