---
title: VPN do Azure gateway RADIUS autenticação segura com o servidor NPS para multi-factor Authentication | Documentos da Microsoft
description: Descreve integrar a autenticação de RADIUS do gateway do Azure com o servidor NPS para multi-factor Authentication.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: willchen
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 4e11b1bc16f874f892288f9677a71023f483de7c
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769979"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Integrar a autenticação de RADIUS do gateway de VPN do Azure com o servidor NPS para multi-factor Authentication 

O artigo descreve como integrar o servidor de políticas de rede (NPS) com a autenticação RADIUS de gateway de VPN do Azure para entregar o multi-factor Authentication (MFA) para ligações de VPN ponto a site. 

## <a name="prerequisite"></a>Pré-requisito

Para ativar a MFA, os utilizadores tem de ser no Azure Active Directory (Azure AD), que têm de ser sincronizados a partir de qualquer local ou ambiente de nuvem. Além disso, o utilizador tem de ter já concluído o processo de inscrição automática para a MFA.  Para obter mais informações, consulte [configurar a minha conta para verificação de dois passos](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>Passos detalhados

### <a name="step-1-create-a-virtual-network-gateway"></a>Passo 1: Criar um gateway de rede virtual

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Na rede virtual que irá alojar o gateway de rede virtual, selecione **sub-redes**e, em seguida, selecione **sub-rede do Gateway** para criar uma sub-rede. 

    ![A imagem sobre como adicionar a sub-rede do gateway](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Crie um gateway de rede virtual ao especificar as seguintes definições:

    - **Tipo de gateway**: Selecione **VPN**.
    - **Tipo de VPN**: Selecione **baseado na rota**.
    - **SKU**: Selecione um tipo SKU com base nos seus requisitos.
    - **Rede virtual**: Selecione a rede virtual em que criou a sub-rede do gateway.

        ![A imagem sobre as definições do gateway de rede virtual](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>Passo 2 configurar o NPS da MFA do Azure

1. No servidor NPS, [instalar a extensão NPS da MFA do Azure](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension).
2. Abra a consola do NSP, com o botão direito **clientes RADUIS**e, em seguida, selecione **New**. Crie o cliente RADUIS ao especificar as seguintes definições:

    - **Nome amigável**: Escreva um nome.
    - **Endereço (IP ou DNS)**: Escreva a sub-rede do gateway que criou no passo 1.
    - **Segredo partilhado**: escreva qualquer chave secreta e não se esqueça dela para utilização posterior.

      ![A imagem sobre definições de cliente RADUIS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  Na **avançadas** separador, defina o nome do fornecedor para **RADIUS padrão** e certifique-se de que o **opções adicionais** não está selecionada a caixa de verificação.

    ![A imagem sobre as definições avançadas do RADUIS cliente](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Aceda a **políticas** > **políticas de rede**, faça duplo clique em **ligações ao servidor de Microsoft Routing e acesso remoto** política, selecione  **Conceder acesso**e, em seguida, clique em **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Passo 3 de configurar o gateway de rede virtual

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Abra o gateway de rede virtual que criou. Certifique-se de que o tipo de gateway está definido como **VPN** e o tipo VPN está **baseado na rota**.
3. Clique em **apontar para a configuração do site** > **configurar agora**e, em seguida, especifique as seguintes definições:

    - **Conjunto de endereços**: Escreva a sub-rede do gateway que criou no passo 1.
    - **Tipo de autenticação**: Selecione **autenticação RADIUS**.
    - **Endereço IP do servidor**: Escreva o endereço IP do servidor NPS.

      ![A imagem sobre ponto para as definições do site](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Passos Seguintes

- [Autenticação Multifator do Azure](../active-directory/authentication/multi-factor-authentication.md)
- [Integrar a infraestrutura NPS existente com o Multi-Factor Authentication do Azure](../active-directory/authentication/howto-mfa-nps-extension.md)
