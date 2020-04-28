---
title: Integrar nPS com autenticação VPN Gateway RADIUS para MFA
description: Descreve a autenticação de gateway RADIUS azure com servidor NPS para autenticação de vários fatores.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 941b6ac86941824351f83592998e8735e3eb8ee5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75780373"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Integrar a autenticação radius de gateway Azure VPN com servidor NPS para autenticação multi-factor 

O artigo descreve como integrar o Servidor de Política de Rede (NPS) com a autenticação radius de gateway Azure VPN para fornecer a autenticação multi-factor (MFA) para ligações VPN ponto-a-site. 

## <a name="prerequisite"></a>Pré-requisito

Para ativar o MFA, os utilizadores devem estar no Azure Ative Directory (Azure AD), que deve ser sincronizado a partir do ambiente no local ou na nuvem. Além disso, o utilizador já deve ter concluído o processo de inscrição automática para MFA.  Para mais informações, consulte [Configurar a minha conta para verificação em duas etapas](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>Passos detalhados

### <a name="step-1-create-a-virtual-network-gateway"></a>Passo 1: Criar um portal de rede virtual

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Na rede virtual que irá alojar o portal de rede virtual, selecione **Subnets**e, em seguida, selecione **a subnet Gateway** para criar uma sub-rede. 

    ![A imagem sobre como adicionar subnet gateway](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Criar um portal de rede virtual especificando as seguintes definições:

    - **Tipo de gateway**: selecione **VPN**.
    - **Tipo VPN**: Selecione **baseado na rota**.
    - **SKU**: Selecione um tipo SKU com base nos seus requisitos.
    - **Rede virtual**: Selecione a rede virtual na qual criou a subnet gateway.

        ![A imagem sobre as definições de gateway de rede virtual](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>Passo 2 Configure o NPS para O MFA Azure

1. No servidor NPS, [instale a extensão NPS para O MFA Azure](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension).
2. Abra a consola NPS, clique à direita nos **Clientes RADIUS**e, em seguida, selecione **New**. Criar o cliente RADIUS especificando as seguintes definições:

    - **Nome amigável**: Digite qualquer nome.
    - **Endereço (IP ou DNS)**: Digite a sub-rede de gateway que criou no Passo 1.
    - **Segredo partilhado**: digite qualquer chave secreta e lembre-se dela para posterior utilização.

      ![A imagem sobre as definições do cliente RADIUS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  No separador **Advanced,** detete o nome do fornecedor para **RADIUS Standard** e certifique-se de que a caixa de verificação de **Opções Adicionais** não está selecionada.

    ![A imagem sobre as definições avançadas do cliente RADIUS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Vá para **políticas** > **políticas de rede,** ligações de duplo clique para a política do **servidor de encaminhamento do Microsoft e de acesso remoto,** selecione **o acesso**ao Grant e, em seguida, clique em **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Passo 3 Configure o gateway da rede virtual

1. Inicie sessão no [portal Azure.](https://portal.azure.com)
2. Abra o portal de rede virtual que criou. Certifique-se de que o tipo de gateway está definido para **VPN** e que o tipo VPN é **baseado em rota**.
3. Clique em **Apontar para configurar** > o site**Configurar agora**, e depois especificar as seguintes definições:

    - **Conjunto de endereços**: Digite a subnet gateway que criou no passo 1.
    - **Tipo de autenticação:** **Selecione a autenticação RADIUS**.
    - **Endereço IP**do servidor : Digite o endereço IP do servidor NPS.

      ![A imagem sobre o ponto para as definições do site](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Passos seguintes

- [Multi-Factor Authentication do Azure](../active-directory/authentication/multi-factor-authentication.md)
- [Integrar a infraestrutura NPS existente com o Multi-Factor Authentication do Azure](../active-directory/authentication/howto-mfa-nps-extension.md)
