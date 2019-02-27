---
title: 'Microsoft Genomics: Perguntas mais comuns - FAQ | Documentos da Microsoft'
titleSuffix: Azure
description: Colocar respostas aos clientes de perguntas comuns sobre o Microsoft Genomics.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: df329d42022f4e56b3d6d4d26519acd42caa1ae4
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56881826"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: Perguntas comuns

Este artigo lista as principais consultas podem ter relacionadas com a Microsoft Genomics. Para obter mais informações sobre o serviço Microsoft Genomics, veja [o que é o Microsoft Genomics?](overview-what-is-genomics.md). Para obter mais informações sobre resolução de problemas, consulte nosso [guia de resolução de problemas](troubleshooting-guide-genomics.md). 

## <a name="what-is-the-microsoft-genomics-service-gatk-4-promotion"></a>O que é a promoção de GATK 4 do serviço Microsoft Genomics?
Até ao fim do ano de calendário de 2018, o serviço Microsoft Genomics está oferecendo 20 execuções WGS com GATK4 sem custos. Para participar neste registro de oferta [aqui](https://aka.ms/msgatk4). 

### <a name="what-are-the-common-issues-i-might-encounter-while-running-the-microsoft-genomics-service-gatk4-promotion"></a>Quais são os problemas comuns que poderá encontrar ao executar o Microsoft Genomics GATK4 promoção de serviço
Eis a lista de erros comuns que poderá encontrar e a respetiva resolução recomendada:

| **mensagem**                                                                                                                                                                                    | **Causa**                                                                                                    | **Resolução**                                                                                                                                                                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `gatk4-promo` não está ativada para a sua conta. Para obter mais informações, consulte https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics                               | Está a tentar executar GATK4 fluxos de trabalho com o serviço Microsoft Genomics sem a ser ativado.       | Visite [aqui](https://aka.ms/msgatk4) para ativar a sua conta. Tenha em atenção que a versão de avaliação expirará no final do ano de calendário de 2018. Não será possível ativar a sua conta para as execuções promocionais após esta data. |
| Obrigado por experimentar o `gatk4-promo`. Período de avaliação terminou. Para obter mais informações, https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics                  | A versão de avaliação do GATK4 expirou no final do ano de calendário e está a tentar invocar o `gatk4-promo` process_name.  | Mudar o parâmetro process_name, `gatk4`, em vez de `gatk4-promo`. Esta é a versão oficial gatk4 e seu fluxo de trabalho será cobrado se utilizar este parâmetro.                                         |
| Obrigado por experimentar o `gatk4-promo` tiverem utilizado todo suas execuções alocadas. Para obter mais informações, consulte https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics | Submeteu com êxito a todas as 20 promocional é executado para GATK4.                               | Submeter a qualquer nova gatk4 é executado com o argumento de process_name definido como `gatk4` em vez de `gatk4-promo`. O fluxo de trabalho será cobrado quando utiliza este parâmetro.                                                          |        


## <a name="can-i-run-gatk4-workflows-on-microsoft-genomics-without-signing-up-for-the-gatk4-promotion"></a>Posso executar fluxos de trabalho GATK4 sobre o Microsoft Genomics sem inscrever-se a promoção de GATK4?
Sim, no ficheiro Config txt do serviço Microsoft Genomics, especifique o process_name para `gatk4`. Tenha em atenção que será cobrado às tarifas regulares de faturas e executa os 20 gratuito não serão aplicáveis à sua conta do Microsoft Genomics.



## <a name="what-is-the-sla-for-microsoft-genomics"></a>O que é o SLA para o Microsoft Genomics?
Garantimos que durante 99,9% do serviço Microsoft Genomics tempo estará disponível para receber pedidos de API de fluxo de trabalho. Para obter mais informações, consulte [SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Como é o uso do Microsoft Genomics aparece na minha fatura?
Faturas do Microsoft Genomics com base no número de gigabases processadas por fluxo de trabalho. Para obter mais informações, consulte [preços](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Onde posso encontrar uma lista de todos os comandos possíveis e argumentos para o `msgen` cliente?
Pode obter uma lista completa dos comandos disponíveis e os argumentos, executando `msgen help`. Se nenhum argumento adicional for fornecido, mostra uma lista de ajuda disponíveis secções, um para cada um dos `submit`, `list`, `cancel`, e `status`. Para obter ajuda para um comando específico, escreva `msgen help command`; por exemplo, `msgen help submit` apresenta uma lista de todas as opções de envio.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Quais são os mais frequentemente utilizados comandos para o `msgen` cliente?
Os comandos mais utilizados são argumentos para o `msgen` cliente incluem: 

 |**Comando**          |  **Descrição do campo** |
 |:--------------------|:-------------         |
 |`list`               |Devolve uma lista de tarefas que tiver submetido. Para obter argumentos, consulte `msgen help list`.  |
 |`submit`             |Envia uma solicitação de fluxo de trabalho para o serviço. Para obter argumentos, consulte `msgen help submit`.|
 |`status`             |Devolve o estado do fluxo de trabalho especificado pelo `--workflow-id`. Consulte também `msgen help status`. |
 |`cancel`             |Envia uma solicitação de cancelamento de processamento do fluxo de trabalho especificado pelo `--workflow-id`. Consulte também `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Onde posso obter o valor para `--api-url-base`?
Aceda ao portal do Azure e abrir a página da sua conta do Genomics. Sob o **gerenciamento** cabeçalho, escolha **chaves de acesso**. Aí, encontrará o URL da API e as chaves de acesso.

## <a name="where-do-i-get-the-value-for---access-key"></a>Onde posso obter o valor para `--access-key`?
Aceda ao portal do Azure e abrir a página da sua conta do Genomics. Sob o **gerenciamento** cabeçalho, escolha **chaves de acesso**. Aí, encontrará o URL da API e as chaves de acesso.

## <a name="why-do-i-need-two-access-keys"></a>Por que razão necessito de duas chaves de acesso?
Tem duas chaves de acesso no caso de que pretende atualizar (voltar a gerar)-los sem interromper a utilização do serviço. Por exemplo, se quiser atualizar a primeira chave, deve ter todos os fluxos de trabalho novo, utilize a segunda chave. Em seguida, aguarde para todos os fluxos de trabalho usando a primeira chave para concluir antes de atualizar a primeira chave.

## <a name="do-you-save-my-storage-account-keys"></a>Guardar minhas chaves de conta de armazenamento?
A chave de conta de armazenamento é utilizada para criar tokens de acesso de curta duração para o serviço Microsoft Genomics ler os ficheiros de entrada e gravar os ficheiros de saída. A duração do token padrão é de 48 horas. A duração do token pode ser alterada com o `-sas/--sas-duration` opção do comando submeter; o valor é em horas.

## <a name="what-genome-references-can-i-use"></a>Faz referência a quais genoma posso utilizar?

Estas referências são suportadas:
 |Referência              | Valor de `-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (nenhuma análise alternativo) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Como posso formatar meus argumentos de linha de comandos como um ficheiro de configuração? 

msgen compreende arquivos de configuração no seguinte formato:
* Todas as opções são fornecidas como pares chave-valor com valores separados a partir das chaves por uma vírgula.
Espaço em branco é ignorado.
* Linhas que começam com `#` são ignorados.
* Qualquer argumento da linha de comandos no formato longo pode ser convertido numa chave de remoção de seu travessões líderes do setor e substituindo travessões entre as palavras com carateres de sublinhado. Aqui estão alguns exemplos de conversão:

 |Argumento da linha de comandos            | Linha do ficheiro de configuração |
 |:-------------                   |:-------------                 |
 |`-u/--api-url-base https://url`  | *api_url_base:https://url*    |
 |`-k/--access-key KEY`            | *access_key:KEY*              |      
 |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>Passos Seguintes

Utilize os seguintes recursos para começar a utilizar o Microsoft Genomics:
- Comece a utilizar ao executar o seu primeiro fluxo de trabalho através do serviço Microsoft Genomics. [Executar um fluxo de trabalho através do serviço Microsoft Genomics](quickstart-run-genomics-workflow-portal.md)
- Submeter os seus dados para processamento pelo serviço Microsoft Genomics: [FASTQ emparelhado](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [múltiplos ficheiros FASTQ ou BAM](quickstart-input-multiple.md) 

