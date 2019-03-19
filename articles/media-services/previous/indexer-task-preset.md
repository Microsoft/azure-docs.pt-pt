---
title: Tarefa de configuração predefinida para o indexador de multimédia do Azure
description: Este tópico apresenta uma visão geral das tarefas de configuração predefinida para o indexador de multimédia do Azure.
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
ms.author: adsolank;juliako;
ms.openlocfilehash: 8ce3ea3847e4c8c022f17375676d8415372dec85
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57991640"
---
# <a name="task-preset-for-azure-media-indexer"></a>Tarefa de configuração predefinida para o indexador de multimédia do Azure 

O indexador de multimédia do Azure é um processador de multimédia que utilizar para efetuar as seguintes tarefas: tornar os arquivos de mídia e conteúdo pesquisável, gerar faixas de legenda codificadas fechadas e palavras-chave, indexar os arquivos de recurso que fazem parte do seu elemento.

Este tópico descreve a tarefa de configuração predefinida que tem de passar para a tarefa de indexação. Para obter um exemplo completo, consulte [indexe ficheiros multimédia com o indexador de multimédia do Azure](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>XML de configuração do indexador de multimédia do Azure

A tabela seguinte explica os elementos e atributos da configuração do XML.

|Name|Requerer|Descrição|
|---|---|---|
|Input|true|Ficheiros de recurso que deseja indexar.<br/>O indexador de multimédia do Azure suporta os seguintes formatos de arquivo de mídia: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Pode especificar o nome de ficheiro (s) no **name** ou **lista** atributo do **entrada** elemento (conforme mostrado abaixo). Se não especificar qual arquivo de recurso para o índice, o ficheiro principal é escolhido. Não se for definido nenhum ficheiro de ativo primário, o primeiro arquivo no recurso de entrada é indexado.<br/><br/>Para especificar explicitamente o nome do arquivo de recurso, fazer:<br/>```<input name="TestFile.wmv" />```<br/><br/>Também pode indexar vários ficheiros de elemento ao mesmo tempo (até 10 ficheiros). Para efetuar este procedimento:<br/>-Criar um arquivo de texto (ficheiro de manifesto) e dê a ele uma extensão de .lst.<br/>-Adicione uma lista de todos os nomes de arquivo de recurso em seu elemento de entrada para este ficheiro de manifesto.<br/>-Adicione o ficheiro de manifesto (carregamento) ao elemento.<br/>-Especifique o nome do ficheiro de manifesto no atributo de lista da entrada.<br/>```<input list="input.lst">```<br/><br/>**Nota:** Se adicionar mais do que 10 ficheiros para o arquivo de manifesto, a tarefa de indexação irá falhar com o código de erro de 2006.|
|do IdP|false|Metadados para o ficheiro de ativo especificado (s).<br/>```<metadata key="..." value="..." />```<br/><br/>Pode fornecer valores para chaves predefinidas. <br/><br/>Atualmente, são suportadas as seguintes chaves:<br/><br/>**título** e **Descrição** - utilizado para atualizar o modelo de idioma para melhorar a precisão de reconhecimento de voz.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**nome de utilizador** e **palavra-passe** - utilizado para autenticação quando o download de arquivos de internet através de http ou https.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>Os valores de nome de utilizador e palavra-passe se aplicam a todos os URLs de multimédia no manifesto de entrada.|
|elástica<br/><br/>Adicionado na versão 1.2. Atualmente, a única funcionalidade suportada é o reconhecimento de fala ("ASR").|false|A funcionalidade de reconhecimento de voz tem as seguintes chaves de definições:<br/><br/>Idioma:<br/>-A linguagem natural a ser reconhecida no ficheiro multimédia.<br/>-Inglês, espanhol<br/><br/>CaptionFormats:<br/>-uma lista delimitada por vírgulas de legenda a saída desejada formatos (se houver)<br/>- ttml;sami;webvtt<br/><br/><br/>GenerateAIB:<br/>-Um sinalizador booleano que especifica se é ou não um ficheiro AIB necessário (para utilização com o SQL Server e o cliente IFilter indexador). Para obter mais informações, consulte usando arquivos de AIB com o indexador de multimédia do Azure e o SQL Server.<br/>-True; FALSO<br/><br/>GenerateKeywords:<br/>-Um sinalizador booleano que especifica se é ou não é necessário um ficheiro XML de palavra-chave.<br/>-True; FALSO.|

## <a name="azure-media-indexer-configuration-xml-example"></a>Exemplo do XML de configuração de indexador de multimédia do Azure

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

Ver [indexe ficheiros multimédia com o indexador de multimédia do Azure](media-services-index-content.md).

