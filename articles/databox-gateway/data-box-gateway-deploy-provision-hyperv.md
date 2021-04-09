---
title: Tutorial sobre como aprovisionar o Azure Data Box Gateway no Hyper-V | Microsoft Docs
description: O segundo tutorial para implementar o Azure Data Box Gateway envolve o aprovisionamento de um dispositivo virtual no Hyper-V.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 2e54b8e58e8139c46639187957906790ffb0fe54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727690"
---
# <a name="tutorial-provision-azure-data-box-gateway-in-hyper-v"></a>Tutorial: Provision Azure Data Box Gateway em Hiper-V

## <a name="overview"></a>Descrição Geral

Este tutorial descreve como aprovisionar um Data Box Gateway num sistema anfitrião com Hyper-V no Windows Server 2016, no Windows Server 2012 R2, ou no Windows Server 2012.

Para aprovisionar e configurar um dispositivo virtual, precisa de privilégios de administrador. O aprovisionamento e a configuração inicial demoram cerca de 10 minutos a concluir. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Certificar-se de que o anfitrião cumpre os requisitos mínimos de dispositivo
> * Aprovisionar um dispositivo virtual no hipervisor
> * Iniciar o dispositivo virtual e obter o endereço IP

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para aprovisionar um dispositivo virtual num sistema anfitrião com Hyper-V no Windows Server 2016 ou no Windows Server 2012 R2 são os seguintes.

### <a name="for-the-data-box-gateway-resource"></a>Para o recurso do Data Box Gateway

Antes de começar, certifique-se de que:

* Concluiu todos os passos descritos em [Preparar o portal para o Data Box Gateway](data-box-gateway-deploy-prep.md).
* Transferiu a imagem do dispositivo virtual para Hyper-V no portal do Azure, conforme descrito em [Prepare the portal for Data Box Gateway](data-box-gateway-deploy-prep.md) (Preparar o portal para o Data Box Gateway).

  > [!IMPORTANT]
  > O software em execução no Data Box Gateway só pode ser utilizado com o recurso do Data Box Gateway.

### <a name="for-the-data-box-gateway-virtual-device"></a>Relativamente ao dispositivo virtual Data Box Gateway

Antes de implementar um dispositivo, certifique-se de que:

* Tem acesso a um sistema anfitrião com Hyper-V no Windows Server 2012 R2 ou posterior que possa ser utilizado para aprovisionar um dispositivo.
* O sistema anfitrião tem capacidade para dedicar os seguintes recursos para aprovisionar o seu dispositivo virtual:

  * Um mínimo de 4 núcleos.
  * Pelos menos 8 GB de RAM. Recomendamos vivamente pelo menos 16 GB de RAM.
  * Uma interface de rede.
  * Um disco de 250 -GB DE SO.
  * Um disco virtual de 2-TB para dados.

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no datacenter

Antes de começar:

