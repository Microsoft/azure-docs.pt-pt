---
title: Compreenda a loja de valor-chave da configuração da aplicação Azure
description: Compreenda o armazenamento de valor-chave na Configuração da Aplicação Azure, que armazena os dados de configuração como valores-chave. Os valores-chave são uma representação das definições de aplicação.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: b1998532c3d9e4272d91280d57d9ea2f6e7a262c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88586397"
---
# <a name="keys-and-values"></a>Chaves e valores

A Azure App Configuration armazena dados de configuração como valores-chave. Os valores-chave são uma representação simples e flexível das definições de aplicação utilizadas pelos desenvolvedores.

## <a name="keys"></a>Chaves

As teclas servem como identificadores para valores-chave e são utilizadas para armazenar e recuperar valores correspondentes. É uma prática comum organizar chaves num espaço hierárquico de nomes usando um delimiter de carácter, como `/` ou `:` . Utilize uma convenção mais adequada à sua aplicação. A Configuração de Aplicativos trata as chaves como um todo. Não analisa as chaves para descobrir como os seus nomes são estruturados ou impor qualquer regra sobre eles.

Aqui está um exemplo de nomes-chave estruturados numa hierarquia baseada em serviços de componentes:

```aspx
    AppName:Service1:ApiEndpoint
    AppName:Service2:ApiEndpoint
```

A utilização de dados de configuração dentro dos quadros de aplicação pode ditar esquemas específicos de nomeação para valores-chave. Por exemplo, o quadro da Nuvem de primavera de Java define `Environment` recursos que fornecem configurações para uma aplicação primavera.  Estes são parametrizados por variáveis que incluem nome de *aplicação* e *perfil.* As chaves para os dados de configuração relacionados com a Nuvem de primavera normalmente começam com estes dois elementos separados por um delimiter.

As teclas armazenadas na Configuração da Aplicação são cordas sensíveis a casos e baseadas em unicódigo. As teclas *app1* e *App1* são distintas numa loja de Configuração de Aplicações. Tenha isto em mente quando utilizar as definições de configuração dentro de uma aplicação porque algumas estruturas manuseiam as chaves de configuração de forma insensível. Não recomendamos a utilização de chaves para diferenciar as teclas.

Pode utilizar qualquer personagem unico em nomes-chave, exceto `%` . Um nome chave não pode ser `.` ou `..` qualquer um. Há um limite de tamanho combinado de 10 KB num valor-chave. Este limite inclui todos os caracteres na chave, o seu valor e todos os atributos opcionais associados. Dentro deste limite, você pode ter muitos níveis hierárquicos para chaves.

### <a name="design-key-namespaces"></a>Espaços de nome chave de design

Existem duas abordagens gerais para nomear teclas usadas para dados de configuração: plana ou hierárquica. Estes métodos são semelhantes do ponto de vista da utilização da aplicação, mas o nome hierárquico oferece uma série de vantagens:

* É mais fácil de ler. Os delimiters num nome-chave hierárquico funcionam como espaços numa frase. Também proporcionam ruturas naturais entre as palavras.
* É mais fácil de gerir. Uma hierarquia de nome-chave representa grupos lógicos de dados de configuração.
* Mais fácil de usar. É mais simples escrever uma consulta que corresponda às teclas numa estrutura hierárquica e recupere apenas uma parte dos dados de configuração. Além disso, muitos quadros de programação mais recentes têm suporte nativo para dados de configuração hierárquica de modo que a sua aplicação pode fazer uso de conjuntos específicos de configuração.

Pode organizar chaves na Configuração de Aplicações de muitas maneiras. Pense em chaves como [URIs.](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier) Cada chave hierárquica é um *caminho* de recurso composto por um ou mais componentes que são unidos por delimitadores. Escolha qual o carácter a usar como umlimiter com base no que a sua aplicação, linguagem de programação ou enquadramento precisa. Utilize vários delimiters para diferentes teclas na Configuração da Aplicação.

### <a name="label-keys"></a>Chaves de etiqueta

