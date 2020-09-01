---
title: Tutorial para configurar definições de rede para dispositivo Azure Stack Edge com GPU no portal Azure Microsoft Docs
description: Tutorial para implementar Azure Stack Edge instrui-o a configurar a rede, a rede de cálculo e as definições de procuração web para o seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 673bd211f3271eceacb18e7fabfcc6ace2ded42c
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89088059"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-with-gpu"></a>Tutorial: Rede de configuração para Azure Stack Edge com GPU

Este tutorial descreve como configurar a rede para o seu dispositivo Azure Stack Edge com uma GPU a bordo, utilizando a UI web local.

O processo de ligação pode demorar cerca de 20 minutos a ser concluído.

Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Rede de configuração
> * Ativar a rede de cálculo
> * Configurar proxy Web


## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar o seu dispositivo Azure Stack Edge com GPU, certifique-se de que:

* Instalou o dispositivo físico conforme detalhado na [Instalação Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).
* Ligou-se à uI web local do dispositivo como detalhado em [Connect to Azure Stack Edge](azure-stack-edge-gpu-deploy-connect.md)


## <a name="configure-network"></a>Rede de configuração

A página **Get start** apresenta as várias definições necessárias para configurar e registar o dispositivo físico com o serviço Azure Stack Edge. 

Siga estes passos para configurar a rede para o seu dispositivo.

1. Na uI web local do seu dispositivo, vá para a página **Get start.** 

2. No **azulejo da Rede,** selecione **Configure**.  
    
    ![Azulejo local web UI "Definições de rede"](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)

    No seu dispositivo físico, existem seis interfaces de rede. PORT 1 e PORT 2 são interfaces de rede de 1 Gbps. PORT 3, PORT 4, PORT 5 e PORT 6 são todas interfaces de rede de 25 Gbps que também podem servir como interfaces de rede de 10 Gbps. O PORT 1 é configurado automaticamente como uma porta só de gestão, e o PORTO 2 para o PORTO 6 são todas portas de dados. Para um novo dispositivo, a página **de definições de Rede** é como mostrado abaixo.
    
    ![Página local de ui "definições de rede"](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2a.png)


   
3. Para alterar as definições de rede, selecione uma porta e no painel direito que aparece, modifique o endereço IP, sub-rede, gateway, DNS primário e DNS secundários. 

    - Se selecionar a Porta 1, pode ver que está pré-configurada como estática. 

        ![Web local UI "Definições de rede port 1"](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-3.png)

    - Se selecionar a Porta 2, a Porta 3, a Porta 4 ou a Porta 5, todas estas portas estão configuradas como DHCP por defeito.

        ![Web local UI "Port 3 Network settings"](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-4.png)

    Ao configurar as definições da rede, lembre-se:

   * Se o DHCP estiver ativado no seu ambiente, as interfaces de rede são configuradas automaticamente. Um endereço IP, sub-rede, gateway e DNS são automaticamente atribuídos.
   * Se o DHCP não estiver ativado, pode atribuir IPs estáticos se necessário.
   * Pode configurar a sua interface de rede como IPv4.
   * Nas interfaces de 25-Gbps, pode definir o modo RDMA (Memória de Acesso Direto Remoto) para iWarp ou RoCE (RDMA sobre Ethernet Converged). Quando as baixas latências são a principal exigência e a escalabilidade não é uma preocupação, utilize o RoCE. Quando a latência é um requisito fundamental, mas a facilidade de utilização e a escalabilidade também são prioridades elevadas, o iWARP é o melhor candidato.
   * O número de série de qualquer porta corresponde ao número de série do nó.

    Uma vez configurada a rede do dispositivo, a página atualiza-se conforme mostrado abaixo.

    ![Página local de ui "definições de rede"](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2.png)


     >[!NOTE]
     >
     > * Recomendamos que não altere o endereço IP local da interface de rede de estática para DCHP, a menos que tenha outro endereço IP para ligar ao dispositivo. Se utilizar uma interface de rede e mudar para DHCP, não haverá forma de determinar o endereço DHCP. Se pretender alterar para um endereço DHCP, aguarde até que o dispositivo tenha sido ativado com o serviço e, em seguida, altere. Em seguida, pode visualizar os IPs de todos os adaptadores nas propriedades do **Dispositivo** no portal Azure para o seu serviço.
     > * Se ativar o módulo IoT Edge no seu dispositivo Azure Stack Edge, recomendamos que desementa da web como **Nenhum**. A NTLM não é suportada.

    Depois de configurar e aplicar as definições de rede, volte para **começar**.

