---
title: Loja de valor-chave de configuração de aplicações Azure
description: Uma visão geral de como os dados de configuração são armazenados na Configuração da Aplicação Azure.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 1cd13369f443f91782eef1024003e07435a44a45
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425226"
---
# <a name="keys-and-values"></a>Chaves e valores

A Configuração de Aplicações Azure armazena os dados de configuração como pares de valor-chave. Os pares de valor-chave são uma forma simples mas flexível de representar vários tipos de configurações de aplicações que os desenvolvedores estão familiarizados.

## <a name="keys"></a>Chaves

As teclas servem como nome para pares de valor-chave e são usadas para armazenar e recuperar valores correspondentes. É uma prática comum organizar chaves num espaço de nome hierárquico usando um delimitador de caracteres, como `/` ou `:`. Use uma convenção que seja mais adequada para a sua candidatura. A configuração da aplicação trata as teclas como um todo. Não analisa as chaves para descobrir como os seus nomes são estruturados ou impor qualquer regra sobre eles.

A utilização de dados de configuração dentro dos quadros de aplicação pode ditar esquemas específicos de nomeação para valores-chave. Como exemplo, o quadro da Nuvem de primavera de Java define `Environment` recursos que fornecem definições a uma aplicação da primavera a ser parametrizada por variáveis que incluem nome e *perfil*de *aplicação.* As teclas para os dados de configuração relacionados com a Nuvem de primavera normalmente começam com estes dois elementos separados por um delimitador.

As chaves armazenadas na Configuração da Aplicação são cordas sensíveis a casos, baseadas em unicódigo. As *teclas app1* e *App1* são distintas numa loja de Configuração de Aplicações. Tenha isto em mente quando utilizar as definições de configuração dentro de uma aplicação, porque algumas estruturas lidam com as teclas de configuração de forma insensível. Por exemplo, o sistema de configuração ASP.NET Core trata as teclas como cordas insensíveis à caixa. Para evitar comportamentos imprevisíveis quando consultar a Configuração de Aplicações dentro de uma aplicação ASP.NET Core, não utilize teclas que diferem apenas por invólucro.

Pode utilizar qualquer personagem unicode em nomes-chave introduzidos na Configuração de Aplicações, exceto para `*`, `,`e `\`. Estes personagens estão reservados. Se precisa incluir um personagem reservado, deve escapar-lhe usando `\{Reserved Character}`. Há um limite de tamanho combinado de 10 KB num par de valor-chave. Este limite inclui todos os caracteres na chave, o seu valor e todos os atributos opcionais associados. Dentro deste limite, pode ter muitos níveis hierárquicos para chaves.

### <a name="design-key-namespaces"></a>Design espaços de nomes-chave

Existem duas abordagens gerais para nomear teclas usadas para dados de configuração: plana ou hierárquica. Estes métodos são semelhantes do ponto de vista do uso da aplicação, mas o nome hierárquico oferece uma série de vantagens:

* Mais fácil de ler. Em vez de uma longa sequência de caracteres, os delimitadores numa função hierárquica de nome-chave funcionam como espaços numa frase. Também proporcionam ruturas naturais entre palavras.
* Mais fácil de gerir. Uma hierarquia de nome-chave representa grupos lógicos de dados de configuração.
* Mais fácil de usar. É mais simples escrever uma consulta que combina com as chaves numa estrutura hierárquica e recupera apenas uma porção de dados de configuração. Além disso, muitos quadros de programação mais recentes têm suporte nativo para dados de configuração hierárquica, de modo a que a sua aplicação possa fazer uso de conjuntos específicos de configuração.

Pode organizar chaves na Configuração de Aplicações hierárquicamente de muitas maneiras. Pense em chaves como [URIs.](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier) Cada chave hierárquica é um *caminho* de recursos composto por um ou mais componentes que são unidos por delimitadores. Escolha qual o carácter a utilizar como delimitador com base no que a sua aplicação, linguagem de programação ou enquadramento necessita. Utilize vários delimitadores para teclas diferentes na Configuração da Aplicação.

Aqui estão vários exemplos de como pode estruturar os seus nomes-chave numa hierarquia:

* Com base em serviços de componentes

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* Com base em regiões de implantação

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

### <a name="label-keys"></a>Teclas de etiqueta

Os valores-chave na Configuração da App podem opcionalmente ter um atributo de etiqueta. As etiquetas são usadas para diferenciar valores-chave com a mesma tecla. Uma *aplicação chave1* com etiquetas *A* e *B* forma duas teclas separadas numa loja de configuração de aplicações. Por defeito, um valor-chave não tem etiqueta. Para referir explicitamente um valor-chave sem etiqueta, utilize `\0` (URL codificado como `%00`).

O rótulo fornece uma forma conveniente de criar variantes de uma chave. Uma utilização comum dos rótulos é especificar múltiplos ambientes para a mesma chave:

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>Valores-chave da versão

A Configuração da Aplicação não versão os valores-chave automaticamente à medida que são modificados. Utilize os rótulos como forma de criar múltiplas versões de um valor-chave. Por exemplo, pode inserir um número de versão de aplicação ou um Git cometer id nas etiquetas para identificar valores-chave associados a uma determinada construção de software.

Pode utilizar qualquer caracteres unicódigo sem exceção `*`, `,`e `\`. Estes personagens estão reservados. Para incluir um personagem reservado, deve escapar-lhe usando `\{Reserved Character}`.

### <a name="query-key-values"></a>Valores-chave de consulta

Cada valor-chave é identificado exclusivamente pela sua chave mais um rótulo que pode ser `null`. Consulta uma loja de configuração de aplicações para valores-chave, especificando um padrão. A loja de configuração de aplicações devolve todos os valores-chave que correspondem ao padrão e aos seus valores e atributos correspondentes. Utilize os seguintes padrões-chave nas chamadas REST API para configuração de aplicações:

| Chave | |
|---|---|
| `key` é omitida ou `key=*` | Corresponde a todas as chaves |
| `key=abc` | Corresponde exatamente ao nome-chave **abc** |
| `key=abc*` | Corresponde a nomes-chave que começam com **o ABC** |
| `key=abc,xyz` | Corresponde aos nomes-chave **abc** ou **xyz,** limitado a cinco CSVs |

Também pode incluir os seguintes padrões de etiqueta:

| Etiqueta | |
|---|---|
| `label` é omitida ou `label=*` | Corresponde a qualquer etiqueta, que inclui `null` |
| `label=%00` | Combina com `null` etiqueta |
| `label=1.0.0` | Etiqueta de correspondência **1.0.0** exatamente |
| `label=1.0.*` | Combina com rótulos que começam com **1.0.** |
| `label=%00,1.0.0` | Etiquetas de correspondência `null` ou **1.0.0,** limitadas a cinco CSVs |

## <a name="values"></a>Valores

Os valores atribuídos às teclas também são cordas unicódigo. Pode usar todos os caracteres unicódigo para valores. Existe um tipo de conteúdo definido pelo utilizador opcional associado a cada valor. Utilize este atributo para armazenar informações, por exemplo, um esquema de codificação, sobre um valor que ajuda a sua aplicação a processá-la corretamente.

Os dados de configuração armazenados numa loja de configuração de aplicações, que inclui todas as chaves e valores, são encriptados em repouso e em trânsito. A configuração da aplicação não é uma solução de substituição para o Cofre de Chaves Azure. Não guarde segredos de aplicação nele.

## <a name="next-steps"></a>Passos seguintes

* [Instantâneo pontual](./concept-point-time-snapshot.md)  
* [Gestão de recursos](./concept-feature-management.md)  
