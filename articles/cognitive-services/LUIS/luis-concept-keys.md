---
title: Criação e chaves de tempo de execução-LUIS
titleSuffix: Azure Cognitive Services
description: O LUIS usa duas chaves, a chave de criação para criar seu modelo e a chave de tempo de execução para consultar o ponto de extremidade de previsão com o usuário declarações.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 973a8dd56437506d907159f212164ff147ba975c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487500"
---
# <a name="authoring-and-runtime-keys"></a>Chaves de criação e de runtime

O Reconhecimento vocal (LUIS) tem dois conjuntos de API e serviços: 

* Criação (anteriormente conhecida como _programática_)
* Tempo de execução de previsão

Há vários tipos de chave, dependendo de qual serviço você deseja trabalhar e como você deseja trabalhar com ele.

## <a name="non-azure-resources-for-luis"></a>Recursos não Azure para LUIS

### <a name="starter-key"></a>Chave de início

Quando você começa a usar o LUIS pela primeira vez, uma **chave de início** é criada para você. Esse recurso fornece:

* solicitações gratuitas de serviço de criação por meio do portal LUIS ou de APIs (incluindo SDKs)
* solicitações gratuitas de ponto de extremidade de previsão 1.000 por mês por meio de um navegador, API ou SDKs

## <a name="azure-resources-for-luis"></a>Recursos do Azure para LUIS

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

O LUIS permite três tipos de recursos do Azure: 
 
