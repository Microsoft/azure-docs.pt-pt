---
title: Provisionar o StorSimple Virtual Array no VMware
description: Este segundo tutorial da série de implantação do StorSimple virtual array envolve o provisionamento de um dispositivo virtual no VMware.
author: alkohli
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5f5ded3faec3a080022eea70de2cca5d27529c4d
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76272085"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>Implantar o StorSimple virtual array-Provision no VMware
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Visão geral

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Este tutorial descreve como provisionar e conectar-se a uma matriz virtual StorSimple em um sistema de host executando o VMware ESXi 5,0, 5,5, 6,0 ou 6,5. Este artigo se aplica à implantação de matrizes virtuais do StorSimple no portal do Azure e na nuvem de Microsoft Azure Governamental.

Necessita de privilégios de administrador para aprovisionar e ligar a um dispositivo virtual. O aprovisionamento e a configuração inicial demoram cerca de 10 minutos a concluir.

## <a name="provisioning-prerequisites"></a>Pré-requisitos de provisionamento
Os pré-requisitos para provisionar um dispositivo virtual em um sistema de host executando o VMware ESXi 5,0, 5,5, 6,0 ou 6,5 são os seguintes.

### <a name="for-the-storsimple-device-manager-service"></a>Para o serviço Gestor de Dispositivos do StorSimple
Antes de começar, certifique-se de que:

* Você concluiu todas as etapas em [preparar o portal para o StorSimple virtual array](storsimple-virtual-array-deploy1-portal-prep.md).
* Você baixou a imagem do dispositivo virtual para VMware do portal do Azure. Para obter mais informações, consulte **etapa 3: baixar o dispositivo virtual imagem** de [preparar o portal para o guia do StorSimple virtual array](storsimple-virtual-array-deploy1-portal-prep.md).

### <a name="for-the-storsimple-virtual-device"></a>Para o dispositivo virtual StorSimple
Antes de implementar um dispositivo virtual, certifique-se de que:

* Você tem acesso a um sistema de host executando o Hyper-V (2008 R2 ou posterior) que pode ser usado para provisionar um dispositivo.
* O sistema anfitrião tem capacidade para dedicar os seguintes recursos para aprovisionar o seu dispositivo virtual:

  * Um mínimo de 4 núcleos.
  * Pelos menos 8 GB de RAM. Se você planeja configurar a matriz virtual como servidor de arquivos, 8 GB oferece suporte a menos de 2 milhões arquivos. Você precisa de 16 GB de RAM para dar suporte a 2-4 milhões de arquivos.
  * Uma interface de rede.
  * Um disco virtual de 500 GB para dados do sistema.

### <a name="for-the-network-in-datacenter"></a>Para a rede no datacenter
Antes de começar, certifique-se de que:

* Você analisou os requisitos de rede para implantar um dispositivo virtual StorSimple e configurou a rede do datacenter de acordo com os requisitos. 

## <a name="step-by-step-provisioning"></a>Provisionamento passo a passo
Para provisionar e conectar-se a um dispositivo virtual, você precisa executar as seguintes etapas:

1. Verifique se o sistema host tem recursos suficientes para atender aos requisitos mínimos de dispositivo virtual.
2. Provisionar um dispositivo virtual em seu hipervisor.
3. Inicie o dispositivo virtual e obtenha o endereço IP.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Etapa 1: garantir que o sistema host atenda aos requisitos mínimos de dispositivo virtual
Para criar um dispositivo virtual, será necessário:

* Acesso a um sistema de host executando o VMware ESXi Server 5,0, 5,5, 6,0 ou 6,5.
* Ter o cliente VMware vSphere no seu sistema para gerir o anfitrião ESXi.

  * Um mínimo de 4 núcleos.
  * Pelos menos 8 GB de RAM. Se você planeja configurar a matriz virtual como servidor de arquivos, 8 GB oferece suporte a menos de 2 milhões arquivos. Você precisa de 16 GB de RAM para dar suporte a 2-4 milhões de arquivos.
  * Uma interface de rede ligada à rede com capacidade para encaminhar o tráfego para a Internet. A largura de banda mínima da Internet deve ser de 5 Mbps para permitir o funcionamento ideal do dispositivo.
  * Um disco virtual de 500 GB para dados.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Etapa 2: provisionar um dispositivo virtual no hipervisor
Execute os passos seguintes para aprovisionar um dispositivo virtual no seu hipervisor.

1. Copie a imagem do dispositivo virtual no seu sistema. Você baixou essa imagem virtual por meio do portal do Azure.

   1. Verifique se você baixou o arquivo de imagem mais recente. Se você baixou a imagem anteriormente, baixe-a novamente para garantir que você tem a imagem mais recente. A imagem mais recente tem dois arquivos (em vez de um).
   2. Tome nota da localização onde copiou a imagem, uma vez que vai utilizar esta imagem mais à frente no procedimento.

