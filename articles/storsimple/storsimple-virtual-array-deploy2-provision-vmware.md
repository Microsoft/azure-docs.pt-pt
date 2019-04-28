---
title: Aprovisionar a matriz Virtual StorSimple no VMware | Documentos da Microsoft
description: Este tutorial segunda série de implementação do StorSimple Virtual Array envolve o aprovisionamento de um dispositivo virtual no VMware.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c9fe597957057dc61da5c2b1cf6f9216711764a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61419159"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>Implementar matriz Virtual StorSimple - aprovisionamento no VMware
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Descrição geral
Este tutorial descreve como aprovisionar e ligar a uma matriz Virtual do StorSimple num sistema anfitrião com o VMware ESXi 5.0, 5.5, 6.0 ou 6.5. Este artigo aplica-se para a implementação de matrizes virtuais do StorSimple no portal do Azure e a Cloud do Microsoft Azure Government.

Necessita de privilégios de administrador para aprovisionar e ligar a um dispositivo virtual. O aprovisionamento e a configuração inicial demoram cerca de dez minutos a concluir.

## <a name="provisioning-prerequisites"></a>Pré-requisitos de aprovisionamento
As pré-requisitos para aprovisionar um dispositivo virtual num sistema anfitrião com o VMware ESXi 5.0, 5.5, 6.0 ou 6.5, são as seguintes.

### <a name="for-the-storsimple-device-manager-service"></a>Para o serviço Gestor de Dispositivos do StorSimple
Antes de começar, certifique-se de que:

* Concluiu todos os passos [preparar o portal do StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).
* Transferir a imagem do dispositivo virtual do VMware no portal do Azure. Para obter mais informações, consulte **passo 3: Transferir a imagem do dispositivo virtual** dos [preparar o portal para o Guia do StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).

### <a name="for-the-storsimple-virtual-device"></a>Para o dispositivo virtual StorSimple
Antes de implementar um dispositivo virtual, certifique-se de que:

* Tem acesso a um sistema anfitrião com Hyper-V (2008 R2 ou posterior) que pode ser utilizado para um aprovisionar um dispositivo.
* O sistema anfitrião tem capacidade para dedicar os seguintes recursos para aprovisionar o seu dispositivo virtual:

  * Um mínimo de 4 núcleos.
  * Pelos menos 8 GB de RAM. Se quiser configurar a matriz virtual como servidor de ficheiros, 8 GB suporta inferior a 2 milhões de ficheiros. Terá de 16 GB de RAM para oferecer suporte a 2-4 milhões de ficheiros.
  * Uma interface de rede.
  * Um disco virtual 500 GB de dados do sistema.

### <a name="for-the-network-in-datacenter"></a>Para a rede no datacenter
Antes de começar, certifique-se de que:

* Consultou os requisitos de rede para implementar um dispositivo virtual StorSimple e configurado a rede de centro de dados de acordo com os requisitos. 

## <a name="step-by-step-provisioning"></a>Provisionamento passo a passo
Para aprovisionar e ligar a um dispositivo virtual, terá de efetuar os seguintes passos:

1. Certifique-se de que o sistema anfitrião tem recursos suficientes para cumprir os requisitos de mínima do dispositivo virtual.
2. Aprovisione um dispositivo virtual no seu hipervisor.
3. Iniciar o dispositivo virtual e obter o endereço IP.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Passo 1: Certifique-se de que sistema anfitrião cumpre os requisitos de mínima do dispositivo virtual
Para criar um dispositivo virtual, terá de:

* Acesso a um sistema de anfitrião a executar o VMware ESXi servidor 5.0, 5.5, 6.0 ou 6.5.
* Ter o cliente VMware vSphere no seu sistema para gerir o anfitrião ESXi.

  * Um mínimo de 4 núcleos.
  * Pelos menos 8 GB de RAM. Se quiser configurar a matriz virtual como servidor de ficheiros, 8 GB suporta inferior a 2 milhões de ficheiros. Terá de 16 GB de RAM para oferecer suporte a 2-4 milhões de ficheiros.
  * Uma interface de rede ligada à rede com capacidade para encaminhar o tráfego para a Internet. A largura de banda mínima do Internet deve ser 5 Mbps para permitir o trabalho ideal do dispositivo.
  * Um disco virtual 500 GB de dados.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Passo 2: Aprovisionar um dispositivo virtual no hipervisor
Execute os passos seguintes para aprovisionar um dispositivo virtual no seu hipervisor.

1. Copie a imagem do dispositivo virtual no seu sistema. Transferiu a esta imagem virtual através do portal do Azure.

   1. Certifique-se de que transferiu o ficheiro de imagem mais recente. Se tiver transferido anteriormente a imagem, tem de transferi-lo novamente para garantir que tem a imagem mais recente. A imagem mais recente tem dois ficheiros (em vez de um).
   2. Tome nota da localização onde copiou a imagem, uma vez que vai utilizar esta imagem mais à frente no procedimento.

