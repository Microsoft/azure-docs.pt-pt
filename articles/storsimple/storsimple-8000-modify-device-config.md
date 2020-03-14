---
title: Modificar a configuração do dispositivo da série StorSimple 8000 / Microsoft Docs
description: Descreve como utilizar o serviço StorSimple Device Manager para reconfigurar um dispositivo StorSimple que já foi implementado.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79267693"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>Utilize o serviço StorSimple Device Manager para modificar a configuração do seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral

A secção de **definições** do portal Azure Na lâmina **Definições** contém todos os parâmetros do dispositivo que pode reconfigurar num dispositivo StorSimple que é gerido por um serviço StorSimple Device Manager. Este tutorial explica como pode utilizar a lâmina **Definições** para executar as seguintes tarefas ao nível do dispositivo:

* Modificar o nome amigável do dispositivo
* Modificar as definições de tempo do dispositivo
* Atribuir um DNS secundário
* Modificar interfaces de rede
* Troque ou reatribuir IPs

## <a name="modify-device-friendly-name"></a>Modificar o nome amigável do dispositivo

Pode utilizar o portal Azure para alterar o nome do dispositivo e atribuir-lhe um nome amigável único à sua escolha. Utilize a lâmina de **regulação geral** do seu dispositivo para modificar o nome amigável do dispositivo. O nome amigável pode conter quaisquer caracteres e pode ter um máximo de 64 caracteres de comprimento.

> [!NOTE] 
> Só é possível modificar o nome do dispositivo no portal Azure antes da configuração do dispositivo estar concluída. Uma vez concluída a configuração mínima do dispositivo, não é possível alterar o nome do dispositivo.

