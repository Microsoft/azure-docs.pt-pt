---
title: Modificar a configuração de dispositivos de série 8000 do StorSimple | Documentos da Microsoft
description: Descreve como utilizar o serviço StorSimple Device Manager para reconfigurar um dispositivo do StorSimple que já tenha sido implementado.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 774f5a73a5fc30352698c0af0c279fbbe488c480
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58008387"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>Utilizar o serviço StorSimple Device Manager para modificar a configuração de dispositivos do StorSimple

## <a name="overview"></a>Descrição geral

O portal do Azure **definições do dispositivo** secção a **definições** painel contém todos os parâmetros de dispositivo que pode reconfigurar num dispositivo StorSimple que é gerido por um serviço StorSimple Device Manager . Este tutorial explica como pode utilizar o **definições** painel para realizar as seguintes tarefas de ao nível do dispositivo:

* Modificar o nome amigável do dispositivo
* Modificar as definições de hora do dispositivo
* Atribuir um DNS secundário
* Modificar as interfaces de rede
* Trocar ou reatribuir IPs

## <a name="modify-device-friendly-name"></a>Modificar o nome amigável do dispositivo

Pode utilizar o portal do Azure para alterar o nome do dispositivo e atribua-lhe um nome amigável exclusivo da sua preferência. Utilize o **definições gerais** painel no seu dispositivo para modificar o nome amigável do dispositivo. O nome amigável pode conter quaisquer carateres e pode ter um máximo de 64 carateres de comprimento.

> [!NOTE] 
> Só é possível modificar o nome do dispositivo no portal do Azure antes da configuração do dispositivo está concluída. Depois de concluída a configuração mínima do dispositivo, não é possível alterar o nome do dispositivo.

