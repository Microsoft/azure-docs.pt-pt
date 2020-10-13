---
title: Enquadramento colaborativo de tradução (CTF) Reporte - Tradutor
titleSuffix: Azure Cognitive Services
description: Como utilizar o relatório do Quadro de Tradução Colaborativa (CTF).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.custom: devx-track-csharp
ms.openlocfilehash: 68b0de40940fa75dd4eb4e1572405f31ce1c22b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88934383"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Como utilizar os relatórios do Collaborative Translation Framework (CTF)

> [!NOTE]
> Este método é precotado. Não está disponível na V3.0 do Tradutor.
> 
> O Quadro colaborativo de Traduções (CTF), anteriormente disponível para V2.0 de Tradutor, foi depreciado a partir de 1 de fevereiro de 2018. As funções AddTranslation e AddTranslationArray permitem aos utilizadores permitir correções através do Quadro de Tradução Colaborativa. Após 31 de janeiro de 2018, estas duas funções não aceitaram novas submissões de sentenças, e os utilizadores recebem uma mensagem de erro. Estas funções foram retiradas e não serão substituídas.

O Quadro Colaborativo de Tradução (CTF) Reportando API devolve estatísticas e o conteúdo real na loja CTF. Esta API é diferente do método GetTranslations porque:
* Devolve o conteúdo traduzido e a sua contagem total apenas a partir da sua conta (appId ou conta Azure Marketplace).
* Devolve o conteúdo traduzido e a sua contagem total sem exigir uma correspondência da frase de origem.
* Não devolve a tradução automática (tradução automática).

## <a name="endpoint"></a>Ponto final
O ponto final da API de reporte ctf é https://api.microsofttranslator.com/v2/beta/ctfreporting.svc .

## <a name="methods"></a>Métodos
| Nome | Descrição|
|:---|:---|
| Método GetUserTranslationCounts | Obtenha contagens das traduções que são criadas pelo utilizador. |
| Método getUserTranslations | Recupera as traduções que são criadas pelo utilizador. |

Estes métodos permitem-lhe:
* Recupere o conjunto completo de traduções e correções do utilizador no iD da sua conta para download.
* Obtenha a lista dos colaboradores frequentes. Certifique-se de que o nome de utilizador correto é fornecido no AddTranslation().
* Construa uma interface de utilizador (UI) que permita aos seus utilizadores de confiança ver todos os candidatos disponíveis, se necessário, restritos a uma parte do seu site, com base no prefixo URI.

> [!NOTE]
> Ambos os métodos são relativamente lentos e caros. Recomenda-se usá-las com moderação.

## <a name="getusertranslationcounts-method"></a>Método GetUserTranslationCounts

Este método obtém a contagem de traduções que são criadas pelo utilizador. Fornece a lista de contagens de tradução agrupadas pelo uriPrefix, de, para, utilizador, minRating e maxRating request parâmetros.

**Syntax**

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
| appId | **Requerido** Se o cabeçalho de autorização for utilizado, deixe o campo appid vazio, especifique uma corda que contenha "Portador" + " + símbolo de acesso.|
| uriPrefix | **Opcional** Uma corda que contém prefixo de URI da tradução.|
| De | **Opcional** Uma cadeia que representa o código linguístico do texto de tradução. |
| para | **Opcional** Uma cadeia que representa o código linguístico para traduzir o texto em.|
| minRating| **Opcional** Um valor inteiro que representa a classificação mínima de qualidade para o texto traduzido. O valor válido é entre -10 e 10. O valor predefinido é 1.|
| maxRating| **Opcional** Um valor inteiro que representa a classificação máxima de qualidade para o texto traduzido. O valor válido é entre -10 e 10. O valor predefinido é 1.|
| utilizador | **Opcional** Uma corda que é usada para filtrar o resultado com base no autor da submissão. |
| categoria| **Opcional** Uma cadeia que contém a categoria ou domínio da tradução. Este parâmetro suporta apenas a opção padrão geral.|
| minDateUtc| **Opcional** A data de quando quiser recuperar as traduções. A data deve estar no formato UTC. |
| maxDateUtc| **Opcional** A data até quando quiser recuperar as traduções. A data deve estar no formato UTC. |
| saltar| **Opcional** O número de resultados que pretende ignorar numa página. Por exemplo, se quiser saltar as primeiras 20 linhas dos resultados e ver a partir do 21º resultado, especifique 20 para este parâmetro. O valor predefinido para este parâmetro é 0.|
| take | **Opcional** O número de resultados que quer recuperar. O número máximo de cada pedido é de 100. A predefinição é 100.|

> [!NOTE]
> Os parâmetros de solicitação de salto e de tomada permitem a paginação para um grande número de registos de resultados.

**Valor de retorno**

O conjunto de resultados contém a matriz do **UserTranslationCount**. Cada UserTranslationCount tem os seguintes elementos:

| Campo | Descrição |
|:---|:---|
| Contagem| O número de resultados que é recuperado|
| De | A língua de origem|
| Classificação| A classificação que é aplicada pelo apresentador na chamada de método AddTranslation()|
| Para| A linguagem-alvo|
| Uri| O URI aplicado na chamada de método AddTranslation()|
| Utilizador| O nome de utilizador|

