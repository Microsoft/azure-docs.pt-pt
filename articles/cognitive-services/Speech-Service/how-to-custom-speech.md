---
title: Introdução à conversão de voz personalizada - serviços de voz
titlesuffix: Azure Cognitive Services
description: Conversão de voz personalizada é um conjunto de ferramentas online que permitem-lhe avaliar e melhorar a precisão da conversão de voz em texto da Microsoft para seus aplicativos, ferramentas e produtos. Para começar a utilizar-se alguns dos arquivos de áudio de teste. Siga os links abaixo para começar a criar uma experiência de voz em texto personalizada.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: ab33feb1ffdbced193afaba8f52719b3c215652f
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65511125"
---
# <a name="what-is-custom-speech"></a>O que é a conversão de voz personalizada?

[Conversão de voz personalizada](https://aka.ms/custom-speech) é um conjunto de ferramentas online que permitem-lhe avaliar e melhorar a precisão da conversão de voz em texto da Microsoft para seus aplicativos, ferramentas e produtos. Para começar a utilizar-se alguns dos arquivos de áudio de teste. Siga os links abaixo para começar a criar uma experiência de voz em texto personalizada.

## <a name="whats-in-custom-speech"></a>O que inclui voz personalizada?

Antes de poder fazer qualquer coisa com a conversão de voz personalizada, terá de uma conta do Azure e uma subscrição de serviços de voz. Assim que tem uma conta, pode preparar os dados, treinar e testar seus modelos, inspecionar a qualidade de reconhecimento, avaliar precisão e, por fim, implementar e utilizar o modelo de voz em texto personalizado.

Este diagrama realça as partes que compõem o portal de voz personalizada. Use os links abaixo para saber mais sobre cada passo.

![Realça os diferentes componentes que compõem o portal de voz personalizada.](./media/custom-speech/custom-speech-overview.png)

1. [Inscrever-se e crie um projeto](#set-up-your-azure-account) - criar uma conta do Azure e subscrever os serviços de voz. Esta subscrição unificada fornece acesso a conversão de voz em texto, voz, tradução de voz e o portal de voz personalizada. Em seguida, utilizar a sua subscrição de serviços de voz, crie seu primeiro projeto de conversão de voz personalizada.

2. [Carregar dados de teste](how-to-custom-speech-test-data.md) -dados de teste de carregamento (arquivos de áudio) para avaliar fala em texto da Microsoft da oferta para seus aplicativos, ferramentas e produtos.

3. [Qualidade de reconhecimento de inspecionar](how-to-custom-speech-inspect-data.md) -utilizar o portal de voz personalizada para reproduzir áudio carregado e inspecionar a qualidade de reconhecimento de fala dos seus dados de teste. Para valores quantitativos, consulte [inspecionar dados](how-to-custom-speech-inspect-data.md).

4. [Avaliar exatidão](how-to-custom-speech-evaluate-data.md) -avaliar a precisão do modelo de voz em texto. O portal de voz personalizada irá disponibilizar uma *taxa de erros do Word*, que podem ser utilizadas para determinar se é necessário treinamento adicional. Se estiver satisfeito com a precisão, pode utilizar as APIs de serviço de voz diretamente. Se gostaria de aumentar a exatidão por uma média relativa de 5% - 20%, utilize o **treinamento** separador no portal para carregar dados de treinamento adicional, como etiqueta humanos transcrições e texto relacionado.

5. [Preparar o modelo](how-to-custom-speech-train-model.md) - melhorar a precisão do seu modelo de voz em texto, fornecendo transcrições de escrita (10-1.000 horas) e relacionados com o texto (< 200 MB), juntamente com sua áudio testar dados. Estes dados ajudam a preparar o modelo de voz em texto. Depois de treinamento, realizar novos testes, e se estiver satisfeito com o resultado, pode implementar o seu modelo.

6. [Implementar o modelo](how-to-custom-speech-deploy-model.md) - criar um ponto final personalizado para o modelo de voz em texto e utilizá-lo em seus aplicativos, ferramentas ou produtos.

## <a name="set-up-your-azure-account"></a>Configurar a sua conta do Azure

É necessária uma subscrição de serviços de voz antes de poder utilizar o portal de voz personalizada para criar um modelo personalizado. Siga estas instruções para criar uma subscrição de serviços de voz padrão: [Criar uma subscrição de voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-azure-account).

> [!NOTE]
> Não se esqueça de criar subscrições de (S0) standard, subscrições de avaliação gratuita (F0) não são suportadas.

Depois de criar uma conta do Azure e uma subscrição de serviços de voz, terá de iniciar sessão no portal de voz personalizada e ligue-se a sua subscrição.

1. Obtenha a chave de subscrição de serviços de voz do portal do Azure.
2. Início de sessão para o [portal da voz personalizada](https://aka.ms/custom-speech).
3. Selecione a subscrição que preciso para trabalhar com e criar um projeto de conversão de voz.
4. Se quiser modificar a sua subscrição, utilize o **ícone de roda dentada** ícone localizado na parte superior da navegação.

## <a name="how-to-create-a-project"></a>Como criar um projeto

Como os dados, modelos, testes e pontos finais estão organizados de conteúdo **projetos** no portal da voz personalizada. Cada projeto é específico para um domínio e o idioma/país. Por exemplo, pode criar um projeto para centros de chamada que utilizam o inglês nos Estados Unidos.

Para criar seu primeiro projeto, selecione o **voz de voz para texto/personalizado**, em seguida, clique em **novo projeto**. Siga as instruções fornecidas pelo Assistente para criar o projeto. Depois de criar um projeto, deverá ver quatro separadores: **Dados**, **testes**, **treinamento**, e **implementação**. Utilize as ligações fornecidas [próximos passos](#next-steps) para saber como utilizar cada guia.

## <a name="next-steps"></a>Passos Seguintes

* [Preparar e testar seus dados](how-to-custom-speech-test-data.md)
* [Inspecione os seus dados](how-to-custom-speech-inspect-data.md)
* [Avaliar os seus dados](how-to-custom-speech-evaluate-data.md)
* [Preparar o seu modelo](how-to-custom-speech-train-model.md)
* [Implementar o seu modelo](how-to-custom-speech-deploy-model.md)
