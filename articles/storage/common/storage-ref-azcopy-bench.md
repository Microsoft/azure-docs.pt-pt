---
title: banco de azcopia / Microsoft Docs
description: Este artigo fornece informações de referência para o comando da bancada da azcopia.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 331d0cd4a20cb4351a1bc9a204c500386c499ada
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2020
ms.locfileid: "84220150"
---
# <a name="azcopy-bench"></a>azcopy bench

Executa um benchmark de desempenho carregando dados de teste para um destino especificado. Os dados do teste são gerados automaticamente.

O comando de referência executa o mesmo processo de upload que 'copy', exceto que:

  - Não há nenhum parâmetro de origem.  O comando requer apenas uma URL de destino. Na versão atual, este URL de destino deve consultar-se a um recipiente de bolhas.
  
  - A carga útil é descrita por parâmetros da linha de comando, que controlam quantos ficheiros são gerados automaticamente e quão grandes são. O processo de geração ocorre inteiramente na memória. O disco não é usado.
  
  - Apenas alguns dos parâmetros opcionais que estão disponíveis para o comando de cópia são suportados.
  
  - Diagnósticos adicionais são medidos e reportados.
  
  - Por predefinição, os dados transferidos são eliminados no final do teste.

O modo benchmark irá sintonizar-se automaticamente com o número de ligações TCP paralelas que dá a máxima potência. Mostrará este número no final. Para evitar a afinação automática, coloque a variável ambiente AZCOPY_CONCURRENCY_VALUE num número específico de ligações.

Todos os tipos de autenticação habitual são suportados. No entanto, a abordagem mais conveniente para o benchmarking é normalmente criar um recipiente vazio com um token SAS e usar a autenticação SAS.

## <a name="examples"></a>Exemplos

```azcopy
azcopy bench [destination] [flags]
```

Executar um teste de referência com parâmetros predefinidos (adequado para redes de benchmarking até 1 Gbps):».

- banco de azcopia "https://[account].blob.core.windows.net/[contentor]? <SAS> "

Executar um teste de referência que carrega 100 ficheiros, cada um de 2 GiB em tamanho: (adequado para benchmarking numa rede rápida, por exemplo, 10 Gbps):'

- banco de azcopia "https://[account].blob.core.windows.net/[contentor]? <SAS> " --contagem de ficheiros 100 --tamanho por ficheiro 2G

O mesmo que acima, mas use 50.000 ficheiros, cada 8 MiB em tamanho e calcule os seus hashes MD5 (da mesma forma que a bandeira --put-md5 faz isso no comando de cópia). O objetivo de --put-md5 quando o benchmarking é testar se a computação MD5 afeta a produção para a contagem e tamanho de ficheiros selecionados:

- banco de azcopia "https://[account].blob.core.windows.net/[contentor]? <SAS> " ---contagem de ficheiros 50000 --tamanho-por-ficheiro 8M --put-md5

## <a name="options"></a>Opções

**---tip** string tipo blob Define o tipo de bolha no destino. Usado para permitir o benchmarking de diferentes tipos de bolhas. Idêntico ao parâmetro do mesmo nome no comando de cópia (predefinição "Detetar").

**--block-size-mb** boia Utilize este tamanho de bloco (especificado em MiB). O predefinimento é calculado automaticamente com base no tamanho do ficheiro. São permitidas frações decimais - por exemplo, 0,25. Idêntico ao parâmetro do mesmo nome no comando de cópia.

**--eliminar dados de teste**  Se forem verdadeiros, os dados de referência serão eliminados no final do período de referência.  Defina-os em falso se pretender manter os dados no destino - por exemplo, para os utilizar para testes manuais fora do modo de referência (verdade padrão).

**--contagem de ficheiros** uint O número de ficheiros de dados gerados automaticamente a utilizar (padrão 100).

**-h, -- ajuda**  Ajuda para o banco

**--cadeia de nível de log** Define a verbosidade do registo para o ficheiro de registo, níveis disponíveis: INFO (todos os pedidos/respostas), ADVERTÊNCIA (respostas lentas), ERRO (apenas pedidos falhados) e NENHUM (sem registos de saída). (predefinição "INFO")

**--put-md5**  Crie um haxixe MD5 de cada ficheiro e guarde o haxixe como propriedade do Conteúdo-MD5 da bolha/ficheiro de destino. (Por defeito, o haxixe NÃO é criado.) Idêntico ao parâmetro do mesmo nome no comando de cópia.

**...tamanho da** cadeia por ficheiro Tamanho de cada ficheiro gerado por automático. Deve ser um número imediatamente seguido por K, M ou G. E.g. 12k ou 200G (padrão "250M").

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

**--cap-mbps uint32**  Reduz a taxa de transferência, em megabits por segundo. A produção momentesa pode variar ligeiramente da tampa. Se esta opção for definida para zero, ou for omitida, a produção não está limitada.

**...-tipo de saída** formato da saída do comando. As escolhas incluem: texto, json. O valor predefinido é 'texto'. ("texto predefinido").

**--cadeia de sufixos fidedignos-microsoft-sufixos** Especifica sufixos de domínio adicionais onde podem ser enviados tokens de login do Azure Ative Directory.  O padrão é '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;* core.usgovcloudapi.net.' Qualquer listado aqui é adicionado ao padrão. Para a segurança, só deve colocar os domínios microsoft Azure aqui. Separe várias entradas com pontos e vírgulas.

## <a name="see-also"></a>Ver também

- [azcopia](storage-ref-azcopy.md)
