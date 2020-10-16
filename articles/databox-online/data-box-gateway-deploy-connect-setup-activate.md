---
title: Conecte-se e ative o Azure Data Box Gateway no portal Azure
description: O terceiro tutorial para implementar o Data Box Gateway instrui-o a ligar, configurar e ativar o seu dispositivo virtual.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: cb6ef6ac25c4afa72160ba437e0ea3b5492cfd93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91741963"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Tutorial: Ligar, configurar, ativar gateway Azure Data Box

## <a name="introduction"></a>Introdução

Este tutorial descreve como ligar, configurar e ativar o seu dispositivo Data Box Gateway utilizando a UI web local. 

O processo de configuração e ativação pode demorar cerca de 10 minutos a ser concluído. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ligar-se a um dispositivo virtual
> * Configurar e ativar o dispositivo virtual

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar o seu Portal da Caixa de Dados, certifique-se de que:

* Ateou um dispositivo virtual e obteve um URL ligado ao mesmo conforme detalhado na [Provision a Data Box Gateway em Hyper-V](data-box-gateway-deploy-provision-hyperv.md) ou Provision a Data Box Gateway em [VMware](data-box-gateway-deploy-provision-vmware.md).
* Tem a chave de ativação do serviço Data Box Gateway que criou para gerir dispositivos Data Box Gateway. Para mais informações, vá a [Preparar-se para implementar o Gateway da Caixa de Dados Azure](data-box-gateway-deploy-prep.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Conecte-se à configuração local do UI web 

1. Abra uma janela do navegador e aceda à UI web local do dispositivo em:
   
   https: \/ /ip-address-of-network-interface
   
   Utilize o URL de ligação indicado no tutorial anterior. Verá um erro ou um aviso a indicar que existe um problema com o certificado de segurança do site.

2. **Selecione Continue a esta página web**. Estes passos podem variar consoante o browser utilizado.
   
    ![Mensagem de erro do certificado de segurança do site](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Inscreva-se na UI web do seu dispositivo virtual. A palavra-passe predefinida é *Palavra-passe1*. 
   
    ![Inscreva-se na UI web local](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. A pedido, altere a palavra-passe do dispositivo. A nova palavra-passe deve conter entre 8 e 16 caracteres. Deve conter 3 dos seguintes caracteres: maiúsculas, minúsculas, numéricas e caracteres especiais.

    ![Alterar a palavra-passe do dispositivo](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Está agora no painel de **instrumentos** do seu dispositivo.

## <a name="set-up-and-activate-the-virtual-device"></a>Configurar e ativar o dispositivo virtual
 
O seu painel de instrumentos exibe as várias definições necessárias para configurar e registar o dispositivo virtual com o serviço Data Box Gateway. O **nome do dispositivo,** **as definições de rede,** **as definições de procuração web**e as **definições de tempo** são opcionais. As únicas definições necessárias são **as definições cloud**.
   
![Página local de UI "Dashboard"](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. No painel esquerdo, selecione **o nome do dispositivo**e, em seguida, introduza um nome amigável para o seu dispositivo. O nome amigável deve conter de 1 a 15 caracteres e ter letra, números e hífenes. 

    ![Página local de ui "nome do dispositivo"](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. (Opcional) No painel esquerdo, selecione **as definições de rede** e, em seguida, configufique as definições. No seu dispositivo virtual, verá pelo menos uma interface de rede e mais dependendo de quantas configuraram na máquina virtual subjacente. A página **de definições de Rede** para um dispositivo virtual com uma interface de rede ativada é a seguinte.
    
    ![Página local de ui "definições de rede"](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Ao configurar as definições de rede, lembre-se:

    - Se o DHCP estiver ativado no seu ambiente, as interfaces de rede são configuradas automaticamente. Um endereço IP, uma sub-rede, um gateway e um DNS são atribuídos automaticamente.
    - Se o DHCP não estiver ativado, pode atribuir IPs estáticos se necessário.
    - Pode configurar a sua interface de rede como IPv4.

     >[!NOTE] 
     > Recomendamos que não altere o endereço IP local da interface de rede de estática para DHCP, a menos que tenha outro endereço IP para ligar ao dispositivo. Se estiver a utilizar uma interface de rede e alternar para DHCP, não haverá forma de determinar o endereço DHCP. Se quiser alterar para um endereço DHCP, aguarde até que o dispositivo tenha sido registado com o serviço e, em seguida, altere. Em seguida, pode visualizar os IPs de todos os adaptadores nas propriedades do **Dispositivo** no portal Azure para o seu serviço.

3. (Opcional) Configure o servidor proxy Web. Embora a configuração de procuração web seja opcional, se utilizar um representante web, só pode configugá-la nesta página.
   
   ![Página local de UI "Web proxy settings"](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Na página **de procuração** da Web, faça o seguinte:
   
   1. Na caixa **DE URL de procuração web,** introduza o URL neste formato: `http://&lt;host-IP address or FQDN&gt;:Port number` . Os URLs HTTPS não são suportados.
   2. Em **Autenticação**, **selecione Nenhum** ou **NTLM**.
   3. Se estiver a utilizar a autenticação, insira um **nome de utilizador** e uma **palavra-passe**.
   4. Para validar e aplicar as definições de procuração web configuradas, selecione **Apply**.

   > [!NOTE]
   > Os ficheiros Proxy-auto config (PAC) não são suportados. Um ficheiro PAC define como os navegadores web e outros agentes do utilizador podem escolher automaticamente o servidor proxy apropriado (método de acesso) para obter um determinado URL.
   > Os proxies que tentam intercetar e ler todo o tráfego (e, em seguida, voltam a assinar tudo com a sua própria certificação) não são compatíveis, pois o certificado do proxy não é fidedigno.
   > Os proxies tipicamente transparentes funcionam bem com o Azure Data Box Gateway.

4. (Opcional) No painel esquerdo, selecione definições de tempo e, em seguida, configufique o **fuso**horário e os servidores NTP primários e secundários para o seu dispositivo. 

    Os servidores NTP são necessários porque o seu dispositivo deve sincronizar o tempo para que possa autenticar com os seus fornecedores de serviços na nuvem.
    
    ![Página local de UI "Definições de tempo"](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Na página **'Definições tempor',** faça o seguinte:
    
    1. Na lista de down-down do **fuso** horário, selecione o fuso horário que corresponde à localização geográfica em que o dispositivo é implantado.
        O fuso horário padrão para o seu dispositivo é PST. O dispositivo utiliza este fuso horário para todas as operações agendadas.

    2. Especifique um **servidor NTP primário** para o seu dispositivo ou aceite o valor predefinido de `time.windows.com` .   
        Verifique se a sua rede permite que o tráfego NTP passe do datacenter para a Internet.

    3. Opcionalmente, na caixa **de servidor NTP secundária,** introduza um servidor secundário para o seu dispositivo.

    4. Para validar e aplicar as definições de tempo configuradas, **selecione Aplicar**.

6. No painel esquerdo, selecione **as definições cloud**e, em seguida, ative o seu dispositivo com o serviço Data Box Gateway no portal Azure.
    
    1. Na caixa **de chave de ativação,** introduza a **tecla de ativação** que obteve na [Obter a chave de ativação](data-box-gateway-deploy-prep.md#get-the-activation-key) para Data Box Gateway.

    2. **Selecione Ativar**.
       
         ![Página local de UI "Cloud Settings"](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. O dispositivo é ativado e as atualizações críticas, se disponíveis, são aplicadas automaticamente. Vê uma notificação nesse sentido. Monitorize o progresso da atualização através do portal Azure.

        ![Web local UI "Cloud settings" página 2](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **O diálogo também tem uma chave de recuperação que deve copiar e guardar num local seguro. Esta chave é utilizada para recuperar os seus dados no caso de o dispositivo não conseguir arrancar.**


    4. Pode ter de esperar vários minutos para que a atualização esteja concluída com sucesso. Depois de concluída a atualização, inscreva-se no dispositivo. A **Cloud configura** atualizações de página para indicar que o dispositivo está ativado com sucesso.

        ![Página local de UI "Cloud Settings" atualizada](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

A configuração do dispositivo está completa. Agora pode adicionar ações no seu dispositivo.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Ligar-se a um dispositivo virtual
> * Configurar e ativar o dispositivo virtual

Para saber como transferir dados com o seu Portal da Caixa de Dados, consulte:

> [!div class="nextstepaction"]
> [Transferir dados com data box gateway](./data-box-gateway-deploy-add-shares.md).
