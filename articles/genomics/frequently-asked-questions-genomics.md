---
title: Questões comuns - FAQ
titleSuffix: Microsoft Genomics
description: Obtenha respostas a questões comuns relacionadas com a utilização do serviço Microsoft Genomics, incluindo informações técnicas, SLA e faturação.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: troubleshooting
ms.date: 12/07/2017
ms.openlocfilehash: 40fff49aa3ed46417cdd9a6190f928707e41accb
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608617"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: Perguntas comuns

Este artigo lista as principais consultas que pode ter relacionadas com a Microsoft Genomics. Para obter mais informações sobre o serviço Microsoft Genomics, veja [o que é a Microsoft Genomics?](overview-what-is-genomics.md) Para mais informações sobre a resolução de problemas, consulte o nosso [Guia de Resolução de Problemas.](troubleshooting-guide-genomics.md) 


## <a name="how-do-i-run-gatk4-workflows-on-microsoft-genomics"></a>Como posso executar fluxos de trabalho GATK4 na Microsoft Genomics?
No ficheiro de config.txt do serviço Microsoft Genomics, especifique a process_name para `gatk4` . Note que será cobrado a taxas de faturação regulares.

## <a name="how-do-i-enable-output-compression"></a>Como posso permitir a compressão da saída?
Pode comprimir o vcf de saída ou gvcf utilizando um argumento opcional para a compressão de saída. Isto equivale a ser `-bgzip` gerido seguido pela saída de `-tabix` VCF ou GVCF, para produzir `.gz` ficheiros (saída bgzip) e `.tbi` (saída tabix). `bgzip` comprime o ficheiro VCF ou gvcf e `tabix` cria um índice para o ficheiro comprimido. O argumento é um booleano, que é definido `false` por padrão para a saída de VCF, e `true` para por padrão para a saída de GCVF. Para utilizar na linha de comando, especifique `-bz` ou `--bgzip-output` como `true` (executar bgzip e tabix) ou `false` . Para utilizar este argumento no ficheiro config.txt, adicione `bgzip_output: true` ou `bgzip_output: false` adicione ao ficheiro.

## <a name="what-is-the-sla-for-microsoft-genomics"></a>O que é o SLA para a Microsoft Genomics?
Garantimos que 99,9% do tempo o serviço Microsoft Genomics estará disponível para receber pedidos de API de fluxo de trabalho. Para mais informações, consulte [o SLA.](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/)

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Como é que o uso da Microsoft Genomics aparece na minha conta?
Contas da Microsoft Genomics com base no número de gigabases processadas por fluxo de trabalho. Para mais informações, consulte [a Fixação de Preços.](https://azure.microsoft.com/pricing/details/genomics/)


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Onde posso encontrar uma lista de todos os possíveis comandos e argumentos para o `msgen` cliente?
Pode obter uma lista completa de comandos e argumentos disponíveis correndo `msgen help` . Se não forem apresentados mais argumentos, apresenta uma lista de secções de ajuda disponíveis, uma para cada um dos `submit` `list` , e `cancel` `status` . Para obter ajuda para um comando específico, `msgen help command` escreva; por exemplo, `msgen help submit` lista todas as opções de submissão.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Quais são os comandos mais utilizados para o `msgen` cliente?
Os comandos mais utilizados são os argumentos para o `msgen` cliente: 

 |**Comando**          |  **Descrição do campo** |
 |:--------------------|:-------------         |
 |`list`               |Devolve uma lista de empregos que submeteu. Para argumentos, `msgen help list` vês.  |
 |`submit`             |Submete um pedido de fluxo de trabalho ao serviço. Para argumentos, `msgen help submit` vês.|
 |`status`             |Devolve o estado do fluxo de trabalho especificado por `--workflow-id` . Consulte também `msgen help status`. |
 |`cancel`             |Envia um pedido de cancelamento do fluxo de trabalho especificado por `--workflow-id` . Consulte também `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Para onde consigo o `--api-url-base` valor?
Vá ao portal Azure e abra a sua página de conta Genomics. No título **Gestão,** escolha **as teclas de acesso.** Lá, encontra-se tanto o URL da API como as chaves de acesso.

## <a name="where-do-i-get-the-value-for---access-key"></a>Para onde consigo o `--access-key` valor?
Vá ao portal Azure e abra a sua página de conta Genomics. No título **Gestão,** escolha **as teclas de acesso.** Lá, encontra-se tanto o URL da API como as chaves de acesso.

## <a name="why-do-i-need-two-access-keys"></a>Por que preciso de duas chaves de acesso?
Precisa de duas teclas de acesso para o caso de pretender atualizá-las (regenerar) sem interromper o uso do serviço. Por exemplo, se pretender atualizar a primeira tecla, deve ter todos os novos fluxos de trabalho a utilizar a segunda tecla. Em seguida, aguarde todos os fluxos de trabalho utilizando a primeira chave para terminar antes de atualizar a primeira chave.

## <a name="do-you-save-my-storage-account-keys"></a>Guarda as chaves da minha conta de armazenamento?
A sua chave de conta de armazenamento é utilizada para criar fichas de acesso a curto prazo para o serviço Microsoft Genomics para ler os seus ficheiros de entrada e escrever os ficheiros de saída. A duração do token predefinido é de 48 horas. A duração do token pode ser alterada com a `-sas/--sas-duration` opção do comando de submissão; o valor é em horas.

## <a name="does-microsoft-genomics-store-customer-data"></a>A Microsoft Genomics armazena os dados dos clientes?

N.º A Microsoft Genomics não armazena quaisquer dados do cliente.

## <a name="what-genome-references-can-i-use"></a>Que referências ao genoma posso usar?

Estas referências são suportadas:

 |Referência              | Valor de `-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (sem análise alt) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Como posso formatar os meus argumentos de linha de comando como um ficheiro config? 

msgen compreende ficheiros de configuração no seguinte formato:
* Todas as opções são fornecidas como pares de valor-chave com valores separados das teclas por um cólon.
  Whitespace é ignorado.
* As linhas que começam `#` são ignoradas.
* Qualquer argumento de linha de comando no formato longo pode ser convertido numa chave, despojando as suas principais traços e substituindo traços entre palavras com sublinhados. Aqui estão alguns exemplos de conversão:

  |Argumento de linha de comando            | Linha de ficheiro de configuração |
  |:-------------                   |:-------------                 |
  |`-u/--api-url-base https://url`  | *api_url_base:https://url*    |
  |`-k/--access-key KEY`            | *access_key:CHAVE*              |      
  |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>Passos seguintes

Utilize os seguintes recursos para começar com a Microsoft Genomics:
- Começa por executar o teu primeiro fluxo de trabalho através do serviço Microsoft Genomics. [Executar um fluxo de trabalho através do serviço Microsoft Genomics](quickstart-run-genomics-workflow-portal.md)
- Envie os seus próprios dados para o processamento pelo serviço Microsoft Genomics: [emparelhado FASTQ](quickstart-input-pair-FASTQ.md)  |  [BAM](quickstart-input-BAM.md)  |  [Multiple FASTQ ou BAM](quickstart-input-multiple.md) 

