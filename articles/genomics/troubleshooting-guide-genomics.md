---
title: 'Microsoft Genomics: Guia de resolução de problemas | Documentos da Microsoft'
titleSuffix: Azure
description: Saiba mais sobre estratégias de resolução de problemas
keywords: resolução de problemas, erro, depuração
services: microsoft-genomics
author: ruchir
editor: jasonwhowell
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: article
ms.date: 10/29/2018
ms.openlocfilehash: 01f4cca8e177d8f9c9325a7920eb7e72c005bbd6
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760947"
---
# <a name="troubleshooting-guide"></a>Guia de resolução de problemas

Eis algumas sugestões de resolução de problemas para alguns dos problemas comuns que poderá deparar ao utilizar o serviço Microsoft Genomics, MSGEN.

 Para perguntas frequentes, não relacionados com a solução de problemas, consulte [perguntas comuns](frequently-asked-questions-genomics.md).
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>Passo 1: Localize os códigos de erro associados com o fluxo de trabalho

Pode localizar as mensagens de erro associadas com o fluxo de trabalho por:

1. Com a linha de comando e escrever na  `msgen status`
2. A examinar o conteúdo do standardoutput.txt.

### <a name="1-using-the-command-line-msgen-status"></a>1. Com a linha de comandos `msgen status`

```bash
msgen status -u URL -k KEY -w ID 
```




Existem três argumentos necessários:

* Adresa URL – o URI de base para a API
* CHAVE - a chave de acesso para a sua conta do Genomics
    * Para localizar o URL e a chave, aceda ao portal do Azure e abrir a página da sua conta Microsoft Genomics. Sob o **gerenciamento** cabeçalho, escolha **chaves de acesso**. Aí, encontrará o URL da API e as chaves de acesso.

  
* ID - o ID de fluxo de trabalho
    * Para localizar o tipo de ID de fluxo de trabalho no `msgen list` comando. Supondo que o ficheiro de configuração contém o URL e as chaves de acesso e está localizado está na mesma localização que o exe msgen, o comando terá esta aparência: 
        
        ```bash
        msgen list -f "config.txt"
        ```
        Saída desse comando terá o seguinte aspeto:
        
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
 >  Em alternativa pode incluir o caminho para o ficheiro de configuração em vez de introduzir diretamente o URL e a chave. Se incluir estes argumentos da linha de comandos, bem como o ficheiro de configuração, os argumentos da linha de comandos irão ter precedência.  

Para o fluxo de trabalho ID de 1001 e o ficheiro Config txt colocado no mesmo caminho que o msgen executável, o comando terá esta aparência:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2.  Examinar o conteúdo de standardoutput.txt 
Localize o contentor de saída para o fluxo de trabalho em questão. MSGEN cria um, `[workflowfilename].logs.zip` pasta após cada execução de fluxo de trabalho. Descompacte a pasta para exibir seu conteúdo:

* outputFileList.txt - uma lista dos ficheiros de saída produzidos durante o fluxo de trabalho
* StandardError.txt - Este ficheiro está em branco.
* StandardOutput.txt - regista todas as mensagens de estado de nível superior, incluindo erros, o que ocorreu ao executar o fluxo de trabalho.
* Ficheiros - todos os outros ficheiros de registo de GATK o `logs` pasta

Para resolução de problemas, examinar o conteúdo de standardoutput.txt e observe as mensagens de erro que são apresentados.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>Passo 2: Experimente os passos recomendados para erros comuns

Esta secção destaca resumidamente comuns de erros de saída ao serviço Microsoft Genomics (msgen) e as estratégias que pode utilizar para resolvê-los. 

O serviço Microsoft Genomics (msgen) pode emitir os seguintes tipos de erros:

