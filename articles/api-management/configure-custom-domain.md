---
title: Configurar o nome de domínio personalizado para a azure API Management instance
titleSuffix: Azure API Management
description: Este tópico descreve como configurar um nome de domínio personalizado para o seu exemplo de Gestão API Azure.
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
ms.openlocfilehash: 53442f0727544221f28bfdb58d8069163eb374e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87024865"
---
# <a name="configure-a-custom-domain-name-for-your-azure-api-management-instance"></a>Configure um nome de domínio personalizado para a sua instância de Gestão API Azure

Quando cria uma instância de serviço de gestão API Azure, a Azure atribui-lhe um subdomínio de `azure-api.net` (por exemplo, `apim-service-name.azure-api.net` ). No entanto, pode expor os seus pontos finais da API Management utilizando o seu próprio nome de domínio personalizado, como **contoso.com**. Este tutorial mostra-lhe como mapear um nome DNS personalizado existente para pontos finais expostos por uma instância de Gestão da API.

> [!IMPORTANT]
> A API Management aceita apenas pedidos com valores [de cabeçalho de anfitrião](https://tools.ietf.org/html/rfc2616#section-14.23) correspondentes ao nome de domínio predefinido ou a qualquer um dos nomes de domínio personalizado configurados.

> [!WARNING]
> Os clientes que desejem utilizar a fixação de certificados para melhorar a segurança das suas aplicações devem utilizar um nome de domínio personalizado e um certificado que gerem, e não o certificado por defeito. Em vez disso, os clientes que fixarem o certificado por defeito terão uma forte dependência das propriedades do certificado que não controlam, o que não é uma prática recomendada.

## <a name="prerequisites"></a>Pré-requisitos

Para executar os passos descritos neste artigo, deve ter:

-   Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Um caso de Gestão da API. Para obter mais informações, consulte [Criar uma instância de Gestão API Azure.](get-started-create-service-instance.md)
-   Um nome de domínio personalizado que é propriedade de si ou da sua organização. Este tópico não fornece instruções sobre como obter um nome de domínio personalizado.
-   Um registo CNAME alojado num servidor DNS que mapeia o nome de domínio personalizado para o nome de domínio padrão da sua instância de Gestão de API. Este tópico não fornece instruções sobre como hospedar um registo CNAME.
-   Deve ter um certificado válido com uma chave pública e privada (. PFX). O nome alternativo do sujeito ou sujeito (SAN) tem de corresponder ao nome de domínio (isto permite que a instância de Gestão da API exponha os URLs de forma segura sobre TLS).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Use o portal Azure para definir um nome de domínio personalizado

1. Navegue para a sua instância de Gestão da API no [portal Azure.](https://portal.azure.com/)
1. Selecione **domínios personalizados**.

    Existem vários pontos finais aos quais pode atribuir um nome de domínio personalizado. Atualmente, os seguintes pontos finais estão disponíveis:

    - **Gateway** (padrão é: `<apim-service-name>.azure-api.net` ),
    - **Portal** (o padrão é: `<apim-service-name>.portal.azure-api.net` ),
    - **Gestão** (o padrão é: `<apim-service-name>.management.azure-api.net` ),
    - **SCM** (o padrão é: `<apim-service-name>.scm.azure-api.net` ),
    - **NewPortal** (o padrão é: `<apim-service-name>.developer.azure-api.net` ).

    > [!NOTE]
    > Apenas o ponto final **gateway** está disponível para configuração no nível de Consumo.
    > Pode atualizar todos os pontos finais ou alguns deles. Geralmente, os clientes atualizam **gateway** (este URL é usado para chamar a API exposta através da API Management) e **Portal** (o URL do portal do desenvolvedor).
    > Os pontos finais **de gestão** e **SCM** são utilizados internamente apenas pelos proprietários de instâncias de gestão da API e, portanto, são menos frequentemente atribuídos um nome de domínio personalizado.
    > O nível **Premium** suporta a definição de vários nomes de anfitriões para o ponto final **gateway.**

1. Selecione o ponto final que pretende atualizar.
1. Na janela à direita, clique em **Custom.**

    - No **nome de domínio personalizado,** especifique o nome que pretende utilizar. Por exemplo, `api.contoso.com`.
    - No **Certificado,** selecione um certificado do Key Vault. Também pode fazer o upload de um válido . Ficheiro PFX e fornecer a sua **Palavra-passe,** se o certificado estiver protegido com uma senha.

    > [!NOTE]
    > Os nomes de domínio wildcard, por exemplo, `*.contoso.com` são suportados em todos os níveis, exceto no nível de Consumo.

    > [!TIP]
    > Recomendamos a utilização [do Cofre de Chaves Azure para gerir certificados](../key-vault/certificates/about-certificates.md) e defini-los para a auto-autorização.
    > Se utilizar o Cofre da Chave Azure para gerir o certificado TLS/SSL de domínio personalizado, certifique-se de que o certificado é inserido no Cofre-Chave [como _certificado_](/rest/api/keyvault/createcertificate/createcertificate), não um _segredo_.
    >
    > Para obter um certificado TLS/SSL, a API Management deve ter a lista e obter permissões secretas no Cofre chave Azure contendo o certificado. Ao utilizar o portal Azure, todos os passos de configuração necessários serão concluídos automaticamente. Ao utilizar ferramentas de linha de comando ou a API de gestão, estas permissões devem ser concedidas manualmente. Isto é feito em dois passos. Em primeiro lugar, utilize a página de identidades geridas na sua instância de Gestão da API para se certificar de que a Identidade Gerida está ativada e tomar nota do id principal mostrado nessa página. Segundo, dê uma lista de permissões e obtenha permissões secretas para este id principal no Cofre da Chave Azure contendo o certificado.
    >
    > Se o certificado for definido como novo, a API Management irá recolher automaticamente a versão mais recente sem qualquer tempo de inatividade para o serviço (se o seu nível de Gestão de API tiver SLA - ou seja, em todos os níveis, exceto no nível de Desenvolvedor).

1. Clique em Aplicar.

    > [!NOTE]
    > O processo de atribuição do certificado pode demorar 15 minutos ou mais, dependendo do tamanho da implantação. O Desenvolvedor SKU tem tempo de inatividade, os SKUs básicos e superiores não têm tempo de inatividade.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>Configuração do DNS

Ao configurar o DNS para o seu nome de domínio personalizado, tem duas opções:

-   Configurar um registo CNAME que aponta para o ponto final do seu nome de domínio personalizado configurado.
-   Configure um registo A que aponta para o seu endereço IP de gateway de gateway de gestão API.

> [!NOTE]
> Embora o endereço IP de caso de managamento da API seja estático, pode mudar em alguns cenários. Por isso é recomendado usar CNAME ao configurar o domínio personalizado. Tenha isso em consideração ao escolher o método de configuração dns. Leia mais no [artigo de documentação IP](api-management-howto-ip-addresses.md#changes-to-the-ip-addresses) e na FAQ de Gestão da [API.](api-management-faq.md#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)

## <a name="next-steps"></a>Passos seguintes

[Atualizar e escalar o seu serviço](upgrade-and-scale.md)
