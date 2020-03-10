---
title: Como usar as chaves de autoria e tempo de funcionamento com LUIS
titleSuffix: Azure Cognitive Services
description: O LUIS utiliza duas teclas, a chave de autor para criar o seu modelo e a chave de tempo de execução para consulta do ponto final de previsão com as declarações do utilizador.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9d213c8fa03ad2ca5e5fd7e620e52aa502749be2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390092"
---
# <a name="authoring-and-runtime-keys"></a>Chaves de criação e de runtime

O Entendimento linguístico (LUIS) tem dois serviços e conjuntos de API: 

* Autoria (anteriormente conhecida como _programática)_
* Tempo de execução da previsão

Existem vários tipos-chave, dependendo do serviço com que pretende trabalhar e como quer trabalhar com ele.

## <a name="non-azure-resources-for-luis"></a>Recursos não-Azure para LUIS

### <a name="starter-key"></a>Chave de arranque

Quando começa si pela primeira vez a usar O LUIS, é criada uma **chave de arranque** para si. Este recurso fornece:

* pedidos gratuitos de serviço de autoria através do portal LUIS ou APIs (incluindo SDKs)
* 1\.000 pedidos de ponto final de previsão gratuitos por mês através de um navegador, API ou SDKs

## <a name="azure-resources-for-luis"></a>Recursos azure para LUIS

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

O LUIS permite três tipos de recursos Azure: 
 
