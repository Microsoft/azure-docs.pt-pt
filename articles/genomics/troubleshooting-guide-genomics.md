---
title: Guia de Resolução de Problemas
titleSuffix: Microsoft Genomics
description: Saiba mais sobre estratégias de resolução de problemas para usar a Microsoft Genomics, incluindo mensagens de erro e como resolvê-las.
keywords: resolução de problemas, erro, depuração
services: genomics
author: ruchir
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.openlocfilehash: c508c10d619cde1a16d89b446c5cfd1a3ce81daf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023828"
---
# <a name="troubleshooting-guide"></a>Guia de resolução de problemas

Aqui ficam algumas dicas de resolução de problemas para algumas das questões comuns que poderá enfrentar ao utilizar o serviço Microsoft Genomics, MSGEN.

 Para as FAQ, não relacionadas com a resolução de problemas, consulte [questões comuns.](frequently-asked-questions-genomics.md)
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>Passo 1: Localizar códigos de erro associados ao fluxo de trabalho

Pode localizar as mensagens de erro associadas ao fluxo de trabalho:

1. Usando a linha de comando e digitando  `msgen status`
2. Examinando o conteúdo de standardoutput.txt.

### <a name="1-using-the-command-line-msgen-status"></a>1. Utilização da linha de comando `msgen status`

```bash
msgen status -u URL -k KEY -w ID 
```




Há três argumentos necessários:

* URL - a base URI para a API
* CHAVE - a chave de acesso para a sua conta Genomics
    * Para encontrar o seu URL e KEY, vá ao portal Azure e abra a sua página de conta Microsoft Genomics. No título **Gestão,** escolha **as teclas de acesso.** Lá, encontra-se tanto o URL da API como as chaves de acesso.

  
* ID - o ID do fluxo de trabalho
    * Para encontrar o seu tipo de identificação de fluxo de trabalho no `msgen list` comando. Assumindo que o seu ficheiro config contém o URL e as suas chaves de acesso, e está localizado no mesmo local que o seu exe msgen, o comando será assim: 
        
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
  >  Em alternativa, pode incluir o caminho para o ficheiro config em vez de introduzir diretamente o URL e a CHAVE. Se incluir estes argumentos na linha de comando, bem como no ficheiro config, os argumentos da linha de comando terão precedência.  

Para o ID 1001 do fluxo de trabalho, e config.txt ficheiro colocado no mesmo caminho que o msgen executável, o comando será assim:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2. Examinar o conteúdo da standardoutput.txt 
Localize o recipiente de saída para o fluxo de trabalho em questão. A MSGEN cria uma   `[workflowfilename].logs.zip` pasta após cada execução do fluxo de trabalho. Desaperte a pasta para ver o seu conteúdo:

* outputFileList.txt - uma lista dos ficheiros de saída produzidos durante o fluxo de trabalho
* standarderror.txt- este ficheiro está em branco.
* standardoutput.txt - regista todas as mensagens de estado de nível superior, incluindo erros, que ocorreram durante a execução do fluxo de trabalho.
* Ficheiros de registo GATK - todos os outros ficheiros na `logs` pasta

Para a resolução de problemas, examine o conteúdo da standardoutput.txt e observe quaisquer mensagens de erro que apareçam.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>Passo 2: Tente passos recomendados para erros comuns

Esta secção destaca brevemente a produção de erros comuns pelo serviço Microsoft Genomics (msgen) e as estratégias que pode utilizar para os resolver. 

O serviço Microsoft Genomics (msgen) pode lançar os seguintes dois tipos de erros:

1. Erros de Serviço Interno: Erros internos ao serviço, que podem não ser resolvidos através da fixação de parâmetros ou ficheiros de entrada. Por vezes, a resubmissão do fluxo de trabalho pode corrigir estes erros.
2. Erros de entrada: Erros que podem ser resolvidos utilizando os argumentos corretos ou corrigindo os formatos de ficheiros.

### <a name="1-internal-service-errors"></a>1. Erros de serviço interno

Um erro de serviço interno não é accuível para o utilizador. Pode reenviar o fluxo de trabalho, mas se isso não funcionar, contacte o suporte da Microsoft Genomics

| Mensagem de erro                                                                                                                            | Passos de resolução de problemas recomendados                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ocorreu um erro interno. Tente reenviar o fluxo de trabalho. Se voltar a ver este erro, contacte o suporte da Microsoft Genomics para obter assistência | Submeta novamente o fluxo de trabalho. Contacte o suporte da Microsoft Genomics para obter assistência se o problema persistir criando um [bilhete](file-support-ticket-genomics.md )de apoio . |

### <a name="2-input-errors"></a>2. Erros de entrada

Estes erros são accuáveis para o utilizador. Com base no tipo de ficheiro e código de erro, o serviço Microsoft Genomics produz códigos de erro distintos. Siga os passos recomendados de resolução de problemas listados abaixo.

