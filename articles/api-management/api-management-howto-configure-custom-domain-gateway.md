---
title: Configure um nome de domínio personalizado para o seu gateway de gestão de API Azure auto-hospedado [ Microsoft Docs
description: Este tópico descreve os passos para configurar um nome de domínio personalizado para gateway de gestão de API Azure auto-hospedado.
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
ms.openlocfilehash: dacf1329d35117c65bcc48a82ac27a767ebd2b3b
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203189"
---
# <a name="configure-a-custom-domain-name"></a>Configurar um nome de domínio personalizado

Quando você fornecer um gateway de [gestão Azure API auto-hospedado](self-hosted-gateway-overview.md) não é atribuído o nome de anfitrião e tem que ser referenciado pelo seu endereço IP. Este artigo mostra como mapear um nome dNS personalizado existente (também referido como nome de anfitrião) um gateway auto-hospedado.

## <a name="prerequisites"></a>Pré-requisitos

Para realizar os passos descritos neste artigo, deve ter:

-   Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Uma instância de Gestão API. Para mais informações, consulte Criar uma instância de [Gestão API Azure.](get-started-create-service-instance.md)
- Um portal auto-hospedado. Para mais informações, consulte [Como fornecer gateway auto-hospedado](api-management-howto-provision-self-hosted-gateway.md)
-   Um nome de domínio personalizado que é propriedade de si ou da sua organização. Este tópico não fornece instruções sobre como obter um nome de domínio personalizado.
-   Um disco DNS hospedado num servidor DNS que mapeia o nome de domínio personalizado para o endereço IP do gateway auto-hospedado. Este tópico não fornece instruções sobre como hospedar um registo DNS.
-   Deve ter um certificado válido com chave pública e privada (. PFX). O nome alternativo sujeito ou sujeito (SAN) tem de corresponder ao nome de domínio (isto permite que a instância de Gestão API exponha os URLs de forma segura em TLS).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>Adicione certificado de domínio personalizado ao seu serviço de Gestão API

1. Selecione **Certificados** a partir de âmbito de **segurança**.
2. Selecione **+ Adicionar**.
3. Introduza um nome de recurso para o certificado no campo **de identificação.**
4. Selecione o ficheiro que contém o certificado (. PFX) selecionando o campo **Certificado** ou o ícone da pasta adjacente.
5. Introduza a palavra-passe do certificado no campo **Password.**
6. Selecione **Criar** para adicionar o certificado ao seu serviço de Gestão API.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Use o portal Azure para definir um nome de domínio personalizado para o seu gateway auto-hospedado

1. Selecione os **Gateways** a partir de **definições**.
2. Selecione o gateway auto-hospedado para o que pretende configurar o nome de domínio.
3. Selecione **nomes de anfitriões** em **Definições**.
4. Selecione **+ Adicionar**
5. Introduza o nome de recurso para o nome de anfitrião no campo **Nome.**
6. Introduza o nome de domínio no campo **Hostname.**
7. Selecione um certificado do **certificado** de entrega.
8. Selecione Negociar caixa **de verificação** de certificado de cliente se alguma das APIs expostas através deste gateway usar autenticação de certificado de cliente.
    > [!WARNING]
    > Esta definição é partilhada por todos os nomes de domínio configurados para o gateway.
9. **Selecione Adicionar** para atribuir o nome de domínio personalizado ao gateway auto-hospedado selecionado.

## <a name="next-steps"></a>Passos seguintes

[Atualizar e escalar o seu serviço](upgrade-and-scale.md)
