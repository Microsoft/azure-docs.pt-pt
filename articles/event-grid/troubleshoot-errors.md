---
title: Azure Event Grid - Guia de resolução de problemas
description: Este artigo fornece uma lista de códigos de erro, mensagens de erro, descrições e ações recomendadas.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 79533918ccc6995f459b39f058de9e01091c0958
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94592996"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Resolução de problemas Erros da Grelha de Eventos do Azure
Este guia de resolução de problemas fornece-lhe as seguintes informações: 

- Códigos de erro da grelha de eventos Azure
- Mensagens de erro
- Descrições dos erros
- Ações recomendadas que deve tomar quando receber estes erros. 

## <a name="error-code-400"></a>Código de erro: 400
| Código de erro | Mensagem de erro | Description | Recomendação |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.BadRequest<br/>400 | O nome tópico deve ter entre 3 e 50 caracteres de comprimento. | O comprimento do nome do tópico personalizado deve ter entre 3 e 50 caracteres de comprimento. Apenas letras alfanuméricas, dígitos e o caráter '-' são permitidos no nome do tópico. Além disso, o nome não deve começar com as seguintes palavras reservadas: <ul><li>Microsoft...</li><li>EventGrid-</li><li>Sistema...</li></ul> | Escolha um nome de tópico diferente que adere aos requisitos de nome tópico. |
| HttpStatusCode.BadRequest<br/>400 | O nome de domínio deve ter entre 3 e 50 caracteres de comprimento. | O comprimento do nome de domínio deve ter entre 3 e 50 caracteres de comprimento. Apenas letras alfanuméricas, dígitos e o caráter '-' são permitidos no nome de domínio. Além disso, o nome não deve começar com as seguintes palavras reservadas:<ul><li>Microsoft...</li><li>EventGrid-</li><li>Sistema...</li> | Escolha um nome de domínio diferente que adere aos requisitos do nome de domínio. |
| HttpStatusCode.BadRequest<br/>400 | Tempo de validade inválido. | O tempo de validade da subscrição do evento determina quando a subscrição do evento se aposentará. Este valor deverá ser um valor de DataTime válido no futuro.| Certifique-se de que o tempo de validade da subscrição do evento num formato de DateTime válido está definido para ser no futuro. |

## <a name="error-code-409"></a>Código de erro: 409
| Código de erro | Mensagem de erro | Description | Ação recomendada |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.Conflict <br/>409 | Tema com o nome especificado já existe. Escolha um nome de tópico diferente.   | O nome de tópico personalizado deve ser único numa única região de Azure para garantir uma correta operação de publicação. O mesmo nome pode ser usado em diferentes regiões de Azure. | Escolha um nome diferente para o tópico. |
| HttpStatusCode.Conflict <br/> 409 | Domínio com o especificado já existe. Escolha um nome de domínio diferente. | O nome de domínio deve ser único numa única região de Azure para garantir uma correta operação de publicação. O mesmo nome pode ser usado em diferentes regiões de Azure. | Escolha um nome diferente para o domínio. |
| HttpStatusCode.Conflict<br/>409 | Limite de quota atingido. Para obter mais informações sobre estes limites, consulte [os limites da Grelha de Eventos Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits).  | Cada subscrição do Azure tem um limite no número de recursos da Azure Event Grid que pode utilizar. Parte ou a parte desta quota tinha sido ultrapassada e não era possível criar mais recursos. |    Verifique o uso dos seus recursos atuais e elimine os que não são necessários. Se ainda precisar de aumentar a sua quota, envie um e-mail [aeg@microsoft.com](mailto:aeg@microsoft.com) para o número exato de recursos necessários. |

## <a name="error-code-403"></a>Código de erro: 403

| Código de erro | Mensagem de erro | Description | Ação recomendada |
| ---------- | ------------- | ----------- | ------------------ |
| HttpStatusCode.Proibido <br/>403 | A publicação para {Topic/Domain} pelo cliente {IpAddress} é rejeitada devido às regras de filtragem do IpAddress. | O tópico ou domínio tem regras de firewall IP configuradas e o acesso é restrito apenas a endereços IP configurados. | Adicione o endereço IP às regras de firewall IP, consulte [firewall IP configurar](configure-firewall.md) |
| HttpStatusCode.Proibido <br/> 403 | A publicação para {Topic/Domain} pelo cliente é rejeitada como pedido veio do Private Endpoint e nenhuma ligação de ponto final privado correspondente encontrada para o recurso. | O tópico ou domínio tem pontos finais privados e o pedido de publicação veio de um ponto final privado que não está configurado ou aprovado. | Configure um ponto final privado para o tópico/domínio. [Configurar pontos finais privados](configure-private-endpoints.md) |

Além disso, verifique se o seu webhook está por trás de um Gateway de Aplicação Azure ou firewall de aplicação web. Se for, desative as seguintes regras de firewall e faça um HTTP POST novamente:

- 920300 (Pedido de falta de um cabeçalho de aceitação)
- 942430 (Deteção restrita de anomalias de caracteres SQL (args): # de caracteres especiais excedidos (12))
- 920230 (Codificação de URL múltipla detetada)
- 942130 (ataque de injeção SQL: tautologia SQL detetada.)
- 931130 (Possível inclusão de ficheiros remotos (RFI) ataque = referência/ligação off-domain)



## <a name="next-steps"></a>Passos seguintes
Se precisar de mais ajuda, publique o seu problema no [fórum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou abra um bilhete de [apoio.](https://azure.microsoft.com/support/options/) 
