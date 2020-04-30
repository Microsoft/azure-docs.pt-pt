---
title: Tutorial para ligar, configurar, ativar dispositivo Azure Stack Edge no portal Azure [ Microsoft Docs
description: Tutorial para implementar O Edge De Stack Azur instrui-o a ligar, configurar e ativar o seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 7c8a35b2699035b3ce4f96a94ca970da2cf343c4
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82570609"
---
# <a name="tutorial-connect-set-up-and-activate-azure-stack-edge"></a>Tutorial: Ligar, configurar e ativar a Borda da Pilha Azure 

Este tutorial descreve como pode ligar, configurar e ativar o seu dispositivo Azure Stack Edge utilizando a UI web local.

O processo de configuração e ativação pode demorar cerca de 20 minutos a ser concluído.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ligar a um dispositivo físico
> * Configurar e ativar o dispositivo físico

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar o seu dispositivo Azure Stack Edge, certifique-se de que:

* Instalou o dispositivo físico conforme detalhado na [Instalação de Edge De Stack Azure](azure-stack-edge-deploy-install.md).
* Tem a chave de ativação do serviço Azure Stack Edge que criou para gerir o dispositivo Azure Stack Edge. Para mais informações, vá ao [Prepare-se para implementar o Azure Stack Edge](azure-stack-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Ligue-se à configuração de UI web local 

1. Configure o adaptador Ethernet no seu computador para ligar ao dispositivo Azure Stack Edge com um endereço IP estático de 192.168.100.5 e subnet 255.255.255.0.

2. Ligue o computador à PORTA 1 no seu dispositivo. Utilize a seguinte ilustração para identificar a PORTA 1 no seu dispositivo.

    ![Painel posterior de um dispositivo ligado por cabo](./media/azure-stack-edge-deploy-install/backplane-cabled.png)


3. Abra uma janela do navegador e aceda `https://192.168.100.10`à UI web local do dispositivo em .  
    Esta ação pode demorar alguns minutos depois de ligar o dispositivo. 

    Você vê um erro ou um aviso indicando que há um problema com o certificado de segurança do site. 
   
    ![Mensagem de erro de certificado de segurança do site](./media/azure-stack-edge-deploy-connect-setup-activate/image2.png)

4. Selecione **Continuar a esta página web**.  
    Estes passos podem variar dependendo do navegador que está a usar.

5. Inscreva-se na Web UI do seu dispositivo. A palavra-passe padrão é *Password1*. 
   
    ![Página de sinalização do dispositivo Azure Stack Edge](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. No momento, altere a palavra-passe do administrador do dispositivo.  
    A nova senha deve conter entre 8 e 16 caracteres. Deve conter três dos seguintes caracteres: maiúsculas, minúsculas, numéricas e caracteres especiais.

Está agora no painel do seu dispositivo.

## <a name="set-up-and-activate-the-physical-device"></a>Configurar e ativar o dispositivo físico
 
O seu painel de instrumentos apresenta as várias definições necessárias para configurar e registar o dispositivo físico com o serviço Azure Stack Edge. O **nome do dispositivo,** **definições**de rede, definições de **procuração web**e **definições** de tempo são opcionais. As únicas definições necessárias são **as definições cloud**.
   
![Página local da UI "Dashboard"](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. No painel esquerdo, selecione o **nome do Dispositivo**e, em seguida, introduza um nome amigável para o seu dispositivo.  
    O nome amigável deve conter de 1 a 15 caracteres e ter letras, números e hífenes.

    ![Página local da UI "Nome do dispositivo"](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-2.png)

2. (Opcional) No painel esquerdo, selecione **as definições** de rede e, em seguida, configure as definições.  
    No seu dispositivo físico, existem seis interfaces de rede. PORTA 1 e PORTA 2 são interfaces de rede de 1-Gbps. PORTA 3, PORTA 4, PORTA 5 e PORTA 6 são todas interfaces de rede de 25 Gbps que também podem servir como interfaces de rede de 10 Gbps. A PORTA 1 é configurada automaticamente como uma porta só de gestão, e a PORTA 2 para a PORTA 6 são todas as portas de dados. A página de **definições** da Rede é como mostrado abaixo.
    
    ![Página local da Web UI "Definições de rede"](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    À medida que configura as definições da rede, tenha em mente:

   - Se o DHCP estiver ativado no seu ambiente, as interfaces de rede são configuradas automaticamente. Um endereço IP, sub-rede, gateway e DNS são automaticamente atribuídos.
   - Se o DHCP não estiver ativado, pode atribuir IPs estáticos se necessário.
   - Pode configurar a interface de rede como IPv4.

     >[!NOTE] 
     > Recomendamos que não altere o endereço IP local da interface de rede de estática para DCHP, a menos que tenha outro endereço IP para ligar ao dispositivo. Se utilizar uma interface de rede e mudar para DHCP, não existiria forma de determinar o endereço DHCP. Se quiser mudar para um endereço DHCP, aguarde até que o dispositivo se tenha registado no serviço e, em seguida, mude. Em seguida, pode visualizar os IPs de todos os adaptadores nas propriedades do **Dispositivo** no portal Azure para o seu serviço.

3. (Opcional) No painel esquerdo, selecione **as definições**de proxy web e, em seguida, configure o seu servidor de proxy web. Embora a configuração de procuração web seja opcional, se utilizar um proxy web, pode configurá-la apenas nesta página.
   
   ![Página local da Web UI "Web proxy settings"](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Na página de definições de **procuração web,** faça o seguinte:
   
   a. Na caixa URL proxy **Web,** introduza `http://host-IP address or FQDN:Port number`o URL neste formato: . OS URLs HTTPS não são suportados.

   b. Em **Autenticação,** selecione **Nenhum** ou **NTLM**.

   c. Se estiver a utilizar a autenticação, introduza um nome de utilizador e uma senha.

   d. Para validar e aplicar as definições configuradas de procuração web, selecione **Aplicar as definições**.
   
   > [!NOTE]
   > Os ficheiros proxy-auto config (PAC) não são suportados. Um ficheiro PAC define como os navegadores web e outros agentes de utilizador podem escolher automaticamente o servidor proxy apropriado (método de acesso) para obter um determinado URL.
   > Os proxies que tentam intercetar e ler todo o tráfego (depois resignar tudo com a sua própria certificação) não são compatíveis, uma vez que o certificado do representante não é de confiança.
   > Os proxies tipicamente transparentes funcionam bem com o Azure Stack Edge.

4. (Opcional) No painel esquerdo, selecione **as definições**de tempo e, em seguida, configure o fuso horário e os servidores NTP primários e secundários para o seu dispositivo.  
    Os servidores NTP são necessários porque o seu dispositivo deve sincronizar o tempo para que possa autenticar com os seus fornecedores de serviços na nuvem.
       
    Na página de definições do **Tempo,** faça o seguinte:
    
    1. Na lista de desmina do **fuso horário,** selecione o fuso horário correspondente à localização geográfica em que o dispositivo está a ser implantado.
        O fuso horário predefinido para o seu dispositivo é PST. O dispositivo utiliza este fuso horário para todas as operações agendadas.

    2. Na caixa de **servidorNTP Primária,** introduza o servidor principal para o seu dispositivo ou aceite o valor padrão de time.windows.com.  
        Certifique-se de que a sua rede permite que o tráfego ntp passe do seu datacenter para a internet.

    3. Opcionalmente, na caixa de **servidores NTP secundária,** introduza um servidor secundário para o seu dispositivo.

    4. Para validar e aplicar as definições de tempo configuradas, selecione **Aplicar as definições**.

        ![Página local da Web UI "Definições de tempo"](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-5.png)

5. (Opcional) No painel esquerdo, selecione **as definições** de armazenamento para configurar a resiliência de armazenamento no seu dispositivo. Esta funcionalidade encontra-se em pré-visualização. Por predefinição, o armazenamento no dispositivo não é resiliente e há perda de dados se um disco de dados falhar no dispositivo. Quando ativa a opção Resilient, o armazenamento no dispositivo será reconfigurado e o dispositivo pode resistir à falha de um disco de dados sem perda de dados. Configurar o armazenamento como resiliente reduzirá a capacidade utilizável do seu dispositivo.

    > [!IMPORTANT] 
    > A resiliência só pode ser configurada antes de ativar o dispositivo. 

    ![Página local da Web UI "Definições de armazenamento"](./media/azure-stack-edge-deploy-connect-setup-activate/storage-settings.png)

6. No painel esquerdo, selecione **as definições cloud**, e, em seguida, ative o seu dispositivo com o serviço Azure Stack Edge no portal Azure.
    
    1. Na caixa de **teclas Ativação,** introduza a tecla de ativação que obteve no [Get the activation key](azure-stack-edge-deploy-prep.md#get-the-activation-key) for Azure Stack Edge.
    2. Selecione **Aplicar**.
       
        ![Página local da Web UI "Cloud settings"](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-6.png)

    3. Primeiro o dispositivo é ativado. O dispositivo é então digitalizado para quaisquer atualizações críticas e, se disponível, as atualizações são automaticamente aplicadas. Vê uma notificação nesse sentido.

        O diálogo também tem uma chave de recuperação que deve copiar e guardá-la num local seguro. Esta chave é usada para recuperar os seus dados caso o dispositivo não possa arrancar.

        ![Página local da Web UI "Cloud settings" atualizada](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-7.png)

    4. Pode ter de esperar alguns minutos depois de a atualização estar concluída com sucesso. As atualizações da página indicam que o dispositivo foi ativado com sucesso.

        ![Página local da Web UI "Cloud settings" atualizada](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-8.png)

A configuração do dispositivo está completa. Agora pode adicionar ações no seu dispositivo.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Ligar a um dispositivo físico
> * Configurar e ativar o dispositivo físico

Para aprender a transferir dados com o seu dispositivo Azure Stack Edge, consulte:

> [!div class="nextstepaction"]
> [Transferir dados com Azure Stack Edge](./azure-stack-edge-deploy-add-shares.md).
