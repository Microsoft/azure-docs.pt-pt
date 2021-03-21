---
title: O que são documentos paralelos? - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Documentos paralelos são pares de documentos onde um é a tradução do outro. Um documento do par contém frases na língua de origem e o outro documento contém estas frases traduzidas para a língua-alvo.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 64192014c9dfe5bb958e730dae62e504f0976fa1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895599"
---
# <a name="what-are-parallel-documents"></a>O que são documentos paralelos?

Documentos paralelos são pares de documentos onde um é a tradução do outro. Um documento do par contém frases na língua de origem e o outro documento contém estas frases traduzidas para a língua-alvo.
Não importa qual a língua que é marcada como "fonte" e qual a língua que é marcada como "alvo" – um documento paralelo pode ser usado para formar um sistema de tradução em qualquer direção.

## <a name="requirements"></a>Requisitos

Você precisará de um mínimo de 10.000 frases paralelas únicas alinhadas para treinar um sistema. Esta limitação é uma rede de segurança para garantir que as suas frases paralelas contenham vocabulário único suficiente para treinar com sucesso um modelo de tradução. Como uma boa prática, adicione continuamente mais conteúdo paralelo e retreinar para melhorar a qualidade do seu sistema de tradução. Consulte o [Alinhamento da Frase](./sentence-alignment.md).

A Microsoft exige que os documentos enviados para o Tradutor Personalizado não violem os direitos de autor ou propriedades intelectuais de terceiros. Para mais informações, por favor leia as [condições legais de utilização.](https://azure.microsoft.com/support/legal/cognitive-services-terms/)
O upload de um documento utilizando o portal não altera a propriedade da propriedade intelectual no próprio documento.

## <a name="use-of-parallel-documents"></a>Utilização de documentos paralelos

Os documentos paralelos são utilizados pelo sistema:

1.  Para aprender como palavras, frases e frases são geralmente mapeadas entre as duas línguas.

2.  Para aprender a processar o contexto apropriado dependendo das frases circundantes. Uma palavra pode nem sempre traduzir-se para a mesma palavra na outra língua.

Como melhor prática, certifique-se de que existe uma correspondência de sentença de 1:1 entre as versões linguísticas de origem e alvo dos documentos.

Se o seu projeto for específico de domínio (categoria), os seus documentos devem ser consistentes em terminologia dentro dessa categoria. A qualidade do sistema de tradução resultante depende do número de frases no seu conjunto de documentos e da qualidade das frases. Quanto mais exemplos os seus documentos contiverem com usos diversos para uma palavra específica para a sua categoria, melhor trabalho o sistema pode fazer durante a tradução.

Os documentos enviados são privados de cada espaço de trabalho e podem ser usados em tantos projetos ou formações que quiser. As frases extraídas dos seus documentos são armazenadas separadamente no seu repositório como ficheiros de texto simples do Unicode e estão disponíveis para eliminar. Não utilize o Tradutor Personalizado como repositório de documentos, não poderá descarregar os documentos que carregou no formato que os carregou.



## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar um [dicionário](what-is-dictionary.md) em Tradutor Personalizado.