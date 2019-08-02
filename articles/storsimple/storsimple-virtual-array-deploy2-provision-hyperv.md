---
title: Provisionar o StorSimple Virtual Array no Hyper-V | Microsoft Docs
description: Este segundo tutorial na implantação do StorSimple virtual array envolve o provisionamento de uma matriz virtual no Hyper-V.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 4354963c-e09d-41ac-9c8b-f21abeae9913
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9d3f4f4ab6cc1c928761fce740d39f3f73426e62
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516798"
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>Implantar o StorSimple virtual array-Provision no Hyper-V
![](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Descrição geral

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Este tutorial descreve como provisionar uma matriz virtual StorSimple em um sistema de host que executa o Hyper-V no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2. Este artigo se aplica à implantação de matrizes virtuais do StorSimple em portal do Azure e Microsoft Azure Governamental nuvem.

Você precisa de privilégios de administrador para provisionar e configurar uma matriz virtual. O aprovisionamento e a configuração inicial demoram cerca de dez minutos a concluir.

## <a name="provisioning-prerequisites"></a>Pré-requisitos de provisionamento
Aqui você encontrará os pré-requisitos para provisionar uma matriz virtual em um sistema de host que executa o Hyper-V no Windows Server 2012 R2, no Windows Server 2012 ou no Windows Server 2008 R2.

### <a name="for-the-storsimple-device-manager-service"></a>Para o serviço Gestor de Dispositivos do StorSimple
Antes de começar, certifique-se de que:

* Você concluiu todas as etapas em [preparar o portal para o StorSimple virtual array](storsimple-virtual-array-deploy1-portal-prep.md).
* Você baixou a imagem da matriz virtual para o Hyper-V do portal do Azure. Para obter mais informações, **consulte a etapa 3: Baixe a imagem** da matriz virtual do [guia preparar o portal para o StorSimple virtual array](storsimple-virtual-array-deploy1-portal-prep.md).

  > [!IMPORTANT]
  > O software em execução na matriz virtual StorSimple só pode ser usado com o serviço StorSimple Device Manager.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>Para a matriz virtual StorSimple
Antes de implantar uma matriz virtual, verifique se:

* Você tem acesso a um sistema de host executando o Hyper-V no Windows Server 2008 R2 ou posterior que pode ser usado para provisionar um dispositivo.
* O sistema host é capaz de dedicar os seguintes recursos para provisionar sua matriz virtual:

  * Um mínimo de 4 núcleos.
  * Pelos menos 8 GB de RAM. Se você planeja configurar a matriz virtual como servidor de arquivos, 8 GB oferece suporte a menos de 2 milhões arquivos. Você precisa de 16 GB de RAM para dar suporte a 2-4 milhões de arquivos.
  * Uma interface de rede.
  * Um disco virtual de 500 GB para dados.

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no datacenter
Antes de começar, examine os requisitos de rede para implantar uma matriz virtual StorSimple e configure a rede do datacenter adequadamente. Para obter mais informações, consulte [requisitos de rede do StorSimple virtual array](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Provisionamento passo a passo
Para provisionar e conectar-se a uma matriz virtual, você precisa executar as seguintes etapas:

1. Verifique se o sistema host tem recursos suficientes para atender aos requisitos mínimos de matriz virtual.
2. Provisione uma matriz virtual em seu hipervisor.
3. Inicie a matriz virtual e obtenha o endereço IP.

Cada uma dessas etapas é explicada nas seções a seguir.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>Passo 1: Verifique se o sistema host atende aos requisitos mínimos da matriz virtual
Para criar uma matriz virtual, você precisa de:

* A função Hyper-V instalada no Windows Server 2012 R2, no Windows Server 2012 ou no Windows Server 2008 R2 SP1.
* Do Microsoft Hyper-V Manager, num cliente Microsoft Windows ligado ao anfitrião.

Verifique se o hardware subjacente (sistema de host) no qual você está criando a matriz virtual é capaz de dedicar os seguintes recursos à sua matriz virtual:

* Um mínimo de 4 núcleos.
* Pelos menos 8 GB de RAM. Se você planeja configurar a matriz virtual como servidor de arquivos, 8 GB oferece suporte a menos de 2 milhões arquivos. Você precisa de 16 GB de RAM para dar suporte a 2-4 milhões de arquivos.
* Uma interface de rede.
* Um disco virtual de 500 GB para dados do sistema.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>Passo 2: Provisionar uma matriz virtual no hipervisor
Execute os passos seguintes para aprovisionar um dispositivo no seu hipervisor.

#### <a name="to-provision-a-virtual-array"></a>Para provisionar uma matriz virtual
1. No host do Windows Server, copie a imagem da matriz virtual para uma unidade local. Você baixou essa imagem (VHD ou VHDX) por meio do portal do Azure. Tome nota da localização onde copiou a imagem, uma vez que vai utilizar esta imagem mais à frente no procedimento.
2. Abra o **Gestor de Servidor**. No canto superior direito, clique em **Tools** (Ferramentas) e selecione **Hyper-V Manager**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Se você estiver executando o Windows Server 2008 R2, abra o Gerenciador do Hyper-V. Em Gerenciador do Servidor, clique em **funções > Hyper-v > Gerenciador do Hyper-v**.
3. No **Hyper-V Manager**, no painel de âmbito, clique com o botão direito do rato no nó do seu sistema para abrir o menu de contexto e clique em **Novo** > **Máquina Virtual**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. Na página **Before you begin** (Antes de começar) do New Virtual Machine Wizard (Assistente de Nova Máquina Virtual), clique em **Next** (Seguinte).
5. Na página **especificar nome e local** , forneça um **nome** para a matriz virtual. Clique em **Seguinte**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. Na página **especificar geração** , escolha o tipo de imagem do dispositivo e clique em **Avançar**. Esta página não aparecerá se você estiver usando o Windows Server 2008 R2.

   * Escolha **geração 2** se você baixou uma imagem. vhdx para o Windows Server 2012 ou posterior.
   * Escolha **geração 1** se você baixou uma imagem. VHD para o Windows Server 2008 R2 ou posterior.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. Na página **Assign memory** (Atribuir memória), especifique **Startup memory** (Memória de arranque) como, pelo menos, **8192 MB**, não ative a memória dinâmica e clique em **Next** (Seguinte).

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. Na página **Configure networking** (Configurar rede), especifique o comutador virtual que está ligado à Internet e clique em **Next** (Seguinte).

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. Na página **conectar disco rígido virtual** , escolha **usar um disco rígido virtual existente**, especifique o local da imagem da matriz virtual (. vhdx ou. vhd) e clique em **Avançar**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Reveja **Summary** (Resumo) e clique em **Finish** (Concluir) para criar a máquina virtual.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. Para cumprir os requisitos mínimos, precisa de quatro núcleos. Para adicionar quatro processadores virtuais, selecione o seu sistema anfitrião, na janela **Hyper-V Manager**. No painel do lado direito, na lista **Virtual Machines** (Máquinas Virtuais), localize a máquina virtual que acabou de criar. Selecione e clique com o botão direito do rato no nome da máquina e selecione **Settings** (Definições).

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. Na página **Settings** (Definições), no painel do lado esquerdo, clique em **Processor** (Processador). No painel do lado direito, defina **number of virtual processors** (número de processadores virtuais) como quatro (ou mais). Clique em **Aplicar**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. Para atender aos requisitos mínimos, você também precisa adicionar um disco de dados virtual de 500 GB. Na página **Settings** (Definições):

    1. No painel do lado esquerdo, selecione **SCSI Controller** (Controlador SCSI).
    2. No painel do lado direito, selecione **Hard Drive** (Disco Rígido) e clique em **Add** (Adicionar).

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. Na página **Hard drive** (Disco Rígido), selecione a opção **Virtual hard disk** (Disco rígido virtual) e clique em **New** (Novo). É iniciado o **New Virtual Hard Disk Wizard** (Assistente de Novo Disco Rígido Virtual).

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. Na página **Before you begin** (Antes de começar) do New Virtual Hard Disk Wizard (Assistente de Novo Disco Rígido Virtual), clique em **Next** (Seguinte).
16. Na página **Choose Disk Format** (Escolher Formato do Disco), aceite a opção predefinida, que é o formato **VHDX**. Clique em **Seguinte**. Esta tela não será apresentada se o Windows Server 2008 R2 estiver em execução.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. Na página **Choose Disk Type page** (Escolher Tipo de Disco), defina o tipo do disco rígido virtual como **Dynamically expanding** (Expansão dinâmica), que é a recomendação. **Fixed size disk** (Disco de tamanho fixo) também funcionaria, mas teria de esperar muito tempo. Não recomendamos a utilização da opção **Differencing** (Diferenciação). Clique em **Seguinte**. No Windows Server 2012 R2 e no Windows Server 2012, a **expansão dinâmica** é a opção padrão, enquanto que no Windows Server 2008 R2, o padrão é **tamanho fixo**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. Na página **Specify Name and Location** (Especificar Nome e Localização), indique **name** (nome) e **location** (localização, para a qual pode navegar) para o disco de dados. Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. Na página **Configurar disco** , selecione a opção **criar um novo disco rígido virtual em branco** e especifique o tamanho como **500 GB** (ou mais). Embora 500 GB seja o requisito mínimo, você sempre pode provisionar um disco maior. Observe que você não pode expandir ou reduzir o disco depois de provisionado. Para obter mais informações sobre o tamanho do disco a ser provisionado, examine a seção de dimensionamento no [documento de práticas recomendadas](storsimple-ova-best-practices.md). Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. Na página **Summary** (Resumo), reveja os detalhes do disco de dados virtual e, se estiver satisfeito, clique em **Finish** (Concluir) para criar o disco. O assistente fecha-se e é adicionado um disco rígido virtual à sua máquina.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Regresse à página **Settings** (Definições). Clique em **OK** para fechar a página **Settings** (Definições) e regressar à janela do Hyper-V Manager.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>Passo 3: Iniciar a matriz virtual e obter o IP
Execute as etapas a seguir para iniciar sua matriz virtual e conectar-se a ela.

#### <a name="to-start-the-virtual-array"></a>Para iniciar a matriz virtual
1. Inicie a matriz virtual.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. Quando o dispositivo estiver em execução, selecione-o, clique com o botão direito do rato e selecione **Connect** (Ligar).

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Talvez seja necessário aguardar 5-10 minutos para o dispositivo estar pronto. É apresentada uma mensagem de estado na consola, para indicar o progresso. Quando o dispositivo estiver pronto, aceda a **Action** (Ação). Pressione `Ctrl + Alt + Delete` para fazer logon na matriz virtual. O usuário padrão é *StorSimpleAdmin* e a senha padrão é *password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Por motivos de segurança, a palavra-passe de administrador do dispositivo expira após o primeiro início de sessão. É-lhe pedido que altere a palavra-passe.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Introduza uma palavra-passe com, pelo menos, 8 carateres. A palavra-passe tem de satisfazer, pelo menos, três dos quatro requisitos seguintes: letras maiúsculas, letras minúsculas, números e carateres especiais. Reintroduza a palavra-passe para a confirmar. É notificado de que a palavra-passe foi alterada.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. Depois que a senha for alterada com êxito, a matriz virtual poderá ser reiniciada. Aguarde pelo início do dispositivo.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    É apresentada a consola Windows PowerShell do dispositivo, juntamente com uma barra de progresso.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. Os passos 6 a 8 aplicam-se apenas se estiver a iniciar num ambiente não DHCP. Se estiver num ambiente DHCP, ignore estes passos e avance para o passo 9. Se você tiver inicializado seu dispositivo em um ambiente não-DHCP, verá a tela a seguir.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    Em seguida, configure a rede.
7. Use o `Get-HcsIpAddress` comando para listar as interfaces de rede habilitadas em sua matriz virtual. Se o seu dispositivo tiver uma única interface de rede ativada, o nome predefinido atribuído a essa interface é `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. Utilize o cmdlet `Set-HcsIpAddress` para configurar a rede. Veja o seguinte exemplo:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. Depois de a configuração inicial estar concluída e o dispositivo iniciado, verá o texto da faixa do dispositivo. Tome nota do endereço IP e do URL apresentado no texto da faixa para gerir o dispositivo. Use esse endereço IP para se conectar à interface do usuário da Web da matriz virtual e concluir a instalação local e o registro.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. Adicional Execute esta etapa somente se você estiver implantando seu dispositivo na nuvem governamental. Agora, você habilitará o modo de Estados Unidos padrão FIPS (FIPS) em seu dispositivo. O padrão FIPS 140 define os algoritmos criptográficos aprovados para uso pelos sistemas de computador do governo federal dos EUA para a proteção de dados confidenciais.

    1. Para habilitar o modo FIPS, execute o seguinte cmdlet:

        `Enable-HcsFIPSMode`
    2. Reinicialize o dispositivo depois de habilitar o modo FIPS para que as validações criptográficas entrem em vigor.

       > [!NOTE]
       > Você pode habilitar ou desabilitar o modo FIPS em seu dispositivo. Não há suporte para a alternância do dispositivo entre o modo FIPS e não FIPS.
       >
       >

Se o dispositivo não atender aos requisitos mínimos de configuração, você verá o seguinte erro no texto da faixa (mostrado abaixo). Modifique a configuração do dispositivo de modo a que a máquina tenha os recursos adequados para satisfazer os requisitos mínimos. Em seguida, pode reiniciar e ligar ao dispositivo. Consulte os requisitos mínimos de configuração na etapa 1: Verifique se o sistema host atende aos requisitos mínimos de matriz virtual.

![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Se você enfrentar qualquer outro erro durante a configuração inicial usando a interface do usuário da Web local, consulte os seguintes fluxos de trabalho:

* Execute testes de diagnóstico para [solucionar problemas de configuração da interface do usuário](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Gerar pacote de log e exibir arquivos de log](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Passos Seguintes
* [Configurar sua matriz virtual StorSimple como um servidor de arquivos](storsimple-virtual-array-deploy3-fs-setup.md)
* [Configurar sua matriz virtual StorSimple como um servidor iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
