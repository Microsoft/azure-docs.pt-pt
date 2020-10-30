---
title: Azure Event Grid - Guia de resolução de problemas
description: Este artigo fornece uma lista de códigos de erro, mensagens de erro, descrições e ações recomendadas.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 1dd464339e7654f8886224ff07cf368b4724ff82
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041396"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Resolução de problemas Erros da Grelha de Eventos do Azure
Este guia de resolução de problemas fornece-lhe uma lista de códigos de erro da Grelha de Eventos Azure, mensagens de erro, suas descrições e ações recomendadas que deve tomar quando receber estes erros. 

## <a name="error-code-400"></a>Código de erro: 400
| Código de erro | Mensagem de erro | Description | Recomendação |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.BadRequest<br/>400 | O nome tópico deve ter entre 3 e 50 caracteres de comprimento. | O comprimento do nome do tópico personalizado deve ter entre 3 e 50 caracteres de comprimento. Apenas letras alfanuméricas, dígitos e o caráter '-' são permitidos no nome do tópico. Além disso, o nome não deve começar com as seguintes palavras reservadas: <ul><li>Microsoft...</li><li>EventGrid-</li><li>Sistema...</li></ul> | Escolha um nome de tópico diferente que adere aos requisitos de nome tópico. |
| HttpStatusCode.BadRequest<br/>400 | O nome de domínio deve ter entre 3 e 50 caracteres de comprimento. | O comprimento do nome de domínio deve ter entre 3 e 50 caracteres de comprimento. Apenas letras alfanuméricas, dígitos e o caráter '-' são permitidos no nome de domínio. Além disso, o nome não deve começar com as seguintes palavras reservadas:<ul><li>Microsoft...</li><li>EventGrid-</li><li>Sistema...</li> | Escolha um nome de domínio diferente que adere aos requisitos do nome de domínio. |
| HttpStatusCode.BadRequest<br/>400 | Tempo de validade inválido. | O tempo de validade da subscrição do evento determina quando a subscrição do evento se aposentará. Este valor deverá ser um valor de DataTime válido no futuro.| Certifique-se de que o tempo de validade da subscrição do evento num formato de DateTime válido está definido para ser no futuro. |

## <a name="error-code-409"></a>Código de erro: 409
| Código de erro | Mensagem de erro | Description | Ação recomendada |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.Conflict <br/>409 | Tema com o nome especificado já existe. Escolha um nome de tópico diferente.   | O nome de tópico personalizado deve ser único numa única região de Azure, a fim de garantir uma correta operação de publicação. O mesmo nome pode ser usado em diferentes regiões de Azure. | Escolha um nome diferente para o tópico. |
| HttpStatusCode.Conflict <br/> 409 | Domínio com o especificado já existe. Escolha um nome de domínio diferente. | O nome de domínio deve ser único numa única região de Azure, a fim de garantir um correto funcionamento editorial. O mesmo nome pode ser usado em diferentes regiões de Azure. | Escolha um nome diferente para o domínio. |
| HttpStatusCode.Conflict<br/>409 | Limite de quota atingido. Para obter mais informações sobre estes limites, consulte [os limites da Grelha de Eventos Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits).  | Cada subscrição do Azure tem um limite no número de recursos da Azure Event Grid que pode utilizar. Parte ou a parte desta quota tinha sido ultrapassada e não era possível criar mais recursos. |    Verifique o uso dos seus recursos atuais e elimine os que não são necessários. Se ainda precisar de aumentar a sua quota, envie um e-mail [aeg@microsoft.com](mailto:aeg@microsoft.com) para o número exato de recursos necessários. |

## <a name="error-code-403"></a>Código de erro: 403

| Código de erro | Mensagem de erro | Description | Ação recomendada |
| ---------- | ------------- | ----------- | ------------------ |
| HttpStatusCode.Proibido <br/>403 | A publicação para {Topic/Domain} pelo cliente {IpAddress} é rejeitada devido às regras de filtragem do IpAddress. | O tópico ou domínio tem regras de firewall IP configuradas e o acesso é restrito apenas a endereços IP configurados. | Adicione o endereço IP às regras de firewall IP, consulte [firewall IP configurar](configure-firewall.md) |
| HttpStatusCode.Proibido <br/> 403 | A publicação para {Topic/Domain} pelo cliente é rejeitada como pedido veio do Private Endpoint e nenhuma ligação de ponto final privado correspondente encontrada para o recurso. | O tópico ou domínio tem private endpoints configurados e o pedido de publicação veio de um ponto final privado que não é configurado/aprovado. | Configure um ponto final privado para o tópico/domínio. [Configurar pontos finais privados](configure-private-endpoints.md) |

## <a name="troubleshoot-event-subscription-validation"></a>Validação de subscrição de eventos de resolução de problemas

Durante a criação de subscrição de eventos, se estiver a ver uma mensagem de erro `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation` como, indica que há uma falha no aperto de mão de validação. Para resolver este erro, verifique os seguintes aspetos:

- Faça um HTTP POST no seu url webhook com uma [amostra SubscriçãoSevent](webhook-event-delivery.md#validation-details) request body usando Carteiro ou curl ou ferramenta semelhante.
- Se o seu webhook estiver a implementar um mecanismo de aperto de mão de validação sincronizado, verifique se o Código de Validação é devolvido como parte da resposta.
- Se o seu webhook estiver a implementar um mecanismo de aperto de mão de validação assíncrona, verifique se é o HTTP POST que devolve 200 OK.
- Se o seu webhook estiver a devolver o 403 (Proibido) na resposta, verifique se o seu webhook está por detrás de um Gateway de Aplicações Azure ou firewall de aplicação web. Se for, então precisa de desativar estas regras de firewall e fazer um HTTP POST novamente:

  920300 (Solicito falta de um cabeçalho de aceitação, podemos corrigir isto)

  942430 (Deteção restrita de anomalias de caracteres SQL (args): # de caracteres especiais excedidos (12))

  920230 (Encoding multiple URL Detetado)

  942130 (SqL Injection Attack: SQL Tautology Detected.)

  931130 (Possível Inclusão de Ficheiros Remotos (RFI) Ataque = Off-Domain Referência/Ligação)


## <a name="next-steps"></a>Passos seguintes
Se precisar de mais ajuda, publique o seu problema no [fórum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou abra um bilhete de [apoio.](https://azure.microsoft.com/support/options/) 
