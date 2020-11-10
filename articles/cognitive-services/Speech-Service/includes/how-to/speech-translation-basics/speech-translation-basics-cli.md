---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: v-demjoh
ms.openlocfilehash: a5796dfb066ea8ac7c59c02b29ef67b184c67edb
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94424907"
---
Uma das características centrais do serviço de fala é a capacidade de reconhecer a fala humana e traduzi-la para outras línguas. Neste arranque rápido aprende-se a usar o Speech SDK nas suas apps e produtos para realizar tradução de fala de alta qualidade. Este quickstart traduz a fala do microfone para o texto em outra língua.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem uma conta Azure e subscrição do serviço de fala. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço Desemação](../../../overview.md#try-the-speech-service-for-free).

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="set-source-and-target-language"></a>Definir origem e linguagem-alvo

Este comando chama O Discurso CLI para traduzir o discurso do microfone do italiano para o francês.

```shell
 spx translate --microphone --source it-IT --target fr
```