---
title: Perguntas comuns-perguntas frequentes
titleSuffix: Microsoft Genomics
description: Obtenha respostas para perguntas comuns relacionadas ao uso do serviço de Microsoft Genomics, incluindo informações técnicas, SLA e cobrança.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: troubleshooting
ms.date: 12/07/2017
ms.openlocfilehash: 4a2b66f95467e7f6cb99f632548351f827e259c3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476427"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: perguntas comuns

Este artigo lista as principais consultas que você pode ter relacionadas a Microsoft Genomics. Para obter mais informações sobre o serviço de Microsoft Genomics, consulte [o que é Microsoft Genomics?](overview-what-is-genomics.md). Para obter mais informações sobre solução de problemas, consulte nosso [Guia de solução de problemas](troubleshooting-guide-genomics.md). 


## <a name="how-do-i-run-gatk4-workflows-on-microsoft-genomics"></a>Como fazer executar fluxos de trabalho do GATK4 no Microsoft Genomics?
No arquivo config. txt do serviço de Microsoft Genomics, especifique o process_name para `gatk4`. Observe que você será cobrado com tarifas de cobrança regulares.


## <a name="what-is-the-sla-for-microsoft-genomics"></a>O que é o SLA para Microsoft Genomics?
Garantimos que 99,9% do tempo Microsoft Genomics serviço estarão disponíveis para receber solicitações de API de fluxo de trabalho. Para obter mais informações, consulte [SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Como o uso de Microsoft Genomics aparece na minha fatura?
Microsoft Genomics Bills com base no número de gigabases processados por fluxo de trabalho. Para obter mais informações, consulte [preços](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Onde posso encontrar uma lista de todos os comandos e argumentos possíveis para o cliente `msgen`?
Você pode obter uma lista completa de comandos e argumentos disponíveis executando `msgen help`. Se nenhum argumento adicional for fornecido, ele mostrará uma lista de seções de ajuda disponíveis, uma para cada `submit`, `list`, `cancel`e `status`. Para obter ajuda para um comando específico, digite `msgen help command`; por exemplo, `msgen help submit` lista todas as opções de envio.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Quais são os comandos mais comumente usados para o cliente `msgen`?
Os comandos usados com mais frequência são argumentos para o `msgen` cliente incluem: 

 |**Comando**          |  **Descrição do campo** |
 |:--------------------|:-------------         |
 |`list`               |Retorna uma lista de trabalhos que você enviou. Para argumentos, consulte `msgen help list`.  |
 |`submit`             |Envia uma solicitação de fluxo de trabalho para o serviço. Para argumentos, consulte `msgen help submit`.|
 |`status`             |Retorna o status do fluxo de trabalho especificado por `--workflow-id`. Consulte também `msgen help status`. |
 |`cancel`             |Envia uma solicitação para cancelar o processamento do fluxo de trabalho especificado por `--workflow-id`. Consulte também `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Onde obtenho o valor para `--api-url-base`?
Vá para portal do Azure e abra a página da sua conta do genomas. No título **Gerenciamento** , escolha **chaves de acesso**. Lá, você encontrará a URL da API e suas chaves de acesso.

## <a name="where-do-i-get-the-value-for---access-key"></a>Onde obtenho o valor para `--access-key`?
Vá para portal do Azure e abra a página da sua conta do genomas. No título **Gerenciamento** , escolha **chaves de acesso**. Lá, você encontrará a URL da API e suas chaves de acesso.

## <a name="why-do-i-need-two-access-keys"></a>Por que preciso de duas chaves de acesso?
Você precisa de duas chaves de acesso caso queira atualizá-las (gerá-las novamente) sem interromper o uso do serviço. Por exemplo, se você quiser atualizar a primeira chave, você deverá ter todos os novos fluxos de trabalho usando a segunda chave. Em seguida, aguarde até que todos os fluxos de trabalho que usam a primeira chave sejam concluídos antes de atualizar a primeira chave.

## <a name="do-you-save-my-storage-account-keys"></a>Você salva minhas chaves de conta de armazenamento?
Sua chave de conta de armazenamento é usada para criar tokens de acesso de curto prazo para o serviço de Microsoft Genomics ler os arquivos de entrada e gravar os arquivos de saída. A duração padrão do token é de 48 horas. A duração do token pode ser alterada com a opção `-sas/--sas-duration` do comando enviar; o valor é em horas.

## <a name="what-genome-references-can-i-use"></a>Quais referências de genoma posso usar?

Há suporte para essas referências:

 |Referência              | Valor de `-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (nenhuma análise Alt) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Como fazer Formatar meus argumentos de linha de comando como um arquivo de configuração? 

o msgen compreende os arquivos de configuração no seguinte formato:
* Todas as opções são fornecidas como pares de chave-valor com valores separados de chaves por dois-pontos.
  O espaço em branco é ignorado.
* As linhas que começam com `#` são ignoradas.
* Qualquer argumento de linha de comando no formato longo pode ser convertido em uma chave, removendo seus traços à esquerda e substituindo traços entre palavras com sublinhados. Aqui estão alguns exemplos de conversão:

  |Argumento de linha de comando            | Linha do arquivo de configuração |
  |:-------------                   |:-------------                 |
  |`-u/--api-url-base https://url`  | *api_url_base: https://url*    |
  |`-k/--access-key KEY`            | *access_key: chave*              |      
  |`-pa/--process-args R=B37m1`     | *process_args: R-b37m1*        |  

## <a name="next-steps"></a>Passos seguintes

Use os seguintes recursos para começar a usar o Microsoft Genomics:
- Comece executando seu primeiro fluxo de trabalho por meio do serviço de Microsoft Genomics. [Executar um fluxo de trabalho por meio do serviço de Microsoft Genomics](quickstart-run-genomics-workflow-portal.md)
- Envie seus próprios dados para processamento pelo serviço de Microsoft Genomics: [FASTQ emparelhado](quickstart-input-pair-FASTQ.md) | [Bam](quickstart-input-BAM.md) | [vários FASTQ ou Bam](quickstart-input-multiple.md) 