1. Erros de serviço interno: Erros que são internos para o serviço, que pode não ser resolvido, corrigindo os parâmetros ou ficheiros de entrada. Por vezes, voltar a submeter o fluxo de trabalho pode corrigir estes erros.
2. Erros de entrada: Formatos de arquivo de erros que podem ser resolvidos utilizando os argumentos corretos ou corrigir.

### <a name="1-internal-service-errors"></a>1. Erros de serviço interno

Um erro de serviço interno não é passíveis de ação do utilizador. Pode submeter novamente o fluxo de trabalho, mas se isto não resultar, contacte o suporte da Microsoft Genomics

| Mensagem de erro                                                                                                                            | Passos de resolução de problemas recomendados                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ocorreu um erro interno. Tente voltar a submeter o fluxo de trabalho. Se a ver este erro, contacte o suporte da Microsoft Genomics para obter assistência | Submeta novamente o fluxo de trabalho. Contacto do Microsoft Genomics suporte para assistência se o problema persistir através da criação de suporte [pedido de suporte](file-support-ticket-genomics.md ). |

### <a name="2-input-errors"></a>2. Erros de entrada

Estes erros são passíveis de ação do utilizador. Com base no tipo de ficheiro e o código de erro, o serviço Microsoft Genomics gera códigos de erro distintos. Siga os passos recomendados de resolução de problemas indicados abaixo.

