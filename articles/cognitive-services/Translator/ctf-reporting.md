---
title: Framework de tradução colaborativa (CTF) relatórios - API de texto do tradutor
titlesuffix: Azure Cognitive Services
description: Como utilizar relatórios de estrutura de tradução colaborativa (CTF).
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a2f8a9c5ceaac6209391df17f4ec08027991382b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58123424"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Como utilizar os relatórios do Collaborative Translation Framework (CTF)

> [!NOTE]
> Tato metoda se zamítá. Não está disponível no V3.0 da API de texto do Translator.
> 
> A colaboração traduções Framework (CTF), anteriormente disponível para a versão 2.0 da API de texto do Translator, foi preterido a partir de 1 de Fevereiro de 2018. As funções AddTranslation e AddTranslationArray permitem aos utilizadores ativar correções por meio do Framework de tradução colaborativa. Após 31 de Janeiro de 2018, essas duas funções não aceitou novos envios de sentença e os utilizadores recebem uma mensagem de erro. Essas funções estavam a ser descontinuadas e não serão substituídas.
> 
> Uma funcionalidade semelhante está disponível na API de Hub do Translator, que lhe permite criar um sistema de tradução personalizadas com a sua terminologia e estilo, e pode invocá-lo a utilizar o ID de categoria na API de texto do Translator. Hub de tradutor: [ https://hub.microsofttranslator.com ](https://hub.microsofttranslator.com). API do Translator Hub: [ https://hub.microsofttranslator.com/swagger ](https://hub.microsofttranslator.com/swagger).

A API de relatórios de estrutura de tradução colaborativa (CTF) devolve estatísticas e o conteúdo real no arquivo de CTF. Esta API é diferente do método GetTranslations() porque ele:
* Devolve o conteúdo traduzido e sua contagem total apenas a partir da sua conta (conta do Azure Marketplace ou de appId).
* Devolve o conteúdo traduzido e sua contagem total sem a necessidade de uma correspondência da sentença de origem.
* Não devolve a tradução automática (conversão de máquina).

## <a name="endpoint"></a>Ponto Final
É o ponto final da API de relatórios de CTF https://api.microsofttranslator.com/v2/beta/ctfreporting.svc


## <a name="methods"></a>Métodos
| Name |    Descrição|
|:---|:---|
| Método GetUserTranslationCounts | Obtenha contagens das traduções que são criadas pelo utilizador. |
| Método GetUserTranslations | Obtém as traduções que são criadas pelo utilizador. |

Esses métodos permitem-lhe:
* Obter o conjunto completo de traduções de utilizador e as correções com o seu ID de conta para download.
* Obter a lista dos contribuintes frequentes. Certifique-se de que o nome de utilizador correto é fornecido no AddTranslation().
* Crie uma interface de utilizador (IU) que permite aos utilizadores fidedignos ver todos os candidatos disponíveis, se necessário restritas a uma parte do seu site, com base no prefixo de URI.

> [!NOTE]
> Ambos os métodos são relativamente lento e caro. Recomenda-se usá-los com moderação.

## <a name="getusertranslationcounts-method"></a>Método GetUserTranslationCounts

Este método obtém a contagem de traduções que são criados pelo utilizador. Ele fornece a lista de contagens de tradução, agrupados por uriPrefix, de, para utilizador e parâmetros do pedido maxRating minRating.

**Sintaxe**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslationCount[]GetUserTranslationCounts(
>            string appId,
>            string uriPrefix,
>            string from,
>            string to,
>            int? minRating,
>            int? maxRating,
>            string user,
>            string category
>            DateTime? minDateUtc,
>            DateTime? maxDateUtc,
>            int? skip,
>            int? take);
> ```

**Parâmetros**

| Parâmetro | Descrição |
|:---|:---|
| appId | **Necessário** se o cabeçalho de autorização é usado, deixe o campo de appid vazio ou especificar uma cadeia de caracteres que contém "Bearer" + "" + o token de acesso.|
| uriPrefix | **Opcional** uma cadeia de caracteres que contém o prefixo do URI da tradução.|
| de | **Opcional** uma cadeia de caracteres que representa o código de idioma do texto de tradução. |
| para | **Opcional** representando o código de idioma para traduzir o texto numa cadeia de caracteres.|
| minRating| **Opcional** um valor inteiro que representa a classificação de qualidade mínima para o texto traduzido. O valor válido é entre -10 e 10. O valor predefinido é 1.|
| maxRating| **Opcional** um valor inteiro que representa a classificação de qualidade máximo para o texto traduzido. O valor válido é entre -10 e 10. O valor predefinido é 1.|
| Utilizador | **Opcional** uma cadeia de caracteres que é utilizada para filtrar o resultado com base em; o originador de envio. |
| categoria| **Opcional** uma cadeia de caracteres que contém o domínio da tradução ou a categoria. Este parâmetro dá suporte apenas a opção de padrão geral.|
| minDateUtc| **Opcional** a data de quando quiser obter as traduções. A data tem de estar no formato UTC. |
| maxDateUtc| **Opcional** a data até que deseja obter as traduções. A data tem de estar no formato UTC. |
| ignorar| **Opcional** o número de resultados que deseja ignorar numa página. Por exemplo, se pretender que a ignorar primeiro 20 linhas dos resultados e a exibição do registo de resultado 21, especifica 20 para este parâmetro. O valor predefinido para este parâmetro é 0.|
| tirar | **Opcional** o número de resultados que pretende recuperar. O número máximo de cada solicitação é 100. A predefinição é 100.|

> [!NOTE]
> Os parâmetros de solicitação skip e take ativar paginação para um grande número de registos de resultado.

**Valor de retorno**

O conjunto de resultados contém uma matriz do **UserTranslationCount**. Cada UserTranslationCount tem os seguintes elementos:

| Campo | Descrição |
|:---|:---|
| Contagem| O número de resultados que é obtido|
| A partir de | O idioma de origem|
| Classificação| A classificação que é aplicada ao emissor na chamada de método AddTranslation()|
| Para| O idioma de destino|
| URI| O URI aplicado na chamada de método AddTranslation()|
| Utilizador| O nome de utilizador|

**Exceções**

| Exceção | Mensagem | Condições |
|:---|:---|:---|
| ArgumentOutOfRangeException | O parâmetro '**maxDateUtc**'tem de ser maior ou igual a'**minDateUtc**'.| O valor do parâmetro **maxDateUtc** é inferior ao valor do parâmetro **minDateUtc**.|
| TranslateApiException | O IP é sobre a quota.| <ul><li>Foi atingido o limite do número de pedidos por minuto.</li><li>O tamanho do pedido continua a ser limitado em 10000 carateres.</li><li>Uma hora a hora e uma quota diária limitam o número de carateres que a API do Microsoft Translator irá aceitar.</li></ul>|
| TranslateApiException | AppId está sobre a quota.| O ID da aplicação excedeu a quota por hora ou diariamente.|

> [!NOTE]
> A quota será ajustado para garantir a honestidade entre todos os utilizadores do serviço.

**Exemplos de código do modo de exibição no GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>Método GetUserTranslations

Este método obtém as traduções que são criadas pelo utilizador. Ele fornece que as traduções agrupados por uriPrefix,, usuário e minRating e maxRating parâmetros do pedido.

**Sintaxe**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslation[] GetUserTranslations (
>             string appId,
>             string uriPrefix,
>             string from,
>             string to,
>             int? minRating,
>             int? maxRating,
>             string user,
>             string category
>             DateTime? minDateUtc,
>             DateTime? maxDateUtc,
>             int? skip,
>             int? take);
> ```

**Parâmetros**

| Parâmetro | Descrição |
|:---|:---|
| appId | **Necessário** se o cabeçalho de autorização é usado, deixe o campo de appid vazio ou especificar uma cadeia de caracteres que contém "Bearer" + "" + o token de acesso.|
| uriPrefix| **Opcional** uma cadeia de caracteres que contém o prefixo do URI da tradução.|
| de| **Opcional** uma cadeia de caracteres que representa o código de idioma do texto de tradução.|
| para| **Opcional** representando o código de idioma para traduzir o texto numa cadeia de caracteres.|
| minRating| **Opcional** um valor inteiro que representa a classificação de qualidade mínima para o texto traduzido. O valor válido é entre -10 e 10. O valor predefinido é 1.|
| maxRating| **Opcional** um valor inteiro que representa a classificação de qualidade máximo para o texto traduzido. O valor válido é entre -10 e 10. O valor predefinido é 1.|
| Utilizador| **Opcional. Uma cadeia de caracteres que é utilizada para filtrar o resultado com base em; o originador de envio**|
| categoria| **Opcional** uma cadeia de caracteres que contém o domínio da tradução ou a categoria. Este parâmetro dá suporte apenas a opção de padrão geral.|
| minDateUtc| **Opcional** a data de quando quiser obter as traduções. A data tem de estar no formato UTC.|
| maxDateUtc| **Opcional** a data até que deseja obter as traduções. A data tem de estar no formato UTC.|
| ignorar| **Opcional** o número de resultados que deseja ignorar numa página. Por exemplo, se pretender que a ignorar primeiro 20 linhas dos resultados e a exibição do registo de resultado 21, especifica 20 para este parâmetro. O valor predefinido para este parâmetro é 0.|
| tirar| **Opcional** o número de resultados que pretende recuperar. O número máximo de cada solicitação é 100. A predefinição é 50.|

> [!NOTE]
> Os parâmetros de solicitação skip e take ativar paginação para um grande número de registos de resultado.

**Valor de retorno**

O conjunto de resultados contém uma matriz do **UserTranslation**. Cada UserTranslation tem os seguintes elementos:

| Campo | Descrição |
|:---|:---|
| CreatedDateUtc| A data de criação da entrada, usando AddTranslation()|
| A partir de| O idioma de origem|
| OriginalText| O texto de linguagem de código que é utilizado ao submeter o pedido|
|Classificação |A classificação que é aplicada ao emissor na chamada de método AddTranslation()|
|Para|    O idioma de destino|
|TranslatedText|    A tradução como submetido na chamada de método AddTranslation()|
|URI|   O URI aplicado na chamada de método AddTranslation()|
|Utilizador   |O nome de utilizador|

**Exceções**

| Exceção | Mensagem | Condições |
|:---|:---|:---|
| ArgumentOutOfRangeException | O parâmetro '**maxDateUtc**'tem de ser maior ou igual a'**minDateUtc**'.| O valor do parâmetro **maxDateUtc** é inferior ao valor do parâmetro **minDateUtc**.|
| TranslateApiException | O IP é sobre a quota.| <ul><li>Foi atingido o limite do número de pedidos por minuto.</li><li>O tamanho do pedido continua a ser limitado em 10000 carateres.</li><li>Uma hora a hora e uma quota diária limitam o número de carateres que a API do Microsoft Translator irá aceitar.</li></ul>|
| TranslateApiException | AppId está sobre a quota.| O ID da aplicação excedeu a quota por hora ou diariamente.|

> [!NOTE]
> A quota será ajustado para garantir a honestidade entre todos os utilizadores do serviço.

**Exemplos de código do modo de exibição no GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)
