---
title: Registe as aplicações Azure Ative Directory para Azure API para FHIR
description: Este tutorial explica quais as aplicações que precisam de ser registadas para Azure API para FHIR e FHIR Server para Azure.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: b01f65b6be31007cebc7a5b03a1c57f6b901fe53
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020241"
---
# <a name="register-the-azure-active-directory-apps-for-azure-api-for-fhir"></a>Registe as aplicações Azure Ative Directory para Azure API para FHIR

Tem várias opções de configuração para escolher quando estiver a configurar a API Azure para FHIR ou o FHIR Server for Azure (OSS). Para obter uma fonte aberta, terá de criar o seu próprio registo de pedidos de recursos. Para a Azure API para FHIR, esta aplicação de recursos é criada automaticamente.

## <a name="application-registrations"></a>Inscrições de candidatura

Para que um pedido de interação com a Azure AD, tem de ser registado. No contexto do servidor FHIR, existem dois tipos de registos de candidaturas para discutir:

1. Registos de pedidos de recursos.
1. Inscrições de pedido de cliente.

**As aplicações de recursos** são representações em Azure AD de uma API ou recurso que é garantido com Azure AD, especificamente seria a API Azure para FHIR. Uma aplicação de recurso para Azure API para FHIR será criada automaticamente quando você fornece o serviço, mas se você estiver usando o servidor de código aberto, você precisará [registrar uma aplicação de recurso](register-resource-azure-ad-client-app.md) em AD Azure. Esta aplicação de recursos terá um URI identificador. Recomenda-se que este URI seja o mesmo que o URI do servidor FHIR. Este URI deve ser utilizado como para `Audience` o servidor FHIR. Uma aplicação de cliente pode solicitar acesso a este servidor FHIR quando solicita um token.

*As candidaturas* ao cliente são registos dos clientes que vão solicitar fichas. Muitas vezes em OAuth 2.0, distinguimos entre pelo menos três tipos diferentes de aplicações:

1. **Clientes confidenciais**, também conhecidos como web apps em Azure AD. Os clientes confidenciais são aplicações que utilizam [o fluxo de código](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) de autorização para obter um token em nome de um utilizador assinado em suporte válido. São chamados clientes confidenciais porque são capazes de guardar um segredo e apresentarão este segredo à Azure AD ao trocar o código de autenticação por um símbolo. Uma vez que os clientes confidenciais são capazes de se autenticar usando o segredo do cliente, eles são confiáveis mais do que os clientes públicos e podem ter tokens de vida mais longo e receber um token de atualização. Leia os detalhes sobre como [registar um cliente confidencial.](register-confidential-azure-ad-client-app.md) Note que é importante registar o url de resposta no qual o cliente receberá o código de autorização.
1. **Clientes públicos.** Estes são clientes que não podem guardar um segredo. Normalmente, esta seria uma aplicação de dispositivo móvel ou uma aplicação JavaScript de uma única página, onde um segredo no cliente poderia ser descoberto por um utilizador. Os clientes públicos também usam o fluxo de código de autorização, mas não estão autorizados a apresentar um segredo quando obtêm um token e podem ter fichas de vida mais curtas e nenhum token de atualização. Leia os detalhes sobre como [registar um cliente público.](register-public-azure-ad-client-app.md)
1. Clientes de serviço. Estes clientes obtêm fichas em nome de si próprios (não em nome de um utilizador) utilizando o fluxo de credenciais do [cliente.](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) Normalmente representam aplicações que acedem ao servidor FHIR de forma não interativa. Um exemplo seria um processo de ingestão. Ao utilizar um cliente de serviço, não é necessário iniciar o processo de obtenção de um token com uma chamada para o `/authorize` ponto final. Um cliente de serviço pode ir diretamente para o `/token` ponto final e apresentar identificação do cliente e segredo de cliente para obter um símbolo. Leia os detalhes sobre como [registar um cliente de serviço](register-service-azure-ad-client-app.md)

## <a name="next-steps"></a>Passos seguintes

Nesta visão geral, você passou pelos tipos de registos de inscrição que você pode precisar para trabalhar com uma API FHIR.

Com base na sua configuração, consulte os guias de como registar as suas aplicações

* [Registar uma aplicação de recurso](register-resource-azure-ad-client-app.md)
* [Registar uma aplicação cliente confidencial](register-confidential-azure-ad-client-app.md)
* [Registar uma aplicação cliente pública](register-public-azure-ad-client-app.md)
* [Registar um pedido de serviço](register-service-azure-ad-client-app.md)

Uma vez registadas as suas candidaturas, pode implantar a API Azure para FHIR.

>[!div class="nextstepaction"]
>[Implementar o Azure API for FHIR](fhir-paas-powershell-quickstart.md)