2. Inicie sessão no servidor ESXi com o cliente vSphere. Tem de ter privilégios de administrador para criar uma máquina virtual.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. No cliente vSphere, na seção inventário no painel esquerdo, selecione o servidor ESXi.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Carregue o VMDK para o servidor ESXi. Navegue até a guia **configuração** no painel direito. Em **hardware**, selecione **armazenamento**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. No painel da direita, em **Datastores** (Arquivos de dados), selecione o arquivo de dados onde pretende carregar o VMDK. O repositório de dados deve ter espaço livre suficiente para o sistema operacional e os discos de disco.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Clique com o botão direito do rato e selecione **Browse Datastore** (Procurar arquivo de dados).

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. É apresentada a janela **Datastore Browser** (Browser de arquivos de dados).

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Na barra de ferramentas, clique no ícone ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) para criar uma nova pasta. Especifique o nome da pasta e tome nota do mesmo. Vai utilizar este nome de pasta mais tarde quando criar uma máquina virtual (melhor prática recomendada). Clique em **OK**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. A nova pasta aparece no painel esquerdo do **Datastore Browser** (Browser de arquivos de dados).

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Clique no ícone carregar ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png) e selecione **carregar arquivo**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Procure e aponte para os ficheiros VMDK que descarregou. Existem dois ficheiros. Selecione um ficheiro para carregar.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Clique em **Open** (Abrir). O carregamento do ficheiro VMDK para o arquivo de dados especificado é iniciado. O carregamento do ficheiro pode demorar alguns minutos.
13. Depois de concluído o carregamento, verá o ficheiro no arquivo de dados na pasta que criou.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Agora, carregue o segundo ficheiro VMDK para o mesmo arquivo de dados.
14. Volte à janela do cliente vSphere. Com o servidor ESXi selecionado, clique com o botão direito do mouse e selecione **nova máquina virtual**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. Uma janela **criar nova máquina virtual** será exibida. Na página **configuração** , selecione a opção **personalizado** . Clique em **Seguinte**.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. Na página **nome e local** , especifique o nome da sua máquina virtual. Esse nome deve corresponder ao nome da pasta (melhor prática recomendada) que você especificou anteriormente na etapa 8.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. Na página **armazenamento** , selecione um repositório de armazenamento que você deseja usar para provisionar sua VM.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. Na página **versão da máquina virtual** , selecione **versão da máquina virtual: 8**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. Na página **sistema operacional convidado** , selecione o **sistema operacional convidado** como **Windows**. Para **versão**, na lista suspensa, selecione **Microsoft Windows Server 2012 (64 bits)** .

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. Na página **CPUs** , ajuste o **número de soquetes virtuais** e o **número de núcleos por soquete virtual** para que o **número total de núcleos** seja 4 (ou mais). Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. Na página **memória** , ESPECIFIQUE 8 GB (ou mais) de RAM. Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. Na página **rede** , especifique o número de interfaces de rede. O requisito mínimo é uma interface de rede.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Na página **controlador SCSI** , aceite o **controlador SAS da LSI Logic**padrão.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. Na página **selecionar um disco** , escolha **usar um disco virtual existente**. Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. Na página **selecionar disco existente** , em **caminho do arquivo de disco**, clique em **procurar**. Isso abre uma caixa de diálogo **procurar repositórios de armazenamento** . Navegue até o local onde você carregou o VMDK. Agora você verá apenas um arquivo no repositório de armazenamento, pois os dois arquivos que você carregou inicialmente foram mesclados. Selecione o arquivo e clique em **OK**. Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. Na página **Opções avançadas** , aceite o padrão e clique em **Avançar**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. Na página **Ready to Complete** (Pronto para concluir), reveja todas as definições associadas à nova máquina virtual. Marque **Editar as configurações da máquina virtual antes da conclusão**. Clique em **Continue** (Continuar).

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. Na página de **Propriedades máquinas virtuais** , na guia **hardware** , localize o hardware do dispositivo. Selecione **novo disco rígido**. Clique em **Adicionar**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Você verá uma janela **adicionar hardware** . Na página **tipo de dispositivo** , em **escolha o tipo de dispositivo que você deseja adicionar**, selecione **disco rígido**e clique em **Avançar**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. Na página **selecionar um disco** , escolha **criar um novo disco virtual**. Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. Na página **criar um disco** , altere o **tamanho do disco** para 500 GB (ou mais). Embora 500 GB seja o requisito mínimo, você sempre pode provisionar um disco maior. Observe que você não pode expandir ou reduzir o disco depois de provisionado. Para obter mais informações sobre o tamanho do disco a ser provisionado, examine a seção de dimensionamento no [documento de práticas recomendadas](storsimple-ova-best-practices.md). Em **provisionamento de disco**, selecione **provisionamento thin**. Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. Na página **Opções avançadas** , aceite o padrão.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. Na página **pronto para concluir** , examine as opções de disco. Clique em **Concluir**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Retorne à página de propriedades da máquina virtual. Um novo disco rígido é adicionado à sua máquina virtual. Clique em **Concluir**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. Com sua máquina virtual selecionada no painel direito, navegue até a guia **Resumo** . Examine as configurações de sua máquina virtual.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

