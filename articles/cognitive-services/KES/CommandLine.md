---
title: Interface de linha de comandos - API de serviço de exploração de conhecimento
titlesuffix: Azure Cognitive Services
description: Utilizar a interface de linha de comandos para criar ficheiros de índice e a gramática de dados estruturados e, em seguida, implementá-las como serviços da web.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/24/2016
ms.author: paulhsu
ms.openlocfilehash: 018552982a8ece3bbbaea2d60e2a6e64f681f822
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815137"
---
# <a name="command-line-interface"></a>Interface de Linha de Comandos

A interface de linha de comando do serviço de exploração de conhecimento (KES) fornece a capacidade de criar o índice e a gramática arquivos de dados estruturados e implementá-las como serviços da web.  Ele usa a sintaxe geral: `kes.exe <command> <required_args> [<optional_args>]`.  Pode executar `kes.exe` sem argumentos para apresentar uma lista de comandos, ou `kes.exe <command>` para apresentar uma lista de argumentos disponíveis para o comando especificado.  Segue-se uma lista de comandos disponíveis:

* build_index
* build_grammar
* host_service
* deploy_service
* describe_index
* describe_grammar

<a name="build_index-command"></a>

## <a name="buildindex-command"></a>build_index comando

O **build_index** comando cria um ficheiro de índice binário a partir de um ficheiro de definição de esquema e um ficheiro de dados de objetos a ser indexados.  O ficheiro de índice resultante pode servir-se para avaliar expressões de consulta estruturadas, ou para gerar interpretações de consultas de linguagem natural em conjunto com um ficheiro de gramática compilado.

`kes.exe build_index <schemaFile> <dataFile> <indexFile> [options]`

| Parâmetro      | Descrição               |
|----------------|---------------------------|
| `<schemaFile>` | Caminho do esquema de entrada |
| `<dataFile>`   | Caminho de dados de entrada   |
| `<indexFile>`  | Caminho de índice de saída |
| `--description <description>` | Cadeia de descrição |
| `--remote <vmSize>`           | Tamanho da VM para a compilação remoto |

Estes ficheiros podem ser especificados por caminhos de ficheiro local ou de URL para blobs do Azure.  O ficheiro de esquema descreve a estrutura dos objetos estão a ser indexados, bem como as operações de suporte (consulte [formato de esquema](SchemaFormat.md)).  O ficheiro de dados enumera os objetos e os valores de atributo a ser indexados (consulte [formato de dados](DataFormat.md)).  Quando a compilação for concluída com êxito, o ficheiro de índice de saída contém uma representação comprimida dos dados de entrada que oferece suporte as operações pretendidas.  

Uma cadeia de descrição pode ser opcionalmente especificada, em seguida, identificar um índice binário, utilizando o **describe_index** comando.  

