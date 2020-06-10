---
title: Tutorial para ligar, configurar, ativar o dispositivo Azure Stack Edge no portal Azure Microsoft Docs
description: Tutorial para implementar Azure Stack Edge instrui-o a ligar, configurar e ativar o seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: e0a0d9415cc55c24bb4dc0690c73d9f79fc0ce0e
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84608439"
---
# <a name="tutorial-connect-set-up-and-activate-azure-stack-edge"></a>Tutorial: Conecte-se, instale e ative o Azure Stack Edge 

Este tutorial descreve como pode ligar-se, configurar e ativar o seu dispositivo Azure Stack Edge utilizando a UI web local.

O processo de configuração e ativação pode demorar cerca de 20 minutos a ser concluído.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
>
> * Ligar-se a um dispositivo físico
> * Configurar e ativar o dispositivo físico

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar o seu dispositivo Azure Stack Edge, certifique-se de que:

* Instalou o dispositivo físico conforme detalhado na [Instalação Azure Stack Edge](azure-stack-edge-deploy-install.md).
* Tem a chave de ativação do serviço Azure Stack Edge que criou para gerir o dispositivo Azure Stack Edge. Para mais informações, vá a [Preparar-se para implementar o Azure Stack Edge](azure-stack-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Conecte-se à configuração local do UI web

1. Configure o adaptador Ethernet no seu computador para ligar ao dispositivo Azure Stack Edge com um endereço IP estático de 192.168.100.5 e sub-rede 255.255.255.0.

2. Ligue o computador ao PORTO 1 no seu dispositivo. Utilize a seguinte ilustração para identificar a PORTA 1 no seu dispositivo.

    ![Painel posterior de um dispositivo ligado por cabo](./media/azure-stack-edge-deploy-install/backplane-cabled.png)

3. Abra uma janela do navegador e aceda à UI web local do dispositivo em `https://192.168.100.10` .  
    Esta ação pode demorar alguns minutos depois de ter ligado o dispositivo.

    Você vê um erro ou um aviso indicando que há um problema com o certificado de segurança do site.
   
    ![Mensagem de erro do certificado de segurança do site](./media/azure-stack-edge-deploy-connect-setup-activate/image2.png)

4. **Selecione Continue a esta página web**.  
    Estes passos podem variar dependendo do navegador que você está usando.

5. Inscreva-se na UI web do seu dispositivo. A palavra-passe predefinida é *Palavra-passe1*. 
   
    ![Página de entrada do dispositivo Azure Stack Edge](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. A pedido, altere a palavra-passe do administrador do dispositivo.  
    A nova palavra-passe deve conter entre 8 e 16 caracteres. Deve conter três dos seguintes caracteres: maiúsculas, minúsculas, numéricas e caracteres especiais.

Está agora no painel do seu dispositivo.

## <a name="set-up-and-activate-the-physical-device"></a>Configurar e ativar o dispositivo físico
 
O seu painel de instrumentos exibe as várias definições necessárias para configurar e registar o dispositivo físico com o serviço Azure Stack Edge. O **nome do dispositivo,** **as definições de rede,** **as definições de procuração web**e as **definições de tempo** são opcionais. As únicas definições necessárias são **as definições cloud**.
   
![Página local de UI "Dashboard"](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. No painel esquerdo, selecione **o nome do dispositivo**e, em seguida, introduza um nome amigável para o seu dispositivo.  
    O nome amigável deve conter de 1 a 15 caracteres e ter letras, números e hífens.

    ![Página local de ui "nome do dispositivo"](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-2.png)

2. (Opcional) No painel esquerdo, selecione **as definições de rede** e, em seguida, configufique as definições.  
    No seu dispositivo físico, existem seis interfaces de rede. PORT 1 e PORT 2 são interfaces de rede de 1 Gbps. PORT 3, PORT 4, PORT 5 e PORT 6 são todas interfaces de rede de 25 Gbps que também podem servir como interfaces de rede de 10 Gbps. O PORT 1 é configurado automaticamente como uma porta só de gestão, e o PORTO 2 para o PORTO 6 são todas portas de dados. A página **de definições de Rede** é como mostrado abaixo.
    
    ![Página local de ui "definições de rede"](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Ao configurar as definições da rede, lembre-se:

   - Se o DHCP estiver ativado no seu ambiente, as interfaces de rede são configuradas automaticamente. Um endereço IP, sub-rede, gateway e DNS são automaticamente atribuídos.
   - Se o DHCP não estiver ativado, pode atribuir IPs estáticos se necessário.
   - Pode configurar a sua interface de rede como IPv4.

     >[!NOTE] 
     > Recomendamos que não altere o endereço IP local da interface de rede de estática para DCHP, a menos que tenha outro endereço IP para ligar ao dispositivo. Se utilizar uma interface de rede e mudar para DHCP, não haverá forma de determinar o endereço DHCP. Se pretender alterar para um endereço DHCP, aguarde até depois de o dispositivo ter registado o serviço e, em seguida, alterar. Em seguida, pode visualizar os IPs de todos os adaptadores nas propriedades do **Dispositivo** no portal Azure para o seu serviço.

3. (Opcional) No painel esquerdo, selecione **as definições de procuração web**e, em seguida, configufique o seu servidor de procuração web. Embora a configuração de procuração web seja opcional, se utilizar um representante web, pode configugá-la apenas nesta página.
   
   ![Página local de UI "Web proxy settings"](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Na página de **definições de procuração** web, faça o seguinte:
   
   a. Na caixa **DE URL de procuração web,** introduza o URL neste formato: `http://host-IP address or FQDN:Port number` . OS URLs HTTPS não são suportados.

   b. Em **Autenticação**, **selecione Nenhum** ou **NTLM**. Se ativar o módulo IoT Edge no seu dispositivo Azure Stack Edge, recomendamos que detenda a autenticação por procuração web a **Nenhum**. **A NTLM** não é suportada.

   c. Se estiver a utilizar a autenticação, insira um nome de utilizador e uma palavra-passe.

   d. Para validar e aplicar as definições de procuração web configuradas, selecione **Aplicar as definições**.

   > [!NOTE]
   > Os ficheiros Proxy-auto config (PAC) não são suportados. Um ficheiro PAC define como os navegadores web e outros agentes do utilizador podem escolher automaticamente o servidor proxy apropriado (método de acesso) para obter um determinado URL.
   > Os proxies que tentam intercetar e ler todo o tráfego (em seguida, re-assinar tudo com a sua própria certificação) não são compatíveis uma vez que o cert do proxy não é confiável.
   > Os proxies tipicamente transparentes funcionam bem com o Azure Stack Edge.

4. (Opcional) No painel esquerdo, selecione definições de tempo e, em seguida, configufique o **fuso**horário e os servidores NTP primários e secundários para o seu dispositivo.  
    Os servidores NTP são necessários porque o seu dispositivo deve sincronizar o tempo para que possa autenticar com os seus fornecedores de serviços na nuvem.
       
    Na página **'Definições',** faça o seguinte:
    
    1. Na lista de down-down do **fuso** horário, selecione o fuso horário que corresponde à localização geográfica em que o dispositivo está a ser implantado.
        O fuso horário padrão para o seu dispositivo é PST. O dispositivo utiliza este fuso horário para todas as operações agendadas.

    2. Na caixa **do servidor NTP primário,** introduza o servidor primário para o seu dispositivo ou aceite o valor padrão de time.windows.com.  
        Certifique-se de que a sua rede permite que o tráfego NTP passe do seu datacenter para a internet.

    3. Opcionalmente, na caixa **de servidor NTP secundária,** introduza um servidor secundário para o seu dispositivo.

    4. Para validar e aplicar as definições de tempo configuradas, **selecione Aplicar as definições**.

        ![Página local de UI "Definições de tempo"](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-5.png)

5. (Opcional) No painel esquerdo, selecione **as definições de armazenamento** para configurar a resiliência de armazenamento no seu dispositivo. Esta funcionalidade encontra-se em pré-visualização. Por predefinição, o armazenamento no dispositivo não é resiliente e existe perda de dados se um disco de dados falhar no dispositivo. Quando ativar a opção Resilient, o armazenamento no dispositivo será reconfigurado e o dispositivo pode resistir à falha de um disco de dados sem perda de dados. Configurar o armazenamento como resistente reduzirá a capacidade utilizável do seu dispositivo.

    > [!IMPORTANT] 
    > A resiliência só pode ser configurada antes de ativar o dispositivo. 

    ![Página local de ui "definições de armazenamento"](./media/azure-stack-edge-deploy-connect-setup-activate/storage-settings.png)

6. No painel esquerdo, selecione **as definições cloud**e, em seguida, ative o seu dispositivo com o serviço Azure Stack Edge no portal Azure.
    
    1. Na caixa **de chave ativação,** introduza a tecla de ativação que obteve na [Obter a chave de ativação](azure-stack-edge-deploy-prep.md#get-the-activation-key) para Azure Stack Edge.
    2. Selecione **Aplicar**.
       
        ![Página local de UI "Cloud Settings"](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-6.png)

    3. Primeiro o dispositivo é ativado. O dispositivo é então digitalizado para quaisquer atualizações críticas e, se disponível, as atualizações são automaticamente aplicadas. Vê uma notificação nesse sentido.

        O diálogo também tem uma chave de recuperação que deve copiar e guardá-la num local seguro. Esta chave é utilizada para recuperar os seus dados no caso de o dispositivo não conseguir arrancar.

        ![Página local de UI "Cloud Settings" atualizada](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-7.png)

    4. Poderá ter de esperar vários minutos após a conclusão da atualização. As atualizações da página indicam que o dispositivo está ativado com sucesso.

        ![Página local de UI "Cloud Settings" atualizada](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-8.png)

A configuração do dispositivo está completa. Agora pode adicionar ações no seu dispositivo.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Ligar-se a um dispositivo físico
> * Configurar e ativar o dispositivo físico

Para aprender a transferir dados com o seu dispositivo Azure Stack Edge, consulte:

> [!div class="nextstepaction"]
> [Transferir dados com a Azure Stack Edge](./azure-stack-edge-deploy-add-shares.md).
