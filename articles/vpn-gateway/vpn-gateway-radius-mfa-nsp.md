---
title: Integrar NPS com autenticação VPN Gateway RADIUS para MFA
description: Descreve a autenticação do GATEWAY Azure RADIUS com servidor NPS para autenticação multi-factor.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: e5456c53ca891a7c88797f9661a6a3b9b9935e08
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660912"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Integre a autenticação do gateway Azure VPN RADIUS com servidor NPS para autenticação multi-factor 

O artigo descreve como integrar o Network Policy Server (NPS) com a autenticação do gateway RADIUS Azure VPN para fornecer autenticação multi-factor (MFA) para ligações VPN ponto a local. 

## <a name="prerequisite"></a>Pré-requisito

Para ativar o MFA, os utilizadores devem estar no Azure Ative Directory (Azure AD), que deve ser sincronizado a partir do ambiente no local ou na nuvem. Além disso, o utilizador já deve ter concluído o processo de inscrição automática para MFA.  Para mais informações, consulte [Configurar a minha conta para verificação em duas etapas](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>Passos detalhados

### <a name="step-1-create-a-virtual-network-gateway"></a>Passo 1: Criar uma porta de entrada de rede virtual

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Na rede virtual que irá acolher o gateway de rede virtual, selecione **Subnets** e, em seguida, selecione **gateway sub-rede** para criar uma sub-rede. 

    ![A imagem sobre como adicionar a sub-rede gateway](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Criar um gateway de rede virtual especificando as seguintes definições:

    - **Tipo de gateway**: selecione **VPN**.
    - **Tipo VPN**: Selecione **Route-based**.
    - **SKU**: Selecione um tipo SKU com base nos seus requisitos.
    - **Rede virtual**: Selecione a rede virtual na qual criou a sub-rede gateway.

        ![A imagem sobre as definições de gateway de rede virtual](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>Passo 2 Configurar o NPS para Azure MFA

1. No servidor NPS, [instale a extensão NPS para Azure MFA](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension).
2. Abra a consola NPS, clique com o botão direito **clientes RADIUS** e, em seguida, selecione **New**. Crie o cliente RADIUS especificando as seguintes definições:

    - **Nome amigável**: Digite qualquer nome.
    - **Endereço (IP ou DNS)**: Digite a sub-rede gateway que criou no Passo 1.
    - **Segredo compartilhado:** digite qualquer chave secreta, e lembre-se para uso posterior.

      ![A imagem sobre as configurações do cliente RADIUS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  No separador **Advanced,** desagure o nome do fornecedor para **RADIUS Standard** e certifique-se de que a caixa de verificação **de Opções Adicionais** não está selecionada.

    ![A imagem sobre as configurações avançadas do cliente RADIUS](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Vá **às**  >  **Políticas de Política de Rede**, clique **duas vezes** nas ligações à política do servidor de encaminhamento e acesso remoto do Microsoft, selecione **o acesso ao Grant** e, em seguida, clique em **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Passo 3 Configurar o portal de rede virtual

1. Inicie sessão no [portal Azure](https://portal.azure.com).
2. Abra o portal de rede virtual que criou. Certifique-se de que o tipo de gateway está definido para **VPN** e que o tipo VPN é **baseado em rotas**.
3. Clique **em Ponto para configuração do site**  >  **Configurar agora** e, em seguida, especificar as seguintes definições:

    - **Conjunto de endereços**: Digite a sub-rede gateway que criou no passo 1.
    - **Tipo de autenticação**: Selecione **a autenticação RADIUS**.
    - **Endereço IP do servidor**: Digite o endereço IP do servidor NPS.

      ![A imagem sobre as definições do ponto para o site](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Próximos passos

- [Multi-Factor Authentication do Azure](../active-directory/authentication/concept-mfa-howitworks.md)
- [Integrar a infraestrutura NPS existente com o Multi-Factor Authentication do Azure](../active-directory/authentication/howto-mfa-nps-extension.md)