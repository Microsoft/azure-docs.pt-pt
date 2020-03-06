---
title: Áudio comprimido de código stream com o SDK de Fala no Android
titleSuffix: Azure Cognitive Services
description: Saiba como transmitir áudio comprimido para o serviço De Discurso com o Speech SDK no Android.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 1539ca9aa18892f617f278e67c8b6141f5f6d880
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331132"
---
# <a name="how-to-use-codec-compressed-audio-input-with-the-speech-sdk-on-android"></a>Como: Utilizar a entrada de áudio comprimido codec com o SDK de Fala no Android

A API de entrada de **áudio comprimido** do Speech SDK fornece uma forma de transmitir áudio comprimido para o serviço de Fala usando pullStream ou PushStream.

> [!IMPORTANT]
> O streaming de áudio de entrada comprimido é atualmente suportado para [ C++, C#e Java em Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8)](how-to-use-codec-compressed-audio-input-streams.md). Também é suportado para Java em Android e [Objective-C na plataforma iOS.](how-to-use-codec-compressed-audio-input-streams-ios.md)
> É necessária a versão 1.7.0 ou superior do Discurso SDK.

Para acenar/PCM consulte a documentação da fala principal. Fora do wav/PCM, são suportados os seguintes formatos de entrada comprimidos codec:

- MP3
- OPUS/OGG
- FLAC
- ALAW em recipiente de awav
- MULAW em recipiente de awav

## <a name="prerequisites-to-using-codec-compressed-audio-input-on-android"></a>Pré-requisitos para a utilização de entrada de áudio comprimido codec no Android

O áudio comprimido codec é implementado utilizando [gStreamer](https://gstreamer.freedesktop.org). Por razões de licenciamento, os binários Gstreamer não são compilados com o SDK. Terá de usar os binários pré-construídos para Android. Para descarregar as bibliotecas pré-construídas, consulte [Instalação para Desenvolvimento Android](https://gstreamer.freedesktop.org/documentation/installing/for-android-development.html?gi-language=c).

`libgstreamer_android.so` é necessário. Certifique-se de que os seus plugins GStreamer estão ligados em `libgstreamer_android.so`.

```make
GSTREAMER_PLUGINS := coreelements app audioconvert mpg123 audioresample audioparsers ogg opusparse opus wavparse alaw mulaw flac
```

Um exemplo `Android.mk` e `Application.mk` ficheiro são fornecidos abaixo. Siga estes passos para criar o objeto partilhado gstreamer: `libgstreamer_android.so`.

```make
# Android.mk
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)

LOCAL_MODULE    := dummy
LOCAL_SHARED_LIBRARIES := gstreamer_android
LOCAL_LDLIBS := -llog
include $(BUILD_SHARED_LIBRARY)


ifndef GSTREAMER_ROOT_ANDROID
$(error GSTREAMER_ROOT_ANDROID is not defined!)
endif

ifndef APP_BUILD_SCRIPT
$(error APP_BUILD_SCRIPT is not defined!)
endif

ifndef TARGET_ARCH_ABI
$(error TARGET_ARCH_ABI is not defined!)
endif

ifeq ($(TARGET_ARCH_ABI),armeabi)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm
else ifeq ($(TARGET_ARCH_ABI),armeabi-v7a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/armv7
else ifeq ($(TARGET_ARCH_ABI),arm64-v8a)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/arm64
else ifeq ($(TARGET_ARCH_ABI),x86)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86
else ifeq ($(TARGET_ARCH_ABI),x86_64)
GSTREAMER_ROOT        := $(GSTREAMER_ROOT_ANDROID)/x86_64
else
$(error Target arch ABI not supported: $(TARGET_ARCH_ABI))
endif

GSTREAMER_NDK_BUILD_PATH  := $(GSTREAMER_ROOT)/share/gst-android/ndk-build/
include $(GSTREAMER_NDK_BUILD_PATH)/plugins.mk
GSTREAMER_PLUGINS         :=  coreelements app audioconvert mpg123 audioresample audioparsers ogg opusparse opus wavparse alaw mulaw flac
GSTREAMER_EXTRA_LIBS      := -liconv
include $(GSTREAMER_NDK_BUILD_PATH)/gstreamer-1.0.mk
```

```make
# Application.mk
APP_STL = c++_shared
APP_PLATFORM = android-21
APP_BUILD_SCRIPT = Android.mk
```

Pode construir `libgstreamer_android.so` utilizando o seguinte comando em Ubuntu 16.04 ou 18.04. As seguintes linhas de comando foram testadas apenas para a [versão 1.14.4](https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2) do Android Android com [o Android NDK b16b.](https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip)

```sh
# assuming wget and unzip already installed on the system
mkdir buildLibGstreamer
cd buildLibGstreamer
wget https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip
unzip -q -o android-ndk-r16b-linux-x86_64.zip
export PATH=$PATH:$(pwd)/android-ndk-r16b
export NDK_PROJECT_PATH=$(pwd)/android-ndk-r16b
wget https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2
mkdir gstreamer_android
tar -xjf gstreamer-1.0-android-universal-1.14.4.tar.bz2 -C $(pwd)/gstreamer_android/
export GSTREAMER_ROOT_ANDROID=$(pwd)/gstreamer_android

mkdir gstreamer
# Copy the Application.mk and Android.mk from the documentation above and put it inside $(pwd)/gstreamer

# Enable only one of the following at one time to create the shared object for the targeted ABI
echo "building for armeabi-v7a. libgstreamer_android.so will be placed in $(pwd)/armeabi-v7a"
ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=armeabi-v7a NDK_LIBS_OUT=$(pwd)

#echo "building for arm64-v8a. libgstreamer_android.so will be placed in $(pwd)/arm64-v8a"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=arm64-v8a NDK_LIBS_OUT=$(pwd)

#echo "building for x86_64. libgstreamer_android.so will be placed in $(pwd)/x86_64"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86_64 NDK_LIBS_OUT=$(pwd)

#echo "building for x86. libgstreamer_android.so will be placed in $(pwd)/x86"
#ndk-build -C $(pwd)/gstreamer "NDK_APPLICATION_MK=Application.mk" APP_ABI=x86 NDK_LIBS_OUT=$(pwd)
```

Uma vez que o objeto partilhado (libgstreamer_android.so) é construído, o desenvolvedor de aplicações precisa colocar o objeto partilhado na aplicação Android, para que possa ser carregado por sdk de fala.

## <a name="example-code-using-codec-compressed-audio-input"></a>Código de exemplo utilizando entrada de áudio comprimido codec

Para transmitir num formato áudio comprimido para o serviço De Fala, crie `PullAudioInputStream` ou `PushAudioInputStream`. Em seguida, crie uma `AudioConfig` a partir de uma instância da sua classe de fluxo, especificando o formato de compressão do fluxo.

Vamos supor que tem uma classe de fluxo de entrada chamada `myPullStream` e está usando OPUS/OGG. O seu código pode parecer assim:

```java
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import com.microsoft.cognitiveservices.speech.audio.AudioInputStream;
import com.microsoft.cognitiveservices.speech.audio.AudioStreamFormat;
import com.microsoft.cognitiveservices.speech.audio.PullAudioInputStream;
import com.microsoft.cognitiveservices.speech.internal.AudioStreamContainerFormat;

SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
AudioStreamFormat audioFormat = AudioStreamFormat.getCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
AudioConfig audioConfig = AudioConfig.fromStreamInput(myPullStream, audioFormat);

SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);

SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get()

String text = result.getText();
```

## <a name="next-steps"></a>Passos seguintes

- [Obter a subscrição de avaliação de Voz](https://azure.microsoft.com/try/cognitive-services/)
- [Veja como reconhecer o discurso em Java](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
