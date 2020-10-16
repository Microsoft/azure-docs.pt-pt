---
title: Modifique a configuração do dispositivo da série StorSimple 8000 Microsoft Docs
description: Descreve como utilizar o serviço StorSimple Device Manager para reconfigurar um dispositivo StorSimple que já foi implantado.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: a08ddae1d10cc8b26f3aa7ee85e731c2cea15f93
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962892"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>Utilize o serviço StorSimple Device Manager para modificar a configuração do seu dispositivo StorSimple

## <a name="overview"></a>Overview (Descrição geral)

A secção **de definições do dispositivo do** portal Azure na lâmina **Definições** contém todos os parâmetros do dispositivo que pode reconfigurar num dispositivo StorSimple que é gerido por um serviço StorSimple Device Manager. Este tutorial explica como pode utilizar a lâmina **De Definições** para executar as seguintes tarefas ao nível do dispositivo:

* Modificar o nome amigável do dispositivo
* Modificar as definições de tempo do dispositivo
* Atribuir um DNS secundário
* Modificar interfaces de rede
* Trocar ou reatribuir IPs

## <a name="modify-device-friendly-name"></a>Modificar o nome amigável do dispositivo

Pode utilizar o portal Azure para alterar o nome do dispositivo e atribuir-lhe um nome único e amigável à sua escolha. Utilize a lâmina **de definição geral** do seu dispositivo para modificar o nome amigável do dispositivo. O nome amigável pode conter qualquer caracteres e pode ter um máximo de 64 caracteres de comprimento.

> [!NOTE] 
> Só é possível modificar o nome do dispositivo no portal Azure antes de a configuração do dispositivo estar concluída. Uma vez concluída a configuração mínima do dispositivo, não é possível alterar o nome do dispositivo.

