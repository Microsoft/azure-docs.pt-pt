---
title: Relatórios de CTF (estrutura de tradução colaborativa)-API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Como usar relatórios de CTF (estrutura de tradução colaborativa).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.openlocfilehash: 6a197095d97e67f7548e60375148cff57e47b797
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595933"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Como utilizar os relatórios do Collaborative Translation Framework (CTF)

> [!NOTE]
> Esse método foi preterido. Ele não está disponível em V 3.0 do API de Tradução de Texto.
> 
> A CTF (estrutura de traduções colaborativa), anteriormente disponível para a V 2.0 da API de Tradução de Texto, foi preterida a partir de 1º de fevereiro de 2018. As funções addtranslation e AddTranslationArray permitem que os usuários habilitem as correções por meio da estrutura de tradução colaborativa. Após 31 de janeiro de 2018, essas duas funções não aceitaram novos envios de sentença e os usuários recebem uma mensagem de erro. Essas funções foram desativadas e não serão substituídas.

A API de relatório da CTF (estrutura de tradução colaborativa) retorna estatísticas e o conteúdo real no repositório CTF. Essa API é diferente do método gettranslas () porque:
* Retorna o conteúdo traduzido e sua contagem total somente de sua conta (appId ou conta do Azure Marketplace).
* Retorna o conteúdo traduzido e sua contagem total sem a necessidade de uma correspondência da frase de origem.
* Não retorna a tradução automática (conversão de máquina).

## <a name="endpoint"></a>Ponto Final
O ponto de extremidade da API de relatório do CTF é https://api.microsofttranslator.com/v2/beta/ctfreporting.svc


## <a name="methods"></a>Métodos
| Nome |    Descrição|
|:---|:---|
| Método GetUserTranslationCounts | Obter contagens das traduções criadas pelo usuário. |
| Método GetUserTranslations | Recupera as traduções que são criadas pelo usuário. |

Esses métodos permitem que você:
* Recupere o conjunto completo de traduções e correções do usuário sob sua ID de conta para download.
* Obtenha a lista de colaboradores frequentes. Verifique se o nome de usuário correto é fornecido em addtranslation ().
* Crie uma interface do usuário (IU) que permite que seus usuários confiáveis vejam todos os candidatos disponíveis, se necessário, restritos a uma parte do seu site, com base no prefixo do URI.

> [!NOTE]
> Ambos os métodos são relativamente lentos e caros. É recomendável usá-las com moderação.

## <a name="getusertranslationcounts-method"></a>Método GetUserTranslationCounts

Esse método obtém a contagem de traduções que são criadas pelo usuário. Ele fornece a lista de contagens de tradução agrupadas pelos parâmetros de solicitação uriPrefix, from, User, minRating e maxRating.

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
| appId | **Necessário** Se o cabeçalho de autorização for usado, deixe o campo AppID vazio, caso contrário, especifique uma cadeia de caracteres contendo "portador" + "" + token de acesso.|
| uriPrefix | **Opcional** Uma cadeia de caracteres que contém o prefixo do URI da tradução.|
| from | **Opcional** Uma cadeia de caracteres que representa o código de idioma do texto de tradução. |
| para | **Opcional** Uma cadeia de caracteres que representa o código de idioma no qual converter o texto.|
| minRating| **Opcional** Um valor inteiro que representa a classificação de qualidade mínima para o texto traduzido. O valor válido é entre-10 e 10. O valor predefinido é 1.|
| maxRating| **Opcional** Um valor inteiro que representa a classificação de qualidade máxima para o texto traduzido. O valor válido é entre-10 e 10. O valor predefinido é 1.|
| Utilizador | **Opcional** Uma cadeia de caracteres que é usada para filtrar o resultado com base no originador do envio. |
| category| **Opcional** Uma cadeia de caracteres que contém a categoria ou o domínio da tradução. Esse parâmetro dá suporte apenas à opção padrão geral.|
| minDateUtc| **Opcional** A data de quando você deseja recuperar as traduções. A data deve estar no formato UTC. |
| maxDateUtc| **Opcional** A data até a qual você deseja recuperar as traduções. A data deve estar no formato UTC. |
| ignorar| **Opcional** O número de resultados que você deseja ignorar em uma página. Por exemplo, se você quiser ignorar as primeiras 20 linhas dos resultados e exibir do registro de resultado 21, especifique 20 para esse parâmetro. O valor padrão para esse parâmetro é 0.|
| ter | **Opcional** O número de resultados que você deseja recuperar. O número máximo de cada solicitação é 100. O padrão é 100.|

> [!NOTE]
> Os parâmetros de solicitação Skip e Take permitem paginação para um grande número de registros de resultado.

**Valor de retorno**

O conjunto de resultados contém uma matriz de **UserTranslationCount**. Cada UserTranslationCount tem os seguintes elementos:

| Campo | Descrição |
|:---|:---|
| Count| O número de resultados recuperados|
| De | O idioma de origem|
| Classificação| A classificação que é aplicada pelo emissor na chamada do método addtranslate ()|
| Para| O idioma de destino|
| URI| O URI aplicado na chamada do método addtranslation ()|
| Utilizador| O nome de usuário|

**Exceções**

| Exceção | Message | Condições |
|:---|:---|:---|
| ArgumentOutOfRangeException | O parâmetro '**maxDateUtc**' deve ser maior ou igual a '**minDateUtc**'.| O valor do parâmetro **maxDateUtc** é menor que o valor do parâmetro **minDateUtc**.|
| TranslateApiException | O IP está acima da cota.| <ul><li>O limite para o número de solicitações por minuto é atingido.</li><li>O tamanho da solicitação permanece limitado a 10000 caracteres.</li><li>Por hora e uma cota diária, limite o número de caracteres que a API do Microsoft Translator aceitará.</li></ul>|
| TranslateApiException | AppId está acima da cota.| A ID do aplicativo excedeu a cota por hora ou diária.|

> [!NOTE]
> A cota será ajustada para garantir a imparcialidade entre todos os usuários do serviço.

**Exibir exemplos de código em GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>Método GetUserTranslations

Esse método recupera as traduções que são criadas pelo usuário. Ele fornece as traduções agrupadas pelos parâmetros de solicitação uriPrefix, from, User e minRating e maxRating.

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
| appId | **Necessário** Se o cabeçalho de autorização for usado, deixe o campo AppID vazio, caso contrário, especifique uma cadeia de caracteres contendo "portador" + "" + token de acesso.|
| uriPrefix| **Opcional** Uma cadeia de caracteres que contém o prefixo do URI da tradução.|
| from| **Opcional** Uma cadeia de caracteres que representa o código de idioma do texto de tradução.|
| para| **Opcional** Uma cadeia de caracteres que representa o código de idioma no qual converter o texto.|
| minRating| **Opcional** Um valor inteiro que representa a classificação de qualidade mínima para o texto traduzido. O valor válido é entre-10 e 10. O valor predefinido é 1.|
| maxRating| **Opcional** Um valor inteiro que representa a classificação de qualidade máxima para o texto traduzido. O valor válido é entre-10 e 10. O valor predefinido é 1.|
| Utilizador| **Adicional. Uma cadeia de caracteres que é usada para filtrar o resultado com base no originador do envio**|
| category| **Opcional** Uma cadeia de caracteres que contém a categoria ou o domínio da tradução. Esse parâmetro dá suporte apenas à opção padrão geral.|
| minDateUtc| **Opcional** A data de quando você deseja recuperar as traduções. A data deve estar no formato UTC.|
| maxDateUtc| **Opcional** A data até a qual você deseja recuperar as traduções. A data deve estar no formato UTC.|
| ignorar| **Opcional** O número de resultados que você deseja ignorar em uma página. Por exemplo, se você quiser ignorar as primeiras 20 linhas dos resultados e exibir do registro de resultado 21, especifique 20 para esse parâmetro. O valor padrão para esse parâmetro é 0.|
| ter| **Opcional** O número de resultados que você deseja recuperar. O número máximo de cada solicitação é 100. O padrão é 50.|

> [!NOTE]
> Os parâmetros de solicitação Skip e Take permitem paginação para um grande número de registros de resultado.

**Valor de retorno**

O conjunto de resultados contém a matriz do usertranslation. Cada usertranslation tem os seguintes elementos:

| Campo | Descrição |
|:---|:---|
| CreatedDateUtc| A data de criação da entrada usando addtranslation ()|
| De| O idioma de origem|
| OriginalText| O texto do idioma de origem que é usado ao enviar a solicitação|
|Classificação |A classificação que é aplicada pelo emissor na chamada do método addtranslate ()|
|Para|    O idioma de destino|
|TranslatedText|    A conversão como enviada na chamada do método addtranslate ()|
|URI|   O URI aplicado na chamada do método addtranslation ()|
|Utilizador   |O nome de usuário|

**Exceções**

| Exceção | Message | Condições |
|:---|:---|:---|
| ArgumentOutOfRangeException | O parâmetro '**maxDateUtc**' deve ser maior ou igual a '**minDateUtc**'.| O valor do parâmetro **maxDateUtc** é menor que o valor do parâmetro **minDateUtc**.|
| TranslateApiException | O IP está acima da cota.| <ul><li>O limite para o número de solicitações por minuto é atingido.</li><li>O tamanho da solicitação permanece limitado a 10000 caracteres.</li><li>Por hora e uma cota diária, limite o número de caracteres que a API do Microsoft Translator aceitará.</li></ul>|
| TranslateApiException | AppId está acima da cota.| A ID do aplicativo excedeu a cota por hora ou diária.|

> [!NOTE]
> A cota será ajustada para garantir a imparcialidade entre todos os usuários do serviço.

**Exibir exemplos de código em GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)