2. Inicie sessão no servidor ESXi com o cliente vSphere. Tem de ter privilégios de administrador para criar uma máquina virtual.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. No cliente do vSphere, na secção de inventário no painel esquerdo, selecione o servidor ESXi.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Carregue o VMDK para o servidor ESXi. Navegue para o **configuração** separador no painel da direita. Sob **Hardware**, selecione **armazenamento**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. No painel da direita, em **Datastores** (Arquivos de dados), selecione o arquivo de dados onde pretende carregar o VMDK. O arquivo de dados tem de ter espaço livre suficiente para os discos de SO e dados.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Clique com o botão direito do rato e selecione **Browse Datastore** (Procurar arquivo de dados).

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. É apresentada a janela **Datastore Browser** (Browser de arquivos de dados).

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Na barra de ferramentas, clique em ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) ícone para criar uma nova pasta. Especifique o nome da pasta e tome nota do mesmo. Vai utilizar este nome de pasta mais tarde quando criar uma máquina virtual (melhor prática recomendada). Clique em **OK**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. A nova pasta aparece no painel esquerdo do **Datastore Browser** (Browser de arquivos de dados).

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Clique no ícone de carregamento ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png) e selecione **carregar o ficheiro**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Procure e aponte para os ficheiros VMDK que descarregou. Existem dois ficheiros. Selecione um ficheiro para carregar.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Clique em **Open** (Abrir). O carregamento do ficheiro VMDK para o arquivo de dados especificado é iniciado. O carregamento do ficheiro pode demorar alguns minutos.
13. Depois de concluído o carregamento, verá o ficheiro no arquivo de dados na pasta que criou.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Agora, carregue o segundo ficheiro VMDK para o mesmo arquivo de dados.
14. Volte à janela do cliente vSphere. Com o servidor ESXi selecionado, clique com botão direito e selecione **Nova Máquina Virtual**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. R **Criar Nova Máquina Virtual** será apresentada a janela. Sobre o **Configuration** página, selecione o **personalizado** opção. Clique em **Seguinte**.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. Sobre o **nome e localização** página, especifique o nome da sua máquina virtual. Este nome deve corresponder ao nome da pasta (melhor prática recomendada) que especificou anteriormente no passo 8.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. Sobre o **armazenamento** , selecione um arquivo de dados que pretende utilizar para aprovisionar a VM.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. Sobre o **versão do Virtual Machine** página, selecione **versão da Máquina Virtual: 8**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. Sobre o **sistema operativo convidado** página, selecione a **sistema operativo convidado** como **Windows**. Para **versão**, na lista pendente, selecione **Microsoft Windows Server 2012 (64-bit)**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. Na **CPUs** página, ajustar a **número de soquetes virtual** e **número de núcleos por virtual socket** para que o **número Total de núcleos** é 4 (ou mais). Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. Sobre o **memória** , especifique de 8 GB (ou mais) de RAM. Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. Sobre o **rede** , especifique o número de interfaces de rede. O requisito mínimo é uma interface de rede.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Sobre o **controlador SCSI** página, aceite a predefinição **controlador LSI Logic SAS**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. Sobre o **selecione um disco** página, selecione **utilizar um disco virtual existente**. Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. Sobre o **selecionar disco existente** página, em **caminho do ficheiro de disco**, clique em **procurar**. Esta ação abre um **procurar arquivos de dados** caixa de diálogo. Navegue para a localização em que carregou o VMDK. Pode agora ver apenas um ficheiro no arquivo de dados como os dois ficheiros que carregou inicialmente foram foram mesclados. Selecione o ficheiro e clique em **OK**. Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. Sobre o **opções avançadas** página, aceite a predefinição e clique em **próxima**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. Na página **Ready to Complete** (Pronto para concluir), reveja todas as definições associadas à nova máquina virtual. Verifique **editar as definições de máquina virtual antes da conclusão**. Clique em **Continue** (Continuar).

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. Na **propriedades de máquinas virtuais** página, além do **Hardware** separador, localize o hardware do dispositivo. Selecione **novo disco rígido**. Clique em **Adicionar**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Verá um **Adicionar Hardware** janela. Sobre o **tipo de dispositivo** página, em **escolha o tipo de dispositivo que pretende adicionar**, selecione **disco rígido**e clique em **seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. Sobre o **selecione um disco** página, selecione **criar um novo disco virtual**. Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. Sobre o **criar um disco** página, altere a **tamanho do disco** a 500 GB (ou mais). Embora a 500 GB é o requisito mínimo, sempre pode aprovisionar um disco maior. Tenha em atenção que não é possível expandir ou diminuir o disco aprovisionado uma vez. Para obter mais informações sobre o tamanho do disco para aprovisionar, consulte a secção de dimensionamento na [documento de práticas recomendado](storsimple-ova-best-practices.md). Sob **disco de aprovisionamento**, selecione **aprovisionamento finos**. Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. Sobre o **opções avançadas** página, aceite a predefinição.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. Sobre o **pronto para concluir** , reveja as opções de disco. Clique em **Concluir**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Voltar à página de propriedades da Máquina Virtual. Um novo disco rígido é adicionado à sua máquina virtual. Clique em **Concluir**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. Com a sua máquina virtual selecionada no painel da direita, navegue para o **resumo** separador. Reveja as definições para a máquina virtual.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

