---
title: Teste de aplicativos Microsoft Authentication Library (MSAL) | Azure
description: Saiba mais sobre o teste de aplicativos Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/28/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3495ad5f691ac57b69ab5d3efcd5436cc66e9a6e
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307529"
---
# <a name="testing-msal-applications"></a>Teste de aplicativos de MSAL
Este artigo discute as sugestões para testar aplicativos Microsoft Authentication Library (MSAL).

## <a name="unit-testing"></a>Teste de unidade
As APIs de MSAL utilizam o [padrão de builder](https://wikipedia.org/wiki/Builder_pattern) muito. Construtores são difíceis e entediantes simular. Em vez disso, recomendamos que encapsular a lógica de autenticação por trás de uma interface e simular que na sua aplicação.

## <a name="end-to-end-testing"></a>Teste de ponto a ponto
Para fins de teste-a-ponto, pode configurar contas de teste, teste os aplicativos ou até mesmo separar diretórios. Nome de utilizador e palavras-passe podem ser implementadas por meio do pipeline de integração contínua (por exemplo, compilação secreta variáveis em DevOps do Azure). Outra estratégia é ter credenciais de teste em [Key Vault](/azure/key-vault/) e configurar a máquina que executa os testes para aceder ao Cofre de chaves), por exemplo, instalando um certificado. Pode utilizar da MSAL [estratégia para aceder ao Key Vault](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/master/tests/Microsoft.Identity.Test.LabInfrastructure/KeyVaultSecretsProvider.cs#L112).

Depois de ocorre a aquisição do token, um token de acesso e um token de atualização são armazenados em cache. A primeira tem uma duração de uma hora, a última opção de vários meses. Quando o token de acesso expira, MSAL utilizará automaticamente o token de atualização para adquirir um novo, sem interação do utilizador. Também pode utilizar este comportamento para aprovisionar os seus testes.

Se tiver configurado de acesso condicional, será difícil automatizar em torno dele. É mais fácil de ter um passo manual que negociações com acesso condicional (por exemplo, autenticação multifator), que irão adicionar tokens para a cache MSAL e, em seguida, contar com aquisições de token silenciosas, ou seja, dependem de um utilizador anteriormente com sessão iniciada.

## <a name="web-apps"></a>Web Apps
Utilize o Selenium ou uma tecnologia equivalente para automatizar a aplicação web. Obter nomes de utilizador e palavra-passe a partir de uma localização segura.

## <a name="daemon-apps"></a>Aplicações daemon
Aplicações daemon utilizam segredos previamente implantados (palavras-passe ou certificados) para comunicar com a Azure Active Directory (AAD). Pode implementar um segredo no seu ambiente de teste ou utilizar o token de colocação em cache técnica para aprovisionar os seus testes. O [concessão de credenciais de cliente](msal-authentication-flows.md#client-credentials) não obter tokens de atualização, apenas os tokens, o que expirarem dentro de uma hora de acesso.

## <a name="native-client-apps"></a>Aplicações clientes nativas
Para clientes nativos, existem várias abordagens para teste:

* Utilize o [concessão de nome de utilizador/palavra-passe](msal-authentication-flows.md#usernamepassword) para obter um token de forma não interativa. Este fluxo não é recomendado em produção, mas é razoável para utilizá-la para fins de teste.

* Utilize uma arquitetura, como Appium ou Xamarin.Test, que fornece uma interface de automatização para a sua aplicação e a MSAL criado browser.

* A MSAL expõe um ponto de extensibilidade que permite aos desenvolvedores injetar sua própria experiência de browser. A equipe MSAL usa essa função internamente para testar cenários de autenticação interativa. Dê uma olhada [este .NET testar o projeto](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/master/tests/Microsoft.Identity.Test.Integration.net45/SeleniumTests/InteractiveFlowTests.cs) para ver como injetar um [Selenium baseada em navegador](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/tree/master/tests/Microsoft.Identity.Test.Integration.net45/Infrastructure) que pode processar a autenticação.

## <a name="xamarin-apps"></a>Aplicações para Xamarin
A equipe MSAL atualmente em execução testes numa aplicação Xamarin que utiliza MSAL.NET; Estamos a utilizar [App Center](https://appcenter.ms/apps) para gerir dispositivos, teste é executado etc. A estrutura de teste está [Xamarin.UITest](/appcenter/test-cloud/uitest/). Uma limitação que Descobrimos é que não é possível testar navegadores de sistema, só incorporado navegadores.

Ao avaliar uma estrutura de teste, vale a pena ter também uma olhada no Appium e outras estruturas de teste, bem como outros fornecedores de CI/CD no espaço móvel.

## <a name="testing-the-token-cache"></a>Teste o cache de tokens
Não importa qual plataforma use, MSAL armazena tokens em cache de tokens. Em algumas plataformas, informa ao MSAL como serializar esta cache. Em plataformas móveis, MSAL serializa o cache para. Da perspectiva do aplicativo, o cache de tokens é responsável por três coisas:

* armazenar tokens em cache após eles foram obtidos (por exemplo, utilizando o `AcquireTokenInteractive` método)
* a obter tokens a partir da cache ao chamar o `AcquireTokenSilent` método
* obter os metadados de conta da cache ao chamar o `GetAccount` método

Portanto, se pretender testar cenários de cache, considere que escrever um cenário que seria:

* adquirir um ou mais tokens (por exemplo, utilizando [concessão de nome de utilizador/palavra-passe](msal-authentication-flows.md#usernamepassword) fluxo, que é o mais simples para fins de teste)
* Certifique-se de que `GetAccounts` funciona
* Certifique-se de que `AcquireTokenSilent` funciona

Convém também testar que:

* reiniciar a aplicação não remove a cache
* `AcquireTokenSilent` não atualiza o token de atualização (ou seja, de rede chamada para o AAD), mas serve o token de acesso, se não expirou. Pode obter esse e outros cenários relacionados com o HTTP ao assumir o controlo do cliente HTTP.  Para obter um exemplo de .NET, consulte [fornecer seu próprio HttpClient](msal-net-provide-httpclient.md)

## <a name="feedback"></a>Comentários
Pode [problemas de registo](developer-support-help-options.md#create-a-github-issue) ou fazer perguntas relacionadas com o teste. Para proporcionar uma experiência de bom teste é um dos objetivos da equipe.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre a implementação [registo](msal-logging.md) em seu aplicativo de MSAL.