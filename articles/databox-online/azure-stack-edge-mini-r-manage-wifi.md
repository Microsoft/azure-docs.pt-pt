---
title: Gestão de Wi-Fi Azure Stack Edge
description: Descreve como usar o portal Azure para gerir Wi-Fi no seu Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/28/2020
ms.author: alkohli
ms.openlocfilehash: f00be43d023d912d4b0b6e825dfe9d3e0ca2d250
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467823"
---
# <a name="use-the-local-web-ui-to-manage-wireless-connectivity-on-your-azure-stack-edge-mini-r"></a>Utilize o uI web local para gerir a conectividade sem fios no seu Azure Stack Edge Mini R

Este artigo descreve como gerir a conectividade da rede sem fios no seu dispositivo Azure Stack Edge Mini R. Pode utilizar a UI web local no seu dispositivo Azure Stack Edge Mini R através de adicionar, ligar e eliminar perfis Wi-Fi.

## <a name="about-wi-fi"></a>Cerca de Wi-Fi

O seu dispositivo Azure Stack Edge Mini R pode funcionar tanto quando ligado à rede como através de uma rede sem fios. O aparelho tem uma porta Wi-Fi que deve ser ativada para permitir que o dispositivo se conecte a uma rede sem fios. 

O seu dispositivo tem cinco portas, PORT 1 através do PORTO 4 e uma quinta Wi-Fi porta. Aqui está um diagrama do plano traseiro de um dispositivo Mini R quando ligado a uma rede sem fios.

![Cablagem para Wi-Fi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)


## <a name="add-connect-to-wi-fi-profile"></a>Adicione, conecte-se ao Wi-Fi perfil

Faça os seguintes passos na UI local do seu dispositivo para adicionar e ligar a um perfil Wi-Fi.

