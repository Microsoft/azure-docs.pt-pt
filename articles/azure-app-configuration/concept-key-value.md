---
title: Compreenda a loja de valor-chave da configuração da aplicação Azure
description: Compreenda como os dados de configuração são armazenados na Configuração da Aplicação Azure.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: b8f8bda52be63a4176411855dd9ff9919e9e31f5
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85856680"
---
# <a name="keys-and-values"></a>Chaves e valores

A Azure App Configuration armazena dados de configuração como pares de valor-chave. Os pares de valores-chave são uma representação simples e flexível das definições de aplicações usadas pelos desenvolvedores.

## <a name="keys"></a>Chaves

As teclas servem como identificadores para pares de valores-chave e são usadas para armazenar e recuperar valores correspondentes. É uma prática comum organizar chaves num espaço hierárquico de nomes usando um delimiter de carácter, como `/` ou `:` . Utilize uma convenção mais adequada à sua aplicação. A Configuração de Aplicativos trata as chaves como um todo. Não analisa as chaves para descobrir como os seus nomes são estruturados ou impor qualquer regra sobre eles.

Aqui estão dois exemplos de nomes-chave estruturados numa hierarquia:

* Com base em serviços de componentes

```aspx
        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint
```

* Com base em regiões de implantação

```aspx
        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint
```

A utilização de dados de configuração dentro dos quadros de aplicação pode ditar esquemas específicos de nomeação para valores-chave. Por exemplo, o quadro da Nuvem de primavera de Java define `Environment` recursos que fornecem configurações para uma aplicação primavera.  Estes são parametrizados por variáveis que incluem nome de *aplicação* e *perfil.* As chaves para os dados de configuração relacionados com a Nuvem de primavera normalmente começam com estes dois elementos separados por um delimiter.

As teclas armazenadas na Configuração da Aplicação são cordas sensíveis a casos e baseadas em unicódigo. As teclas *app1* e *App1* são distintas numa loja de Configuração de Aplicações. Tenha isto em mente quando utilizar as definições de configuração dentro de uma aplicação porque algumas estruturas manuseiam as chaves de configuração de forma insensível. Não recomendamos a utilização de chaves para diferenciar as teclas.

Pode utilizar qualquer personagem unico em nomes-chave, exceto `*` `,` , e `\` .  Se precisar de incluir um destes caracteres reservados, escape-o `\{Reserved Character}` utilizando. 

Há um limite de tamanho combinado de 10 KB num par de valores-chave. Este limite inclui todos os caracteres na chave, o seu valor e todos os atributos opcionais associados. Dentro deste limite, você pode ter muitos níveis hierárquicos para chaves.

### <a name="design-key-namespaces"></a>Espaços de nome chave de design

Existem duas abordagens gerais para nomear teclas usadas para dados de configuração: plana ou hierárquica. Estes métodos são semelhantes do ponto de vista da utilização da aplicação, mas o nome hierárquico oferece uma série de vantagens:

* É mais fácil de ler. Os delimiters num nome-chave hierárquico funcionam como espaços numa frase. Também proporcionam ruturas naturais entre as palavras.
* É mais fácil de gerir. Uma hierarquia de nome-chave representa grupos lógicos de dados de configuração.
* Mais fácil de usar. É mais simples escrever uma consulta que corresponda às teclas numa estrutura hierárquica e recupere apenas uma parte dos dados de configuração. Além disso, muitos quadros de programação mais recentes têm suporte nativo para dados de configuração hierárquica de modo que a sua aplicação pode fazer uso de conjuntos específicos de configuração.

Pode organizar chaves na Configuração de Aplicações de muitas maneiras. Pense em chaves como [URIs.](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier) Cada chave hierárquica é um *caminho* de recurso composto por um ou mais componentes que são unidos por delimitadores. Escolha qual o carácter a usar como umlimiter com base no que a sua aplicação, linguagem de programação ou enquadramento precisa. Utilize vários delimiters para diferentes teclas na Configuração da Aplicação.

### <a name="label-keys"></a>Chaves de etiqueta

Os valores-chave na Configuração de Aplicações podem opcionalmente ter um atributo de etiqueta. Os rótulos são usados para diferenciar valores-chave com a mesma tecla. Uma *aplicação-chave1* com as etiquetas *A* e *B* forma duas teclas separadas numa loja de Configuração de Aplicações. Por predefinição, um valor chave não tem etiqueta. Para referir explicitamente um valor-chave sem etiqueta, utilize `\0` (URL codificado como `%00` ).

O rótulo fornece uma maneira conveniente de criar variantes de uma chave. Uma utilização comum dos rótulos consiste em especificar vários ambientes para a mesma chave:

```aspx
    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production
```

### <a name="version-key-values"></a>Valores-chave da versão

A Configuração da Aplicação não introduz valores-chave automaticamente. Use as etiquetas como forma de criar várias versões de um valor chave. Por exemplo, pode inserir um número de versão de aplicação ou um Git cometer ID nas etiquetas para identificar valores-chave associados a uma determinada construção de software.

Pode utilizar qualquer personagem unicode nas etiquetas `*` exceto , `,` e `\` . Estes personagens estão reservados. Para incluir um carácter reservado, deve escapar-lhe `\{Reserved Character}` utilizando.

### <a name="query-key-values"></a>Valores-chave de consulta

Cada valor chave é identificado exclusivamente pela sua chave mais um rótulo que pode ser `null` . Consulta uma loja de configuração de aplicações para valores-chave especificando um padrão. A loja de Configuração de Aplicações devolve todos os valores-chave que correspondem ao padrão e aos seus valores e atributos correspondentes. Utilize os seguintes padrões-chave nas chamadas DE API rest para a Configuração da Aplicação:

| Chave | |
|---|---|
| `key`é omitido ou`key=*` | Corresponde a todas as chaves |
| `key=abc` | Corresponde exatamente ao nome chave **abc** |
| `key=abc*` | Corresponde a nomes-chave que começam com **abc** |
| `key=abc,xyz` | Corresponde aos nomes-chave **abc** ou **xyz**. Limitado a cinco CSVs |

Também pode incluir os seguintes padrões de etiqueta:

| Etiqueta | |
|---|---|
| `label`é omitido ou`label=*` | Corresponde a qualquer rótulo, que inclui`null` |
| `label=%00` | `null`Etiqueta de correspondência |
| `label=1.0.0` | Corresponde a etiqueta **1.0.0** exatamente |
| `label=1.0.*` | Corresponde às etiquetas que começam com **1.0.** |
| `label=%00,1.0.0` | Etiquetas de correspondência `null` ou **1.0.0,** limitadas a cinco CSVs |

## <a name="values"></a>Valores

Os valores atribuídos às teclas também são cordas unicódigo. Pode utilizar todos os caracteres unicode para valores. Há um tipo de conteúdo definido pelo utilizador opcional associado a cada valor. Utilize este atributo para armazenar informações sobre um valor que ajuda a sua aplicação a processá-la corretamente.

Os dados de configuração armazenados numa loja de Configuração de Aplicações são encriptados em repouso e em trânsito. As chaves não estão encriptadas em repouso. A Configuração de Aplicativos não é uma solução de substituição para o Cofre da Chave Azure. Não guarde segredos de aplicações nele.

## <a name="next-steps"></a>Próximos passos

* [Instantâneo para um ponto anterior no tempo](./concept-point-time-snapshot.md)  
* [Gestão de funcionalidades](./concept-feature-management.md)  
