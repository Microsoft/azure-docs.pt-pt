---
title: Tutorial para configurar definições de rede para dispositivo Azure Stack Edge Mini R no portal Azure
description: Tutorial para implementar Azure Stack Edge Mini R instrui-o a configurar a rede, a rede de cálculo e as definições de procuração web para o seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Mini R so I can use it to transfer data to Azure.
ms.openlocfilehash: 1cca747003a127371db7d110500e2b4168f10219
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594455"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-mini-r"></a>Tutorial: Rede de configuração para Azure Stack Edge Mini R

Este tutorial descreve como configurar a rede para o seu dispositivo Azure Stack Edge Mini R com uma GPU a bordo utilizando a UI web local.

O processo de ligação pode demorar cerca de 20 minutos a ser concluído.

Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Rede de configuração
> * Ativar a rede de cálculo
> * Configurar proxy Web


## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar o seu dispositivo Azure Stack Edge Mini R, certifique-se de que:

* Instalou o dispositivo físico conforme detalhado na [Instalação Azure Stack Edge Mini R](azure-stack-edge-gpu-deploy-install.md).
* Você ligou-se à uI web local do dispositivo como detalhado em [Connect to Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-connect.md)


## <a name="configure-network"></a>Rede de configuração

A página **Get start** apresenta as várias definições necessárias para configurar e registar o dispositivo físico com o serviço Azure Stack Edge. 

Siga estes passos para configurar a rede para o seu dispositivo.

1. Na uI web local do seu dispositivo, vá para a página **Get start.** 

