---
title: Segurança quando estão a colaborar
titleSuffix: Language Understanding - Azure Cognitive Services
description: Acesso de criação está disponível para os proprietários e colaboradores. Para uma aplicação privada, o acesso de ponto final está disponível para os proprietários e colaboradores.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: diberry
ms.openlocfilehash: 4465488dc02568932e839d3234bdb672d59e6804
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58089850"
---
# <a name="authoring-and-endpoint-user-access"></a>Criação e o ponto final de acesso de utilizador
Acesso de criação está disponível para os proprietários e colaboradores. Para uma aplicação privada, o acesso de ponto final está disponível para os proprietários e colaboradores. Para uma aplicação pública, acesso de ponto final está disponível para todos os utilizadores que tem sua própria conta de LUIS tem o ID de. a aplicação pública 

## <a name="access-to-authoring"></a>Acesso a criação
Acesso para a aplicação a partir da [LUIS](luis-reference-regions.md#luis-website) Web site ou o [APIs de criação](https://aka.ms/luis-authoring-apis) é controlado pelo proprietário da aplicação. 

O proprietário e todos os funcionários têm acesso para criar a aplicação. 

|Inclui acesso de criação|Notas|
|--|--|
|Adicionar ou remover chaves de ponto final||
|Exportação de versão||
|Exportar registos de ponto final||
|Importação de versão||
|Tornar a aplicação pública|Quando uma aplicação é pública, qualquer pessoa com uma chave de criação ou ponto de extremidade pode consultar a aplicação.|
|Modificar modelo|
|Publicar|
|Reveja as expressões de ponto final para [aprendizagem ativa](luis-how-to-review-endoint-utt.md)|
|Preparar|

## <a name="access-to-endpoint"></a>Acesso ao ponto final

Acesso para consultar o ponto final é controlado por uma definição no **informações da aplicação** página no **gerir** secção. 

![Aplicação de conjunto para o público](./media/luis-concept-security/set-application-as-public.png)

|[Ponto final privado](#private-app-endpoint-security)|[Ponto final público](#public-app-endpoint-access)|
|:--|:--|
|Disponível para o proprietário e os funcionários|Disponível para o proprietário, colaboradores e qualquer pessoa outro que sabe o ID de aplicação|

### <a name="private-app-endpoint-security"></a>Segurança de ponto final de aplicativos privados

Ponto final da aplicação privada só está disponível para o seguinte:

|Chave e o utilizador|Explicação|
|--|--|
|Chave de criação do proprietário| Até o ponto final de 1000 ocorrências|
|Chaves de criação dos colaboradores| Até o ponto final de 1000 ocorrências|
|Qualquer tecla atribuída a LUIS por um autor ou colaborador|Com base na camada de utilização de chave|

#### <a name="microsoft-user-accounts"></a>Contas de utilizador da Microsoft

Autores e colaboradores podem atribuir chaves para uma aplicação LUIS privada. A conta de utilizador da Microsoft que cria a chave de LUIS no portal do Azure tem de ser o proprietário da aplicação ou um funcionário da aplicação. Não é possível atribuir uma chave para uma aplicação privada a partir de outra conta do Azure.

Ver [utilizador de inquilino do Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-tenant-user) para saber mais sobre contas de utilizador do Active Directory. 

### <a name="public-app-endpoint-access"></a>Acesso de ponto final de aplicações públicas

Depois de uma aplicação é configurada como pública, _qualquer_ LUIS válido, a criação de chave ou chave de ponto final do LUIS pode consultar a sua aplicação, desde que a chave não utilizado a quota de ponto final de todo.

Um utilizador que não é um proprietário ou funcionário, só pode aceder a uma aplicação pública se for indicado o ID da aplicação. LUIS não tem um público _mercado_ ou outra forma para procurar uma aplicação pública.  

Uma aplicação pública está publicada em todas as regiões, para que um utilizador com uma chave de recurso com base na região do LUIS pode aceder à aplicação em qualquer região está associado com a chave de recurso.

## <a name="microsoft-user-accounts"></a>Contas de utilizador da Microsoft

Autores e colaboradores podem adicionar chaves para LUIS na página de publicação. A conta de utilizador da Microsoft que cria a chave de LUIS no portal do Azure tem de ser o proprietário da aplicação ou um funcionário da aplicação. 

Ver [utilizador de inquilino do Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-tenant-user) para saber mais sobre contas de utilizador do Active Directory. 

## <a name="securing-the-endpoint"></a>Proteger o ponto final 

Pode controlar quem pode ver a sua chave de ponto final do LUIS chamando-lo num ambiente de servidor a servidor. Se estiver a utilizar o LUIS de um bot, a ligação entre o bot e o LUIS já é segura. Se estiver a chamar diretamente o ponto de extremidade do LUIS, deve criar uma API do lado do servidor (como do Azure [função](https://azure.microsoft.com/services/functions/)) com o acesso controlado (tal como [AAD](https://azure.microsoft.com/services/active-directory/)). Quando é chamado de API do lado do servidor e autenticação e autorização são verificadas, passe a chamada para o LUIS. Embora essa estratégia não impede ataques man-in-the-middle, obfuscates o ponto final dos seus utilizadores, permite-lhe controlar o acesso e permite-lhe adicionar o registo de resposta do ponto final (tal como [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="security-compliance"></a>Conformidade de segurança
 
[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-security-compliance.md)]

## <a name="next-steps"></a>Passos Seguintes

Ver [melhores práticas](luis-concept-best-practices.md) para aprender a utilizar as intenções e entidades para as melhores previsões.
