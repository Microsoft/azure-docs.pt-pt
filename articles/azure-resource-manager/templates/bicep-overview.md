---
title: Linguagem bicep para modelos de gestor de recursos Azure
description: Descreve a linguagem Bicep para implantar infraestruturas para Azure através de modelos Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: af207e6ca88eab50fe6030883379c87c0ec05691
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773752"
---
# <a name="what-is-bicep-preview"></a>O que é Bicep (Pré-visualização)?

Bicep é uma linguagem para a implantação declarativa dos recursos do Azure. Pode utilizar Bicep em vez de JSON para desenvolver os seus modelos de Gestor de Recursos Azure (modelos ARM). A Bicep simplifica a experiência de autoria fornecendo sintaxe concisa, melhor suporte para a reutilização de códigos e melhor segurança do tipo. Bicep é uma língua específica do domínio (DSL), o que significa que é projetado para um determinado cenário ou domínio. Não se destina a ser uma linguagem de programação geral para escrever aplicações.

A sintaxe JSON para criar o modelo pode ser verbosa e requer expressão complicada. Bicep melhora essa experiência sem perder nenhuma das capacidades de um modelo JSON. É uma abstração transparente sobre o JSON para modelos ARM. Cada ficheiro Bicep compila-se com um modelo ARM padrão. Os tipos de recursos, versões API e propriedades válidas num modelo ARM são válidos num ficheiro Bicep. Existem algumas [limitações conhecidas](#known-limitations) na versão atual.

Bicep está atualmente em pré-visualização. Para acompanhar o estado da obra, consulte o [repositório do projeto Bicep.](https://github.com/Azure/bicep)

Para saber mais sobre Bicep, veja o seguinte vídeo.

> [!VIDEO https://www.youtube.com/embed/sc1kJfcRQgY]

## <a name="get-started"></a>Introdução

Para começar com a Bicep, [instale as ferramentas](bicep-install.md).

Depois de instalar as ferramentas, experimente o [tutorial Bicep](./bicep-tutorial-create-first-bicep.md). A série tutorial acompanha-o através da estrutura e capacidades de Bicep. Você implementa ficheiros Bicep e converte um modelo ARM no ficheiro Bicep equivalente.

Para ver os ficheiros JSON e Bicep equivalentes lado a lado, consulte o [Bicep Playground](https://aka.ms/bicepdemo).

Se tiver um modelo ARM existente que gostaria de converter em Bicep, consulte [os modelos DE CONVERSÃO ARM entre JSON e Bicep](bicep-decompile.md).

## <a name="benefits-of-bicep-versus-other-tools"></a>Benefícios da Bicep contra outras ferramentas

A Bicep oferece as seguintes vantagens sobre outras opções:

* **Suporte para todos os tipos de recursos e versões API**: Bicep suporta imediatamente todas as versões de pré-visualização e GA para serviços Azure. Assim que um fornecedor de recursos introduz novos tipos de recursos e versões API, pode usá-los no seu ficheiro Bicep. Não é preciso esperar que as ferramentas sejam atualizadas antes de utilizar os novos serviços.
* **Experiência de autoria**: Quando utiliza o Código VS para criar os seus ficheiros Bicep, obtém uma experiência de autoria de primeira classe. O editor fornece uma rica validação de tipo de segurança, intellisense e sintaxe.
* **Modularidade**: Pode dividir o seu código Bicep em peças manejáveis utilizando [módulos](bicep-modules.md). O módulo implementa um conjunto de recursos relacionados. Os módulos permitem-lhe reutilizar código e simplificar o desenvolvimento. Adicione o módulo a um ficheiro Bicep sempre que precisar de utilizar esses recursos.
* **Integração com os serviços Azure**: A Bicep está integrada com serviços Azure, tais como Azure Policy, especificações de modelos e Plantas.
* **Não há ficheiros estatais ou estatais para gerir:** Todos os estados estão armazenados em Azure. Os utilizadores podem colaborar e ter confiança que as suas atualizações são tratadas como esperado. Utilize o ["e se" para pré-visualizar](template-deploy-what-if.md) alterações antes de implementar o seu modelo.
* **Sem custos e fonte aberta**: Bicep é completamente gratuito. Não tens de pagar por capacidades premium. Também é suportado pelo suporte da Microsoft.

## <a name="bicep-improvements"></a>Melhorias do Bicep

Bicep oferece uma sintaxe mais fácil e concisa quando comparada com o JSON equivalente. Não se usam `[...]` expressões. Em vez disso, você chama diretamente funções, e obtém valores a partir de parâmetros e variáveis. Você dá a cada recurso implantado um nome simbólico, o que facilita a referência desse recurso no seu modelo.

Por exemplo, o seguinte JSON devolve um valor de saída de uma propriedade de recurso.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

A expressão de saída equivalente em Bicep é mais fácil de escrever. O exemplo a seguir devolve a mesma propriedade utilizando o nome simbólico **publicIP** para um recurso definido dentro do modelo:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

Para uma comparação completa da sintaxe, consulte [comparar JSON e Bicep para modelos](compare-template-syntax.md).

A Bicep gere automaticamente as dependências entre os recursos. Pode evitar a definição `dependsOn` quando o nome simbólico de um recurso é utilizado noutra declaração de recursos.

A estrutura do ficheiro Bicep é mais flexível do que o modelo JSON. Pode declarar parâmetros, variáveis e saídas em qualquer lugar do ficheiro. No JSON, tem de declarar todos os parâmetros, variáveis e saídas dentro das secções correspondentes do modelo.

## <a name="known-limitations"></a>Limitações conhecidas

Existem atualmente os seguintes limites:

* Não é possível definir o modo ou o tamanho do lote em ciclos de cópia.
* Não se pode combinar laços e condições.
* Objetos de linha única e matrizes, `['a', 'b', 'c']` como, não são suportados.

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

Quando estiver pronto, pode [converter os ficheiros JSON em Bicep](bicep-decompile.md).

## <a name="next-steps"></a>Passos seguintes

Começa com o [tutorial do Bicep.](./bicep-tutorial-create-first-bicep.md)