2. Se for necessária uma atualização de zero dias, pode fazê-lo aqui configurando uma porta de dados com uma ligação com fios. Para obter mais instruções sobre como configurar uma ligação com fios para este dispositivo, consulte [o seu dispositivo por cabo](azure-stack-edge-mini-r-deploy-install.md#cable-the-device). Depois de terminar a atualização, pode remover a ligação com fios.

3. Crie certificados para Wi-Fi e cadeia de assinaturas. Tanto a cadeia de assinaturas como os certificados de Wi-Fi devem ser o formato DER com uma extensão de ficheiro *.cer.* Para obter instruções, consulte [Criar certificados](azure-stack-edge-j-series-manage-certificates.md).

4. Na uI web local, vá para **Começar.** No azulejo **de Segurança,** selecione **Certificados** e, em seguida, selecione **Configure**. 

    [![Página local de UI "Certificados"](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/get-started-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/get-started-1.png#lightbox)

    1. Selecione **+ Adicionar certificado**. 
    
        [![Web local UI "Certificados" página 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-1.png#lightbox)

    2. Faça o upload da cadeia de assinaturas e **selecione Apply**.

        ![Web local UI "Certificados" página 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-2.png)

    3. Repita o procedimento com o certificado de Wi-Fi. 

        ![Web local UI "Certificados" página 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-4.png)

    4. Os novos certificados devem ser apresentados na página **de Certificados.** 
    
        [![Web local UI "Certificados" página 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-5.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-5.png#lightbox)

    5. Volta para **começar.**

3. No **azulejo da Rede,** selecione **Configure**.  
    
    No seu dispositivo físico, existem cinco interfaces de rede. A PORTA 1 e a PORTA 2 são interfaces de rede de 1 Gbps. PORT 3 e PORT 4 são todas interfaces de rede de 10 Gbps. O quinto porto é o porto de Wi-Fi. 

    [![Web local UI "Definições de rede" página 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)    
    
    Selecione a porta Wi-Fi e configufique as definições da porta. 
    
    > [!IMPORTANT]
    > Recomendamos vivamente que configuure um endereço IP estático para a porta Wi-Fi.  

    ![Web local UI "Definições de rede" página 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

    A **página 'Rede'** atualiza depois de aplicar as definições de porta Wi-Fi.

    ![Web local UI "Definições de rede" página 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)
   
4. **Selecione Adicionar Wi-Fi perfil** e faça upload do seu perfil de Wi-Fi. 

    ![Web local UI "Port WiFi Network settings" 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)
    
    Um perfil de rede sem fios contém o SSID (nome da rede), a chave de senha e as informações de segurança para poder ligar-se a uma rede sem fios. Pode obter o perfil Wi-Fi para o seu ambiente do seu administrador de rede.

    ![Web local UI "Port WiFi Network settings" 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

    Após a adição do perfil, a lista de Wi-Fi atualizações de perfis para refletir o novo perfil. O perfil deve mostrar o **estado de Ligação** como **Desligado**. 

    ![Web local UI "Port WiFi Network settings" 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)


5. Depois de o perfil de rede sem fios ser carregado com sucesso, conecte-se a este perfil. Selecione **Ligar para Wi-Fi perfil**. 

    ![Web local UI "Port Wi-Fi Network settings" 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

6. Selecione o perfil de Wi-Fi que adicionou no passo anterior e selecione **Apply**. 

    ![Web local UI "Port Wi-Fi Network settings" 5](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

    O **estado de Ligação** deve atualizar-se para **Connected**. A força do sinal atualiza para indicar a qualidade do sinal. 

    ![Web local UI "Port Wi-Fi Network settings" 6](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

    > [!NOTE]
    > Para transferir grandes quantidades de dados, recomendamos que utilize uma ligação com fios em vez da rede sem fios. 

6. Desligue a PORTA 1 do dispositivo a partir do portátil. 

7. Ao definir as definições de rede, tenha em mente:

   - Se o DHCP estiver ativado no seu ambiente, as interfaces de rede são configuradas automaticamente. Um endereço IP, uma sub-rede, um gateway e um DNS são atribuídos automaticamente.
   - Se o DHCP não estiver ativado, pode atribuir IPs estáticos se necessário.
   - Pode configurar a sua interface de rede como IPv4.
   - A agregação de teaming ou agregação de ligações do Cartão de Interface de Rede (NIC) não é suportada com Azure Stack Edge.
   - O número de série de qualquer porta corresponde ao número de série do nó. Para um dispositivo da série K, apenas é apresentado um número de série.

     >[!NOTE] 
     > Recomendamos que não alterne o endereço IP local da interface de rede de estático para DCHP, a menos que tenha outro endereço IP para se ligar ao dispositivo. Se estiver a utilizar uma interface de rede e alternar para DHCP, não haverá forma de determinar o endereço DHCP. Se quiser alterar para um endereço DHCP, aguarde até que o dispositivo tenha sido registado com o serviço e, em seguida, altere. Em seguida, pode visualizar os IPs de todos os adaptadores nas propriedades do **Dispositivo** no portal Azure para o seu serviço.

Depois de configurar e aplicar as definições de rede, selecione **Seguinte: Computação** para configurar a rede de cálculo.

## <a name="enable-compute-network"></a>Ativar a rede de cálculo

Siga estes passos para permitir a computação e configurar a rede de computação. 


1. Na página **Compute,** selecione uma interface de rede que deseja ativar para calcular. 

    ![Página de computação na UI local 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-1.png)

1. No diálogo **de definições de rede,** selecione **Ative**. Quando ativa o cálculo, é criado um interruptor virtual no seu dispositivo nessa interface de rede. O interruptor virtual é utilizado para a infraestrutura de computação do dispositivo. 
    
1. Atribuir **IPs de nó kubernetes**. Estes endereços IP estáticos são para o VM compute.  

    Para um dispositivo n-node, uma gama contígua de um mínimo de endereços *N+1* IPv4 (ou mais) são fornecidos para o VM compute usando os endereços IP de início e fim. Dado que a Azure Stack Edge é um dispositivo de 1 nó, são fornecidos no mínimo 2 endereços IPv4 contíguos.

    > [!IMPORTANT]
    > Kubernetes em Azure Stack Edge utiliza sub-rede 172.27.0.0/16 para pod e sub-rede 172.28.0.0/16 para o serviço. Certifique-se de que estes não estão a ser utilizados na sua rede. Se estas sub-redes já estiverem a ser utilizadas na sua rede, pode alterar estas sub-redes executando o `Set-HcsKubeClusterNetworkInfo` cmdlet a partir da interface PowerShell do dispositivo. Para obter mais informações, consulte [a cápsula Change Kubernetes e as sub-redes de serviço.](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets)


1. Atribuir **IPs de serviço externo Kubernetes**. Estes são também os endereços IP de equilíbrio de carga. Estes endereços IP contíguos são para serviços que pretende expor fora do cluster Kubernetes e especifica a gama IP estática dependendo do número de serviços expostos. 
    
    > [!IMPORTANT]
    > Recomendamos vivamente que especifique um endereço IP mínimo de 1 endereço IP para o serviço Azure Stack Edge Mini R Hub para aceder a módulos computacional. Em seguida, pode especificar opcionalmente endereços IP adicionais para outros módulos IoT Edge (1 por serviço/módulo) que precisam de ser acedidos a partir de fora do cluster. Os endereços IP do serviço podem ser atualizados mais tarde. 
    
1. Selecione **Aplicar**.

    ![Página de computação na UI local 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. A configuração demora alguns minutos a ser aplicada e poderá ser necessário refrescar o navegador. Pode ver que a porta especificada está ativada para calcular. 
 
    ![Página de computação na UI local 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Selecione **Seguinte: Procuração web** para configurar o representante web.  

  
## <a name="configure-web-proxy"></a>Configurar proxy Web

Esta é uma configuração opcional.

> [!IMPORTANT]
> * Se ativar o módulo IoT Edge no seu dispositivo Azure Stack Edge Mini R, recomendamos que desementa da web como **Nenhum**. O NTLM não é suportado.
>* Os ficheiros Proxy-auto config (PAC) não são suportados. Um ficheiro PAC define como os navegadores web e outros agentes do utilizador podem escolher automaticamente o servidor proxy apropriado (método de acesso) para obter um determinado URL. Os proxies que tentam intercetar e ler todo o tráfego (em seguida, re-assinar tudo com a sua própria certificação) não são compatíveis uma vez que o certificado de procuração não é confiável. Os proxies tipicamente transparentes funcionam bem com o Azure Stack Edge Mini R. Os proxies web não transparentes não são suportados.


1. Na página de **definições de procuração** web, tome os seguintes passos:

    1. Na caixa **DE URL de procuração web,** introduza o URL neste formato: `http://host-IP address or FQDN:Port number` . Os URLs HTTPS não são suportados.

    2. Em **Autenticação**, **selecione Nenhum** ou **NTLM**. Se ativar o módulo IoT Edge no seu dispositivo Azure Stack Edge Mini R, recomendamos que desementa de procuração web a **Nenhum**. **A NTLM** não é suportada.

    3. Se estiver a utilizar a autenticação, insira um nome de utilizador e uma palavra-passe.

    4. Para validar e aplicar as definições de procuração web configuradas, selecione **Apply**.
    
   ![Página local de UI "Web proxy settings"](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/web-proxy-1.png)

2. Depois de aplicar as definições, selecione **Seguinte: Dispositivo**.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre:

> [!div class="checklist"]
> * Pré-requisitos
> * Rede de configuração
> * Ativar a rede de cálculo
> * Configurar proxy Web


Para aprender a configurar o seu dispositivo Azure Stack Edge Mini R, consulte:

> [!div class="nextstepaction"]
> [Configurar definições de dispositivos](./azure-stack-edge-mini-r-deploy-set-up-device-update-time.md)
