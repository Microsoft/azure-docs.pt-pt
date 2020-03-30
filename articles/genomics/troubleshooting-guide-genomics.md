---
title: Guia de Resolução de Problemas
titleSuffix: Microsoft Genomics
description: Conheça estratégias de resolução de problemas para a utilização da Microsoft Genomics, incluindo mensagens de erro e como resolvê-las.
keywords: resolução de problemas, erro, depuração
services: genomics
author: ruchir
editor: jasonwhowell
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.openlocfilehash: f6ef56e4188a7541036db096e4ab35a1b95fc141
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73486007"
---
# <a name="troubleshooting-guide"></a>Guia de resolução de problemas

Aqui estão algumas dicas de resolução de problemas para alguns dos problemas comuns que você pode enfrentar ao usar o serviço Microsoft Genomics, MSGEN.

 Para as FAQ, não relacionadas com a resolução de problemas, consulte [questões comuns.](frequently-asked-questions-genomics.md)
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>Passo 1: Localizar códigos de erro associados ao fluxo de trabalho

Pode localizar as mensagens de erro associadas ao fluxo de trabalho por:

1. Usando a linha de comando e digitando`msgen status`
2. Examinando o conteúdo da produção padrão.txt.

### <a name="1-using-the-command-line-msgen-status"></a>1. Utilizando a linha de comando`msgen status`

```bash
msgen status -u URL -k KEY -w ID 
```




Existem três argumentos necessários:

* URL - a base URI para a API
* KEY - a chave de acesso para a sua conta Genómica
    * Para encontrar o seu URL e KEY, vá ao portal Azure e abra a sua página de conta Microsoft Genomics. Sob a rubrica **Gestão,** escolha **as teclas de acesso**. Aí, encontras tanto o URL da API como as tuas chaves de acesso.

  
* ID - o ID do fluxo de trabalho
    * Para encontrar o seu `msgen list` tipo de ID de fluxo de trabalho no comando. Assumindo que o seu ficheiro config contém o URL e as suas chaves de acesso, e está localizado no mesmo local que o seu exe msgen, o comando será assim: 
        
        ```bash
        msgen list -f "config.txt"
        ```
        A saída deste comando será assim:
        
        ```bash
            Microsoft Genomics command-line client v0.7.4
                Copyright (c) 2018 Microsoft. All rights reserved.
                
                Workflow List
                -------------
                Total Count  : 1
                
                Workflow ID     : 10001
                Status          : Completed successfully
                Message         :
                Process         : snapgatk-20180730_1
                Description     :
                Created Date    : Mon, 27 Aug 2018 20:27:24 GMT
                End Date        : Mon, 27 Aug 2018 20:55:12 GMT
                Wall Clock Time : 0h 27m 48s
                Bases Processed : 1,348,613,600 (1 GBase)
        ```

  > [!NOTE]
  >  Em alternativa, pode incluir o caminho para o ficheiro config em vez de introduzir diretamente o URL e o KEY. Se incluir estes argumentos na linha de comando, bem como o ficheiro config, os argumentos da linha de comando terão precedência.  

Para o workflow ID 1001, e ficheiro config.txt colocado no mesmo caminho que o msgen executável, o comando será assim:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2. Examinar o conteúdo da standardoutput.txt 
Localize o recipiente de saída para o fluxo de trabalho em questão. A MSGEN `[workflowfilename].logs.zip` cria uma pasta após cada execução de fluxo de trabalho. Desapertar a pasta para ver o seu conteúdo:

* outputFileList.txt - uma lista dos ficheiros de saída produzidos durante o fluxo de trabalho
* standarderror.txt - este ficheiro está em branco.
* standardoutput.txt - regista todas as mensagens de estado de alto nível, incluindo erros, que ocorreram durante o funcionamento do fluxo de trabalho.
* Ficheiros de registo GATK `logs` - todos os outros ficheiros na pasta

