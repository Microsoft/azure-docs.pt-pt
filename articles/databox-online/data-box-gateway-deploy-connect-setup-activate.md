---
title: Ligue- se, configure e ative o Portal da Caixa de Dados Azure no portal Azure
description: Terceiro tutorial para implementar Data Box Gateway instrui-o a ligar, configurar e ativar o seu dispositivo virtual.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: 78c06cc7f08fe94a25ea63d9bf76cc1352d9f2b7
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82561668"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Tutorial: Ligar, configurar, ativar portal da caixa de dados Azure

## <a name="introduction"></a>Introdução

Este tutorial descreve como ligar, configurar e ativar o seu dispositivo Data Box Gateway utilizando a UI web local. 

O processo de configuração e ativação pode demorar cerca de 10 minutos a ser concluído. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ligar a um dispositivo virtual
> * Configurar e ativar o dispositivo virtual

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar o gateway da caixa de dados, certifique-se de que:

* Você disponibilizou um dispositivo virtual e obteve um URL conectado para ele como detalhado na Disposição de uma Porta de [Dados em Hiper-V](data-box-gateway-deploy-provision-hyperv.md) ou [Provision a Data Box Gateway em VMware](data-box-gateway-deploy-provision-vmware.md).
* Tem a chave de ativação do serviço Data Box Gateway que criou para gerir os dispositivos Data Box Gateway. Para mais informações, vá à [Prepare-se para implementar o Portal](data-box-gateway-deploy-prep.md)da Caixa de Dados Do Azure .


## <a name="connect-to-the-local-web-ui-setup"></a>Ligue-se à configuração de UI web local 

1. Abra uma janela do navegador e aceda à UI web local do dispositivo em:
   
   https:\//ip-address-of-network-interface
   
   Utilize o URL de ligação anotado no tutorial anterior. Você vê um erro ou um aviso indicando que há um problema com o certificado de segurança do site.

