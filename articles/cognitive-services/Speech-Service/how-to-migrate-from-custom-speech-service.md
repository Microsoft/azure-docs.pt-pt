---
title: Migrar do serviço de voz personalizada para serviços de voz
titlesuffix: Azure Cognitive Services
description: O serviço de voz personalizada agora é parte dos serviços de voz. Mudar para os serviços de voz para tirar partido dos mais recentes atualizações de qualidade e funcionalidade.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 501555c68b7b5922bbf5a74d2a7ed5e548b900c5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57833542"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migrar do serviço de voz personalizada para o serviço de voz

Utilize este artigo para migrar seus aplicativos de serviço de voz personalizada para o serviço de voz.

O serviço de voz personalizada agora é parte integrante do serviço de voz. Mudar para os serviços de voz para tirar partido dos mais recentes atualizações de qualidade e funcionalidade.

## <a name="migration-for-new-customers"></a>Migração para os novos clientes

O modelo de preços é mais simples, com um modelo de preços baseado em hora para o serviço de voz.  

1. Crie um recurso do Azure em cada região em que a sua aplicação está disponível. O nome de recurso do Azure é **voz**. Pode usar um único recurso do Azure para os seguintes serviços na mesma região, em vez de criar recursos separados:

    * Conversão de voz em texto
    * Personalizado de voz em texto
    * Conversão de texto em voz
    * Tradução de voz

2. Transfira o [SDK de voz](speech-sdk.md).

3. Siga os guias de início rápido e amostras de SDK para utilizar as APIs corretas. Se utilizar as APIs REST, terá também de utilizar os pontos de extremidade corretos e as chaves do recurso.

4. Atualize a aplicação de cliente para utilizar as APIs e serviços de voz.

## <a name="migration-for-existing-customers"></a>Migração para os clientes existentes

Migre as chaves de recurso existente para os serviços de voz no portal do serviços de voz. Utilize os passos seguintes:

> [!NOTE]
> As chaves do recurso só podem ser migradas dentro da mesma região.

1. Inicie sessão para o [cris.ai](https://www.cris.ai) portal e selecione a subscrição no menu superior direito.

2. Selecione **migrar a subscrição selecionada**.

3. Introduza a chave de subscrição na caixa de texto e selecione **migrar**.

## <a name="next-steps"></a>Passos Seguintes

* [Experimente gratuitamente os serviços de voz](get-started.md).
* Saiba mais [conversão de voz em texto](./speech-to-text.md) conceitos.

## <a name="see-also"></a>Consulte também

* [O que é o serviço de voz](overview.md)
* [Documentação de serviços de voz e o SDK de voz](speech-sdk.md#get-the-sdk)
