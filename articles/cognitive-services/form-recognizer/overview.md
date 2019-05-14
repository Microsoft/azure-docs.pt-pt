---
title: O que é o Reconhecedor de Formato?
titleSuffix: Azure Cognitive Services
description: Aprenda a usar o reconhecedor de formulário para analisar dados de formulário e de tabela.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 04/08/2019
ms.author: pafarley
ms.openlocfilehash: 8fb382227c71fce7ebe062057adf5edfb90a1a92
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65601617"
---
# <a name="what-is-form-recognizer"></a>O que é o Reconhecedor de Formato?

O Reconhecedor de Formato do Azure é um serviço cognitivo que utiliza a tecnologia de aprendizagem automática para identificar e extrair pares chave-valor e dados de tabela de documentos de formulário. Em seguida, gera a saída de dados estruturados, que inclui as relações no ficheiro original. Pode chamar o seu modelo de formulário reconhecedor personalizado usando uma API REST simples para reduzir a complexidade e integrá-lo facilmente no seu fluxo de trabalho ou aplicação. Para começar, basta cinco de formulário de documentos ou um formulário vazio do mesmo tipo como seu material de entrada. Obter resultados precisos adaptadas ao seu conteúdo específico sem muita intervenção manual ou conhecimentos de ciência de dados extensa rapidamente.

## <a name="request-access"></a>Pedir acesso
Formulário reconhecedor está disponível uma pré-visualização de acesso limitado. Para obter acesso à pré-visualização, preencha e envie os [pedido de acesso do reconhecedor de formulário](https://aka.ms/FormRecognizerRequestAccess) formulário. O formulário solicita as informações sobre si, sua empresa e o cenário de utilizador para o qual usará o reconhecedor de formulário. Se o seu pedido é aprovado pela equipe de serviços cognitivos do Azure, receberá um e-mail com instruções para acessar o serviço.

## <a name="what-it-does"></a>O que faz

Quando envia os dados de entrada, o algoritmo prepara a ele, clusters os formulários por tipos, Deteta que as chaves e tabelas estão presentes e aprende a associar valores para chaves e as entradas a tabelas. A aprendizagem não supervisionada permite ao modelo compreender o esquema e as relações entre os campos e as entradas sem etiquetagem de dados manual ou codificação e manutenção intensivas. Por outro lado, modelos de aprendizagem automática com formação prévia padronizada de dados e são menos precisos quando utilizado com material de entrada que desvia do formatos tradicionais, como formulários específicos da indústria.

Depois de preparar o modelo, pode testar e voltar a prepará-lo e, eventualmente, utilizá-lo confiável extrair dados de formulários mais consoante as suas necessidades.

## <a name="what-it-includes"></a>O que inclui

Formulário reconhecedor está disponível como uma API REST. Pode criar, dar formação e um modelo de pontuação ao invocar a API. Se quiser, pode executar o modelo num contentor do Docker local.

## <a name="input-requirements"></a>Requisitos de entrada

Formulário reconhecedor funciona em documentos de entrada que cumpram estes requisitos:

* Formato tem de ser JPG, PNG ou PDF (texto ou digitalizados). Incorporado no texto de PDFs são melhor porque não existe nenhuma possibilidade de erro na extração de carateres e localização.
* Tamanho do ficheiro tem de ser inferior a 4 megabytes (MB).
* Para imagens, dimensões tem de ser entre 50 x 50 pixéis e 4200 x 4200 pixels.
* Se analisados de documentos de documento, formulários devem ser análises de alta qualidade.
* Texto tem de utilizar o alfabeto latino (carateres em inglês).
* Dados devem ser impressa (não código).
* Dados tem de conter as chaves e valores.
* As chaves podem aparecer acima ou à esquerda dos valores, mas nunca abaixo ou à direita.

Formulário reconhecedor atualmente não suporta estes tipos de dados de entrada:

* Tabelas complexas (tabelas aninhadas, cabeçalhos intercalados ou células e assim por diante).
* Botões de caixas de seleção ou opção.
* Mais de 50 páginas dos documentos em PDF.

## <a name="where-do-i-start"></a>Por onde devo começar?

**Passo 1:** Crie um recurso do reconhecedor de formulário no portal do Azure.

**Passo 2:** Tente um início rápido para uma experiência prática:
* [Quickstart: Preparar um modelo de formulário reconhecedor e extrair dados de formulário com a API de REST com cURL](quickstarts/curl-train-extract.md)
* [Quickstart: Preparar um modelo de formulário reconhecedor e extrair dados de formulário com a API de REST com Python](quickstarts/python-train-extract.md)

Recomendamos que utilize o serviço gratuito, quando está aprendendo a tecnologia, mas lembre-se de que o número de páginas livres é limitado a 500 páginas por mês.

**Passo 3:** Reveja as APIs REST

Utilize as seguintes APIs para treinar e extrair dados estruturados de formulários.

| API REST | Descrição |
|-----|-------------|
| Treinar | Prepare um modelo de novo para analisar seus formulários com cinco formulários do mesmo tipo ou de um formulário vazio.  |
| Analisar  |Analise um único documento passado como um fluxo para extrair os pares chave-valor e tabelas do formulário com o seu modelo personalizado.  |

Explore os [documento de referência da REST API](https://aka.ms/form-recognizer/api). 

## <a name="data-privacy-and-security"></a>Segurança e privacidade dos dados

Este serviço é disponibilizado como um [pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de um serviço do Azure sob o [termos de serviço Online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Tal como acontece com todos os serviços cognitivos, os desenvolvedores que usam o serviço do reconhecedor de formulário devem de estar ciente das políticas de Microsoft em dados do cliente. Consulte a [página dos serviços cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center para obter mais informações.

## <a name="next-steps"></a>Passos Seguintes

Concluir um [início rápido](quickstarts/curl-train-extract.md) para começar a utilizar com o [APIs do reconhecedor de formulário](https://aka.ms/form-recognizer/api).