Para resolução de problemas, examine o conteúdo da saída padrão.txt e note quaisquer mensagens de erro que apareçam.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>Passo 2: Tente passos recomendados para erros comuns

Esta secção destaca brevemente a saída de erros comuns pelo serviço Microsoft Genomics (msgen) e as estratégias que pode utilizar para resolvê-los. 

O serviço Microsoft Genomics (msgen) pode lançar os seguintes dois tipos de erros:

1. Erros de Serviço Interno: Erros internos ao serviço, que podem não ser resolvidos através da fixação de parâmetros ou ficheiros de entrada. Por vezes, reenviar o fluxo de trabalho pode corrigir estes erros.
2. Erros de entrada: Erros que podem ser resolvidos utilizando os argumentos corretos ou fixando formatos de ficheiros.

### <a name="1-internal-service-errors"></a>1. Erros de serviço interno

Um erro de serviço interno não é exequível pelo utilizador. Pode reenviar o fluxo de trabalho, mas se isso não funcionar, contacte o suporte da Microsoft Genomics

| Mensagem de erro                                                                                                                            | Passos de resolução de problemas recomendados                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ocorreu um erro interno. Tente reenviar o fluxo de trabalho. Se voltar a ver este erro, contacte o suporte da Microsoft Genomics para obter assistência | Submeta o fluxo de trabalho novamente. Contacte o suporte da Microsoft Genomics para assistência se o problema persistir através da criação de um [bilhete](file-support-ticket-genomics.md )de suporte . |

### <a name="2-input-errors"></a>2. Erros de entrada

Estes erros são utilizáveis pelo utilizador. Com base no tipo de ficheiro e código de erro, o serviço Microsoft Genomics produz códigos de erro distintos. Siga os passos recomendados de resolução de problemas listados abaixo.