**Exceções**

| Exceção | Mensagem | Condições |
|:---|:---|:---|
| ArgumentOutOfRangeException | O parâmetro '**maxDateUtc**' deve ser maior ou igual a '**minDateUtc**'.| O valor do parâmetro **maxDateUtc** é inferior ao valor do parâmetro **minDateUtc**.|
| TraduçãoExcepção | O IP está acima da quota.| <ul><li>O limite para o número de pedidos por minuto é atingido.</li><li>O tamanho do pedido permanece limitado em 10000 caracteres.</li><li>Uma hora e uma quota diária limitam o número de caracteres que o Tradutor aceitará.</li></ul>|
| TraduçãoExcepção | O AppId ultrapassou a quota.| A identificação do pedido excedeu a quota horária ou diária.|

> [!NOTE]
> A quota ajustar-se-á para garantir a equidade entre todos os utilizadores do serviço.

**Ver exemplos de código no GitHub**
* [C#](https://github.com/MicrosoftTranslator/CustomTranslator-API-CSharp)
* [PHP](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-PHP)

## <a name="getusertranslations-method"></a>Método GetUserTranslations

Este método recupera as traduções que são criadas pelo utilizador. Fornece as traduções agrupadas pelo uriPrefix, de, para, utilizador, e minRating e maxRating request parâmetros.

**Syntax**

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
| appId | **Requerido** Se o cabeçalho de autorização for utilizado, deixe o campo appid vazio, especifique uma corda que contenha "Portador" + " + símbolo de acesso.|
| uriPrefix| **Opcional** Uma corda que contém prefixo de URI da tradução.|
| De| **Opcional** Uma cadeia que representa o código linguístico do texto de tradução.|
| para| **Opcional** Uma cadeia que representa o código linguístico para traduzir o texto em.|
| minRating| **Opcional** Um valor inteiro que representa a classificação mínima de qualidade para o texto traduzido. O valor válido é entre -10 e 10. O valor predefinido é 1.|
| maxRating| **Opcional** Um valor inteiro que representa a classificação máxima de qualidade para o texto traduzido. O valor válido é entre -10 e 10. O valor predefinido é 1.|
| utilizador| **É opcional. Uma corda que é usada para filtrar o resultado com base no autor da submissão**|
| categoria| **Opcional** Uma cadeia que contém a categoria ou domínio da tradução. Este parâmetro suporta apenas a opção padrão geral.|
| minDateUtc| **Opcional** A data de quando quiser recuperar as traduções. A data deve estar no formato UTC.|
| maxDateUtc| **Opcional** A data até quando quiser recuperar as traduções. A data deve estar no formato UTC.|
| saltar| **Opcional** O número de resultados que pretende ignorar numa página. Por exemplo, se quiser saltar as primeiras 20 linhas dos resultados e ver a partir do 21º resultado, especifique 20 para este parâmetro. O valor predefinido para este parâmetro é 0.|
| take| **Opcional** O número de resultados que quer recuperar. O número máximo de cada pedido é de 100. O padrão é 50.|

> [!NOTE]
> Os parâmetros de solicitação de salto e de tomada permitem a paginação para um grande número de registos de resultados.

**Valor de retorno**

O conjunto de resultados contém a matriz da Transferência de **Utilização do Utilizador**. Cada UtilizadorTranslation tem os seguintes elementos:

| Campo | Descrição |
|:---|:---|
| CreatedDateutc| A data de criação da entrada utilizando a AddTranslation()|
| De| A língua de origem|
| Texto Original| O texto linguístico de origem que é usado ao submeter o pedido|
|Classificação |A classificação que é aplicada pelo apresentador na chamada de método AddTranslation()|
|Para|    A linguagem-alvo|
|Texto traduzido|    A tradução apresentada na chamada de método AddTranslation()|
|Uri|   O URI aplicado na chamada de método AddTranslation()|
|Utilizador   |O nome de utilizador|

**Exceções**

| Exceção | Mensagem | Condições |
|:---|:---|:---|
| ArgumentOutOfRangeException | O parâmetro '**maxDateUtc**' deve ser maior ou igual a '**minDateUtc**'.| O valor do parâmetro **maxDateUtc** é inferior ao valor do parâmetro **minDateUtc**.|
| TraduçãoExcepção | O IP está acima da quota.| <ul><li>O limite para o número de pedidos por minuto é atingido.</li><li>O tamanho do pedido permanece limitado em 10000 caracteres.</li><li>Uma hora e uma quota diária limitam o número de caracteres que o Tradutor aceitará.</li></ul>|
| TraduçãoExcepção | O AppId ultrapassou a quota.| A identificação do pedido excedeu a quota horária ou diária.|

> [!NOTE]
> A quota ajustar-se-á para garantir a equidade entre todos os utilizadores do serviço.

**Ver exemplos de código no GitHib**
* [C#](https://github.com/MicrosoftTranslator/CustomTranslator-API-CSharp)
* [PHP](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-PHP)