|Chave|Objetivo|Serviço cognitivo `kind`|Serviço cognitivo `type`|
|--|--|--|--|
|[Chave de autoria](#programmatic-key)|Acesso e gestão de dados de aplicação com autoria, formação, publicação e testes. Crie uma chave de autor DO LUIS se pretender autor programático de aplicações LUIS.<br><br>O objetivo da chave `LUIS.Authoring` é permitir-lhe:<br>* gerir programáticamente aplicações e modelos de compreensão de linguagem, incluindo formação e publicação<br> * controlar as permissões ao recurso de autoria atribuindo as pessoas [ao papel de contribuinte](#contributions-from-other-authors).|`LUIS.Authoring`|`Cognitive Services`|
|[Chave de previsão](#prediction-endpoint-runtime-key)| Pedidos de ponto final de previsão de consulta. Crie uma chave de previsão LUIS antes que a aplicação do seu cliente solicite previsões para além dos 1.000 pedidos fornecidos pelo recurso de arranque. |`LUIS`|`Cognitive Services`|
|[Chave de recursos multi-serviço do Serviço Cognitivo](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Pedidos de ponto final de previsão de consulta partilhados com LUIS e outros Serviços Cognitivos apoiados.|`CognitiveServices`|`Cognitive Services`|

Quando o processo de criação de recursos estiver concluído, [atribua a chave](luis-how-to-azure-subscription.md) à app no portal LUIS.

É importante para o autor de apps LUIS em [regiões](luis-reference-regions.md#publishing-regions) onde você quer publicar e consultar.

> [!CAUTION]
> Por conveniência, muitas das amostras utilizam a [tecla Starter](#starter-key) porque fornece algumas chamadas finais de previsão gratuitas na sua [quota](luis-boundaries.md#key-limits).  


### <a name="query-prediction-resources"></a>Recursos de previsão de consultas

* A chave de tempo de execução pode ser usada para todas as suas aplicações LUIS ou para aplicações ESPECÍFICAs do LUIS. 
* Não utilize a chave de tempo de execução para a autoria de aplicações LUIS. 

O ponto final do tempo de execução LUIS aceita dois estilos de consulta, ambos usam a chave de tempo de execução do ponto final de previsão, mas em diferentes lugares.

O ponto final usado para aceder ao tempo de execução utiliza um subdomínio único na região do seu recurso, denotado com `{region}` na tabela seguinte. 

## <a name="assignment-of-the-key"></a>Atribuição da chave

Pode [atribuir](luis-how-to-azure-subscription.md) a chave de tempo de execução no [portal LUIS](https://www.luis.ai) ou através das APIs correspondentes. 

## <a name="key-limits"></a>Limites de chaves

Pode criar até 10 chaves de autoria por região por subscrição. 

Consulte [os limites de chave](luis-boundaries.md#key-limits) e as [regiões de Azure.](luis-reference-regions.md) 

Regiões de publicação são diferentes das regiões de criação. Certifique-se de que cria uma aplicação na região de autor correspondente à região editorial que pretende localizar a sua aplicação de cliente.

## <a name="key-limit-errors"></a>Erros de chave de limite
Se exceder a sua quota de transações por segundo (TPS), receberá um erro HTTP 429. Se exceder a sua quota de transação por mês (TPS), receberá um erro HTTP 403. 

## <a name="contributions-from-other-authors"></a>Contribuições de outros autores

**Para a autoria de aplicações [migradas](luis-migration-authoring.md)** por recursos : _os contribuintes_ são geridos no portal Azure para o recurso de autoria, utilizando a página de Controlo de **Acesso (IAM).** Saiba [como adicionar um utilizador,](luis-how-to-collaborate.md)utilizando o endereço de e-mail do colaborador e a função de _contribuinte._ 

**Para aplicações que ainda não migraram**: todos os _colaboradores_ são geridos no portal LUIS a partir da página **Manage &> Colaboradores.**

## <a name="move-transfer-or-change-ownership"></a>Mover, transferir ou alterar a propriedade

Uma aplicação é definida pelos seus recursos Azure, que é determinada pela subscrição do proprietário. 

Pode mover a sua aplicação LUIS. Utilize os seguintes recursos de documentação no portal Azure ou no Azure CLI:

* [Mover app entre os recursos de autoria da LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Mover recursos para novo grupo de recursos ou subscrição](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Mover recursos dentro da mesma subscrição ou através de subscrições](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

Para transferir a [propriedade](../../cost-management-billing/manage/billing-subscription-transfer.md) da sua subscrição: 

**Para os utilizadores que migraram - [aplicações de autoria](luis-migration-authoring.md)** de recursos migrados : Como proprietário do recurso, pode adicionar um `contributor`.

**Para utilizadores que ainda não tenham migrado**: Exporte a sua aplicação como um ficheiro JSON. Outro utilizador da LUIS pode importar a app, tornando-se assim o proprietário da app. A nova aplicação terá um ID de aplicação diferente.  

## <a name="access-for-private-and-public-apps"></a>Acesso a aplicativos privados e públicos

Para uma aplicação **privada,** o acesso a tempo de execução está disponível para proprietários e colaboradores. Para uma aplicação **pública,** o acesso a tempo de execução está disponível para todos os que têm o seu próprio [Serviço Cognitivo](../cognitive-services-apis-create-account.md) Azure ou recurso de tempo de execução [LUIS,](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) e tem o ID da aplicação pública. 

Atualmente, não existe um catálogo de aplicações públicas.

### <a name="authoring-access"></a>Acesso de autoria
O acesso à aplicação a partir do portal [LUIS](luis-reference-regions.md#luis-website) ou das [APIs de autoria](https://go.microsoft.com/fwlink/?linkid=2092087) é controlado pelo recurso de autoria Azure. 

O proprietário e todos os colaboradores têm acesso ao autor da app. 

|Inclui acesso de criação|Notas|
|--|--|
|Adicionar ou remover chaves de ponto final||
|Exportação de versão||
|Exportar registos de ponto final||
|Importação de versão||
|Tornar a aplicação pública|Quando uma aplicação é pública, qualquer pessoa com uma chave de criação ou ponto de extremidade pode consultar a aplicação.|
|Modificar modelo|
|Publicar|
|Rever as declarações finais para [a aprendizagem ativa](luis-how-to-review-endpoint-utterances.md)|
|Preparar|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Acesso de prazo de ponta de previsão

O acesso à consulta o ponto final da previsão é controlado por uma definição na página informação de **aplicação** na secção **Gerir.** 

|[Ponto final privado](#runtime-security-for-private-apps)|[Ponto final público](#runtime-security-for-public-apps)|
|:--|:--|
|Disponível para proprietário e contribuintes|Disponível para proprietário, contribuintes, e qualquer outra pessoa que conheça o ID da aplicação|

Pode controlar quem vê a sua chave de tempo de funcionamento LUIS, chamando-a num ambiente servidor-a-servidor. Se estiver a utilizar o LUIS de um bot, a ligação entre o bot e o LUIS já é segura. Se estiver a ligar diretamente para o ponto final da LUIS, deve criar uma API do lado do servidor (como uma [função](https://azure.microsoft.com/services/functions/)Azure) com acesso controlado (como [AAD).](https://azure.microsoft.com/services/active-directory/) Quando a API do lado do servidor for chamada e autenticada e a autorização for verificada, passe a chamada para o LUIS. Embora esta estratégia não impeça ataques man-in-the-middle, ele obfusa o seu URL chave e ponto final dos seus utilizadores, permite-lhe rastrear o acesso, e permite-lhe adicionar registo de resposta de ponto final (como Insights de [Aplicação).](https://azure.microsoft.com/services/application-insights/)

#### <a name="runtime-security-for-private-apps"></a>Segurança de tempo de execução para aplicações privadas

O tempo de execução de uma aplicação privada só está disponível para o seguinte:

|Chave e o utilizador|Explicação|
|--|--|
|Chave de criação do proprietário| Até o ponto final de 1000 ocorrências|
|Chaves de autoria colaborador/colaborador| Até o ponto final de 1000 ocorrências|
|Qualquer chave atribuída à LUIS por um autor ou colaborador/colaborador|Com base na camada de utilização de chave|

#### <a name="runtime-security-for-public-apps"></a>Segurança de tempo de execução para aplicações públicas

Uma vez configurada uma aplicação como pública, _qualquer_ chave de autor de LUIS válida ou chave de ponto final LUIS pode consultar a sua app, desde que a chave não tenha usado toda a quota de ponto final.

Um utilizador que não seja proprietário ou colaborador, só pode aceder ao tempo de execução de uma aplicação pública se for dado o ID da aplicação. O LUIS não tem um _mercado_ público ou outra forma de procurar uma aplicação pública.  

Uma aplicação pública está publicada em todas as regiões, para que um utilizador com uma chave de recurso com base na região do LUIS pode aceder à aplicação em qualquer região está associado com a chave de recurso.

## <a name="transfer-of-ownership"></a>Transferência de propriedade

Luis não tem o conceito de transferir a propriedade de um recurso. 

## <a name="securing-the-endpoint"></a>Proteger o ponto final 

Pode controlar quem pode ver a sua chave de ponta de ponta de previsão LUIS, chamando-a num ambiente servidor-a-servidor. Se estiver a utilizar o LUIS de um bot, a ligação entre o bot e o LUIS já é segura. Se estiver a ligar diretamente para o ponto final da LUIS, deve criar uma API do lado do servidor (como uma [função](https://azure.microsoft.com/services/functions/)Azure) com acesso controlado (como [AAD).](https://azure.microsoft.com/services/active-directory/) Quando é chamado de API do lado do servidor e autenticação e autorização são verificadas, passe a chamada para o LUIS. Embora esta estratégia não impeça ataques man-in-the-middle, ele obfusa o seu ponto final dos seus utilizadores, permite-lhe rastrear o acesso, e permite-lhe adicionar registo de resposta de ponto final (como Insights de [Aplicação).](https://azure.microsoft.com/services/application-insights/)  

## <a name="next-steps"></a>Passos seguintes

* Compreender [conceitos de versão.](luis-concept-version.md) 
* Aprenda [a criar chaves.](luis-how-to-azure-subscription.md)
