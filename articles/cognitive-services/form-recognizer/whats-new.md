---
title: Novidades no Reconhecedor de Formato?
titleSuffix: Azure Cognitive Services
description: Compreenda as últimas alterações à API do Reconhecimento de Formulários.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2021
ms.author: lajanuar
ms.openlocfilehash: e952d481daf53b1806dc3cfbb658c8c0c21f6984
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516302"
---
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD036 -->
# <a name="whats-new-in-form-recognizer"></a>Novidades no Reconhecedor de Formato?

O serviço Form Recogniser é atualizado de forma contínua. Utilize este artigo para se manter atualizado com melhorias de funcionalidades, correções e atualizações de documentação.

## <a name="april-2021"></a>abril de 2021
<!-- markdownlint-disable MD029 -->

### <a name="sdk-updates-api--version-21-preview3"></a>Atualizações SDK (versão API 2.1-pré-visualização.3)

### <a name="c-version-310-beta4"></a>**C# versão 3.1.0-beta.4**

* **Novos métodos para analisar dados a partir de documentos de identidade:**

   **[StartRecognizeIdDocumentsFromUriAsync](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizeiddocumentsasync?view=azure-dotnet-preview&preserve-view=true)**

   **[StartRecognizeIdDocumentsAsync](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizeiddocumentsasync?view=azure-dotnet-preview&preserve-view=true)**

   Para obter uma lista de valores de campo, _consulte_ [Campos extraídos](concept-identification-cards.md#fields-extracted) na nossa documentação do Reconhecimento de Formulários.

* Expandiu o conjunto de idiomas documentais que podem ser fornecidos ao método **[StartRecognizeContent.](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecontent?view=azure-dotnet-preview&preserve-view=true)**

* **Novo `Pages` imóvel suportado pelas seguintes classes:**

   **[Reconhecer Opções de Cartões de Negócio](/dotnet/api/azure.ai.formrecognizer.recognizebusinesscardsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[Reconhecer Opções de Formas DeCustom](/dotnet/api/azure.ai.formrecognizer.recognizecustomformsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[ReconhecerInvoicesOptions](/dotnet/api/azure.ai.formrecognizer.recognizeinvoicesoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[Reconhecimento DeReceiptsOptions](/dotnet/api/azure.ai.formrecognizer.recognizereceiptsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>

   A `Pages` propriedade permite selecionar indivíduos ou uma gama de páginas para documentos PDF e TIFF de várias páginas. Para páginas individuais, insira o número da página, por exemplo, `3` . Para uma série de páginas (como a página 2 e as páginas 5-7) insira os números e intervalos de idade p separados por vírgulas: `2, 5-7` .    

* **Novo `ReadingOrder` imóvel suportado para a seguinte classe:**

   **[ReconhecerContentOptions](/dotnet/api/azure.ai.formrecognizer.recognizecontentoptions?view=azure-dotnet-preview&preserve-view=true)**

  A `ReadingOrder` propriedade é um parâmetro opcional que permite especificar qual o algoritmo de ordem de leitura ou - deve ser aplicado para ordenar a `basic` `natural` extração de elementos de texto. Se não for especificado, o valor predefinido é `basic` .

#### <a name="breaking-changes"></a>Alterações interruptivas

* O cliente está em incumprimento da versão de serviço suportada mais recente, que é atualmente **2.1-preview.3**.

* **[StartRecognizeCustomForms](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecustomforms?view=azure-dotnet-preview&preserve-view=true#Azure_AI_FormRecognizer_FormRecognizerClient_StartRecognizeCustomForms_System_String_System_IO_Stream_Azure_AI_FormRecognizer_RecognizeCustomFormsOptions_System_Threading_CancellationToken_)** agora lança um `RequestFailedException()` quando um ficheiro inválido é passado.

### <a name="java-version-310-beta3"></a>**Versão Java 3.1.0-beta.3**

* **Novos métodos para analisar dados a partir de documentos de identidade:**

  **[beginRecognizeIdDocumentsFromUrl](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizeiddocumentsfromurl?view=azure-java-preview&preserve-view=true)**

  **[inícioRecognizeIdDocuments](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizeiddocuments?view=azure-java-preview&preserve-view=true)**

   Para obter uma lista de valores de campo, _consulte_ [Campos extraídos](concept-identification-cards.md#fields-extracted) na nossa documentação do Reconhecimento de Formulários.

* **Suporte de ficheiro bitmap (.bmp) para formas personalizadas e métodos de treino no `FormContentType` enum**:

* `image/bmp`

* **Novo `Pages` imóvel suportado pelas seguintes classes:**

   **[Reconhecer Opções de Cartões de Negócio](/java/api/com.azure.ai.formrecognizer.models.recognizebusinesscardsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[Reconhecer Opções DeCustom](/java/api/com.azure.ai.formrecognizer.models.recognizecustomformsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[ReconhecerInvoicesOptions](/java/api/com.azure.ai.formrecognizer.models.recognizeinvoicesoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[Reconhecimento DeReceiptsOptions](/java/api/com.azure.ai.formrecognizer.models.recognizereceiptsoptions?view=azure-java-preview&preserve-view=true)**</br>

  A `Pages` propriedade permite selecionar indivíduos ou uma gama de páginas para documentos PDF e TIFF de várias páginas. Para páginas individuais, insira o número da página, por exemplo, `3` . Para uma série de páginas (como a página 2 e as páginas 5-7) insira os números de página e os intervalos separados por vírgulas: `2, 5-7` .

* **Suporte de ficheiro de imagem Bitmap (.bmp) para formas personalizadas e métodos de treino nos campos [FormContentType](/java/api/com.azure.ai.formrecognizer.models.formcontenttype?view=azure-java-preview&preserve-view=true#fields)**:

  `image/bmp`

* **Novo argumento de palavra-chave `ReadingOrder` apoiado para os seguintes métodos:**

* **[beginRecognizeContent](https://docs.microsoft.com/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontent?view=azure-java-preview&preserve-view=true)**</br>
**[beginRecognizeContentFromUrl](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontentfromurl?view=azure-java-preview&preserve-view=true)**</br>

   O `ReadingOrder` argumento da palavra-chave é um parâmetro opcional que permite especificar qual o algoritmo de ordem de leitura `basic` ou - deve ser aplicado para ordenar a `natural` extração de elementos de texto. Se não for especificado, o valor predefinido é `basic` .

* O cliente está em incumprimento da versão de serviço suportada mais recente, que atualmente é **2.1-preview.3**.

### <a name="javascript-version-310-beta3"></a>**Versão JavaScript 3.1.0-beta.3**

* **Novos métodos para analisar dados a partir de documentos de identidade:**

    **[beginRecognizeIdDocumentsFromUrl](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocumentsFromUrl_string__BeginRecognizeIdDocumentsOptions_)**

    **[inícioRecognizeIdDocuments](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocuments_FormRecognizerRequestBody__BeginRecognizeIdDocumentsOptions_)**

    Para obter uma lista de valores de campo, _consulte_ [Campos extraídos](concept-identification-cards.md#fields-extracted) na nossa documentação do Reconhecimento de Formulários.

* **Novos valores de campo adicionados à interface FieldValue:**

    `gender`— valores possíveis são `M` `F` ou `X` .</br>
   `country`— os valores possíveis seguem a cadeia de códigos de três letras [ISO alpha-3.](https://www.iso.org/obp/ui/#search)

* **Nova opção `pages` suportada por todos os métodos de reconhecimento de formulários (formas personalizadas e todos os modelos pré-construídos). O argumento permite-lhe selecionar indivíduos ou uma série de páginas para documentos PDF e TIFF de várias páginas. Para páginas individuais, insira o número da página, por exemplo, `3` . Para uma série de páginas (como a página 2 e as páginas 5-7) insira os números de página e os intervalos separados por vírgulas: `2, 5-7` .

* Suporte adicional para um tipo **[ReadingOrder](/javascript/api/@azure/ai-form-recognizer/readingorder?view=azure-node-preview&preserve-view=true)** aos métodos de reconhecimento de conteúdo. Esta opção permite-lhe controlar o algoritmo que o serviço utiliza para determinar como devem ser encomendadas linhas de texto reconhecidas. Pode especificar qual o algoritmo de ordem de leitura `basic` ou - deve ser aplicado para ordenar a `natural` extração de elementos de texto. Se não for especificado, o valor predefinido é `basic` .

* Tipo **[De Divisão FormField](/javascript/api/@azure/ai-form-recognizer/formfield?view=azure-node-preview&preserve-view=true)** em várias interfaces diferentes. Isto não deve causar quaisquer problemas de compatibilidade da API exceto em certos casos de borda (valor indefinidoType).

* Migrado para o ponto final de serviço **2.1-preview.3** Do reconhecimento de formulários para todas as chamadas da API REST.

### <a name="python-version--310b4"></a>**Versão python 3.1.0b4**

* **Novos métodos para analisar dados a partir de documentos de identidade:**

  **[begin_recognize_id_documents_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  **[begin_recognize_id_documents](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  Para obter uma lista de valores de campo, _consulte_ [Campos extraídos](concept-identification-cards.md#fields-extracted) na nossa documentação do Reconhecimento de Formulários.

* **Novos valores de campo adicionados ao [FieldValueType](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.fieldvaluetype?view=azure-python-preview&preserve-view=true) enum:**

   género — valores possíveis são `M` `F` ou `X` .

  país — valores possíveis sigam [os códigos de países ISO alpha-3](https://www.iso.org/obp/ui/#search).

* **Suporte de ficheiro de imagem Bitmap (.bmp) para formas personalizadas e métodos de formação no [FormContentType](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formcontenttype?view=azure-python-preview&preserve-view=true) enum**:

    imagem/bmp

* **Novo argumento de palavra-chave `pages` apoiado pelos seguintes métodos:**

    **[begin_recognize_receipts](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true&branch=main#begin-recognize-receipts-receipt----kwargs-)**

    **[begin_recognize_receipts_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-receipts-from-url-receipt-url----kwargs-)**

   **[begin_recognize_business_cards](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-business-card----kwargs-)**

   **[begin_recognize_business_cards_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-from-url-business-card-url----kwargs-)**

   **[begin_recognize_invoices](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-invoice----kwargs-)**

   **[begin_recognize_invoices_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-from-url-invoice-url----kwargs-)**

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

  **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   O `pages` argumento da palavra-chave permite-lhe selecionar indivíduos ou uma série de páginas para documentos PDF e TIFF de várias páginas. Para páginas individuais, insira o número da página, por exemplo, `3` . Para uma série de páginas (como a página 2 e as páginas 5-7) insira os números de página e os intervalos separados por vírgulas: `2, 5-7` .

* **Novo argumento de palavra-chave `readingOrder` apoiado para os seguintes métodos:**

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

   **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   O `readingOrder` argumento da palavra-chave é um parâmetro opcional que permite especificar qual o algoritmo de ordem de leitura `basic` ou - deve ser aplicado para ordenar a `natural` extração de elementos de texto. Se não for especificado, o valor predefinido é `basic` .

## <a name="march-2021"></a>março de 2021

**Form Recogniser v2.1 pré-visualização pública 3 já está disponível.** v2.1-pré-visualização.3 foi lançado, incluindo as seguintes características:

* **Novo modelo de ID pré-construído** O novo modelo de ID pré-construído permite que os clientes levem IDs e devolvam dados estruturados para automatizar o processamento. Combina as nossas poderosas capacidades de reconhecimento de caracteres óticos (OCR) com modelos de compreensão de ID para extrair informações chave de passaportes e cartas de condução dos EUA, tais como nome, data de nascimento, data de emissão, data de validade, e muito mais.

  [Saiba mais sobre o modelo de ID pré-construído](concept-identification-cards.md)

   :::image type="content" source="./media/id-canada-passport-example.png" alt-text="exemplo passaporte" lightbox="./media/id-canada-passport-example.png":::

* **Extração de artigos de linha para modelo de fatura pré-construída** - O modelo de fatura pré-construída suporta agora a extração de artigos de linha; extrai agora itens completos e suas partes - descrição, quantidade, quantidade, identificação do produto, data e muito mais. Com uma chamada simples API/SDK, pode extrair dados úteis das suas faturas - texto, tabela, pares de valor-chave e itens de linha.

   [Saiba mais sobre o modelo de fatura pré-construído](concept-invoices.md)

* **Rotulagem e formação de mesa supervisionada, rotulagem de valor vazio** - Para além [das capacidades de extração automática de mesas de aprendizagem profunda de](https://techcommunity.microsoft.com/t5/azure-ai/enhanced-table-extraction-from-documents-with-form-recognizer/ba-p/2058011)última geração da Form Recogniser, permite agora que os clientes rotulem e treinem em mesas. Esta nova versão inclui a capacidade de rotular e treinar itens/tabelas em linha (dinâmicos e fixos) e treinar um modelo personalizado para extrair pares de valores-chave e itens de linha. Uma vez treinado um modelo, o modelo extrairá itens de linha como parte da saída JSON na secção DocumentResults.

    :::image type="content" source="./media/table-labeling.png" alt-text="Rotulagem de mesa" lightbox="./media/table-labeling.png":::

    Além das tabelas de rotulagem, pode agora rotular valores e regiões vazios; se alguns documentos do seu conjunto de formação não tiverem valores para determinados campos, pode rotulá-los para que o seu modelo saiba extrair valores corretamente de documentos analisados.

* **Suporte para 66 novos idiomas** - Form Recogniser's Layout API e Modelos Personalizados suportam agora 73 idiomas.

  [Saiba mais sobre o suporte linguístico do Form Recogniser](language-support.md)

* **Ordem de leitura natural, classificação de caligrafia e seleção de página** - Com esta atualização, pode optar por obter as saídas da linha de texto na ordem de leitura natural em vez da encomenda padrão da esquerda para a direita e de cima para baixo. Utilize o novo parâmetro de consulta de leiturasordene e desa couuse-o a um valor "natural" para uma saída de ordem de leitura mais amiga do homem. Além disso, para as línguas latinas, o Form Recogniser classificará as linhas de texto como estilo manuscrito ou não e dará uma pontuação de confiança.

* **Melhorias de qualidade do modelo de receção pré-construídas** Esta atualização inclui muitas melhorias de qualidade para o modelo de Recibo pré-construído, especialmente em torno da extração de item de linha.

## <a name="november-2020"></a>Novembro de 2020

### <a name="new-features"></a>Novas funcionalidades

**Form Recogniser v2.1 pré-visualização pública 2 já está disponível.** v2.1-pré-visualização.2 foi lançado, incluindo as seguintes características:

- **Novo modelo de fatura pré-construído** - O novo modelo de Fatura pré-construído permite que os clientes levem faturas em vários formatos e devolvam dados estruturados para automatizar o processamento da fatura. Combina as nossas poderosas capacidades de Reconhecimento de Caracteres Óticos (OCR) com a compreensão da fatura de modelos de aprendizagem profunda para extrair informações-chave das faturas em inglês. Extrai texto chave, tabelas e informações como cliente, fornecedor, identificação de fatura, data de vencimento da fatura, total, valor devido, valor do imposto, envio e fatura para.

  > [Saiba mais sobre o modelo de fatura pré-construído](concept-invoices.md)

  :::image type="content" source="./media/invoice-example.jpg" alt-text="exemplo de fatura" lightbox="./media/invoice-example.jpg":::

- **Extração de mesa melhorada** - O Reconhecimento de Formulários fornece agora uma extração melhorada da mesa, que combina as nossas poderosas capacidades de reconhecimento de caracteres óticos (OCR) com um modelo de extração de mesa de aprendizagem profunda. O Form Recogniser pode extrair dados de tabelas, incluindo tabelas complexas com colunas fundidas, linhas, sem fronteiras e muito mais.

  :::image type="content" source="./media/tables-example.jpg" alt-text="tabelas exemplo" lightbox="./media/tables-example.jpg":::


  > [Saiba mais sobre a extração de Layout](concept-layout.md)

- **Atualização da biblioteca** de clientes - As versões mais recentes das bibliotecas de [clientes](quickstarts/client-library.md) para .NET, Python, Java e JavaScript suportam o Formulário Reconhecedor 2.1 API.
- **Nova linguagem suportada: Japonês** - As seguintes novas línguas são agora apoiadas: para `AnalyzeLayout` e : `AnalyzeCustomForm` japonês ( `ja` ). [Suporte de idiomas](language-support.md)
- **Indicação de estilo de linha de texto (manuscrito/outro) (apenas línguas latinas)** - O Reconhecedor de Formulários agora produz um `appearance` objeto classificando se cada linha de texto é ou não estilo manuscrito, juntamente com uma pontuação de confiança. Esta funcionalidade é suportada apenas para línguas latinas.
- **Melhorias de qualidade** - Melhorias de extração, incluindo melhorias na extração de um dígito.
- **Nova funcionalidade de try-it-out na ferramenta de amostra e rotulagem do Reconhecimento de Formulários** - Capacidade de experimentar modelos pré-construídos de fatura, recibo e cartão de visita e a API de layout utilizando a ferramenta de rotulagem da amostra do reconhecimento de formulários. Veja como os seus dados serão extraídos sem escrever nenhum código.

  > [Experimente a ferramenta de amostra do reconhecimento de formulários](https://fott-preview.azurewebsites.net/)

  ![Exemplo FOTT](./media/ui-preview.jpg)

- **Loop de feedback** - Ao analisar ficheiros através da ferramenta de rotulagem da amostra, pode agora também adicioná-lo ao conjunto de treino e ajustar as etiquetas se necessário e treinar para melhorar o modelo.
- **Documentos de etiquetagem** automática - Rotula automaticamente documentos adicionais com base em documentos com etiqueta anteriores no projeto.

## <a name="august-2020"></a>Agosto de 2020

### <a name="new-features"></a>Novas funcionalidades

**A pré-visualização pública do Reconhecimento de Formulários v2.1 já está disponível.** V2.1-preview.1 foi lançado, incluindo as seguintes características:


- **Rest A referência API está disponível** - Ver a [referência v2.1-preview.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync)
- **Novas línguas apoiadas Para além** do inglês, as [seguintes línguas](language-support.md) são agora apoiadas: para `Layout` e : inglês `Train Custom Model` `en` (simplificado) ( ( `zh-Hans` holandês ( francês `nl` ( ), alemão `fr` `de` `it` `pt` `es` ( ) português ( ) e espanhol ( ).
- **Deteção de marca de verificação / Seleção** – O Reconhecimento de Formulários suporta a deteção e extração de marcas de seleção, tais como caixas de verificação e botões de rádio. As marcas de seleção são extraídas `Layout` e agora também pode rotular e treinar em Comboio com `Train Custom Model`  -  _Etiquetas_ para extrair pares de valores chave para marcas de seleção.
- **Model Compose** - permite que vários modelos sejam compostos e chamados com um único modelo ID. Quando se submete um documento a ser analisado com um ID de modelo composto, é feita pela primeira vez uma etapa de classificação para encaminhá-lo para o modelo personalizado correto. O Modelo Compose está disponível para `Train Custom Model`  -  _Comboio com etiquetas._
- **Nome do modelo** - adicione um nome amigável aos seus modelos personalizados para facilitar a gestão e o rastreio.
- **[Novo modelo pré-construído para cartões de visita](concept-business-cards.md)** para extrair campos comuns em inglês, cartões de visita em língua.
- **[Novas localidades para recibos pré-construídos](concept-receipts.md)** para além da EN-US, o suporte está agora disponível para EN-AU, EN-CA, EN-GB, EN-IN
- **Melhorias de qualidade** para `Layout` , Tre sem `Train Custom Model`  -  _Etiquetas_ e Comboio com _Etiquetas._

**v2.0** inclui a seguinte atualização:

- As [bibliotecas de clientes](quickstarts/client-library.md) net, Python, Java e JavaScript entraram na Disponibilidade Geral.

**Novas amostras** estão disponíveis no GitHub.

- As [Receitas de Extração de Conhecimento - Forms Playbook](https://github.com/microsoft/knowledge-extraction-recipes-forms) recolhe as melhores práticas a partir de compromissos reais do cliente do Form Recogniser e fornece amostras de código utilizáveis, listas de verificação e pipelines de amostras utilizados no desenvolvimento destes projetos.
- A [ferramenta de rotulagem da amostra](https://github.com/microsoft/OCR-Form-Tools) foi atualizada para suportar a nova funcionalidade v2.1. Veja este [quickstart](quickstarts/label-tool.md) para começar com a ferramenta.
- A amostra de Reconhecimento [de Formulários de Quiosque Inteligente](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md) mostra como integrar e treinar sem `Analyze Receipt` `Train Custom Model`  -  _etiquetas._

## <a name="july-2020"></a>Julho de 2020

### <a name="new-features"></a>Novas funcionalidades
<!-- markdownlint-disable MD004 -->
* **v2.0 referência disponível** - Ver a [referência V2.0 API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) e os SDKs atualizados para [.NET](/dotnet/api/overview/azure/ai.formrecognizer-readme), [Python,](/python/api/overview/azure/) [Java,](/java/api/overview/azure/ai-formrecognizer-readme)e [JavaScript](/javascript/api/overview/azure/).
* **Melhoramentos de mesa e melhorias de extração** - inclui melhorias de precisão e melhorias de extrações de mesa, especificamente, a capacidade de aprender cabeçalhos e estruturas de mesas em _comboios personalizados sem etiquetas_.

* **Suporte cambial** - Deteção e extração de símbolos de moeda global.
* **Azure Gov** - Form Recogniser também está disponível em Azure Gov.
* **Características de segurança reforçadas:**
  * **Traga a sua própria chave** - O Form Recogniser encripta automaticamente os seus dados quando persistido na nuvem para protegê-los e ajudá-lo a cumprir os seus compromissos de segurança organizacional e conformidade. Por predefinição, a subscrição utiliza chaves de encriptação geridas pela Microsoft. Agora também pode gerir a sua subscrição com as suas próprias chaves de encriptação. [As teclas geridas pelo cliente, também conhecidas como trazer a sua própria chave (BYOK),](./encrypt-data-at-rest.md)oferecem uma maior flexibilidade para criar, rodar, desativar e revogar os controlos de acesso. Também pode auditar as chaves de encriptação utilizadas para proteger os dados.
  * **Pontos finais privados** – Permite-lhe aceder de forma segura a [dados por uma Ligação Privada.](../../private-link/private-link-overview.md)

## <a name="june-2020"></a>Junho de 2020

### <a name="new-features"></a>Novas funcionalidades

* **CopyModel API adicionado aos SDKs do cliente** - Agora pode utilizar os SDKs do cliente para copiar modelos de uma subscrição para outra. Consulte [Os modelos de Back up e recupere para](./disaster-recovery.md) obter informações gerais sobre esta funcionalidade.
* **Integração do Azure Ative Directory** - Pode agora utilizar as suas credenciais AZure AD para autenticar os seus objetos de cliente Do Reconhecimento de Formulários nos SDKs.
* **Alterações específicas da SDK** - Esta alteração inclui adições de recursos menores e alterações de rutura. Consulte os sdk para obter mais informações.
  * [C# SDK Preview 3 alterlog](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Python SDK Preview 3 alterlog](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Antevisão 3 de Antevisão de Java SDK](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Pré-visualização javaScript SDK 3 alterlog](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>Abril de 2020

### <a name="new-features"></a>Novas funcionalidades

* **Suporte SDK para a pré-visualização pública do Reconhecimento de Formulários V2.0** - Este mês expandimos o nosso suporte de serviço para incluir um SDK de pré-visualização para o lançamento do Reconhecimento de Formulários v2.0 (pré-visualização). Use os links abaixo para começar com o seu idioma de eleição:
  * [SDK do .NET](/dotnet/api/overview/azure/ai.formrecognizer-readme)
  * [SDK Java](/java/api/overview/azure/ai-formrecognizer-readme)
  * [Python SDK](/python/api/overview/azure/ai-formrecognizer-readme)
  * [SDK JavaScript](/javascript/api/overview/azure/ai-form-recognizer-readme)

  O novo SDK suporta todas as funcionalidades da API v2.0 REST para o Reconhecimento de Formulários. Por exemplo, pode treinar um modelo com ou sem etiquetas e extrair texto, pares de valor chave e tabelas dos seus formulários, extrair dados de recibos com o serviço de recibos pré-construídos e extrair texto e tabelas com o serviço de layout dos seus documentos. Pode partilhar o seu feedback sobre os SDKs através do [formulário SDK Feedback](https://aka.ms/FR_SDK_v1_feedback).

* **Modelo personalizado de cópia** Agora pode copiar modelos entre regiões e subscrições utilizando a nova funcionalidade Copy Custom Model. Antes de invocar a API do Modelo Personalizado de Cópia, tem primeiro de obter autorização para copiar para o recurso-alvo, chamando a operação de Autorização de Cópia contra o ponto final do recurso-alvo.

  * [Gerar uma autorização de cópia](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization) REPOUSO API
  * [Copiar um modelo personalizado](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel) REPOUSO API

### <a name="security-improvements"></a>Melhoramentos de segurança

* Customer-Managed Keys estão agora disponíveis para FormRecognizer. Para obter mais informações, consulte [a encriptação de dados em repouso para o Reconhecimento de Formulários](./encrypt-data-at-rest.md).
* Utilize identidades geridas para acesso aos recursos Azure com o Azure Ative Directory. Para mais informações, consulte [Autoriza o acesso a identidades geridas.](../authentication.md#authorize-access-to-managed-identities)

## <a name="march-2020"></a>Março de 2020

### <a name="new-features"></a>Novas funcionalidades

* **Tipos de valor para rotulagem** Agora pode especificar os tipos de valores que está a rotular com a ferramenta de rotulagem da amostra do Reconhecimento de Formulários. Os seguintes tipos de valor e variações são atualmente suportados:
  * `string`
    * padrão, `no-whitespaces` , `alphanumeric`
  * `number`
    * padrão, `currency`
  * `date`
    * padrão, `dmy` `mdy` , `ymd`
  * `time`
  * `integer`

  Consulte o guia [da ferramenta de rotulagem da amostra](./quickstarts/label-tool.md#specify-tag-value-types) para aprender a utilizar esta função.


* **Visualização de tabelas** A ferramenta de rotulagem da amostra apresenta agora tabelas que foram reconhecidas no documento. Esta funcionalidade permite-lhe visualizar as tabelas que foram reconhecidas e extraídas do documento, antes de rotular e analisar. Esta função pode ser alternada entre/desligando utilizando a opção camadas.

  A imagem a seguir é um exemplo de como as tabelas são reconhecidas e extraídas:

  > [!div class="mx-imgBorder"]
  > ![Visualização da tabela utilizando a ferramenta de rotulagem da amostra](./media/whats-new/table-viz.png)

    As tabelas extraídas estão disponíveis na saída JSON em `"pageResults"` .

  > [!IMPORTANT]
  > As mesas de rotulagem não são suportadas. Se as tabelas não forem reconhecidas e extraídas automaticamente, só pode rotulá-las como pares chave/valor. Ao rotular as tabelas como pares chave/valor, rotular cada célula como um valor único.

### <a name="extraction-enhancements"></a>Melhoramentos de extração

Esta versão inclui melhorias de extração e melhorias de precisão, especificamente, a capacidade de rotular e extrair múltiplos pares de chaves/valor na mesma linha de texto.

### <a name="sample-labeling-tool-is-now-open-source"></a>Ferramenta de rotulagem de amostra é agora de código aberto

A ferramenta de rotulagem da amostra do Form Recogniser está agora disponível como um projeto de código aberto. Pode integrá-lo dentro das suas soluções e fazer alterações específicas do cliente para atender às suas necessidades.

Para obter mais informações sobre a ferramenta de rotulagem da amostra do Reconhecimento de Formulários, reveja a documentação disponível no [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

### <a name="tls-12-enforcement"></a>Imposição de TLS 1.2

O TLS 1.2 passa a ser aplicado para todos os pedidos HTTP a este serviço. Para mais informações, consulte [a segurança dos Serviços Cognitivos Azure.](../cognitive-services-security.md)

## <a name="january-2020"></a>Janeiro de 2020

Esta versão introduz o Reconhecimento de Formulários 2.0 (pré-visualização). Nas secções abaixo, encontrará mais informações sobre novas funcionalidades, melhorias e alterações.

### <a name="new-features"></a>Novas funcionalidades

* **Modelo personalizado**
  * **Treine com etiquetas** Agora pode treinar um modelo personalizado com dados etiquetados manualmente. Este método resulta em modelos de melhor desempenho e pode produzir modelos que funcionam com formas ou formas complexas que contêm valores sem chaves.
  * **API assíncrono** Você pode usar chamadas de API async para treinar e analisar grandes conjuntos de dados e ficheiros.
  * **Suporte de ficheiros TIFF** Agora pode treinar e extrair dados de documentos do TIFF.
  * **Melhorias da precisão da extração**

* **Modelo de recibo pré-construído**
  * **Montantes de gorjeta** Agora pode extrair quantidades de ponta e outros valores manuscritos.
  * **Extração de artigos de linha** Pode extrair valores de artigos de linha a partir de recibos.
  * **Valores de confiança** Pode ver a confiança do modelo por cada valor extraído.
  * **Melhorias da precisão da extração**

* **Extração de layout** Pode agora utilizar a API do Layout para extrair dados de texto e dados de tabela dos seus formulários.

### <a name="custom-model-api-changes"></a>Alterações de API de modelo personalizado

Todas as APIs para treino e utilização de modelos personalizados foram renomeadas, e alguns métodos sincronizados são agora assíncronos. Seguem-se as principais alterações:

* O processo de formação de um modelo é agora assíncronos. Inicia o treino através da chamada **API /personalizada/modelo.** Esta chamada devolve um ID de operação, que pode passar para **personalizado/modelo/{modelID}** para devolver os resultados do treino.
* A extração chave/valor é agora iniciada pela chamada **API /custom/model/{modelID}/análise.** Esta chamada devolve um ID de operação, que pode passar para **personalizado/modelo/{modelID}/análiseResults/{resultID}** para devolver os resultados da extração.
* As operação IDs para a operação do comboio encontram-se agora no cabeçalho de **localização** das respostas HTTP, e não no **cabeçalho operação-localização.**

### <a name="receipt-api-changes"></a>Alterações da API de receção

As APIs para a leitura dos recibos de venda foram renomeadas.

* A extração de dados de receção é agora iniciada pela chamada **API /pré-incorporada/recibo/análise.** Esta chamada devolve um ID de operação, que pode passar para **/pré-incorporado/recibo/análiseResults/{resultID}** para devolver os resultados da extração.

### <a name="output-format-changes"></a>Alterações no formato de saída

As respostas JSON para todas as chamadas da API têm novos formatos. Algumas chaves e valores foram adicionados, removidos ou renomeados. Consulte os quickstarts para exemplos dos formatos JSON atuais.

## <a name="next-steps"></a>Passos seguintes

Complete um [quickstart](quickstarts/client-library.md) para começar a escrever uma aplicação de processamento de formulários com o Form Recogniser no idioma de desenvolvimento da sua escolha.

## <a name="see-also"></a>Ver também

* [O que é o Reconhecedor de Formato?](./overview.md)