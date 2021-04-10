---
title: Predefinição de tarefa para Azure Media Indexer
description: Este tópico apresenta uma visão geral da predefinição de tarefa para o Azure Media Services Media Indexer.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: c054c0aa8c58894f63f8ce8432e8d7a9e1275639
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012229"
---
# <a name="task-preset-for-azure-media-indexer"></a>Predefinição de tarefa para Azure Media Indexer

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

O Azure Media Indexer é um Processador de Media que utiliza para executar as seguintes tarefas: tornar os ficheiros de mídia e conteúdos pesjáveis, gerar faixas de legendas fechadas e palavras-chave, ficheiros de ativos de índice que fazem parte do seu ativo.

Este tópico descreve a predefinição de tarefa que precisa de passar para o seu trabalho de indexação. Por exemplo, consulte [os ficheiros de indexação dos meios de comunicação com o Azure Media Indexer](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>Configuração do Indexer Azure Media XML

A tabela seguinte explica elementos e atributos da configuração XML.

|Name|Requerer|Description|
|---|---|---|
|Entrada|true|Ficheiros de ativos que pretende indexar.<br/>O Azure Media Indexer suporta os seguintes formatos de ficheiros de mídia: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Pode especificar o (s) nome do ficheiro (s) no **nome** ou atributo da **lista** do elemento de **entrada** (como mostrado abaixo). Se não especificar qual o ficheiro de ativos a indexar, o ficheiro principal é escolhido. Se não for definido nenhum ficheiro de ativo primário, o primeiro ficheiro do ativo de entrada é indexado.<br/><br/>Para especificar explicitamente o nome do ficheiro do ativo, faça:<br/>```<input name="TestFile.wmv" />```<br/><br/>Também pode indexar vários ficheiros de ativos de uma só vez (até 10 ficheiros). Para efetuar este procedimento:<br/>- Crie um ficheiro de texto (ficheiro manifesto) e dê-lhe uma extensão .lst.<br/>- Adicione uma lista de todos os nomes de ficheiros do ativo no seu ativo de entrada a este ficheiro manifesto.<br/>- Adicione (carregar) o ficheiro manifesto ao ativo.<br/>- Especificar o nome do ficheiro manifesto no atributo da lista de entradas.<br/>```<input list="input.lst">```<br/><br/>**Nota:** Se adicionar mais de 10 ficheiros ao ficheiro manifesto, o trabalho de indexação falhará com o código de erro de 2006.|
|do IdP|false|Metadados para os ficheiros de ativos especificados.<br/>```<metadata key="..." value="..." />```<br/><br/>Pode fornecer valores para chaves predefinidas. <br/><br/>Atualmente, as seguintes teclas são suportadas:<br/><br/>**título** e **descrição** - usado para atualizar o modelo de linguagem para melhorar a precisão do reconhecimento da fala.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**username** e **password** - usado para autenticação ao descarregar ficheiros de internet através de http ou https.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>O nome de utilizador e os valores da palavra-passe aplicam-se a todos os URLs de mídia no manifesto de entrada.|
|funcionalidades<br/><br/>Adicionado na versão 1.2. Atualmente, a única característica suportada é o reconhecimento de voz ("ASR").|false|A função de Reconhecimento de Voz tem as seguintes teclas de definições:<br/><br/>Idioma:<br/>- A linguagem natural a reconhecer no ficheiro multimédia.<br/>- Inglês, Espanhol<br/><br/>CaptionFormats:<br/>- uma lista separada de pontos e vírgulas dos formatos de legenda de saída pretendidos (se houver)<br/>- ttml;webvtt<br/><br/><br/>Gerar Palavras-chave:<br/>- Uma bandeira booleana especificando se é ou não necessário um ficheiro XML de palavra-chave.<br/>- Verdade; Falso, falso.|

## <a name="azure-media-indexer-configuration-xml-example"></a>Azure Media Indexer configuração XML exemplo

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
        <add key="GenerateKeywords" value ="true" />  
      </settings>  
    </feature>  
  </features>  
  
</configuration>  
```
  
## <a name="next-steps"></a>Passos seguintes

Consulte [os ficheiros de indexação dos meios de comunicação com o Azure Media Indexer](media-services-index-content.md).

