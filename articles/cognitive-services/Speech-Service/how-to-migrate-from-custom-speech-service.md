---
title: Migrar do serviço de Fala Personalizada para o serviço de fala
titleSuffix: Azure Cognitive Services
description: O serviço de Fala Personalizada agora faz parte do serviço de fala. Alterne para o serviço de fala para se beneficiar da qualidade e das atualizações de recursos mais recentes.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 9c93286329316d081f8fd99ebd360195931b7b09
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805931"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migrar do serviço de Fala Personalizada para o serviço de fala

Use este artigo para migrar seus aplicativos do serviço de Fala Personalizada para o serviço de fala.

O serviço de Fala Personalizada agora faz parte do serviço de fala. Alterne para o serviço de fala para se beneficiar da qualidade e das atualizações de recursos mais recentes.

## <a name="migration-for-new-customers"></a>Migração para os novos clientes

O modelo de preços é mais simples, usando um modelo de preços baseado em hora para o serviço de fala.  

1. Crie um recurso do Azure em cada região em que a sua aplicação está disponível. O nome de recurso do Azure é **voz**. Pode usar um único recurso do Azure para os seguintes serviços na mesma região, em vez de criar recursos separados:

    * Conversão de voz em texto
    * Personalizado de voz em texto
    * Conversão de texto em voz
    * Tradução de voz

2. Transfira o [SDK de voz](speech-sdk.md).

3. Siga os guias de início rápido e amostras de SDK para utilizar as APIs corretas. Se utilizar as APIs REST, terá também de utilizar os pontos de extremidade corretos e as chaves do recurso.

4. Atualize o aplicativo cliente para usar o serviço de fala e as APIs.

## <a name="migration-for-existing-customers"></a>Migração para os clientes existentes

Migre as chaves de recurso existentes para o serviço de fala no portal do serviço de fala. Utilize os passos seguintes:

> [!NOTE]
> As chaves do recurso só podem ser migradas dentro da mesma região.

1. Inicie sessão para o [cris.ai](https://cris.ai/Home/CustomSpeech) portal e selecione a subscrição no menu superior direito.

2. Selecione **migrar a subscrição selecionada**.

3. Introduza a chave de subscrição na caixa de texto e selecione **migrar**.

## <a name="next-steps"></a>Passos seguintes

* [Experimente o Speech Service gratuitamente](get-started.md).
* Saiba mais [conversão de voz em texto](./speech-to-text.md) conceitos.

## <a name="see-also"></a>Ver também

* [O que é o serviço de fala](overview.md)
* [Documentação do Speech Service e do Speech SDK](speech-sdk.md#get-the-sdk)
