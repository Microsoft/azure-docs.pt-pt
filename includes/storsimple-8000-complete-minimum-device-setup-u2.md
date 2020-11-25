---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 867cdc97ff91d5932230b733dee4d7660d499c39
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026781"
---
#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>Para concluir a configuração mínima do dispositivo StorSimple

   > [!NOTE]
   > Não pode alterar o nome do dispositivo depois de a configuração mínima do dispositivo estiver concluída.
   
1. A partir da lista tabular de dispositivos no painel **Dispositivos**, selecione e clique no seu dispositivo. O dispositivo está num estado **Preparado para configurar**. O painel **Configurar dispositivo** é aberto.

     ![Interfaces de rede para a configuração mínima do dispositivo StorSimple](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig1.png)

2. No painel **Configurar dispositivo**:
   
   1. Forneça um **nome amigável** para o dispositivo. O nome predefinido do dispositivo reflete informações como o modelo e número de série do dispositivo. Pode atribuir um nome amigável com um máximo de 64 carateres para gerir o dispositivo.
   2. Defina o **fuso horário** com base na localização geográfica onde o dispositivo está a ser implementado. O dispositivo utiliza este fuso horário para todas as operações agendadas.
   3. Sob as **definições de DADOS 0**:

       1. A interface de rede de DADOS 0 é apresentada quando está ativada com as definições de rede (IP, sub-rede, gateway) configuradas através do assistente de configuração. Os DADOS 0 também são automaticamente ativados para a cloud, bem como a iSCSI.

       2. Indique os endereços IP fixos para o Controlador 0 e o Controlador 1. **Os endereços IP fixos do controlador têm de ser IPs livres na sub-rede acessível pelo endereço IP do dispositivo.** Se a interface DATA 0 tiver sido configurada para IPv4, os endereços IP fixos têm de ser fornecidos no formato IPv4. Se tiver indicado um prefixo para a configuração IPv6, os endereços IP fixos são preenchidos automaticamente nestes campos.

            ![Interfaces de rede de configuração de dispositivos mínimos StorSimple 2](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig2.png)

            Os endereços IP fixos do controlador servem para a manutenção de atualizações do dispositivo e para a libertação da memória. Por conseguinte, os IPs fixos têm de ser encaminháveis e conseguir estabelecer uma ligação à Internet. Pode verificar se os IPs fixos do controlador fixo são encaminháveis com o cmdlet [Test-HcsmConnection][Test]. O exemplo seguinte mostra IPs fixos do controlador a serem encaminhados para a Internet e a poderem aceder os servidores do Microsoft Update.

            ![Test-HcsmConnection a mostrar os IPs encaminháveis](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig3.png)

1. Clique em **OK**. A configuração do dispositivo inicia. Quando a configuração do dispositivo estiver concluída, será notificado. O estado do dispositivo muda para **Online** no painel **Dispositivos**.

    ![Interfaces de rede de configuração de dispositivos mínimos StorSimple 3](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig4.png)

<!--Link reference-->
[Test]: /previous-versions/windows/powershell-scripting/dn715782(v=wps.630)