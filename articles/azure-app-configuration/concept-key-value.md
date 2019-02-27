---
title: Arquivo de chave-valor de configuração de aplicações do Azure | Documentos da Microsoft
description: Uma visão geral de como os dados de configuração são armazenados na configuração de aplicações do Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: f04067358b0b2bae745727a5dd7a1f5554f9f70e
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884820"
---
# <a name="key-value-store"></a>Arquivo de chave-valor

Configuração de aplicações do Azure armazena dados de configuração como pares chave-valor, uma forma simples e flexível de representar vários tipos de configurações de aplicativo que os desenvolvedores estão familiarizados.

## <a name="keys"></a>Chaves

Chaves de servem como o nome de pares chave-valor e são utilizadas para armazenar e obter os valores correspondentes. É uma prática comum para organizar as chaves num espaço de nomes hierárquico utilizando um delimitador de caracteres (por exemplo, `/` ou `:`) com base numa convenção de que é mais adequada para a sua aplicação. Configuração de aplicações trata chaves como um todo. Eles não analisar as chaves para descobrir como seus nomes são estruturados ou impõem qualquer regra nos mesmos.

A utilização do arquivo de configuração dentro de estruturas de aplicações pode estabelecer esquemas de nomenclatura específicas para os valores de chave. Por exemplo, define a estrutura de Spring Cloud do Java `Environment` recursos que fornecem definições para uma aplicação de Spring para ser parametrizadas por variáveis, incluindo *nome da aplicação* e *perfil*. Chaves para Spring associada relacionados com a configuração de dados, normalmente, começará com estes dois elementos, separadamente por um delimitador.

Chaves armazenadas em configuração de aplicações são cadeias de caracteres de maiúsculas e minúsculas, com base em unicode. As chaves *app1* e *App1* são diferentes num arquivo de configuração de aplicação. Tenha isso em mente ao utilizar as definições de configuração dentro de um aplicativo, uma vez que algumas estruturas de lidar com case-insensitively chaves de configuração. Por exemplo, o sistema de configuração do ASP.NET Core trata chaves como cadeias de caracteres de maiúsculas e minúsculas. Para evitar imprevisíveis comportamentos ao consultar a configuração de aplicações dentro de uma aplicação ASP.NET Core, as chaves que diferem apenas por letras maiúsculas e minúsculas não devem ser utilizadas.

