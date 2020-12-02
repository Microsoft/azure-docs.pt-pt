---
title: Tutorial para configurar definições de rede para dispositivo Azure Stack Edge Pro R no portal Azure Microsoft Docs
description: Tutorial para implementar Azure Stack Edge Pro R instrui-o a configurar a rede, a rede de cálculo e as definições de procuração web para o seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: ad0010a1885c1e9bac8fa2341771cedc12115adc
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468735"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-pro-r"></a>Tutorial: Rede de configuração para Azure Stack Edge Pro R

Este tutorial descreve como configurar a rede para o seu dispositivo Azure Stack Edge Pro R utilizando a UI web local.

O processo de ligação pode demorar cerca de 20 minutos a ser concluído.

Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Rede de configuração
> * Ativar a rede de cálculo
> * Configurar proxy Web


## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar o seu dispositivo Azure Stack Edge Pro R, certifique-se de que:

* Instalou o dispositivo físico conforme detalhado na [Instalação Azure Stack Edge Pro R](azure-stack-edge-gpu-deploy-install.md).
* Você ligou-se à uI web local do dispositivo como detalhado em [Connect to Azure Stack Edge Pro R](azure-stack-edge-gpu-deploy-connect.md)


## <a name="configure-network"></a>Rede de configuração

A página **Get start** apresenta as várias definições necessárias para configurar e registar o dispositivo físico com o serviço Azure Stack Edge. 

Siga estes passos para configurar a rede para o seu dispositivo.

1. Na uI web local do seu dispositivo, vá para a página **Get start.** 

2. No azulejo **da Rede,** selecione **Configure** para ir à página **'Rede'.** 
    
    <!--![Local web UI "Network settings" tile](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)-->

    No seu dispositivo físico, existem quatro interfaces de rede. A PORTA 1 e a PORTA 2 são interfaces de rede de 1 Gbps. PORT 3 e PORT 4 são todas interfaces de rede de 10/25 Gbps. O PORT 1 é configurado automaticamente como uma porta só de gestão, e o PORTO 2 para o PORTO 4 são todas portas de dados. A página **'Rede'** é a seguinte.
    
    ![Página local de ui "definições de rede"](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-2.png)

   
3. Para alterar as definições de rede, selecione uma porta e no painel direito que aparece, modifique o endereço IP, sub-rede, gateway, DNS primário e DNS secundários. 

    - Se selecionar a Porta 1, pode ver que está pré-configurada como estática. 

        ![Web local UI "Definições de rede port 1"](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-3.png)

    - Se selecionar a Porta 2, a Porta 3 ou a Porta 4, todas estas portas estão configuradas como DHCP por defeito.

        ![Web local UI "Port 3 Network settings"](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-4.png)

    Ao definir as definições de rede, tenha em mente:

   * Se o DHCP estiver ativado no seu ambiente, as interfaces de rede são configuradas automaticamente. Um endereço IP, uma sub-rede, um gateway e um DNS são atribuídos automaticamente.
   * Se o DHCP não estiver ativado, pode atribuir IPs estáticos se necessário.
   * Pode configurar a sua interface de rede como IPv4.
   * O número de série de qualquer porta corresponde ao número de série do nó.
    <!--* On the 25-Gbps interfaces, you can set the RDMA (Remote Direct Access Memory) mode to iWarp or RoCE (RDMA over Converged Ethernet). Where low latencies are the primary requirement and scalability is not a concern, use RoCE. When latency is a key requirement, but ease-of-use and scalability are also high priorities, iWARP is the best candidate.-->
    Uma vez configurada a rede do dispositivo, a página atualiza-se conforme mostrado abaixo.

    ![Web local UI "Definições de rede" página 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-2a.png)<!--change-->


     >[!NOTE]
     >
     > * Recomendamos que não alterne o endereço IP local da interface de rede de estático para DCHP, a menos que tenha outro endereço IP para se ligar ao dispositivo. Se estiver a utilizar uma interface de rede e alternar para DHCP, não haverá forma de determinar o endereço DHCP. Se pretender alterar para um endereço DHCP, aguarde até que o dispositivo tenha sido ativado com o serviço e, em seguida, altere. Em seguida, pode visualizar os IPs de todos os adaptadores nas propriedades do **Dispositivo** no portal Azure para o seu serviço.


    Depois de configurar e aplicar as definições de rede, selecione **Seguinte: Computação** para configurar a rede de cálculo.

## <a name="enable-compute-network"></a>Ativar a rede de cálculo

Siga estes passos para permitir a computação e configurar a rede de computação. 

1. Na página **Compute,** selecione uma interface de rede que deseja ativar para calcular. 

    ![Página de computação na UI local 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-2.png)

