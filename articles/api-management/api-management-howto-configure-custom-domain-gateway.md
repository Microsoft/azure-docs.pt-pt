---
title: Configure um nome de domínio personalizado para a sua porta de entrada de gestão API Azure auto-hospedada | Microsoft Docs
description: Este tópico descreve os passos para configurar um nome de domínio personalizado para o gateway de gestão API AZure auto-hospedado.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: d52bf87b74ae9b1770ed5092738fd05eb9f54fde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99491035"
---
# <a name="configure-a-custom-domain-name-for-a-self-hosted-gateway"></a>Configure um nome de domínio personalizado para um gateway auto-hospedado

Quando disponibiliza um [gateway de Gestão API Azure auto-hospedado,](self-hosted-gateway-overview.md)não lhe é atribuído um nome de anfitrião e tem de ser referenciado pelo seu endereço IP. Este artigo mostra como mapear um nome DNS personalizado existente (também referido como nome de hospedeiro) para um gateway auto-hospedado.

## <a name="prerequisites"></a>Pré-requisitos

Para executar os passos descritos neste artigo, deve ter:

-   Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Um caso de Gestão da API. Para obter mais informações, consulte [Criar uma instância de Gestão API Azure.](get-started-create-service-instance.md)
- Um portal auto-hospedado. Para mais informações, consulte [Como providenciar gateway auto-hospedado](api-management-howto-provision-self-hosted-gateway.md)
-   Um nome de domínio personalizado que é propriedade de si ou da sua organização. Este tópico não fornece instruções sobre como obter um nome de domínio personalizado.
-   Um registo DNS alojado num servidor DNS que mapeia o nome de domínio personalizado para o endereço IP do gateway auto-hospedado. Este tópico não fornece instruções sobre como hospedar um registo DNS.
-   Deve ter um certificado válido com uma chave pública e privada (. PFX). O nome alternativo do sujeito ou sujeito (SAN) tem de corresponder ao nome de domínio (isto permite que a instância de Gestão da API exponha os URLs de forma segura sobre TLS).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>Adicione certificado de domínio personalizado ao seu serviço de Gestão API

Adicione um certificado de domínio personalizado (. PFX) arquiva na sua instância de Gestão API ou referencia um certificado armazenado no Cofre da Chave Azure. Siga as etapas nos [serviços de backend Secure utilizando a autenticação de certificado de cliente na Azure API Management.](api-management-howto-mutual-certificates.md)

> [!NOTE]
> Recomendamos a utilização de um certificado de cofre chave para o domínio de gateway auto-hospedado.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Use o portal Azure para definir um nome de domínio personalizado para o seu gateway auto-hospedado

1. Selecione os **Gateways** a partir de **implementação e infraestrutura**.
2. Selecione o gateway auto-hospedado para configurar o nome de domínio para.
3. Selecione **os hostnames** em **Definições**.
4. Selecione **+ Adicionar**
5. Introduza o nome de recurso para o nome anfitrião no campo **Nome.**
6. Insira o nome de domínio no campo **Hostname.**
7. Selecione um certificado a partir da entrega do **Certificado.**
8. **Selecione Negociar** caixa de verificação de certificado de cliente se algum dos APIs expostos através deste gateway usar autenticação de certificado de cliente.
    > [!WARNING]
    > Esta definição é partilhada por todos os nomes de domínio configurados para o gateway.
9. **Selecione Adicionar** para atribuir o nome de domínio personalizado ao gateway auto-hospedado selecionado.

## <a name="next-steps"></a>Passos seguintes

[Atualizar e escalar o seu serviço](upgrade-and-scale.md)
