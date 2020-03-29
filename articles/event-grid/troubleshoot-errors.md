---
title: Azure Event Grid - Guia de resolução de problemas
description: Este artigo fornece uma lista de códigos de erro, mensagens de erro, descrições e ações recomendadas.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 1ab9aeac0bde21e229fdb57b7ad02d5d48471551
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645077"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Problemas de suturação de erros da Grelha de Eventos Azure
Este guia de resolução de problemas fornece-lhe uma lista de códigos de erro da Grelha de Eventos Do Azure, mensagens de erro, suas descrições e ações recomendadas que deve tomar quando receber estes erros. 

## <a name="error-code-400"></a>Código de erro: 400
| Código de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ----------- | -------------- | 
| httpstatuscode.badRequest<br/>400 | O nome tópico deve ter entre 3 e 50 caracteres de comprimento. | O comprimento do nome do tópico personalizado deve ter entre 3 e 50 caracteres de comprimento. Apenas letras alfanuméricas, dígitos e o caráter '-' são permitidos no nome tópico. Além disso, o nome não deve começar com as seguintes palavras reservadas: <ul><li>Microsoft</li><li>EventGrid</li><li>Sistema</li></ul> | Escolha um nome de tópico diferente que adere aos requisitos de nome tópico. |
| httpstatuscode.badRequest<br/>400 | O nome do domínio deve ter entre 3 e 50 caracteres de comprimento. | O comprimento do nome de domínio deve ter entre 3 e 50 caracteres de comprimento. Apenas letras alfanuméricas, dígitos e o caráter '-' são permitidos no nome tópico. Além disso, o nome não deve começar com as seguintes palavras reservadas:<ul><li>Microsoft</li><li>EventGrid</li><li>Sistema</li> | Escolha um nome de domínio diferente que adere aos requisitos de nome de domínio. |
| httpstatuscode.badRequest<br/>400 | Tempo de validade inválido. | O prazo de validade para a subscrição do evento determina quando a subscrição do evento se aposentará. Este valor deverá ser um valor data-tempo válido no futuro.| Certifique-se de que o tempo de validade da Subscrição do Evento está num formato DateTime válido e está definido para ser no futuro. |

## <a name="error-code-409"></a>Código de erro: 409
| Código de erro | Mensagem de erro | Descrição | Ação recomendada |
| ---------- | ------------- | ----------- | -------------- | 
| httpstatuscode.Conflict <br/>409 | Já existe tópico com o nome especificado. Escolha um nome de tópico diferente.   | O nome do tópico personalizado deve ser único numa única região de Azure, a fim de garantir uma operação de publicação correta. O mesmo nome pode ser usado em diferentes regiões de Azure. | Escolha um nome diferente para o tópico. |
| httpstatuscode.Conflict <br/> 409 | O domínio com o especificado já existe. Escolha um nome de domínio diferente. | O nome de domínio deve ser único numa única região do Azure, a fim de assegurar uma operação de publicação correta. O mesmo nome pode ser usado em diferentes regiões de Azure. | Escolha um nome diferente para o domínio. |
| httpstatuscode.Conflict<br/>409 | Limite de quota atingido. Para obter mais informações sobre estes limites, consulte [os limites da Grelha de Eventos Do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits).  | Cada subscrição do Azure tem um limite no número de recursos da Rede de Eventos Azure que pode utilizar. Parte ou a toda esta quota tinha sido ultrapassada e não podiam ser criados mais recursos. |    Verifique o uso dos seus recursos atuais e elimine os que não são necessários. Se ainda precisar de aumentar a sua [aeg@microsoft.com](mailto:aeg@microsoft.com) quota, envie um e-mail com o número exato de recursos necessários. |


## <a name="next-steps"></a>Passos seguintes
Se precisar de mais ajuda, publique o seu problema no [fórum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou abra um bilhete de [apoio](https://azure.microsoft.com/support/options/). 
