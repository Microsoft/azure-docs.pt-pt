---
title: Configurar um nome de domínio personalizado para a instância de gestão de API do Azure | Documentos da Microsoft
description: Este tópico descreve como configurar um nome de domínio personalizado para a sua instância de gestão de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 07/01/2019
ms.author: apimpm
ms.openlocfilehash: 59b44dcc9ec3a1f7c274f426a19aa8ed2258db3e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509308"
---
# <a name="configure-a-custom-domain-name"></a>Configurar um nome de domínio personalizado

Quando cria uma instância de serviço de gestão de API do Azure, Azure atribui-lhe um subdomínio do azure-api.net (por exemplo, `apim-service-name.azure-api.net`). No entanto, pode expor os pontos finais de gestão de API com o seu próprio nome de domínio personalizado, como **contoso.com**. Este tutorial mostra-lhe como mapear um nome DNS existente personalizado para pontos de extremidade expostos por uma instância de gestão de API.

> [!WARNING]
> Os clientes que pretendem utilizar afixação de certificado para melhorar a segurança das suas aplicações tem de utilizar um nome de domínio personalizado > e o certificado que eles gerenciam, não o certificado predefinido. Os clientes que afixar o certificado predefinido em vez disso será > tendo uma dependência nas propriedades do certificado que não controlam, que não é uma prática recomendada.

## <a name="prerequisites"></a>Pré-requisitos

Para efetuar os passos descritos neste artigo, tem de ter:

-   Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Uma instância de gestão de API. Para obter mais informações, consulte [criar uma instância de gestão de API do Azure](get-started-create-service-instance.md).
-   Um nome de domínio personalizado que é detido por si. O nome de domínio personalizado que pretende utilizar, tem de ser obtido em separado e hospedados num servidor DNS. Este tópico não dá instruções sobre como alojar um nome de domínio personalizado.
-   Tem de ter um certificado válido com uma chave pública e privada (. PFX). Requerente ou nome alternativo do requerente (SAN) tem de corresponder ao nome de domínio (isso permite uma instância de gestão de API para expor em segurança URLs através de SSL).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Utilize o portal do Azure para definir um nome de domínio personalizado

1. Navegue até à sua instância da gestão de API no [portal do Azure](https://portal.azure.com/).
1. Selecione **domínios personalizados e SSL**.

    Há uma série de pontos de extremidade ao qual pode atribuir um nome de domínio personalizado. Atualmente, os pontos finais seguintes estão disponíveis:

    - **Proxy** (a predefinição é: `<apim-service-name>.azure-api.net`),
    - **Portal** (a predefinição é: `<apim-service-name>.portal.azure-api.net`),
    - **Gerenciamento** (a predefinição é: `<apim-service-name>.management.azure-api.net`),
    - **SCM** (a predefinição é: `<apim-service-name>.scm.azure-api.net`).

    > [!NOTE]
    > Pode atualizar todos os pontos de extremidade ou alguns deles. Normalmente, os clientes atualizar **Proxy** (este URL é utilizado para chamar a API exposta por meio de gestão de API) e **Portal** (portal do programador URL). **Gerenciamento** e **SCM** pontos de extremidade são usados internamente pela apenas os proprietários de instância de gestão de API e, portanto, com menos frequência recebem um nome de domínio personalizado. Na maioria dos casos, apenas um nome único domínio personalizado pode ser definido para um determinado ponto de extremidade. No entanto, o **Premium** camada de suporte a definição de vários nomes de anfitrião para o **Proxy** ponto final.

1. Selecione o ponto final que pretende atualizar.
1. Na janela à direita, clique em **personalizado**.

    - Na **nome de domínio personalizado**, especifique o nome que pretende utilizar. Por exemplo, `api.contoso.com`. Nomes de domínio de caráter universal (por exemplo, \*. domínio. com) também são suportados.
    - Na **certificado**, selecione um certificado do Key Vault. Também pode carregar um válido. PFX de ficheiros e fornecer sua **palavra-passe**, se o certificado está protegido com uma palavra-passe.

    > [!TIP]
    > Recomendamos que utilize o Azure Key Vault para gerir certificados e defini-los como autorotate.
    > Se utilizar o Azure Key Vault para gerir o certificado SSL de domínio personalizado, certifique-se do certificado é inserido no Key Vault [como uma _certificado_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate), e não um _segredo_.
    >
    > Para obter um certificado SSL, gestão de API tem de ter a lista de permissões de segredos um get no Cofre de chaves do Azure que contém o certificado. Ao utilizar o portal do Azure que serão concluídos automaticamente todos os passos de configuração necessárias. Ao utilizar ferramentas de linha de comandos ou a API de gestão, devem ser manualmente concedidas estas permissões. Isso é feito em duas etapas. Em primeiro lugar, utilize a página de identidades de geridos na sua instância de gestão de API para certificar-se de que a identidade gerida está ativada e tome nota do id principal mostrado nessa página. Em segundo lugar, dar a lista de permissão e obter permissões de segredos para este id de principal no Azure Key Vault que contém o certificado.
    >
    > Se o certificado é definido como autorotate, gestão de API selecionará a versão mais recente automaticamente sem qualquer período de inatividade para o serviço (se o seu escalão de gestão de API tem SLA - i. e. em todas as camadas, exceto o escalão de programador).

1. Clique em aplicar.

    > [!NOTE]
    > O processo de atribuir o certificado pode demorar 15 minutos ou mais, dependendo do tamanho da implementação. Programador SKU tem um período de indisponibilidade, básico e superiores SKU não têm tempo de inatividade.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>Passos Seguintes

[Atualizar e dimensionar o serviço](upgrade-and-scale.md)