| Tipo de arquivo | Código de erro | Mensagem de erro                                                                           | Passos de resolução de problemas recomendados                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Qualquer          | 701        | Leia [readId] tem bases [numberOfBases], mas o limite é [maxReadLength]           | A razão mais comum para este erro é a corrupção de ficheiros que leva à concatenação de duas leituras. Verifique os seus ficheiros de entrada. |
| BAM          | 200        |   Não é possível ler o ficheiro '[yourFileName]'.                                                                                       | Verifique o formato do ficheiro BAM. Submeta novamente o fluxo de trabalho com um ficheiro devidamente formatado.                                                                           |
| BAM          | 201        |  Incapaz de ler o ficheiro BAM [File_name].                                                                                      |Verifique o formato do ficheiro BAM.  Envie o fluxo de trabalho com um ficheiro corretamente formatado.                                                                            |
| BAM          | 202        | Incapaz de ler o ficheiro BAM [File_name]. Arquivo muito pequeno e falta de cabeça.                                                                                        | Verifique o formato do ficheiro BAM.  Envie o fluxo de trabalho com um ficheiro corretamente formatado.                                                                            |
| BAM          | 203        |   Incapaz de ler o ficheiro BAM [File_name]. O cabeçalho do ficheiro era corrupto.                                                                                      |Verifique o formato do ficheiro BAM.  Envie o fluxo de trabalho com um ficheiro corretamente formatado.                                                                           |
| BAM          | 204        |    Incapaz de ler o ficheiro BAM [File_name]. O cabeçalho do ficheiro era corrupto.                                                                                     | Verifique o formato do ficheiro BAM.  Envie o fluxo de trabalho com um ficheiro corretamente formatado.                                                                           |
| BAM          | 205        |    Incapaz de ler o ficheiro BAM [File_name]. O cabeçalho do ficheiro era corrupto.                                                                                     | Verifique o formato do ficheiro BAM.  Envie o fluxo de trabalho com um ficheiro corretamente formatado.                                                                            |
| BAM          | 206        |   Incapaz de ler o ficheiro BAM [File_name]. O cabeçalho do ficheiro era corrupto.                                                                                      | Verifique o formato do ficheiro BAM.  Envie o fluxo de trabalho com um ficheiro corretamente formatado.                                                                            |
| BAM          | 207        |  Incapaz de ler o ficheiro BAM [File_name]. Ficheiro truncado perto de offset [offset].                                                                                       | Verifique o formato do ficheiro BAM.  Envie o fluxo de trabalho com um ficheiro corretamente formatado.                                                                            |
| BAM          | 208        |   Ficheiro BAM inválido. O ReadID [Read_Id] não tem sequência em ficheiro [File_name].                                                                                      | Verifique o formato do ficheiro BAM.  Envie o fluxo de trabalho com um ficheiro corretamente formatado.                                                                             |
| FASTQ        | 300        |  Incapaz de ler o ficheiro FASTQ. [File_name] não termina com uma nova linha.                                                                                     | Corrija o formato do ficheiro FASTQ e envie novamente o fluxo de trabalho.                                                                           |
| FASTQ        | 301        |   Não é possível ler o ficheiro FASTQ [File_name]. O recorde fastq é maior do que o tamanho do tampão no offset: [_offset]                                                                                      | Corrija o formato do ficheiro FASTQ e envie novamente o fluxo de trabalho.                                                                         |
| FASTQ        | 302        |     Erro de sintaxe FASTQ. O ficheiro [File_name] tem uma linha em branco.                                                                                    | Corrija o formato do ficheiro FASTQ e envie novamente o fluxo de trabalho.                                                                         |
| FASTQ        | 303        |       Erro de sintaxe FASTQ. O ficheiro[File_name] tem um carácter inicial inválido no offset: [_offset], tipo de linha: [line_type], personagem: [_char]                                                                                  | Corrija o formato do ficheiro FASTQ e envie novamente o fluxo de trabalho.                                                                         |
| FASTQ        | 304      |  Erro de sintaxe FASTQ no readID [_ReadID].  Primeira leitura do lote não tem readID terminando em /1 em arquivo [File_name]                                                                                       | Corrija o formato do ficheiro FASTQ e envie novamente o fluxo de trabalho.                                                                         |
| FASTQ        | 305        |  Erro de sintaxe FASTQ no readID [_readID]. A segunda leitura do lote não tem readID terminando em /2 em arquivo [File_name]                                                                                      | Corrija o formato do ficheiro FASTQ e envie novamente o fluxo de trabalho.                                                                          |
| FASTQ        | 306        |  Erro de sintaxe FASTQ no readID [_ReadID]. A primeira leitura do par não tem um ID que termina em /1 em ficheiro [File_name]                                                                                       | Corrija o formato do ficheiro FASTQ e envie novamente o fluxo de trabalho.                                                                          |
| FASTQ        | 307        |   Erro de sintaxe FASTQ no readID [_ReadID]. O ReadID não termina com /1 ou/2. O ficheiro [File_name] não pode ser usado como um ficheiro FASTQ emparelhado.                                                                                      |Corrija o formato do ficheiro FASTQ e envie novamente o fluxo de trabalho.                                                                          |
| FASTQ        | 308        |  Erro de leitura FASTQ. As leituras de ambas as extremidades responderam de forma diferente. Escolheu os ficheiros FASTQ corretos?                                                                                       | Corrija o formato do ficheiro FASTQ e envie novamente o fluxo de trabalho.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>Passo 3: Contacte o suporte da Microsoft Genomics

Se continuar a ter falhas de emprego, ou se tiver outras questões, contacte o suporte da Microsoft Genomics a partir do portal Azure. Informações adicionais sobre como submeter um pedido de apoio podem ser consultada [aqui.](file-support-ticket-genomics.md)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a resolver problemas e a resolver problemas comuns com o serviço Microsoft Genomics. Para obter mais informações e perguntas frequentes mais gerais, consulte [questões comuns.](frequently-asked-questions-genomics.md) 
