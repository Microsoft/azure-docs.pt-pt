---
title: O que são documentos paralelos? - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Documentos paralelos são pares de documentos onde um é a tradução do outro. Um documento no par contém frases na língua de origem e o outro documento contém estas frases traduzidas na linguagem-alvo.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: d7c38a44e3111a319e4146b3c9b71a22b0d31bfd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "72675473"
---
# <a name="what-are-parallel-documents"></a>O que são documentos paralelos?

Documentos paralelos são pares de documentos onde um é a tradução do outro. Um documento no par contém frases na língua de origem e o outro documento contém estas frases traduzidas na linguagem-alvo.
Não importa qual a língua que é marcada como "fonte" e qual a língua que é marcada como "alvo" – um documento paralelo pode ser usado para treinar um sistema de tradução em qualquer direção.

## <a name="requirements"></a>Requisitos

Você precisará de um mínimo de 10.000 frases paralelas alinhadas únicas para treinar um sistema. Esta limitação é uma rede de segurança para garantir que as suas frases paralelas contenham vocabulário único suficiente para treinar com sucesso um modelo de tradução. Como uma boa prática, adicione continuamente mais conteúdo paralelo e retreça para melhorar a qualidade do seu sistema de tradução. Por favor, consulte o Alinhamento da [Sentença](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment).

A Microsoft exige que os documentos enviados para o Tradutor Personalizado não violem os direitos de autor ou propriedades intelectuais de terceiros. Para mais informações, por favor leia as condições legais de [utilização.](https://azure.microsoft.com/support/legal/cognitive-services-terms/)
O upload de um documento utilizando o portal não altera a propriedade da propriedade intelectual no próprio documento.

## <a name="use-of-parallel-documents"></a>Utilização de documentos paralelos

Os documentos paralelos são utilizados pelo sistema:

1.  Para aprender como as palavras, frases e frases são comumente mapeadas entre as duas línguas.

2.  Para aprender a processar o contexto apropriado dependendo das frases circundantes. Uma palavra pode nem sempre traduzir-se para a mesma palavra na outra língua.

Como uma boa prática, certifique-se de que existe uma correspondência de 1:1 entre a fonte e as versões linguísticas-alvo dos documentos.

Se o seu projeto for específico de domínio (categoria), os seus documentos devem ser consistentes em terminologia dentro dessa categoria. A qualidade do sistema de tradução resultante depende do número de frases no seu conjunto de documentos e da qualidade das frases. Quanto mais exemplos os seus documentos contêm com diversos usos para uma palavra específica da sua categoria, melhor é o trabalho que o sistema pode fazer durante a tradução.

Os documentos enviados são privados para cada espaço de trabalho e podem ser usados em tantos projetos ou formações quanto quiser. As frases extraídas dos seus documentos são armazenadas separadamente no seu repositório como ficheiros de texto Unicode simples e estão disponíveis para a sua eliminação. Não utilize o Tradutor Personalizado como repositório de documentos, não poderá descarregar os documentos que carregou no formato que os carregou.



## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar um [dicionário](what-is-dictionary.md) em Tradutor Personalizado.