Os valores-chave na Configuração de Aplicações podem opcionalmente ter um atributo de etiqueta. Os rótulos são usados para diferenciar valores-chave com a mesma tecla. Uma *aplicação-chave1* com as etiquetas *A* e *B* forma duas teclas separadas numa loja de Configuração de Aplicações. Por padrão, um valor-chave não tem etiqueta. Para referir explicitamente um valor-chave sem etiqueta, utilize `\0` (URL codificado como `%00` ).

O rótulo fornece uma maneira conveniente de criar variantes de uma chave. Uma utilização comum dos rótulos consiste em especificar vários ambientes para a mesma chave:

```
    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production
```

### <a name="version-key-values"></a>Valores-chave da versão

Use as etiquetas como forma de criar várias versões de um valor-chave. Por exemplo, pode inserir um número de versão de aplicação ou um Git comprometer iD nas etiquetas para identificar valores-chave associados a uma determinada construção de software.

> [!NOTE]
> Se estiver à procura de versões de alteração, a Configuração da Aplicação mantém automaticamente todas as alterações de um valor-chave ocorridos no último período de tempo. Consulte a [foto pontual](./concept-point-time-snapshot.md) para mais detalhes.

### <a name="query-key-values"></a>Valores-chave de consulta

Cada valor-chave é identificado exclusivamente pela sua chave mais um rótulo que pode ser `\0` . Consulta uma loja de configuração de aplicações para valores-chave especificando um padrão. A loja de configuração da aplicação devolve todos os valores-chave que correspondem ao padrão, incluindo os valores e atributos correspondentes. Utilize os seguintes padrões-chave nas chamadas DE API rest para a Configuração da Aplicação:

| Chave | Descrição |
|---|---|
| `key` é omitido ou `key=*` | Corresponde a todas as chaves |
| `key=abc` | Corresponde exatamente ao nome chave **abc** |
| `key=abc*` | Corresponde a nomes-chave que começam com **abc** |
| `key=abc,xyz` | Corresponde aos nomes-chave **abc** ou **xyz**. Limitado a cinco CSVs |

Também pode incluir os seguintes padrões de etiqueta:

| Etiqueta | Descrição |
|---|---|
| `label` é omitido ou `label=*` | Corresponde a qualquer rótulo, que inclui `\0` |
| `label=%00` | `\0`Etiqueta de correspondência |
| `label=1.0.0` | Corresponde a etiqueta **1.0.0** exatamente |
| `label=1.0.*` | Corresponde às etiquetas que começam com **1.0.** |
| `label=%00,1.0.0` | Etiquetas de correspondência `\0` ou **1.0.0,** limitadas a cinco CSVs |

> [!NOTE]
> `*`, `,` e `\` são personagens reservados em consultas. Se um carácter reservado for utilizado nos seus nomes-chave ou etiquetas, deve escapar-lhe `\{Reserved Character}` utilizando-o em consultas.

## <a name="values"></a>Valores

Os valores atribuídos às teclas também são cordas unicódigo. Pode utilizar todos os caracteres unicode para valores.

### <a name="use-content-type"></a>Utilizar o tipo de conteúdo
Cada valor-chave na Configuração de Aplicações tem um atributo de tipo conteúdo. Pode utilizar opcionalmente este atributo para armazenar informações sobre o tipo de valor num valor-chave que ajuda a sua aplicação a processá-lo corretamente. Pode utilizar qualquer formato para o tipo de conteúdo. A Configuração da Aplicação utiliza [tipos de mídia]( https://www.iana.org/assignments/media-types/media-types.xhtml) (também conhecidos como tipos MIME) para tipos de dados incorporados, tais como bandeiras de funcionalidades, referências do Cofre-Chave e valores-chave JSON.

## <a name="next-steps"></a>Passos seguintes

* [Instantâneo para um ponto anterior no tempo](./concept-point-time-snapshot.md)
* [Gestão de funcionalidades](./concept-feature-management.md)
* [Manipulação de eventos](./concept-app-configuration-event.md)
