---
title: Registo SDK de fala - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como ativar o login no SDK do discurso (C++, C#, Python, Objective-C, Java).
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: f044596cf084e68b6c9ca10e3da4fbdf4c8c2062
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026527"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Ativar o registo no SDK de discurso

Registar para arquivar é uma característica opcional para o SDK de discurso. Durante o registo de desenvolvimento fornece informações e diagnósticos adicionais dos componentes principais da SDK do Discurso. Pode ser ativado definindo a propriedade `Speech_LogFilename` num objeto de configuração de fala para a localização e nome do ficheiro de registo. O registo será ativado globalmente assim que um reconhecimento for criado a partir dessa configuração e não possa ser desativado posteriormente. Não é possível alterar o nome de um ficheiro de registo durante uma sessão de registo em execução.

> [!NOTE]
> O registo está disponível desde a versão 1.4.0 do Speech SDK em todas as linguagens de programação do Speech SDK suportadas, com exceção do JavaScript.

## <a name="sample"></a>Sample

O nome do ficheiro de registo é especificado num objeto de configuração. Tomando o `SpeechConfig` exemplo e assumindo que criou um caso chamado `config` :

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

Pode criar um reconhecimento a partir do objeto config. Isto permitirá o registo de todos os reconhecedores.

> [!NOTE]
> Se criar um `SpeechSynthesizer` objeto config, não ativará o registo. No entanto, se o registo madeireira estiver ativado, também receberá diagnósticos a partir do `SpeechSynthesizer` .

## <a name="create-a-log-file-on-different-platforms"></a>Criar um ficheiro de registo em diferentes plataformas

Para Windows ou Linux, o ficheiro de registo pode estar em qualquer caminho para o qual o utilizador tenha permissão de escrita. Escrever permissões para arquivar localizações do sistema em outros sistemas operativos pode ser limitado ou restringido por padrão.

### <a name="universal-windows-platform-uwp"></a>Plataforma Universal do Windows (UWP)

As aplicações UWP precisam de ser coloca ficheiros de registo num dos locais de dados da aplicação (local, roaming ou temporário). Um ficheiro de registo pode ser criado na pasta de aplicação local:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Mais sobre a permissão de acesso a ficheiros para aplicações UWP está disponível [aqui.](/windows/uwp/files/file-access-permissions)

### <a name="android"></a>Android

Pode guardar um ficheiro de registo para armazenamento interno, armazenamento externo ou diretório de cache. Os ficheiros criados no armazenamento interno ou no diretório de cache são privados para a aplicação. É preferível criar um ficheiro de registo no armazenamento externo.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

O código acima irá guardar um ficheiro de registo para o armazenamento externo na raiz de um diretório específico da aplicação. Um utilizador pode aceder ao ficheiro com o gestor de ficheiros (normalmente em `Android/data/ApplicationName/logfile.txt` ). O ficheiro será eliminado quando o pedido for desinstalado.

Também precisa de solicitar `WRITE_EXTERNAL_STORAGE` permissão no ficheiro manifesto:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Mais sobre dados e armazenamento de ficheiros para aplicações Android está disponível [aqui.](https://developer.android.com/guide/topics/data/data-storage.html)

#### <a name="ios"></a>iOS

Apenas os diretórios dentro da caixa de areia da aplicação estão acessíveis. Os ficheiros podem ser criados nos documentos, bibliotecas e diretórios temporários. Os ficheiros no diretório de documentos podem ser disponibilizados a um utilizador. O seguinte código snippet mostra a criação de um ficheiro de registo no diretório de documentos de aplicação:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Para aceder a um ficheiro criado, adicione as propriedades abaixo à `Info.plist` lista de propriedades da aplicação:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Mais sobre o sistema de ficheiros iOS está disponível [aqui.](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore as nossas amostras no GitHub](https://aka.ms/csspeech/samples)