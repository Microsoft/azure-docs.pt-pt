---
title: Configure um nome de domínio personalizado para o seu auto-hospedado gateway API Management Azure / Microsoft Docs
description: Este tópico descreve os passos para configurar um nome de domínio personalizado para o gateway de gestão API AZure auto-hospedado.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: 0894203be4867e305c8e15467a2a867b9bfdc727
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86506813"
---
# <a name="configure-a-custom-domain-name"></a>Configurar um nome de domínio personalizado

Quando disponibiliza uma [porta de entrada auto-hospedada da Azure API Management,](self-hosted-gateway-overview.md) não é atribuído o nome de anfitrião e tem de ser referenciado pelo seu endereço IP. Este artigo mostra como mapear um nome DNS personalizado existente (também referido como nome de hospedeiro) um gateway auto-hospedado.

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

1. Selecione **Certificados** de **Segurança.**
2. Selecione **+ Adicionar**.
3. Introduza um nome de recurso para o certificado no campo **ID.**
4. Selecione o ficheiro que contém o certificado (. PFX) selecionando o campo **Certificado** ou o ícone da pasta adjacente ao mesmo.
5. Introduza a palavra-passe do certificado no campo **Palavra-passe.**
6. Selecione **Criar** para adicionar o certificado ao seu serviço de Gestão API.

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
