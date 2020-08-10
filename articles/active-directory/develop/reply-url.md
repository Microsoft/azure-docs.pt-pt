---
title: Redirecionar restrições URI (URL de resposta) / Rio Azure
titleSuffix: Microsoft identity platform
description: Uma descrição das restrições e limitações no formato URI (RESPOSTA URL) aplicada pela plataforma de identidade microsoft.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 08/07/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 6a8cc588ff7325242e7e010e9869eaa9a24f6fc2
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88033341"
---
# <a name="redirect-uri-reply-url-restrictions-and-limitations"></a>Redirecionar restrições e limitações URI (URL de resposta)

Um URI de redirecionamento, ou URL de resposta, é o local onde o servidor de autorização envia o utilizador uma vez que a aplicação foi autorizada com sucesso e concedeu um código de autorização ou ficha de acesso. O servidor de autorização envia o código ou ficha para o URI redirecionamento, por isso é importante registar a localização correta como parte do processo de registo da aplicação.

 Aplicam-se as seguintes restrições ao redirecionamento de IUR:

* O URI de redireccionamento deve começar com o regime `https` .

* O URI de redirecionamento é sensível a casos. O seu caso deve coincidir com o caso do caminho URL da sua aplicação de execução. Por exemplo, se a sua aplicação incluir como parte do seu `.../abc/response-oidc` caminho, não especifique `.../ABC/response-oidc` no URI de redirecionamento. Como o navegador da Web trata os caminhos como sensíveis a casos, os cookies associados `.../abc/response-oidc` podem ser excluídos se forem redirecionados para o URL desajustado do `.../ABC/response-oidc` caso.

## <a name="maximum-number-of-redirect-uris"></a>Número máximo de URIs de redirecionamento

Esta tabela mostra o número máximo de URIs de redirecionamento que pode adicionar a um registo de aplicações na plataforma de identidade da Microsoft.

| Contas a serem assinadas | Número máximo de URIs de redirecionamento | Descrição |
|--------------------------|---------------------------------|-------------|
| Trabalho da Microsoft ou contas escolares em qualquer inquilino do Azure Ative Directory (Azure AD) de qualquer organização | 256 | `signInAudience`campo no manifesto de aplicação é definido para *AzureADMyOrg* ou *AzureADMultipleOrgs* |
| Contas pessoais da Microsoft e contas de trabalho e escola | 100 | `signInAudience`campo no manifesto de aplicação é definido para *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Comprimento máximo de URI

Pode utilizar um máximo de 256 caracteres para cada redireccionamento URI que adicionar a um registo de aplicações.

## <a name="supported-schemes"></a>Regimes apoiados

O modelo de aplicação Azure Ative Directory (Azure AD) suporta atualmente esquemas HTTP e HTTPS para aplicações que assinam contas de trabalho ou escola no inquilino AZure AD de qualquer organização. Estes tipos de conta são especificados pelo `AzureADMyOrg` e `AzureADMultipleOrgs` valores no `signInAudience` campo do manifesto de candidatura. Para aplicações que assinam nas contas pessoais da Microsoft (MSA) *e* contas de trabalho e escola (isto é, `signInAudience` o programado `AzureADandPersonalMicrosoftAccount` para), apenas o esquema HTTPS é permitido.

