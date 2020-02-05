---
title: Perguntas comuns - FAQ
titleSuffix: Microsoft Genomics
description: Obtenha respostas a questões comuns relacionadas com a utilização do serviço Microsoft Genomics, incluindo informações técnicas, SLA e faturação.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: troubleshooting
ms.date: 12/07/2017
ms.openlocfilehash: e8806bc4f761214e6740a22093b7e18030fdf881
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986041"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: Questões comuns

Este artigo lista as principais consultas que pode ter relacionadas com a Microsoft Genomics. Para obter mais informações sobre o serviço Microsoft Genomics, consulte o que é a [Microsoft Genomics?](overview-what-is-genomics.md) Para mais informações sobre resolução de problemas, consulte o nosso [Guia de Resolução de Problemas.](troubleshooting-guide-genomics.md) 


## <a name="how-do-i-run-gatk4-workflows-on-microsoft-genomics"></a>Como posso executar fluxos de trabalho GATK4 na Microsoft Genomics?
No ficheiro config.txt do serviço Microsoft Genomics, especifique o process_name para `gatk4`. Note que será cobrado a taxas regulares de faturação.

## <a name="how-do-i-enable-output-compression"></a>Como posso permitir a compressão da saída?
Pode comprimir o vcf de saída ou o gvcf utilizando um argumento opcional para a compressão da saída. Isto equivale a `-bgzip` de funcionamento seguido de `-tabix` na saída vcf ou gvcf, para produzir `.gz` (saída bgzip) e `.tbi` (saída tabix). `bgzip` comprime o ficheiro VCF ou GVCF, e `tabix` cria um índice para o ficheiro comprimido. O argumento é uma booleana, que está definida para `false` por padrão para a saída de VCF, e para `true` por padrão para a saída de gcvf. Para utilizar na linha de comando, especifique `-bz` ou `--bgzip-output` como `true` (executar bgzip e tabix) ou `false`. Para utilizar este argumento no ficheiro config.txt, adicione `bgzip_output: true` ou `bgzip_output: false` ao ficheiro.

## <a name="what-is-the-sla-for-microsoft-genomics"></a>O que é o SLA para a Microsoft Genomics?
Garantimos que 99,9% do tempo que o serviço Microsoft Genomics estará disponível para receber pedidos de API de fluxo de trabalho. Para mais informações, consulte [a SLA.](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/)

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Como é que o uso da Microsoft Genomics aparece na minha conta?
As contas da Microsoft Genomics com base no número de gigabases processadas por fluxo de trabalho. Para obter mais informações, consulte [preços](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Onde posso encontrar uma lista de todos os comandos e argumentos possíveis para o cliente `msgen`?
Pode obter uma lista completa de comandos e argumentos disponíveis executando `msgen help`. Se não forem apresentados mais argumentos, mostra uma lista de secções de ajuda disponíveis, uma para cada `submit`, `list`, `cancel`e `status`. Para obter ajuda para um comando específico, escreva `msgen help command`; por exemplo, `msgen help submit` lista todas as opções de apresentação.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Quais são os comandos mais utilizados para o cliente `msgen`?
Os comandos mais utilizados são argumentos para o cliente `msgen` incluem: 

 |**Comando**          |  **Descrição do campo** |
 |:--------------------|:-------------         |
 |`list`               |Devolve uma lista de trabalhos que submeteu. Para argumentos, consulte `msgen help list`.  |
 |`submit`             |Submete um pedido de fluxo de trabalho ao serviço. Para argumentos, consulte `msgen help submit`.|
 |`status`             |Devolve o estado do fluxo de trabalho especificado por `--workflow-id`. Consulte também `msgen help status`. |
 |`cancel`             |Envia um pedido para cancelar o processamento do fluxo de trabalho especificado por `--workflow-id`. Consulte também `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Onde consigo o valor para `--api-url-base`?
Vá ao portal Azure e abra a sua página de conta Genómica. Sob a rubrica **Gestão,** escolha **as teclas de acesso**. Aí, encontras tanto o URL da API como as tuas chaves de acesso.

## <a name="where-do-i-get-the-value-for---access-key"></a>Onde consigo o valor para `--access-key`?
Vá ao portal Azure e abra a sua página de conta Genómica. Sob a rubrica **Gestão,** escolha **as teclas de acesso**. Aí, encontras tanto o URL da API como as tuas chaves de acesso.

## <a name="why-do-i-need-two-access-keys"></a>Por que preciso de duas chaves de acesso?
Precisa de duas chaves de acesso caso pretenda atualizá-las (regenerar) sem interromper o uso do serviço. Por exemplo, se quiser atualizar a primeira tecla, deve ter todos os novos fluxos de trabalho a utilizar a segunda tecla. Em seguida, aguarde todos os fluxos de trabalho usando a primeira chave para terminar antes de atualizar a primeira tecla.

## <a name="do-you-save-my-storage-account-keys"></a>Guarda as chaves da minha conta de armazenamento?
A chave da sua conta de armazenamento é utilizada para criar fichas de acesso de curto prazo para o serviço Microsoft Genomics para ler os seus ficheiros de entrada e escrever os ficheiros de saída. A duração do símbolo padrão é de 48 horas. A duração do símbolo pode ser alterada com a opção `-sas/--sas-duration` do comando de submissão; o valor é em horas.

## <a name="what-genome-references-can-i-use"></a>Que referências ao genoma posso usar?

Estas referências são suportadas:

 |Referência              | Valor da `-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (sem análise alt) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Como formei os meus argumentos de linha de comando como um ficheiro de config? 

Msgen entende ficheiros de configuração no seguinte formato:
* Todas as opções são fornecidas como pares de valor-chave com valores separados das teclas por um cólon.
  O espaço branco é ignorado.
* As linhas que começam com `#` são ignoradas.
* Qualquer argumento de linha de comando no formato longo pode ser convertido para uma chave, despojando os seus traços principais e substituindo traços entre palavras por sublinhados. Aqui estão alguns exemplos de conversão:

  |Argumento de linha de comando            | Linha de ficheiro de configuração |
  |:-------------                   |:-------------                 |
  |`-u/--api-url-base https://url`  | *api_url_base: https://url*    |
  |`-k/--access-key KEY`            | *access_key:KEY*              |      
  |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>Passos seguintes

Utilize os seguintes recursos para começar com a Microsoft Genomics:
- Começa por executar o seu primeiro fluxo de trabalho através do serviço Microsoft Genomics. [Executar um fluxo de trabalho através do serviço Microsoft Genomics](quickstart-run-genomics-workflow-portal.md)
- Envie os seus próprios dados para processamento pelo serviço Microsoft Genomics: [FASTQ emparelhado](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [Multiple FASTQ ou BAM](quickstart-input-multiple.md) 

