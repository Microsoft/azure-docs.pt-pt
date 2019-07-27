---
title: Migrar do serviço de Fala Personalizada para o serviço de fala
titleSuffix: Azure Cognitive Services
description: O serviço de voz personalizada agora é parte integrante do serviço de voz. Mude para o serviço de voz para tirar partido dos mais recentes atualizações de qualidade e funcionalidade.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 01b853c59723a8ed79cb32b0ee9c245c9c3ffb3f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562768"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migrar do serviço de voz personalizada para o serviço de voz

Utilize este artigo para migrar seus aplicativos de serviço de voz personalizada para o serviço de voz.

O serviço de voz personalizada agora é parte integrante do serviço de voz. Alterne para os serviços de fala para se beneficiar da qualidade e das atualizações de recursos mais recentes.

## <a name="migration-for-new-customers"></a>Migração para os novos clientes

O modelo de preços é mais simples, com um modelo de preços baseado em hora para o serviço de voz.  

1. Crie um recurso do Azure em cada região em que a sua aplicação está disponível. O nome de recurso do Azure é **voz**. Pode usar um único recurso do Azure para os seguintes serviços na mesma região, em vez de criar recursos separados:

    * Conversão de voz em texto
    * Personalizado de voz em texto
    * Conversão de texto em voz
    * Tradução de voz

2. Transfira o [SDK de voz](speech-sdk.md).

3. Siga os guias de início rápido e amostras de SDK para utilizar as APIs corretas. Se utilizar as APIs REST, terá também de utilizar os pontos de extremidade corretos e as chaves do recurso.

4. Atualize o aplicativo cliente para usar os serviços de fala e as APIs.

## <a name="migration-for-existing-customers"></a>Migração para os clientes existentes

Migre suas chaves de recurso existentes para os serviços de fala no portal de serviços de fala. Utilize os passos seguintes:

> [!NOTE]
> As chaves do recurso só podem ser migradas dentro da mesma região.

1. Inicie sessão para o [cris.ai](https://cris.ai/Home/CustomSpeech) portal e selecione a subscrição no menu superior direito.

2. Selecione **migrar a subscrição selecionada**.

3. Introduza a chave de subscrição na caixa de texto e selecione **migrar**.

## <a name="next-steps"></a>Passos Seguintes

* [Experimente os serviços de fala gratuitamente](get-started.md).
* Saiba mais [conversão de voz em texto](./speech-to-text.md) conceitos.

## <a name="see-also"></a>Consulte também

* [O que é o serviço de voz](overview.md)
* [Documentação dos serviços de fala e do SDK de fala](speech-sdk.md#get-the-sdk)
