---
title: Predefinição de tarefa para Azure Media Indexer
description: Este tópico fornece uma visão geral da predefinição de tarefa para Azure Media Indexer.
services: media-services
documentationcenter: ''
author: Asolanki
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: a9a47f970f0f934e0953bd5e2d6e5575758a9c1c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67873505"
---
# <a name="task-preset-for-azure-media-indexer"></a>Predefinição de tarefa para Azure Media Indexer 

Azure Media Indexer é um processador de mídia que você usa para executar as seguintes tarefas: tornar os arquivos de mídia e o conteúdo pesquisáveis, gerar faixas e palavras-chave com legendas codificadas, indexar arquivos de ativo que fazem parte do seu ativo.

Este tópico descreve a predefinição de tarefa que você precisa passar para o trabalho de indexação. Para obter um exemplo completo, consulte [indexando arquivos de mídia com Azure Media indexer](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>Azure Media Indexer XML de configuração

A tabela a seguir explica os elementos e atributos do XML de configuração.

|Nome|Requerer|Descrição|
|---|---|---|
|Input|true|Arquivo (s) de ativo que você deseja indexar.<br/>O Azure Media Indexer dá suporte aos seguintes formatos de arquivo de mídia: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Você pode especificar os nomes de arquivo no atributo **Name** ou **list** do elemento **Input** (como mostrado abaixo). Se você não especificar qual arquivo de ativo indexar, o arquivo primário será escolhido. Se nenhum arquivo de ativo principal for definido, o primeiro arquivo no ativo de entrada será indexado.<br/><br/>Para especificar explicitamente o nome do arquivo de ativo, faça:<br/>```<input name="TestFile.wmv" />```<br/><br/>Você também pode indexar vários arquivos de ativo de uma vez (até 10 arquivos). Para efetuar este procedimento:<br/>-Crie um arquivo de texto (arquivo de manifesto) e dê a ele uma extensão. lst.<br/>-Adicione uma lista de todos os nomes de arquivo de ativo em seu ativo de entrada para esse arquivo de manifesto.<br/>-Adicionar (carregar) o arquivo de manifesto ao ativo.<br/>-Especifique o nome do arquivo de manifesto no atributo de lista da entrada.<br/>```<input list="input.lst">```<br/><br/>**Nota:** Se você adicionar mais de 10 arquivos ao arquivo de manifesto, o trabalho de indexação falhará com o código de erro 2006.|
|do IdP|false|Metadados para os arquivos de ativo especificados.<br/>```<metadata key="..." value="..." />```<br/><br/>Você pode fornecer valores para chaves predefinidas. <br/><br/>No momento, há suporte para as seguintes chaves:<br/><br/>**título** e **Descrição** – usado para atualizar o modelo de idioma para melhorar a precisão do reconhecimento de fala.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**nome de usuário** e **senha** – usados para autenticação ao baixar arquivos da Internet via http ou HTTPS.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>Os valores de nome de usuário e senha se aplicam a todas as URLs de mídia no manifesto de entrada.|
|elástica<br/><br/>Adicionado na versão 1,2. Atualmente, o único recurso com suporte é o reconhecimento de fala ("ASR").|false|O recurso de reconhecimento de fala tem as seguintes chaves de configuração:<br/><br/>Idioma:<br/>-O idioma natural a ser reconhecido no arquivo multimídia.<br/>-Inglês, espanhol<br/><br/>CaptionFormats:<br/>-uma lista separada por ponto-e-vírgula dos formatos de legenda de saída desejados (se houver)<br/>- ttml;sami;webvtt<br/><br/><br/>GenerateAIB:<br/>-Um sinalizador booliano que especifica se um arquivo AIB é necessário (para uso com SQL Server e o IFilter do indexador do cliente). Para obter mais informações, consulte usando arquivos AIB com Azure Media Indexer e SQL Server.<br/>True For<br/><br/>GenerateKeywords:<br/>-Um sinalizador booliano que especifica se um arquivo XML de palavra-chave é necessário ou não.<br/>True For.|

## <a name="azure-media-indexer-configuration-xml-example"></a>Exemplo de XML de configuração Azure Media Indexer

``` 
<?xml version="1.0" encoding="utf-8"?>  
<configuration version="2.0">  
  <input>  
    <metadata key="title" value="[Title of the media file]" />  
    <metadata key="description" value="[Description of the media file]" />  
  </input>  
  <settings>  
  </settings>  
  
  <features>  
    <feature name="ASR">    
      <settings>  
        <add key="Language" value="English"/>  
        <add key="CaptionFormats" value="ttml;sami;webvtt"/>  
        <add key="GenerateAIB" value ="true" />  
        <add key="GenerateKeywords" value ="true" />  
      </settings>  
    </feature>  
  </features>  
  
</configuration>  
```
  
## <a name="next-steps"></a>Passos Seguintes

Consulte [indexando arquivos de mídia com Azure Media indexer](media-services-index-content.md).