2. Selecione **Continuar a esta página web**. Estes passos podem variar dependendo do navegador que está a usar.
   
    ![Mensagem de erro de certificado de segurança do site](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Inscreva-se na Web UI do seu dispositivo virtual. A palavra-passe padrão é *Password1*. 
   
    ![Inscreva-se na Web UI local](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. No momento, altere a senha do dispositivo. A nova senha deve conter entre 8 e 16 caracteres. Deve conter 3 dos seguintes caracteres: maiúsculas, minúsculas, numéricas e caracteres especiais.

    ![Alterar a palavra-passe do dispositivo](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Está agora no **Painel** do Seu Dispositivo.

## <a name="set-up-and-activate-the-virtual-device"></a>Configurar e ativar o dispositivo virtual
 
O seu painel de instrumentos apresenta as várias definições necessárias para configurar e registar o dispositivo virtual com o serviço Data Box Gateway. O **nome do dispositivo,** **definições**de rede, definições de **procuração web**e **definições** de tempo são opcionais. As únicas definições necessárias são **as definições cloud**.
   
![Página local da UI "Dashboard"](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. No painel esquerdo, selecione o **nome do Dispositivo**e, em seguida, introduza um nome amigável para o seu dispositivo. O nome amigável deve conter de 1 a 15 caracteres de comprimento e ter letra, números e hífenes. 

    ![Página local da UI "Nome do dispositivo"](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. (Opcional) No painel esquerdo, selecione **as definições** de rede e, em seguida, configure as definições. No seu dispositivo virtual, verá pelo menos uma interface de rede e mais dependendo de quantos configurado na máquina virtual subjacente. A página de **definições** da Rede para um dispositivo virtual com uma interface de rede ativada é como mostrado abaixo.
    
    ![Página local da Web UI "Definições de rede"](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    À medida que configura as definições de rede, lembre-se:

    - Se o DHCP estiver ativado no seu ambiente, as interfaces de rede são configuradas automaticamente. Um endereço IP, sub-rede, gateway e DNS são automaticamente atribuídos.
    - Se o DHCP não estiver ativado, pode atribuir IPs estáticos se necessário.
    - Pode configurar a interface de rede como IPv4.

     >[!NOTE] 
     > Recomendamos que não altere o endereço IP local da interface de rede de estática para DHCP, a menos que tenha outro endereço IP para ligar ao dispositivo. Se utilizar uma interface de rede e mudar para DHCP, não existiria forma de determinar o endereço DHCP. Se quiser mudar para um endereço DHCP, aguarde até que o dispositivo se tenha registado no serviço e, em seguida, mude. Em seguida, pode visualizar os IPs de todos os adaptadores nas propriedades do **Dispositivo** no portal Azure para o seu serviço.

3. (Opcional) Configure o servidor proxy Web. Embora a configuração de procuração web seja opcional, se utilizar um proxy web, pode configurá-la apenas nesta página.
   
   ![Página local da Web UI "Web proxy settings"](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Na página de **procuração da Web,** faça o seguinte:
   
   1. Na caixa URL proxy **Web,** introduza `http://&lt;host-IP address or FQDN&gt;:Port number`o URL neste formato: . OS URLs HTTPS não são suportados.
   2. Em **Autenticação,** selecione **Nenhum** ou **NTLM**.
   3. Se estiver a utilizar a autenticação, introduza um **nome de utilizador** e **palavra-passe**.
   4. Para validar e aplicar as definições configuradas de procuração web, selecione **Aplicar**.

   > [!NOTE]
   > Os ficheiros proxy-auto config (PAC) não são suportados. Um ficheiro PAC define como os navegadores web e outros agentes de utilizador podem escolher automaticamente o servidor proxy apropriado (método de acesso) para obter um determinado URL.
   > Os proxies que tentam intercetar e ler todo o tráfego (depois resignar tudo com a sua própria certificação) não são compatíveis, uma vez que o certificado do representante não é de confiança.
   > Os proxies tipicamente transparentes funcionam bem com o Portal da Caixa de Dados Azure.

4. (Opcional) No painel esquerdo, selecione **as definições**de tempo e, em seguida, configure o fuso horário e os servidores NTP primários e secundários para o seu dispositivo. 

    Os servidores NTP são necessários porque o seu dispositivo deve sincronizar o tempo para que possa autenticar com os seus fornecedores de serviços na nuvem.
    
    ![Página local da Web UI "Definições de tempo"](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Na página de definições do **Tempo,** faça o seguinte:
    
    1. Na lista de desmina do **fuso horário,** selecione o fuso horário correspondente à localização geográfica em que o dispositivo é implantado.
        O fuso horário predefinido para o seu dispositivo é PST. O dispositivo utiliza este fuso horário para todas as operações agendadas.

    2. Especifique um **servidor NTP primário** para `time.windows.com`o seu dispositivo ou aceite o valor padrão de .   
        Verifique se a sua rede permite que o tráfego NTP passe do datacenter para a Internet.

    3. Opcionalmente, na caixa de **servidores NTP secundária,** introduza um servidor secundário para o seu dispositivo.

    4. Para validar e aplicar as definições de tempo configuradas, selecione **Aplicar**.

6. No painel esquerdo, selecione **as definições cloud**, e, em seguida, ative o seu dispositivo com o serviço Data Box Gateway no portal Azure.
    
    1. Na caixa **de teclas Ativação,** introduza a **tecla Ativação** que obteve, [Obtenha a chave de ativação](data-box-gateway-deploy-prep.md#get-the-activation-key) para data box Gateway.

    2. **Selecione Ativar**.
       
         ![Página local da Web UI "Cloud settings"](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. O dispositivo é ativado e as atualizações críticas, se disponíveis, são automaticamente aplicadas. Vê uma notificação nesse sentido. Acompanhe o progresso da atualização através do portal Azure.

        ![Página local da Web UI "Cloud settings"](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **O diálogo também tem uma chave de recuperação que deve copiar e guardar num local seguro. Esta chave é usada para recuperar os seus dados caso o dispositivo não possa arrancar.**


    4. Pode ter de esperar vários minutos para que a atualização esteja concluída com sucesso. Depois de concluída a atualização, inscreva-se no dispositivo. As atualizações da página **de definições cloud** indicam que o dispositivo foi ativado com sucesso.

        ![Página local da Web UI "Cloud settings" atualizada](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

A configuração do dispositivo está completa. Agora pode adicionar ações no seu dispositivo.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Ligar a um dispositivo virtual
> * Configurar e ativar o dispositivo virtual

Para aprender a transferir dados com o seu Portal de Data Box, consulte:

> [!div class="nextstepaction"]
> [Transferir dados com Data Box Gateway](./data-box-gateway-deploy-add-shares.md).
