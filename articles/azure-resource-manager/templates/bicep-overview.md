---
title: Linguagem bicep para modelos de gestor de recursos Azure
description: Descreve a linguagem Bicep para implantar infraestruturas para Azure através de modelos Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 6a2750dc99e82c9cf8c9b8b97d156d3a9fe30f31
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101747189"
---
# <a name="what-is-bicep-preview"></a>O que é Bicep (Pré-visualização)?

Bicep é uma linguagem para a implantação declarativa dos recursos do Azure. Simplifica a experiência de autoria fornecendo sintaxe concisa e melhor suporte para a modularidade e reutilização de códigos. Bicep é uma língua específica do domínio (DSL), o que significa que é projetado para um determinado cenário ou domínio. Bicep não se destina a ser uma linguagem de programação geral para escrever aplicações.

Bicep é uma abstração transparente sobre os modelos Azure Resource Manager (modelos ARM). Cada ficheiro Bicep compila-se com um modelo ARM padrão. Os tipos de recursos, versões API e propriedades válidas num modelo ARM são válidos num ficheiro Bicep.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="get-started"></a>Introdução

Para começar com a Bicep, [instale as ferramentas](https://github.com/Azure/bicep/blob/main/docs/installing.md).

Depois de instalar as ferramentas, experimente o [tutorial Bicep](./bicep-tutorial-create-first-bicep.md). A série tutorial acompanha-o através da estrutura e capacidades de Bicep. Você implementa ficheiros Bicep e converte um modelo ARM no ficheiro Bicep equivalente.

Para ver os ficheiros JSON e Bicep equivalentes lado a lado, consulte o [Bicep Playground](https://aka.ms/bicepdemo).

Se tiver um modelo ARM existente que gostaria de converter em Bicep, consulte [Decompile JSON para Bicep](compare-template-syntax.md#decompile-json-to-bicep).

## <a name="bicep-improvements"></a>Melhorias do Bicep

Bicep oferece uma sintaxe mais fácil e concisa quando comparada com o JSON equivalente. Não se usam `[...]` expressões. Em vez disso, você chama diretamente funções, obtém valores de parâmetros e variáveis, e recursos de referência. Para uma comparação completa da sintaxe, consulte [comparar JSON e Bicep para modelos](compare-template-syntax.md).

A Bicep gere automaticamente as dependências entre os recursos. Pode evitar a definição `dependsOn` quando o nome simbólico de um recurso é utilizado noutra declaração de recursos.

Com o Bicep, pode dividir o seu projeto em vários módulos.

A estrutura do ficheiro Bicep é mais flexível do que o modelo JSON. Pode declarar parâmetros, variáveis e saídas em qualquer lugar do ficheiro. No JSON, tem de declarar todos os parâmetros, variáveis e saídas dentro das secções correspondentes do modelo.

A extensão do Código VS para Bicep oferece validação mais rica e intellisense. Por exemplo, a extensão tem intellisense para obter propriedades de um recurso.

## <a name="faq"></a>FAQ

**Porquê criar uma nova linguagem em vez de usar uma existente?**

Pode pensar no Bicep como uma revisão à linguagem do modelo ARM existente em vez de uma nova linguagem. A sintaxe mudou, mas a funcionalidade e o tempo de funcionamento permanecem os mesmos.

Antes de desenvolver bicep, considerámos usar uma linguagem de programação existente. Decidimos que o nosso público-alvo seria mais fácil aprender Bicep em vez de começar com outra língua.

**Por que não concentrar a sua energia na Terraform ou noutra infraestrutura de terceiros como ofertas de Código?**

Diferentes utilizadores preferem diferentes idiomas de configuração e ferramentas. Queremos garantir que todas estas ferramentas proporcionam uma grande experiência no Azure. Bicep faz parte desse esforço.

Se está feliz por usar terraform, não há razão para mudar. A Microsoft está empenhada em garantir que a Terraform on Azure é o melhor que pode ser.

Para clientes que selecionaram modelos ARM, acreditamos que Bicep melhora a experiência de autoria. A Bicep também ajuda na transição para clientes que não adotaram a infraestrutura como código.

**Bicep é só para O Azure?**

A Bicep é uma DSL focada na implementação de soluções completas para o Azure. Cumprir esse objetivo requer trabalhar com algumas APIs que estão fora de Azure. Esperamos fornecer pontos de extensibilidade para esses cenários.

**O que acontece com os meus modelos ARM existentes?**

Continuam a funcionar exatamente como sempre funcionaram. Não precisas de fazer alterações. Continuaremos a apoiar a linguagem JSON do modelo ARM subjacente. Os ficheiros Bicep compilam-se com o JSON, e o JSON é enviado para Azure para ser implantado.

Quando estiver pronto, pode [converter os ficheiros JSON em Bicep](compare-template-syntax.md#decompile-json-to-bicep).

## <a name="next-steps"></a>Passos seguintes

Começa com o [tutorial do Bicep.](./bicep-tutorial-create-first-bicep.md)
