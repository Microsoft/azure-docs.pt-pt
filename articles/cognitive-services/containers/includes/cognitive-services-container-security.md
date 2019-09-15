---
title: Segurança do contentor
titleSuffix: Azure Cognitive Services
description: Saiba como proteger seu contêiner
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: dapine
ms.openlocfilehash: d8d069dddbce6ab6ddb541db460634ad3f6fa067
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70994916"
---
## <a name="azure-cognitive-services-container-security"></a>Segurança do contêiner de serviços cognitivas do Azure

A segurança deve ser um foco principal sempre que você estiver desenvolvendo aplicativos. A importância da segurança é uma métrica para o sucesso. Quando você está arquitetando uma solução de software que inclui contêineres de serviços cognitivas, é vital entender as limitações e os recursos disponíveis para você. Para obter mais informações, consulte [segurança do Azure][az-security].

> [!IMPORTANT]
> Por padrão, não há *nenhuma segurança* na API do contêiner de serviços cognitivas. O motivo disso é que, na maioria das vezes, o contêiner será executado como parte de um pod que é protegido do exterior por uma ponte de rede. No entanto, é possível habilitar a autenticação que funciona de forma idêntica à autenticação usada ao acessar [Serviços cognitivas baseados em nuvem][request-authentication].

O diagrama a seguir ilustra a abordagem padrão e **insegura** :

![Segurança do contentor](../media/container-security.svg)

Como uma abordagem alternativa e *segura* , os consumidores de contêineres de serviços cognitivas poderiam aumentar um contêiner com um componente front-end, mantendo o ponto de extremidade do contêiner privado. Vamos considerar um cenário em que usamos [İSTİO][istio] como um gateway de entrada. O İSTİO dá suporte a autenticação HTTPS/SSL e de certificado de cliente. Nesse cenário, o front-end İSTİO expõe o acesso ao contêiner, apresentando o certificado do cliente que está na lista de permissões antecipadamente com İSTİO.

[Nginx][nginx] é outra opção popular na mesma categoria. İSTİO e Nginx atuam como uma malha de serviço e oferecem recursos adicionais, incluindo coisas como balanceamento de carga, roteamento e controle de taxa.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../../security/fundamentals/overview.md
