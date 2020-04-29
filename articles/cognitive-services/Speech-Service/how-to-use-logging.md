---
title: Registo de Fala SDK - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como permitir a exploração madeireira no SDK da Fala (C++, C#, Python, Objective-C, Java).
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: 707a0f801a739a7a91cee19635e609305cd8f021
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74805795"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Ativar o registo no SDK da fala

Iniciar a sua vida por arquivar é uma característica opcional para o SDK do Discurso. Durante o desenvolvimento, o registo fornece informações e diagnósticos adicionais dos componentes principais do SDK da Fala. Pode ser ativado colocando `Speech_LogFilename` a propriedade num objeto de configuração da fala para a localização e nome do ficheiro de registo. O registo será ativado globalmente assim que um restente for criado a partir dessa configuração e não puder ser desativado depois. Não é possível alterar o nome de um ficheiro de registo durante uma sessão de registo em execução.

> [!NOTE]
> O registo está disponível desde a versão 1.4.0 do Speech SDK em todos os idiomas de programação sdk suportados, com exceção do JavaScript.

## <a name="sample"></a>Sample

O nome do ficheiro de registo é especificado num objeto de configuração. Tomando o `SpeechConfig` exemplo e assumindo que `config`criou uma instância chamada:

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

Pode criar um reconhecível a partir do objeto de config. Isto permitirá a exploração madeireira para todos os reconhecíveis.

> [!NOTE]
> Se criar `SpeechSynthesizer` um a partir do objeto de config, não permitirá a exploração madeireira. No entanto, se o registo estiver ativado, também receberá diagnósticos a `SpeechSynthesizer`partir do .

## <a name="create-a-log-file-on-different-platforms"></a>Criar um ficheiro de registo em diferentes plataformas

Para windows ou Linux, o ficheiro de registo pode estar em qualquer caminho para o qual o utilizador tenha permissão de escrita. Escrever permissões para registar localizações do sistema noutros sistemas operativos pode ser limitado ou restringido por padrão.

### <a name="universal-windows-platform-uwp"></a>Plataforma Universal do Windows (UWP)

As aplicações da UWP têm de ser colocações de ficheiros de registo num dos locais de dados da aplicação (local, roaming ou temporário). Um ficheiro de registo pode ser criado na pasta de aplicação local:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Mais sobre a permissão de acesso de ficheiros para aplicações UWP está disponível [aqui](https://docs.microsoft.com/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

Pode guardar um ficheiro de registo para armazenamento interno, armazenamento externo ou diretório de cache. Os ficheiros criados no armazenamento interno ou no diretório de cache são privados da aplicação. É preferível criar um ficheiro de registo no armazenamento externo.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

O código acima irá guardar um ficheiro de registo para o armazenamento externo na raiz de um diretório específico da aplicação. Um utilizador pode aceder ao ficheiro com `Android/data/ApplicationName/logfile.txt`o gestor de ficheiros (normalmente em ). O ficheiro será apagado quando a aplicação estiver desinstalada.

Também precisa de `WRITE_EXTERNAL_STORAGE` solicitar permissão no ficheiro manifesto:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Mais sobre dados e armazenamento de ficheiros para aplicações Android está disponível [aqui](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Apenas os diretórios dentro da caixa de areia de aplicação são acessíveis. Os ficheiros podem ser criados nos diretórios de documentos, bibliotecas e temporários. Os ficheiros no diretório de documentos podem ser disponibilizados a um utilizador. O seguinte código de snippet mostra a criação de um ficheiro de registo no diretório do documento de aplicação:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Para aceder a um ficheiro criado, `Info.plist` adicione os imóveis abaixo na lista de propriedades da aplicação:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Mais sobre o sistema de ficheiros iOS está disponível [aqui](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore as nossas amostras no GitHub](https://aka.ms/csspeech/samples)
