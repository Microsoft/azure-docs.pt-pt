---
title: Ligue-se para configurar e ativar um dispositivo de limite de caixa de dados do Azure no portal do Azure | Documentos da Microsoft
description: Terceiro tutorial para implementar dados de caixa de borda instrui a ligar, configurar e ativar o dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 01/09/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 4fd52510abd61c4d319a3fcbc8f722df5edbc476
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120606"
---
# <a name="tutorial-connect-set-up-and-activate-azure-data-box-edge-preview"></a>Tutorial: Ligar, configurar e ativar o Edge de caixa de dados do Azure (pré-visualização) 

Este tutorial descreve como pode ligar, configurar e ativar o seu dispositivo Edge de caixa de dados do Azure, utilizando a IU da web local. 

O processo de instalação e ativação pode demorar cerca de 20 minutos a concluir. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ligar a um dispositivo físico
> * Configurar e ativar o dispositivo físico

> [!IMPORTANT]
> O Data Box Edge está em pré-visualização. Antes de pedir e implementar esta solução, reveja os [do Azure termos de serviço para pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 


## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar o dispositivo do Edge de caixa de dados, certifique-se de que:

* Instalou o dispositivo físico conforme detalhado no [Edge de caixa de dados instalar](data-box-edge-deploy-install.md).
* Tem a chave de ativação do serviço do Edge de caixa de dados que criou para gerir o dispositivo de limite de caixa de dados. Para obter mais informações, aceda a [preparar a implementação de borda de caixa de dados do Azure](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Ligue-se para a configuração de interface do Usuário da local web 

1. Configure o adaptador Ethernet no computador para se ligar ao dispositivo de limite com um endereço IP estático de 192.168.100.5 e sub-rede 255.255.255.0.

1. Liga o computador a 1 de porta no seu dispositivo. 

1. Abra uma janela do browser e o acesso da interface do Usuário do dispositivo no local da web https://192.168.100.10.  
    Esta ação pode demorar alguns minutos depois de a ativar no dispositivo. 

    Verá um erro ou um aviso que indica que existe um problema com o certificado de segurança do Web site. 
   
    ![Mensagem de erro de certificado de segurança de Web site](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

1. Selecione **continuar para esta página da Web**.  
    Estes passos podem variar consoante o browser que estiver a utilizar.

1. Inicie sessão para a web da interface do Usuário do seu dispositivo. A palavra-passe predefinido é *Password1*. 
   
    ![Dados caixa Edge início de sessão na página do dispositivo](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

1. Na linha de comandos, altere a palavra-passe de administrador do dispositivo.  
    A nova palavra-passe tem de conter entre 8 e 16 carateres. Tem de conter três dos seguintes carateres: carateres em maiúsculas, minúsculas, numéricos e especiais.

Agora, está no dashboard do seu dispositivo.

## <a name="set-up-and-activate-the-physical-device"></a>Configurar e ativar o dispositivo físico
 
O dashboard apresenta as várias configurações que são necessárias para configurar e registar o dispositivo físico com o serviço de borda de caixa de dados. O **nome do dispositivo**, **as definições de rede**, **definições de proxy Web**, e **definições da hora** são opcionais. As definições necessárias apenas são **definições da Cloud**.
   
![O dashboard de dispositivo do Edge de caixa de dados](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. No painel esquerdo, selecione **nome do dispositivo**e, em seguida, introduza um nome amigável para o seu dispositivo.  
    O nome amigável tem de conter entre 1 e 15 carateres e conter letras, números e hífenes.

    ![A página de "Nome do dispositivo"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

1. (Opcional) No painel esquerdo, selecione **as definições de rede** e, em seguida, configure as definições.  
    No seu dispositivo físico são seis interfaces de rede. PORTA 1 e 2 de porta são interfaces de rede de 1 Gbps. PORTA 3, 4 de porta, porta 5 e 6 de porta são todas as interfaces de rede de 25 Gbps. PORTA 1 é automaticamente configurada como uma porta de gestão só e porta 2 a 6 de porta são todas as portas de dados. O **as definições de rede** página conforme mostrado abaixo.
    
    ![A página "Definições de rede"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    À medida que configura as definições de rede, tenha em mente:

   - Se o DHCP estiver ativado no seu ambiente, as interfaces de rede são configuradas automaticamente. Um endereço IP, sub-rede, gateway e DNS é atribuído automaticamente.
   - Se o DHCP não está ativado, pode atribuir IPs estáticos, se necessário.
   - Pode configurar a sua interface de rede como IPv4.

     >[!NOTE] 
     > Recomendamos que não muda o endereço IP local da interface de rede de estático para DHCP, a menos que tenha outro endereço IP para ligar ao dispositivo. Se utilizar uma interface de rede e mude para DHCP, não haveria nenhuma forma de determinar o endereço DHCP. Se pretender alterar para um endereço DHCP, esperar até depois do dispositivo foi registado com o serviço e, em seguida, altere. Em seguida, pode ver os IPs de todos os adaptadores na **propriedades do dispositivo** no portal do Azure para o seu serviço.

1. (Opcional) No painel esquerdo, selecione **definições de proxy Web**e, em seguida, configure o servidor de proxy da web. Apesar de configuração do proxy web é opcional, se utilizar um proxy de web, pode configurá-lo apenas nesta página.
   
   ![A página "Definições de proxy da Web"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Sobre o **definições de proxy Web** página, efetue o seguinte:
   
   a. Na **URL de proxy Web** , introduza o URL no seguinte formato: `http://host-IP address or FQDN:Port number`. Não são suportados HTTPS URLs.

   b. Sob **autenticação**, selecione **None** ou **NTLM**.

   c. Se estiver a utilizar a autenticação, introduza um nome de utilizador e palavra-passe.

   d. Para validar e aplicar as definições de proxy da web configurado, selecione **aplicar definições**.

1. (Opcional) No painel esquerdo, selecione **definições da hora**e, em seguida, configure o fuso horário e os servidores NTP primários e secundários para o seu dispositivo.  
    Os servidores NTP são necessários porque o dispositivo tem de sincronizar tempo para que ele pode autenticar com o seu fornecedor de serviços cloud.
    
    ![A página "Definições de hora"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)
    
    Sobre o **definições da hora** página, efetue o seguinte:
    
    a. Na **fuso horário** pendente, selecione o fuso horário que corresponde à localização geográfica na qual o dispositivo está a ser implementado.  
        O fuso horário predefinido para o seu dispositivo é PST. O dispositivo utiliza este fuso horário para todas as operações agendadas.

    b. Na **servidor NTP primário** caixa, introduza o servidor primário para o seu dispositivo ou aceite o valor predefinido de time.windows.com.  
        Certifique-se de que a sua rede permite tráfego NTP passe a partir do seu datacenter para a internet.

    c. Opcionalmente, na **servidor NTP secundário** , introduza um servidor secundário para o seu dispositivo.

    d. Para validar e aplicar as definições de tempo configurado, selecione **aplicar**.

6. No painel esquerdo, selecione **definições da Cloud**e, em seguida, ative o seu dispositivo com o serviço de borda de caixa de dados no portal do Azure.
    
    a. Na **chave de ativação** , introduza a chave de ativação que obteve na [obter a chave de ativação](data-box-edge-deploy-prep.md#get-the-activation-key) para o Edge de caixa de dados.

    b. Selecione **Aplicar**. 
       
    ![A página "Definições da nuvem"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)
    
    Depois do dispositivo é ativado com êxito, é apresentada com opções de modo de conectividade. Estas definições são configuradas, se precisar de trabalhar com o dispositivo no modo parcialmente desconectado ou desligado. 

    ![A confirmação de ativação "Definições da Cloud"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)    

A configuração do dispositivo foi concluída. Agora pode adicionar partilhas no seu dispositivo.


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Ligar a um dispositivo físico
> * Configurar e ativar o dispositivo físico


Para saber como transferir dados com o seu dispositivo do Edge de caixa de dados, veja:

> [!div class="nextstepaction"]
> [Transferir dados com dados de caixa de borda](./data-box-edge-deploy-add-shares.md).
