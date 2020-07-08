---
title: Redirecionar & resposta URI restrições de URL - plataforma de identidade da Microsoft Rio Azure
description: Resposta URLs/redirecionar restrições de RINS & limitações
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: b7aefc54a20e23ae969750532e7e3bc824f69c56
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83725317"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Redirecionar restrições e limitações de URI/URI de resposta

Um URI de redirecionamento, ou URL de resposta, é o local para o qual o servidor de autorização enviará o utilizador assim que a aplicação tiver sido autorizada com sucesso, e concedido um código de autorização ou ficha de acesso. O código ou token está contido no URI de redirecionamento ou token de resposta, pelo que é importante registar a localização correta como parte do processo de registo da aplicação.

 Aplicam-se as seguintes restrições aos URLs de resposta:

* A URL de resposta deve começar pelo regime `https` .

* A URL de resposta é sensível a casos. O seu caso deve coincidir com o caso do caminho URL da sua aplicação de execução. Por exemplo, se a sua aplicação incluir como parte do seu `.../abc/response-oidc` caminho, não especifique `.../ABC/response-oidc` no URL de resposta. Como o navegador da Web trata os caminhos como sensíveis a casos, os cookies associados `.../abc/response-oidc` podem ser excluídos se forem redirecionados para o URL desajustado do `.../ABC/response-oidc` caso.
    
## <a name="maximum-number-of-redirect-uris"></a>Número máximo de URIs de redirecionamento

A tabela a seguir mostra o número máximo de URIs de redirecionamento que pode adicionar quando regista a sua aplicação.

| Contas a serem assinadas | Número máximo de URIs de redirecionamento | Descrição |
|--------------------------|---------------------------------|-------------|
| Trabalho da Microsoft ou contas escolares em qualquer inquilino do Azure Ative Directory (Azure AD) de qualquer organização | 256 | `signInAudience`campo no manifesto de aplicação é definido para *AzureADMyOrg* ou *AzureADMultipleOrgs* |
| Contas pessoais da Microsoft e contas de trabalho e escola | 100 | `signInAudience`campo no manifesto de aplicação é definido para *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Comprimento máximo de URI

Pode utilizar um máximo de 256 caracteres para cada URI de redirecionamento que adicionar a um registo de aplicações.

## <a name="supported-schemes"></a>Regimes apoiados
O modelo de aplicação AZure AD suporta hoje esquemas HTTP e HTTPS para aplicações que assinam no trabalho da Microsoft ou contas escolares em qualquer inquilino do Azure Ative Directory (Azure AD) de qualquer organização. Isto `signInAudience` é, o campo no manifesto de aplicação está definido para *AzureADMyOrg* ou *AzureADMultipleOrgs*. Para as aplicações que assinam nas contas pessoais da Microsoft e contas de trabalho e escola (que está `signInAudience` definida para *AzureADandPersonalMicrosoftAccount),* apenas é permitido o esquema HTTPS.

> [!NOTE]
> A experiência dos novos [registos de aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) não permite que os desenvolvedores adicione URIs com esquema HTTP na UI. Adicionar URIs HTTP para apps que assinam no trabalho ou contas escolares é suportado apenas através do editor manifesto da aplicação. Para a frente, as novas aplicações não poderão utilizar esquemas HTTP no URI de redirecionamento. No entanto, as aplicações mais antigas que contenham esquemas HTTP em URIs de redirecionamento continuarão a funcionar. Os desenvolvedores devem utilizar esquemas HTTPS nos URIs de redirecionamento.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Restrições usando um wildcard em URIs

Os URIs wildcard, tais `https://*.contoso.com` como, são convenientes, mas devem ser evitados. A utilização de wildcards no URI de redirecionamento tem implicações de segurança. De acordo com a especificação OAuth 2.0 ([secção 3.1.2 do RFC 6749),](https://tools.ietf.org/html/rfc6749#section-3.1.2)um ponto final de reorientação URI deve ser um URI absoluto. 

O modelo de aplicação AD AZure não suporta URIs wildcard para aplicações configuradas para assinar em contas pessoais da Microsoft e contas de trabalho ou escola. No entanto, os URIs wildcard são permitidos para apps configuradas para assinar em trabalho ou contas escolares no inquilino AZure AD de uma organização hoje. 
 
> [!NOTE]
> A experiência dos novos [registos de aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) não permite que os desenvolvedores adicionem URIs wildcard na UI. A adição de wilcard URI para apps que assinam no trabalho ou contas escolares é suportada apenas através do editor manifesto da app. Para a frente, as novas aplicações não poderão utilizar wildcards no URI de redirecionamento. No entanto, aplicações mais antigas que contenham wildcards em URIs de redirecionamento continuarão a funcionar.

Se o seu cenário requer mais URIs redirecionado do que o limite máximo permitido, em vez de adicionar um wildcard redirect URI, considere a seguinte abordagem.

### <a name="use-a-state-parameter"></a>Use um parâmetro de estado

Se tiver vários sub-domínios, e se o seu cenário exigir que redirecione os utilizadores após a autenticação bem sucedida para a mesma página onde começaram, usar um parâmetro de estado pode ser útil. 

Nesta abordagem:

1. Crie um URI de redirecionamento "compartilhado" por aplicação para processar os tokens de segurança que recebe do ponto final de autorização.
1. A sua aplicação pode enviar parâmetros específicos da aplicação (como URL de sub-domínio onde o utilizador se originou ou qualquer coisa como informações de marca) no parâmetro do estado. Ao utilizar um parâmetro de estado, proteja contra a proteção CSRF, conforme especificado na [secção 10.12 do RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12). 
1. Os parâmetros específicos da aplicação incluirão todas as informações necessárias para a aplicação para tornar a experiência correta para o utilizador, ou seja, construir o estado de aplicação adequado. O ponto final de autorização AZure AD tira HTML do parâmetro do estado, por isso certifique-se de que não está a passar o conteúdo HTML neste parâmetro.
1. Quando a Azure AD enviar uma resposta ao redirecionamento "partilhado" URI, enviará o parâmetro do Estado de volta para a aplicação.
1. A aplicação pode então utilizar o valor no parâmetro do estado para determinar para que URL enviar ainda mais o utilizador. Certifique-se de que valida para a proteção CSRF.

> [!NOTE]
> Esta abordagem permite que um cliente comprometido modifique os parâmetros adicionais enviados no parâmetro do estado, redirecionando assim o utilizador para um URL diferente, que é a [ameaça de redirecionamento aberto](https://tools.ietf.org/html/rfc6819#section-4.2.4) descrita no RFC 6819. Portanto, o cliente deve proteger estes parâmetros encriptando o estado ou verificando-o por outros meios, tais como validar o nome de domínio no URI redirecionado contra o token.

## <a name="next-steps"></a>Próximos passos

- Conheça o [manifesto da Candidatura](reference-app-manifest.md)
