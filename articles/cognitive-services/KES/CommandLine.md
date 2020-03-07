---
title: Interface de linha de comando - Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Utilize a interface da linha de comando para construir ficheiros indexados e gramaticais a partir de dados estruturados e, em seguida, implementá-los como serviços web.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/24/2016
ms.author: paulhsu
ms.openlocfilehash: 018552982a8ece3bbbaea2d60e2a6e64f681f822
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385631"
---
# <a name="command-line-interface"></a>Interface da linha de comando

A interface de linha de comando do Knowledge Exploration Service (KES) fornece a capacidade de construir ficheiros indexados e gramaticais a partir de dados estruturados e implantá-los como serviços web.  Utiliza a sintaxe geral: `kes.exe <command> <required_args> [<optional_args>]`.  Pode executá`kes.exe` sem argumentos para exibir uma lista de comandos, ou `kes.exe <command>` apresentar uma lista de argumentos disponíveis para o comando especificado.  Abaixo está uma lista de comandos disponíveis:

* build_index
* build_grammar
* host_service
* deploy_service
* describe_index
* describe_grammar

<a name="build_index-command"></a>

## <a name="build_index-command"></a>Comando build_index

O comando **build_index** constrói um ficheiro de índice binário a partir de um ficheiro de definição de esquema e um ficheiro de dados de objetos a indexar.  O ficheiro de índice resultante pode ser usado para avaliar expressões de consulta estruturadas, ou para gerar interpretações de consultas de linguagem natural em conjunto com um ficheiro gramatical compilado.

`kes.exe build_index <schemaFile> <dataFile> <indexFile> [options]`

| Parâmetro      | Descrição               |
|----------------|---------------------------|
| `<schemaFile>` | Caminho de input schema |
| `<dataFile>`   | Caminho de dados de entrada   |
| `<indexFile>`  | Trajetória do índice de saída |
| `--description <description>` | Cadeia de descrição |
| `--remote <vmSize>`           | Tamanho de VM para construção remota |

Estes ficheiros podem ser especificados por caminhos de ficheiros locais ou caminhos de URL para as bolhas Azure.  O ficheiro schema descreve a estrutura dos objetos indexados, bem como as operações a suportar (ver [Formato Schema).](SchemaFormat.md)  O ficheiro de dados enumera os valores dos objetos e atributos a indexar (ver [Formato de Dados).](DataFormat.md)  Quando a construção for bem sucedida, o ficheiro do índice de saída contém uma representação comprimido dos dados de entrada que suportam as operações desejadas.  

Uma cadeia de descrição pode ser opcionalmente especificada para identificar posteriormente um índice binário utilizando o comando **describe_index.**  

