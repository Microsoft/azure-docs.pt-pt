---
title: banco de azcopy [ Microsoft Docs
description: Este artigo fornece informações de referência para o comando da bancada da azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8570bce87aeea5473b4aadf9bd30bc0a648a6f0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72518307"
---
# <a name="azcopy-bench"></a>azcopy bench

Executa uma referência de desempenho enviando dados de teste para um destino especificado. Os dados do teste são gerados automaticamente.

O comando de referência executa o mesmo processo de upload que 'copy', exceto que:

  - Não há parâmetro de origem.  O comando requer apenas um URL de destino. Na versão atual, este URL de destino deve referir-se a um recipiente de bolhas.
  
  - A carga útil é descrita por parâmetros de linha de comando, que controlam quantos ficheiros são gerados automaticamente e quão grandes são. O processo de geração ocorre inteiramente na memória. O disco não é utilizado.
  
  - Apenas alguns dos parâmetros opcionais disponíveis para o comando de cópia são suportados.
  
  - Os diagnósticos adicionais são medidos e reportados.
  
  - Por predefinição, os dados transferidos são eliminados no final do ensaio.

O modo de referência irá automaticamente sintonizar-se ao número de ligações TCP paralelas que dá a máxima potência. Mostrará esse número no final. Para evitar a afinação automática, defina a variável ambiente AZCOPY_CONCURRENCY_VALUE para um número específico de ligações.

Todos os tipos habituais de autenticação são suportados. No entanto, a abordagem mais conveniente para o benchmarking é normalmente criar um recipiente vazio com um token SAS e usar a autenticação SAS.

## <a name="examples"></a>Exemplos

```azcopy
azcopy bench [destination] [flags]
```

Ecorra um teste de referência com parâmetros predefinidos (adequado saqueado redes até 1 Gbps):»

- banco de azcopy "https://[account].blob.core.windows.net/[container]? <SAS>"

Ecorra um teste de referência que faça upload de 100 ficheiros, cada 2 GiB em tamanho: (adequado para benchmarking numa rede rápida, por exemplo, 10 Gbps):»

- banco de azcopy "https://[account].blob.core.windows.net/[container]? <SAS>" --contagem de ficheiros 100 -tamanho-por-ficheiro 2G

O mesmo que acima, mas use 50.000 ficheiros, cada 8 MiB em tamanho e calcule as suas hashes MD5 (da mesma forma que a bandeira -put-md5 faz isso no comando de cópia). O objetivo de -put-md5 quando o benchmarking é testar se o cálculo MD5 afeta a entrada para a contagem e tamanho de ficheiros selecionados:

- banco de azcopy "https://[account].blob.core.windows.net/[container]? <SAS>" -file-count 50000 --size-per-file 8M --put-md5

## <a name="options"></a>Opções

**--cadeia do tipo blob** Define o tipo de bolha no destino. Usado para permitir o benchmarking diferentes tipos de bolhas. Idêntico ao mesmo parâmetro no comando de cópia (predefinido "Detetar").

**--bloco-tamanho-mb** flutuante Utilize este tamanho de bloco (especificado no MiB). A predefinição é automaticamente calculada com base no tamanho do ficheiro. São permitidas frações decimais - por exemplo, 0,25. Idêntico ao mesmo parâmetro no comando de cópia.

**-eliminar dados de teste**  Se forem verdadeiros, os dados de referência serão eliminados no final do percurso de referência.  Desajuste-os como falsos se pretender manter os dados no destino - por exemplo, usá-lo para testes manuais fora do modo de referência (padrão verdadeiro).

**--contagem de ficheiros** UInt O número de ficheiros de dados gerados automaticamente para utilizar (padrão 100).

**-h, --ajuda**  Ajuda para o banco

**--cadeia de log-level** Defina a verbosidade do registo, níveis disponíveis: INFO (todos os pedidos/respostas), AVISO (respostas lentas), ERROR (apenas pedidos falhados) e NENHUM (sem registos de saída). ("INFO" padrão)

**-put-md5**  Crie um haxixe MD5 de cada ficheiro e guarde o haxixe como propriedade Content-MD5 da bolha/ficheiro de destino. (Por padrão, o hash NÃO é criado.) Idêntico ao mesmo parâmetro no comando de cópia.

**-tamanho por ficheiro** Tamanho de cada ficheiro de dados gerado automaticamente. Deve ser um número imediatamente seguido por K, M ou G.E.g. 12k ou 200G (padrão "250M").

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

**--cap-mbps uint32**  Cobre a taxa de transferência, em megabits por segundo. A entrada momentânea pode variar ligeiramente a partir da tampa. Se esta opção estiver definida para zero, ou for omitida, a entrada não está limitada.

**-formato** de cadeia do tipo de saída da saída do comando. As escolhas incluem: texto, json. O valor predefinido é "texto". ("texto por defeito").

## <a name="see-also"></a>Consulte também

- [azcopy](storage-ref-azcopy.md)
