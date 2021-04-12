---
title: Encontre e redirecione rostos em Azure Media Services v3 API | Microsoft Docs
description: O Azure Media Services v3 fornece uma predefinição de deteção facial e redação (borrão) que lhe permite submeter um ficheiro de vídeo, detetar rostos e aplicar opcionalmente a redação (desfocagem) a eles num único passe combinado, ou através de uma operação de dois estágios que permite a edição. Este artigo demonstra como encontrar e redigir rostos com a predefinição do Detetor de Rosto na API v3.
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
ms.date: 03/25/2021
ms.author: johndeu
ms.custom: devx-track-csharp
ms.openlocfilehash: 4907a81fc8cb55499fa97f2b02a3e19e7117bbbc
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286390"
---
# <a name="find-and-redact-blur-faces-with-the-face-detector-preset"></a>Encontrar e redigir rostos (borrão) com a predefinição do Detetor facial

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Azure Media Services v3 API inclui uma predefinição do Detetor facial que oferece deteção facial escalável e redação (borrada) na nuvem. A redação facial permite-lhe modificar o seu vídeo de modo a desfocar rostos de indivíduos selecionados. Pode querer utilizar o serviço de redação facial em cenários de segurança pública e media de notícias. Alguns minutos de imagens que contêm múltiplas faces podem demorar horas a redigir manualmente, mas com este predefinimento o processo de redação facial requer apenas alguns passos simples.

Este artigo dá detalhes sobre **a predefinição do detetor de rosto** e mostra como usá-lo com Azure Media Services SDK para .NET.

[!INCLUDE [regulation](../video-indexer/includes/regulation.md)]

## <a name="compliance-privacy-and-security"></a>Conformidade, privacidade e segurança
 