| Tipo de arquivo | Código de erro | Mensagem de erro                                                                           | Passos de resolução de problemas recomendados                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Qualquer          | 701        | Ler [readId] tem bases [númeroSOfBases], mas o limite é [maxReadLength]           | A razão mais comum para este erro é a corrupção que leva à concatenação de duas leituras. Verifique os seus ficheiros de entrada. |
| BAM          | 200        |   Incapaz de ler o ficheiro '[o nome do ficheiro]'.                                                                                       | Verifique o formato do ficheiro BAM. Submeta o fluxo de trabalho novamente com um ficheiro devidamente formatado.                                                                           |
| BAM          | 201        |  Incapaz de ler ficheiro BAM [File_name].                                                                                      |Verifique o formato do ficheiro BAM.  Submeta o fluxo de trabalho com um ficheiro devidamente formatado.                                                                            |
| BAM          | 202        | Incapaz de ler ficheiro BAM [File_name]. Arquiva o cabeceamento muito pequeno e desaparecido.                                                                                        | Verifique o formato do ficheiro BAM.  Submeta o fluxo de trabalho com um ficheiro devidamente formatado.                                                                            |
| BAM          | 203        |   Incapaz de ler ficheiro BAM [File_name]. O cabeçalho do ficheiro era corrupto.                                                                                      |Verifique o formato do ficheiro BAM.  Submeta o fluxo de trabalho com um ficheiro devidamente formatado.                                                                           |
| BAM          | 204        |    Incapaz de ler ficheiro BAM [File_name]. O cabeçalho do ficheiro era corrupto.                                                                                     | Verifique o formato do ficheiro BAM.  Submeta o fluxo de trabalho com um ficheiro devidamente formatado.                                                                           |
| BAM          | 205        |    Incapaz de ler ficheiro BAM [File_name]. O cabeçalho do ficheiro era corrupto.                                                                                     | Verifique o formato do ficheiro BAM.  Submeta o fluxo de trabalho com um ficheiro devidamente formatado.                                                                            |
| BAM          | 206        |   Incapaz de ler ficheiro BAM [File_name]. O cabeçalho do ficheiro era corrupto.                                                                                      | Verifique o formato do ficheiro BAM.  Submeta o fluxo de trabalho com um ficheiro devidamente formatado.                                                                            |
| BAM          | 207        |  Incapaz de ler ficheiro BAM [File_name]. Ficheiro truncado perto de offset [offset].                                                                                       | Verifique o formato do ficheiro BAM.  Submeta o fluxo de trabalho com um ficheiro devidamente formatado.                                                                            |
| BAM          | 208        |   Ficheiro BAM inválido. O ReadID [Read_Id] não tem sequência no ficheiro [File_name].                                                                                      | Verifique o formato do ficheiro BAM.  Submeta o fluxo de trabalho com um ficheiro devidamente formatado.                                                                             |
| FASTQ        | 300        |  Incapaz de ler o ficheiro FASTQ. [File_name] não termina com uma nova linha.                                                                                     | Corrija o formato do ficheiro FASTQ e volte a submeter o fluxo de trabalho.                                                                           |
| FASTQ        | 301        |   Incapaz de ler ficheiro FASTQ [File_name]. O recorde FASTQ é maior do que o tamanho do tampão em offset: [_offset]                                                                                      | Corrija o formato do ficheiro FASTQ e volte a submeter o fluxo de trabalho.                                                                         |
| FASTQ        | 302        |     Erro de Sintaxe FASTQ. O ficheiro [File_name] tem uma linha em branco.                                                                                    | Corrija o formato do ficheiro FASTQ e volte a submeter o fluxo de trabalho.                                                                         |
| FASTQ        | 303        |       Erro de Sintaxe FASTQ. O ficheiro[File_name] tem um carácter inicial inválido em offset: [_offset], tipo de linha: [line_type], personagem: [_char]                                                                                  | Corrija o formato do ficheiro FASTQ e volte a submeter o fluxo de trabalho.                                                                         |
| FASTQ        | 304      |  Erro de sintaxe FASTQ no readID [_ReadID].  Primeira leitura do lote não tem readID terminando em /1 em arquivo [File_name]                                                                                       | Corrija o formato do ficheiro FASTQ e volte a submeter o fluxo de trabalho.                                                                         |
| FASTQ        | 305        |  Erro de sintaxe FASTQ no readID [_readID]. Segunda leitura do lote não tem readID terminando em /2 em arquivo [File_name]                                                                                      | Corrija o formato do ficheiro FASTQ e volte a submeter o fluxo de trabalho.                                                                          |
| FASTQ        | 306        |  Erro de sintaxe FASTQ no readID [_ReadID]. A primeira leitura do par não tem uma identificação que termina em /1 no ficheiro [File_name]                                                                                       | Corrija o formato do ficheiro FASTQ e volte a submeter o fluxo de trabalho.                                                                          |
| FASTQ        | 307        |   Erro de sintaxe FASTQ no readID [_ReadID]. O ReadID não termina com o /1 ou/2. O ficheiro [File_name] não pode ser usado como um ficheiro FASTQ emparelhado.                                                                                      |Corrija o formato do ficheiro FASTQ e volte a submeter o fluxo de trabalho.                                                                          |
| FASTQ        | 308        |  FastQ leu erro. As leituras de ambas as extremidades responderam de forma diferente. Escolheu os ficheiros FASTQ corretos?                                                                                       | Corrija o formato do ficheiro FASTQ e volte a submeter o fluxo de trabalho.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>Passo 3: Contacte o suporte da Microsoft Genomics

Se continuar a ter falhas de emprego, ou se tiver outras dúvidas, contacte o suporte da Microsoft Genomics a partir do portal Azure. Informações adicionais sobre como submeter um pedido de apoio podem ser encontradas [aqui](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a resolver problemas e a resolver problemas comuns com o serviço Microsoft Genomics. Para mais informações e FAQ mais gerais, consulte [questões comuns.](frequently-asked-questions-genomics.md) 