![Nome do dispositivo nas definições gerais](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

Um dispositivo StorSimple que esteja ligado ao serviço StorSimple Device Manager é atribuído a um nome predefinido. O nome predefinido reflete tipicamente o número de série do dispositivo. Por exemplo, um nome de dispositivo predefinido com 15 caracteres de comprimento, como 8600-SHX091003G44HT, indica o seguinte:

* **8600** - Indica o modelo do dispositivo.
* **SHX** – Indica o local de fabrico.
* **0991003** - Indica um produto específico.
* **G44HT**- Os últimos 5 dígitos são incrementados para criar números de série únicos. Isto pode não ser um conjunto sequencial.

## <a name="modify-device-description"></a>Modificar a descrição do dispositivo

Utilize a lâmina de **regulação geral** do seu dispositivo para modificar a descrição do dispositivo.

![Descrição do dispositivo em configurações gerais](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

Uma descrição do dispositivo geralmente ajuda a identificar o proprietário e a localização física do dispositivo. O campo de descrição deve conter menos de 256 caracteres.

## <a name="modify-time-settings"></a>Modificar as definições de tempo

O seu dispositivo deve sincronizar o tempo para autenticar com o seu fornecedor de serviços de armazenamento em nuvem. Utilize a lâmina de **regulação geral** do seu dispositivo para modificar as definições de tempo do dispositivo.

![Descrição do dispositivo em configurações gerais](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 Selecione o seu fuso horário na lista de lançamentos. Pode especificar até dois servidores do Protocolo de Tempo de Rede (NTP):

 - **Servidor NTP primário** - A configuração é necessária e é especificada quando utiliza o Windows PowerShell para o StorSimple para configurar o seu dispositivo. Pode especificar o **time.windows.com** do Servidor do Windows predefinido como servidor NTP. Pode visualizar a configuração do servidor NTP primário através do portal Azure, mas tem de utilizar a interface Do Windows PowerShell para o alterar. Utilize o `Set-HcsNTPClientServerAddress` cmdlet para modificar o servidor NTP Primário do seu dispositivo. Para mais informações, vá à sintaxe para [Set-HcsNTPClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) cmdlet.

- **Servidor NTP secundário** - A configuração é opcional. Pode utilizar o portal para configurar um servidor NTP secundário.

Ao configurar o servidor NTP, certifique-se de que a sua rede permite que o tráfego NTP passe do seu datacenter para a Internet. Ao especificar um servidor PÚBLICO NTP, deve certificar-se de que as suas firewalls de rede e outros dispositivos de segurança estão configurados para permitir que o tráfego ntp viaje de e para a rede externa. Se o tráfego de NTP bidirecional não for permitido, deve utilizar um servidor NTP interno (um controlador de domínio Windows fornece esta função). Se o seu dispositivo não conseguir sincronizar o tempo, poderá não conseguir comunicar com o seu fornecedor de armazenamento em nuvem.

Para ver uma lista de servidores PÚBLICOS NTP, vá ao [Web ntp Servers](https://support.ntp.org/bin/view/Servers/WebHome).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>O que acontece se o dispositivo for implantado num fuso horário diferente?

Se o dispositivo for implantado num fuso horário diferente, o fuso horário do dispositivo irá mudar. Dado que todas as políticas de backup utilizam o fuso horário do dispositivo, as políticas de backup ajustar-se-ão automaticamente de acordo com o novo fuso horário. Não é necessária nenhuma intervenção do utilizador.

## <a name="modify-dns-settings"></a>Modificar as definições de DNS

Um servidor DNS é utilizado quando o seu dispositivo tenta comunicar com o seu fornecedor de serviços de armazenamento em nuvem. Utilize a lâmina de **definições** de rede no seu dispositivo para visualizar e modificar as definições de DNS configuradas. 

![Definições dNS nas definições da Rede](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

Para uma elevada disponibilidade, é necessário configurar tanto os servidores DNS primários como secundários durante a implementação inicial do dispositivo.

**Servidor DNS primário** - Utilize o Windows PowerShell para o StorSimple para especificar primeiro o servidor DNS Primário durante a configuração inicial. Só é possível reconfigurar o servidor DNS primário através da interface Windows PowerShell. Utilize o `Set-HcsDNSClientServerAddress` cmdlet para modificar o servidor DNS primário do seu dispositivo. Para mais informações, vá à sintaxe para [Set-HcsDNSClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) cmdlet.

**Servidor DNS secundário** - Para modificar o servidor DNS secundário, utilize o `Set-HcsDNSClientServerAddress` cmdlet na interface Windows PowerShell do dispositivo ou na lâmina de **definições** de rede do seu dispositivo StorSimple no portal Azure.

Para modificar o servidor Secundário DNS no portal Azure, execute os seguintes passos.

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. A partir da lista de dispositivos, selecione e clique no seu dispositivo.

2. Na lâmina **Definições,** vá às **definições do Dispositivo > Rede**. Isto abre a lâmina de definições da **Rede.** Clique em azulejo de **definições dNS.** Modificar o endereço IP do servidor DNS secundário.

    ![Modificar o endereço IP do servidor DNS secundário](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. A partir da barra de comando, clique em **Guardar** e quando solicitado para confirmação, clique em **OK**.

    ![Guardar e confirmar alterações](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Modificar interfaces de rede

O seu dispositivo tem seis interfaces de rede de dispositivos, quatro dos quais são 1 GbE e dois dos quais são 10 GbE. Estas interfaces estão rotuladas como DATA 0 – DATA 5. DADOS 0, DADOS 1, DADOS 4 e DADOS 5 são 1 GbE, enquanto OS DADOS 2 e DATA 3 são interfaces de rede de 10 GbE.

Utilize a lâmina de **definições** da rede para configurar cada uma das interfaces a utilizar.

![Configure interfaces de rede através de definições de rede](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

Para garantir uma elevada disponibilidade, recomendamos que tenha pelo menos duas interfaces iSCSI e duas interfaces ativadas pela nuvem no seu dispositivo. Recomendamos, mas não exigimos que as interfaces não utilizadas sejam desativadas.

Para cada interface de rede, são apresentados os seguintes parâmetros:

* **Velocidade** – Não é um parâmetro configurável pelo utilizador. DADOS 0, DADOS 1, DADOS 4 e DADOS 5 são sempre 1 GbE, enquanto OS DADOS 2 e DATA 3 são interfaces de 10 GbE.
  
  > [!NOTE]
  > Velocidade e duplex são sempre negociados automaticamente. As molduras jumbo não são suportadas.
  
* **Estado** da interface – Uma interface pode ser ativada ou desativada. Se ativado, o dispositivo tentará utilizar a interface. Recomendamos que apenas as interfaces que estão ligadas à rede e utilizadas estejam ativadas. Desative quaisquer interfaces que não esteja a utilizar.
* **Tipo** de interface – Este parâmetro permite isolar o tráfego iSCSI do tráfego de armazenamento em nuvem. Este parâmetro pode ser um dos seguintes:
  
  * **Cloud ativado** – quando ativado, o dispositivo utilizará esta interface para comunicar com a nuvem.
  * **iSCSI ativado** – quando ativado, o dispositivo utilizará esta interface para comunicar com o anfitrião iSCSI.
    
    Recomendamos que isole o tráfego iSCSI do tráfego de armazenamento em nuvem. Note também que se o seu anfitrião estiver dentro da mesma sub-rede que o seu dispositivo, não precisa de atribuir um portal; no entanto, se o seu anfitrião estiver numa subrede diferente do seu dispositivo, terá de atribuir uma porta de entrada.
* **Endereço IP** – Quando configurar qualquer uma das interfaces de rede, tem de configurar um IP virtual (VIP). Isto pode ser IPv4 ou IPv6 ou ambos. Tanto as famílias de endereços IPv4 como IPv6 são suportadas para as interfaces da rede de dispositivos. Ao utilizar o IPv4, especifique um endereço IP de 32 bits *(xxx.xxx.xxx.xxx*) em notação decimal ponto-decimal. Ao utilizar o IPv6, basta fornecer um prefixo de 4 dígitos e um endereço de 128 bits será gerado automaticamente para a interface da rede do dispositivo com base nesse prefixo.
* **Subnet** – Isto refere-se à máscara de sub-rede e é configurado através da interface Windows PowerShell.
* **Gateway** – Esta é a porta de entrada predefinida que deve ser utilizada por esta interface quando tenta comunicar com nós que não estão dentro do mesmo espaço de endereço IP (subnet). O gateway predefinido deve estar no mesmo espaço de endereço (sub-rede) que o endereço IP da interface, determinado pela máscara de sub-rede.
* **Endereço IP fixo** – Este campo só está disponível enquanto configura a interface DATA 0. Para operações como atualizações ou resolução de problemas do dispositivo, poderá ser necessário ligar-se diretamente ao controlador do dispositivo. O endereço IP fixo pode ser utilizado para aceder tanto ao controlador ativo como ao controlador passivo do seu dispositivo.

> [!NOTE]
> * Para assegurar o bom funcionamento, verifique a velocidade da interface e o duplex no interruptor a que cada interface do dispositivo está ligada. As interfaces de comutação devem negociar ou ser configuradas para Gigabit Ethernet (1000 Mbps) e ser totalmente duplex. As interfaces que operam a velocidades mais lentas ou em meio duplex resultarão em problemas de desempenho.
> * Para minimizar as perturbações e o tempo de inatividade, recomendamos que ative a porta rápida em cada uma das portas do interruptor a que a interface de rede iSCSI do seu dispositivo estará ligada. Isto garantirá que a conectividade da rede possa ser estabelecida rapidamente em caso de falha.

### <a name="configure-data-0"></a>Configurar DADOS 0

O DATA 0 é ativado por predefinição. Ao configurar dados 0, também é necessário configurar dois endereços IP fixos, um para cada controlador. Estes endereços IP fixos podem ser utilizados para aceder diretamente aos controladores do dispositivo e são úteis quando instala atualizações no dispositivo, para que a recolha de lixo funcione corretamente ou quando acede aos controladores para efeitos de resolução de problemas.

Pode reconfigurar os controladores IP fixos através da lâmina de definição DATA 0.

![Configure interface de rede - DATA 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> Os endereços IP fixos para o controlador são utilizados para a manutenção das atualizações no dispositivo e para que os algoritmos de recuperação de espaços (recolha de lixo) funcionem corretamente. Por conseguinte, os IPs fixos têm de ser encaminháveis e conseguir estabelecer uma ligação à Internet.

### <a name="configure-data-1---data-5"></a>Configure DADOS 1 - DADOS 5

Para DATA 1 - DATA 5 interfaces de rede, pode configurar todas as definições de rede como mostrado na seguinte imagem:

![Configurar interfaces de rede DATA 1 - DATA 5](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>Troque ou reatribuir IPs

Atualmente, se for atribuída qualquer interface de rede no controlador a um VIP que esteja a ser utilizado (pelo mesmo dispositivo ou outro dispositivo na rede), então o controlador falhará. Se trocar ou reatribuir VIPs por uma interface de rede de dispositivos, deve seguir um procedimento adequado, pois pode criar uma situação ip duplicada.

Execute os seguintes passos para trocar ou reatribuir os VIPs para qualquer uma das interfaces de rede:

#### <a name="to-reassign-ips"></a>Para reatribuir iPs

1. Limpe o endereço IP para ambas as interfaces.
2. Depois de apurados os endereços IP, atribua os novos endereços IP às respetivas interfaces.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [configurar o MPIO para o seu dispositivo StorSimple](storsimple-8000-configure-mpio-windows-server.md).
* Aprenda a [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

