---
title: Guia de solução de problemas
titleSuffix: Microsoft Genomics
description: Saiba mais sobre estratégias de solução de problemas para usar o Microsoft Genomics.
keywords: solução de problemas, erro, depuração
services: genomics
author: ruchir
editor: jasonwhowell
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.openlocfilehash: ce8af4d444e642a8f67f43f8cf403ce9b2cb08ab
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248520"
---
# <a name="troubleshooting-guide"></a>Guia de resolução de problemas

Aqui estão algumas dicas de solução de problemas para alguns dos problemas comuns que você pode enfrentar ao usar o serviço de Microsoft Genomics, MSGEN.

 Para perguntas frequentes, não relacionadas à solução de problemas, consulte [perguntas comuns](frequently-asked-questions-genomics.md).
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>Etapa 1: localizar códigos de erro associados ao fluxo de trabalho

Você pode localizar as mensagens de erro associadas ao fluxo de trabalho:

1. Usando a linha de comando e digitando `msgen status`
2. Examinando o conteúdo de StandardOutput. txt.

### <a name="1-using-the-command-line-msgen-status"></a>1. usando a linha de comando `msgen status`

```bash
msgen status -u URL -k KEY -w ID 
```




Há três argumentos necessários:

* URL-o URI de base para a API
* CHAVE-a chave de acesso para sua conta do genomas
    * Para localizar a URL e a chave, acesse portal do Azure e abra a página da sua conta do Microsoft Genomics. No título **Gerenciamento** , escolha **chaves de acesso**. Lá, você encontrará a URL da API e suas chaves de acesso.

  
* ID-a ID do fluxo de trabalho
    * Para localizar o tipo de ID do fluxo de trabalho no comando `msgen list`. Supondo que o arquivo de configuração contenha a URL e suas chaves de acesso e esteja localizado no mesmo local que o msgen exe, o comando terá a seguinte aparência: 
        
        ```bash
        msgen list -f "config.txt"
        ```
        A saída desse comando terá a seguinte aparência:
        
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
  >  Como alternativa, você pode incluir o caminho para o arquivo de configuração em vez de inserir diretamente a URL e a chave. Se você incluir esses argumentos na linha de comando, bem como no arquivo de configuração, os argumentos de linha de comando terão precedência.  

Para a ID do fluxo de trabalho 1001 e o arquivo config. txt colocado no mesmo caminho que o executável msgen, o comando terá a seguinte aparência:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2. Examine o conteúdo de StandardOutput. txt 
Localize o contêiner de saída do fluxo de trabalho em questão. MSGEN cria uma pasta, `[workflowfilename].logs.zip` após cada execução de fluxo de trabalho. Descompacte a pasta para exibir seu conteúdo:

* outputFileList. txt-uma lista dos arquivos de saída produzidos durante o fluxo de trabalho
* StandardError. txt-Este arquivo está em branco.
* StandardOutput. txt-registra todas as mensagens de status de nível superior, incluindo erros, que ocorreram durante a execução do fluxo de trabalho.
* Arquivos de log do GATK – todos os outros arquivos na pasta `logs`

Para solução de problemas, examine o conteúdo de StandardOutput. txt e anote todas as mensagens de erro exibidas.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>Etapa 2: Experimente as etapas recomendadas para erros comuns

Esta seção realça resumidamente erros comuns de saída Microsoft Genomics serviço (msgen) e as estratégias que você pode usar para resolvê-los. 

O serviço de Microsoft Genomics (msgen) pode gerar os dois tipos de erros a seguir:

1. Erros de serviço interno: erros que são internos ao serviço, que podem não ser resolvidos corrigindo parâmetros ou arquivos de entrada. Às vezes, o reenvio do fluxo de trabalho pode corrigir esses erros.
2. Erros de entrada: erros que podem ser resolvidos usando os argumentos corretos ou corrigindo formatos de arquivo.

### <a name="1-internal-service-errors"></a>1. erros de serviço interno

Um erro de serviço interno não é acionável pelo usuário. Você pode reenviar o fluxo de trabalho, mas se isso não funcionar, entre em contato com o suporte do Microsoft Genomics

| Mensagem de erro                                                                                                                            | Passos de resolução de problemas recomendados                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ocorreu um erro interno. Tente reenviar o fluxo de trabalho. Se você vir esse erro novamente, contate o suporte do Microsoft Genomics para obter assistência | Envie o fluxo de trabalho novamente. Contate o suporte do Microsoft Genomics para obter assistência se o problema persistir criando um [tíquete](file-support-ticket-genomics.md )de suporte. |

### <a name="2-input-errors"></a>2. erros de entrada

Esses erros são acionáveis pelo usuário. Com base no tipo de arquivo e código de erro, Microsoft Genomics serviço gera códigos de erro distintos. Siga as etapas de solução de problemas recomendadas listadas abaixo.

