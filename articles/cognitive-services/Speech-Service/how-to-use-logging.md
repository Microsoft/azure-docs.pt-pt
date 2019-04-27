---
title: Registo do SDK de voz - serviços de voz
titleSuffix: Azure Cognitive Services
description: Ative o registo no SDK de voz.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: amishu
ms.openlocfilehash: 75eaea22c4809eda78e54514961d13113b4a5f3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60696856"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Ativar o registo no SDK de voz

Registar ficheiro é uma funcionalidade opcional para o SDK de voz. Durante o desenvolvimento registo fornece informações adicionais e de diagnóstico a partir de componentes do núcleo do SDK Speeck. Pode ser ativada, definindo a propriedade `Speech_LogFilename` num objeto de configuração de voz para a localização e o nome do ficheiro de registo. Registo será ativado globalmente assim que um reconhecedor é criado a partir de que a configuração e não pode ser desabilitado posteriormente. Não é possível alterar o nome de um ficheiro de registo durante uma execução de iniciar sessão.

> [!NOTE]
> O registo está disponível no SDK de voz suportadas todas as linguagens de programação, com exceção de JavaScript.

## <a name="sample"></a>Exemplo

O nome de ficheiro de registo é especificado num objeto de configuração. Colocar o `SpeechConfig` como exemplo e partindo do princípio de que criou uma instância chamado `config`:

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

Pode criar um reconhecedor do objeto de configuração. Isto irá ativar o registo de todos os reconhecedores.

> [!NOTE]
> Se criar um `SpeechSynthesizer` do objeto de configuração, ele será não ative o registo. Se o registo está ativado no entanto, também receberá diagnóstico a partir do `SpeechSynthesizer`.

## <a name="create-a-log-file-on-different-platforms"></a>Criar um ficheiro de registo em plataformas diferentes

Para Windows ou Linux, o ficheiro de registo pode estar em qualquer caminho, que o utilizador tem permissão de escrita de. Permissões de escrita para localizações de sistema de ficheiros em outros sistemas operacionais, podem estar limitadas ou restritos por padrão.

### <a name="universal-windows-platform-uwp"></a>Plataforma Universal do Windows (UWP)

Aplicações do UWP tem de ser locais ficheiros de registo das localizações de dados de aplicação (locais, roaming ou temporárias). Um arquivo de log pode ser criado na pasta local do aplicativo:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Mais informações sobre o acesso de arquivo está disponível permissão para aplicações UWP [aqui](https://docs.microsoft.com/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

Pode salvar um arquivo de log para armazenamento interno, armazenamento externo ou o diretório de cache. Ficheiros criados no armazenamento interno ou o diretório de cache são particulares ao aplicativo. É preferível para criar um ficheiro de registo no armazenamento externo.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

O código acima irá guardar um ficheiro de registo para o armazenamento externo na raiz de um diretório específico do aplicativo. Um utilizador pode aceder ao ficheiro com o Gestor de ficheiros (habitualmente em `Android/data/ApplicationName/logfile.txt`). O ficheiro será eliminado quando o aplicativo é desinstalado.

Também tem de pedir `WRITE_EXTERNAL_STORAGE` permissão no arquivo de manifesto:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Mais sobre dados e ficheiro de armazenamento para aplicações Android está disponível [aqui](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Apenas os diretórios dentro da área de segurança de aplicação estão acessíveis. Ficheiros podem ser criados nos documentos, biblioteca e diretórios temporários. Ficheiros no diretório de documentos podem ser disponibilizados a um utilizador. O fragmento de código seguinte mostra a criação de um arquivo de log no diretório de documento da aplicação:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Para aceder a um ficheiro criado, adicione o abaixo as propriedades para o `Info.plist` lista de propriedades da aplicação:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Mais sobre iOS sistema de ficheiros está disponível [aqui](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explore os nossos exemplos no GitHub](https://aka.ms/csspeech/samples)

