---
title: Ligue-se para configurar e ativar o Gateway de caixa de dados do Azure no portal do Azure | Documentos da Microsoft
description: Terceiro tutorial para implementar o Gateway de caixa de dados instrui a ligar, configurar e ativar o dispositivo virtual.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 84eb458c68c7accf1b638b8e21907516328cb892
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57545095"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway-preview"></a>Tutorial: Ligar, configurar, ativar o Gateway de caixa de dados do Azure (pré-visualização) 

## <a name="introduction"></a>Introdução

Este tutorial descreve como ligar a configurar e ativar o seu dispositivo de Gateway de caixa de dados com a IU web local. 

O processo de instalação e ativação pode demorar cerca de 10 minutos para concluir. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ligar ao dispositivo virtual
> * Configurar e ativar o dispositivo virtual

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


> [!IMPORTANT]
> - O Data Box Gateway está em pré-visualização. Reveja os [Termos de serviço do Azure para pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de encomendar e implementar esta solução. 


## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar o Gateway de caixa de dados, certifique-se de que:

* Ter aprovisionado um dispositivo virtual e obter um URL ligado à mesma, conforme detalhado no [aprovisionar um Gateway de caixa de dados no Hyper-V](data-box-gateway-deploy-provision-hyperv.md) ou [aprovisionar um Gateway de caixa de dados no VMware](data-box-gateway-deploy-provision-vmware.md).
* Tem a chave de ativação do serviço de Gateway de caixa de dados que criou para gerir dispositivos de Gateway de caixa de dados. Para obter mais informações, aceda a [preparar a implementação de Gateway de caixa de dados do Azure](data-box-gateway-deploy-prep.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Ligue-se para a configuração de interface do Usuário da local web 

1. Abra uma janela do browser e ligue à IU da web local. Tipo:
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   Utilize o URL de ligação que anotou no tutorial anterior. Verá um erro que indica que existe um problema com o certificado de segurança do Web site. Clique em **continuar para esta página da Web**. (Estes passos podem ser diferentes com base no navegador utilizado.)
   
    ![Erro durante a ligação](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

2. Inicie sessão para a web da interface do Usuário do seu dispositivo virtual. A palavra-passe predefinido é *Password1*. 
   
    ![Inicie sessão na IU web local](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

3. São-lhe pedido para alterar a palavra-passe de administrador do dispositivo. Escreva uma nova palavra-passe que contenha entre 8 e 16 carateres. A palavra-passe tem de conter 3 dos seguintes: maiúsculas, minúsculas, numéricos e especiais carateres.

    ![Alteração de senha de administrador de dispositivo](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Está agora a **Dashboard** do seu dispositivo.

## <a name="set-up-and-activate-the-virtual-device"></a>Configurar e ativar o dispositivo virtual
 
1. No dashboard, pode aceder a várias definições necessárias para configurar e registar o dispositivo virtual com o serviço de Gateway de caixa de dados. O **as definições de rede**, **definições de proxy Web**, e **definições da hora** são opcionais. As definições necessárias apenas são **nome do dispositivo** e **definições da Cloud**.
   
    ![Web local da interface do Usuário "Dashboard" página](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

2. Na **nome do dispositivo** página, configure um nome amigável para o seu dispositivo. O nome amigável pode ter 1 a 15 carateres e pode conter letras, números e hífenes.

    ![Página de "Nome do dispositivo" da IU local web](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

3. (Opcionalmente) configurar seus **as definições de rede**. Verá a interface de rede, pelo menos, 1 e mais, dependendo de quantos configurado na máquina virtual subjacente. O **as definições de rede** página para um dispositivo virtual com uma interface de rede ativada é conforme mostrado abaixo.
    
    ![Página de "Definições de rede" da IU local web](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Quando configurar as definições de rede, tenha em atenção o seguinte:

    - Se o DHCP estiver ativado no seu ambiente, as interfaces de rede são configuradas automaticamente. Por conseguinte, um endereço IP, sub-rede, gateway e DNS são automaticamente atribuído.
    - Se DHCP não estiver ativado, pode atribuir IPs estáticos, se necessário.
    - Pode configurar a sua interface de rede como IPv4.

    >[!NOTE] 
    > Recomendamos que não muda o endereço IP local da interface de rede de estático para DHCP, a menos que tenha outro endereço IP para ligar ao dispositivo. Se utilizar uma interface de rede e mude para DHCP, não haveria nenhuma forma de determinar o endereço DHCP. Se pretender alterar para um endereço DHCP, esperar até depois do dispositivo foi registado com o serviço e, em seguida, altere. Em seguida, pode ver os IPs de todos os adaptadores na **propriedades do dispositivo** no portal do Azure para o seu serviço.

4. (Opcionalmente)-configure o servidor de proxy da web. Apesar de configuração do proxy web é opcional, lembre-se de que se utilizar um proxy da web, pode apenas configurá-lo aqui.
   
   ![Página de "Definições de proxy da Web" de IU local web](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Na **Web proxy** página:
   
   1. Forneça o **URL de proxy Web** neste formato: *http://&lt;endereço IP do anfitrião ou FQDN&gt;: número de porta*. Tenha em atenção que não são suportados HTTPS URLs.
   2. Especifique **autenticação** como **básica** ou **None**.
   3. Se utilizar a autenticação, também terá de fornecer um **nome de utilizador** e **palavra-passe**.
   4. Clique em **Aplicar**. Isto irá validar e aplicar as definições de proxy da web configurado.

5. (Opcionalmente) configure as definições de hora para o seu dispositivo, como fuso horário e os servidores NTP primário e secundários. Os servidores NTP são necessários porque o dispositivo tem de sincronizar tempo para que ele pode autenticar com o seu fornecedor de serviços cloud.
    
    ![Página de "Definições de tempo" de IU local web](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Na **definições da hora** página:
    
    1. Na lista pendente, selecione o **fuso horário** com base na localização geográfica na qual o dispositivo está a ser implementado. O fuso horário predefinido para o seu dispositivo é PST. O dispositivo utiliza este fuso horário para todas as operações agendadas.
    2. Especifique um **servidor NTP primário** para o seu dispositivo ou aceite o valor predefinido de time.windows.com. Verifique se a sua rede permite que o tráfego NTP passe do datacenter para a Internet.
    3. Opcionalmente, especifique um **servidor NTP secundário** para o seu dispositivo.
    4. Clique em **Aplicar**. Isto irá validar e aplicar as definições de tempo configurado.

6. Na **definições da Cloud** página, ative o seu dispositivo com o serviço de Gateway de caixa de dados no portal do Azure.
    
    1. Introduza o **chave de ativação** obtido [obter a chave de ativação](data-box-gateway-deploy-prep.md#get-the-activation-key) para o Gateway de caixa de dados.

    2. Clique em **ativar**. 
       
         ![Página de "Definições da nuvem" da IU local web](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. Em primeiro lugar, o dispositivo será ativado. O dispositivo, em seguida, é procurado quaisquer atualizações críticas e se estiver disponível, as atualizações são aplicadas automaticamente. Verá uma notificação para o efeito. 

        A caixa de diálogo também tem uma chave de recuperação que deve copiar e guardá-lo numa localização segura. Esta chave é utilizada para recuperar os dados no caso do dispositivo não é possível efetuar o arranque.

        ![Página de "Definições da nuvem" da IU local web](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)    

    4. Terá de aguardar alguns minutos após a atualização é concluída com êxito. A página é atualizada para indicar que o dispositivo é ativado com êxito.

        ![Página de "Definições da nuvem" local web da interface do Usuário atualizada](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a conhecer tópicos do Data Box Gateway, como:

> [!div class="checklist"]
> * Ligar ao dispositivo virtual
> * Configurar e ativar o dispositivo virtual


Avance para o próximo tutorial para saber como transferir dados com o Gateway de caixa de dados.

> [!div class="nextstepaction"]
> [Transferir dados com dados de caixa de Gateway](./data-box-gateway-deploy-add-shares.md).
