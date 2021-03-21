---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: fa65a7354112a2b220686372459b348d45832dd9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96467374"
---
Faça os seguintes passos na UI web local do seu dispositivo. Este passo demora cerca de 15 minutos, incluindo o upload do ficheiro de configuração VPN (ou o ficheiro de marca de serviço). 

1. Vá para **configuração > VPN**. Selecione **Configurar**.

    ![Configurar uI local 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-1.png)

2. Na lâmina **VPN configurada:**

    - Ativar **as definições VPN**.
    - Forneça o **segredo partilhado da VPN.** Esta é a chave partilhada que forneceu ao criar o recurso de conexão Azure VPN.
    - Forneça o endereço **IP de gateway VPN.** Este é o endereço IP da rede local Azure.
    - Para **o grupo PFS**, selecione **Nenhum**. 
    - Para **o grupo DH**, selecione **Group2**.
    - Para **o método de integridade do IPsec,** selecione **SHA256**.
    - Para **as constantes de transformação de cifra de IPsec**, selecione **GCMAES256**.
    - Para **as constantes de transformação de autenticação IPsec,** selecione **GCMAES256**.
    - Para **o método de encriptação IKE**, selecione **AES256**.
    - Selecione **Aplicar**.

        ![Configurar uI local 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-2.png)

    Para obter mais informações sobre os algoritmos criptográficos suportados, vá a [Sobre requisitos criptográficos e gateways Azure VPN](../articles/vpn-gateway/vpn-gateway-about-compliance-crypto.md#ipsecike-policy-faq). 

3. Para carregar o ficheiro de configuração da rota VPN, selecione **Upload**. 

    ![Configurar uI local 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-3.png)

    - Navegue para o ficheiro *json* tags de serviço que descarregou no seu sistema local no passo anterior.
    - Selecione a região como a região Azure associada ao dispositivo, rede virtual e gateways.
    - Selecione **Aplicar**.

        ![Configurar uI local 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-4.png)
    
    O upload demora cerca de 7-8 minutos no dispositivo.

4. Para adicionar rotas específicas ao cliente, configurar os intervalos de endereço IP a serem acedidos apenas com VPN. 

    - Nos **intervalos de endereços IP a aceder apenas através de VPN,** selecione **Configure**.
    - Forneça uma gama IPv4 válida e selecione **Adicionar**. Repita os passos para adicionar outros intervalos.
    - Selecione **Aplicar**.

        ![Configurar uI local 5](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-5.png)

