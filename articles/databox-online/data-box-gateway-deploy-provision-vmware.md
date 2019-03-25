---
title: Tutorial sobre o aprovisionamento do Azure Data Box Gateway em VMware | Microsoft Docs
description: O segundo tutorial para implementar o Azure Data Box Gateway envolve o aprovisionamento de um dispositivo virtual em VMware.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: 61fc72fe295fc292f944d6fea0f67fce0d537c32
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402334"
---
# <a name="tutorial-provision-azure-data-box-gateway-in-vmware"></a>Tutorial: Gateway de caixa de dados do Azure de aprovisionamento no VMware

## <a name="overview"></a>Descrição geral

Este tutorial descreve como aprovisionar um Gateway de caixa de dados num sistema anfitrião com o VMware ESXi 6.0, 6.5 ou 6.7. 

Necessita de privilégios de administrador para aprovisionar e ligar a um dispositivo virtual. O aprovisionamento e a configuração inicial demoram cerca de dez minutos a concluir.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Confirmar que o anfitrião cumpre os requisitos mínimos do dispositivo.
> * Aprovisionar um dispositivo virtual no VMware
> * Iniciar o dispositivo virtual e obter o endereço IP

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

As pré-requisitos para aprovisionar um dispositivo virtual num sistema anfitrião com o VMware ESXi 6.0, 6.5 ou 6.7, são as seguintes.

### <a name="for-the-data-box-gateway-resource"></a>Para o recurso do Data Box Gateway

Antes de começar, certifique-se de que:

* Concluiu todos os passos descritos em [Prepare the portal for Data Box Gateway](data-box-gateway-deploy-prep.md) (Preparar o portal para o Data Box Gateway).
* Transferiu a imagem do dispositivo virtual para VMware no portal do Azure, conforme descrito em [Preparar o portal para o Data Box Gateway](data-box-gateway-deploy-prep.md).

  > [!IMPORTANT]
  > O software em execução no Data Box Gateway só pode ser utilizado com o recurso do Data Box Gateway.

### <a name="for-the-data-box-gateway-virtual-device"></a>Para o dispositivo virtual Data Box Gateway

Antes de implementar um dispositivo virtual, certifique-se de que:

* Tem acesso a um sistema anfitrião que execute o VMware (ESXi 6.0, 6.5 ou 6.7) que pode ser utilizado para um aprovisionar um dispositivo.
* O sistema anfitrião tem capacidade para dedicar os seguintes recursos para aprovisionar o seu dispositivo virtual:

  * Um mínimo de 4 núcleos.
  * Pelos menos 8 GB de RAM.
  * Uma interface de rede.
  * Um disco de SO de 250 GB.
  * Um disco virtual de 2 TB para dados do sistema.

### <a name="for-the-network-in-datacenter"></a>Para a rede no datacenter

Antes de começar:

