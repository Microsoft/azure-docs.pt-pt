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
ms.date: 12/14/2017
ms.author: apimpm
ms.openlocfilehash: f613995dbdd787d0a031cb2c24d67c682b2d7cec
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446382"
---
# <a name="configure-a-custom-domain-name"></a>Configurar um nome de domínio personalizado 

Quando cria uma instância de gestão de API (APIM), o Azure atribui-la para um subdomínio do azure-api.net (por exemplo, `apim-service-name.azure-api.net`). No entanto, pode expor os pontos de extremidade APIM com seu próprio nome de domínio, como **contoso.com**. Este tutorial mostra-lhe como mapear um nome DNS existente personalizado para pontos de extremidade expostos por uma instância de gestão de API do Azure.

> [!WARNING]
> Os clientes que pretendem utilizar afixação de certificado para melhorar a segurança das suas aplicações tem de utilizar um nome de domínio personalizado > e o certificado que eles gerenciam, não o certificado predefinido. Os clientes que afixar o certificado predefinido em vez disso será > tendo uma dependência nas propriedades do certificado que não controlam, que não é uma prática recomendada.

## <a name="prerequisites"></a>Pré-requisitos

Para efetuar os passos descritos neste artigo, tem de ter:

+ Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Uma instância APIM. Para obter mais informações, consulte [criar uma instância de gestão de API do Azure](get-started-create-service-instance.md).
+ Um nome de domínio personalizado que é detido por si. O nome de domínio personalizado que pretende utilizar, tem de ser obtido em separado e hospedados num servidor DNS. Este tópico não dá instruções sobre como alojar um nome de domínio personalizado.
+ Tem de ter um certificado válido com uma chave pública e privada (. PFX). Requerente ou nome alternativo do requerente (SAN) tem de corresponder ao nome de domínio (isso habilita o APIM para expor em segurança URLs através de SSL).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Utilize o portal do Azure para definir um nome de domínio personalizado

1. Navegue para a instância APIM no [portal do Azure](https://portal.azure.com/).
2. Selecione **domínios personalizados e SSL**.
    
    Existe um número de pontos de extremidade ao qual pode atribuir um nome de domínio personalizado. Atualmente, os pontos finais seguintes estão disponíveis: 
    + **Proxy** (a predefinição é: `<apim-service-name>.azure-api.net`), 
    + **Portal** (a predefinição é: `<apim-service-name>.portal.azure-api.net`),     
    + **Gerenciamento** (a predefinição é: `<apim-service-name>.management.azure-api.net`), 
    + **SCM** (a predefinição é: `<apim-service-name>.scm.azure-api.net`).

    >[!NOTE]
    > Pode atualizar todos os pontos de extremidade ou alguns deles. Normalmente, os clientes atualizar **Proxy** (este URL é utilizado para chamar a API exposta por meio de gestão de API) e **Portal** (portal do programador URL). **Gerenciamento** e **SCM** pontos de extremidade são usados internamente por clientes APIM e, portanto, com menos frequência recebem um nome de domínio personalizado.
3. Selecione o ponto final que pretende atualizar. 
4. Na janela à direita, clique em **personalizado**.

    + Na **nome de domínio personalizado**, especifique o nome que pretende utilizar. Por exemplo, `api.contoso.com`. <br/>Nomes de domínio de caráter universal (por exemplo, *. domínio. com) também são suportados.
    + Na **certificado**, especifique um válido. Ficheiro PFX que pretende carregar. 
    + Se o certificado tem uma palavra-passe, introduza-a na **palavra-passe** campo.
1. Clique em aplicar.

    >[!NOTE]
    >O processo de atribuir o certificado pode demorar 15 minutos ou mais, dependendo do tamanho da implementação. Programador SKU tem um período de indisponibilidade, básico e superiores SKU não têm tempo de inatividade.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>Passos Seguintes

[Atualizar e dimensionar o serviço](upgrade-and-scale.md)