Para adicionar URIs redirecionando com um esquema HTTP para registos de aplicações que assinam em contas de trabalho ou escola, você precisa usar o editor manifesto de aplicação nas [inscrições](https://go.microsoft.com/fwlink/?linkid=2083908) da App no portal Azure. No entanto, embora seja possível definir um URI de redirecionamento baseado em HTTP usando o editor manifesto, recomendamos *vivamente* que utilize o esquema HTTPS para os seus URIs de redirecionamento.

## <a name="localhost-exceptions"></a>Exceções locais

Por [RFC 8252 secções 8.3](https://tools.ietf.org/html/rfc8252#section-8.3) e [7.3](https://tools.ietf.org/html/rfc8252#section-7.3), "loopback" ou "localhost" redireccionamento URIs vêm com duas considerações especiais:

1. `http`Os esquemas URI são aceitáveis porque o redirecionamento nunca sai do dispositivo. Como tal, ambas são aceitáveis:
    - `http://127.0.0.1/myApp`
    - `https://127.0.0.1/myApp`
1. Devido às gamas de portas efémeras frequentemente exigidas por aplicações nativas, o componente portuário (por exemplo, `:5001` ou ) é ignorado para efeitos de correspondência de um `:443` URI de redirecionamento. Como resultado, todos estes são considerados equivalentes:
    - `http://127.0.0.1/MyApp`
    - `http://127.0.0.1:1234/MyApp`
    - `http://127.0.0.1:5000/MyApp`
    - `http://127.0.0.1:8080/MyApp`

Do ponto de vista do desenvolvimento, isto significa algumas coisas:

1. Não registe uris de redirecionamento múltiplo onde apenas a porta difere. O servidor de login escolherá um arbitrariamente e utilizará o comportamento associado a esse URI redirecionado (por exemplo, seja `web` -, `native` ou `spa` -type redirect).
1. Se precisar de registar uris de redirecionamento múltiplo na localidade local para testar diferentes fluxos durante o desenvolvimento, diferenciá-los utilizando o componente de *caminho* do URI. Por exemplo, `http://127.0.0.1/MyWebApp` não `http://127.0.0.1/MyNativeApp` corresponde.
1. Por orientação RFC, não deve ser utilizado `localhost` no URI de redirecionamento. Em vez disso, utilize o endereço IP de retorno real, `127.0.0.1` . Isto impede que a sua aplicação seja quebrada por firewalls mal configuradas ou interfaces de rede renomeadas.

    O endereço de backback IPv6 `[::1]` não está atualmente suportado.

## <a name="restrictions-on-wildcards-in-redirect-uris"></a>Restrições em wildcards em URIs de redirecionamento

Os URIs wildcard `https://*.contoso.com` podem parecer convenientes, mas devem ser evitados devido a implicações de segurança. De acordo com a especificação OAuth 2.0 ([secção 3.1.2 do RFC 6749),](https://tools.ietf.org/html/rfc6749#section-3.1.2)um ponto final de reorientação URI deve ser um URI absoluto.

Os URIs wildcard não são atualmente suportados em registos de aplicações configurados para assinar em contas pessoais da Microsoft e contas de trabalho ou escola. Os URIs wildcard são permitidos, no entanto, para aplicações configuradas para assinar apenas em contas de trabalho ou escola no inquilino AZure AD de uma organização.

Para adicionar URIs redirecionando com wildcards para registos de aplicações que assinam em contas de trabalho ou escola, você precisa usar o editor manifesto de aplicação nas [inscrições](https://go.microsoft.com/fwlink/?linkid=2083908) da App no portal Azure. Embora seja possível definir um URI redirecionado com um wildcard usando o editor manifesto, recomendamos *vivamente* que adera à [secção 3.1.2 do RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2) e use apenas URIs absolutos.

Se o seu cenário requer mais URIs de redirecionamento do que o limite máximo permitido, considere a [seguinte abordagem](#use-a-state-parameter) em vez de adicionar um URI de redirecionamento wildcard.

### <a name="use-a-state-parameter"></a>Use um parâmetro de estado

Se tiver vários subdomínios e o seu cenário requer que, após a autenticação bem sucedida, redirecione os utilizadores para a mesma página a partir da qual começaram, usando um parâmetro de estado pode ser útil.

Nesta abordagem:

1. Crie um URI de redirecionamento "compartilhado" por aplicação para processar os tokens de segurança que recebe do ponto final de autorização.
1. A sua aplicação pode enviar parâmetros específicos da aplicação (como URL de subdomínio, onde o utilizador se originou ou qualquer coisa como informações de marca) no parâmetro do estado. Ao utilizar um parâmetro de estado, proteja contra a proteção CSRF, conforme especificado na [secção 10.12 do RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12).
1. Os parâmetros específicos da aplicação incluirão todas as informações necessárias para a aplicação para tornar a experiência correta para o utilizador, ou seja, construir o estado de aplicação adequado. O ponto final de autorização AZure AD tira HTML do parâmetro do estado, por isso certifique-se de que não está a passar o conteúdo HTML neste parâmetro.
1. Quando a Azure AD enviar uma resposta ao redirecionamento "partilhado" URI, enviará o parâmetro do Estado de volta para a aplicação.
1. A aplicação pode então utilizar o valor no parâmetro do estado para determinar para que URL enviar ainda mais o utilizador. Certifique-se de que valida para a proteção CSRF.

> [!WARNING]
> Esta abordagem permite que um cliente comprometido modifique os parâmetros adicionais enviados no parâmetro do estado, redirecionando assim o utilizador para um URL diferente, que é a [ameaça de redirecionamento aberto](https://tools.ietf.org/html/rfc6819#section-4.2.4) descrita no RFC 6819. Portanto, o cliente deve proteger estes parâmetros encriptando o estado ou verificando-o por outros meios, como validar o nome de domínio no URI redirecionado contra o token.

## <a name="next-steps"></a>Passos seguintes

Conheça o [manifesto de inscrição](reference-app-manifest.md)da aplicação.
