---
title: Aprovisionar a matriz Virtual StorSimple no Hyper-V | Documentos da Microsoft
description: Este segundo tutorial na implementação do StorSimple Virtual Array envolve o aprovisionamento de uma matriz virtual no Hyper-V.
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
ms.date: 03/15/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5104d630e2b4e97b80a6fedfb6d863061c2722fb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61416340"
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>Implementar matriz Virtual StorSimple - aprovisionamento no Hyper-V
![](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Descrição geral
Este tutorial descreve como aprovisionar uma matriz Virtual do StorSimple num sistema anfitrião com Hyper-V no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2. Este artigo aplica-se para a implementação de matrizes virtuais do StorSimple no portal do Azure e a Cloud do Microsoft Azure Government.

Necessita de privilégios de administrador para aprovisionar e configurar uma matriz virtual. O aprovisionamento e a configuração inicial demoram cerca de dez minutos a concluir.

## <a name="provisioning-prerequisites"></a>Pré-requisitos de aprovisionamento
Aqui encontrará as pré-requisitos para Aprovisionar uma matriz virtual num sistema anfitrião com Hyper-V no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2.

### <a name="for-the-storsimple-device-manager-service"></a>Para o serviço Gestor de Dispositivos do StorSimple
Antes de começar, certifique-se de que:

* Concluiu todos os passos [preparar o portal do StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).
* Transferir a imagem da matriz virtual do Hyper-V do portal do Azure. Para obter mais informações, consulte **passo 3: Transferir a imagem da matriz virtual** dos [preparar o portal para o Guia do StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).

  > [!IMPORTANT]
  > O software em execução no StorSimple Virtual Array só pode ser utilizado com o serviço StorSimple Device Manager.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>Para a matriz Virtual StorSimple
Antes de implementar uma matriz virtual, certifique-se de que:

* Tem acesso a um sistema anfitrião com Hyper-V no Windows Server 2008 R2 ou posterior que pode ser utilizados para um aprovisionar um dispositivo.
* O sistema anfitrião é capaz de dedicar os seguintes recursos para aprovisionar a sua matriz virtual:

  * Um mínimo de 4 núcleos.
  * Pelos menos 8 GB de RAM. Se quiser configurar a matriz virtual como servidor de ficheiros, 8 GB suporta inferior a 2 milhões de ficheiros. Terá de 16 GB de RAM para oferecer suporte a 2-4 milhões de ficheiros.
  * Uma interface de rede.
  * Um disco virtual 500 GB de dados.

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no datacenter
Antes de começar, reveja os requisitos de rede para implementar uma matriz Virtual StorSimple e configurar a rede de centro de dados adequadamente. Para obter mais informações, consulte [StorSimple Virtual Array, requisitos de rede](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Provisionamento passo a passo
Para aprovisionar e ligar a uma matriz virtual, terá de efetuar os seguintes passos:

1. Certifique-se de que o sistema anfitrião tem recursos suficientes para cumprir os requisitos mínimos de matriz virtual.
2. Aprovisione uma matriz virtual no seu hipervisor.
3. Inicie a matriz virtual e obter o endereço IP.

Cada uma dessas etapas é explicada nas seções a seguir.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>Passo 1: Certifique-se de que o sistema anfitrião cumpre os requisitos de matriz virtual mínimo
Para criar uma matriz virtual, terá de:

* A função de Hyper-V instalada no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 SP1.
* Do Microsoft Hyper-V Manager, num cliente Microsoft Windows ligado ao anfitrião.

Certifique-se de que o hardware subjacente (sistema de host) no qual está a criar a matriz virtual é capaz de dedicar os seguintes recursos para a sua matriz virtual:

* Um mínimo de 4 núcleos.
* Pelos menos 8 GB de RAM. Se quiser configurar a matriz virtual como servidor de ficheiros, 8 GB suporta inferior a 2 milhões de ficheiros. Terá de 16 GB de RAM para oferecer suporte a 2-4 milhões de ficheiros.
* Uma interface de rede.
* Um disco virtual 500 GB de dados do sistema.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>Passo 2: Aprovisionar uma matriz virtual no hipervisor
Execute os passos seguintes para aprovisionar um dispositivo no seu hipervisor.

#### <a name="to-provision-a-virtual-array"></a>Para Aprovisionar uma matriz virtual
1. No anfitrião do Windows Server, copie a imagem de matriz virtual numa unidade local. Esta imagem (VHD ou VHDX) que transferiu através do portal do Azure. Tome nota da localização onde copiou a imagem, uma vez que vai utilizar esta imagem mais à frente no procedimento.
2. Abra o **Gestor de Servidor**. No canto superior direito, clique em **Tools** (Ferramentas) e selecione **Hyper-V Manager**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Se estiver a executar o Windows Server 2008 R2, abra o Gestor de Hyper-V. No Gestor de servidor, clique em **funções > Hyper-V > Gestor de Hyper-V**.
3. No **Hyper-V Manager**, no painel de âmbito, clique com o botão direito do rato no nó do seu sistema para abrir o menu de contexto e clique em **Novo** > **Máquina Virtual**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. Na página **Before you begin** (Antes de começar) do New Virtual Machine Wizard (Assistente de Nova Máquina Virtual), clique em **Next** (Seguinte).
5. Sobre o **Especificar nome e localização** , indique um **nome** para a sua matriz virtual. Clique em **Seguinte**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. Sobre o **especificar geração** página, escolha o tipo de imagem do dispositivo e, em seguida, clique em **próxima**. Esta página não aparece se estiver a utilizar o Windows Server 2008 R2.

   * Escolher **geração 2** se transferiu uma imagem. vhdx para o Windows Server 2012 ou posterior.
   * Escolher **geração 1** se transferiu uma imagem. vhd para o Windows Server 2008 R2 ou posterior.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. Na página **Assign memory** (Atribuir memória), especifique **Startup memory** (Memória de arranque) como, pelo menos, **8192 MB**, não ative a memória dinâmica e clique em **Next** (Seguinte).

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. Na página **Configure networking** (Configurar rede), especifique o comutador virtual que está ligado à Internet e clique em **Next** (Seguinte).

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. Sobre o **ligar o disco rígido virtual** página, selecione **utilizar um disco rígido virtual existente**, especifique a localização da imagem da matriz virtual (. vhdx ou. vhd) e, em seguida, clique em **seguinte**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Reveja **Summary** (Resumo) e clique em **Finish** (Concluir) para criar a máquina virtual.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. Para cumprir os requisitos mínimos, precisa de quatro núcleos. Para adicionar quatro processadores virtuais, selecione o seu sistema anfitrião, na janela **Hyper-V Manager**. No painel do lado direito, na lista **Virtual Machines** (Máquinas Virtuais), localize a máquina virtual que acabou de criar. Selecione e clique com o botão direito do rato no nome da máquina e selecione **Settings** (Definições).

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. Na página **Settings** (Definições), no painel do lado esquerdo, clique em **Processor** (Processador). No painel do lado direito, defina **number of virtual processors** (número de processadores virtuais) como quatro (ou mais). Clique em **Aplicar**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. Para cumprir os requisitos mínimos, também tem de adicionar um disco de dados virtual de 500 GB. Na página **Settings** (Definições):

    1. No painel do lado esquerdo, selecione **SCSI Controller** (Controlador SCSI).
    2. No painel do lado direito, selecione **Hard Drive** (Disco Rígido) e clique em **Add** (Adicionar).

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. Na página **Hard drive** (Disco Rígido), selecione a opção **Virtual hard disk** (Disco rígido virtual) e clique em **New** (Novo). É iniciado o **New Virtual Hard Disk Wizard** (Assistente de Novo Disco Rígido Virtual).

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. Na página **Before you begin** (Antes de começar) do New Virtual Hard Disk Wizard (Assistente de Novo Disco Rígido Virtual), clique em **Next** (Seguinte).
16. Na página **Choose Disk Format** (Escolher Formato do Disco), aceite a opção predefinida, que é o formato **VHDX**. Clique em **Seguinte**. Este ecrã não é apresentado se o Windows Server 2008 R2.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. Na página **Choose Disk Type page** (Escolher Tipo de Disco), defina o tipo do disco rígido virtual como **Dynamically expanding** (Expansão dinâmica), que é a recomendação. **Fixed size disk** (Disco de tamanho fixo) também funcionaria, mas teria de esperar muito tempo. Não recomendamos a utilização da opção **Differencing** (Diferenciação). Clique em **Seguinte**. No Windows Server 2012 R2 e Windows Server 2012, **expansão dinâmica** é a opção predefinida, enquanto que no Windows Server 2008 R2, a predefinição é **fixos de tamanho**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. Na página **Specify Name and Location** (Especificar Nome e Localização), indique **name** (nome) e **location** (localização, para a qual pode navegar) para o disco de dados. Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. Sobre o **configurar disco** página, selecione a opção **criar um novo disco de rígido virtual em branco** e especifique o tamanho **500 GB** (ou mais). Embora a 500 GB é o requisito mínimo, sempre pode aprovisionar um disco maior. Tenha em atenção que não é possível expandir ou diminuir o disco aprovisionado uma vez. Para obter mais informações sobre o tamanho do disco para aprovisionar, consulte a secção de dimensionamento na [documento de práticas recomendado](storsimple-ova-best-practices.md). Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. Na página **Summary** (Resumo), reveja os detalhes do disco de dados virtual e, se estiver satisfeito, clique em **Finish** (Concluir) para criar o disco. O assistente fecha-se e é adicionado um disco rígido virtual à sua máquina.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Regresse à página **Settings** (Definições). Clique em **OK** para fechar a página **Settings** (Definições) e regressar à janela do Hyper-V Manager.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>Passo 3: Inicie a matriz virtual e obtenha o IP
Execute os seguintes passos para começar a sua matriz virtual e ligá-la.

#### <a name="to-start-the-virtual-array"></a>Para iniciar a matriz virtual
1. Inicie a matriz virtual.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. Quando o dispositivo estiver em execução, selecione-o, clique com o botão direito do rato e selecione **Connect** (Ligar).

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Poderá ter de aguardar 5 a 10 minutos para o dispositivo esteja pronto. É apresentada uma mensagem de estado na consola, para indicar o progresso. Quando o dispositivo estiver pronto, aceda a **Action** (Ação). Prima `Ctrl + Alt + Delete` para iniciar sessão para a matriz virtual. O utilizador predefinido é *StorSimpleAdmin* e a palavra-passe predefinida é *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Por motivos de segurança, a palavra-passe de administrador do dispositivo expira após o primeiro início de sessão. É-lhe pedido que altere a palavra-passe.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Introduza uma palavra-passe com, pelo menos, 8 carateres. A palavra-passe tem de satisfazer, pelo menos, três dos quatro requisitos seguintes: letras maiúsculas, letras minúsculas, números e carateres especiais. Reintroduza a palavra-passe para a confirmar. É notificado de que a palavra-passe foi alterada.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. Depois da palavra-passe é alterada com êxito, a matriz virtual pode reiniciar. Aguarde pelo início do dispositivo.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    É apresentada a consola Windows PowerShell do dispositivo, juntamente com uma barra de progresso.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. Os passos 6 a 8 aplicam-se apenas se estiver a iniciar num ambiente não DHCP. Se estiver num ambiente DHCP, ignore estes passos e avance para o passo 9. Se reiniciei o seu dispositivo no ambiente de não-DHCP, verá o ecrã seguinte.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    Em seguida, configure a rede.
7. Utilize o `Get-HcsIpAddress` comando para listar as interfaces de rede ativadas na sua matriz virtual. Se o seu dispositivo tiver uma única interface de rede ativada, o nome predefinido atribuído a essa interface é `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. Utilize o cmdlet `Set-HcsIpAddress` para configurar a rede. Veja o seguinte exemplo:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. Depois de a configuração inicial estar concluída e o dispositivo iniciado, verá o texto da faixa do dispositivo. Tome nota do endereço IP e do URL apresentado no texto da faixa para gerir o dispositivo. Utilize este endereço IP para ligar a sua matriz virtual IU da web e concluir a configuração local e o registo.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. (Opcional) Efetue este passo apenas se estiver a implementar o seu dispositivo na Cloud do Governo. Agora irá ativar o modo de Estados Unidos Federal Information Processing Standard (FIPS) no seu dispositivo. A norma FIPS 140 define os algoritmos criptográficos aprovados para utilização pelos sistemas de computador do Governo Federal para a proteção de dados confidenciais.

    1. Para ativar o modo FIPS, execute o seguinte cmdlet:

        `Enable-HcsFIPSMode`
    2. Reinicie o seu dispositivo depois de ter ativado o modo FIPS para que as validações criptográficas em vigor.

       > [!NOTE]
       > Pode ativar ou desativar o modo FIPS no seu dispositivo. Alternar o dispositivo entre o modo FIPS e não FIPS não é suportada.
       >
       >

Se o dispositivo não cumprir os requisitos mínimos de configuração, verá o erro seguinte no texto da faixa (mostrado abaixo). Modifique a configuração do dispositivo de modo a que a máquina tenha os recursos adequados para satisfazer os requisitos mínimos. Em seguida, pode reiniciar e ligar ao dispositivo. Consulte os requisitos mínimos de configuração no passo 1: Certifique-se de que o sistema anfitrião cumpre os requisitos de matriz virtual mínimo.

![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Se tiver qualquer outro erro durante a configuração inicial com a IU web local, consulte os fluxos de trabalho seguintes:

* Execute testes de diagnóstico [resolver problemas de configuração de interface do Usuário da web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Gerar o pacote de registo e ver ficheiros de registo](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Passos Seguintes
* [Configurar a sua matriz Virtual StorSimple como um servidor de ficheiros](storsimple-virtual-array-deploy3-fs-setup.md)
* [Configurar a sua matriz Virtual StorSimple como um servidor de iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
