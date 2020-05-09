---
title: Azure Event Grid - Guia de resolução de problemas
description: Este artigo fornece uma lista de códigos de erro, mensagens de erro, descrições e ações recomendadas.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 3b09b431e827bed4e416913c88d23ee1eddaf17c
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629019"
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

## <a name="troubleshoot-event-subscription-validation"></a>Validação de subscrição de eventos de resolução de problemas

Durante a criação de subscrição de eventos, se estiver a ver uma mensagem de erro como, `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation`indica que há uma falha no aperto de mão de validação. Para resolver este erro, verifique os seguintes aspetos:

- Faça uma PUBLICAÇÃO HTTP no seu url webhook com uma [amostra SubscriptionValidaçãoOrganismo](webhook-event-delivery.md#validation-details) solicitar corpo usando o Carteiro ou caracol ou ferramenta semelhante.
- Se o seu webhook estiver a implementar um mecanismo de aperto de mão de validação sincronizada, verifique se o Código de Validação é devolvido como parte da resposta.
- Se o seu webhook estiver a implementar um mecanismo de aperto de mão de validação assíncrono, verifique se é o HTTP POST que está a devolver 200 OK.
- Se o seu webhook estiver a devolver 403 (Proibido) na resposta, verifique se o seu webhook está por detrás de um Portal de Aplicações Azure ou firewall de aplicação web. Se for, então a sua necessidade de desativar estas regras de firewall e fazer um POST HTTP novamente:

  920300 (Pedido faltando um cabeçalho de aceitação, podemos corrigir isto)

  942430 (Deteção restrita de anomalias de caracteres SQL (args): # de caracteres especiais ultrapassados (12))

  920230 (Múltipla codificação de URL Detetada)

  942130 (Ataque de Injeção SQL: Tautology SQL Detetado.)

  931130 (Possível ataque de inclusão de ficheiros remotos (RFI) = Referência/Link off-domínio)


## <a name="next-steps"></a>Passos seguintes
Se precisar de mais ajuda, publique o seu problema no [fórum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou abra um bilhete de [apoio](https://azure.microsoft.com/support/options/). 