1. No diálogo **de definições de rede,** selecione **Ative**. Quando ativa o cálculo, é criado um interruptor virtual no seu dispositivo nessa interface de rede. O interruptor virtual é utilizado para a infraestrutura de computação do dispositivo. 
    
1. Atribuir **IPs de nó kubernetes**. Estes endereços IP estáticos são para o VM compute.  

    Para *n* um dispositivo n-node, uma gama contígua de um mínimo de endereços *N+1* IPv4 (ou mais) são fornecidos para o VM compute usando os endereços IP de início e fim. Dado que a Azure Stack Edge é um dispositivo de 1 nó, são fornecidos no mínimo 2 endereços IPv4 contíguos. Estes endereços IP devem estar na mesma rede onde o computo ativou e foi criado o switch virtual.

    > [!IMPORTANT]
    > Kubernetes em Azure Stack Edge utiliza sub-rede 172.27.0.0/16 para pod e sub-rede 172.28.0.0/16 para o serviço. Certifique-se de que estes não estão a ser utilizados na sua rede. Se estas sub-redes já estiverem a ser utilizadas na sua rede, pode alterar estas sub-redes executando o `Set-HcsKubeClusterNetworkInfo` cmdlet a partir da interface PowerShell do dispositivo. Para obter mais informações, consulte [a cápsula Change Kubernetes e as sub-redes de serviço.](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets)


1. Atribuir **IPs de serviço externo Kubernetes**. Estes são também os endereços IP de equilíbrio de carga. Estes endereços IP contíguos são para serviços que pretende expor fora do cluster Kubernetes e especifica a gama IP estática dependendo do número de serviços expostos. 
    
    > [!IMPORTANT]
    > Recomendamos vivamente que especifique um endereço IP mínimo de 1 endereço IP para o serviço Azure Stack Edge Pro R Hub para aceder a módulos computacional. Em seguida, pode especificar opcionalmente endereços IP adicionais para outros módulos IoT Edge (1 por serviço/módulo) que precisam de ser acedidos a partir de fora do cluster. Os endereços IP do serviço podem ser atualizados mais tarde. 
    
1. Selecione **Aplicar**.

    ![Página de computação na UI local 3](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. A configuração demora alguns minutos a ser aplicada e poderá ser necessário refrescar o navegador. Pode ver que a porta especificada está ativada para calcular. 
 
    ![Página de computação na UI local 4](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Selecione **Seguinte: Procuração web** para configurar o representante web.  

  
## <a name="configure-web-proxy"></a>Configurar proxy Web

Esta é uma configuração opcional.

> [!IMPORTANT]
> * Se ativar o módulo IoT Edge no seu dispositivo Azure Stack Edge Pro R, recomendamos que desementa da web como **Nenhum**. O NTLM não é suportado.
>* Os ficheiros Proxy-auto config (PAC) não são suportados. Um ficheiro PAC define como os navegadores web e outros agentes do utilizador podem escolher automaticamente o servidor proxy apropriado (método de acesso) para obter um determinado URL. Os proxies que tentam intercetar e ler todo o tráfego (em seguida, re-assinar tudo com a sua própria certificação) não são compatíveis uma vez que o certificado de procuração não é confiável. Os proxies tipicamente transparentes funcionam bem com o Azure Stack Edge Pro R. Os proxies web não transparentes não são suportados.


1. Na página de **definições de procuração** web, tome os seguintes passos:

    1. Na caixa **DE URL de procuração web,** introduza o URL neste formato: `http://host-IP address or FQDN:Port number` . Os URLs HTTPS não são suportados.

    2. Em **Autenticação**, **selecione Nenhum** ou **NTLM**. Se ativar o módulo IoT Edge no seu dispositivo Azure Stack Edge Pro R, recomendamos que desementa de procuração web a **Nenhum**. **A NTLM** não é suportada.

    3. Se estiver a utilizar a autenticação, insira um nome de utilizador e uma palavra-passe.

    4. Para validar e aplicar as definições de procuração web configuradas, selecione **Apply**.
    
   ![Web local UI "Web proxy settings" página 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/web-proxy-2.png)

2. Depois de aplicar as definições, selecione **Seguinte: Dispositivo**.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre:

> [!div class="checklist"]
> * Pré-requisitos
> * Rede de configuração
> * Ativar a rede de cálculo
> * Configurar proxy Web


Para aprender a configurar o seu dispositivo Azure Stack Edge Pro R, consulte:

> [!div class="nextstepaction"]
> [Configurar definições de dispositivos](./azure-stack-edge-pro-r-deploy-set-up-device-update-time.md)
