---
title: O que há de novo - Compreensão da Linguagem (LUIS)
description: Este artigo é regularmente atualizado com notícias sobre a API de Compreensão da Linguagem dos Serviços Cognitivos Do Azure.
ms.topic: overview
ms.date: 05/08/2020
ms.openlocfilehash: d7ef989d04132dedc79d2408b7083b57d8c926b0
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592802"
---
# <a name="whats-new-in-language-understanding"></a>O que há de novo na compreensão da linguagem

Saiba o que há de novo no serviço. Estes itens incluem notas de lançamento, vídeos, posts de blog e outros tipos de informação. Marque esta página para manter-se atualizado com o serviço.

## <a name="release-notes"></a>Notas de versão

### <a name="may-2020---build"></a>Maio 2020 - //Construção

* Lançado como **geralmente disponível** (GA):
    * [Recipiente de compreensão da linguagem](luis-container-howto.md)
    * Portal de pré-visualização promovido ao [portal atual](https://www.luis.ai), portal [anterior](https://previous.luis.ai) ainda disponível
    * Nova experiência de criação e rotulagem de entidades aprendidas com máquinas
    * Processo de [atualização](migrate-from-composite-entity.md) de entidades compostas e simples para entidades aprendidas por máquinas
    * [Definição de](how-to-application-settings-portal.md) suporte para normalizar variantes de palavras
* Pré-visualização Autorde alterações da API
    * App schema 7.x para entidades aninhadas aprendizagem de máquinas
    * [Migração para recurso necessário](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)


### <a name="march-2020"></a>Março de 2020

* TLS 1.2 é agora aplicado para todos os pedidos http para este serviço. Para mais informações, consulte a [segurança dos Serviços Cognitivos Azure.](../cognitive-services-security.md)

### <a name="november-4-2019---ignite"></a>4 de novembro de 2019 - Ignição

* Vídeo - [Modelos avançados de Compreensão da Linguagem Natural (NLU) utilizando os Serviços Cognitivos LUIS e Azure [ BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Melhoria da produtividade do desenvolvedor
    * Disponibilidade geral do nosso [ponto final de previsão V3](luis-migration-api-v3.md).
    * Capacidade de importar e exportar aplicações com `.lu` formato[LUDown.](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown) Isto abre caminho para um processo eficaz de CI/CD.
* Expansão linguística
    * [Árabe e Hindi](luis-language-support.md) em pré-visualização pública.
* Modelos pré-construídos
    * [Os domínios pré-construídos](luis-reference-prebuilt-domains.md) estão agora geralmente disponíveis (GA)
    * As [entidades pré-construídas japonesas](luis-reference-prebuilt-entities.md#japanese-entity-support) - idade, moeda, número e percentagem não são suportadas em V3.
    * Entidades [pré-construídas italianas](luis-reference-prebuilt-entities.md#italian-entity-support) - idade, moeda, dimensão, número e resolução percentual alterada seleção de V2.
* Experiência melhorada do utilizador em [preview.luis.ai portal](https://preview.luis.ai) - experiência de rotulagem renovada para permitir a construção e depuração de modelos complexos. Experimente os tutoriais do portal de pré-visualização:
    * [Apenas intenções](tutorial-intents-only.md)
    * [Entidade descompizável de máquinas](tutorial-machine-learned-entity.md)
* Capacidades avançadas de compreensão da linguagem - [construindo modelos linguísticos sofisticados](luis-concept-entity-types.md) com menos esforço.
* Defina funcionalidades de machine learning ao nível do modelo e permita que os modelos sejam usados como sinais para outros modelos, por exemplo, utilizando entidades como funcionalidades de intenções e outras entidades.
* Novos [limites](luis-limits.md) expandidos - máximo máximo para listas de frases e frases totais, novo modelo como limites de características
* Extrair informação do texto no formato da estrutura da hierarquia profunda, tornando as aplicações de conversação mais poderosas.

    ![imagem de entidade aprendida por máquinas](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3 de setembro de 2019

* Recurso de autoria Azure - [migrar agora](luis-migration-authoring.md).
    * 500 apps por recurso Azure
    * 100 versões por app
* Apoio turco a entidades pré-construídas
* Apoio italiano para dataV2

### <a name="july-23-2019"></a>23 de julho de 2019

* Atualizar o [Texto Reconhecedores-Texto](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) para 1.2.3
    * Reconhecer idade, temperatura, dimensão e moeda em italiano.
    * Melhoria do reconhecimento de férias em inglês para calcular corretamente as datas baseadas no Dia de Ação de Graças.
    * Melhorias na Data De Data Francesa Para reduzir falsos positivos de entidades não datadas e não-time.
    * Apoio ao calendário/escola/ano fiscal e siglas no English DateRange.
    * Melhor reconhecimento de Número de Telefone em chinês e japonês.
    * Melhor suporte para NumberRange em inglês.
    * Melhorias de desempenho.

### <a name="june-24-2019"></a>24 de junho de 2019

* [Entidade pré-construída OrdinalV2](luis-reference-prebuilt-ordinal-v2.md) para apoiar encomendas como a próxima, anterior e última. Só a cultura inglesa.

### <a name="may-6-2019---build-conference"></a>6 de maio de 2019 - //Build Conference

As seguintes características foram lançadas na Conferência Build 2019:

* [Pré-visualização do guia de migração V3 API](luis-migration-api-v3.md)
* [Painel de análise melhorado](luis-how-to-use-dashboard.md)
* [Domínios pré-construídos melhorados](luis-reference-prebuilt-domains.md)
* [Entidades de lista dinâmica](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Entidades externas](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blogues

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>Vídeos

### <a name="2019-ignite-videos"></a>Vídeos ignite 2019

[Modelos avançados de Compreensão da Linguagem Natural (NLU) utilizando os Serviços Cognitivos LUIS e Azure [ BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 Construir vídeos

[Como usar a IA Conversacional Azure para escalar o seu negócio para a próxima geração](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Atualizações de serviço

[Anúncios de atualizações do Azure para serviços cognitivos](https://azure.microsoft.com/updates/?product=cognitive-services)