- Reveja os requisitos de rede para implementar um Data Box Gateway e configurar a rede do datacenter de acordo com os requisitos. Para obter mais informações, veja [Requisitos de rede do Data Box Gateway](data-box-gateway-system-requirements.md#networking-port-requirements).
- Certifique-se de que a largura de banda mínima de Internet é 20 Mbps, para permitir o funcionamento otimizado do dispositivo.

## <a name="check-the-host-system"></a>Verificar o sistema anfitrião

Para criar um dispositivo virtual, precisa:

* Acesso a um sistema de anfitrião a executar o servidor do VMware ESXi 6.0, 6.5 ou 6.7. O sistema anfitrião tem capacidade para dedicar os recursos seguintes ao seu dispositivo virtual:
 
  * Um mínimo de 4 processadores virtuais.
  * Pelos menos 8 GB de RAM. 
  * Uma interface de rede ligada à rede com capacidade para encaminhar o tráfego para a Internet.
  * Um disco de SO de 250 GB.
  * Um disco virtual de 2 TB para dados.
* Ter o cliente VMware vSphere no seu sistema para gerir o anfitrião ESXi.


## <a name="provision-a-virtual-device-in-hypervisor"></a>Aprovisionar um dispositivo virtual no hipervisor

Execute os passos seguintes para aprovisionar um dispositivo virtual no seu hipervisor.

1. Copie a imagem do dispositivo virtual no seu sistema. Transferiu esta imagem virtual (dois ficheiros) através do portal do Azure. Tome nota da localização onde copiou a imagem, uma vez que vai utilizar esta imagem mais à frente no procedimento.

2. Inicie sessão servidor ESXi através de um browser neste URL: `https://<IP address of the ESXi server>`. Tem de ter privilégios de administrador para criar uma máquina virtual.

   ![Página de início de sessão](./media/data-box-gateway-deploy-provision-vmware/image1.png)
  
3. Carregue o VMDK para o servidor ESXi. No painel Navigator (Navegador), selecione **Storage** (Armazenamento).

   ![](./media/data-box-gateway-deploy-provision-vmware/image2.png)

4. No painel da direita, em **Datastores** (Arquivos de dados), selecione o arquivo de dados onde pretende carregar o VMDK. 

    - O arquivo de dados tem de ser do tipo VMFS5. 
    - O arquivo de dados também tem de ter espaço livre suficiente para os discos de dados e do SO.
   
5. Clique com o botão direito do rato e selecione **Browse Datastore** (Procurar arquivo de dados).

   ![Procurar o arquivo de dados](./media/data-box-gateway-deploy-provision-vmware/image3.png)

6. É apresentada a janela **Datastore Browser** (Browser de arquivos de dados).

   ![Browser de arquivo de dados](./media/data-box-gateway-deploy-provision-vmware/image4.png)

7. Na barra de ferramentas, clique no ícone **Create directory** (Criar diretório) para criar uma nova pasta. Especifique o nome da pasta e tome nota do mesmo. Vai utilizar este nome de pasta mais tarde quando criar uma máquina virtual (melhor prática recomendada). Clique em **Create directory** (Criar diretório).

   ![Criar um diretório](./media/data-box-gateway-deploy-provision-vmware/image5.png)

8. A nova pasta aparece no painel esquerdo do **Datastore Browser** (Browser de arquivos de dados). Clique no ícone **Upload** (Carregar) e selecione **Upload File** (Carregar ficheiro).

    ![Carregar o ficheiro](./media/data-box-gateway-deploy-provision-vmware/image6.png)

9. Procure e aponte para os ficheiros VMDK que descarregou. Existem dois ficheiros. Selecione um ficheiro para carregar.

    ![Selecione o ficheiro a carregar](./media/data-box-gateway-deploy-provision-vmware/image7.png)

10. Clique em **Open** (Abrir). O carregamento do ficheiro VMDK para o arquivo de dados especificado é iniciado. O carregamento do ficheiro pode demorar alguns minutos.
11. Depois de concluído o carregamento, verá o ficheiro no arquivo de dados na pasta que criou. Agora, carregue o segundo ficheiro VMDK para o mesmo arquivo de dados. Quando os dois ficheiros estiverem carregados, são intercalados num único ficheiro. Em seguida, verá um único ficheiro no diretório.

    ![Dois arquivos VMDK são mesclados num único arquivo](./media/data-box-gateway-deploy-provision-vmware/image8.png)

12. Volte à janela do cliente vSphere. No painel Navigator (Navegador), selecione **Virtual Machines** (Máquinas virtuais). No painel da direita, clique em **Create/Register VM** (Criar/Registar VM).

    ![Criar ou registar a VM](./media/data-box-gateway-deploy-provision-vmware/image9.png)

13. É apresentada o ecrã **New Virtual Machine** (Nova máquina virtual). Em Select creation type (Selecionar tipo de criação), escolha **Create a new virtual machine** (Criar uma nova máquina virtual) e clique em **Next** (Seguinte).
    ![Selecione a página de tipo de criação](./media/data-box-gateway-deploy-provision-vmware/image10.png)

14. Na página **Select a Name and OS Name and Location** (Selecionar um nome, nome de SO e localização), especifique o **Nome** da sua máquina virtual. Este nome deve corresponder ao nome da pasta (melhor prática recomendada) que especificou anteriormente no Passo 7. Em **Guest OS family** (Família do SO convidado), escolha Windows, e em **Guest OS version** (Versão do SO convidado), escolha Microsoft Windows Server 2016 (64-bit). Clique em **Seguinte**.

    ![Selecionar uma página de nome e o nome do SO e o local](./media/data-box-gateway-deploy-provision-vmware/image11.png)

15. Na página **Select storage** (Selecionar armazenamento), selecione o arquivo de dados que pretende utilizar para aprovisionar a VM. Clique em **Seguinte**.

    ![Selecione a página de armazenamento](./media/data-box-gateway-deploy-provision-vmware/image12.png)
16. Na página **Customize settings** (Personalizado definições), defina **CPU** como 4, **Memory** (Memória) como 8192 MB (ou mair) e **Hard disk 1** (Disco rígido 1) como 2 TB (ou mais). Escolha **Disco rígido SCSI** para adicionar. Neste caso, foi LSI Logic SAS. **Os discos IDE estáticos não são suportados.** O **Disco rígido 1** é o disco virtual de dados. Tenha em atenção que não pode encolher o disco depois de aprovisionado.

    ![Personalizar a página de definições](./media/data-box-gateway-deploy-provision-vmware/image13.png)

    Na mesma página, clique em **Add hard disk** (Adicionar disco rígido) e selecione **Existing hard disk** (Disco rígido existente). Selecione o ficheiro VMDK no arquivo de dados. Esta ação adicionará um disco de SO. 

     ! Personalizar a página de definições[](./media/data-box-gateway-deploy-provision-vmware/image14.png)

    Desloque a página para baixo até ver **New hard disk** (Novo disco rígido) e expanda-o para ver as definições. Defina **Virtual Device Node** (Nó de dispositivo virtual) como **IDE controller 0** (Controlador IDE 0).

     ![Personalizar a página de definições](./media/data-box-gateway-deploy-provision-vmware/image15.png)

17. (Opcional) *Efetue este passo apenas se estiver a executar o VMware ESXi servidor 6.7*. Sobre o **personalizar definições** página, clique em **opções de VM**. Aceda a **opções de arranque > Firmware** e altere-o para **BIOS**. Por predefinição, o valor é definido como EFI. Clique em **Seguinte**.

    ![Personalizar a página de definições se 6.7 de servidor do VMware ESXi a executar](./media/data-box-gateway-deploy-provision-vmware/image15a.png)

18. Na página **Ready to Complete** (Pronto para concluir), reveja todas as definições associadas à nova máquina virtual. Verifique se a CPU é 4, a memória é 8192 MB, a interface de rede é 1 e o Disco rígido 2 tem o Controlador IDE 0. Clique em **Concluir**.
   
    ![Pronto para a página completa](./media/data-box-gateway-deploy-provision-vmware/image16.png)
    ![pronto para a página completa](./media/data-box-gateway-deploy-provision-vmware/image17.png)

A sua máquina virtual está agora aprovisionada. Verá uma notificação para o efeito e a nova máquina virtual é adicionada à lista de VMs.

![Nova máquina virtual adicionada à lista de VMs](./media/data-box-gateway-deploy-provision-vmware/image17.png)

A próxima etapa é ativar esta VM e obter o endereço IP.

> [!NOTE]
> Recomendamos que não instale ferramentas VMware no seu dispositivo virtual (como aprovisionado acima). A instalação de ferramentas VMware resultará numa configuração não suportada.

## <a name="start-the-virtual-device-and-get-the-ip"></a>Iniciar o dispositivo virtual e obter o IP

Execute os passos seguintes para iniciar o dispositivo virtual e ligar ao mesmo.

#### <a name="to-start-the-virtual-device"></a>Para iniciar o dispositivo virtual
1. Inicie o dispositivo virtual. No painel da direita, selecione o seu dispositivo na lista de VMs e clique com o botão direito do rato para abrir o menu de contexto. Selecione **Power** (Alimentação) e, em seguida, selecione **Power on** (Ligar). Esta ação deve ligar a máquina virtual. Pode ver o estado no painel inferior do cliente Web.

    ![Ligar o dispositivo virtual](./media/data-box-gateway-deploy-provision-vmware/image19.png)

2. Mais uma vez, selecione a sua VM. Clique com o botão direito do rato e selecione **Console** (Consola) e, em seguida, selecione **Open in a new window** (Abrir numa nova janela).

    ![Abra a consola do dispositivo virtual](./media/data-box-gateway-deploy-provision-vmware/image20.png)

3. A consola da máquina virtual abre numa nova janela. 

    ![Consola do dispositivo virtual](./media/data-box-gateway-deploy-provision-vmware/image21.png)

4. Quando o dispositivo estiver em execução, aponte o cursor e clique no separador na parte central superior da janela da consola. Selecione **Guest OS (SO convidado) > Send keys (Enviar chaves) > Ctrl+Alt+Delete**. Esta ação irá desbloquear a VM.

   ![Desbloquear o dispositivo virtual](./media/data-box-gateway-deploy-provision-vmware/image22.png)

5. Indique a palavra-passe para iniciar sessão na máquina. A palavra-passe predefinido é *Password1*.

   ![Introduza a palavra-passe do dispositivo virtual](./media/data-box-gateway-deploy-provision-vmware/image23.png)

6. Os passos de 5 a 7 aplicam-se apenas se estiver a iniciar num ambiente não DHCP. Se estiver num ambiente DHCP, ignore estes passos e avance para o passo 8. Se reiniciei o seu dispositivo no ambiente de não-DHCP, verá uma mensagem para o efeito: **Utilize o cmdlet Set-HcsIPAddress para configurar a rede**. 
   
7. Para configurar a rede, na linha de comandos, utilize o comando `Get-HcsIpAddress` para listar as interfaces de rede ativadas no seu dispositivo virtual. Se o seu dispositivo tiver uma única interface de rede ativada, o nome predefinido atribuído a esta interface é `Ethernet`.

8. Utilize o cmdlet `Set-HcsIpAddress` para configurar a rede. Apresentamos um exemplo abaixo:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

9. Depois de a configuração inicial estar concluída e o dispositivo iniciado, verá o texto da faixa do dispositivo. Tome nota do endereço IP e do URL apresentado no texto da faixa para gerir o dispositivo. Vai utilizar este endereço IP para ligar à IU da Web do seu dispositivo virtual e concluir a configuração local e a ativação.

   ![URL de texto e a ligação de faixa de dispositivo virtual](./media/data-box-gateway-deploy-provision-vmware/image24.png)

Se o dispositivo não cumprir os requisitos mínimos de configuração, verá um erro no texto da faixa (apresentado abaixo). Terá de modificar a configuração do dispositivo de modo a que tenha os recursos adequados para satisfazer os requisitos mínimos. Em seguida, pode reiniciar e ligar ao dispositivo. Consulte os requisitos mínimos de configuração em [verificar se o sistema anfitrião cumpre os requisitos mínimos do dispositivo virtual](#check-the-host-system).

Se tiver qualquer outro erro durante a configuração inicial com a IU web local, consulte os fluxos de trabalho seguintes:

- [Execute testes de diagnóstico para resolver problemas de configuração de interface do Usuário da web](data-box-gateway-troubleshoot.md#run-diagnostics).
- [Gerar o pacote de registo e ver ficheiros de registo](data-box-gateway-troubleshoot.md#collect-support-package).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a conhecer tópicos do Data Box Gateway, como:

> [!div class="checklist"]
> * Confirmar que o anfitrião cumpre os requisitos mínimos do dispositivo.
> * Aprovisionar um dispositivo virtual no VMware
> * Iniciar o dispositivo virtual e obter o endereço IP

Avance para o próximo tutorial para aprender a ligar, configurar e ativar o dispositivo virtual.

* [Configurar e ligar a partilhas no Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)