![Dispositivo nome, em geral, as definições](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

Um dispositivo do StorSimple que está ligado ao serviço StorSimple Device Manager é atribuído um nome predefinido. O nome padrão refletirá, normalmente, o número de série do dispositivo. Por exemplo, um nome de dispositivo predefinido que é de 15 carateres de comprimento, tais como 8600-SHX0991003G44HT, indica o seguinte:

* **8600** – indica o modelo do dispositivo.
* **SHX** – indica o site de produção.
* **0991003** -indica um produto específico.
* **G44HT**-os últimos 5 dígitos são aumentados para criar os números de série exclusivos. Não pode ser um conjunto seqüencial.

## <a name="modify-device-description"></a>Modificação da descrição do dispositivo

Utilize o **definições gerais** painel no seu dispositivo para modificar a descrição do dispositivo.

![Descrição do dispositivo nas definições gerais](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

Uma descrição do dispositivo, normalmente, ajuda a identificar o proprietário e a localização física do dispositivo. O campo de descrição tem de conter menos de 256 carateres.

## <a name="modify-time-settings"></a>Modificar as definições de hora

O dispositivo tem de sincronizar tempo para se autenticar com o seu fornecedor de serviços de armazenamento na cloud. Utilize o **definições gerais** painel no seu dispositivo para modificar as definições de hora do dispositivo.

![Descrição do dispositivo nas definições gerais](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 Selecione o seu fuso horário na lista pendente. Pode especificar até dois servidores de protocolo NTP (Network Time):

 - **Servidor NTP primário** -a configuração é necessária e é especificada quando utilizam o Windows PowerShell para StorSimple para configurar o seu dispositivo. Pode especificar o padrão do Windows Server **time.windows.com** como o servidor NTP. Pode ver a configuração do servidor NTP primária através do portal do Azure, mas tem de utilizar a interface do Windows PowerShell para alterá-la. Utilize o `Set-HcsNTPClientServerAddress` cmdlet para modificar o servidor NTP primário do seu dispositivo. Para obter mais informações, aceda a sintaxe para [Set-HcsNTPClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) cmdlet.

- **O servidor NTP secundário** -a configuração é opcional. Pode utilizar o portal para configurar um servidor NTP secundário.

Ao configurar o servidor NTP, certifique-se de que a sua rede permite o tráfego NTP passe do seu datacenter para a Internet. Ao especificar um servidor NTP público, deve certificar-se de que as firewalls de rede e outros dispositivos de segurança estão configurados para permitir o tráfego NTP viajar para e partir da rede externa. Se o tráfego NTP bidirecional não é permitido, tem de utilizar um servidor NTP interno (um controlador de domínio do Windows fornece essa função). Se o seu dispositivo não é possível sincronizar a hora, poderá não conseguir comunicar com o seu fornecedor de armazenamento na cloud.

Para ver uma lista de servidores NTP públicos, vá para o [NTP servidores Web](https://support.ntp.org/bin/view/Servers/WebHome).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>O que acontece se o dispositivo é implantado num fuso horário diferente?

Se o dispositivo é implantado num fuso horário diferente, irá alterar o fuso horário do dispositivo. Dado que todas as políticas de cópia de segurança, utilize o fuso horário do dispositivo, as políticas de cópia de segurança serão ajustado automaticamente em conformidade com o novo fuso horário. Não é necessária nenhuma intervenção do utilizador.

## <a name="modify-dns-settings"></a>Modificar as definições de DNS

Um servidor DNS é utilizado quando o dispositivo tenta comunicar com o seu fornecedor de serviços de armazenamento na cloud. Utilize o **as definições de rede** painel no seu dispositivo para ver e modificar as definições de DNS configuradas. 

![Definições de DNS nas definições de rede](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

Para elevada disponibilidade, tem de configurar a primária e os servidores DNS secundários durante a implementação inicial do dispositivo.

**Servidor DNS primário** -é usar o Windows PowerShell para StorSimple para primeiro especificar o servidor DNS primário durante a configuração inicial. Pode reconfigurar o servidor DNS principal apenas através da interface do Windows PowerShell. Utilize o `Set-HcsDNSClientServerAddress` cmdlet para modificar o servidor DNS primário do seu dispositivo. Para obter mais informações, aceda a sintaxe para [Set-HcsDNSClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) cmdlet.

**Servidor DNS secundário** - para modificar o servidor DNS secundário, utilize o `Set-HcsDNSClientServerAddress` cmdlet na interface do Windows PowerShell do dispositivo ou **as definições de rede** painel do dispositivo StorSimple no portal do Azure.

Para modificar o servidor DNS secundário no portal do Azure, execute os seguintes passos.

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Na lista de dispositivos, selecione e clique no seu dispositivo.

2. Na **definições** painel, aceda à **definições do dispositivo > rede**. Esta ação abre o **as definições de rede** painel. Clique em **as definições de DNS** mosaico. Modificar o endereço IP de servidor DNS secundário.

    ![Modificar o endereço IP do servidor DNS secundário](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. Na barra de comandos, clique em **salvar** e quando lhe for pedida confirmação, clique em **OK**.

    ![Guardar e confirme as alterações](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Modificar as interfaces de rede

O dispositivo tem seis interfaces de rede de dispositivo, quatro delas são de 1 GbE e duas das quais são os 10 GbE. Essas interfaces são identificadas como dados 0 – 5 de dados. DATA 0, 1 de dados, dados 4 e 5 de dados é de 1 GbE, enquanto os dados 2 e 3 de dados são interfaces de rede de 10 GbE.

Utilize o **as definições de rede** painel configurar cada uma das interfaces a ser utilizado.

![Configurar as interfaces de rede através das definições de rede](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

Para garantir a elevada disponibilidade, recomendamos que tem, pelo menos, duas interfaces de iSCSI e duas interfaces de capacidade de cloud no seu dispositivo. Recomendamos, mas não exigem que as interfaces não utilizadas ser desativada.

Para cada interface de rede, são apresentados os seguintes parâmetros:

* **Velocidade** – não é um parâmetro configurável pelo utilizador. DATA 0, 1 de dados, dados 4 e 5 de dados é sempre de 1 GbE, enquanto os dados 2 e 3 de dados são interfaces de 10 GbE.
  
  > [!NOTE]
  > Velocidade e duplex são sempre auto-negociados. Frames Jumbo não é suportados.
  
* **Estado da interface** – uma interface pode ser ativada ou desabilitada. Se estiver ativada, o dispositivo tentará utilizar a interface. Recomendamos que apenas essas interfaces que são ligados à rede e utilizados esteja ativada. Desative qualquer interface que não estiver a utilizar.
* **Tipo de interface** – este parâmetro permite-lhe isolar o tráfego de iSCSI do tráfego de armazenamento na cloud. Este parâmetro pode ser um dos seguintes:
  
  * **Cloud ativada** – quando ativada, o dispositivo irá utilizar essa interface para comunicar com a cloud.
  * **compatível com iSCSI** – quando ativada, o dispositivo irá utilizar essa interface para comunicar com o anfitrião iSCSI.
    
    Recomendamos que isole o tráfego de iSCSI do tráfego de armazenamento na cloud. Observe também que se o seu anfitrião estiver na mesma sub-rede que o seu dispositivo, não é necessário atribuir um gateway; No entanto, se o seu anfitrião estiver numa sub-rede diferente, que o seu dispositivo, terá de atribuir um gateway.
* **Endereço IP** – quando configurar qualquer uma das interfaces de rede, tem de configurar um IP virtual (VIP). Isso pode ser IPv4 ou IPv6 ou ambos. Famílias de endereços IPv4 e IPv6 são suportadas para as interfaces de rede do dispositivo. Ao utilizar o IPv4, especifique um endereço IP de 32 bits (*xxx.xxx.xxx.xxx-xxx.xxx.xxx.xxx*) na notação de ponto decimal. Ao utilizar o IPv6, simplesmente fornecer um prefixo de 4 dígitos e um endereço de 128 bits será gerado automaticamente para a sua interface de rede do dispositivo com base nesse prefixo.
* **Sub-rede** – isso refere-se para a máscara de sub-rede e está configurado por meio da interface do Windows PowerShell.
* **Gateway** – este é o gateway padrão que deve ser utilizado por esta interface quando ela tenta comunicar com os nós que não estão no mesmo espaço de endereços IP (sub-rede). O gateway predefinido deve estar no mesmo espaço de endereços (sub-rede) como a interface de endereço IP, conforme determinado pela máscara de sub-rede.
* **Foi corrigido o endereço IP** – este campo está disponível somente enquanto configura os dados 0 interface. Para operações tais como atualizações ou de resolução de problemas do dispositivo, terá de ligar diretamente ao controlador de dispositivo. O endereço IP fixo pode servir-se para acessar o ativo e o controlador passivo no seu dispositivo.

> [!NOTE]
> * Para garantir a operação apropriada, verifique se a velocidade da interface e duplex no que cada interface de dispositivo está ligada ao comutador. Interfaces de comutador deve negociar seja com ou ser configurados para Gigabit Ethernet (1000 Mbps) e ser full duplex. Interfaces de operar em velocidades mais lentas ou no meio-duplex irão resultar em problemas de desempenho.
> * Para minimizar as interrupções e tempo de inatividade, recomendamos que ative portfast em cada uma das portas de comutador que ligarem para a interface de rede iSCSI do seu dispositivo. Isto irá garantir que a conectividade de rede pode ser estabelecida rapidamente em caso de uma ativação pós-falha.

### <a name="configure-data-0"></a>Configurar dados 0

DADOS 0 são a cloud ativada por predefinição. Ao configurar dados 0, também tem de configurar dois endereços IP fixos, um para cada controlador. Estes endereços IP fixos podem ser utilizados para aceder diretamente a controladores de dispositivo e são úteis quando instalar atualizações no dispositivo, para coleta de lixo funcione corretamente, ou quando acessa os controladores para fins de resolução de problemas.

Pode reconfigurar os controladores IP fixos através do painel de definições de 0 de dados.

![Configurar a interface de rede - dados 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> Os endereços IP fixos do controlador são utilizados para a manutenção de atualizações para o dispositivo e para algoritmos de recuperação de espaço (coleta de lixo) para funcionar corretamente. Por conseguinte, os IPs fixos têm de ser encaminháveis e conseguir estabelecer uma ligação à Internet.

### <a name="configure-data-1---data-5"></a>Configurar dados de 1 - dados 5

Para dados de 1 - interfaces de rede de 5 de dados, pode configurar todas as definições de rede, conforme mostrado na captura de ecrã seguinte:

![Configurar as interfaces de rede 1 de dados - 5 de dados](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>Trocar ou reatribuir IPs

Atualmente, se qualquer interface de rede no controlador for atribuído um VIP que está a ser utilizado (ao mesmo dispositivo ou outro dispositivo na rede), em seguida, o controlador irá efetuar a ativação pós-falha. Se troca ou reatribuir VIPs para uma interface de rede do dispositivo, tem de seguir um procedimento adequado conforme poderia criar uma situação de IP duplicada.

Execute os seguintes passos para trocar ou reatribuir os VIPs para qualquer uma das interfaces de rede:

#### <a name="to-reassign-ips"></a>Para reatribuir IPs

1. Limpe o endereço IP para ambas as interfaces.
2. Depois dos endereços IP forem verificados, atribua novos endereços IP para as respetivas interfaces.

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [configurar o MPIO para o dispositivo StorSimple](storsimple-8000-configure-mpio-windows-server.md).
* Saiba como [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