A sua máquina virtual está agora aprovisionada. O passo seguinte é ligar esta máquina e obter o endereço IP.

> [!NOTE]
> Recomendamos que não instale as ferramentas do VMware em sua matriz virtual (como aprovisionado acima). A instalação de ferramentas VMware resultará numa configuração não suportada.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Passo 3: Iniciar o dispositivo virtual e obter o IP
Execute os passos seguintes para iniciar o dispositivo virtual e ligar ao mesmo.

#### <a name="to-start-the-virtual-device"></a>Para iniciar o dispositivo virtual
1. Inicie o dispositivo virtual. No vSphere Configuration Manager, no painel esquerdo, selecione o seu dispositivo e faça duplo clique para abrir o menu de contexto. Selecione **Power** (Alimentação) e, em seguida, selecione **Power on** (Ligar). Esta ação deve ligar a máquina virtual. Pode ver o estado na parte inferior **tarefas recentes** painel do cliente do vSphere.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. As tarefas de configuração irão demorar alguns minutos a concluir. Quando o dispositivo estiver em execução, navegue para o **consola** separador. Envie Ctrl + Alt + Delete para iniciar sessão no dispositivo. Em alternativa, pode apontar o cursor na janela de consola e prima Ctrl + Alt + Insert. O utilizador predefinido é *StorSimpleAdmin* e a palavra-passe predefinida é *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Por motivos de segurança, a palavra-passe de administrador do dispositivo expira após o primeiro início de sessão. É-lhe pedido que altere a palavra-passe.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Introduza uma palavra-passe com, pelo menos, 8 carateres. A palavra-passe tem de conter 3 de 4 estes requisitos: carateres em maiúsculas, minúsculas, numéricos e especiais. Reintroduza a palavra-passe para a confirmar. Será notificado que a palavra-passe foi alterada.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. Depois da palavra-passe é alterada com êxito, pode reiniciar o dispositivo virtual. Aguarde o reinício para concluir. A consola do Windows PowerShell do dispositivo pode ser apresentada juntamente com uma barra de progresso.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. Os passos 6 a 8 aplicam-se apenas se estiver a iniciar num ambiente não DHCP. Se estiver num ambiente DHCP, ignore estes passos e avance para o passo 9. Se reiniciei o seu dispositivo no ambiente de não-DHCP, verá o ecrã seguinte.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Em seguida, configure a rede.
7. Utilize o `Get-HcsIpAddress` comando para listar as interfaces de rede ativadas no seu dispositivo virtual. Se o seu dispositivo tiver uma única interface de rede ativada, o nome predefinido atribuído a essa interface é `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Utilize o cmdlet `Set-HcsIpAddress` para configurar a rede. Apresentamos um exemplo abaixo:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Depois de a configuração inicial estar concluída e o dispositivo iniciado, verá o texto da faixa do dispositivo. Tome nota do endereço IP e do URL apresentado no texto da faixa para gerir o dispositivo. Utilizará este endereço IP para ligar para a web da interface do Usuário do seu dispositivo virtual e concluir a configuração local e o registo.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. (Opcional) Efetue este passo apenas se estiver a implementar o seu dispositivo na Cloud do Governo. Agora irá ativar o modo de Estados Unidos Federal Information Processing Standard (FIPS) no seu dispositivo. A norma FIPS 140 define os algoritmos criptográficos aprovados para utilização pelos sistemas de computador do Governo Federal para a proteção de dados confidenciais.

    1. Para ativar o modo FIPS, execute o seguinte cmdlet:

        `Enable-HcsFIPSMode`
    2. Reinicie o seu dispositivo depois de ter ativado o modo FIPS para que as validações criptográficas em vigor.

       > [!NOTE]
       > Pode ativar ou desativar o modo FIPS no seu dispositivo. Alternar o dispositivo entre o modo FIPS e não FIPS não é suportada.
       >
       >

Se o dispositivo não cumprir os requisitos mínimos de configuração, verá um erro no texto da faixa (apresentado abaixo). Terá de modificar a configuração do dispositivo de modo a que tenha os recursos adequados para satisfazer os requisitos mínimos. Em seguida, pode reiniciar e ligar ao dispositivo. Consulte os requisitos mínimos de configuração no [passo 1: Certifique-se de que o sistema anfitrião cumpre os requisitos de mínima do dispositivo virtual](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Se tiver qualquer outro erro durante a configuração inicial com a IU web local, consulte os fluxos de trabalho seguintes:

* Execute testes de diagnóstico [resolver problemas de configuração de interface do Usuário da web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Gerar o pacote de registo e ver ficheiros de registo](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Passos Seguintes
* [Configurar a sua matriz Virtual StorSimple como um servidor de ficheiros](storsimple-virtual-array-deploy3-fs-setup.md)
* [Configurar a sua matriz Virtual StorSimple como um servidor de iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
