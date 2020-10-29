---
title: Novidades no Reconhecedor de Formato?
titleSuffix: Azure Cognitive Services
description: Compreenda as últimas alterações à API do Reconhecimento de Formulários.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: pafarley
ms.openlocfilehash: c9287e9661172480292a2214b231e7e5dac9c32f
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912248"
---
# <a name="whats-new-in-form-recognizer"></a>Novidades no Reconhecedor de Formato?

O serviço Form Recogniser é atualizado de forma contínua. Utilize este artigo para se manter atualizado com melhorias de funcionalidades, correções e atualizações de documentação.

## <a name="august-2020"></a>Agosto de 2020

### <a name="new-features"></a>Novas funcionalidades

**A pré-visualização pública do Reconhecimento de Formulários v2.1 já está disponível.** V2.1-preview.1 foi lançado, incluindo as seguintes características: 


- **Rest A referência API está disponível** - Ver a [referência v2.1-preview.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync) 
- **Novas línguas apoiadas Para além** do inglês, as [seguintes línguas](language-support.md) são agora apoiadas: para `Layout` e : inglês `Train Custom Model` `en` (simplificado) ( ( holandês ( ( ( ( francês ( ( ( ( ( ( ( ), alemão ( `zh-Hans` ( ( ( ( ( ( ( ( ( ( ( ( ) português `nl` ( ) e espanhol ( `fr` `de` `it` `pt` ). `es`
- **Deteção de marca de verificação / Seleção** – O Reconhecimento de Formulários suporta a deteção e extração de marcas de seleção, tais como caixas de verificação e botões de rádio. As marcas de seleção são extraídas `Layout` e agora também pode rotular e treinar em Comboio com `Train Custom Model`  -  _Etiquetas_ para extrair pares de valores chave para marcas de seleção. 
- **O Model Compose** permite que vários modelos sejam compostos e chamados com um único modelo ID. Quando um documento é submetido a ser analisado com um ID de modelo composto, uma etapa de classificação é executada pela primeira vez para encaminhá-lo para o modelo personalizado correto. O modelo composição está disponível para `Train Custom Model`  -  _Comboio com etiquetas._
- **O nome do modelo** adiciona um nome amigável aos seus modelos personalizados para facilitar a gestão e o rastreio.
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

* **v2.0 referência disponível** Ver a [referência v2.0 API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) e os SDKs atualizados para [.NET](/dotnet/api/overview/azure/ai.formrecognizer-readme?view=azure-dotnet), [Python,](/python/api/overview/azure/?view=azure-python) [Java](/java/api/overview/azure/ai-formrecognizer-readme?view=azure-java-preview)e [JavaScript](/javascript/api/overview/azure/?view=azure-node-latest).
* **Melhoramentos de mesa e extração** incluem melhorias de precisão e melhorias de extrações de mesa, especificamente, a capacidade de aprender cabeçalhos de mesa e estruturas em _comboios personalizados sem etiquetas._ 

* **Suporte cambial** Deteção e extração de símbolos de moeda global.
* **Azure Gov** O Form Recogniser também está disponível em Azure Gov.
* **Características de segurança reforçadas:** 
   * **Traga a sua própria chave**  O Form Recogniser encripta automaticamente os seus dados quando persistido na nuvem para protegê-los e ajudá-lo a cumprir os seus compromissos de segurança organizacional e conformidade. Por predefinição, a subscrição utiliza chaves de encriptação geridas pela Microsoft. Agora também pode gerir a sua subscrição com as suas próprias chaves de encriptação. [As teclas geridas pelo cliente (CMK), também conhecidas como trazer a sua própria chave (BYOK),](./form-recognizer-encryption-of-data-at-rest.md)oferecem uma maior flexibilidade para criar, rodar, desativar e revogar os controlos de acesso. Também pode auditar as chaves de encriptação utilizadas para proteger os dados.  
   * **Pontos finais privados** – Permite-lhe aceder de forma segura a [dados por uma Ligação Privada.](../../private-link/private-link-overview.md)


## <a name="june-2020"></a>Junho de 2020

### <a name="new-features"></a>Novas funcionalidades
* **CopyModel API adicionado aos SDKs do cliente** Agora pode utilizar os SDKs do cliente para copiar modelos de uma subscrição para outra. Consulte [Os modelos de Back up e recupere para](./disaster-recovery.md) obter informações gerais sobre esta funcionalidade.
* **Integração do Diretório Ativo Azure** Agora pode utilizar as suas credenciais AZure AD para autenticar os objetos do cliente do Form Recogniser nos SDKs.
* **Alterações específicas do SDK** Isto inclui adições de recursos menores e alterações de rutura. Consulte os sdk para obter mais informações.
  * [C# SDK Preview 3 alterlog](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Python SDK Preview 3 alterlog](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Antevisão 3 de Antevisão de Java SDK](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Pré-visualização javaScript SDK 3 alterlog](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>Abril de 2020

### <a name="new-features"></a>Novas funcionalidades
* **Suporte SDK para reconhecimento de formulário API v2.0 Visualização pública** Este mês expandimos o nosso suporte de serviço para incluir um SDK de pré-visualização para o lançamento do Reconhecimento de Formulários v2.0 (pré-visualização). Use os links abaixo para começar com o seu idioma de eleição: 
   * [SDK do .NET](/dotnet/api/overview/azure/ai.formrecognizer-readme?view=azure-dotnet)
   * [SDK Java](/java/api/overview/azure/ai-formrecognizer-readme?view=azure-java-preview)
   * [Python SDK](/python/api/overview/azure/ai-formrecognizer-readme?view=azure-python-preview)
   * [SDK JavaScript](/javascript/api/overview/azure/ai-form-recognizer-readme?view=azure-node-preview)

  O novo SDK suporta todas as funcionalidades da API v2.0 REST para o Reconhecimento de Formulários. Por exemplo, pode treinar um modelo com ou sem etiquetas e extrair texto, pares de valor chave e tabelas dos seus formulários, extrair dados de recibos com o serviço de recibos pré-construídos e extrair texto e tabelas com o serviço de layout dos seus documentos. Pode partilhar o seu feedback sobre os SDKs através do [formulário SDK Feedback](https://aka.ms/FR_SDK_v1_feedback).
 
* **Modelo personalizado de cópia** Agora pode copiar modelos entre regiões e subscrições utilizando a nova funcionalidade Copy Custom Model. Antes de invocar a API do Modelo Personalizado de Cópia, tem primeiro de obter autorização para copiar para o recurso-alvo, chamando a operação de Autorização de Cópia contra o ponto final do recurso-alvo.
   * [Gerar uma autorização de cópia](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization) REPOUSO API
   * [Copiar um modelo personalizado](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel) REPOUSO API 

### <a name="security-improvements"></a>Melhoramentos de segurança

* As Chaves Geridas pelo Cliente estão agora disponíveis para FormRecognizer. Para obter mais informações, consulte [a encriptação de dados em repouso para o Reconhecimento de Formulários](./form-recognizer-encryption-of-data-at-rest.md).
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


* **Visualização de tabelas** A ferramenta de rotulagem da amostra apresenta agora tabelas que foram reconhecidas no documento. Isto permite-lhe ver as tabelas que foram reconhecidas e extraídas do documento, antes da rotulagem e análise. Esta função pode ser alternada entre/desligando utilizando a opção camadas.

  Este é um exemplo de como as tabelas são reconhecidas e extraídas:

  > [!div class="mx-imgBorder"]
  > ![Visualização da tabela utilizando a ferramenta de rotulagem da amostra](./media/whats-new/formre-table-viz.png)

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
  * **Treine com etiquetas** Agora pode treinar um modelo personalizado com dados etiquetados manualmente. Isto resulta em modelos de melhor desempenho e pode produzir modelos que funcionam com formas ou formas complexas que contêm valores sem chaves.
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

Complete um [quickstart da biblioteca de clientes](quickstarts/client-library.md) para começar a escrever uma aplicação de processamento de formulários com o Form Recogniser no idioma à sua escolha.

## <a name="see-also"></a>Ver também

* [O que é o Reconhecedor de Formato?](./overview.md)