Por padrão, o índice é construído sobre a máquina local.  Fora do ambiente Azure, as construções locais limitam-se a ficheiros de dados que contenham até 10.000 objetos.  Quando a bandeira --remota for especificada, o índice será construído sobre um Azure VM temporariamente criado do tamanho especificado.  Isto permite que grandes índices sejam construídos eficientemente usando VMs Azure com mais memória.  Para evitar a paging que atrasa o processo de construção, recomendamos a utilização de um VM com 3 vezes a quantidade de RAM como o tamanho do ficheiro de dados de entrada.  Para obter uma lista de tamanhos de VM disponíveis, veja [Tamanhos de máquinas virtuais](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

> [!TIP] 
> Para construções mais rápidas, apore os objetos no ficheiro de dados diminuindo a probabilidade.

<a name="build_grammar-command"></a>

## <a name="build_grammar-command"></a>Comando build_grammar

O comando **build_grammar** compila uma gramática especificada no XML a um ficheiro gramatical binário.  O ficheiro gramatical resultante pode ser usado em conjunto com um ficheiro de índice para gerar interpretações de consultas de linguagem natural.

`kes.exe build_grammar <xmlFile> <grammarFile>`

| Parâmetro       | Descrição               |
|-----------------|---------------------------|
| `<xmlFile>`     | Input XML caminho de especificação gramatical |
| `<grammarFile>` | Saída compilada caminho gramatical         |

Estes ficheiros podem ser especificados por caminhos de ficheiros locais ou caminhos de URL para as bolhas Azure.  A especificação gramatical descreve o conjunto de expressões linguísticas naturais ponderadas e as suas interpretações semânticas (ver [Formato Gramatical).](GrammarFormat.md)  Quando a construção for bem sucedida, o ficheiro gramatical de saída contém uma representação binária da especificação gramatical para permitir a descodificação rápida.

<a name="host_service-command"/>

## <a name="host_service-command"></a>Comando host_service

O comando **host_service** acolhe uma instância do serviço KES na máquina local.

`kes.exe host_service <grammarFile> <indexFile> [options]`

| Parâmetro       | Descrição                |
|-----------------|----------------------------|
| `<grammarFile>` | Input caminho gramatical binário         |
| `<indexFile>`   | Caminho do índice binário de entrada           |
| `--port <port>` | Número do porto local.  Padrão: 8000 |

Estes ficheiros podem ser especificados por caminhos de ficheiros locais ou caminhos de URL para as bolhas Azure.  Um serviço web será hospedado na http://localhost:&lt;port&gt;/.  Consulte [as APIs da Web](WebAPI.md) para obter uma lista de operações suportadas.

Fora do ambiente Azure, os serviços hospedados localmente limitam-se a ficheiros indexados até 1 MB de tamanho, 10 pedidos por segundo e 1000 chamadas totais.  Para ultrapassar estas limitações, execute **host_service** dentro de um VM Azure, ou desloque-se para um serviço de nuvem Azure usando **deploy_service**.

<a name="deploy_service-command"/>

## <a name="deploy_service-command"></a>Comando deploy_service

O comando **deploy_service** implanta uma instância do serviço KES para um serviço de nuvem Azure.

`kes.exe deploy_service <grammarFile> <indexFile> <serviceName> <vmSize>[options]`

| Parâmetro       | Descrição                  |
|-----------------|------------------------------|
| `<grammarFile>` | Input caminho gramatical binário           |
| `<indexFile>`   | Caminho do índice binário de entrada             |
| `<serviceName>` | Nome do serviço de nuvem alvo |
| `<vmSize>`      | Tamanho do serviço de nuvem VM     |
| `--slot <slot>` | Ranhura de serviço em nuvem: "staging" (padrão), "produção" |

Estes ficheiros podem ser especificados por caminhos de ficheiros locais ou caminhos de URL para as bolhas Azure.  O nome de serviço especifica um serviço de nuvem Azure reconfigurado (ver [Como Criar e Implantar um Serviço de Cloud).](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)  O comando irá automaticamente implantar o serviço KES para o serviço de nuvem Azure especificado, utilizando VMs do tamanho especificado.  Para evitar a paging que diminui significativamente o desempenho, recomendamos a utilização de um VM com mais 1 GB de RAM do que o tamanho do ficheiro do índice de entrada.  Para obter uma lista dos tamanhos vm disponíveis, consulte [Sizes for Cloud Services](../../../articles/cloud-services/cloud-services-sizes-specs.md).

Por padrão, o serviço é implantado para o ambiente de paragem, opcionalmente ultrapassado através do parâmetro --slot.  Consulte [as APIs da Web](WebAPI.md) para obter uma lista de operações suportadas.

<a name="describe_index-command"/>

## <a name="describe_index-command"></a>comando describe_index

O **comando describe_index** produz informações sobre um ficheiro de índice, incluindo o esquema e a descrição.

`kes.exe describe_index <indexFile>`

| Parâmetro     | Descrição      |
|---------------|------------------|
| `<indexFile>` | Caminho do índice de entrada |

Este ficheiro pode ser especificado por um caminho de ficheiro local ou por um caminho URL para uma bolha Azure.  A cadeia de descrição da saída pode ser especificada utilizando o parâmetro -descrição do comando **build_index.**

<a name="describe_grammar-command"/>

## <a name="describe_grammar-command"></a>comando describe_grammar

O **comando describe_grammar** produz a especificação gramatical original usada para construir a gramática binária.

`kes.exe describe_grammar <grammarFile>`

| Parâmetro       | Descrição      |
|-----------------|------------------|
| `<grammarFile>` | Caminho gramatical de entrada |

Este ficheiro pode ser especificado por um caminho de ficheiro local ou por um caminho URL para uma bolha Azure.

