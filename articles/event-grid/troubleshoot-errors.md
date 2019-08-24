---
title: Grade de eventos do Azure-guia de solução de problemas
description: Este artigo fornece uma lista de códigos de erro, mensagens de erro, descrições e ações recomendadas.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 84a227d832c45bdce6f16578b9c52edbc171a5f8
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69984501"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Solucionar erros de grade de eventos do Azure
Este guia de solução de problemas fornece uma lista de códigos de erro da grade de eventos do Azure, mensagens de erro, suas descrições e ações recomendadas que você deve executar ao receber esses erros. 

## <a name="error-code-409"></a>Código de erro: 409
| Código de erro | Mensagem de erro | Descrição | Ação recomendada |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. Conflict <br/>409 | Já existe um tópico {0} com o nome. Escolha um nome de tópico diferente. | O nome do tópico personalizado deve ser exclusivo em uma única região do Azure para garantir uma operação de publicação correta. O mesmo nome pode ser usado em diferentes regiões do Azure. | Escolha um nome diferente para o tópico. |
| HttpStatusCode. Conflict <br/> 409 | Já existe um domínio {0} com o nome. Escolha um nome de domínio diferente. | O nome de domínio deve ser exclusivo em uma única região do Azure para garantir uma operação de publicação correta. O mesmo nome pode ser usado em diferentes regiões do Azure. | Escolha um nome diferente para o domínio. |
| HttpStatusCode. Conflict<br/>409 | Limite de cota atingido. Para obter mais informações sobre esses limites, consulte [limites de grade de eventos do Azure](../azure-subscription-service-limits.md#event-grid-limits).  | Cada assinatura do Azure tem um limite para o número de recursos da grade de eventos do Azure que ele pode usar. Algumas ou todas essas cotas foram excedidas e não foi possível criar mais recursos. |  Verifique o uso dos recursos atuais e exclua os que não forem necessários. Se você ainda precisar aumentar sua cota, envie um email para [aeg@microsoft.com](mailto:aeg@microsoft.com) com o número exato de recursos necessários. |

## <a name="error-code-400"></a>Código de erro: 400
| Código de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. BadRequest<br/>400 | O nome do tópico deve ter entre 3 e 50 caracteres de comprimento. | O comprimento do nome do tópico personalizado deve ter entre 3 e 50 caracteres de comprimento. Somente letras alfanuméricas, dígitos e o caractere '-' são permitidos no nome do tópico. Além disso, o nome não deve começar com as seguintes palavras reservadas: <ul><li>Microsoft</li><li>EventGrid</li><li>Sistema</li></ul> | Escolha um nome de tópico diferente que esteja de acordo com os requisitos de nome do tópico. |
| HttpStatusCode. BadRequest<br/>400 | O nome de domínio deve ter entre 3 e 50 caracteres de comprimento. | O comprimento do nome de domínio deve ter entre 3 e 50 caracteres de comprimento. Somente letras alfanuméricas, dígitos e o caractere '-' são permitidos no nome do tópico. Além disso, o nome não deve começar com as seguintes palavras reservadas:<ul><li>Microsoft</li><li>EventGrid</li><li>Sistema</li> | Escolha um nome de domínio diferente que atenda aos requisitos de nome de domínio. |
| HttpStatusCode. BadRequest<br/>400 | Tempo de expiração inválido. | O tempo de expiração da assinatura de evento determina quando a assinatura do evento será desativada. Esse valor deve ser um valor DateTime válido no futuro.| Verifique se o tempo de expiração da assinatura do evento está em um formato válido de DateTime e se está definido como no futuro. |

## <a name="next-steps"></a>Passos seguintes
Se precisar de mais ajuda, poste seu problema no [Fórum de Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou abra um [tíquete de suporte](https://azure.microsoft.com/support/options/). 
