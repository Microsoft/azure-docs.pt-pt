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
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: 5a6c87da7ae62af54990e0a1a2c62065717a201a
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256963"
---
# <a name="authoring-and-runtime-keys"></a>Criação e chaves de tempo de execução


>[!NOTE]
>[Migre](luis-migration-authoring.md) todos os aplicativos, que não usam o recurso de criação do Azure, antes de continuar.

O LUIS usa dois tipos de recursos do Azure, cada tipo tem chaves: 
 
* [Criação](#programmatic-key) para criar tentativas, entidades e rotular declarações, treinar e publicar. Quando estiver pronto para publicar seu aplicativo LUIS, você precisará de uma [chave de ponto de extremidade de previsão para o tempo de execução](luis-how-to-azure-subscription.md) atribuído ao aplicativo.
* [Chave de ponto de extremidade de previsão para o tempo de execução](#prediction-endpoint-runtime-key). Aplicativos cliente, como um bot de chat, precisam acessar o ponto de extremidade de **previsão de consulta** do tempo de execução por meio dessa chave. 

|Chave|Objetivo|Serviço cognitiva`kind`|Serviço cognitiva`type`|
|--|--|--|--|
|[Chave de criação](#programmatic-key)|Criação, treinamento, publicação, teste.|`LUIS.Authoring`|`Cognitive Services`|
|[Chave de tempo de execução de ponto de extremidade de previsão](#prediction-endpoint-runtime-key)| Tempo de execução de ponto de extremidade de previsão de consulta com um usuário expressão para determinar tentativas e entidades.|`LUIS`|`Cognitive Services`|

O LUIS também fornece uma [chave inicial](luis-how-to-azure-subscription.md#starter-key) com uma cota de ponto de extremidade de previsão de transações de 1000 por mês. 

Embora você não precise criar as duas chaves ao mesmo tempo, é muito mais fácil se você fizer isso.

É importante criar aplicativos LUIS em [regiões](luis-reference-regions.md#publishing-regions) em que você deseja publicar e consultar.

<a name="programmatic-key" ></a>

## <a name="authoring-key"></a>Chave de criação

Uma chave de criação é criada automaticamente quando você cria uma conta do LUIS e é gratuita. Quando você começa com o LUIS, você tem uma chave de início em todos os seus aplicativos LUIS para cada [região](luis-reference-regions.md)de criação. A finalidade da chave de criação é fornecer autenticação para gerenciar seu aplicativo LUIS ou para testar consultas de ponto de extremidade de previsão. 

A criação de chaves de criação no portal do Azure permite que você controle permissões para o recurso de criação atribuindo pessoas à [função colaborador](#contributions-from-other-authors). Você precisa de permissão no nível de assinatura do Azure para adicionar colaboradores. 

Para localizar a chave de criação, entre em [Luis](luis-reference-regions.md#luis-website) e clique no nome da conta na barra de navegação superior direita para abrir **as configurações da conta**.

![Chave de criação](./media/luis-concept-keys/authoring-key.png)

Quando você quiser fazer **consultas de tempo de execução**, crie o [recurso Luis](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)do Azure. 

> [!CAUTION]
> Para sua conveniência, muitos dos exemplos usam a [chave inicial](#starter-prediction-endpoint-runtime-key) porque ele fornece algumas chamadas de ponto de extremidade de previsão gratuitas em sua [cota](luis-boundaries.md#key-limits).  

<a name="endpoint-key"></a>

## <a name="prediction-endpoint-runtime-key"></a>Chave de tempo de execução de ponto de extremidade de previsão 

Quando você precisar de **consultas de ponto de extremidade de tempo de execução**, crie um recurso de reconhecimento vocal (Luis) e, em seguida, atribua-o ao aplicativo Luis. 

[!INCLUDE [Azure runtime resource creation for Language Understanding and Cognitive Service resources](../../../includes/cognitive-services-luis-azure-resource-instructions.md)]

Quando o processo de criação de recursos for concluído, [atribua a chave](luis-how-to-azure-subscription.md) ao aplicativo. 

* A chave de tempo de execução (ponto de extremidade de previsão de consulta) permite uma cota de ocorrências de ponto de extremidade com base no plano de uso especificado ao criar a chave de tempo de execução. Ver [preços de serviços cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h) para informações sobre preços.

* A chave de tempo de execução pode ser usada para todos os seus aplicativos LUIS ou para aplicativos LUIS específicos. 
* Não use a chave de tempo de execução para criar aplicativos LUIS. 

### <a name="starter-prediction-endpoint-runtime-key"></a>Chave de tempo de execução de ponto de extremidade de previsão iniciada

A chave do ponto de extremidade de previsão **inicial** é fornecida gratuitamente e inclui consultas de ponto de extremidade de previsão 1000. Depois que essas consultas forem usadas, você deverá criar seu próprio recurso de ponto de extremidade de previsão para Reconhecimento vocal.  

Esse é um recurso especial criado para você. Ela não aparece na lista de recursos do Azure porque ela se destina como uma chave inicial temporária. 

<a name="use-endpoint-key-in-query"></a>

### <a name="use-runtime-key-in-query"></a>Usar chave de tempo de execução na consulta
O ponto de extremidade do LUIS Runtime aceita dois estilos de consulta, ambos usam a chave de tempo de execução de ponto de extremidade de previsão, mas em locais diferentes.

O ponto de extremidade usado para acessar o tempo de execução usa um subdomínio que é exclusivo para a região do seu recurso `{region}` , indicado com na tabela a seguir. 

|Verbo|Localização de url e a chave de exemplo|
|--|--|
|[GET](https://{region}.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?runtime-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn%20on%20the%20lights`<br><br>valor de cadeia de caracteres de consulta para `runtime-key`<br><br>Alterar o valor de consulta de ponto final para o `runtime-key` da chave de criação (iniciante), para a nova chave de ponto de extremidade para usar a taxa de quota de chave de ponto final do LUIS. Se criar a chave e atribuir a chave, mas não altere o valor de consulta de ponto final para `runtime-key`, sua quota de chave de ponto final não estiver a utilizar.|
|[POST](https://{region}.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| `https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2`<br><br> valor de cabeçalho para `Ocp-Apim-Subscription-Key`<br>Se você criar a chave de tempo de execução e atribuir a chave de tempo de execução, mas não alterar `Ocp-Apim-Subscription-Key`o valor de consulta de ponto de extremidade para, você não está usando sua chave de tempo de execução.|

O ID da aplicação utilizado nos URLs anteriores, `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, é a aplicação de IoT pública utilizada para o [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). 

## <a name="assignment-of-the-runtime-key"></a>Atribuição da chave de tempo de execução

Você pode [atribuir](luis-how-to-azure-subscription.md) a chave de tempo de execução no [portal do Luis](https://www.luis.ai) ou por meio das APIs correspondentes. 

## <a name="key-limits"></a>Limites de chaves

Você pode criar até 10 chaves de criação por região por assinatura. 

Consulte [limites de chave](luis-boundaries.md#key-limits) e [regiões do Azure](luis-reference-regions.md). 

Regiões de publicação são diferentes das regiões de criação. Certifique-se de criar um aplicativo na região de criação correspondente à região de publicação que você deseja que seu aplicativo cliente esteja localizado.

## <a name="key-limit-errors"></a>Erros de chave de limite
Se você exceder sua cota de transações por segundo (TPS), receberá um erro HTTP 429. Se você exceder sua cota de transação por mês (TPS), receberá um erro HTTP 403. 

## <a name="contributions-from-other-authors"></a>Contribuições de outros autores



O gerenciamento de contribuições de colaboradores depende do status atual do aplicativo.

**Para [criar](luis-migration-authoring.md) aplicativos migrados de recursos**: os _colaboradores_ são gerenciados no portal do Azure para o recurso de criação, usando a página de **controle de acesso (iam)** . Saiba [como adicionar um usuário](luis-how-to-collaborate.md), usando o endereço de email da colaboração e a função _colaborador_ . 

**Para aplicativos que ainda não foram migrados**: todos os _colaboradores_ são gerenciados no portal do Luis a partir da página **Gerenciar > colaboradores** .

### <a name="contributor-roles-vs-entity-roles"></a>Funções de colaborador vs. funções de entidade

As [funções de entidade](luis-concept-roles.md) se aplicam ao modelo de dados do aplicativo Luis. As funções colaborador/colaborador se aplicam a níveis de acesso de criação. 

## <a name="moving-or-changing-ownership"></a>Movendo ou alterando a propriedade

Um aplicativo é definido por seus recursos do Azure, que é determinado pela assinatura do proprietário. 

Você pode mover seu aplicativo LUIS. Use os seguintes recursos de documentação no portal do Azure ou CLI do Azure:

* [Mover o aplicativo entre os recursos de criação do LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Mover recurso para novo grupo de recursos ou assinatura](../../azure-resource-manager/resource-group-move-resources.md)
* [Mover recurso na mesma assinatura ou em assinaturas](../../azure-resource-manager/move-limitations/app-service-move-limitations.md)
* [Transferir a propriedade](../../billing/billing-subscription-transfer.md) de sua assinatura 

## <a name="access-for-private-and-public-apps"></a>Acesso para aplicativos públicos e privados

Para um aplicativo **privado** , o acesso ao tempo de execução está disponível para proprietários e colaboradores. Para um aplicativo **público** , o acesso ao tempo de execução está disponível para todos que têm seu próprio [serviço cognitiva](../cognitive-services-apis-create-account.md) do Azure ou recurso de tempo de execução [Luis](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) e tem a ID do aplicativo público. 

Atualmente, não há um catálogo de aplicativos públicos.

### <a name="authoring-access"></a>Acesso de criação
O acesso ao aplicativo no portal do [Luis](luis-reference-regions.md#luis-website) ou nas [APIs de criação](https://go.microsoft.com/fwlink/?linkid=2092087) é controlado pelo recurso de criação do Azure. 

O proprietário e todos os colaboradores têm acesso para criar o aplicativo. 

|Inclui acesso de criação|Notas|
|--|--|
|Adicionar ou remover chaves de ponto final||
|Exportação de versão||
|Exportar registos de ponto final||
|Importação de versão||
|Tornar a aplicação pública|Quando uma aplicação é pública, qualquer pessoa com uma chave de criação ou ponto de extremidade pode consultar a aplicação.|
|Modificar modelo|
|Publicar|
|Reveja as expressões de ponto final para [aprendizagem ativa](luis-how-to-review-endpoint-utterances.md)|
|Preparar|

### <a name="prediction-endpoint-runtime-access"></a>Acesso ao tempo de execução de ponto de extremidade de previsão

O acesso para consultar o ponto de extremidade de previsão é controlado por uma configuração na página de **informações do aplicativo** na seção **gerenciar** . 

![Aplicação de conjunto para o público](./media/luis-concept-security/set-application-as-public.png)

|[Ponto final privado](#runtime-security-for-private-apps)|[Ponto final público](#runtime-security-for-public-apps)|
|:--|:--|
|Disponível para o proprietário e os colaboradores|Disponível para o proprietário, colaboradores e qualquer outra pessoa que conheça a ID do aplicativo|

Você pode controlar quem vê sua chave de tempo de execução LUIS chamando-a em um ambiente de servidor para servidor. Se estiver a utilizar o LUIS de um bot, a ligação entre o bot e o LUIS já é segura. Se estiver a chamar diretamente o ponto de extremidade do LUIS, deve criar uma API do lado do servidor (como do Azure [função](https://azure.microsoft.com/services/functions/)) com o acesso controlado (tal como [AAD](https://azure.microsoft.com/services/active-directory/)). Quando a API do lado do servidor for chamada e autenticada e a autorização for verificada, passe a chamada para LUIS. Embora essa estratégia não impeça ataques man-in-the-Middle, ela ofusca sua chave e a URL do ponto de extremidade dos usuários, permite que você acompanhe o acesso e permite que você adicione o log de resposta do ponto de extremidade (como [Application insights](https://azure.microsoft.com/services/application-insights/)).

#### <a name="runtime-security-for-private-apps"></a>Segurança de tempo de execução para aplicativos privados

O tempo de execução de um aplicativo privado só está disponível para o seguinte:

|Chave e o utilizador|Explicação|
|--|--|
|Chave de criação do proprietário| Até o ponto final de 1000 ocorrências|
|Colaboradores/chaves de criação de colaborador| Até o ponto final de 1000 ocorrências|
|Qualquer chave atribuída a LUIS por um autor ou colaboração/colaborador|Com base na camada de utilização de chave|

#### <a name="runtime-security-for-public-apps"></a>Segurança de tempo de execução para aplicativos públicos

Depois de uma aplicação é configurada como pública, _qualquer_ LUIS válido, a criação de chave ou chave de ponto final do LUIS pode consultar a sua aplicação, desde que a chave não utilizado a quota de ponto final de todo.

Um usuário que não seja um proprietário ou colaborador, só poderá acessar o tempo de execução de um aplicativo público se a ID do aplicativo for fornecida. LUIS não tem um público _mercado_ ou outra forma para procurar uma aplicação pública.  

Uma aplicação pública está publicada em todas as regiões, para que um utilizador com uma chave de recurso com base na região do LUIS pode aceder à aplicação em qualquer região está associado com a chave de recurso.

## <a name="transfer-of-ownership"></a>Transferência de propriedade

**Para [criar](luis-migration-authoring.md) aplicativos migrados de recursos**: 

**Para aplicativos que ainda não foram migrados**: Exporte seu aplicativo como um arquivo JSON. Outro usuário do LUIS pode importar o aplicativo, tornando-se o proprietário do aplicativo. O novo aplicativo terá uma ID de aplicativo diferente.  

## <a name="securing-the-endpoint"></a>Proteger o ponto final 

Você pode controlar quem pode ver sua chave de ponto de extremidade do LUIS de tempo de execução de previsão chamando-a em um ambiente de servidor para servidor. Se estiver a utilizar o LUIS de um bot, a ligação entre o bot e o LUIS já é segura. Se estiver a chamar diretamente o ponto de extremidade do LUIS, deve criar uma API do lado do servidor (como do Azure [função](https://azure.microsoft.com/services/functions/)) com o acesso controlado (tal como [AAD](https://azure.microsoft.com/services/active-directory/)). Quando é chamado de API do lado do servidor e autenticação e autorização são verificadas, passe a chamada para o LUIS. Embora essa estratégia não impede ataques man-in-the-middle, obfuscates o ponto final dos seus utilizadores, permite-lhe controlar o acesso e permite-lhe adicionar o registo de resposta do ponto final (tal como [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="next-steps"></a>Passos Seguintes

* Compreender [controle de versão](luis-concept-version.md) conceitos. 
* Saiba [como criar chaves](luis-how-to-azure-subscription.md).