| Tipo de ficheiro | Código de erro | Mensagem de erro                                                                           | Passos de resolução de problemas recomendados                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Qualquer          | 701        | Tem de leitura [readId] [numberOfBases] bases, mas o limite é de [maxReadLength]           | A razão mais comum para este erro é que leva a concatenação de duas leituras de danos no ficheiro. Verifique os ficheiros de entrada. |                                |
| BAM          | 200        |   Não é possível ler o ficheiro '[yourFileName]'.                                                                                       | Verifique o formato do ficheiro BAM. Submeta novamente o fluxo de trabalho com um ficheiro formatado corretamente.                                                                           |
| BAM          | 201        |  Não é possível ler o ficheiro BAM [nome_ficheiro].                                                                                      |Verifique o formato do ficheiro BAM.  Submeta o fluxo de trabalho com um ficheiro formatado corretamente.                                                                            |
| BAM          | 202        | Não é possível ler o ficheiro BAM [nome_ficheiro]. Ficheiro demasiado pequeno e em falta o cabeçalho.                                                                                        | Verifique o formato do ficheiro BAM.  Submeta o fluxo de trabalho com um ficheiro formatado corretamente.                                                                            |
| BAM          | 203        |   Não é possível ler o ficheiro BAM [nome_ficheiro]. Cabeçalho do ficheiro foi danificado.                                                                                      |Verifique o formato do ficheiro BAM.  Submeta o fluxo de trabalho com um ficheiro formatado corretamente.                                                                           |
| BAM          | 204        |    Não é possível ler o ficheiro BAM [nome_ficheiro]. Cabeçalho do ficheiro foi danificado.                                                                                     | Verifique o formato do ficheiro BAM.  Submeta o fluxo de trabalho com um ficheiro formatado corretamente.                                                                           |
| BAM          | 205        |    Não é possível ler o ficheiro BAM [nome_ficheiro]. Cabeçalho do ficheiro foi danificado.                                                                                     | Verifique o formato do ficheiro BAM.  Submeta o fluxo de trabalho com um ficheiro formatado corretamente.                                                                            |
| BAM          | 206        |   Não é possível ler o ficheiro BAM [nome_ficheiro]. Cabeçalho do ficheiro foi danificado.                                                                                      | Verifique o formato do ficheiro BAM.  Submeta o fluxo de trabalho com um ficheiro formatado corretamente.                                                                            |
| BAM          | 207        |  Não é possível ler o ficheiro BAM [nome_ficheiro]. Ficheiro truncado junto ao deslocamento [offset].                                                                                       | Verifique o formato do ficheiro BAM.  Submeta o fluxo de trabalho com um ficheiro formatado corretamente.                                                                            |
| BAM          | 208        |   Ficheiro BAM inválido. O ReadID [Read_Id] não tem nenhuma sequência no ficheiro [nome_ficheiro].                                                                                      | Verifique o formato do ficheiro BAM.  Submeta o fluxo de trabalho com um ficheiro formatado corretamente.                                                                             |
| FASTQ        | 300        |  Não é possível ler o ficheiro de ficheiros FASTQ. [Nome_ficheiro] não termina com uma nova linha.                                                                                     | Corrija o formato do arquivo de ficheiros FASTQ e submeta novamente o fluxo de trabalho.                                                                           |
| FASTQ        | 301        |   Não é possível ler o arquivo de ficheiros FASTQ [nome_ficheiro]. Registo de ficheiros FASTQ é maior do que o tamanho da memória intermédia no desvio: [_offset]                                                                                      | Corrija o formato do arquivo de ficheiros FASTQ e submeta novamente o fluxo de trabalho.                                                                         |
| FASTQ        | 302        |     Erro de sintaxe de ficheiros FASTQ. O ficheiro [nome_ficheiro] tem uma linha em branco.                                                                                    | Corrija o formato do arquivo de ficheiros FASTQ e submeta novamente o fluxo de trabalho.                                                                         |
| FASTQ        | 303        |       Erro de sintaxe de ficheiros FASTQ. O ficheiro [nome_ficheiro] tem um caráter de partida inválido no desvio: escreva do [_offset], linha: [line_type], caracteres: [_char]                                                                                  | Corrija o formato do arquivo de ficheiros FASTQ e submeta novamente o fluxo de trabalho.                                                                         |
| FASTQ        | 304      |  Erro de sintaxe de ficheiros FASTQ em readID [_ReadID].  Leia primeiro do batch não tem readID que termine em /1 no ficheiro [nome_ficheiro]                                                                                       | Corrija o formato do arquivo de ficheiros FASTQ e submeta novamente o fluxo de trabalho.                                                                         |
| FASTQ        | 305        |  Erro de sintaxe de ficheiros FASTQ em readID [_readID]. Segunda leitura do lote não tem readID que termine em /2 no ficheiro [nome_ficheiro]                                                                                      | Corrija o formato do arquivo de ficheiros FASTQ e submeta novamente o fluxo de trabalho.                                                                          |
| FASTQ        | 306        |  Erro de sintaxe de ficheiros FASTQ em readID [_ReadID]. Leia primeiro do par não tem um ID que termina em /1 no ficheiro [nome_ficheiro]                                                                                       | Corrija o formato do arquivo de ficheiros FASTQ e submeta novamente o fluxo de trabalho.                                                                          |
| FASTQ        | 307        |   Erro de sintaxe de ficheiros FASTQ em readID [_ReadID]. ReadID não termina com /1 ou / 2. O ficheiro [nome_ficheiro] não pode ser utilizado como um arquivo de ficheiros FASTQ emparelhado.                                                                                      |Corrija o formato do arquivo de ficheiros FASTQ e submeta novamente o fluxo de trabalho.                                                                          |
| FASTQ        | 308        |  Erro de leitura de ficheiros FASTQ. Leituras de ambas as extremidades responderam de forma diferente. Escolher os ficheiros FASTQ corretos?                                                                                       | Corrija o formato do arquivo de ficheiros FASTQ e submeta novamente o fluxo de trabalho.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>Passo 3: Contacte o suporte da Microsoft Genomics

Se continuar com falhas de tarefas, ou se tiver outras dúvidas, contacte o suporte da Microsoft Genomics no portal do Azure. Pode encontrar informações adicionais sobre como submeter um pedido de suporte [aqui](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a resolver problemas comuns com o serviço Microsoft Genomics. Para obter mais informações e FAQ mais gerais, veja [perguntas comuns](frequently-asked-questions-genomics.md). 