## <a name="enable-compute-network"></a>Ativar a rede de cálculo

Siga estes passos para permitir a computação e configurar a rede de computação.

1. Vá à página **Get start** na UI web local do seu dispositivo. No **azulejo da Rede,** selecione **Rede Compute**.  

    ![Página de computação na UI local](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-1.png)

2. Na página **Compute,** selecione uma interface de rede que deseja ativar para calcular. 

    ![Página de computação na UI local](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)

2. No diálogo **de definições de rede,** selecione **Ative**. Quando ativa o cálculo, é criado um interruptor virtual no seu dispositivo nessa interface de rede. O interruptor virtual é utilizado para a infraestrutura de computação do dispositivo. 
    
3. Atribuir **IPs de nó kubernetes**. Estes endereços IP estáticos são para o VM compute. 

    Para *n*um dispositivo n-node, uma gama contígua de um mínimo de endereços *N+1* IPv4 (ou mais) são fornecidos para o VM compute usando os endereços IP de início e fim. Dado que a Azure Stack Edge é um dispositivo de 1 nó, são fornecidos no mínimo 2 endereços IPv4 contíguos. 

4. Atribuir **IPs de serviço externo Kubernetes**. Estes são também os endereços IP de equilíbrio de carga. Estes endereços IP contíguos são para serviços que pretende expor fora do cluster Kubernetes e especifica a gama IP estática dependendo do número de serviços expostos. 
    
    > [!IMPORTANT]
    > Recomendamos vivamente que especifique um endereço IP mínimo de 1 endereço IP para o serviço Azure Stack Edge Hub para aceder a módulos computacional. Em seguida, pode especificar opcionalmente endereços IP adicionais para outros módulos IoT Edge (1 por serviço/módulo) que precisam de ser acedidos a partir de fora do cluster. Os endereços IP do serviço podem ser atualizados mais tarde. 
    
5. Selecione **Aplicar**.

    ![Página de computação na UI local](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-3.png)


## <a name="configure-web-proxy"></a>Configurar proxy Web

Esta é uma configuração opcional.

1. Vá à página Get start na UI web local do seu dispositivo.
2. No azulejo **da Rede,** configurar as definições do servidor de procuração web. Embora a configuração de procuração web seja opcional, se utilizar um representante web, pode configugá-la apenas nesta página.

   ![Página local de UI "Web proxy settings"](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-1.png)

3. Na página de **definições de procuração** web, tome os seguintes passos:

    1. Na caixa **DE URL de procuração web,** introduza o URL neste formato: `http://host-IP address or FQDN:Port number` . OS URLs HTTPS não são suportados.

    2. Em **Autenticação**, **selecione Nenhum** ou **NTLM**. Se ativar o módulo IoT Edge no seu dispositivo Azure Stack Edge, recomendamos que detenda a autenticação por procuração web a **Nenhum**. **A NTLM** não é suportada.

    3. Se estiver a utilizar a autenticação, insira um nome de utilizador e uma palavra-passe.

    4. Para validar e aplicar as definições de procuração web configuradas, selecione **Apply**.
    
   ![Página local de UI "Web proxy settings"](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-2.png)

4. Depois de aplicadas as definições, volte para **começar**.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre:

> [!div class="checklist"]
> * Pré-requisitos
> * Rede de configuração
> * Ativar a rede de cálculo
> * Configurar proxy Web


Para aprender a configurar o seu dispositivo Azure Stack Edge, consulte:

> [!div class="nextstepaction"]
> [Configurar definições de dispositivos](./azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