1. Vá à página **Get start** na UI web local do seu dispositivo. No **azulejo da Rede,** selecione **Configure**.  
    
    No seu dispositivo físico, existem cinco interfaces de rede. A PORTA 1 e a PORTA 2 são interfaces de rede de 1 Gbps. PORT 3 e PORT 4 são todas interfaces de rede de 10 Gbps. O quinto porto é o porto de Wi-Fi. 

    [![Web local UI "Definições de rede" página 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)  
    
    Selecione a porta Wi-Fi e configufique as definições da porta. 
    
    > [!IMPORTANT]
    > Recomendamos vivamente que configuure um endereço IP estático para a porta Wi-Fi.  

    ![Web local UI "Definições de rede" página 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

    A **página 'Rede'** atualiza depois de aplicar as definições de porta Wi-Fi.

    ![Web local UI "Definições de rede" página 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)

   
2. **Selecione Adicionar Wi-Fi perfil** e faça upload do seu perfil de Wi-Fi. 

    ![Web local UI "Port WiFi Network settings" 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)
    
    Um perfil de rede sem fios contém o SSID (nome da rede), a chave de senha e as informações de segurança para poder ligar-se a uma rede sem fios. Pode obter o perfil Wi-Fi para o seu ambiente do seu administrador de rede.

    ![Web local UI "Port WiFi Network settings" 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

    Após a adição do perfil, a lista de Wi-Fi atualizações de perfis para refletir o novo perfil. O perfil deve mostrar o **estado de Ligação** como **Desligado**. 

    ![Web local UI "Port WiFi Network settings" 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)
    
3. Depois de o perfil de rede sem fios ser carregado com sucesso, conecte-se a este perfil. Selecione **Ligar para Wi-Fi perfil**. 

    ![Web local UI "Port Wi-Fi Network settings" 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

4. Selecione o perfil Wi-Fi que adicionou no passo anterior e selecione **Aplicar**. 

    ![Web local UI "Port Wi-Fi Network settings" 5](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

    O **estado de Ligação** deve atualizar-se para **Connected**. A força do sinal atualiza para indicar a qualidade do sinal. 

    ![Web local UI "Port Wi-Fi Network settings" 6](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

    > [!NOTE]
    > Para transferir grandes quantidades de dados, recomendamos que utilize uma ligação com fios em vez da rede sem fios. 


## <a name="download-wi-fi-profile"></a>Baixar Wi-Fi perfil

Pode descarregar um perfil Wi-Fi que está a utilizar para a conectividade da rede sem fios.

1. Na UI web local do seu dispositivo, aceda à **Rede de > de Configuração**. 

2. Sob Wi-Fi definições de perfil, selecione **Perfil de descarregamento**. Isto deve descarregar o perfil Wi-Fi que está a utilizar.


## <a name="delete-wi-fi-profile"></a>Excluir Wi-Fi perfil

Pode eliminar um perfil Wi-Fi que está a utilizar para a conectividade da rede sem fios.


1. Na UI web local do seu dispositivo, aceda à **Rede de > de Configuração**. 

2. Nas definições de perfil Wi-Fi, **selecione Eliminar Wi-Fi perfil**.

3. Na lâmina **de perfil Delete Wi-Fi,** escolha o perfil que pretende eliminar. Selecione **Aplicar**.


## <a name="configure-cisco-wi-fi-profile"></a>Configurar perfil de Wi-Fi cisco

Aqui ficam algumas orientações para como gerir e configurar um controlador sem fios Cisco e um ponto de acesso no seu dispositivo. 

### <a name="dhcp-bridging-mode"></a>Modo de Ponte DHCP

Para utilizar um controlador Cisco Wireless para o seu dispositivo, tem de ativar o modo de ligação dinâmica do protocolo de configuração do anfitrião (DHCP) no controlador LAN sem fios (WLC).

Para obter mais informações, consulte [o Modo de Ponte DHCP](https://www.cisco.com/c/en/us/support/docs/wireless/4400-series-wireless-lan-controllers/110865-dhcp-wlc.html#anc9).

#### <a name="bridging-configuration-example"></a>Exemplo de configuração de ponte

Para ativar a funcionalidade de ponte DHCP no controlador, tem de desativar a função de procuração DHCP no controlador. Para ativar a ponte DHCP utilizando a linha de comando:

```powershell
(Cisco Controller) > config dhcp proxy disable
(Cisco Controller) > show dhcp proxy
DHCP Proxy Behaviour: disabled
```

Se o servidor DHCP não existir na mesma rede da Camada 2 (L2) que o cliente, então a transmissão deve ser encaminhada para o servidor DHCP na porta do cliente utilizando um ajudante IP. Esta é uma amostra desta configuração:

```powershell
Switch#conf t
Switch(config)#interface vlan <client vlan #>
Switch(config-if)#ip helper-address <dhcp server IP>
```

A funcionalidade de ponte DHCP é um cenário global, pelo que afeta todas as transações dhcp dentro do controlador. É necessário adicionar declarações de ajudantes IP na infraestrutura com fios para todas as redes de área virtual necessárias (VLAN) no controlador.

### <a name="enable-the-passive-client-for-wlan"></a>Ativar o cliente passivo para a WLAN

Para ativar a funcionalidade de cliente passivo para a rede local sem fios (WLAN) num controlador Cisco Wireless:

* A interface associada ao WLAN deve ter uma marcação VLAN ativada.
* VLAN multicast deve ser ativado para o WLAN.
* O reencaminhamento garp deve ser ativado no WLC.

Para obter mais informações, consulte [informações sobre a otimização multicast VLAN.](https://www.cisco.com/c/en/us/td/docs/wireless/controller/8-5/config-guide/b_cg85/wlan_interfaces.html)

### <a name="troubleshoot"></a>Resolução de problemas

Se encontrar problemas com alocações de endereços IP em VMs em execução num dispositivo Azure Stack Edge Mini R, as definições de configuração acima no ambiente de rede devem ser validadas.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [implantar o dispositivo Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-prep.md).
