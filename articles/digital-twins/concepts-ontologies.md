---
title: O que é uma ontologia?
titleSuffix: Azure Digital Twins
description: Conheça as intologias da indústria DTDL para modelação num determinado domínio
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3393856b25040cff603ea2ef51e8adbcba78dc26
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034698"
---
# <a name="what-is-an-ontology"></a>O que é uma ontologia? 

O vocabulário de uma solução Azure Digital Twins é definido usando [modelos](concepts-models.md), que descrevem os tipos de entidade que existem no seu ambiente.

Por vezes, quando a sua solução está ligada a uma determinada indústria, pode ser mais fácil e eficaz começar com um conjunto de modelos para essa indústria que já existem, em vez de autorizar o seu próprio modelo definido de raiz. Estes conjuntos de modelos pré-existentes são **chamados de ontologies**. 

Em geral, uma ontologia é um conjunto de modelos para um determinado domínio - como uma estrutura de construção, sistema IoT, cidade inteligente, a rede de energia, conteúdo web, etc. As ontotologias são frequentemente usadas como esquemas para gráficos de conhecimento, como podem permitir:
* Harmonização de componentes de software, documentação, bibliotecas de consultas, etc.
* Redução do investimento na modelação conceptual e no desenvolvimento do sistema
* Interoperabilidade de dados mais fácil a nível semântico
* Reutilização de boas práticas, em vez de começar do zero ou "reinventar a roda"

Este artigo explica porquê e como usar ontologias para os seus modelos Azure Digital Twins, bem como quais as ontologias e ferramentas para eles estão disponíveis hoje.

## <a name="using-ontologies-for-azure-digital-twins"></a>Utilização de ontologies para gémeos digitais Azure

As ontologies proporcionam um ótimo ponto de partida para soluções digitais gémeas. Englobam um conjunto de modelos e relações específicos de domínio entre entidades para conceber, criar e analisar um gráfico digital de dois gráficos. As onlogies permitem que os desenvolvedores de soluções iniciem uma solução de gémeos digitais a partir de um ponto de partida comprovado, e concentrem-se na resolução de problemas de negócio. As intíologias fornecidas pela Microsoft também são concebidas para serem facilmente extensíveis, para que possa personalizá-las para a sua solução. 

Além disso, a utilização destas linhas de uso nas suas soluções pode configurar-as para uma integração mais perfeita entre diferentes parceiros e fornecedores, porque as ontotologias podem fornecer um vocabulário comum através de soluções.

Como os modelos em Azure Digital Twins são representados na [Linguagem de Definição de Gémeos Digitais (DTDL),](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)as toologias para utilização com as Gémeas Digitais Azure também estão escritas em DTDL. 

## <a name="strategies-for-integrating-ontologies"></a>Estratégias de integração de ontologies

Existem três estratégias possíveis para integrar as práticas padrão da indústria com o DTDL. Pode escolher o que melhor funciona para si, dependendo das suas necessidades:

| Estratégia | Descrição | Recursos |
| --- | --- | --- |
| **Adotar** | Você pode começar a sua solução com uma ontologia DTDL de código aberto que foi construída com base em padrões da indústria amplamente adotados. Pode utilizar estes conjuntos de modelos fora da caixa ou alargá-los com as suas próprias adições para uma solução personalizada. | [*Conceitos: &nbsp; Adotar &nbsp; as práticas padrão da indústria &nbsp;*](concepts-ontologies-adopt.md)<br><br>[*Conceitos: &nbsp; Alargamento das &nbsp; falas*](concepts-ontologies-extend.md) |
| **Converter** | Se já tiver os modelos existentes representados noutro formato padrão, pode convertê-los em DTDL para os utilizar com a Azure Digital Twins. | [*Conceitos: &nbsp; Conversão de &nbsp; ontologies*](concepts-ontologies-convert.md)<br><br>[*Conceitos: &nbsp; Alargamento das &nbsp; falas*](concepts-ontologies-extend.md) |
| **Autor** | Pode sempre desenvolver os seus próprios modelos DTDL personalizados do zero, utilizando quaisquer padrões da indústria aplicáveis como inspiração. | [*Conceitos: Modelos DTDL*](concepts-models.md) |

### <a name="using-ontology-strategies-in-a-model-development-path"></a>Usando estratégias de ontologia em um caminho de desenvolvimento de modelos

Independentemente da estratégia que escolha para integrar uma ontologia em Azure Digital Twins, pode seguir o caminho completo abaixo para guiá-lo através da criação e upload da sua ontologia como modelos DTDL.

1. Comece por rever e entender [a modelação DTDL em Azure Digital Twins](concepts-models.md).
1. Proceda com a sua estratégia de integração ontrologia escolhida de cima: [**Adopt,**](concepts-ontologies-adopt.md) [**Converta,**](concepts-ontologies-convert.md)ou [**Autore**](concepts-models.md) os seus modelos com base na sua ontologia.
    1. Se necessário, [estenda](concepts-ontologies-extend.md) a sua ontologia para personalizá-la às suas necessidades.
1. [Valide](how-to-parse-models.md) os seus modelos para verificar se estão a trabalhar documentos DTDL.
1. Faça o upload dos seus modelos acabados para Azure Digital Twins, utilizando as [APIs](how-to-manage-model.md#upload-models) ou uma amostra como o [carregador de modelos Azure Digital Twins](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader).

Depois disso, deverá poder utilizar os seus modelos na sua instância Azure Digital Twins. 

Pode visualizá-las com amostras como [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) ou [Azure Digital Twins Model Visualizer,](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer)ou passar a usá-las para criar [gémeos digitais.](concepts-twins-graph.md)

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre as estratégias de adoção, conversão e autoria de ontotologias:
* [*Conceitos: Adoção de práticas padrão da indústria*](concepts-ontologies-adopt.md)
* [*Conceitos: Conversão de ontologies*](concepts-ontologies-convert.md)
* [*Como: Gerir modelos DTDL*](how-to-manage-model.md)

Ou, saiba como os modelos são usados para criar gémeos digitais: [*Conceitos: Gémeos Digitais e o gráfico gémeo.*](concepts-twins-graph.md)