* Reveja os requisitos de rede para implementar um Data Box Gateway e configurar a rede do datacenter de acordo com os requisitos. Para obter mais informações, veja [Requisitos de rede do Data Box Gateway](data-box-gateway-system-requirements.md#networking-port-requirements).
* Certifique-se de que a largura de banda mínima de Internet é 20 Mbps, para permitir o funcionamento otimizado do dispositivo.

## <a name="check-the-host-system"></a>Verificar o sistema anfitrião

Para criar um dispositivo virtual, precisa:

* Da função Hyper-V instalada no Windows Server 2016, no Windows Server 2012 R2 ou no Windows Server 2012.
* Do Microsoft Hyper-V Manager, num cliente Microsoft Windows ligado ao anfitrião.
* Confirme que o hardware subjacente (sistema anfitrião) no qual está a criar o dispositivo virtual consegue dedicar os seguintes recursos ao dispositivo:

  * Um mínimo de 4 processadores virtuais.
  * Pelos menos 8 GB de RAM.
  * Uma interface de rede ligada à rede com capacidade para encaminhar o tráfego para a Internet.
  * Um disco de 250 GB de SO.
  * Um disco virtual de 2-TB para dados do sistema.

## <a name="bitlocker-considerations"></a>Considerações BitLocker

* Recomendamos que ative o BitLocker na sua máquina virtual Data Box Gateway. Por predefinição, o BitLocker não está ativado. Para obter mais informações, consulte:
  * [Definições de suporte de encriptação no Hyper-V Manager](/windows-server/virtualization/hyper-v/learn-more/generation-2-virtual-machine-security-settings-for-hyper-v#encryption-support-settings-in-hyper-v-manager)
  * [Suporte BitLocker em uma máquina virtual](https://kb.vmware.com/s/article/2036142)

## <a name="provision-a-virtual-device-in-hypervisor"></a>Aprovisionar um dispositivo virtual no hipervisor

Execute os passos seguintes para aprovisionar um dispositivo no seu hipervisor.

1. No anfitrião do Windows Server, copie a imagem do dispositivo virtual para uma unidade local. Esta imagem VHDX foi transferida através do portal do Azure. Tome nota da localização onde copiou a imagem, uma vez que vai utilizar esta imagem mais à frente no procedimento.

2. Abra o **Gestor de Servidor**. No canto superior direito, clique em **Ferramentas** e selecione **Hyper-V Manager**.

    ![Selecione Hyper-V Manager no Gestor do Servidor](./media/data-box-gateway-deploy-provision-hyperv/image1.png)

3. No **Hyper-V Manager**, no painel de âmbito, clique com o botão direito do rato no nó do seu sistema para abrir o menu de contexto e clique em **Novo** > **Máquina Virtual**.

   ![Criar nova máquina virtual no Hyper-V Manager](./media/data-box-gateway-deploy-provision-hyperv/image2.png)
4. Na página **Before you begin** (Antes de começar) do New Virtual Machine Wizard (Assistente de Nova Máquina Virtual), clique em **Next** (Seguinte).
5. Na página **Specify name and location** (Especificar nome e localização), indique **Name** (Nome) para o dispositivo virtual. Clique em **Seguinte**.

   ![Especificar nome e página de localização](./media/data-box-gateway-deploy-provision-hyperv/image3.png)
6. Na página **Specify generation** (Especificar geração), escolha **Generation 2** para o tipo de imagem do dispositivo .vhdx e clique em **Next** (Seguinte).    

   ![Especificar página de geração](./media/data-box-gateway-deploy-provision-hyperv/image4.png)
7. Na página **Assign memory** (Atribuir memória), especifique **Startup memory** (Memória de arranque) como, pelo menos, **8192 MB**, não ative a memória dinâmica e clique em **Next** (Seguinte).

   ![Atribuir página de memória](./media/data-box-gateway-deploy-provision-hyperv/image5.png) 
8. Na página **Configure networking** (Configurar rede), especifique o comutador virtual que está ligado à Internet e clique em **Next** (Seguinte).

   ![Configure página de rede](./media/data-box-gateway-deploy-provision-hyperv/image6.png)
9. Na página **Connect virtual hard disk** (Ligar disco rígido virtual), escolha **Use an existing virtual hard disk** (Utilizar um disco rígido virtual já existente), especifique a localização da imagem do dispositivo virtual e clique em **Next** (Seguinte).

   ![Conecte a página de disco rígido virtual](./media/data-box-gateway-deploy-provision-hyperv/image7.png)
10. Reveja **Summary** (Resumo) e clique em **Finish** (Concluir) para criar a máquina virtual.

    ![Completar a nova página de assistente de máquina virtual](./media/data-box-gateway-deploy-provision-hyperv/image8.png)
11. Para cumprir os requisitos mínimos, precisa de quatro processadores virtuais. Para adicionar quatro processadores virtuais, selecione o seu sistema de anfitrião na janela **Hyper-V Manager.** No painel do lado direito, na lista **Virtual Machines** (Máquinas Virtuais), localize a máquina virtual que acabou de criar. Selecione e clique com o botão direito do rato no nome da máquina e selecione **Settings** (Definições).

    ![Definições da máquina virtual](./media/data-box-gateway-deploy-provision-hyperv/image9.png)
12. Na página **Settings** (Definições), no painel do lado esquerdo, clique em **Processor** (Processador). No painel do lado direito, defina **number of virtual processors** (número de processadores virtuais) como quatro (ou mais). Clique em **Aplicar**.

    ![Definir número de processadores virtuais na página De Definições](./media/data-box-gateway-deploy-provision-hyperv/image10.png)
13. Para satisfazer os requisitos mínimos, também precisa adicionar um disco de dados virtual de 2-TB. Na página **Settings** (Definições):

    1. No painel do lado esquerdo, selecione **SCSI Controller** (Controlador SCSI).
    2. No painel do lado direito, selecione **Hard Drive** (Disco Rígido) e clique em **Add** (Adicionar).

    ![Adicionar disco rígido na página Definições](./media/data-box-gateway-deploy-provision-hyperv/image11.png)
14. Na página **Hard drive** (Disco Rígido), selecione a opção **Virtual hard disk** (Disco rígido virtual) e clique em **New** (Novo). É iniciado o **New Virtual Hard Disk Wizard** (Assistente de Novo Disco Rígido Virtual).

    ![Novo assistente de disco rígido virtual](./media/data-box-gateway-deploy-provision-hyperv/image12.png)
15. Na página **Before you begin** (Antes de começar) do New Virtual Hard Disk Wizard (Assistente de Novo Disco Rígido Virtual), clique em **Next** (Seguinte).
16. Na página **Choose Disk Format** (Escolher Formato do Disco), aceite a opção predefinida, que é o formato **VHDX**. Clique em **Seguinte**.
17. Na página **Choose Disk Type page** (Escolher Tipo de Disco), defina o tipo do disco rígido virtual como **Dynamically expanding** (Expansão dinâmica), que é a recomendação. **Fixed size disk** (Disco de tamanho fixo) também funcionaria, mas teria de esperar muito tempo. Não recomendamos a utilização da opção **Differencing** (Diferenciação). Clique em **Seguinte**.

    ![Escolha a página do tipo do disco](./media/data-box-gateway-deploy-provision-hyperv/image13.png)
18. Na página **'Nomear e Localização' especifique,** forneça um **nome** e **localização** (pode navegar para um) para o disco de dados. Clique em **Seguinte**.

    ![Especificar o nome e a página de localização](./media/data-box-gateway-deploy-provision-hyperv/image14.png)
19. Na página **Configure Disk,** selecione a nova opção **de disco rígido virtual em branco** e especifique o tamanho como **2 TB** (ou mais).

    Embora 2 TB seja o requisito mínimo, pode sempre aprovisionar um disco maior. Note que não pode encolher o disco uma vez que está previsto. Tentar encolher o disco resulta numa perda de todos os dados locais do dispositivo. A expansão do disco de dados não é suportada. Clique em **Seguinte**.

    ![Configure a página do disco](./media/data-box-gateway-deploy-provision-hyperv/image15.png)
20. Na página **Summary** (Resumo), reveja os detalhes do disco de dados virtual e, se estiver satisfeito, clique em **Finish** (Concluir) para criar o disco. O assistente fecha-se e é adicionado um disco rígido virtual à sua máquina.

    ![Completar a nova página virtual do assistente de disco rígido](./media/data-box-gateway-deploy-provision-hyperv/image16.png)
21. Regresse à página **Settings** (Definições). Clique em **OK** para fechar a página **Settings** (Definições) e regressar à janela do Hyper-V Manager.

    ![Página de definições](./media/data-box-gateway-deploy-provision-hyperv/image17.png)

A sua máquina virtual está agora totalmente configurada.

> [!NOTE]
> Não é possível prever um novo Gateway de Caixa de Dados copiando o seu VHD configurado. Cada novo dispositivo virtual Data Box Gateway deve ser ateado a partir de uma imagem de dispositivo virtual para Hiper-V que é descarregada a partir do portal Azure.

## <a name="start-the-virtual-device-and-get-the-ip"></a>Iniciar o dispositivo virtual e obter o IP

Execute os passos seguintes para iniciar o dispositivo virtual e ligar ao mesmo.

#### <a name="to-start-the-virtual-device"></a>Para iniciar o dispositivo virtual

1. Inicie o dispositivo virtual.

   ![Iniciar dispositivo virtual](./media/data-box-gateway-deploy-provision-hyperv/image18.png)
2. Quando o dispositivo estiver em execução, selecione-o, clique com o botão direito do rato e selecione **Connect** (Ligar).

3. Poderá ter de esperar entre 10 a 15 minutos para que o dispositivo esteja pronto. É apresentada uma mensagem de estado na consola, para indicar o progresso. Quando o dispositivo estiver pronto, aceda a **Action** (Ação). Prima `Ctrl + Alt + Delete` para iniciar sedução no dispositivo virtual. O utilizador predefinido é *EdgeUser* e a palavra-passe predefinida é *Password1*.

   ![Inscreva-se no dispositivo virtual](./media/data-box-gateway-deploy-provision-hyperv/image21.png)

4. Os passos de 5 a 7 aplicam-se apenas se estiver a iniciar num ambiente não DHCP. Se estiver num ambiente DHCP, ignore estes passos. Se tiver iniciado o dispositivo num ambiente não DHCP, verá uma mensagem a informar do mesmo.

5. Para configurar a rede, utilize o comando `Get-HcsIpAddress` para listar as interfaces de rede ativadas no seu dispositivo virtual. Se o seu dispositivo tiver uma única interface de rede ativada, o nome predefinido atribuído a essa interface é `Ethernet`.

6. Utilize o cmdlet `Set-HcsIpAddress` para configurar a rede. Veja o seguinte exemplo:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

7. Depois de a configuração inicial estar concluída e o dispositivo iniciado, verá o texto da faixa do dispositivo. Tome nota do endereço IP e do URL apresentado no texto da faixa para gerir o dispositivo. Utilize esse endereço IP para ligar à IU da Web do seu dispositivo virtual e concluir a configuração local e a ativação.

   ![Banner de dispositivo virtual com endereço IP e URL de conexão](./media/data-box-gateway-deploy-provision-hyperv/image23.png)

Se o dispositivo não cumprir os requisitos mínimos de configuração, verá um erro no texto da faixa. Modifique a configuração do dispositivo de modo a que a máquina tenha os recursos adequados para satisfazer os requisitos mínimos. Em seguida, pode reiniciar e ligar ao dispositivo. Consulte os requisitos mínimos de configuração no [Check the host system satisfaz os requisitos mínimos do dispositivo virtual](#check-the-host-system).

Se enfrentar qualquer outro erro durante a configuração inicial utilizando a UI web local, consulte os seguintes fluxos de trabalho:

* [Executar testes de diagnóstico para resolver problemas na configuração da UI web](data-box-gateway-troubleshoot.md#run-diagnostics).
* [Gerei o pacote de registo e visualiza os ficheiros de registo](data-box-gateway-troubleshoot.md#collect-support-package).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Data Box Gateway, como:

> [!div class="checklist"]
> * Certificar-se de que o anfitrião cumpre os requisitos mínimos de dispositivo
> * Aprovisionar um dispositivo virtual no hipervisor
> * Iniciar o dispositivo virtual e obter o endereço IP

Avance para o próximo tutorial para aprender a ligar, configurar e ativar o dispositivo virtual.

> [!div class="nextstepaction"]
> [Connect and set up your Data Box Gateway](./data-box-gateway-deploy-connect-setup-activate.md) (Ligar e configurar o Data Box Gateway)
