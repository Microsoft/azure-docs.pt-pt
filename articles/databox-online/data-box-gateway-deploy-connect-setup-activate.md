---
title: Ligue-se para configurar e ativar o Gateway de caixa de dados do Azure no portal do Azure | Documentos da Microsoft
description: Terceiro tutorial para implementar o Gateway de caixa de dados instrui a ligar, configurar e ativar o dispositivo virtual.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: de2ef4908260a62acf28a270dda6ad738a1760b9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402342"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Tutorial: Ligar, configurar, ativar o Gateway de caixa de dados do Azure

## <a name="introduction"></a>Introdução

Este tutorial descreve como ligar a, configurar e ativar o seu dispositivo de Gateway de caixa de dados utilizando a IU da web local. 

O processo de instalação e ativação pode demorar cerca de 10 minutos para concluir. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ligar a um dispositivo virtual
> * Configurar e ativar o dispositivo virtual

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar o Gateway de caixa de dados, certifique-se de que:

* Tiver aprovisionado um dispositivo virtual e obter um URL ligado à mesma, conforme detalhado no [aprovisionar um Gateway de caixa de dados no Hyper-V](data-box-gateway-deploy-provision-hyperv.md) ou [aprovisionar um Gateway de caixa de dados no VMware](data-box-gateway-deploy-provision-vmware.md).
* Tem a chave de ativação do serviço de Gateway de caixa de dados que criou para gerir dispositivos de Gateway de caixa de dados. Para obter mais informações, aceda a [preparar a implementação de Gateway de caixa de dados do Azure](data-box-gateway-deploy-prep.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Ligue-se para a configuração de interface do Usuário da local web 

1. Abra uma janela do browser e o acesso da interface do Usuário do dispositivo no local da web:
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   Utilize o URL de ligação que anotou no tutorial anterior. Verá um erro ou um aviso que indica que existe um problema com o certificado de segurança do Web site.

2. Selecione **continuar para esta página da Web**. Estes passos podem variar consoante o browser que estiver a utilizar.
   
    ![Mensagem de erro de certificado de segurança de Web site](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Inicie sessão para a web da interface do Usuário do seu dispositivo virtual. A palavra-passe predefinido é *Password1*. 
   
    ![Inicie sessão na IU web local](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. Na linha de comandos, altere a palavra-passe do dispositivo. A nova palavra-passe tem de conter entre 8 e 16 carateres. Tem de conter 3 dos seguintes: maiúsculas, minúsculas, numéricos e especiais carateres.

    ![Alterar a palavra-passe do dispositivo](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Agora, está no **Dashboard** do seu dispositivo.

## <a name="set-up-and-activate-the-virtual-device"></a>Configurar e ativar o dispositivo virtual
 
O dashboard apresenta as várias configurações que são necessárias para configurar e registar o dispositivo virtual com o serviço de Gateway de caixa de dados. O **nome do dispositivo**, **as definições de rede**, **definições de proxy Web**, e **definições da hora** são opcionais. As definições necessárias apenas são **definições da Cloud**.
   
![Web local da interface do Usuário "Dashboard" página](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. No painel esquerdo, selecione **nome do dispositivo**e, em seguida, introduza um nome amigável para o seu dispositivo. O nome amigável tem de conter entre 1 e 15 carateres e ter letras, números e hífenes.

    ![Página de "Nome do dispositivo" da IU local web](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. (Opcional) No painel esquerdo, selecione **as definições de rede** e, em seguida, configure as definições. No seu dispositivo virtual, verá, pelo menos, uma interface de rede e mais, dependendo de quantos configurado na máquina virtual subjacente. O **as definições de rede** página para um dispositivo virtual com uma interface de rede ativada é conforme mostrado abaixo.
    
    ![Página de "Definições de rede" da IU local web](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    À medida que configura as definições de rede, tenha em mente:

    - Se o DHCP estiver ativado no seu ambiente, as interfaces de rede são configuradas automaticamente. Um endereço IP, sub-rede, gateway e DNS é atribuído automaticamente.
    - Se o DHCP não está ativado, pode atribuir IPs estáticos, se necessário.
    - Pode configurar a sua interface de rede como IPv4.

     >[!NOTE] 
     > Recomendamos que não muda o endereço IP local da interface de rede de estático para DHCP, a menos que tenha outro endereço IP para ligar ao dispositivo. Se utilizar uma interface de rede e mude para DHCP, não haveria nenhuma forma de determinar o endereço DHCP. Se pretender alterar para um endereço DHCP, esperar até depois do dispositivo foi registado com o serviço e, em seguida, altere. Em seguida, pode ver os IPs de todos os adaptadores na **propriedades do dispositivo** no portal do Azure para o seu serviço.

3. (Opcional) Configure o servidor proxy Web. Apesar de configuração do proxy web é opcional, se utilizar um proxy de web, pode configurá-lo apenas nesta página.
   
   ![Página de "Definições de proxy da Web" de IU local web](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Sobre o **Web proxy** página, efetue o seguinte:
   
   1. Na **URL de proxy Web** , introduza o URL no seguinte formato: `http://&lt;host-IP address or FQDN&gt;:Port number`. Não são suportados HTTPS URLs.
   2. Sob **autenticação**, selecione **None** ou **NTLM**.
   3. Se estiver a utilizar a autenticação, introduza um **nome de utilizador** e **palavra-passe**.
   4. Para validar e aplicar as definições de proxy da web configurado, selecione **aplicar**.

4. (Opcional) No painel esquerdo, selecione **definições da hora**e, em seguida, configure o fuso horário e os servidores NTP primários e secundários para o seu dispositivo. 

    Os servidores NTP são necessários porque o dispositivo tem de sincronizar tempo para que ele pode autenticar com o seu fornecedor de serviços cloud.
    
    ![Página de "Definições de tempo" de IU local web](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Na **definições da hora** página, efetue o seguinte:
    
    1. Na **fuso horário** pendente, selecione o fuso horário que corresponde à localização geográfica na qual o dispositivo é implementado.
        O fuso horário predefinido para o seu dispositivo é PST. O dispositivo utiliza este fuso horário para todas as operações agendadas.

    2. Especifique um **servidor NTP primário** para o seu dispositivo ou aceite o valor predefinido de `time.windows.com`.   
        Verifique se a sua rede permite que o tráfego NTP passe do datacenter para a Internet.

    3. Opcionalmente, na **servidor NTP secundário** , introduza um servidor secundário para o seu dispositivo.

    4. Para validar e aplicar as definições de tempo configurado, selecione **aplicar**.

6. No painel esquerdo, selecione **definições da Cloud**e, em seguida, ativar o dispositivo com o serviço de Gateway de caixa de dados no portal do Azure.
    
    1. Na **chave de ativação** , introduza o **chave de ativação** que obteve [obter a chave de ativação](data-box-gateway-deploy-prep.md#get-the-activation-key) para o Gateway de caixa de dados.

    2. Selecione **ativar**.
       
         ![Página de "Definições da nuvem" da IU local web](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. O dispositivo é ativado e atualizações críticas, se estiver disponível, são automaticamente aplicadas. Verá uma notificação para o efeito. Monitorize o progresso de atualização através do portal do Azure.

        ![Página de "Definições da nuvem" da IU local web](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **A caixa de diálogo também tem uma chave de recuperação que deve copiar e guardar numa localização segura. Esta chave é utilizada para recuperar os dados no caso do dispositivo não é possível efetuar o arranque.**


    4. Poderá ter de aguardar alguns minutos para a atualização concluir com êxito. Após a atualização é concluída, inicie sessão no dispositivo. O **definições da Cloud** página é atualizada para indicar que o dispositivo é ativado com êxito.

        ![Página de "Definições da nuvem" local web da interface do Usuário atualizada](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

A configuração do dispositivo foi concluída. Agora pode adicionar partilhas no seu dispositivo.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Ligar a um dispositivo virtual
> * Configurar e ativar o dispositivo virtual

Para saber como transferir dados com o Gateway de caixa de dados, veja:

> [!div class="nextstepaction"]
> [Transferir dados com dados de caixa de Gateway](./data-box-gateway-deploy-add-shares.md).
