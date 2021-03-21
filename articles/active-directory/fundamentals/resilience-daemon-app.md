---
title: Aumentar a resiliência da autenticação e autorização nas aplicações da Ememon que desenvolve
titleSuffix: Microsoft identity platform
description: Orientações para aumentar a resiliência da autenticação e autorização na aplicação Daemon utilizando a plataforma de identidade da Microsoft
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: a7b8f893026bb96c8d768d2e6d07d0240ecb81fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724846"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-daemon-applications-you-develop"></a>Aumentar a resiliência da autenticação e autorização nas aplicações da Ememon que desenvolve

Este artigo fornece orientações sobre como os desenvolvedores podem usar a plataforma de identidade microsoft e o Azure Ative Directory para aumentar a resiliência das aplicações daemon. Isto inclui processos de fundo, serviços, servidores para aplicações de servidores e aplicações sem utilizadores.

![Uma aplicação daemon fazendo uma chamada para a identidade da Microsoft](media/resilience-daemon-app/calling-microsoft-identity.png)

## <a name="use-managed-identities-for-azure-resources"></a>Utilizar identidades geridas para recursos azuis

Os desenvolvedores que couem aplicações daemon no Microsoft Azure podem usar [identidades geridas para recursos Azure.](../managed-identities-azure-resources/overview.md) Identidades geridas eliminam a necessidade de os desenvolvedores gerirem segredos e credenciais. A funcionalidade melhora a resiliência evitando erros em torno da expiração do certificado, erros de rotação ou confiança. Também tem várias características incorporadas destinadas especificamente a aumentar a resiliência.

Identidades geridas usam fichas de acesso de longa duração e informações da Microsoft Identity para adquirir proativamente novos tokens dentro de uma grande janela de tempo antes que o token existente expire. A sua aplicação pode continuar a funcionar enquanto tenta adquirir um novo token.

As identidades geridas também utilizam pontos finais regionais para melhorar o desempenho e a resiliência contra falhas fora da região. A utilização de um ponto final regional ajuda a manter todo o tráfego dentro de uma área geográfica. Por exemplo, se o seu Recurso Azure estiver no WestUS2, todo o tráfego, incluindo o tráfego gerado pela Microsoft Identity, deve permanecer no WestUS2. Isto elimina possíveis pontos de falha consolidando as dependências do seu serviço.

## <a name="use-the-microsoft-authentication-library"></a>Utilize a Biblioteca de Autenticação microsoft

Os desenvolvedores de aplicações daemon que não usam Identidades Geridas podem usar a [Microsoft Authentication Library (MSAL),](../develop/msal-overview.md)o que torna a autenticação e autorização de implementação simples, e automaticamente utiliza as melhores práticas para a resiliência. A MSAL facilitará o processo de disponibilização das credenciais de cliente necessárias. Por exemplo, a sua aplicação não precisa de implementar a criação e a assinatura de afirmações do JSON Web Token ao utilizar credenciais baseadas em certificados.

### <a name="use-microsoftidentityweb-for-net-developers"></a>Utilize Microsoft.Identity.Web para desenvolvedores .NET

Os desenvolvedores que couem aplicações daemon no ASP.NET Core podem usar a biblioteca [Microsoft.Identity.Web.](../develop/microsoft-identity-web.md) Esta biblioteca é construída em cima do MSAL para tornar a autorização de implementação ainda mais fácil para ASP.NET aplicações Core. Inclui várias estratégias [de cache de fichas distribuídas](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization#distributed-token-cache) para aplicações distribuídas que podem ser executadas em várias regiões.

## <a name="cache-and-store-tokens"></a>Cache e fichas de loja

Se não estiver a utilizar o MSAL para implementar a autenticação e a autorização, pode implementar algumas boas práticas para caching e armazenamento de fichas. A MSAL implementa e segue automaticamente estas boas práticas.

Um pedido adquire fichas de um fornecedor de identidade para autorizar o pedido de chamada de APIs protegidas. Quando a sua aplicação recebe fichas, a resposta que contém os tokens também contém uma propriedade "expira \_ em" que diz à aplicação quanto tempo para cache, e reutilizar, o token. É importante que as aplicações utilizem a propriedade "expira \_ em" para determinar o tempo de vida útil do token. A aplicação nunca deve tentar descodificar um token de acesso API. A utilização do token em cache impede o tráfego desnecessário entre a sua aplicação e a Microsoft Identity. O seu utilizador pode manter-se inscrito na sua aplicação durante a duração da vida útil desse símbolo.

## <a name="properly-handle-service-responses"></a>Lidar adequadamente com as respostas de serviço

Finalmente, embora as aplicações devam lidar com todas as respostas de erro, existem algumas respostas que podem ter impacto na resiliência. Se a sua aplicação receber um código de resposta HTTP 429, Demasiados Pedidos, a Microsoft Identity está a estrangular os seus pedidos. Se a sua aplicação continuar a fazer muitos pedidos, continuará a ser estrangulada impedindo que a sua app receba fichas. A sua aplicação não deve tentar adquirir um token novamente até que, em segundos, no campo de resposta "Retry-After" tenha passado. Receber uma resposta 429 é muitas vezes uma indicação de que a aplicação não está a caching e a reutilizar tokens corretamente. Os desenvolvedores devem rever como os tokens são em cache e reutilizados na aplicação.

Quando uma aplicação recebe um código de resposta HTTP 5xx, a aplicação não deve introduzir um ciclo de repetição rápida. Quando presente, o pedido deve honrar o mesmo manuseamento "Retry-After" como faz para uma resposta 429. Se não for fornecido um cabeçalho "Retry-After" pela resposta, recomendamos a implementação de uma relemada exponencial com a primeira repetição pelo menos 5 segundos após a resposta.

Quando um pedido de pedidos não deve voltar a tentar imediatamente. Implemente uma retração exponencial com a primeira repetição pelo menos 5 segundos após a resposta.

## <a name="next-steps"></a>Passos seguintes

- [Criar resiliência em aplicações que inscrevam utilizadores](resilience-client-app.md)
- [Construa resiliência na sua infraestrutura de gestão de identidade e acessos](resilience-in-infrastructure.md)
- [Construa resiliência nos seus sistemas CIAM](resilience-b2c.md)