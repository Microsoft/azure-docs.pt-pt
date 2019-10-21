---
title: O que são documentos paralelos? -Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Os documentos paralelos são pares de documentos em que um é a tradução do outro. Um documento no par contém sentenças no idioma de origem e o outro documento contém essas frases traduzidas para o idioma de destino.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: d7c38a44e3111a319e4146b3c9b71a22b0d31bfd
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675473"
---
# <a name="what-are-parallel-documents"></a>O que são documentos paralelos?

Os documentos paralelos são pares de documentos em que um é a tradução do outro. Um documento no par contém sentenças no idioma de origem e o outro documento contém essas frases traduzidas para o idioma de destino.
Não importa qual idioma está marcado como "origem" e qual idioma está marcado como "destino" – um documento paralelo pode ser usado para treinar um sistema de tradução em qualquer direção.

## <a name="requirements"></a>Requisitos

Você precisará de um mínimo de 10.000 sentenças paralelas alinhadas exclusivamente para treinar um sistema. Essa limitação é uma rede de segurança para garantir que suas frases paralelas contenham um vocabulário exclusivo suficiente para treinar com êxito um modelo de tradução. Como prática recomendada, adicione continuamente mais conteúdo paralelo e retreine para melhorar a qualidade do seu sistema de tradução. Consulte alinhamento de [frase](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment).

A Microsoft exige que os documentos carregados no Tradutor personalizado não violem as propriedades intelectuais ou de direitos autorais de terceiros. Para obter mais informações, consulte os [termos de uso](https://azure.microsoft.com/support/legal/cognitive-services-terms/).
Carregar um documento usando o portal não altera a propriedade da propriedade intelectual no próprio documento.

## <a name="use-of-parallel-documents"></a>Uso de documentos paralelos

Os documentos paralelos são usados pelo sistema:

1.  Para saber como palavras, frases e sentenças são comumente mapeadas entre os dois idiomas.

2.  Para saber como processar o contexto apropriado dependendo das frases ao redor. Uma palavra nem sempre pode ser traduzida para exatamente a mesma palavra em outro idioma.

Como prática recomendada, certifique-se de que há uma correspondência de sentença 1:1 entre as versões de idioma de origem e de destino dos documentos.

Se seu projeto for específico de domínio (categoria), seus documentos deverão ser consistentes em terminologia dentro dessa categoria. A qualidade do sistema de tradução resultante depende do número de frases no conjunto de documentos e da qualidade das frases. Quanto mais exemplos seus documentos contêm com diferentes usos para uma palavra específica para sua categoria, melhor será o trabalho que o sistema pode fazer durante a tradução.

Os documentos carregados são privados em cada espaço de trabalho e podem ser usados em quantos projetos ou treinamentos desejar. As frases extraídas de seus documentos são armazenadas separadamente em seu repositório como arquivos de texto Unicode simples e estão disponíveis para exclusão. Não use o tradutor personalizado como um repositório de documentos. você não poderá baixar os documentos carregados no formato que os carregou.



## <a name="next-steps"></a>Passos seguintes

- Saiba como usar um [dicionário](what-is-dictionary.md) no Tradutor personalizado.
