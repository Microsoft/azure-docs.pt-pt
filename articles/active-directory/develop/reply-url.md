---
title: Limitações - plataforma de identidade da Microsoft e restrições de URL de resposta/URI de redirecionamento
description: Restrições de URls de redirecionamento/URLs de resposta e limitações
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: article
ms.subservice: develop
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07be7d0c70193fec88782fea681e33d6b4cf4b40
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486236"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Redirecionar restrições e limitações de URI/URI de resposta

Um URI ou URL de resposta de redirecionamento é a localização que o servidor de autorização irá enviar o utilizador para uma vez a aplicação foi autorizada com êxito e conceder um acesso de código de autorização ou token. O código ou o token está contido no redirecionamento URI ou resposta token pelo que é importante que Registre-se a localização correta como parte do processo de registo de aplicação.

## <a name="maximum-number-of-redirect-uris"></a>Número máximo de URIs de redirecionamento

A tabela seguinte mostra o número máximo de URIs que podem ser adicionados ao registar a aplicação de redirecionamento. 

| Contas que está a ser iniciadas sessão | Número máximo de URIs de redirecionamento | Descrição |
|--------------------------|---------------------------------|-------------|
| Contas no inquilino do Azure Active Directory (Azure AD) de qualquer organização de escolar ou profissional da Microsoft | 256 | `signInAudience` campo no manifesto do aplicativo está definido como *AzureADMyOrg* ou *AzureADMultipleOrgs* |
| Microsoft pessoa contas e trabalhar contas escolares ou profissionais | 100 | `signInAudience` campo no manifesto do aplicativo é definido como *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Comprimento máximo do URI

Pode utilizar um máximo de 256 carateres para cada URI de redirecionamento que adicionar a um registo de aplicações.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Restrições de utilizar um caráter universal no URIs

URIs de caráter universal, por exemplo, `https://*.contoso.com`, são convenientes, mas deve ser evitado. Utilização de carateres universais no redirecionamento o URI tem implicações de segurança. De acordo com a especificação de OAuth 2.0 ([secção 3.1.2 de RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)), um URI de ponto final de redirecção tem de ser um URI absoluto. 

O modelo de aplicação do Azure AD não suporta carateres universais URIs para aplicações que estão configuradas para iniciar sessão em contas pessoais da Microsoft e de trabalho contas escolares ou profissionais. No entanto, o caráter universal URIs são permitidas para as aplicações que estão configuradas para iniciar sessão no trabalho contas escolares ou profissionais no inquilino do Azure AD de uma organização hoje mesmo. 
 
> [!NOTE]
> A nova [registos das aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) experiência não permite aos programadores adicionar universais URIs na interface do Usuário. A adição de caráter universal o URI para as aplicações que inicie sessão no trabalho contas escolares ou profissionais é suportada apenas através do editor de manifesto de aplicação. Daqui em diante, aplicações de novas não será possível utilizar carateres universais no URI de redirecionamento. No entanto, as aplicações mais antigas que contêm carateres universais no redirecionamento URIs continuarão a funcionar.

Se o seu cenário requer que mais URIs de redirecionamento que o limite máximo permitido, em vez de adicionar um redirecionamento de caráter universal URI, considere uma das seguintes abordagens.

### <a name="use-a-state-parameter"></a>Utilizar um parâmetro de estado

Se tiver vários subdomínios, e se o seu cenário requer a redirecionar utilizadores quando a autenticação com êxito para a mesma página de início, com um parâmetro de estado poderá ser útil. 

Nesta abordagem:

1. Crie um URI de redirecionamento "partilhado" por aplicação para processar os tokens de segurança a que receber do ponto final de autorização.
1. A aplicação pode enviar parâmetros específicos de aplicativo (por exemplo, o URL de subdomínio onde o utilizador teve origem ou qualquer coisa, como informações de identidade visual) no parâmetro state. Quando utilizar um parâmetro de estado, proteção contra proteção CSRF, conforme especificado nas [secção 10.12 de RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)). 
1. Os parâmetros específicos de aplicações irão incluir todas as informações necessárias para a aplicação processar o correto experiência do utilizador, ou seja, construir o estado de aplicativo apropriado. Faixas de ponto final de autorização do Azure AD HTML do parâmetro de estado por isso, certifique-se de que não estão passando HTML conteúdo neste parâmetro.
1. Quando o Azure AD envia uma resposta para o URI de redirecionamento "partilhado", irá enviar o parâmetro de estado para a aplicação.
1. A aplicação, em seguida, pode utilizar o valor no parâmetro state para determinar o URL para enviar ainda mais o usuário. Certifique-se de que validar para proteção de CSRF.

> [!NOTE]
> Essa abordagem permite que um cliente comprometido modificar os parâmetros adicionais enviados no parâmetro state, deste modo, redirecionar o utilizador para um URL diferente, o que é o [abra ameaças de redirecionador](https://tools.ietf.org/html/rfc6819#section-4.2.4) descrito na RFC 6819. Por conseguinte, o cliente terá de proteger estes parâmetros ao encriptar o estado ou verificar através de outros meios, como o nome de domínio no URI de redirecionamento contra o token de validação.

### <a name="add-redirect-uris-to-service-principals"></a>Adicionar URIs de redirecionamento para principais de serviço

Outra abordagem é adicionar redirecionar URIs para o [principais de serviço](app-objects-and-service-principals.md#application-and-service-principal-relationship) que representam o registo de aplicação em qualquer inquilino do Azure AD. Pode utilizar esta abordagem quando não é possível utilizar um parâmetro de estado ou seu cenário requer a adicionar novo URIs de redirecionamento ao registo da aplicação para cada novo inquilino suportadas. 

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [manifesto da aplicação](reference-app-manifest.md)
