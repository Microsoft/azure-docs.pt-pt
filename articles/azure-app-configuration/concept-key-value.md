---
title: Chave de configuração do Azure App-repositório de valores | Microsoft Docs
description: Uma visão geral de como os dados de configuração são armazenados na configuração do Azure App
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: 054de294c9edb0fe5b75da4ac7fd54ce987123de
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185227"
---
# <a name="keys-and-values"></a>Chaves e valores

Azure App configuração armazena dados de configuração como pares chave-valor. Os pares chave-valor são uma maneira simples, porém flexível, de representar vários tipos de configurações de aplicativo com as quais os desenvolvedores estão familiarizados.

## <a name="keys"></a>Chaves

As chaves servem como o nome para pares de chave-valor e são usadas para armazenar e recuperar valores correspondentes. É uma prática comum organizar chaves em um namespace hierárquico usando um delimitador de caracteres, como `/` ou `:`. Use uma convenção mais adequada para seu aplicativo. A configuração do aplicativo trata as chaves como um todo. Ele não analisa as chaves para descobrir como seus nomes são estruturados ou impõem qualquer regra neles.

O uso de dados de configuração em estruturas de aplicativo pode ditar esquemas de nomenclatura específicos para valores de chave. Por exemplo, a estrutura Spring Cloud do Java define `Environment` recursos que fornecem configurações para um aplicativo Spring a serem parametrizados por variáveis que incluem o *nome do aplicativo* e o *perfil*. As chaves para dados de configuração relacionados à nuvem Spring normalmente começam com esses dois elementos separados por um delimitador.

As chaves armazenadas na configuração do aplicativo diferenciam maiúsculas de minúsculas, cadeias de caracteres baseadas em Unicode. As chaves *App1* e *App1* são distintas em um repositório de configuração de aplicativo. Tenha isso em mente ao usar as definições de configuração em um aplicativo, pois algumas estruturas lidam com chaves de configuração sem diferenciação de maiúsculas e minúsculas. Por exemplo, o sistema de configuração ASP.NET Core trata as chaves como cadeias de caracteres que não diferenciam maiúsculas de minúsculas. Para evitar comportamentos imprevisíveis ao consultar a configuração de aplicativo em um aplicativo ASP.NET Core, não use chaves que diferem apenas por maiúsculas e minúsculas.

