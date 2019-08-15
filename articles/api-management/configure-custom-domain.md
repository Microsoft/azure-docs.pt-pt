---
title: Configurar um nome de domínio personalizado para sua instância do gerenciamento de API do Azure | Microsoft Docs
description: Este tópico descreve como configurar um nome de domínio personalizado para sua instância do gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/12/2019
ms.author: apimpm
ms.openlocfilehash: 45e1ad6bd757ec5acaf784c94e4cfb5e487ce9ba
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68975743"
---
# <a name="configure-a-custom-domain-name"></a>Configurar um nome de domínio personalizado

Quando você cria uma instância de serviço de gerenciamento de API do Azure, o Azure atribui um subdomínio de azure-api.net (por `apim-service-name.azure-api.net`exemplo,). No entanto, você pode expor seus pontos de extremidade de gerenciamento de API usando seu próprio nome de domínio personalizado, como **contoso.com**. Este tutorial mostra como mapear um nome DNS personalizado existente para pontos de extremidade expostos por uma instância de gerenciamento de API.

> [!WARNING]
> Os clientes que desejam usar a fixação de certificado para melhorar a segurança de seus aplicativos devem usar um nome de domínio personalizado > e um certificado que eles gerenciam, não o certificado padrão. Os clientes que fixarem o certificado padrão serão > tomando uma dependência rígida das propriedades do certificado que eles não controlam, o que não é uma prática recomendada.

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas descritas neste artigo, você deve ter:

-   Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Uma instância de gerenciamento de API. Para obter mais informações, consulte [criar uma instância de gerenciamento de API do Azure](get-started-create-service-instance.md).
-   Um nome de domínio personalizado que pertence a você ou à sua organização. Este tópico não fornece instruções sobre como adquirir um nome de domínio personalizado.
-   Um registro CNAME hospedado em um servidor DNS que mapeia o nome de domínio personalizado para o nome de domínio padrão da sua instância de gerenciamento de API. Este tópico não fornece instruções sobre como hospedar um registro CNAME.
-   Você deve ter um certificado válido com uma chave pública e privada (. PFX). A entidade ou o nome alternativo da entidade (SAN) deve corresponder ao nome de domínio (isso permite que a instância do gerenciamento de API exponha URLs com segurança por SSL).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Usar o portal do Azure para definir um nome de domínio personalizado

1. Navegue até sua instância de gerenciamento de API no [portal do Azure](https://portal.azure.com/).
1. Selecione **domínios personalizados**.

    Há vários pontos de extremidade aos quais você pode atribuir um nome de domínio personalizado. Atualmente, os seguintes pontos de extremidade estão disponíveis:

    - Do **Gateway** (o padrão é `<apim-service-name>.azure-api.net`:),
    - **Portal** do (o padrão é `<apim-service-name>.portal.azure-api.net`:),
    - **Gerenciamento** do (o padrão é `<apim-service-name>.management.azure-api.net`:),
    - **SCM** (o padrão é `<apim-service-name>.scm.azure-api.net`:).

    > [!NOTE]
    > Somente o ponto de extremidade do **Gateway** está disponível para configuração na camada de consumo.
    > Você pode atualizar todos os pontos de extremidade ou alguns deles. Normalmente, os clientes atualizam o **Gateway** (essa URL é usada para chamar a API exposta por meio do gerenciamento de API) e do **portal** (a URL do portal do desenvolvedor).
    > Os pontos de extremidade de **Gerenciamento** e **SCM** são usados internamente apenas pelos proprietários da instância do gerenciamento de API e, portanto, são atribuídos com menos frequência um nome de domínio personalizado.
    > A camada **Premium** dá suporte à configuração de vários nomes de host para o ponto de extremidade do **Gateway** .

1. Selecione o ponto de extremidade que você deseja atualizar.
1. Na janela à direita, clique em **personalizado**.

    - No **nome de domínio personalizado**, especifique o nome que você deseja usar. Por exemplo, `api.contoso.com`.
    - No **certificado**, selecione um certificado de Key Vault. Você também pode carregar um válido. Arquivo PFX e forneça sua **senha**, se o certificado estiver protegido com uma senha.

    > [!NOTE]
    > Os nomes de domínio curinga, `*.contoso.com` por exemplo, têm suporte em todas as camadas, exceto a camada de consumo.

    > [!TIP]
    > É recomendável usar Azure Key Vault para gerenciar certificados e defini-los para rotação automática.
    > Se você usar Azure Key Vault para gerenciar o certificado SSL de domínio personalizado, verifique se o certificado foi inserido no Key Vault [como um _certificado_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate), não como um _segredo_.
    >
    > Para buscar um certificado SSL, o gerenciamento de API deve ter a lista uma permissão obter segredos no Azure Key Vault que contém o certificado. Ao usar portal do Azure todas as etapas de configuração necessárias serão concluídas automaticamente. Ao usar as ferramentas de linha de comando ou a API de gerenciamento, essas permissões devem ser concedidas manualmente. Isso é feito em duas etapas. Primeiro, use a página identidades gerenciadas na instância de gerenciamento de API para garantir que a identidade gerenciada esteja habilitada e anote a ID da entidade de segurança mostrada nessa página. Em segundo lugar, dê à lista de permissões e obtenha permissões de segredos para essa ID de entidade de segurança no Azure Key Vault que contém o certificado.
    >
    > Se o certificado for definido para rotação automática, o gerenciamento de API selecionará a versão mais recente automaticamente sem nenhum tempo de inatividade para o serviço (se sua camada de gerenciamento de API tiver SLA-i. e. em todas as camadas, exceto a camada de desenvolvedor).

1. Clique em aplicar.

    > [!NOTE]
    > O processo de atribuição do certificado pode levar 15 minutos ou mais, dependendo do tamanho da implantação. A SKU do desenvolvedor tem tempo de inatividade, os SKUs básico e superior não têm tempo de inatividade.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>Configuração do DNS

Ao configurar o DNS para seu nome de domínio personalizado, você tem duas opções:

-   Configure um registro CNAME que aponte para o ponto de extremidade do seu nome de domínio personalizado configurado.
-   Configure um registro A que aponta para seu endereço IP do gateway de gerenciamento de API.

> [!NOTE]
> Embora o endereço IP da instância de gerenciamento de API seja estático, ele pode ser alterado em alguns cenários. Por isso, é recomendável usar CNAME ao configurar o domínio personalizado. Leve isso em consideração ao escolher o método de configuração de DNS. Leia mais nas [perguntas frequentes sobre a API Mananagement](https://docs.microsoft.com/azure/api-management/api-management-faq#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules).

## <a name="next-steps"></a>Passos seguintes

[Atualizar e dimensionar seu serviço](upgrade-and-scale.md)