|Chave|Objetivo|`kind` de serviço cognitiva|`type` de serviço cognitiva|
|--|--|--|--|
|[Chave de criação](#programmatic-key)|Acesse e gerencie dados de aplicativos com criação, treinamento, publicação e teste. Crie uma chave de criação do LUIS se você pretende criar aplicativos LUIS programaticamente.<br><br>A finalidade da chave de `LUIS.Authoring` é permitir que você:<br>* gerenciar programaticamente aplicativos e modelos Reconhecimento vocal, incluindo treinamento e publicação<br> * controle permissões para o recurso de criação atribuindo pessoas à [função colaborador](#contributions-from-other-authors).|`LUIS.Authoring`|`Cognitive Services`|
|[Chave de previsão](#prediction-endpoint-runtime-key)| Solicitações de ponto de extremidade de previsão de consulta. Crie uma chave de previsão LUIS antes que seu aplicativo cliente solicite previsões além das 1.000 solicitações fornecidas pelo recurso de início. |`LUIS`|`Cognitive Services`|
|[Chave de recurso de vários serviços cognitivas do serviço](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Solicitações de ponto de extremidade de previsão de consulta compartilhadas com LUIS e outros serviços cognitivas com suporte.|`CognitiveServices`|`Cognitive Services`|

Quando o processo de criação de recursos for concluído, [atribua a chave](luis-how-to-azure-subscription.md) ao aplicativo no portal do Luis.

É importante criar aplicativos LUIS em [regiões](luis-reference-regions.md#publishing-regions) em que você deseja publicar e consultar.

> [!CAUTION]
> Para sua conveniência, muitos dos exemplos usam a [chave inicial](#starter-key) porque ele fornece algumas chamadas de ponto de extremidade de previsão gratuitas em sua [cota](luis-boundaries.md#key-limits).  


### <a name="query-prediction-resources"></a>Recursos de previsão de consulta

* A chave de tempo de execução pode ser usada para todos os seus aplicativos LUIS ou para aplicativos LUIS específicos. 
* Não use a chave de tempo de execução para criar aplicativos LUIS. 

O ponto de extremidade do LUIS Runtime aceita dois estilos de consulta, ambos usam a chave de tempo de execução de ponto de extremidade de previsão, mas em locais diferentes.

O ponto de extremidade usado para acessar o tempo de execução usa um subdomínio que é exclusivo para a região do seu recurso, indicado com `{region}` na tabela a seguir. 

## <a name="assignment-of-the-key"></a>Atribuição da chave

Você pode [atribuir](luis-how-to-azure-subscription.md) a chave de tempo de execução no [portal do Luis](https://www.luis.ai) ou por meio das APIs correspondentes. 

## <a name="key-limits"></a>Limites de chave

Você pode criar até 10 chaves de criação por região por assinatura. 

Consulte [limites de chave](luis-boundaries.md#key-limits) e [regiões do Azure](luis-reference-regions.md). 

As regiões de publicação são diferentes das regiões de criação. Certifique-se de criar um aplicativo na região de criação correspondente à região de publicação que você deseja que seu aplicativo cliente esteja localizado.

## <a name="key-limit-errors"></a>Erros de limite de chave
Se você exceder sua cota de transações por segundo (TPS), receberá um erro HTTP 429. Se você exceder sua cota de transação por mês (TPS), receberá um erro HTTP 403. 

## <a name="contributions-from-other-authors"></a>Contribuições de outros autores

**Para [criar aplicativos migrados de recursos](luis-migration-authoring.md)** : os _colaboradores_ são gerenciados no portal do Azure para o recurso de criação, usando a página de **controle de acesso (iam)** . Saiba [como adicionar um usuário](luis-how-to-collaborate.md), usando o endereço de email da colaboração e a função _colaborador_ . 

**Para aplicativos que ainda não foram migrados**: todos os _colaboradores_ são gerenciados no portal do Luis a partir da página **Gerenciar > colaboradores** .

## <a name="move-transfer-or-change-ownership"></a>Mover, transferir ou alterar a propriedade

Um aplicativo é definido por seus recursos do Azure, que é determinado pela assinatura do proprietário. 

Você pode mover seu aplicativo LUIS. Use os seguintes recursos de documentação no portal do Azure ou CLI do Azure:

* [Mover o aplicativo entre os recursos de criação do LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Mover recurso para novo grupo de recursos ou assinatura](../../azure-resource-manager/resource-group-move-resources.md)
* [Mover recurso na mesma assinatura ou em assinaturas](../../azure-resource-manager/move-limitations/app-service-move-limitations.md)

Para transferir a [Propriedade](../../billing/billing-subscription-transfer.md) da sua assinatura: 

**Para usuários que migraram aplicativos [migrados de recursos de criação](luis-migration-authoring.md)** : como o proprietário do recurso, você pode adicionar um `contributor`.

**Para usuários que ainda não migraram**: exporte seu aplicativo como um arquivo JSON. Outro usuário do LUIS pode importar o aplicativo, tornando-se o proprietário do aplicativo. O novo aplicativo terá uma ID de aplicativo diferente.  

## <a name="access-for-private-and-public-apps"></a>Acesso para aplicativos públicos e privados

Para um aplicativo **privado** , o acesso ao tempo de execução está disponível para proprietários e colaboradores. Para um aplicativo **público** , o acesso ao tempo de execução está disponível para todos que têm seu próprio [serviço cognitiva](../cognitive-services-apis-create-account.md) do Azure ou recurso de tempo de execução [Luis](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) e tem a ID do aplicativo público. 

Atualmente, não há um catálogo de aplicativos públicos.

### <a name="authoring-access"></a>Acesso de criação
O acesso ao aplicativo no portal do [Luis](luis-reference-regions.md#luis-website) ou nas [APIs de criação](https://go.microsoft.com/fwlink/?linkid=2092087) é controlado pelo recurso de criação do Azure. 

O proprietário e todos os colaboradores têm acesso para criar o aplicativo. 

|O acesso de criação inclui|Notas|
|--|--|
|Adicionar ou remover chaves de ponto de extremidade||
|Exportando versão||
|Exportar logs de ponto de extremidade||
|Importando versão||
|Tornar o aplicativo público|Quando um aplicativo é público, qualquer pessoa com uma chave de criação ou de ponto de extremidade pode consultar o aplicativo.|
|Modificar modelo|
|Publicar|
|Examinar o declarações do ponto de extremidade para o [aprendizado ativo](luis-how-to-review-endpoint-utterances.md)|
|Preparar|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Acesso ao tempo de execução de ponto de extremidade de previsão

O acesso para consultar o ponto de extremidade de previsão é controlado por uma configuração na página de **informações do aplicativo** na seção **gerenciar** . 

|[Ponto de extremidade privado](#runtime-security-for-private-apps)|[Ponto de extremidade público](#runtime-security-for-public-apps)|
|:--|:--|
|Disponível para o proprietário e os colaboradores|Disponível para o proprietário, colaboradores e qualquer outra pessoa que conheça a ID do aplicativo|

Você pode controlar quem vê sua chave de tempo de execução LUIS chamando-a em um ambiente de servidor para servidor. Se você estiver usando o LUIS de um bot, a conexão entre o bot e o LUIS já será segura. Se você estiver chamando o ponto de extremidade LUIS diretamente, deverá criar uma API do lado do servidor (como uma [função](https://azure.microsoft.com/services/functions/)do Azure) com acesso controlado (como o [AAD](https://azure.microsoft.com/services/active-directory/)). Quando a API do lado do servidor for chamada e autenticada e a autorização for verificada, passe a chamada para LUIS. Embora essa estratégia não impeça ataques man-in-the-Middle, ela ofusca sua chave e a URL do ponto de extremidade dos usuários, permite que você acompanhe o acesso e permite que você adicione o log de resposta do ponto de extremidade (como [Application insights](https://azure.microsoft.com/services/application-insights/)).

#### <a name="runtime-security-for-private-apps"></a>Segurança de tempo de execução para aplicativos privados

O tempo de execução de um aplicativo privado só está disponível para o seguinte:

|Chave e usuário|Explicação|
|--|--|
|Chave de criação do proprietário| Até 1000 ocorrências de ponto de extremidade|
|Colaboradores/chaves de criação de colaborador| Até 1000 ocorrências de ponto de extremidade|
|Qualquer chave atribuída a LUIS por um autor ou colaboração/colaborador|Com base na camada de uso de chave|

#### <a name="runtime-security-for-public-apps"></a>Segurança de tempo de execução para aplicativos públicos

Depois que um aplicativo é configurado como público, _qualquer_ chave de criação de Luis válida ou de ponto de extremidade Luis pode consultar seu aplicativo, desde que a chave não tenha usado toda a cota de ponto de extremidade.

Um usuário que não seja um proprietário ou colaborador, só poderá acessar o tempo de execução de um aplicativo público se a ID do aplicativo for fornecida. O LUIS não tem um _mercado_ público ou outra maneira de Pesquisar um aplicativo público.  

Um aplicativo público é publicado em todas as regiões para que um usuário com uma chave de recurso LUIS baseada em região possa acessar o aplicativo em qualquer região associada à chave de recurso.

## <a name="transfer-of-ownership"></a>Transferência de propriedade

LUIS não tem o conceito de transferir a propriedade de um recurso. 

## <a name="securing-the-endpoint"></a>Protegendo o ponto de extremidade 

Você pode controlar quem pode ver sua chave de ponto de extremidade do LUIS de tempo de execução de previsão chamando-a em um ambiente de servidor para servidor. Se você estiver usando o LUIS de um bot, a conexão entre o bot e o LUIS já será segura. Se você estiver chamando o ponto de extremidade LUIS diretamente, deverá criar uma API do lado do servidor (como uma [função](https://azure.microsoft.com/services/functions/)do Azure) com acesso controlado (como o [AAD](https://azure.microsoft.com/services/active-directory/)). Quando a API do lado do servidor é chamada e a autenticação e a autorização são verificadas, passe a chamada para LUIS. Embora essa estratégia não impeça ataques man-in-the-Middle, ele ofusca seu ponto de extremidade de seus usuários, permite que você acompanhe o acesso e permite que você adicione o log de resposta do ponto de extremidade (como [Application insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="next-steps"></a>Passos seguintes

* Entenda os conceitos de [controle de versão](luis-concept-version.md) . 
* Saiba [como criar chaves](luis-how-to-azure-subscription.md).