A sua máquina virtual está agora aprovisionada. O passo seguinte é ligar esta máquina e obter o endereço IP.

> [!NOTE]
> Recomendamos que você não instale as ferramentas do VMware em sua matriz virtual (como provisionado acima). A instalação de ferramentas VMware resultará numa configuração não suportada.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Etapa 3: iniciar o dispositivo virtual e obter o IP
Execute os passos seguintes para iniciar o dispositivo virtual e ligar ao mesmo.

#### <a name="to-start-the-virtual-device"></a>Para iniciar o dispositivo virtual
1. Inicie o dispositivo virtual. No Configuration Manager vSphere, no painel esquerdo, selecione seu dispositivo e clique com o botão direito do mouse para abrir o menu de contexto. Selecione **Power** (Alimentação) e, em seguida, selecione **Power on** (Ligar). Esta ação deve ligar a máquina virtual. Você pode exibir o status no painel **últimas tarefas recentes** do cliente vSphere.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. As tarefas de instalação levarão alguns minutos para serem concluídas. Depois que o dispositivo estiver em execução, navegue até a guia **console** . envie Ctrl + Alt + Delete para fazer logon no dispositivo. Como alternativa, você pode apontar o cursor na janela do console e pressionar Ctrl + Alt + Insert. O usuário padrão é *StorSimpleAdmin* e a senha padrão é *password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Por motivos de segurança, a palavra-passe de administrador do dispositivo expira após o primeiro início de sessão. É-lhe pedido que altere a palavra-passe.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Introduza uma palavra-passe com, pelo menos, 8 carateres. A senha deve conter 3 desses quatro requisitos: letras maiúsculas, minúsculas, números e caracteres especiais. Reintroduza a palavra-passe para a confirmar. Você será notificado de que a senha foi alterada.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. Depois que a senha for alterada com êxito, o dispositivo virtual poderá ser reinicializado. Aguarde a conclusão da reinicialização. O console do Windows PowerShell do dispositivo pode ser exibido junto com uma barra de progresso.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. Os passos 6 a 8 aplicam-se apenas se estiver a iniciar num ambiente não DHCP. Se estiver num ambiente DHCP, ignore estes passos e avance para o passo 9. Se você tiver inicializado seu dispositivo em um ambiente não-DHCP, verá a tela a seguir.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Em seguida, configure a rede.
7. Use o comando `Get-HcsIpAddress` para listar as interfaces de rede habilitadas em seu dispositivo virtual. Se o seu dispositivo tiver uma única interface de rede ativada, o nome predefinido atribuído a essa interface é `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Utilize o cmdlet `Set-HcsIpAddress` para configurar a rede. Apresentamos um exemplo abaixo:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Depois de a configuração inicial estar concluída e o dispositivo iniciado, verá o texto da faixa do dispositivo. Tome nota do endereço IP e do URL apresentado no texto da faixa para gerir o dispositivo. Você usará esse endereço IP para se conectar à interface do usuário da Web do seu dispositivo virtual e concluir a instalação local e o registro.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. Adicional Execute esta etapa somente se você estiver implantando seu dispositivo na nuvem governamental. Agora, você habilitará o modo de Estados Unidos padrão FIPS (FIPS) em seu dispositivo. O padrão FIPS 140 define os algoritmos criptográficos aprovados para uso pelos sistemas de computador do governo federal dos EUA para a proteção de dados confidenciais.

    1. Para habilitar o modo FIPS, execute o seguinte cmdlet:

        `Enable-HcsFIPSMode`
    2. Reinicialize o dispositivo depois de habilitar o modo FIPS para que as validações criptográficas entrem em vigor.

       > [!NOTE]
       > Você pode habilitar ou desabilitar o modo FIPS em seu dispositivo. Não há suporte para a alternância do dispositivo entre o modo FIPS e não FIPS.
       >
       >

Se o dispositivo não cumprir os requisitos mínimos de configuração, verá um erro no texto da faixa (apresentado abaixo). Terá de modificar a configuração do dispositivo de modo a que tenha os recursos adequados para satisfazer os requisitos mínimos. Em seguida, pode reiniciar e ligar ao dispositivo. Veja os requisitos mínimos de configuração no [Passo 1: Confirmar que o sistema anfitrião cumpre os requisitos mínimos do dispositivo virtual](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Se você enfrentar qualquer outro erro durante a configuração inicial usando a interface do usuário da Web local, consulte os seguintes fluxos de trabalho:

* Execute testes de diagnóstico para [solucionar problemas de configuração da interface do usuário](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Gerar pacote de log e exibir arquivos de log](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Passos seguintes
* [Configurar sua matriz virtual StorSimple como um servidor de arquivos](storsimple-virtual-array-deploy3-fs-setup.md)
* [Configurar sua matriz virtual StorSimple como um servidor iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
