---
title: Predefinição de tarefa sintetizador a Azure Media Indexer
description: Este tópico apresenta uma visão geral da predefinição de tarefa para o Azure Media Indexer.
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
ms.openlocfilehash: 29753759af341f82429f12b6710ae9c32dcb4103
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74896031"
---
# <a name="task-preset-for-azure-media-indexer"></a>Predefinição de tarefa sintetizador a Azure Media Indexer 

O Azure Media Indexer é um Processador de Media que utiliza para executar as seguintes tarefas: tornar os ficheiros de mídia e conteúdos pesquisáveis, gerar faixas de legendas fechadas e palavras-chave, ficheiros de ativos de índice que fazem parte do seu ativo.

Este tópico descreve o predefinido de tarefa que precisa de passar para o seu trabalho de indexação. Por exemplo completo, consulte [ficheiros de media indexantes com o Indexante](media-services-index-content.md)dos Media Azure .

## <a name="azure-media-indexer-configuration-xml"></a>Configuração do Indexante Azure Media XML

A tabela que se segue explica elementos e atributos da configuração XML.

|Nome|Requerer|Descrição|
|---|---|---|
|Input|true|Ficheiros de ativos que pretende indexar.<br/>O Azure Media Indexer suporta os seguintes formatos de ficheiros de mídia: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Pode especificar o nome (s) do ficheiro no **nome** ou atributo da **lista** do elemento de **entrada** (como mostrado abaixo). Se não especificar qual o ficheiro do ativo a indexar, o ficheiro principal é escolhido. Se nenhum ficheiro de ativo primário for definido, o primeiro ficheiro do ativo de entrada está indexado.<br/><br/>Para especificar explicitamente o nome do ficheiro do ativo, faça:<br/>```<input name="TestFile.wmv" />```<br/><br/>Também pode indexar vários ficheiros de ativos ao mesmo tempo (até 10 ficheiros). Para efetuar este procedimento:<br/>- Criar um ficheiro de texto (ficheiro manifesto) e dar-lhe uma extensão .lst.<br/>- Adicione uma lista de todos os nomes de ficheiros de ativos no seu ativo de entrada a este ficheiro manifesto.<br/>- Adicione (upload) o ficheiro manifesto ao ativo.<br/>- Especificar o nome do ficheiro manifesto no atributo da lista de entrada.<br/>```<input list="input.lst">```<br/><br/>**Nota:** Se adicionar mais de 10 ficheiros ao ficheiro manifesto, o trabalho de indexação falhará com o código de erro de 2006.|
|do IdP|false|Metadados para os ficheiros de ativos especificados.<br/>```<metadata key="..." value="..." />```<br/><br/>Pode fornecer valores para chaves pré-definidas. <br/><br/>Atualmente, são suportadas as seguintes teclas:<br/><br/>**título** e **descrição** - usado para atualizar o modelo de linguagem para melhorar a precisão do reconhecimento da fala.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**username** e **password** - usado para autenticação ao descarregar ficheiros de internet via http ou https.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>O nome de utilizador e os valores de palavra-passe aplicam-se a todos os URLs de mídia no manifesto de entrada.|
|funcionalidades<br/><br/>Adicionado na versão 1.2. Atualmente, a única característica apoiada é o reconhecimento da fala ("ASR").|false|A função de Reconhecimento de Voz tem as seguintes teclas de definições:<br/><br/>Idioma:<br/>- A linguagem natural a ser reconhecida no ficheiro multimédia.<br/>- Inglês, Espanhol<br/><br/>Formatos de legendas:<br/>- uma lista separada do ponto evígula dos formatos de legenda de saída desejados (se houver)<br/>- ttml;webvtt<br/><br/><br/>GeraçãoPalavras Chave:<br/>- É necessária ou não uma bandeira booleana que especifique se é ou não necessário um ficheiro XML de palavra-chave.<br/>- Verdade; Falso, falso.|

## <a name="azure-media-indexer-configuration-xml-example"></a>Exemplo de XML de configuração do Indexer Azure Media

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

Consulte [ficheiros de media indexantes com o Indexante](media-services-index-content.md)dos Media Azure .

