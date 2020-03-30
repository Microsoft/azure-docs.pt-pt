---
title: Configure o nome de domínio personalizado para a instância de Gestão API azure
titleSuffix: Azure API Management
description: Este tópico descreve como configurar um nome de domínio personalizado para a sua instância de Gestão API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 4587909ad6fca6cdf21d54d11d89f797bbb29833
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335841"
---
# <a name="configure-a-custom-domain-name"></a>Configurar um nome de domínio personalizado

Quando se cria uma instância de serviço de Gestão API `azure-api.net` Azure, `apim-service-name.azure-api.net`o Azure atribui-lhe um subdomínio de (por exemplo, ). No entanto, pode expor os seus pontos finais de Gestão API utilizando o seu próprio nome de domínio personalizado, como **contoso.com**. Este tutorial mostra-lhe como mapear um nome DNS personalizado existente para pontos finais expostos por uma instância de Gestão API.

> [!IMPORTANT]
> A API Management aceita apenas pedidos com valores de cabeçalho do [anfitrião](https://tools.ietf.org/html/rfc2616#section-14.23) que correspondam ao nome de domínio padrão ou qualquer um dos nomes de domínio personalizado configurados.

> [!WARNING]
> Os clientes que desejem utilizar o certificado para melhorar a segurança das suas aplicações devem utilizar um nome de domínio e certificado personalizados que gerem, e não o certificado predefinido. Os clientes que fixarem o certificado predefinido estarão, em vez disso, a assumir uma forte dependência das propriedades do certificado que não controlam, o que não é uma prática recomendada.

## <a name="prerequisites"></a>Pré-requisitos

Para realizar os passos descritos neste artigo, deve ter:

-   Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Uma instância de Gestão API. Para mais informações, consulte Criar uma instância de [Gestão API Azure.](get-started-create-service-instance.md)
-   Um nome de domínio personalizado que é propriedade de si ou da sua organização. Este tópico não fornece instruções sobre como obter um nome de domínio personalizado.
-   Um disco CNAME hospedado num servidor DNS que mapeia o nome de domínio personalizado para o nome de domínio padrão da sua instância de Gestão API. Este tópico não fornece instruções sobre como hospedar um registo CNAME.
-   Deve ter um certificado válido com chave pública e privada (. PFX). O nome alternativo sujeito ou sujeito (SAN) tem de corresponder ao nome de domínio (isto permite que a instância de Gestão API exponha os URLs de forma segura em TLS).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Use o portal Azure para definir um nome de domínio personalizado

1. Navegue para a sua instância de Gestão API no [portal Azure.](https://portal.azure.com/)
1. Selecione **domínios Personalizados**.

    Existem vários pontos finais para os quais pode atribuir um nome de domínio personalizado. Atualmente, estão disponíveis os seguintes pontos finais:

    - **Gateway** (o `<apim-service-name>.azure-api.net`padrão é: ),
    - **Portal** (padrão `<apim-service-name>.portal.azure-api.net`é: ),
    - **Gestão** (o `<apim-service-name>.management.azure-api.net`padrão é: ),
    - **SCM** (padrão `<apim-service-name>.scm.azure-api.net`é: ),
    - **NewPortal** (padrão `<apim-service-name>.developer.azure-api.net`é: ).

    > [!NOTE]
    > Apenas o ponto final **do Gateway** está disponível para configuração no nível de Consumo.
    > Pode atualizar todos os pontos finais ou alguns deles. Normalmente, os clientes atualizam **gateway** (este URL é usado para chamar a API exposta através da API Management) e **Portal** (o URL portal do desenvolvedor).
    > Os pontos finais **de gestão** e **SCM** são utilizados internamente pelos proprietários da instância de Gestão API e, portanto, são atribuídos com menos frequência um nome de domínio personalizado.
    > O nível **Premium** suporta a definição de vários nomes de anfitriões para o ponto final do **Gateway.**

1. Selecione o ponto final que pretende atualizar.
1. Na janela à direita, clique em **Costume.**

    - No nome de **domínio Personalizado,** especifique o nome que pretende utilizar. Por exemplo, `api.contoso.com`.
    - No **Certificado,** selecione um certificado da Key Vault. Também pode fazer o upload de um válido . Ficheiro PFX e forneça a sua **Palavra-passe,** se o certificado estiver protegido com uma palavra-passe.

    > [!NOTE]
    > Os nomes de domínio `*.contoso.com` wildcard, por exemplo, são suportados em todos os níveis, exceto no nível de consumo.

    > [!TIP]
    > Recomendamos a utilização do Cofre de Chaves Azure para gerir certificados e defini-los para autorotate.
    > Se utilizar o Cofre de Chaves Azure para gerir o certificado de domínio personalizado TLS/SSL, certifique-se de que o certificado está inserido no Cofre chave [como _certificado_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate), e não como _segredo_.
    >
    > Para obter um certificado TLS/SSL, a API Management deve ter a lista e obter permissões de segredos no Cofre de Chaves Azure contendo o certificado. Ao utilizar o portal Azure, todos os passos de configuração necessários serão concluídos automaticamente. Ao utilizar ferramentas de linha de comando ou API de gestão, estas permissões devem ser concedidas manualmente. Isto é feito em dois passos. Em primeiro lugar, utilize a página de identidades geridas na sua instância de Gestão API para se certificar de que a Identidade Gerida está ativada e tomar nota do id principal mostrado nessa página. Segundo, dê uma lista de permissões e obtenha permissões de segredos para esta identificação principal no Cofre chave Azure contendo o certificado.
    >
    > Se o certificado estiver definido para autorotate, a API Management irá recolher automaticamente a versão mais recente sem qualquer tempo de inatividade no serviço (se o seu nível de Gestão API tiver SLA - ou seja, em todos os níveis, exceto o nível de Desenvolvimento).

1. Clique em Aplicar.

    > [!NOTE]
    > O processo de atribuição do certificado pode demorar 15 minutos ou mais dependendo da dimensão da implantação. O desenvolvedor SKU tem tempo de inatividade, As SKUs básicas e mais altas não têm tempo de inatividade.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>Configuração do DNS

Ao configurar dNS para o seu nome de domínio personalizado, tem duas opções:

-   Configure um registo CNAME que aponta para o ponto final do seu nome de domínio personalizado configurado.
-   Configure um registo A que aponte para o seu endereço IP de gateway de gestão aPI.

> [!NOTE]
> Embora o endereço IP da instância de Managment da API seja estático, pode mudar em alguns cenários. Por isso, recomenda-se a utilização de CNAME ao configurar o domínio personalizado. Tome isso em consideração ao escolher o método de configuração DNS. Leia mais no [artigo de documentação IP](api-management-howto-ip-addresses.md#changes-to-the-ip-addresses) e na [API Management FAQ](api-management-faq.md#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services).

## <a name="next-steps"></a>Passos seguintes

[Atualizar e escalar o seu serviço](upgrade-and-scale.md)
