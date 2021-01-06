---
title: SaaS cumpre APIs v2 no mercado comercial da Microsoft
description: Aprenda a criar e gerir uma oferta SaaS no Microsoft AppSource e no Azure Marketplace utilizando a versão 2 das APIs de realização.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 0d7259972693b9ca12e0801007cbd3c0af45ca1c
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937336"
---
# <a name="saas-fulfillment-apis-version-2-in-the-commercial-marketplace"></a>SaaS cumpre APIs versão 2 no mercado comercial

Este artigo detalha as APIs que permitem aos parceiros vender o seu software como um serviço (SaaS) oferece no Microsoft AppSource e no Azure Marketplace. Um editor é obrigado a implementar a integração com estas APIs para publicar uma oferta de SaaS transacionável no Partner Center.

## <a name="managing-the-saas-subscription-life-cycle"></a>Gerir o ciclo de vida de subscrição saaS

O mercado comercial gere todo o ciclo de vida de uma subscrição SaaS após a sua compra pelo utilizador final. Utiliza a página de aterragem, APIs de Realização, APIs de Operações e o webhook como um mecanismo para impulsionar a ativação, utilização, atualizações e cancelamentos reais da subscrição do SaaS. A conta do utilizador final baseia-se no estado da subscrição saaS que a Microsoft mantém. 

### <a name="states-of-a-saas-subscription"></a>Estados de uma assinatura SaaS

O diagrama seguinte mostra os estados de uma subscrição saaS e as ações aplicáveis.