Pode usar qualquer caráter unicode em nomes de chaves inseridos na configuração de aplicações, exceto para `*`, `,` e `\` que estão reservado. Se tiver de incluir um caractere reservado, tem de escape usando `\{Reserved Character}`. Existe um limite de tamanho combinado de carateres de 10 mil num par chave-valor. Isto inclui todos os caracteres na chave, seu valor, e todas as associadas atributos opcionais. Dentro deste limite, pode ter vários níveis hierárquicos para as chaves.

### <a name="designing-key-namespaces"></a>Estruturar espaços de nomes de chaves

Existem duas abordagens gerais para as chaves utilizadas para dados de configuração de nomenclatura: simples ou hierárquico. Esses métodos são muito semelhantes de um ponto de vista de utilização do aplicativo, mas a segunda opção oferece uma série de vantagens:

* Mais fácil de ler. Em vez de uma sequência de longa de caracteres, delimitadores num nome de chave hierárquico funciona como espaços numa frase e fornece quebras naturais entre as palavras.
* Mais fácil de gerenciar. Uma hierarquia de nome da chave representa grupos lógicos de dados de configuração.
* Mais fácil de usar. É mais simples de escrever uma consulta que correspondências de padrão de chaves numa estrutura hierárquica e obtém apenas uma parte de dados de configuração. Além disso, muitas estruturas de programação mais recente tem suporte nativo para dados de configuração hierárquica, de modo que seu aplicativo pode fazer uso de conjuntos específicos de configuração.

Pode organizar as chaves na configuração de aplicação hierarquicamente de várias maneiras. Pode pensar estas chaves como [URIs](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Cada chave hierárquica é um recurso *caminho* composto por um ou mais componentes, unidas por delimitadores. Pode escolher quais caracteres para utilizar como um delimitador com base no que seu aplicativo, linguagem ou arquitetura de programação precisam. Pode usar vários delimitadores para chaves diferentes na configuração de aplicações.

Seguem-se vários exemplos de como pode estruturar seus nomes de chaves para uma hierarquia:

* Com base em ambientes

        AppName:Test:DB:Endpoint
        AppName:Staging:DB:Endpoint
        AppName:Production:DB:Endpoint

* Com base nos serviços de componentes

        AppName:Service1:Test:DB:Endpoint
        AppName:Service1:Staging:DB:Endpoint
        AppName:Service1:Production:DB:Endpoint
        AppName:Service2:Test:DB:Endpoint
        AppName:Service2:Staging:DB:Endpoint
        AppName:Service2:Production:DB:Endpoint

* Baseadas nas regiões de implementação

        AppName:Production:Region1:DB:Endpoint
        AppName:Production:Region2:DB:Endpoint

### <a name="versioning-key-values"></a>Valores de chave de controle de versão

Valores de chave na configuração de aplicações, opcionalmente, podem ter uma **etiqueta** atributo. Rótulos são usados para diferenciar os valores de chave com a mesma chave. Uma chave *app1* com as etiquetas *v1* e *v2* duas chaves-valores separados num arquivo de configuração de aplicação de formulário. Por predefinição, a etiqueta de uma chave-valor está vazia (ou `null`).

Configuração de aplicação faz não chaves-valores da versão automaticamente à medida que são modificados. Pode utilizar etiquetas como uma forma de criar várias versões de um valor de chave. Por exemplo, pode inserir um número de versão do aplicativo ou um ID de consolidação de Git em etiquetas para identificar os valores de chave associados a uma compilação de software específico.

Tem permissão para utilizar qualquer caráter unicode em etiquetas, exceto para `*`, `,` e `\`, que são reservadas. Se tiver de incluir um caractere reservado, tem de escape usando `\{Reserved Character}`.

### <a name="querying-key-values"></a>Consultar valores de chave

Cada chave-valor é identificado exclusivamente pela respetiva chave além de uma etiqueta que pode ser `null`. Consultar um arquivo de configuração de aplicação para os valores de chave ao especificar um padrão. O armazenamento de configuração de aplicação irá devolver todos os valores de chave corresponde ao padrão e suas correspondentes valores e atributos. Pode utilizar os seguintes padrões de chave em chamadas de REST API para configuração de aplicações:

| Chave | |
|---|---|
| `key` for omitido ou `key=*` | Corresponde a todas as chaves |
| `key=abc` | Nome da chave de correspondências **abc** exatamente. |
| `key=abc*` | Correspondências de nomes que começam por chave **abc** |
| `key=*abc` | Correspondências de nomes que terminam com chaves **abc** |
| `key=*abc*` | Correspondências de nomes que contêm chaves **abc** |
| `key=abc,xyz` | Nomes de chave de correspondências **abc** ou **xyz**, limitado a 5 CSVs |

Também pode incluir os seguintes padrões de etiqueta:

| Label | |
|---|---|
| `label` for omitido ou `label=*` | Corresponde a qualquer etiqueta, incluindo `null` |
| `label=%00` | Correspondências `null` etiqueta. |
| `label=1.0.0` | Etiqueta de correspondências **1.0.0** exatamente |
| `label=1.0.*` | Corresponde a etiquetas que começam com **1.0.** |
| `label=*.0.0` | Corresponde a etiquetas que terminam com **.0.0** |
| `label=*.0.*` | Corresponde a etiquetas que contêm **.0.** |
| `label=%00,1.0.0` | Corresponde a etiquetas `null` ou **1.0.1**, limitado a 5 CSVs |

## <a name="values"></a>Valores

Valores atribuídos às chaves também são cadeias de caracteres unicode. Pode usar todos os caracteres unicode para valores. Existe um opcional definido pelo utilizador **tipo de conteúdo** associados a cada valor. Pode utilizar esse atributo para armazenar informações (por exemplo, esquema de codificação) sobre um valor que ajuda a sua aplicação para processá-lo adequadamente.

Dados de configuração armazenados num arquivo de configuração de aplicação, incluindo todas as chaves e valores, são encriptados em descanso e em trânsito. Não obstante a configuração de aplicações não é uma solução de substituição para o Azure Key Vault. Não deve armazenar segredos da aplicação no mesmo.

## <a name="next-steps"></a>Passos Seguintes

* [Conceito: Instantâneo de ponto no tempo](concept-point-time-snapshot.md)  