| Tipo de arquivo | Código de erro | Mensagem de erro                                                                           | Passos de resolução de problemas recomendados                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Qualquer          | 701        | Read [readid] tem bases [numberOfBases], mas o limite é [maxReadLength]           | O motivo mais comum para esse erro é a corrupção de arquivo que leva à concatenação de duas leituras. Verifique os arquivos de entrada. |
| BAM          | 200        |   Não é possível ler o arquivo ' [yourFileName] '.                                                                                       | Verifique o formato do arquivo BAM. Envie o fluxo de trabalho novamente com um arquivo formatado corretamente.                                                                           |
| BAM          | 201        |  Não é possível ler o arquivo BAM [nome_do_arquivo].                                                                                      |Verifique o formato do arquivo BAM.  Envie o fluxo de trabalho com um arquivo formatado corretamente.                                                                            |
| BAM          | 202        | Não é possível ler o arquivo BAM [nome_do_arquivo]. Arquivo muito pequeno e faltando cabeçalho.                                                                                        | Verifique o formato do arquivo BAM.  Envie o fluxo de trabalho com um arquivo formatado corretamente.                                                                            |
| BAM          | 203        |   Não é possível ler o arquivo BAM [nome_do_arquivo]. O cabeçalho do arquivo foi corrompido.                                                                                      |Verifique o formato do arquivo BAM.  Envie o fluxo de trabalho com um arquivo formatado corretamente.                                                                           |
| BAM          | 204        |    Não é possível ler o arquivo BAM [nome_do_arquivo]. O cabeçalho do arquivo foi corrompido.                                                                                     | Verifique o formato do arquivo BAM.  Envie o fluxo de trabalho com um arquivo formatado corretamente.                                                                           |
| BAM          | 205        |    Não é possível ler o arquivo BAM [nome_do_arquivo]. O cabeçalho do arquivo foi corrompido.                                                                                     | Verifique o formato do arquivo BAM.  Envie o fluxo de trabalho com um arquivo formatado corretamente.                                                                            |
| BAM          | 206        |   Não é possível ler o arquivo BAM [nome_do_arquivo]. O cabeçalho do arquivo foi corrompido.                                                                                      | Verifique o formato do arquivo BAM.  Envie o fluxo de trabalho com um arquivo formatado corretamente.                                                                            |
| BAM          | 207        |  Não é possível ler o arquivo BAM [nome_do_arquivo]. Arquivo truncado próximo ao deslocamento [deslocamento].                                                                                       | Verifique o formato do arquivo BAM.  Envie o fluxo de trabalho com um arquivo formatado corretamente.                                                                            |
| BAM          | 208        |   Arquivo BAM inválido. O Readid [Read_Id] não tem sequência no arquivo [nome_do_arquivo].                                                                                      | Verifique o formato do arquivo BAM.  Envie o fluxo de trabalho com um arquivo formatado corretamente.                                                                             |
| FASTQ        | 300        |  Não é possível ler o arquivo FASTQ. [Nome_do_arquivo] não termina com uma nova linha.                                                                                     | Corrija o formato do arquivo FASTQ e envie o fluxo de trabalho novamente.                                                                           |
| FASTQ        | 301        |   Não é possível ler o arquivo FASTQ [nome_do_arquivo]. O registro FASTQ é maior que o tamanho do buffer no deslocamento: [_offset]                                                                                      | Corrija o formato do arquivo FASTQ e envie o fluxo de trabalho novamente.                                                                         |
| FASTQ        | 302        |     Erro de sintaxe de FASTQ. O arquivo [nome_do_arquivo] tem uma linha em branco.                                                                                    | Corrija o formato do arquivo FASTQ e envie o fluxo de trabalho novamente.                                                                         |
| FASTQ        | 303        |       Erro de sintaxe de FASTQ. O arquivo [nome_do_arquivo] tem um caractere inicial inválido no deslocamento: [_offset], tipo de linha: [line_type], caractere: [_char]                                                                                  | Corrija o formato do arquivo FASTQ e envie o fluxo de trabalho novamente.                                                                         |
| FASTQ        | 304      |  Erro de sintaxe de FASTQ em readid [_ReadID].  A primeira leitura do lote não tem o readid terminando em/1 no arquivo [nome_do_arquivo]                                                                                       | Corrija o formato do arquivo FASTQ e envie o fluxo de trabalho novamente.                                                                         |
| FASTQ        | 305        |  Erro de sintaxe de FASTQ em readid [_readID]. A segunda leitura do lote não tem o readid terminando em/2 no arquivo [nome_do_arquivo]                                                                                      | Corrija o formato do arquivo FASTQ e envie o fluxo de trabalho novamente.                                                                          |
| FASTQ        | 306        |  Erro de sintaxe de FASTQ em readid [_ReadID]. A primeira leitura do par não tem uma ID que termina em/1 no arquivo [nome_do_arquivo]                                                                                       | Corrija o formato do arquivo FASTQ e envie o fluxo de trabalho novamente.                                                                          |
| FASTQ        | 307        |   Erro de sintaxe de FASTQ em readid [_ReadID]. O readid não termina com/1 ou/2. O arquivo [nome_do_arquivo] não pode ser usado como um arquivo FASTQ emparelhado.                                                                                      |Corrija o formato do arquivo FASTQ e envie o fluxo de trabalho novamente.                                                                          |
| FASTQ        | 308        |  Erro de leitura de FASTQ. As leituras de ambas as extremidades responderam de forma diferente. Você escolheu os arquivos corretos do FASTQ?                                                                                       | Corrija o formato do arquivo FASTQ e envie o fluxo de trabalho novamente.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>Etapa 3: contate o suporte do Microsoft Genomics

Se você continuar tendo falhas de trabalho ou se tiver outras perguntas, contate Microsoft Genomics suporte do portal do Azure. Informações adicionais sobre como enviar uma solicitação de suporte podem ser encontradas [aqui](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a resolver problemas comuns com o serviço Microsoft Genomics. Para obter mais informações e perguntas frequentes mais gerais, consulte [perguntas comuns](frequently-asked-questions-genomics.md). 