Por predefinição, o índice é criado no computador local.  Fora do ambiente do Azure, compilações locais estão limitadas a arquivos de dados com até 10 000 objetos.  Quando o-- remoto sinalizador for especificado, o índice será incorporado numa VM do Azure criado temporariamente do tamanho especificado.  Isso permite que os índices de grandes para ser criado com eficiência usando as VMs do Azure com mais memória.  Para evitar a paginação que pode atrasar o processo de compilação, recomendamos que utilize uma VM com 3 vezes a quantidade de RAM, como o tamanho do ficheiro de dados de entrada.  Para obter uma lista de tamanhos de VM disponíveis, veja [Tamanhos de máquinas virtuais](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

> [!TIP] 
> Para mais rápidas compilações, presort os objetos no ficheiro de dados, diminuindo a probabilidade.

<a name="build_grammar-command"></a>

## <a name="buildgrammar-command"></a>build_grammar comando

O **build_grammar** comando compila uma gramática especificada em XML para um ficheiro binário gramática.  O ficheiro de gramática resultante pode ser utilizado em conjunto com um ficheiro de índice para gerar interpretações de consultas de linguagem natural.

`kes.exe build_grammar <xmlFile> <grammarFile>`

| Parâmetro       | Descrição               |
|-----------------|---------------------------|
| `<xmlFile>`     | Caminho de especificação de gramática XML entrada |
| `<grammarFile>` | Caminho de gramática compilados de saída         |

Estes ficheiros podem ser especificados por caminhos de ficheiro local ou de URL para blobs do Azure.  A especificação de gramática descreve o conjunto de expressões de linguagem natural ponderada e seus interpretações semânticas (consulte [formato de gramática](GrammarFormat.md)).  Quando a compilação for concluída com êxito, o ficheiro de gramática de saída contém uma representação binária da especificação de gramática, para permitir a rápida decodificação.

<a name="host_service-command"/>

## <a name="hostservice-command"></a>host_service comando

O **host_service** comando aloja uma instância do serviço KES na máquina local.

`kes.exe host_service <grammarFile> <indexFile> [options]`

| Parâmetro       | Descrição                |
|-----------------|----------------------------|
| `<grammarFile>` | Caminho de entrada binární gramatika Je         |
| `<indexFile>`   | Caminho de entrada de índice binário           |
| `--port <port>` | Número de porta local.  Predefinição: 8000 |

Estes ficheiros podem ser especificados por caminhos de ficheiro local ou de URL para blobs do Azure.  Um serviço web será alojado no http://localhost:&lt; porta&gt;/.  Ver [Web APIs](WebAPI.md) para obter uma lista de operações com suporte.

Fora do Azure o ambiente, localmente serviços alojados estão limitados a indexar ficheiros até 1 MB de tamanho, 10 pedidos por segundo e total de chamadas de 1000.  Para superar essas limitações, execute **host_service** dentro de uma VM do Azure, ou implementar um serviço cloud do Azure a utilizar **deploy_service**.

<a name="deploy_service-command"/>

## <a name="deployservice-command"></a>deploy_service comando

O **deploy_service** comando implementa uma instância do serviço KES num serviço cloud do Azure.

`kes.exe deploy_service <grammarFile> <indexFile> <serviceName> <vmSize>[options]`

| Parâmetro       | Descrição                  |
|-----------------|------------------------------|
| `<grammarFile>` | Caminho de entrada binární gramatika Je           |
| `<indexFile>`   | Caminho de entrada de índice binário             |
| `<serviceName>` | Nome do serviço cloud de destino |
| `<vmSize>`      | Tamanho de VM de serviço de cloud     |
| `--slot <slot>` | Bloco de serviço cloud: "transição" (predefinição), "produção" |

Estes ficheiros podem ser especificados por caminhos de ficheiro local ou de URL para blobs do Azure.  Nome do serviço Especifica um serviço cloud do Azure pré-configurada (consulte [como criar e implementar um serviço Cloud](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)).  O comando irá implementar automaticamente o serviço KES no serviço de nuvem do Azure especificada, com VMs do tamanho especificado.  Para evitar a paginação que reduz significativamente o desempenho, recomendamos que utilize uma VM com 1 GB mais RAM do que o tamanho do ficheiro de entrada de índice.  Para obter uma lista de tamanhos VM disponíveis, consulte [tamanhos para os serviços Cloud](../../../articles/cloud-services/cloud-services-sizes-specs.md).

Por predefinição, o serviço está implementado o ambiente de teste, opcionalmente, substituído via o-- parâmetro de ranhura.  Ver [Web APIs](WebAPI.md) para obter uma lista de operações com suporte.

<a name="describe_index-command"/>

## <a name="describeindex-command"></a>comando describe_index

O **describe_index** comando produz informações sobre um arquivo de índice, incluindo o esquema e a descrição.

`kes.exe describe_index <indexFile>`

| Parâmetro     | Descrição      |
|---------------|------------------|
| `<indexFile>` | Caminho de entrada de índice |

Este ficheiro pode ser especificado por um caminho de ficheiro local ou um caminho de URL para um blob do Azure.  A cadeia de descrição de saída pode ser especificada utilizando o-- parâmetro de descrição do **build_index** comando.

<a name="describe_grammar-command"/>

## <a name="describegrammar-command"></a>comando describe_grammar

O **describe_grammar** comando produz a especificação de gramática original utilizada para criar a gramática binária.

`kes.exe describe_grammar <grammarFile>`

| Parâmetro       | Descrição      |
|-----------------|------------------|
| `<grammarFile>` | Caminho de entrada de gramática |

Este ficheiro pode ser especificado por um caminho de ficheiro local ou um caminho de URL para um blob do Azure.

