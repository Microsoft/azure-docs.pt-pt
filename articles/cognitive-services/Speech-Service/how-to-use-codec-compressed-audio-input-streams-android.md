---
title: Áudio compactado do codec de fluxo com o SDK de fala no Android
titleSuffix: Azure Cognitive Services
description: Saiba como transmitir áudio compactado para os serviços de fala do Azure com o SDK de fala no Android.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 76a4b010dd54a9cc6224432adf615814520ef4fd
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109899"
---
# <a name="how-to-use-codec-compressed-audio-input-with-the-speech-sdk-on-android"></a>Como usar a entrada de áudio compactado por codec com o SDK de fala no Android

A API de **fluxo de entrada de áudio compactada** do SDK de fala fornece uma maneira de transmitir áudio compactado para o serviço de fala usando PullStream ou PushStream.

> [!IMPORTANT]
> O áudio de entrada compactado de streaming tem suporte no momento para [ C++o, C#o e o Java no Linux (Ubuntu 16, 4, Ubuntu 18, 4, Debian 9)](how-to-use-codec-compressed-audio-input-streams.md). Também há suporte para Java no Android e [no Objective-C na plataforma iOS](how-to-use-codec-compressed-audio-input-streams-ios.md) .
> O Speech SDK versão 1.7.0 ou superior é necessário.

Para wav/PCM, consulte a documentação de fala principal. Fora de wav/PCM, há suporte para os seguintes formatos de entrada compactados do Codec:

- MP3
- OPUS/OGG
- FLAC
- ALAW no contêiner WAV
- MULAW no contêiner WAV

## <a name="prerequisites-to-using-codec-compressed-audio-input-on-android"></a>Pré-requisitos para usar a entrada de áudio compactado por codec no Android

O codec de áudio compactado é implementado usando [GStreamer](https://gstreamer.freedesktop.org). Por motivos de licenciamento, os binários GStreamer não são compilados com o SDK. Você precisará usar os binários predefinidos para Android. Para baixar as bibliotecas predefinidas, consulte [Installing for Android Development](https://gstreamer.freedesktop.org/documentation/installing/for-android-development.html?gi-language=c).

`libgstreamer_android.so` é necessário. Verifique se os plug-ins GStreamer estão vinculados em `libgstreamer_android.so`.

```make
GSTREAMER_PLUGINS := coreelements app audioconvert mpg123 audioresample audioparsers ogg opusparse opus wavparse alaw mulaw flac
```

Um exemplo `Android.mk` e `Application.mk` arquivo são fornecidos abaixo. Siga estas etapas para criar o objeto compartilhado GStreamer: `libgstreamer_android.so`.

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

Você pode criar `libgstreamer_android.so` usando o comando a seguir no Ubuntu 16, 4 ou 18, 4. As linhas de comando a seguir foram testadas apenas para [GStreamer Android versão 1.14.4](https://gstreamer.freedesktop.org/data/pkg/android/1.14.4/gstreamer-1.0-android-universal-1.14.4.tar.bz2) com [Android NDK b16b.](https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip)

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

Depois que o objeto compartilhado (libgstreamer_android. so) é criado, o desenvolvedor do aplicativo precisa posicionar o objeto compartilhado no aplicativo do Android, para que ele possa ser carregado pelo SDK do Speech.

## <a name="example-code-using-codec-compressed-audio-input"></a>Código de exemplo usando a entrada de áudio compactado por codec

Para transmitir em um formato de áudio compactado para os serviços de fala, crie `PullAudioInputStream` ou `PushAudioInputStream`. Em seguida, crie um `AudioConfig` de uma instância de sua classe de fluxo, especificando o formato de compactação do fluxo.

Vamos supor que você tenha uma classe de fluxo de entrada chamada `myPullStream` e esteja usando OPUS/OGG. Seu código pode ter a seguinte aparência:

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
- [Veja como reconhecer a fala em Java](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