Você pode usar qualquer caractere Unicode em nomes de chave inseridos na configuração do aplicativo, exceto por `*`, `,`e `\`. Esses caracteres são reservados. Se precisar incluir um caractere reservado, você deverá escapar dele usando `\{Reserved Character}`. Há um limite de tamanho combinado de 10.000 caracteres em um par chave-valor. Esse limite inclui todos os caracteres na chave, seu valor e todos os atributos opcionais associados. Dentro desse limite, você pode ter muitos níveis hierárquicos para chaves.

### <a name="design-key-namespaces"></a>Namespaces de chave de design

Há duas abordagens gerais para nomear chaves usadas para dados de configuração: simples ou hierárquicas. Esses métodos são semelhantes de um ponto de vista de uso do aplicativo, mas a nomenclatura hierárquica oferece várias vantagens:

* Mais fácil de ler. Em vez de uma longa sequência de caracteres, os delimitadores em uma função de nome de chave hierárquica como espaços em uma sentença. Eles também fornecem quebras naturais entre palavras.
* Mais fácil de gerenciar. Uma hierarquia de nome de chave representa grupos lógicos de dados de configuração.
* Mais fácil de usar. É mais simples escrever uma consulta que corresponda ao padrão de chaves em uma estrutura hierárquica e recupera apenas uma parte dos dados de configuração. Além disso, muitas estruturas de programação mais recentes têm suporte nativo para dados de configuração hierárquicas, de modo que seu aplicativo possa fazer uso de conjuntos específicos de configuração.

Você pode organizar as chaves na configuração de aplicativo hierarquicamente de várias maneiras. Considere essas chaves como [URIs](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Cada chave hierárquica é um *caminho* de recurso composto por um ou mais componentes Unidos por delimitadores. Escolha qual caractere usar como um delimitador com base no que seu aplicativo, linguagem de programação ou estrutura precisa. Use vários delimitadores para chaves diferentes na configuração do aplicativo.

Aqui estão vários exemplos de como você pode estruturar seus nomes de chave em uma hierarquia:

* Com base nos serviços de componentes

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* Com base nas regiões de implantação

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

### <a name="label-keys"></a>Chaves de rótulo

Os valores de chave na configuração de aplicativo podem, opcionalmente, ter um atributo de rótulo. Os rótulos são usados para diferenciar valores de chave com a mesma chave. Uma chave *App1* com rótulos *A* e *B* forma duas chaves separadas em um repositório de configuração de aplicativo. Por padrão, o rótulo para um valor de chave está vazio ou `null`.

O rótulo fornece uma maneira conveniente de criar variantes de uma chave. Um uso comum de rótulos é especificar vários ambientes para a mesma chave:

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>Valores de chave de versão

A configuração de aplicativo não valores de chave de versão automaticamente à medida que são modificados. Use rótulos como uma maneira de criar várias versões de um valor de chave. Por exemplo, você pode inserir um número de versão do aplicativo ou uma ID de confirmação git em rótulos para identificar os valores de chave associados a uma compilação de software específica.

Você pode usar qualquer caractere Unicode em rótulos, exceto `*`, `,`e `\`. Esses caracteres são reservados. Para incluir um caractere reservado, você deve escapar dele usando `\{Reserved Character}`.

### <a name="query-key-values"></a>Valores de chave de consulta

Cada valor de chave é identificado exclusivamente por sua chave, mais um rótulo que pode ser `null`. Você consulta um repositório de configuração de aplicativo para obter valores de chave especificando um padrão. O repositório de configuração de aplicativo retorna todos os valores de chave que correspondem ao padrão e seus valores e atributos correspondentes. Use os seguintes padrões de chave em chamadas à API REST para a configuração do aplicativo:

| Chave | |
|---|---|
| `key` é omitido ou `key=*` | Corresponde a todas as chaves |
| `key=abc` | Corresponde ao nome da chave **ABC** exatamente |
| `key=abc*` | Corresponde aos nomes de chave que começam com **ABC** |
| `key=*abc` | Corresponde aos nomes de chave que terminam com **ABC** |
| `key=*abc*` | Corresponde aos nomes de chave que contêm **ABC** |
| `key=abc,xyz` | Corresponde aos nomes de chave **ABC** ou **XYZ**, limitado a cinco CSVs |

Você também pode incluir os seguintes padrões de rótulo:

| Etiqueta | |
|---|---|
| `label` é omitido ou `label=*` | Corresponde a qualquer rótulo, que inclui `null` |
| `label=%00` | Corresponde `null` rótulo |
| `label=1.0.0` | Corresponde exatamente ao rótulo **1.0.0** |
| `label=1.0.*` | Faz a correspondência de rótulos que começam com **1,0.** |
| `label=*.0.0` | Faz a correspondência de rótulos que terminam com **. 0,0** |
| `label=*.0.*` | Faz a correspondência de rótulos que contêm **. 0.** |
| `label=%00,1.0.0` | Corresponde aos rótulos `null` ou **1.0.0**, limitado a cinco CSVs |

## <a name="values"></a>Valores

Os valores atribuídos às chaves também são cadeias de caracteres Unicode. Você pode usar todos os caracteres Unicode para valores. Há um tipo de conteúdo opcional definido pelo usuário associado a cada valor. Use esse atributo para armazenar informações, por exemplo, um esquema de codificação, sobre um valor que ajuda seu aplicativo a processá-lo corretamente.

Os dados de configuração armazenados em um repositório de configuração de aplicativo, que inclui todas as chaves e valores, são criptografados em repouso e em trânsito. A configuração do aplicativo não é uma solução de substituição para Azure Key Vault. Não armazene os segredos do aplicativo nele.

## <a name="next-steps"></a>Passos seguintes

* [Instantâneo point-in-time](./concept-point-time-snapshot.md)  
* [Gerenciamento de recursos](./concept-feature-management.md)  
