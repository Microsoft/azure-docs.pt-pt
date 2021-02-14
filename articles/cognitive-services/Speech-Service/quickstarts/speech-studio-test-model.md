---
title: Teste um modelo usando ficheiros de áudio - Speech Studio
titleSuffix: Azure Cognitive Services
description: Neste como fazer, você usa o Speech Studio para testar o reconhecimento da fala num ficheiro áudio.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 02/12/2021
ms.author: v-demjoh
ms.openlocfilehash: 93a0c171497faa9e87457a03da0b78003feaa533
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362272"
---
# <a name="test-a-model-using-an-audio-file-in-speech-studio"></a>Teste um modelo usando um arquivo áudio no Speech Studio

Neste como fazer, você usa o Speech Studio para converter o discurso de um ficheiro áudio para texto. O Speech Studio permite-lhe testar, comparar, melhorar e implementar modelos de reconhecimento de voz utilizando texto relacionado, áudio com transcrições com rótulo humano e orientação de pronúncia que fornece.

## <a name="prerequisites"></a>Pré-requisitos

Antes de utilizar o Speech Studio, [siga estas instruções para criar uma conta Azure e subscreva o serviço Speech](../custom-speech-overview.md#set-up-your-azure-account). Esta subscrição unificada dá-lhe acesso a discurso-a-texto, texto-a-discurso, tradução de fala e estúdio de fala.

## <a name="download-an-audio-file"></a>Descarregue um ficheiro áudio

Siga estes passos para descarregar um ficheiro áudio que contenha a fala e embale-o num ficheiro zip.

1. Descarregue o **[ficheiro de wav de amostra a partir deste link](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav)** clicando à direita no link e selecionando Save link **como**. Clique **em Guardar** para descarregar o `whatstheweatherlike.wav` ficheiro.
2. Utilizando um explorador de ficheiros ou uma janela terminal com uma ferramenta zip, crie um ficheiro zip nomeado `whatstheweatherlike.zip` que contenha o `whatstheweatherlike.wav` ficheiro que descarregou. No Windows, pode abrir o Windows Explorer, navegar para a `Downloads` pasta, clique com o botão `whatstheweatherliike.wav` direito, clicar **em Enviar para**, clicar na pasta **Comprimida (com fecho)** e pressionar para entrar para aceitar o nome de ficheiro predefinido.

## <a name="create-a-project-in-the-speech-studio"></a>Criar um projeto no Estúdio da Fala

Siga estes passos para criar um projeto que contenha o seu fecho de um ficheiro áudio.

1. Abra [o Estúdio de Discurso](https://speech.microsoft.com/), e clique em Novo **projeto**. Digite um nome para este projeto e clique em **Criar**. O seu projeto aparece na lista de Discursos Personalizados.
2. Clique no nome do seu projeto. No separador Dados, clique em **Enviar dados.**
3. O tipo de dados de fala predefinido **apenas** para Áudio , por isso clique em **Seguinte**.
4. Nomeie o seu novo conjunto de dados de `MyZipOfAudio` fala e clique em **Seguinte**.
5. Clique **em Navegar nos ficheiros...**, navegue para o seu ficheiro e clique em `whatstheweatherlike.zip` **Abrir**.
6. Clique no botão **Carregar**. O navegador envia o seu ficheiro zip para o Speech Studio e o Speech Studio processa os conteúdos.

## <a name="test-a-model"></a>Testar um modelo

Depois de o Speech Studio processar o conteúdo do seu ficheiro zip, pode reproduzir o áudio de origem enquanto examina a transcrição para procurar erros ou omissões. Siga estes passos para examinar a qualidade da transcrição no navegador.

1. Clique no **separador Teste** e clique em **Adicionar teste**.
2. Neste teste, estamos a inspecionar a qualidade dos dados apenas áudio, por isso clique **em Seguinte** para aceitar este tipo de teste.
3. Nomeie este teste `MyModelTest` e clique em **Seguinte**.
4. Clique no botão de rádio à esquerda de `MyZipOfAudio` , e clique em **Seguinte**.
5. O **Modelo 1** desres padrão para o modelo de reconhecimento mais recente, por isso clique em **Criar**. Após o processamento do conteúdo do seu conjunto de dados áudio, o estado do teste mudará para **Sucesso**.
6. Clique **no MyModelTest**. Os resultados do reconhecimento da fala aparecem. Clique no triângulo de apontamento direito dentro do círculo para ouvir o áudio e compare o que ouve com o texto pelo círculo.

## <a name="download-detailed-results"></a>Baixar resultados detalhados

Pode descarregar ficheiros que descrevam transcrições em detalhes muito maiores. Os ficheiros incluem forma lexical de fala nos seus ficheiros áudio e ficheiros JSON que contêm detalhes de confiança compensação, duração e transcrição sobre cada palavra. Siga estes passos para ver estes ficheiros.

1. Clique em **Transferir**.
2. No diálogo de Descarregamento, desescolh **o áudio** e clique em **Baixar**.
3. Desaperte o ficheiro zip descarregado e examine os ficheiros extraídos.

## <a name="next-steps"></a>Passos seguintes

Saiba melhorar a precisão do reconhecimento da fala [treinando um modelo personalizado.](../how-to-custom-speech-test-and-train.md)