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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986041"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: Questões comuns

Este artigo lista as principais consultas que pode ter relacionadas com a Microsoft Genomics. Para obter mais informações sobre o serviço Microsoft Genomics, consulte o que é a [Microsoft Genomics?](overview-what-is-genomics.md) Para mais informações sobre resolução de problemas, consulte o nosso [Guia de Resolução de Problemas.](troubleshooting-guide-genomics.md) 


## <a name="how-do-i-run-gatk4-workflows-on-microsoft-genomics"></a>Como posso executar fluxos de trabalho GATK4 na Microsoft Genomics?
No ficheiro config.txt do serviço Microsoft Genomics, `gatk4`especifique o process_name para . Note que será cobrado a taxas regulares de faturação.

## <a name="how-do-i-enable-output-compression"></a>Como posso permitir a compressão da saída?
Pode comprimir o vcf de saída ou o gvcf utilizando um argumento opcional para a compressão da saída. Isto equivale a `-bgzip` funcionamento seguido pela `-tabix` saída vcf `.gz` ou gvcf, `.tbi` para produzir ficheiros (saída bgzip) e (saída tabix). `bgzip`comprime o ficheiro VCF ou `tabix` GVCF e cria um índice para o ficheiro comprimido. O argumento é uma booleana, que está definida `false` por `true` padrão para a saída de VCF, e para por padrão para a saída de gcvf. Para utilizar na linha `-bz` de `--bgzip-output` `true` comando, especifique ou `false`as (executar bgzip e tabix) ou . Para utilizar este argumento no ficheiro config.txt, adicione `bgzip_output: true` ou `bgzip_output: false` ao ficheiro.

## <a name="what-is-the-sla-for-microsoft-genomics"></a>O que é o SLA para a Microsoft Genomics?
Garantimos que 99,9% do tempo que o serviço Microsoft Genomics estará disponível para receber pedidos de API de fluxo de trabalho. Para mais informações, consulte [a SLA.](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/)

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Como é que o uso da Microsoft Genomics aparece na minha conta?
As contas da Microsoft Genomics com base no número de gigabases processadas por fluxo de trabalho. Para mais informações, consulte [Preços](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Onde posso encontrar uma lista de todos os `msgen` possíveis comandos e argumentos para o cliente?
Pode obter uma lista completa de comandos `msgen help`e argumentos disponíveis executando . Se não forem apresentados mais argumentos, mostra uma lista de `submit` `list`secções de ajuda disponíveis, uma para cada uma das , , , `cancel`e `status`. Para obter ajuda para um `msgen help command`comando específico, escreva; por exemplo, `msgen help submit` lista todas as opções de submissão.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Quais são os comandos mais `msgen` utilizados para o cliente?
Os comandos mais utilizados são `msgen` argumentos para o cliente: 

 |**Comando**          |  **Descrição do campo** |
 |:--------------------|:-------------         |
 |`list`               |Devolve uma lista de trabalhos que submeteu. Para argumentos, `msgen help list`veja.  |
 |`submit`             |Submete um pedido de fluxo de trabalho ao serviço. Para argumentos, `msgen help submit`veja.|
 |`status`             |Devolve o estado do fluxo `--workflow-id`de trabalho especificado por . Consulte também `msgen help status`. |
 |`cancel`             |Envia um pedido para cancelar o processamento `--workflow-id`do fluxo de trabalho especificado por . Consulte também `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Para onde consigo `--api-url-base`o valor?
Vá ao portal Azure e abra a sua página de conta Genómica. Sob a rubrica **Gestão,** escolha **as teclas de acesso**. Aí, encontras tanto o URL da API como as tuas chaves de acesso.

## <a name="where-do-i-get-the-value-for---access-key"></a>Para onde consigo `--access-key`o valor?
Vá ao portal Azure e abra a sua página de conta Genómica. Sob a rubrica **Gestão,** escolha **as teclas de acesso**. Aí, encontras tanto o URL da API como as tuas chaves de acesso.

## <a name="why-do-i-need-two-access-keys"></a>Por que preciso de duas chaves de acesso?
Precisa de duas chaves de acesso caso pretenda atualizá-las (regenerar) sem interromper o uso do serviço. Por exemplo, se quiser atualizar a primeira tecla, deve ter todos os novos fluxos de trabalho a utilizar a segunda tecla. Em seguida, aguarde todos os fluxos de trabalho usando a primeira chave para terminar antes de atualizar a primeira tecla.

## <a name="do-you-save-my-storage-account-keys"></a>Guarda as chaves da minha conta de armazenamento?
A chave da sua conta de armazenamento é utilizada para criar fichas de acesso de curto prazo para o serviço Microsoft Genomics para ler os seus ficheiros de entrada e escrever os ficheiros de saída. A duração do símbolo padrão é de 48 horas. A duração do símbolo `-sas/--sas-duration` pode ser alterada com a opção do comando de submissão; o valor é em horas.

## <a name="what-genome-references-can-i-use"></a>Que referências ao genoma posso usar?

Estas referências são suportadas:

 |Referência              | Valor de`-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (sem análise alt) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Como formei os meus argumentos de linha de comando como um ficheiro de config? 

Msgen entende ficheiros de configuração no seguinte formato:
* Todas as opções são fornecidas como pares de valor-chave com valores separados das teclas por um cólon.
  O espaço branco é ignorado.
* As linhas `#` que começam são ignoradas.
* Qualquer argumento de linha de comando no formato longo pode ser convertido para uma chave, despojando os seus traços principais e substituindo traços entre palavras por sublinhados. Aqui estão alguns exemplos de conversão:

  |Argumento de linha de comando            | Linha de ficheiro de configuração |
  |:-------------                   |:-------------                 |
  |`-u/--api-url-base https://url`  | *api_url_base:https://url*    |
  |`-k/--access-key KEY`            | *access_key:KEY*              |      
  |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>Passos seguintes

Utilize os seguintes recursos para começar com a Microsoft Genomics:
- Começa por executar o seu primeiro fluxo de trabalho através do serviço Microsoft Genomics. [Executar um fluxo de trabalho através do serviço Microsoft Genomics](quickstart-run-genomics-workflow-portal.md)
- Envie os seus próprios dados para processamento pelo serviço Microsoft Genomics: [FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [Multiple FASTQ ou BAM](quickstart-input-multiple.md) emparelhados 

