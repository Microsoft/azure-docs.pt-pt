---
title: banco de azcopia | Microsoft Docs
description: Este artigo fornece informações de referência para o comando da bancada da azcopia.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c1028d0a4a458746c08fd6fa4f16aa952d9962a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87282012"
---
# <a name="azcopy-benchmark"></a>referência azcopia

Executa um benchmark de desempenho carregando ou descarregando dados de teste de ou para um destino especificado. Para os uploads, os dados do teste são gerados automaticamente.

O comando de referência executa o mesmo processo que 'cópia', exceto que: 

  - Em vez de exigir parâmetros de origem e destino, o benchmark leva apenas um. Este é o recipiente blob, Azure Files Share ou Azure Data Lake Storage Gen2 sistema de ficheiros que pretende carregar ou descarregar.

  - O parâmetro 'mode' descreve se o AzCopy deve testar uploads ou downloads a partir de um determinado alvo. Os valores válidos são 'Upload' e 'Download'. O valor predefinido é 'Upload'.

  - Para os parâmetros de upload, a carga útil é descrita por parâmetros da linha de comando, que controlam quantos ficheiros são autogeridos e quão significativos são os ficheiros. O processo de geração ocorre inteiramente na memória. O disco não é usado.

  - Para downloads, a carga útil consiste em quaisquer ficheiros já existentes na fonte. (Veja o exemplo abaixo sobre como gerar ficheiros de teste, se necessário).
  
  - Apenas alguns dos parâmetros opcionais que estão disponíveis para o comando de cópia são suportados.
  
  - Diagnósticos adicionais são medidos e reportados.
  
  - Para os uploads, o comportamento predefinido é eliminar os dados transferidos no final do teste.  Para downloads, os dados nunca são guardados localmente.

O modo benchmark irá sintonizar-se automaticamente com o número de ligações TCP paralelas que dá a máxima potência. Mostrará este número no final. Para evitar a autofinar, coloque a variável ambiente AZCOPY_CONCURRENCY_VALUE num número específico de ligações. 

Todos os tipos de autenticação habitual são suportados. No entanto, a abordagem mais conveniente para o benchmarking upload é normalmente criar um recipiente vazio com um token SAS e usar a autenticação SAS. (O modo de descarregamento requer que um conjunto de dados de teste esteja presente no recipiente-alvo.)

## <a name="examples"></a>Exemplos

```azcopy
azcopy benchmark [destination] [flags]
```

Executar um teste de referência com parâmetros predefinidos (adequado para redes de benchmarking até 1 Gbps):».

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"
```
Executar um teste de referência que carrega 100 ficheiros, cada um de 2 GiB em tamanho: (adequado para benchmarking numa rede rápida, por exemplo, 10 Gbps):'

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"--file-count 100 --size-per-file 2G
```
Faça um teste de referência mas use 50.000 ficheiros, cada 8 MiB em tamanho e calcule os seus hashes MD5 (da mesma forma que a `--put-md5` bandeira faz isso no comando de cópia). O objetivo do `--put-md5` benchmarking é testar se a computação MD5 afeta a produção para a contagem e o tamanho dos ficheiros selecionados:

```azcopy
azcopy bench --mode='Upload' "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 50000 --size-per-file 8M --put-md5
```

Faça um teste de referência que descarrega ficheiros existentes a partir de um alvo

```azcopy
azcopy bench --mode='Download' "https://[account].blob.core.windows.net/[container]?<SAS?"
```

Executar um upload que não apague os ficheiros transferidos. (Estes ficheiros podem então servir como a carga útil para um teste de descarregamento)

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 100 --delete-test-data=false
```

## <a name="options"></a>Opções

**---tip** string tipo blob Define o tipo de bolha no destino. Usado para permitir o benchmarking de diferentes tipos de bolhas. Idêntico ao parâmetro do mesmo nome no comando de cópia (predefinição "Detetar").

**--block-size-mb** boia Utilize este tamanho de bloco (especificado em MiB). O predefinimento é calculado automaticamente com base no tamanho do ficheiro. Frações decimais são permitidas - por exemplo, 0,25. Idêntico ao parâmetro do mesmo nome no comando de cópia.

**--check-length**  Verifique a duração de um ficheiro no destino após a transferência. Se houver um desfasamento entre a fonte e o destino, a transferência é marcada como falhada. (verdade padrão)

**--eliminar dados de teste**  Se forem verdadeiros, os dados de referência serão eliminados no final do período de referência.  Desa cosde-o como falso se pretender manter os dados no destino - por exemplo, usá-lo para testes manuais fora do modo de referência (padrão verdadeiro).

**...-contagem de ficheiros** uint.  O número de ficheiros de dados autogerados para utilizar (padrão 100).

**...-ajuda**  Ajuda para o banco

**--cadeia de nível de log** Define a verbosidade do registo para o ficheiro de registo, níveis disponíveis: INFO (todos os pedidos/respostas), ADVERTÊNCIA (respostas lentas), ERRO (apenas pedidos falhados) e NENHUM (sem registos de saída). (predefinição "INFO")

**--cadeia de modo** Define se a Azcopy deve testar uploads ou downloads a partir deste alvo. Valores válidos são 'upload' e 'download'. A opção por defeito é 'upload'. ('upload' predefinido)

**--número de pastas** uint Se for maior que 0, crie pastas para dividir os dados.

**--put-md5**  Crie um haxixe MD5 de cada ficheiro e guarde o haxixe como propriedade do Conteúdo-MD5 da bolha/ficheiro de destino. (Por defeito, o haxixe NÃO é criado.) Idêntico ao parâmetro do mesmo nome no comando de cópia.

**...tamanho da** cadeia por ficheiro Tamanho de cada ficheiro autogerido. Deve ser um número imediatamente seguido por K, M ou G. E.g. 12k ou 200G (padrão "250M").

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

**---cap-mbps flutuar**  Reduz a taxa de transferência, em megabits por segundo. A produção momentesa pode variar ligeiramente da tampa. Se esta opção for definida para zero, ou for omitida, a produção não está limitada.

**...-tipo de saída** formato da saída do comando. As escolhas incluem: texto, json. O valor predefinido é 'texto'. ("texto predefinido").

**--cadeia de sufixos fidedignos-microsoft-sufixos** Especifica sufixos de domínio adicionais onde podem ser enviados tokens de login do Azure Ative Directory.  O padrão é '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;* core.usgovcloudapi.net.' Qualquer listado aqui é adicionado ao padrão. Para a segurança, só deve colocar os domínios microsoft Azure aqui. Separe várias entradas com pontos e vírgulas.


## <a name="see-also"></a>Ver também

- [azcopia](storage-ref-azcopy.md)
