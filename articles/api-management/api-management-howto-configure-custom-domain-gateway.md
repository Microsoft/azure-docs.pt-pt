---
title: Configurar um nome de domínio personalizado para o gateway de gerenciamento de API do Azure auto-hospedado | Microsoft Docs
description: Este tópico descreve as etapas para configurar um nome de domínio personalizado para o gateway de gerenciamento de API do Azure de hospedagem interna.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 1df2cce04021c1cd14c356311df921dd1c0298e4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513812"
---
# <a name="configure-a-custom-domain-name"></a>Configurar um nome de domínio personalizado

Quando você provisiona um [Gateway de gerenciamento de API do Azure autohospedado](self-hosted-gateway-overview.md) , ele não recebe o nome do host e precisa ser referenciado por seu endereço IP. Este artigo mostra como mapear um nome DNS personalizado existente (também conhecido como hostname) de um gateway auto-hospedado.

> [!NOTE]
> O recurso de gateway auto-hospedado está em versão prévia. Durante a visualização, o gateway auto-hospedado está disponível apenas nas camadas desenvolvedor e Premium sem custo adicional. A camada de desenvolvedor é limitada a uma única implantação de gateway de hospedagem interna.

## <a name="prerequisites"></a>Pré-requisitos

Para executar as etapas descritas neste artigo, você deve ter:

-   Uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Uma instância de gerenciamento de API. Para obter mais informações, consulte [criar uma instância de gerenciamento de API do Azure](get-started-create-service-instance.md).
- Um gateway auto-hospedado. Para obter mais informações, consulte [como provisionar o gateway](api-management-howto-provision-self-hosted-gateway.md) de hospedagem interna
-   Um nome de domínio personalizado que pertence a você ou à sua organização. Este tópico não fornece instruções sobre como adquirir um nome de domínio personalizado.
-   Um registro DNS hospedado em um servidor DNS que mapeia o nome de domínio personalizado para o endereço IP do gateway de hospedagem interna. Este tópico não fornece instruções sobre como hospedar um registro DNS.
-   Você deve ter um certificado válido com uma chave pública e privada (. PFX). A entidade ou o nome alternativo da entidade (SAN) deve corresponder ao nome de domínio (isso permite que a instância do gerenciamento de API exponha URLs com segurança por SSL).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>Adicionar um certificado de domínio personalizado ao serviço de gerenciamento de API

1. Selecione **certificados** em **segurança**.
2. Selecione **+ Adicionar**.
3. Insira um nome de recurso para o certificado no campo **ID** .
4. Selecione o arquivo que contém o certificado (. PFX) selecionando o campo **certificado** ou o ícone de pasta adjacente a ele.
5. Insira a senha do certificado no campo **senha** .
6. Selecione **criar** para adicionar o certificado ao serviço de gerenciamento de API.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Use o portal do Azure para definir um nome de domínio personalizado para o gateway auto-hospedado

1. Selecione os **gateways** em **configurações**.
2. Selecione o gateway auto-hospedado para o qual você deseja configurar o nome de domínio.
3. Selecione **nomes de host** em **configurações**.
4. Selecione **+ Adicionar**
5. Insira o nome do recurso para o nome do host no campo **Name** .
6. Insira o nome de domínio no campo **hostname** .
7. Selecione um certificado na lista suspensa **certificado** .
8. Marque a caixa de seleção **negociar certificado de cliente** se qualquer uma das APIs expostas por esse gateway usar autenticação de certificado de cliente.
    > [!WARNING]
    > Essa configuração é compartilhada por todos os nomes de domínio configurados para o gateway.
9. Selecione **Adicionar** para atribuir o nome de domínio personalizado para o gateway auto-hospedado selecionado.

## <a name="next-steps"></a>Passos seguintes

[Atualizar e dimensionar seu serviço](upgrade-and-scale.md)
