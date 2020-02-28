---
title: Redirecione as restrições de URL do URI e da resposta - plataforma de identidade da Microsoft Azure
description: Resposta URLs/redirecionamento uRls restrições e limitações
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 8fdc64632be8b5fcb3dca8de2ee833fef25719fe
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77656743"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Redirecionar restrições e limitações de URI/URI de resposta

Um URI redirecionado, ou URL de resposta, é a localização para a qual o servidor de autorização enviará o utilizador uma vez que a aplicação tenha sido autorizada com sucesso, e concedeu um código de autorização ou ficha de acesso. O código ou token está contido no uri redirecionamento ou token de resposta, pelo que é importante que registe a localização correta como parte do processo de registo da aplicação.

 As seguintes restrições aplicam-se à resposta dos URLs:

    * O URL de resposta deve começar com o esquema `https`.
    * O URL de resposta é sensível a casos. O seu caso deve coincidir com o caso do percurso url da sua aplicação de execução. Por exemplo, se a sua aplicação incluir como parte do seu caminho `.../abc/response-oidc`, não especifique `.../ABC/response-oidc` no URL de resposta. Uma vez que o navegador da Web trata os caminhos como sensíveis a casos, os cookies associados à `.../abc/response-oidc` podem ser excluídos se forem redirecionados para o URL de `.../ABC/response-oidc` desajustado.
    
## <a name="maximum-number-of-redirect-uris"></a>Número máximo de URIs redirecionais

A tabela que se segue mostra o número máximo de URIs redirecionais que pode adicionar quando registar a sua aplicação.

| Contas sendo assinadas em | Número máximo de URIs redirecionais | Descrição |
|--------------------------|---------------------------------|-------------|
| Microsoft trabalha ou conta escolar em qualquer inquilino azure ative diretório (Azure AD) | 256 | `signInAudience` campo no manifesto de aplicação está definido para *AzureADMyOrg* ou *AzureADMultipleOrgs* |
| Contas pessoais da Microsoft e contas de trabalho e escola | 100 | `signInAudience` campo no manifesto de aplicação está definido para *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Comprimento máximo do URI

Pode utilizar um máximo de 256 caracteres para cada redirecionamento de URI que adiciona a uma inscrição na aplicação.

## <a name="supported-schemes"></a>Regimes apoiados
O modelo de aplicação Azure AD suporta hoje tanto os esquemas HTTP como https para apps que assinam em trabalho seletiva da Microsoft ou contas escolares em qualquer inquilino azure ative diretório (Azure AD) de qualquer organização. Isto é `signInAudience` campo no manifesto de aplicação está definido para *AzureADMyOrg* ou *AzureADMultipleOrgs*. Para as aplicações que assinam em contas pessoais da Microsoft e contas de trabalho e escola (que é `signInAudience` definida para *AzureADandPersonalMicrosoftAccount*) apenas é permitido o esquema HTTPS.

> [!NOTE]
> A experiência de [registos](https://go.microsoft.com/fwlink/?linkid=2083908) da nova App não permite que os desenvolvedores adicionem URIs com o esquema HTTP na UI. A adição de HTTP URIs para apps que assinam em contas de trabalho ou de escola é suportada apenas através do editor manifesto da aplicação. Daqui para a frente, as novas aplicações não poderão utilizar esquemas HTTP no redirecionamento URI. No entanto, aplicações mais antigas que contenham esquemas HTTP em URIs redirecionados continuarão a funcionar. Os desenvolvedores devem utilizar esquemas HTTPS nos URIs de redirecionamento.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Restrições usando um wildcard em URIs

Os URIs wildcard, como `https://*.contoso.com`, são convenientes, mas devem ser evitados. A utilização de wildcards no URI redirecionado tem implicações de segurança. De acordo com a especificação OAuth 2.0[(secção 3.1.2 do RFC 6749),](https://tools.ietf.org/html/rfc6749#section-3.1.2)um ponto final de reorientação URI deve ser um URI absoluto. 

O modelo de aplicação Azure AD não suporta URIs wildcard para aplicações configuradas para assinar em contas pessoais da Microsoft e contas de trabalho ou escola. No entanto, os URIs wildcard são permitidos para apps que estão configuradas para assinar em contas de trabalho ou escola em um inquilino Azure AD de uma organização hoje. 
 
> [!NOTE]
> A experiência de [registos](https://go.microsoft.com/fwlink/?linkid=2083908) da nova App não permite que os desenvolvedores adicionem URIs wildcard na UI. A adição de wilcard URI para apps que assinam em contas de trabalho ou de escola é suportada apenas através do editor manifesto da aplicação. Daqui para a frente, as novas aplicações não poderão utilizar wildcards no REdirect URI. No entanto, aplicações mais antigas que contenham wildcards em URIs redirecionados continuarão a funcionar.

Se o seu cenário requer mais URIs redirecionais do que o limite máximo permitido, em vez de adicionar um redirecionamento de wildcard URI, considere a seguinte abordagem.

### <a name="use-a-state-parameter"></a>Use um parâmetro estatal

Se tiver vários subdomínios, e se o seu cenário exigir que redirecione os utilizadores após autenticação bem sucedida para a mesma página onde começaram, usar um parâmetro de estado pode ser útil. 

Nesta abordagem:

1. Crie um URI redirecionado "partilhado" por aplicação para processar as fichas de segurança que recebe do ponto final da autorização.
1. A sua aplicação pode enviar parâmetros específicos da aplicação (como URL de subdomínio onde o utilizador se originou ou qualquer coisa como informações de marca) no parâmetro do estado. Quando utilizar um parâmetro estatal, proteja-se contra a proteção CSRF, conforme especificado na [secção 10.12 do RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)). 
1. Os parâmetros específicos da aplicação incluirão todas as informações necessárias para que a aplicação torne a experiência correta para o utilizador, ou seja, construir o estado de aplicação adequado. O ponto final de autorização da AD Azure tira HTML do parâmetro estatal para se certificar de que não está a passar o conteúdo HTML neste parâmetro.
1. Quando a Azure AD enviar uma resposta ao redirecionamento "partilhado" URI, enviará o parâmetro estatal de volta para a aplicação.
1. A aplicação pode então utilizar o valor no parâmetro do Estado para determinar a que URL enviar o utilizador. Certifique-se de que valida para proteção CSRF.

> [!NOTE]
> Esta abordagem permite que um cliente comprometido modifique os parâmetros adicionais enviados no parâmetro do Estado, redirecionando assim o utilizador para um URL diferente, que é a ameaça aberta de [redirector](https://tools.ietf.org/html/rfc6819#section-4.2.4) descrito no RFC 6819. Por isso, o cliente deve proteger estes parâmetros encriptando o estado ou verificando-o por outros meios, tais como validar o nome de domínio no redirecionamento do URI contra o símbolo.

## <a name="next-steps"></a>Passos seguintes

- Conheça o manifesto de [Candidatura](reference-app-manifest.md)