Como um lembrete importante, deve cumprir todas as leis aplicáveis na sua utilização de analítica nos Serviços de Media Azure. Não deve utilizar a Azure Media Services ou qualquer outro serviço Azure de forma a violar os direitos dos outros. Antes de enviar quaisquer vídeos, incluindo quaisquer dados biométricos, para o serviço Azure Media Services para processamento e armazenamento, você deve ter todos os direitos adequados, incluindo todos os consentimentos apropriados, dos indivíduos no vídeo. Para conhecer o cumprimento, privacidade e segurança nos Serviços Azure Media, os Termos dos [Serviços Cognitivos](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)Azure. Para as obrigações de privacidade da Microsoft e tratamento dos seus dados, reveja a Declaração de [Privacidade](https://privacy.microsoft.com/PrivacyStatement)da Microsoft, os [Termos dos Serviços Online](https://www.microsoft.com/licensing/product-licensing/products) (OST) e a [adenda ao processamento de dados](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Mais informações sobre privacidade, incluindo sobre a retenção de dados, eliminação/destruição, estão disponíveis no OST e [aqui.](../video-indexer/faq.md) Ao utilizar os Serviços de Media Azure, concorda em ficar vinculado pelos Termos dos Serviços Cognitivos, o OST, o DPA e a Declaração de Privacidade

## <a name="face-redaction-modes"></a>Modos de redação de rosto

A redação facial funciona detetando rostos em cada quadro de vídeo e rastreando o objeto facial tanto para a frente como para trás no tempo, para que o mesmo indivíduo possa ser borrado de outros ângulos também. O processo de redação automatizado é complexo e nem sempre borra cada face 100% garantido. Por esta razão, a predefinição pode ser utilizada num modo de dois passes para melhorar a qualidade e precisão do borrão através de uma fase de edição antes de submeter o ficheiro para o passe de borrão final. 

Além de um modo **combinado** totalmente automático, o fluxo de trabalho de dois passes permite-lhe escolher as faces que deseja desfocar (ou não borrar) através de uma lista de IDs faciais. Para fazer ajustes arbitrários por quadro, a predefinição utiliza um ficheiro de metadados no formato JSON como entrada para o segundo passe. Este fluxo de trabalho é dividido em modos **Analisar** e **Redact.**

Também pode facilmente combinar os dois modos num único passe que executa ambas as tarefas num só trabalho; este modo chama-se **Combinado.**  Neste artigo, o código de amostra mostrará como utilizar o modo **combinado** de passe único simplificado num ficheiro de origem da amostra.

### <a name="combined-mode"></a>Modo combinado

Isto produz um ficheiro de vídeo MP4 redigido num único passe sem necessidade de edição manual do ficheiro JSON. A saída na pasta de ativos para o trabalho será um único ficheiro .mp4 que contém faces desfocadas utilizando o efeito borrado selecionado. Utilize a propriedade de resolução definida para **SourceResolution** para obter os melhores resultados para a redação.

| Fase | Nome de Ficheiro | Notas |
| --- | --- | --- |
| Ativo de entrada |"ignite-sample.mp4" |Vídeo em formato WMV, MOV ou MP4 |
| Config predefinido |Configuração do detetor de rosto | **modo**: FaceRedactorMode.Combined,  **BlurType**: BlurType.Med, **resolução**: AnalysisResolution.SourceResolution |
| Ativo de saída |"ignite-redacted.mp4 |Vídeo com efeito borrado aplicado aos rostos |

### <a name="analyze-mode"></a>Modo de análise

O passe **de Análise** do fluxo de trabalho de dois passes requer uma entrada de vídeo e produz um ficheiro JSON com uma lista dos locais faciais, imagens face ID e jpg de cada face detetada. 

| Fase | Nome de Ficheiro | Notas |
| --- | --- | --- |
| Ativo de entrada |"ignite-sample.mp4" |Vídeo em formato WMV, MPV ou MP4 |
| Config predefinido |Configuração do detetor de rosto |**modo**: FaceRedactorMode.Analyze, **resolução**: AnalysisResolution.SourceResolution|
| Ativo de saída |ignite-sample_annotations.jsem |Dados de anotação de localizações faciais no formato JSON. Isto pode ser editado pelo utilizador para modificar as caixas de desfocagem. Veja a amostra abaixo. |
| Ativo de saída |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Um jpg cortado de cada rosto detetado, onde o número indica o rótuloId do rosto |

#### <a name="output-example"></a>Exemplo de saída

```json
{
  "version": 1,
  "timescale": 24000,
  "offset": 0,
  "framerate": 23.976,
  "width": 1280,
  "height": 720,
  "fragments": [
    {
      "start": 0,
      "duration": 48048,
      "interval": 1001,
      "events": [
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [
          {
            "index": 13,
            "id": 1138,
            "x": 0.29537,
            "y": -0.18987,
            "width": 0.36239,
            "height": 0.80335
          },
          {
            "index": 13,
            "id": 2028,
            "x": 0.60427,
            "y": 0.16098,
            "width": 0.26958,
            "height": 0.57943
          }
        ],

    ... truncated
```

### <a name="redact-blur-mode"></a>Modo redact (borrão)

A segunda passagem do fluxo de trabalho requer um maior número de entradas que devem ser combinadas num único ativo.

Isto inclui uma lista de IDs para desfocar, o vídeo original, e as anotações JSON. Este modo utiliza as anotações para aplicar desfocagem no vídeo de entrada.

A saída do passe Analyze não inclui o vídeo original. O vídeo precisa de ser carregado no ativo de entrada para a tarefa do modo Redact e selecionado como o ficheiro principal.

| Fase | Nome de Ficheiro | Notas |
| --- | --- | --- |
| Ativo de entrada |"ignite-sample.mp4" |Vídeo em formato WMV, MPV ou MP4. O mesmo vídeo do passo 1. |
| Ativo de entrada |"ignite-sample_annotations.jsligado" |anotações ficheiro metadados da primeira fase, com modificações opcionais se desejar alterar as faces borradas. Isto deve ser editado numa aplicação externa, código ou editor de texto. |
| Ativo de entrada | "ignite-sample_IDList.txt" (Opcional) | Nova linha opcional separou lista de IDs faciais para redigir. Se ficarem em branco, todos os rostos da fonte terão o borrão aplicado. Pode utilizar a lista para optar seletivamente por não esbater rostos específicos. |
| Predefinição do detetor de rosto  |Configuração de predefinição  | **modo**: FaceRedactorMode.Redact, **BlurType**: BlurType.Med |
| Ativo de saída |"ignite-sample-redacted.mp4" |Vídeo com borrão aplicado com base em anotações |

#### <a name="example-output"></a>Saída de exemplo

Esta é a saída de uma Lista de IDList com um ID selecionado.

Exemplo foo_IDList.txt

```
1
2
3
```

## <a name="blur-types"></a>Tipos de borrão

No modo **Combinado** ou **Redact,** existem cinco modos de borrão diferentes que pode escolher através da configuração de entrada JSON: **Baixo,** **Med,** **High,** **Box** e **Preto**. Por **defeito, Med** é usado.

Pode encontrar amostras dos tipos de borrão abaixo.


#### <a name="low"></a>Baixo

![Borrão de baixa resolução dando o exemplo.](./media/media-services-face-redaction/blur-1.png)

#### <a name="med"></a>Med

![Borrão de resolução média dando o exemplo.](./media/media-services-face-redaction/blur-2.png)

#### <a name="high"></a>Alto

![Borrão de alta resolução dando o exemplo.](./media/media-services-face-redaction/blur-3.png)

#### <a name="box"></a>Box

![Modo de caixa para utilização na depuragem da sua saída.](./media/media-services-face-redaction/blur-4.png)

#### <a name="black"></a>Negra

![O modo caixa preta cobre todas as faces com caixas pretas.](./media/media-services-face-redaction/blur-5.png)

## <a name="elements-of-the-output-json-file"></a>Elementos do ficheiro JSON de saída

O MP redaction fornece deteção e rastreamento de localização facial de alta precisão que pode detetar até 64 rostos humanos numa moldura de vídeo. As faces frontais proporcionam os melhores resultados, enquanto as faces laterais e as faces pequenas (menos ou igual a 24x24 pixels) são desafiantes.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>.NET código de amostra

O seguinte programa mostra como utilizar o modo **combinado** de redação de passe único:

- Crie um ativo e carre faça o upload de um ficheiro de media para o ativo.
- Configure a predefinição do detetor de rosto que utiliza as definições de mode e DesfocadoType.
- Criar uma nova transformação utilizando a predefinição do detetor de rosto
- Descarregue o ficheiro de vídeo redigido.

## <a name="download-and-configure-the-sample"></a>Descarregue e configuure a amostra

Clone o repositório do GitHub que contém o exemplo de .NET para o seu computador com o seguinte comando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```

A amostra está localizada na pasta [FaceRedactor.](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/FaceRedactor) Abra [appsettings.jsno](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoAnalytics/FaceRedactor/appsettings.json) seu projeto descarregado. Substitua os valores pelas credenciais que obteve de [aceder às APIs.](./access-api-howto.md)

**Opcionalmente,** pode copiar o ficheiro **[sample.env](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/sample.env)** na raiz do repositório e preencher os detalhes lá dentro, e renomear esse ficheiro para **.env** (Note o ponto na frente!) para que possa ser usado em todos os projetos de amostra no repositório.  Isto elimina a necessidade de ter um appsettings.jspreenchido em cada amostra, e também o protege de verificar quaisquer configurações nos seus próprios repositórios clonados do git hub.

#### <a name="examples"></a>Exemplos

Este código mostra como configurar o **FaceDetectorPreset** para um borrão de modo **combinado.**

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPreset)]

Esta amostra de código mostra como a predefinição é transmitida num objeto Transform durante a criação. Após a criação do Transform, os postos de trabalho podem ser submetidos diretamente a ela.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPresetTransform)]

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

