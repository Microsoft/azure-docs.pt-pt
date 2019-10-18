---
title: bancada de azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy banca.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8570bce87aeea5473b4aadf9bd30bc0a648a6f0f
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518307"
---
# <a name="azcopy-bench"></a>bancada de azcopy

Executa um parâmetro de comparação de desempenho carregando dados de teste para um destino especificado. Os dados de teste são gerados automaticamente.

O comando de parâmetro de comparação executa o mesmo processo de upload como ' Copy ', exceto pelo seguinte:

  - Não há nenhum parâmetro de origem.  O comando requer apenas uma URL de destino. Na versão atual, essa URL de destino deve se referir a um contêiner de BLOB.
  
  - A carga é descrita por parâmetros de linha de comando, que controlam quantos arquivos são gerados automaticamente e qual é a grande quantidade deles. O processo de geração ocorre inteiramente na memória. O disco não é usado.
  
  - Há suporte apenas para alguns dos parâmetros opcionais disponíveis para o comando de cópia.
  
  - Diagnósticos adicionais são medidos e relatados.
  
  - Por padrão, os dados transferidos são excluídos no final da execução de teste.

O modo de parâmetro de comparação se ajustará automaticamente ao número de conexões TCP paralelas que fornece a taxa de transferência máxima. Ele exibirá esse número no final. Para evitar o ajuste automático, defina a variável de ambiente AZCOPY_CONCURRENCY_VALUE para um número específico de conexões.

Todos os tipos de autenticação usuais têm suporte. No entanto, a abordagem mais conveniente para o benchmarking normalmente é criar um contêiner vazio com um token SAS e usar a autenticação SAS.

## <a name="examples"></a>Exemplos

```azcopy
azcopy bench [destination] [flags]
```

Execute um teste de benchmark com parâmetros padrão (adequado para redes de parâmetro de comparação de até 1 Gbps): '

- azcopy bancada "https://[conta]. blob. Core. Windows. net/[contêiner]? <SAS> "

Execute um teste de benchmark que carregue 100 arquivos, cada um com 2 GiB de tamanho: (adequado para benchmarking em uma rede rápida, por exemplo, 10 Gbps): '

- azcopy bancada "https://[conta]. blob. Core. Windows. net/[contêiner]? <SAS> " --contagem de arquivos 100--tamanho-por-arquivo 2G

O mesmo que acima, mas use os arquivos 50.000, cada 8 MiB em tamanho e calcule seus hashes MD5 (da mesma forma que o sinalizador--Put-MD5 faz isso no comando de cópia). A finalidade do--Put-MD5 quando o benchmark é testar se a computação MD5 afeta a taxa de transferência para a contagem de arquivos e o tamanho selecionados:

- azcopy bancada "https://[conta]. blob. Core. Windows. net/[contêiner]? <SAS> " --contagem de arquivos 50000--tamanho-por-arquivo de 8 m--Put-MD5

## <a name="options"></a>Opções

**--blob-Type** cadeia de caracteres define o tipo de blob no destino. Usado para permitir o parâmetro de comparação de tipos diferentes de BLOB. Idêntico ao parâmetro de mesmo nome no comando de cópia (padrão "Detect").

**--Block-size-MB** float Use esse tamanho de bloco (especificado na MIB). O padrão é calculado automaticamente com base no tamanho do arquivo. Frações decimais são permitidas-por exemplo, 0,25. Idêntico ao parâmetro de mesmo nome no comando de cópia.

**--delete-test-data**  Se for true, os dados de parâmetro de comparação serão excluídos no final da execução do parâmetro de comparação.  Defina-a como false se você quiser manter os dados no destino, por exemplo, para usá-los para testes manuais fora do modo de parâmetro de comparação (padrão true).

**--File-Count** uint o número de arquivos de dados gerados automaticamente a serem usados (padrão 100).

**-h,--ajuda**  Ajuda para o bancada

**--** cadeia de caracteres de nível de log defina o detalhamento de log para o arquivo de log, níveis disponíveis: informações (todas as solicitações/respostas), aviso (respostas lentas), erro (somente solicitações com falha) e nenhum (nenhum log de saída). (padrão "INFO")

**--Put-MD5**  Crie um hash MD5 de cada arquivo e salve o hash como a propriedade Content-MD5 do blob/arquivo de destino. (Por padrão, o hash não é criado.) Idêntico ao parâmetro de mesmo nome no comando de cópia.

--tamanho da cadeia de caracteres de **tamanho por arquivo** de cada arquivo de dados gerado automaticamente. Deve ser um número imediatamente seguido por K, M ou G. por exemplo, 12K ou 200G (padrão "250M").

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

**--Cap-Mbps UInt32**  Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.

**--** formato da cadeia de caracteres do tipo de saída da saída do comando. As opções incluem: Text, JSON. O valor padrão é ' Text '. (padrão "texto").

## <a name="see-also"></a>Ver também

- [azcopy](storage-ref-azcopy.md)
