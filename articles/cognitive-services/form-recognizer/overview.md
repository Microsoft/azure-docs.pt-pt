---
title: O que é o Reconhecedor de Formato?
titleSuffix: Azure Cognitive Services
description: Aprenda a usar o reconhecedor de formulário para analisar dados de formulário e de tabela.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: a17e47fb059c23ab2e6eb69f3cfe6f2f8d0e51b2
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502909"
---
# <a name="what-is-form-recognizer"></a>O que é o Reconhecedor de Formato?

Reconhecedor de formulário do Azure é um serviço cognitivo que utiliza a tecnologia de aprendizagem automática para identificar e extrair dados de tabela e de pares chave/valor dos documentos de formulário. Em seguida, gera a saída de dados estruturados, que inclui as relações no ficheiro original. Pode chamar o seu modelo de formulário reconhecedor personalizado usando uma API REST simples para reduzir a complexidade e integrá-lo facilmente no seu fluxo de trabalho ou aplicação. Para começar, basta cinco documentos de preenchidos formulário ou de dois formulários preenchidos além de um formulário vazio do mesmo tipo como seu material de entrada. Obter resultados precisos adaptadas ao seu conteúdo específico sem muita intervenção manual ou conhecimentos de ciência de dados extensa rapidamente.

## <a name="custom-models"></a>Modelos personalizados

O modelo personalizado do reconhecedor de formulário prepara-se aos seus próprios dados e tem apenas cinco formulários de entrada de exemplo para começar. Quando envia os dados de entrada, o algoritmo clusters os formulários por tipo, Deteta que as chaves e tabelas estão presentes e associa os valores para chaves e as entradas a tabelas. Em seguida, gera a saída de dados estruturados, que inclui as relações no ficheiro original. Depois de preparar o modelo, pode testar e voltar a prepará-lo e, eventualmente, utilizá-lo confiável extrair dados de formulários mais consoante as suas necessidades.

A aprendizagem não supervisionada permite ao modelo compreender o esquema e as relações entre os campos e as entradas sem etiquetagem de dados manual ou codificação e manutenção intensivas. Por outro lado, modelos de aprendizagem automática com formação prévia exigem dados padronizados. Eles são menos precisos com material de entrada que desvia do formatos tradicionais, como formulários específicos da indústria.

## <a name="pre-built-receipt-model"></a>Modelo de recebimento pré-criadas

Formulário reconhecedor também inclui um modelo para a leitura de recibos de vendas. Esse modelo extrai informações da chave, como a data e hora da transação, informações comercial, quantidades de impostos e totais e muito mais. Além disso, é preparado o modelo de recibos criados previamente para reconhecer e retornar todo o texto uma após a receção.

## <a name="what-it-includes"></a>O que inclui

Formulário reconhecedor está disponível como uma API REST. Pode criar, dar formação e e classificar um modelo personalizado ou aceder ao modelo de previamente criado ao invocar a essas APIs. Se quiser, pode preparar e executar modelos personalizados num contentor do Docker local.

## <a name="input-requirements-custom-model"></a>Requisitos de entrada (modelo personalizado)

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="request-access"></a>Pedir acesso

Formulário reconhecedor está disponível uma pré-visualização de acesso limitado. Para obter acesso à pré-visualização, preencha e envie os [pedido de acesso do reconhecedor de formulário](https://aka.ms/FormRecognizerRequestAccess) formulário. O formulário solicita as informações sobre si, sua empresa e o cenário de utilizador para o qual usará o reconhecedor de formulário. Se o seu pedido é aprovado pela equipe de serviços cognitivos do Azure, receberá um e-mail com instruções para acessar o serviço.

## <a name="where-do-i-start"></a>Por onde devo começar?

**Passo 1:** Crie um recurso do reconhecedor de formulário no portal do Azure.

**Passo 2:** Seguir um guia de introdução para utilizar a API REST:
* [Quickstart: Preparar um modelo de formulário reconhecedor e extrair dados de formulário com a API de REST com cURL](quickstarts/curl-train-extract.md)
* [Quickstart: Preparar um modelo de formulário reconhecedor e extrair dados de formulário com a API de REST com Python](quickstarts/python-train-extract.md)
* [Quickstart: Extrair dados de recebimento com o cURL](quickstarts/curl-receipts.md)
* [Quickstart: Extrair dados de recebimento com Python](quickstarts/python-receipts.md)

Recomendamos que utilize o serviço gratuito, quando está aprendendo a tecnologia. Tenha em atenção que o número de páginas livres é limitado a 500 por mês.

**Passo 3:** Reveja as APIs REST

Utilize as seguintes APIs para treinar e extrair dados estruturados de formulários.

|||
|---|---|
| Preparar Modelo| Prepare um modelo de novo para analisar seus formulários usando os cinco formulários do mesmo tipo. Em alternativa, preparar com um formulário vazio e dois formulários preenchidos.  |
| Analisar o formulário |Analise um único documento passado como um fluxo para extrair os pares chave/valor e tabelas do formulário com o seu modelo personalizado.  |
| Analisar de receção |Analise um documento único recibo para extrair informações da chave e outro texto de receção.|

Explore os [documentação de referência da REST API](https://aka.ms/form-recognizer/api) para saber mais. 

## <a name="data-privacy-and-security"></a>Segurança e privacidade dos dados

Este serviço é disponibilizado como um [pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de um serviço do Azure sob o [termos de serviço Online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Tal como acontece com todos os serviços cognitivos, os desenvolvedores que usam o serviço do reconhecedor de formulário devem de estar ciente das políticas de Microsoft em dados do cliente. Consulte a [página dos serviços cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center para obter mais informações.

## <a name="next-steps"></a>Passos Seguintes

Concluir um [início rápido](quickstarts/curl-train-extract.md) para começar a utilizar com o [APIs do reconhecedor de formulário](https://aka.ms/form-recognizer/api).