![Nome do dispositivo em configurações gerais](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

É atribuído um nome padrão a um dispositivo StorSimple que esteja ligado ao serviço StorSimple Device Manager. O nome padrão normalmente reflete o número de série do dispositivo. Por exemplo, um nome de dispositivo padrão com 15 caracteres, como 8600-SHX0991003G44HT, indica o seguinte:

* **8600**  - Indica o modelo do dispositivo.
* **SHX** – Indica o local de fabrico.
* **0991003** - Indica um produto específico.
* **G44HT**- Os últimos 5 dígitos são incrementados para criar números de série únicos. Isto pode não ser um conjunto sequencial.

## <a name="modify-device-description"></a>Modificar a descrição do dispositivo

Utilize a lâmina **de definição geral** do seu dispositivo para modificar a descrição do dispositivo.

![Descrição do dispositivo em configurações gerais](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

Uma descrição do dispositivo geralmente ajuda a identificar o proprietário e a localização física do dispositivo. O campo de descrição deve conter menos de 256 caracteres.

## <a name="modify-time-settings"></a>Modificar as definições de tempo

O seu dispositivo deve sincronizar o tempo para autenticar com o seu fornecedor de serviços de armazenamento em nuvem. Utilize a lâmina **de definição geral** do seu dispositivo para modificar as definições de tempo do dispositivo.

![Descrição do dispositivo nas definições gerais 2](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 Selecione o seu fuso horário a partir da lista de drop-down. Pode especificar até dois servidores do Protocolo de Tempo de Rede (NTP):

 - **Servidor NTP primário** - A configuração é necessária e é especificada quando utiliza o Windows PowerShell para configurar o seu dispositivo. Pode especificar o **time.windows.com** padrão do Windows Server como o seu servidor NTP. Pode ver a configuração do servidor NTP primário através do portal Azure, mas tem de utilizar a interface Windows PowerShell para alterá-la. Utilize o `Set-HcsNTPClientServerAddress` cmdlet para modificar o servidor NTP primário do seu dispositivo. Para mais informações, aceda à sintaxe para [Set-HcsNTPClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) cmdlet.

- **Servidor NTP secundário** - A configuração é opcional. Pode utilizar o portal para configurar um servidor NTP secundário.

Ao configurar o servidor NTP, certifique-se de que a sua rede permite que o tráfego NTP passe do seu datacenter para a Internet. Ao especificar um servidor NTP público, deve certificar-se de que as suas firewalls de rede e outros dispositivos de segurança estão configurados para permitir que o tráfego NTP viaje de e para a rede externa. Se não for permitido tráfego NTP bidirecional, deve utilizar um servidor NTP interno (um controlador de domínio Windows fornece esta função). Se o seu dispositivo não conseguir sincronizar o tempo, poderá não conseguir comunicar com o seu fornecedor de armazenamento em nuvem.

Para ver uma lista de servidores NTP públicos, vá à Web dos [Servidores NTP](https://support.ntp.org/bin/view/Servers/WebHome).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>O que acontece se o dispositivo for implantado num fuso horário diferente?

Se o dispositivo for implantado num fuso horário diferente, o fuso horário do dispositivo mudará. Dado que todas as políticas de backup utilizam o fuso horário do dispositivo, as políticas de backup ajustar-se-ão automaticamente de acordo com o novo fuso horário. Não é necessária a intervenção do utilizador.

## <a name="modify-dns-settings"></a>Modificar as definições de DNS

Um servidor DNS é utilizado quando o seu dispositivo tenta comunicar com o seu fornecedor de serviços de armazenamento em nuvem. Utilize a lâmina **de definição de rede** no seu dispositivo para visualizar e modificar as definições de DNS configuradas. 

![Definições de DNS nas definições de Rede](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

Para uma elevada disponibilidade, é necessário configurar tanto os servidores DNS primários como os secundários durante a implementação inicial do dispositivo.

**Servidor DNS primário** - Utilize o Windows PowerShell para StorSimple para especificar primeiro o servidor DNS primário durante a configuração inicial. Só é possível reconfigurar o servidor dNS primário através da interface Windows PowerShell. Utilize o `Set-HcsDNSClientServerAddress` cmdlet para modificar o servidor DNS primário do seu dispositivo. Para mais informações, aceda à sintaxe para [Set-HcsDNSClientServerAderAddress](https://technet.microsoft.com/library/dn688138.aspx) cmdlet.

**Servidor DNS secundário** - Para modificar o servidor DNS secundário, utilize o `Set-HcsDNSClientServerAddress` cmdlet na interface Windows PowerShell do dispositivo ou lâmina de **definição** de rede do seu dispositivo StorSimple no portal Azure.

Para modificar o servidor DNS secundário no portal Azure, execute os seguintes passos.

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. A partir da lista de dispositivos, selecione e clique no seu dispositivo.

2. Na lâmina **Definições,** aceda às **definições do dispositivo > Rede**. Isto abre a lâmina **de definição de rede.** Clique **em azulejo de definições DNS.** Modifique o endereço IP do servidor DNS secundário.

    ![Modificar o endereço IP do servidor DNS secundário](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. A partir da barra de comando, clique em **Guardar** e quando solicitado para confirmação, clique **em OK**.

    ![Guardar e confirmar alterações](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Modificar interfaces de rede

O seu dispositivo tem seis interfaces de rede de dispositivos, quatro das quais são 1 GbE e duas são de 10 GbE. Estas interfaces são rotuladas como DATA 0 – DATA 5. Os DADOS 0, OS DADOS 1, OS DADOS 4 e OS DADOS 5 são 1 GbE, enquanto os DADOS 2 e OS DADOS 3 são interfaces de rede de 10 GbE.

Utilize a lâmina **de definição de rede** para configurar cada uma das interfaces a utilizar.

![Configure interfaces de rede através de definições de rede](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

Para garantir uma elevada disponibilidade, recomendamos que tenha pelo menos duas interfaces iSCSI e duas interfaces ativadas pela nuvem no seu dispositivo. Recomendamos, mas não exigimos que as interfaces não são desativadas.

Para cada interface de rede, são apresentados os seguintes parâmetros:

* **Velocidade** – Não um parâmetro configurável pelo utilizador. Os DADOS 0, OS DADOS 1, OS DADOS 4 e OS DADOS 5 são sempre 1 GbE, enquanto os DADOS 2 e OS DADOS 3 são interfaces de 10 GbE.
  
  > [!NOTE]
  > Velocidade e duplex são sempre negociados automaticamente. As molduras jumbo não são suportadas.
  
* **Estado de interface** – Uma interface pode ser ativada ou desativada. Se estiver ativado, o dispositivo tentará utilizar a interface. Recomendamos que apenas as interfaces que estejam ligadas à rede e utilizadas estejam ativadas. Desative quaisquer interfaces que não esteja a utilizar.
* **Tipo de interface** – Este parâmetro permite isolar o tráfego iSCSI do tráfego de armazenamento em nuvem. Este parâmetro pode ser um dos seguintes:
  
  * **Com a nuvem ativada** – quando ativado, o dispositivo utilizará esta interface para comunicar com a nuvem.
  * **iSCSI ativado** – quando ativado, o dispositivo utilizará esta interface para comunicar com o anfitrião iSCSI.
    
    Recomendamos que isole o tráfego iSCSI do tráfego de armazenamento na nuvem. Note também que se o seu anfitrião estiver dentro da mesma sub-rede que o seu dispositivo, não precisa de atribuir um gateway; no entanto, se o seu anfitrião estiver numa sub-rede diferente do seu dispositivo, terá de atribuir um portal.
* **Endereço IP** – Ao configurar qualquer uma das interfaces de rede, tem de configurar um IP virtual (VIP). Isto pode ser IPv4 ou IPv6 ou ambos. Tanto as famílias de endereços IPv4 como IPv6 são suportadas para as interfaces de rede de dispositivos. Ao utilizar o IPv4, especifique um endereço IP de 32 bits *(xxx.xxx.xxx.xxx*) na notação decimal de ponto. Ao utilizar o IPv6, basta fornecer um prefixo de 4 dígitos e um endereço de 128 bits será gerado automaticamente para a interface da rede do dispositivo com base nesse prefixo.
* **Sub-rede** – Isto refere-se à máscara de sub-rede e é configurada através da interface Windows PowerShell.
* **Gateway** – Este é o gateway predefinido que deve ser utilizado por esta interface quando tenta comunicar com os nóns que não estão dentro do mesmo espaço de endereço IP (sub-rede). O gateway predefinido deve estar no mesmo espaço de endereço (sub-rede) que o endereço IP da interface, conforme determinado pela máscara de sub-rede.
* **Endereço IP fixo** – Este campo só está disponível enquanto configura a interface DATA 0. Para operações como atualizações ou resolução de problemas do dispositivo, poderá ter de se ligar diretamente ao controlador do dispositivo. O endereço IP fixo pode ser utilizado para aceder tanto ao controlador ativo como ao controlador passivo no seu dispositivo.

> [!NOTE]
> * Para garantir um funcionamento adequado, verifique a velocidade da interface e o duplex no interruptor a que cada interface do dispositivo está ligada. As interfaces switch devem negociar ou ser configuradas para Gigabit Ethernet (1000 Mbps) e ser totalmente duplex. As interfaces que operam a velocidades mais lentas ou em meio duplex resultarão em problemas de desempenho.
> * Para minimizar as perturbações e o tempo de inatividade, recomendamos que ative o portfast em cada uma das portas do interruptor a que a interface de rede iSCSI do seu dispositivo se irá ligar. Isto garantirá que a conectividade da rede possa ser estabelecida rapidamente em caso de falha.

### <a name="configure-data-0"></a>Configure DATA 0

O DATA 0 está ativado na nuvem por padrão. Ao configurar data 0, também é necessário configurar dois endereços IP fixos, um para cada controlador. Estes endereços IP fixos podem ser utilizados para aceder diretamente aos controladores do dispositivo e são úteis quando instala atualizações no dispositivo, para que a recolha de lixo funcione corretamente ou quando acede aos controladores com o propósito de resolver problemas.

Pode reconfigurar os controladores IP fixos através da lâmina de definições DATA 0.

![Configure interface de rede - DATA 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> Os endereços IP fixos para o controlador são utilizados para a manutenção das atualizações do dispositivo e para que os algoritmos de recuperação de espaço (recolha de lixo) funcionem corretamente. Por conseguinte, os IPs fixos têm de ser encaminháveis e conseguir estabelecer uma ligação à Internet.

### <a name="configure-data-1---data-5"></a>Configure DADOS 1 - DADOS 5

Para as interfaces de rede DATA 1 - DATA 5, pode configurar todas as definições de rede como mostrado na imagem seguinte:

![Configure interfaces de rede DATA 1 - DATA 5](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>Trocar ou reatribuir IPs

Atualmente, se alguma interface de rede no controlador for atribuída a um VIP que esteja a ser utilizado (pelo mesmo dispositivo ou outro dispositivo na rede), então o controlador falhará. Se trocar ou reatribuir VIPs para uma interface de rede de dispositivos, deve seguir um procedimento adequado, pois pode criar uma situação ip duplicada.

Execute os seguintes passos para trocar ou reatribuir os VIPs para qualquer uma das interfaces de rede:

#### <a name="to-reassign-ips"></a>Para reatribuir IPs

1. Limpe o endereço IP para ambas as interfaces.
2. Após a apuração dos endereços IP, atribua os novos endereços IP às respetivas interfaces.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [configurar o MPIO para o seu dispositivo StorSimple](storsimple-8000-configure-mpio-windows-server.md).
* Saiba como [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

