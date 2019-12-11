---
title: O que é o Reconhecedor de Formato?
titleSuffix: Azure Cognitive Services
description: O reconhecedor de formulário de serviços cognitivas do Azure permite que você identifique e extraia pares de chave/valor e dados de tabela de documentos de formulário.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 5b8628a8a235e89614ab87dcc2020915db459f38
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978429"
---
# <a name="what-is-form-recognizer"></a>O que é o Reconhecedor de Formato?

O reconhecedor do Azure Form é um serviço cognitiva que usa a tecnologia de aprendizado de máquina para identificar e extrair pares de chave/valor e dados de tabela de documentos de formulário. Em seguida, gera a saída de dados estruturados, que inclui as relações no ficheiro original. Você pode chamar seu modelo de reconhecedor de formulário personalizado usando uma API REST simples para reduzir a complexidade e integrá-la facilmente ao seu fluxo de trabalho ou aplicativo. Para começar, você precisa apenas de cinco documentos de formulário preenchidos ou dois formulários preenchidos, além de um formulário vazio do mesmo tipo que o material de entrada. Você obtém rapidamente resultados precisos que são adaptados ao seu conteúdo específico sem muita intervenção manual ou ampla experiência em ciência de dados.

## <a name="custom-models"></a>Modelos personalizados

O modelo personalizado do reconhecedor de formulário treina seus próprios dados e você precisa apenas de cinco formulários de entrada de exemplo para iniciar. Quando você envia seus dados de entrada, o algoritmo agrupa os formulários por tipo, descobre quais chaves e tabelas estão presentes e associa valores a chaves e entradas a tabelas. Em seguida, gera a saída de dados estruturados, que inclui as relações no ficheiro original. Depois de treinar o modelo, você pode testá-lo e retreiná-lo e, eventualmente, usá-lo para extrair dados de forma confiável de mais formas de acordo com suas necessidades.

A aprendizagem não supervisionada permite ao modelo compreender o esquema e as relações entre os campos e as entradas sem etiquetagem de dados manual ou codificação e manutenção intensivas. Por outro lado, os modelos de aprendizado de máquina pré-treinados exigem dados padronizados. Eles são menos precisos com material de entrada que se desvia de formatos tradicionais, como formulários específicos do setor.

## <a name="prebuilt-receipt-model"></a>Modelo de recebimento predefinido

O reconhecedor de formulário também inclui um modelo para ler os recibos de vendas. Esse modelo extrai informações importantes, como a hora e a data da transação, informações de comerciante, quantidades de impostos e totais e muito mais. Além disso, o modelo de recebimento predefinido é treinado para reconhecer e retornar todo o texto em um recibo.

## <a name="what-it-includes"></a>O que inclui

O reconhecedor de formulário está disponível como uma API REST. Você pode criar, treinar e pontuar um modelo personalizado ou acessar o modelo predefinido invocando essas APIs. Se desejar, você pode treinar e executar modelos personalizados em um contêiner do Docker local.

## <a name="input-requirements"></a>Requisitos de entrada
### <a name="custom-model"></a>Modelo personalizado

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>Modelo de recebimento predefinido

Os requisitos de entrada para o modelo de recebimento são um pouco diferentes.

* O formato deve ser JPEG, PNG, BMP, PDF (texto ou verificado) ou TIFF.
* O tamanho do arquivo deve ser inferior a 20 MB.
* As dimensões de imagem devem estar entre 50 x 50 pixels e 10000 x 10000 pixels. 
* As dimensões de PDF devem ter no máximo 17 x 17 polegadas, correspondentes aos tamanhos de papel ofício ou a3 e menores.
* Para PDF e TIFF, somente as primeiras 200 páginas são processadas (com uma assinatura de camada gratuita, somente as duas primeiras páginas são processadas).

## <a name="request-access"></a>Pedir acesso

O reconhecedor de formulário está disponível em uma versão prévia de acesso limitado. Para obter acesso à visualização, preencha e envie o formulário [solicitação de acesso do reconhecedor de formulário](https://aka.ms/FormRecognizerRequestAccess) . O formulário solicita informações sobre você, sua empresa e o cenário de usuário para o qual você usará o reconhecedor de formulário. Se sua solicitação for aprovada pela equipe de serviços cognitivas do Azure, você receberá um email com instruções para acessar o serviço.

## <a name="where-do-i-start"></a>Por onde devo começar?

**Etapa 1:** Crie um recurso de reconhecimento de formulário no portal do Azure.

**Etapa 2:** Siga um guia de início rápido para usar a API REST:
* [Início rápido: treinar um modelo de reconhecimento de formulário e extrair dados de formulário usando a API REST com ondulação](quickstarts/curl-train-extract.md)
* [Início rápido: treinar um modelo de reconhecimento de formulário e extrair dados de formulário usando a API REST com Python](quickstarts/python-train-extract.md)
* [Início rápido: extrair dados de recebimento usando ondulação](quickstarts/curl-receipts.md)
* [Início rápido: extrair dados de recebimento usando o Python](quickstarts/python-receipts.md)

Recomendamos que você use o serviço gratuito quando estiver aprendendo a tecnologia. Tenha em mente que o número de páginas livres é limitado a 500 por mês.

**Etapa 3:** Examinar as APIs REST

Você usa as seguintes APIs para treinar e extrair dados estruturados de formulários.

|||
|---|---|
| Preparar Modelo| Treine um novo modelo para analisar seus formulários usando cinco formas do mesmo tipo. Ou então, treine com um formulário vazio e dois formulários preenchidos.  |
| Analisar formulário |Analise um único documento passado como um fluxo para extrair pares de chave/valor e tabelas do formulário com seu modelo personalizado.  |
| Analisar recibo |Analise um único documento de recebimento para extrair informações de chave e outro texto de recebimento.|

Explore a [documentação de referência da API REST](https://aka.ms/form-recognizer/api) para saber mais. 

## <a name="data-privacy-and-security"></a>Privacidade e segurança de dados

Esse serviço é oferecido como uma [Visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de um serviço do Azure sob os [termos do serviço online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Assim como acontece com todos os serviços cognitivas, os desenvolvedores que usam o serviço de reconhecimento de formulário devem estar cientes das políticas da Microsoft nos dados do cliente. Consulte a [página serviços cognitivas](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na central de confiabilidade da Microsoft para saber mais.

## <a name="next-steps"></a>Passos seguintes

Conclua um guia de [início rápido](quickstarts/curl-train-extract.md) para começar a usar as [APIs do reconhecedor de formulário](https://aka.ms/form-recognizer/api).