![Diagrama mostrando o ciclo de vida de um software como uma subscrição de serviço no mercado.](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>Comprado mas ainda não ativado *(Pendente Desempreso)*

Depois de um utilizador final (ou CSP) adquirir uma oferta SaaS no mercado comercial, o editor deve ser notificado da compra. A editora pode então criar e configurar uma nova conta SaaS do lado da editora para o utilizador final.

Para que a criação de contas aconteça:

1. O cliente seleciona o botão **Configure** que está disponível para uma oferta SaaS após a sua compra bem sucedida no Microsoft AppSource ou no portal Azure. Em alternativa, o cliente pode utilizar o botão **Configure** no e-mail que receberá logo após a compra.
2. Em seguida, a Microsoft notifica o parceiro sobre a compra abrindo no novo separador de navegador o URL da página de aterragem com o parâmetro simbólico (o símbolo de identificação de compra do mercado comercial).

Um exemplo desta chamada `https://contoso.com/signup?token=<blob>` é, enquanto que a placa de aterragem URL para esta oferta SaaS no Partner Center está configurada como `https://contoso.com/signup` . Este token fornece à editora um ID que identifica exclusivamente a compra da SaaS e o cliente.

>[!NOTE]
>A editora não será notificada da compra do SaaS até que o cliente inicie o processo de configuração a partir do lado da Microsoft.

O URL da página de aterragem deve estar a funcionar todos os dias, todos os dias, e pronto para receber sempre novas chamadas da Microsoft. Se a página de aterragem ficar indisponível, os clientes não poderão inscrever-se no serviço SaaS e começar a usá-la.

Em seguida, a editora deve passar o *token* de volta para a Microsoft, chamando a [API SaaS Resolve](#resolve-a-purchased-subscription), e introduzindo o token como o valor do parâmetro do `x-ms-marketplace-token header` cabeçalho. Como resultado da chamada resolve a API, o token é trocado por detalhes da compra do SaaS, como iD exclusivo da compra, ID de oferta comprada e ID do plano adquirido.

Na página de aterragem, o cliente deve ser inscrito na nova ou existente conta SaaS via Azure Ative Directory (Azure AD) single sign-on (SSO).

O editor deve implementar sSO para fornecer a experiência do utilizador exigida pela Microsoft para este fluxo. Certifique-se de que utiliza a aplicação AD Azure multitenant e permite tanto contas de trabalho como escolas ou contas pessoais da Microsoft ao configurar sSO. Este requisito aplica-se apenas à página de aterragem, para utilizadores redirecionados para o serviço SaaS quando já assinaram com as credenciais da Microsoft. SSO não é necessário para todos os insurretos para o serviço SaaS.

> [!NOTE]
>Se o SSO exigir que um administrador conceda permissão a uma aplicação, a descrição da oferta no Partner Center deve revelar que é necessário acesso ao nível de administração. Esta divulgação é para cumprir as [políticas de certificação do mercado comercial.](/legal/marketplace/certification-policies#10003-authentication-options)

Após a sindução, o cliente deverá completar a configuração SaaS no lado da editora. Em seguida, o editor deve ligar para a [API de subscrição ativa](#activate-a-subscription) para enviar um sinal ao Azure Marketplace de que o provisionamento da conta SaaS está completo.
Esta ação iniciará o ciclo de faturação do cliente. Se a chamada API de Subscrição Ativada não for bem sucedida, o cliente não é cobrado para a compra.


![Diagrama mostrando o A P I pede um cenário de provisionamento.](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>Ativo *(Subscrito)*

*Ative (Subscrevido)* é o estado estável de uma subscrição de SaaS a provisionada. Depois de o lado da Microsoft ter processado a chamada [API de subscrição ativa,](#activate-a-subscription) a subscrição SaaS é marcada como *Subscrita*. O cliente pode agora utilizar o serviço SaaS do lado da editora e será cobrado.

Quando uma subscrição saaS já está ativa, o cliente pode selecionar a **experiência Manage SaaS** a partir do portal Azure ou do Microsoft 365 Admin Center. Esta ação também faz com que a Microsoft chame o URL da **página de aterragem** com o parâmetro *token,* como acontece no fluxo Activate. O editor deve distinguir entre novas compras e a gestão das contas SaaS existentes, e lidar com esta chamada DE URL da página de aterragem em conformidade.

#### <a name="being-updated-subscribed"></a>Ser atualizado *(Subscrito)*

Esta ação significa que uma atualização para uma subscrição Ativa do SaaS existente está a ser processada tanto pela Microsoft como pela editora. Tal atualização pode ser iniciada por:

- O cliente do mercado comercial.
- O CSP do mercado comercial.
- O cliente do site SaaS da editora (mas não para compras feitas pela CSP).

Dois tipos de atualizações estão disponíveis para uma subscrição SaaS:

- Plano de atualização quando o cliente escolher outro plano para a subscrição.
- Atualize a quantidade quando o cliente alterar o número de lugares adquiridos para a subscrição.

Apenas uma subscrição ativa pode ser atualizada. Enquanto a subscrição está a ser atualizada, o seu estado permanece ativo no lado da Microsoft.

##### <a name="update-initiated-from-the-commercial-marketplace"></a>Atualização iniciada a partir do mercado comercial

Neste fluxo, o cliente altera o plano de subscrição ou a quantidade de lugares do portal Azure ou do Microsoft 365 Admin Center.

1. Após a introdução de uma atualização, a Microsoft chamará o URL webhook da editora, configurado no campo **Connection Webhook** no Partner Center, com um valor adequado para *a ação* e outros parâmetros relevantes. 
1. O lado da editora deve escrutinar as alterações necessárias ao serviço SaaS e notificar a Microsoft quando terminar, ligando para o [Estado de Atualização da Operação API](#update-the-status-of-an-operation).
1. Se o patch for enviado com *estado de falha,* o processo de atualização não terminará no lado da Microsoft. A assinatura SaaS manterá o plano existente e a quantidade de lugares.

> [!NOTE]
> A editora deverá invocar patch para [atualizar o Estado da Operação API](#update-the-status-of-an-operation) com uma resposta De Falha/Sucesso dentro de uma janela temporal de *10 segundos* após receber a notificação webhook. Se o estado de funcionamento do PATCH não for recebido dentro dos 10 segundos, o plano de alteração é *automaticamente corrigido como Sucesso*. 

A sequência de API requer um cenário de atualização iniciado a partir do mercado comercial é mostrada no diagrama seguinte.

![Diagrama mostrando o A P I pede uma atualização iniciada no mercado.](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>Atualização iniciada pela editora

Neste fluxo, o cliente altera o plano de subscrição ou a quantidade de lugares adquiridos no próprio serviço SaaS. 

1. O código do editor deve ligar para a [API](#change-the-plan-on-the-subscription) do Plano de Alteração e/ou para a [API de Alteração de Quantidade](#change-the-quantity-of-seats-on-the-saas-subscription) antes de e fazer a alteração solicitada no lado da editora. 

1. A Microsoft aplicará a alteração à subscrição e, em seguida, notificará o editor através **do Connection Webhook** para aplicar a mesma alteração.

1. Só então o editor deve fazer a alteração necessária à subscrição saaS e notificar a Microsoft quando a alteração for feita chamando [a Atualização estado da Operação API](#update-the-status-of-an-operation).

A sequência de API requer um cenário de atualização iniciado pelo lado da editora é mostrada no diagrama seguinte.

![Diagrama mostrando o A P I pede uma atualização iniciada pelo lado da editora.](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>Suspenso *(suspenso)*

Este estado indica que o pagamento de um cliente pelo serviço SaaS não foi recebido. A editora será notificada desta alteração no estado de subscrição do SaaS pela Microsoft. A notificação é feita através de uma chamada para webhook com o parâmetro *de ação* definido para *Suspenso*.

A editora pode ou não fazer alterações no serviço SaaS do lado da editora. Recomendamos que o editor disponibilize esta informação ao cliente suspenso e limites ou bloqueie o acesso do cliente ao serviço SaaS. Há uma probabilidade de o pagamento nunca ser recebido.

A Microsoft dá ao cliente um período de carência de 30 dias antes de cancelar automaticamente a subscrição. Quando uma subscrição está no estado *suspenso:*

* O parceiro ou ISV deve manter a conta SaaS num estado recuperável, para que a funcionalidade completa possa ser restaurada sem qualquer perda de dados ou configurações.
* O parceiro ou ISV deve esperar um pedido de reintegração da subscrição, se o pagamento for recebido durante o período de carência, ou um pedido de desagravamento da subscrição no final do período de carência. Ambos os pedidos serão enviados através do mecanismo webhook.

O estado de subscrição é alterado para Suspenso do lado da Microsoft antes que a editora tome qualquer medida. Apenas as subscrições ativas podem ser suspensas.

#### <a name="reinstated-suspended"></a>Reintegrado *(Suspenso)*

Esta ação indica que o instrumento de pagamento do cliente voltou a ser válido, foi feito um pagamento para a subscrição do SaaS e a subscrição está a ser reposta. Neste caso: 

1. A Microsoft chama webhook com um parâmetro *de ação* definido para o valor *de Reinserção.*
1. A editora garante que a subscrição está totalmente operacional novamente no lado da editora.
1. A editora chama à API a [Operação Patch com](#update-the-status-of-an-operation) estatuto de sucesso.
1. O processo De reintegração é bem sucedido e o cliente é novamente cobrado para a subscrição saaS. 

Se o patch for enviado com *estado de falha,* o processo de reintegração não terminará no lado da Microsoft e a subscrição permanecerá *suspensa*.

Apenas uma subscrição suspensa pode ser reintegrada. A assinatura suspensa do SaaS permanece em estado *suspenso* enquanto está a ser reintegrada. Após o final desta operação, o estado da subscrição passará a ser *Ativo*.

#### <a name="renewed-subscribed"></a>Renovado *(Subscrito)*

A subscrição saaS é automaticamente renovada pela Microsoft no final do período de subscrição de um mês ou um ano. O padrão para a definição de renovação automática é *verdadeiro* para todas as subscrições saaS. As subscrições Ative SaaS continuarão a ser renovadas com uma cadência regular. A Microsoft não notifica o editor quando uma subscrição está a ser renovada. Um cliente pode desativar a renovação automática de uma subscrição SaaS através do Portal de Administração Microsoft 365. Neste caso, a subscrição do SaaS será automaticamente cancelada no final do atual período de faturação. Os clientes também podem cancelar a subscrição do SaaS a qualquer momento.

Apenas as subscrições ativas são renovadas automaticamente. As subscrições permanecem ativas durante o processo de renovação, e se a renovação automática for bem sucedida. Após a renovação, as datas de início e fim do prazo de subscrição são atualizadas para as datas do novo termo.

Se uma renovação automática falhar devido a um problema com o pagamento, a subscrição será *suspensa* e a editora será notificada.

#### <a name="canceled-unsubscribed"></a>Cancelado *(Não inscrito)* 

As subscrições chegam a este estado em resposta a uma ação explícita de cliente ou CSP pelo cancelamento de uma subscrição do site da editora, do portal Azure ou do Microsoft 365 Admin Center. Uma subscrição também pode ser cancelada implicitamente, em resultado do não pagamento de dívidas, depois de ter estado no estado *suspenso* por 30 dias.

Após a editora receber uma chamada webhook de cancelamento, devem reter os dados do cliente para recuperação a pedido durante pelo menos sete dias. Só então os dados do cliente podem ser eliminados.

Uma subscrição SaaS pode ser cancelada em qualquer ponto do seu ciclo de vida. Depois de uma subscrição ser cancelada, não pode ser reativada.

## <a name="api-reference"></a>Referência da API

Esta secção documenta as APIs de Subscrição e Operações do SaaS.

**As APIs de subscrição** devem ser usadas para lidar com o ciclo de vida de subscrição saaS desde a compra até ao cancelamento.

**As APIs de operações** devem ser utilizadas para:

* Verifique e reconheça as chamadas processadas do webhook.
* Obtenha uma lista de aplicações pendentes de operações que estão à espera de serem reconhecidas pela editora.

> [!NOTE]
> A versão TLS versão 1.2 será aplicada logo que a versão mínima para comunicações HTTPS. Certifique-se de que utiliza esta versão TLS no seu código. As versões TLS 1.0 e 1.1 serão em breve depreciadas.

### <a name="subscription-apis"></a>APIs de subscrição

#### <a name="resolve-a-purchased-subscription"></a>Resolver uma subscrição comprada

O ponto final de resolução permite ao editor trocar o token de identificação de compra do mercado comercial (referido como *símbolo* em [Comprado mas ainda não ativado)](#purchased-but-not-yet-activated-pendingfulfillmentstart)para um ID de subscrição saas comprado persistente e seus detalhes.

Quando um cliente é redirecionado para o URL da página de aterragem do parceiro, o token de identificação do cliente é passado como o parâmetro *simbólico* nesta chamada de URL. Espera-se que o parceiro use este token e faça um pedido para resolvê-lo. A resposta Resolve API contém o ID de subscrição saaS e outros detalhes para identificar exclusivamente a compra. O *token* fornecido com a chamada URL da página de aterragem é geralmente válido por 24 horas. Se o *sinal* que recebe já expirou, recomendamos que forneça as seguintes orientações ao utilizador final:

"Não conseguimos identificar esta compra. Por favor, reabra esta subscrição do SaaS no portal Azure ou no Microsoft 365 Admin Center e selecione "Configure Account" ou "Manage Account" novamente."

Chamar a API resolve devolverá detalhes de subscrição e estado para subscrições SaaS em todos os status suportados.

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Publicar `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor            |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.   |

*Pedido de cabeçalhos:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json` |
|  `x-ms-requestid`    |  Um valor de corda único para acompanhar o pedido do cliente, de preferência um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  `x-ms-correlationid` |  Um valor de corda único para operar no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  `authorization`     |  Um token de acesso único que identifica a editora que faz esta chamada da API. O formato é `"Bearer <accessaccess_token>"` quando o valor simbólico é recuperado pela editora, como explicado na Get a [token com base na aplicação AD AZure](./pc-saas-registration.md#get-the-token-with-an-http-post). |
|  `x-ms-marketplace-token`  | O parâmetro *simbólico* de identificação de compra para resolver.  O token é passado na chamada URL da página de aterragem quando o cliente é redirecionado para o site do parceiro SaaS (por exemplo: `https://contoso.com/signup?token=<token><authorization_token>` ). <br> <br>  Note que o valor simbólico que está *a* ser codificado faz parte do URL da página de aterragem, pelo que tem de ser descodificado antes de ser usado como parâmetro nesta chamada da API.  <br> <br> Aqui está um exemplo de uma cadeia codificada na URL: `contoso.com/signup?token=ab%2Bcd%2Fef` , onde está o *símbolo* `ab%2Bcd%2Fef` .  O mesmo símbolo descodificado será: `Ab+cd/ef` |
| | |

*Códigos de resposta:*

Código: 200 Devolve identificadores exclusivos de subscrição SaaS com base nos `x-ms-marketplace-token` fornecidos.

Exemplo do corpo de resposta:

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "subscriptionName": "Contoso Cloud Solution", // SaaS subscription name
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased offer's plan ID
  "quantity": "20", // number of purchased seats, might be empty if the plan is not per seat
  "subscription": { // full SaaS subscription details, see Get Subscription APIs response body for full description
    "id": "<guid>",
    "publisherId": "contoso",
    "offerId": "offer1",
    "name": "Contoso Cloud Solution",
    "saasSubscriptionStatus": " PendingFulfillmentStart ",
    "beneficiary": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "purchaser": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "planId": "silver",
    "term": {
      "termUnit": "P1M",
      "startDate": "2019-05-31",
      "endDate": "2019-06-29"
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": ["Delete", "Update", "Read"],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

Código: 400 Mau pedido. `x-ms-marketplace-token` está desaparecido, mal formado, inválido ou caducado.

Código: 403 Proibido. O sinal de autorização é inválido, caducado ou não foi fornecido.  O pedido está a tentar aceder a uma subscrição do SaaS para uma oferta que foi publicada com um ID de aplicação AD AZure diferente daquele usado para criar o token de autorização.

Este erro é frequentemente um sintoma de não realizar corretamente o [registo SaaS.](pc-saas-registration.md)

Código: 500 Erro interno do servidor.  Re-tentar a chamada da API.  Se o erro persistir, contacte o [suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="activate-a-subscription"></a>Ativar uma subscrição

Depois de a conta SaaS ser configurada para um utilizador final, o editor deve ligar para a API de subscrição ativada do lado da Microsoft.  O cliente não será cobrado a menos que esta chamada da API seja bem sucedida.

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Publicar `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Use 2018-08-31.   |
| `subscriptionId` | O identificador único da assinatura SaaS comprada.  Este ID é obtido após a resolução do token de autorização do mercado comercial utilizando a [API resolvendo.](#resolve-a-purchased-subscription)
 |

*Pedido de cabeçalhos:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Um valor de corda único para acompanhar o pedido do cliente, de preferência um GUID.  Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| `x-ms-correlationid` |  Um valor de corda único para operar no cliente.  Esta cadeia correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| `authorization`      |  Um token de acesso único que identifica a editora que faz esta chamada da API. O formato é `"Bearer <access_token>"` quando o valor simbólico é recuperado pela editora, como explicado na Get a [token com base na aplicação AD AZure](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Solicitar exemplo de carga útil:*

```json
{  // needed for validation of the activation request
  "planId": "gold", // purchased plan, cannot be empty
  "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*Códigos de resposta:*

Código: 200 A subscrição foi marcada como Subscrita no lado da Microsoft.

Não há nenhum corpo de resposta para esta chamada.

Código: 400 Mau pedido: a validação falhou.

* `planId` não existe no pedido de carga útil.
* `planId` a pedido de carga útil não corresponde à que foi comprada.
* `quantity` a pedido carga não corresponde ao que foi comprado
* A subscrição saaS está em estado *subscrito* ou *suspenso.*

Código: 403 Proibido. O sinal de autorização é inválido, caducado ou não foi fornecido. O pedido está a tentar aceder a uma subscrição do SaaS para uma oferta que foi publicada com um ID de aplicação AD AZure diferente daquele usado para criar o token de autorização.

Este erro é frequentemente um sintoma de não realizar corretamente o [registo SaaS.](pc-saas-registration.md)

Código: 404 Não encontrado. A assinatura SaaS está num estado *não subscrito.*

Código: 500 Erro interno do servidor.  Re-tentar a chamada da API.  Se o erro persistir, contacte o [suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-list-of-all-subscriptions"></a>Obtenha a lista de todas as subscrições

Esta API recupera uma lista de todas as subscrições saaS compradas para todas as ofertas que a editora publica no mercado comercial.  As assinaturas SaaS em todos os status possíveis serão devolvidas. As subscrições saaS não subscritas também são devolvidas, porque esta informação não é eliminada do lado da Microsoft.

A API devolve resultados paginados de 100 por página.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Obter `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Use 2018-08-31.  |
| `continuationToken`  | Parâmetro opcional. Para recuperar a primeira página dos resultados, deixe-a vazia.  Utilize o valor devolvido no `@nextLink` parâmetro para recuperar a página seguinte. |

*Pedido de cabeçalhos:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Um valor de corda único para acompanhar o pedido do cliente, de preferência um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| `x-ms-correlationid` |  Um valor de corda único para operar no cliente.  Este parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| `authorization`      |  Um símbolo de acesso único que identifica a editora que está a fazer esta chamada da API.  O formato é `"Bearer <access_token>"` quando o valor simbólico é recuperado pela editora, como explicado na Get a [token com base na aplicação AD AZure](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Códigos de resposta:*

Código: 200 Devolve a lista de todas as subscrições existentes para todas as ofertas feitas por esta editora, com base na autorização da editora.

*Exemplo do corpo de resposta:*

```json
{
  "subscriptions": [
    {
      "id": "<guid>", // purchased SaaS subscription ID
      "name": "Contoso Cloud Solution", // SaaS subscription name
      "publisherId": "contoso", // publisher ID
      "offerId": "offer1", // purchased offer ID
      "planId": "silver", // purchased plan ID
      "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
      "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription was purchased.
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": { // email address, user ID and tenant ID that purchased the SaaS subscription. These could be different from beneficiary information for reseller (CSP) purchase
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": { // The period for which the subscription was purchased.
        "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
        "endDate": "2019-06-30", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
        "termUnit": "P1M" // where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values
      },
      "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
      "sessionMode": "None", // not relevant
      "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. (Optional field -– if not returned, the value is false.)
      "isTest": false, // not relevant
      "sandboxType": "None", // not relevant
      "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. Can be one of the following: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
    },
    // next SaaS subscription details, might be a different offer
    {
      "id": "<guid1>",
      "name": "Contoso Cloud Solution1",
      "publisherId": "contoso",
      "offerId": "offer2",
      "planId": "gold",
      "quantity": "",
      "beneficiary": {
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": {
        "emailId": "purchase@csp.com ",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": {
        "startDate": "2019-05-31",
        "endDate": "2020-04-30",
        "termUnit": "P1Y"
      },
      "allowedCustomerOperations": ["Read"],
      "sessionMode": "None",
      "isFreeTrial": false,
      "isTest": false,
      "sandboxType": "None",
      "saasSubscriptionStatus": "Suspended"
    }
  ],
  "@nextLink": "https:// https://marketplaceapi.microsoft.com/api/saas/subscriptions/?continuationToken=%5b%7b%22token%22%3a%22%2bRID%3a%7eYeUDAIahsn22AAAAAAAAAA%3d%3d%23RT%3a1%23TRC%3a2%23ISV%3a1%23FPC%3aAgEAAAAQALEAwP8zQP9%2fFwD%2b%2f2FC%2fwc%3d%22%2c%22range%22%3a%7b%22min%22%3a%22%22%2c%22max%22%3a%2205C1C9CD673398%22%7d%7d%5d&api-version=2018-08-31" // url that contains continuation token to retrieve next page of the SaaS subscriptions list, if empty or absent, this is the last page. ISV can use this url as is to retrieve the next page or extract the value of continuation token from this url.
}
```

Se não forem encontradas assinaturas SaaS compradas para este editor, o corpo de resposta vazio é devolvido.

Código: 403 Proibido. O sinal de autorização não está disponível, inválido ou caducado.

Este erro é frequentemente um sintoma de não realizar corretamente o [registo SaaS.](pc-saas-registration.md) 

Código: 500 Erro interno do servidor. Re-tentar a chamada da API.  Se o erro persistir, contacte o [suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-subscription"></a>Obter subscrição

Esta API recupera uma subscrição SaaS comprada especificada para uma oferta SaaS que a editora publica no mercado comercial. Utilize esta chamada para obter todas as informações disponíveis para uma subscrição específica do SaaS pelo seu ID em vez de ligar para a API que é usada para obter uma lista de todas as subscrições.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Obter `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   Use 2018-08-31. |
| `subscriptionId`     |  O identificador único da assinatura SaaS comprada.  Este ID é obtido após a resolução do token de autorização de mercado comercial utilizando a API Resolve. |

*Pedido de cabeçalhos:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  Um valor de corda único para acompanhar o pedido do cliente, de preferência um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  `x-ms-correlationid` |  Um valor de corda único para operar no cliente.  Este parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  `authorization`     | Um símbolo de acesso único que identifica a editora que está a fazer esta chamada da API. O formato é `"Bearer <access_token>"` quando o valor simbólico é recuperado pela editora, como explicado na Get a [token com base na aplicação AD AZure](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Códigos de resposta:*

Código: 200 Devolução de detalhes para uma subscrição SaaS com base no `subscriptionId` fornecido.

*Exemplo do corpo de resposta:*

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "name": "Contoso Cloud Solution", // SaaS subscription name
  "publisherId": "contoso", // publisher ID
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased plan ID
  "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
  "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription is purchased.
    "emailId": "test@contoso.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "purchaser": { // email address ,user ID and tenant ID that purchased the SaaS subscription.  These could be different from beneficiary information for reseller (CSP) scenario
    "emailId": "test@test.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary.  For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
  "sessionMode": "None", // not relevant
  "isFreeTrial": false, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. Optional field – if not returned the value is false.
  "isTest": false, // not relevant
  "sandboxType": "None", // not relevant
  "saasSubscriptionStatus": " Subscribed ", // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
  "term": { // the period for which the subscription was purchased
    "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
    "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
    "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
  }
}
```

Código: 403 Proibido. O sinal de autorização é inválido, caducado ou não foi fornecido. O pedido está a tentar aceder a uma subscrição do SaaS para uma oferta que é publicada com um ID de aplicação AD AD diferente daquele usado para criar o token de autorização.

Este erro é frequentemente um sintoma de não realizar corretamente o [registo SaaS.](pc-saas-registration.md) 

Código: 404 Não encontrado.  A subscrição do SaaS com o especificado `subscriptionId` não pode ser encontrada.

Código: 500 Erro interno do servidor.  Re-tentar a chamada da API.  Se o erro persistir, contacte o [suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="list-available-plans"></a>Listar planos disponíveis

Esta API recupera todos os planos para uma oferta SaaS identificada por `subscriptionId` uma compra específica desta oferta.  Utilize esta chamada para obter uma lista de todos os planos privados e públicos que o beneficiário de uma subscrição saaS pode atualizar para a subscrição.  Os planos devolvidos estarão disponíveis na mesma geografia que o plano já adquirido.

Esta chamada devolve uma lista de planos disponíveis para esse cliente, além do já adquirido.  A lista pode ser apresentada a um utilizador final no site da editora.  Um utilizador final pode alterar o plano de subscrição para qualquer um dos planos da lista devolvida.  Mudar o plano para um que não está na lista vai falhar.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Obter `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
|  `subscriptionId`    |  O identificador único da assinatura SaaS comprada.  Este ID é obtido após a resolução do token de autorização de mercado comercial utilizando a API Resolve. |

*Pedido de cabeçalhos:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  Um valor de corda único para acompanhar o pedido do cliente, de preferência um GUID.  Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  `x-ms-correlationid`  |  Um valor de corda único para operar no cliente.  Este parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  `authorization`     |  Um símbolo de acesso único que identifica a editora que está a fazer esta chamada da API.  O formato é `"Bearer <access_token>"` quando o valor simbólico é recuperado pela editora, como explicado na Get a [token com base na aplicação AD AZure](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Códigos de resposta:*

Código: 200 Devolve uma lista de todos os planos disponíveis para uma subscrição saaS existente, incluindo a já adquirida.

Exemplo do corpo de resposta:

```json
{
  "plans": [
    {
      "planId": "Platinum001",
      "displayName": "Private platinum plan for Contoso", // display name of the plan as it appears in the marketplace
      "isPrivate": true //true or false
    },
    {
      "planId": "gold",
      "displayName": "Gold plan for Contoso",
      "isPrivate": false //true or false
    }
  ]
}
```

Se `subscriptionId` não for encontrado, o corpo de resposta vazio é devolvido.

Código: 403 Proibido. O sinal de autorização é inválido, caducado ou não foi fornecido.  O pedido pode estar a tentar aceder a uma subscrição do SaaS para uma oferta que é publicada com um ID de aplicação AD AD diferente daquele usado para criar o token de autorização.

Este erro é frequentemente um sintoma de não realizar corretamente o [registo SaaS.](pc-saas-registration.md) 

Código: 500 Erro interno do servidor.  Re-tentar a chamada da API.  Se o erro persistir, contacte o [suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="change-the-plan-on-the-subscription"></a>Alterar o plano da subscrição

Utilize esta API para atualizar o plano existente adquirido para uma subscrição do SaaS a um novo plano (público ou privado).  A editora deve chamar a esta API quando um plano é alterado do lado da editora para uma subscrição SaaS comprada no mercado comercial.

Esta API só pode ser chamada para subscrições *Ativas.*  Qualquer plano pode ser alterado para qualquer outro plano existente (público ou privado), mas não para si mesmo.  Para planos privados, o inquilino do cliente deve ser definido como parte do público do plano no Partner Center.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
| `subscriptionId`     | O identificador único da assinatura SaaS comprada.  Este ID é obtido após a resolução do token de autorização de mercado comercial utilizando a API Resolve. |

*Pedido de cabeçalhos:*
 
|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Um valor de corda único para acompanhar o pedido do cliente, de preferência um GUID. Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  `x-ms-correlationid`  | Um valor de corda único para operar no cliente.  Este parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  `authorization`     |  Um símbolo de acesso único que identifica a editora que está a fazer esta chamada da API.  O formato é `"Bearer <access_token>"` quando o valor simbólico é recuperado pela editora, como explicado na Get a [token com base na aplicação AD AZure](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Solicitar exemplo de carga útil:*

```json
{
  "planId": "gold" // the ID of the new plan to be purchased
}
```

*Códigos de resposta:*

Código: 202 O pedido de alteração do plano foi aceite e tratado de forma assíncronal.  Espera-se que o parceiro registe a **URL de Localização Operação** para determinar o sucesso ou falha do pedido de alteração do plano.  As sondagens devem ser feitas a cada vários segundos até que o estado final de *Failed*, *Succeed*, ou *Conflict* seja recebido para a operação.  O estado de funcionamento final deve ser devolvido rapidamente, mas pode levar alguns minutos em alguns casos.

O parceiro também receberá a notificação do webhook quando a ação estiver pronta para ser concluída com sucesso no lado do mercado comercial.  Só então o editor deverá fazer a mudança de plano do lado da editora.

*Cabeçalhos de resposta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  URL para obter o estado da operação.  Por exemplo, `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Código: 400 Mau pedido: falhas de validação.

* O novo plano não existe ou não está disponível para esta subscrição específica do SaaS.
* O novo plano é o mesmo que o plano atual.
* O estado de subscrição do SaaS não está *subscrito.*
* A operação de atualização de uma subscrição saaS não está incluída em `allowedCustomerOperations` .

Código: 403 Proibido. O sinal de autorização é inválido, expirado ou não foi fornecido.  O pedido está a tentar aceder a uma subscrição do SaaS para uma oferta que é publicada com um ID de aplicação AD AD diferente daquele usado para criar o token de autorização.

Este erro é frequentemente um sintoma de não realizar corretamente o [registo SaaS.](pc-saas-registration.md)

Código: 404 Não encontrado.  A assinatura SaaS `subscriptionId` com não é encontrada.

Código: 500 Erro interno do servidor.  Re-tentar a chamada da API.  Se o erro persistir, contacte o [suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

>[!NOTE]
>Ou o plano ou a quantidade de lugares podem ser alterados de uma só vez, não ambos.

>[!Note]
>Esta API só pode ser chamada depois de obter aprovação explícita para a mudança do utilizador final.

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>Alterar a quantidade de lugares na subscrição do SaaS

Utilize esta API para atualizar (aumentar ou diminuir) a quantidade de lugares comprados para uma subscrição saaS.  O editor deve chamar a esta API quando o número de lugares é alterado do lado da editora para uma subscrição SaaS criada no mercado comercial.

A quantidade de lugares não pode ser superior à quantidade permitida no plano atual.  Neste caso, o editor deve alterar o plano antes de alterar a quantidade de assentos.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
|  `subscriptionId`     | Um identificador único da assinatura SaaS comprada.  Este ID é obtido após a resolução do token de autorização de mercado comercial utilizando a API Resolve.  |

*Pedido de cabeçalhos:*
 
|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Um valor de corda único para acompanhar o pedido do cliente, de preferência um GUID.  Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  `x-ms-correlationid`  | Um valor de corda único para operar no cliente.  Este parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  `authorization`     | Um símbolo de acesso único que identifica a editora que está a fazer esta chamada da API.  O formato é `"Bearer <access_token>"` quando o valor simbólico é recuperado pela editora, como explicado na Get a [token com base na aplicação AD AZure](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Solicitar exemplo de carga útil:*

```json
{
  "quantity": 5 // the new amount of seats to be purchased
}
```

*Códigos de resposta:*

Código: 202 O pedido de alteração da quantidade foi aceite e tratado de forma assíncronea. Espera-se que o parceiro registe o **URL de localização operação** para determinar o sucesso ou falha do pedido de alteração da quantidade.  As sondagens devem ser feitas a cada vários segundos até que o estado final de *Failed*, *Succeed*, ou *Conflict* seja recebido para a operação.  O estado de funcionamento final deve ser devolvido rapidamente, mas pode demorar vários minutos em alguns casos.

O parceiro também receberá a notificação do webhook quando a ação estiver pronta para ser concluída com sucesso no lado do mercado comercial.  Só então o editor deve alterar a quantidade no lado da editora.

*Cabeçalhos de resposta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Ligue-se a um recurso para obter o estado da operação.  Por exemplo, `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`.  |

Código: 400 Mau pedido: falhas de validação.

* A nova quantidade é maior ou inferior ao limite do plano atual.
* Falta a nova quantidade.
* A nova quantidade é a mesma que a atual quantidade.
* O estado de Subscrição saaS não é subscrito.
* A operação de atualização de uma subscrição SaaS não está incluída em `allowedCustomerOperations` .

Código: 403 Proibido.  O sinal de autorização é inválido, caducado ou não foi fornecido.  O pedido está a tentar aceder a uma subscrição que não pertence à editora atual.

Este erro é frequentemente um sintoma de não realizar corretamente o [registo SaaS.](pc-saas-registration.md) 

Código: 404 Não encontrado.  A assinatura SaaS `subscriptionId` com não é encontrada.

Código: 500 Erro interno do servidor.  Re-tentar a chamada da API.  Se o erro persistir, contacte o [suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

>[!Note]
>Só um plano ou quantidade pode ser alterado ao mesmo tempo, não ambos.

>[!Note]
>Esta API só pode ser chamada depois de obter a aprovação explícita do utilizador final para a alteração.

#### <a name="cancel-a-subscription"></a>Cancelar uma subscrição

Utilize esta API para cancelar a subscrição de SaaS especificada.  A editora não tem de usar esta API e recomendamos que os clientes sejam direcionados para o mercado comercial para cancelar subscrições saaS.

Se a editora decidir implementar o cancelamento de uma subscrição SaaS comprada no mercado comercial do lado da editora, deve chamar-lhe API.  Após a conclusão desta chamada, o estado da subscrição passará a ser *não subscrito* no lado da Microsoft.

O cliente não será cobrado se uma subscrição for cancelada nos seguintes períodos de graça:

* 24 horas para uma subscrição mensal após a ativação.
* Catorze dias para uma subscrição anual após a ativação.

O cliente será faturado se uma subscrição for cancelada após os períodos de graça anteriores.  O cliente perderá o acesso à subscrição saaS do lado da Microsoft imediatamente após o cancelamento. 

##### <a name="delete-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Excluir `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
|  `subscriptionId`     | O identificador único da assinatura SaaS comprada.  Este ID é obtido após a resolução do token de autorização de mercado comercial utilizando a API Resolve.  |

*Pedido de cabeçalhos:*
 
|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Um valor de corda único para acompanhar o pedido do cliente, de preferência um GUID.  Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  `x-ms-correlationid`  | Um valor de corda único para operar no cliente.  Este parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  `authorization`     |  Um token de acesso único que identifica a editora que faz esta chamada da API.  O formato é `"Bearer <access_token>"` quando o valor simbólico é recuperado pela editora, como explicado na Get a [token com base na aplicação AD AZure](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Códigos de resposta:*

Código: 202 O pedido de anulação foi aceite e tratado de forma assíncronea.  Espera-se que o parceiro registe a **URL de Operação-Localização** para determinar o sucesso ou falha deste pedido.  As sondagens devem ser feitas a cada vários segundos até que o estado final de *Failed*, *Succeed*, ou *Conflict* seja recebido para a operação.  O estado de funcionamento final deve ser devolvido rapidamente, mas pode demorar vários minutos em alguns casos.

O parceiro também receberá a notificação do webhook quando a ação for concluída com sucesso no lado do mercado comercial.  Só então o editor cancelará a subscrição do lado da editora.

*Cabeçalhos de resposta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Ligue-se a um recurso para obter o estado da operação.  Por exemplo, `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Código: 400 Mau pedido.  A exclusão não está na `allowedCustomerOperations` lista para esta subscrição do SaaS.

Código: 403 Proibido.  O token de autorização é inválido, caducado ou não está disponível. O pedido está a tentar aceder a uma subscrição do SaaS para uma oferta que é publicada com um ID de aplicação AD AD diferente daquele usado para criar o token de autorização.

Este erro é frequentemente um sintoma de não realizar corretamente o [registo SaaS.](pc-saas-registration.md)

Código: 404 Não encontrado.  A assinatura SaaS `subscriptionId` com não é encontrada.

Código: 500 Erro interno do servidor. Re-tentar a chamada da API.  Se o erro persistir, contacte o [suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

### <a name="operations-apis"></a>APIs de Operações

#### <a name="list-outstanding-operations"></a>Lista de operações pendentes 

Obtenha a lista das operações pendentes para a subscrição especificada do SaaS.  O editor deve reconhecer as operações devolvidas ligando para a [Operação Patch API](#update-the-status-of-an-operation).

Atualmente apenas **as operações de Reintegração** são devolvidas como resposta para esta chamada da API.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Obter `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  Use 2018-08-31.         |
|    `subscriptionId` | O identificador único da assinatura SaaS comprada.  Este ID é obtido após a resolução do token de autorização de mercado comercial utilizando a API Resolve.  |

*Pedido de cabeçalhos:*
 
|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  Um valor de corda único para acompanhar o pedido do cliente, de preferência um GUID.  Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  `x-ms-correlationid` |  Um valor de corda único para operar no cliente.  Este parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  `authorization`     |  O formato é `"Bearer <access_token>"` quando o valor simbólico é recuperado pela editora, como explicado na Get a [token com base na aplicação AD AZure](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Códigos de resposta:*

Código: 200 Devoluções pendentes de reposição da subscrição saas especificada.

*Exemplo de carga útil de resposta:*

```json
{
  "operations": [
    {
      "id": "<guid>", //Operation ID, should be provided in the operations patch API call
      "activityId": "<guid>", //not relevant
      "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
      "offerId": "offer1", // purchased offer ID
      "publisherId": "contoso",
      "planId": "silver", // purchased plan ID
      "quantity": "20", // purchased amount of seats, will be empty is not relevant
      "action": "Reinstate",
      "timeStamp": "2018-12-01T00:00:00", // UTC
      "status": "InProgress" // the only status that can be returned in this case
    }
  ]
}
```

Devoluções vazias se não houver operações de reintegração pendentes.

Código: 400 Mau pedido: falhas de validação.

Código: 403 Proibido. O sinal de autorização é inválido, caducado ou não foi fornecido.  O pedido está a tentar aceder a uma subscrição do SaaS para uma oferta que é publicada com um ID de aplicação AD AD diferente daquele usado para criar o token de autorização.

Este erro é frequentemente um sintoma de não realizar corretamente o [registo SaaS.](pc-saas-registration.md) 

Código: 404 Não encontrado.  A assinatura SaaS `subscriptionId` com não é encontrada.

Código: 500 Erro interno do servidor. Re-tentar a chamada da API.  Se o erro persistir, contacte o [suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-operation-status"></a>Obter estado de operação

Esta API permite ao editor acompanhar o estado da operação assincênta especificada:  **Não subscrever,** **Alterar Plano** ou **AlterarQuantidade**.

A `operationId` chamada para esta API pode ser recuperada a partir do valor devolvido pela **Operação-Localização,** a chamada de API de Operações pendentes ou o valor do `<id>` parâmetro recebido numa chamada webhook.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Obter `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
|  `subscriptionId`    |  O identificador único da assinatura SaaS comprada.  Este ID é obtido após a resolução do token de autorização de mercado comercial utilizando a API Resolve. |
|  `operationId`       |  O identificador único da operação que está a ser recuperada. |

*Pedido de cabeçalhos:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  Um valor de corda único para acompanhar o pedido do cliente, de preferência um GUID.  Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  `x-ms-correlationid` |  Um valor de corda único para operar no cliente.  Este parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  `authorization`     |  Um token de acesso único que identifica a editora que faz esta chamada da API.  O formato é `"Bearer <access_token>"` quando o valor simbólico é recuperado pela editora, como explicado na Get a [token com base na aplicação AD AZure](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Códigos de resposta:*

Código: 200 Obtém detalhes para a operação SaaS especificada. 

*Exemplo de carga útil de resposta:*

```json
Response body:
{
  "id  ": "<guid>", //Operation ID, should be provided in the patch operation API call
  "activityId": "<guid>", //not relevant
  "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
  "offerId": "offer1", // purchased offer ID
  "publisherId": "contoso",
  "planId": "silver", // purchased plan ID
  "quantity": "20", // purchased amount of seats
  "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
  "timeStamp": "2018-12-01T00:00:00", // UTC
  "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeeded, Conflict (new quantity / plan is the same as existing)
  "errorStatusCode": "",
  "errorMessage": ""
}
```

Código: 403 Proibido. O sinal de autorização é inválido, caducado ou não foi fornecido.  O pedido está a tentar aceder a uma subscrição do SaaS para uma oferta que é publicada com um ID de aplicação AD AD diferente daquele usado para criar o token de autorização.

Este erro é frequentemente um sintoma de não realizar corretamente o [registo SaaS.](pc-saas-registration.md) 

Código: 404 Não encontrado.  

* A assinatura `subscriptionId` com não é encontrada.
* A operação com `operationId` não foi encontrada.

Código: 500 Erro interno do servidor.  Re-tentar a chamada da API.  Se o erro persistir, contacte o [suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="update-the-status-of-an-operation"></a>Atualizar o estado de uma operação

Utilize esta API para atualizar o estado de uma operação pendente para indicar o sucesso ou falha da operação no lado da editora.

A `operationId` chamada para esta API pode ser recuperada a partir do valor devolvido pela **Operação-Localização,** a chamada de API de Operações pendentes ou o valor do `<id>` parâmetro recebido numa chamada webhook.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  Use 2018-08-31.  |
|   `subscriptionId`   |  O identificador único da assinatura SaaS comprada.  Este ID é obtido após a resolução do token de autorização de mercado comercial utilizando a API Resolve.  |
|   `operationId`      |  O identificador único da operação que está a ser concluída. |

*Pedido de cabeçalhos:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  Um valor de corda único para acompanhar o pedido do cliente, de preferência um GUID.  Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|   `x-ms-correlationid` |  Um valor de corda único para operar no cliente.  Este parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se este valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  `authorization`     |  Um símbolo de acesso único que identifica a editora que está a fazer esta chamada da API.  O formato é `"Bearer <access_token>"` quando o valor simbólico é recuperado pela editora, como explicado na Get a [token com base na aplicação AD AZure](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Solicitar exemplo de carga útil:*

```json
{
  "status": "Success" // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*Códigos de resposta:*

Código: 200 Uma chamada para informar da conclusão de uma operação do lado do parceiro.  Por exemplo, esta resposta pode sinalizar a conclusão da mudança de lugares ou planos do lado da editora.

Código: 403
- É proibido.  O sinal de autorização não está disponível, é inválido ou caducado. O pedido pode estar a tentar aceder a uma subscrição que não pertença à editora atual.
- É proibido.  O sinal de autorização é inválido, caducado ou não foi fornecido.  O pedido está a tentar aceder a uma subscrição do SaaS para uma oferta que é publicada com um ID de aplicação AD AD diferente daquele usado para criar o token de autorização.

Este erro é frequentemente um sintoma de não realizar corretamente o [registo SaaS.](pc-saas-registration.md)

Código: 404 Não encontrado.

* A assinatura `subscriptionId` com não é encontrada.
* A operação com `operationId` não foi encontrada.

Código: 409 Conflito.  Por exemplo, uma nova atualização já está cumprida.

Código: 500 Erro interno do servidor.  Re-tentar a chamada da API.  Se o erro persistir, contacte o [suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implementação de um webhook no serviço SaaS

Ao criar uma oferta de SaaS transacionável no Partner Center, o parceiro fornece o URL **De Ligação Webhook** para ser usado como ponto final HTTP.  Este webhook é chamado pela Microsoft utilizando a chamada POST HTTP para notificar o lado da editora dos seguintes eventos que ocorrem no lado da Microsoft:

* Quando a subscrição do SaaS estiver em estado *de subscrição:*
    * Plano de Alteração 
    * AlterarQuantidade
    * Suspender
    * Anular a subscrição
* Quando a subscrição do SaaS estiver em estado *suspenso:*
    * Restabelecer
    * Anular a subscrição

O editor deve implementar um webhook no serviço SaaS para manter o estado de subscrição saaS consistente com o lado da Microsoft.  O serviço SaaS é obrigado a ligar para a API da Operação Get para validar e autorizar os dados de chamada e carga útil do webhook antes de tomar medidas com base na notificação do webhook.  A editora deverá devolver HTTP 200 à Microsoft assim que a chamada do webhook for processada.  Este valor reconhece que a chamada webhook foi recebida com sucesso pela editora.

>[!Note]
>O serviço de URL webhook deve estar a funcionar 24x7 e pronto para receber sempre novas chamadas da Microsoft.  A Microsoft tem uma política de retenção para a chamada webhook (500 retries em 8 horas), mas se a editora não aceitar a chamada e devolver uma resposta, a operação que o webhook notifica acabará por falhar no lado da Microsoft.

*Exemplos de carga útil webhook:*

```json
// end user changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": "<guid>", // this is the operation ID to call with get operation API
  "activityId": "<guid>", // do not use
  "subscriptionId": "guid", // The GUID identifier for the SaaS resource which status changes
  "publisherId": "contoso", // A unique string identifier for each publisher
  "offerId": "offer1", // A unique string identifier for each offer
  "planId": "silver", // the most up-to-date plan ID
  "quantity": " 25", // the most up-to-date number of seats, can be empty if not relevant
  "timeStamp": "2019-04-15T20:17:31.7350641Z", // UTC time when the webhook was called
  "action": "ChangeQuantity", // the operation the webhook notifies about
  "status": "Success" // Can be either InProgress or Success
}
```

```json
// end user's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
  "publisherId": "contoso",
  "offerId": "offer2 ",
  "planId": "gold",
  "quantity": " 20",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Reinstate",
  "status": "In Progress"
}
```

## <a name="development-and-testing"></a>Desenvolvimento e teste

Para iniciar o processo de desenvolvimento, recomendamos a criação de respostas falsas da API do lado da editora.  Estas respostas podem basear-se em respostas de amostra fornecidas neste artigo.

Quando a editora estiver pronta para o fim dos testes:

* Publique uma oferta SaaS a um público de pré-visualização limitado e mantenha-a em fase de pré-visualização.
* Dedi o preço do plano para 0, para evitar desencadear despesas reais de faturação durante os testes.  Outra opção é definir um preço não-zero e cancelar todas as compras de teste dentro de 24 horas.
* Certifique-se de que todos os fluxos são invocados de ponta a ponta, para simular um cenário real do cliente.
* Se o parceiro quiser testar o fluxo de compra e faturação total, faça-o com uma oferta que tenha um preço superior a $0.  A compra será faturada e uma fatura será gerada.

Um fluxo de compra pode ser desencadeado a partir do portal Azure ou dos sites Microsoft AppSource, dependendo do local onde a oferta está a ser publicada.

*O plano de alteração,* *a quantidade de alteração* e as ações de não subscrição são *testadas* do lado da editora.  Do lado da Microsoft, a *subscrição não-subscrição* pode ser desencadeada tanto a partir do portal Azure como do Admin Center (o portal onde as compras do Microsoft AppSource são geridas).  *A alteração da quantidade e do plano* só podem ser acionadas a partir do Centro de Administração.

## <a name="get-support"></a>Obter suporte

Consulte [o Suporte para o programa de marketplace comercial no Partner Center](../support.md) para opções de suporte de editores.

## <a name="next-steps"></a>Passos seguintes

Consulte o [serviço de medição de mercado comercial APIs](marketplace-metering-service-apis.md) para mais opções para ofertas SaaS no mercado comercial.

Reveja e utilize o [SaaS SDK](https://github.com/Azure/Microsoft-commercial-marketplace-transactable-SaaS-offer-SDK) construído em cima das APIs descritas neste documento.
