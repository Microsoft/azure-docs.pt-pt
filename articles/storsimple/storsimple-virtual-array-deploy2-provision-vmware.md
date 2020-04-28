---
title: Provision StorSimple Virtual Array em VMware
description: Este segundo tutorial na série de implementação StorSimple Virtual Array envolve o fornecimento de um dispositivo virtual em VMware.
author: alkohli
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5f5ded3faec3a080022eea70de2cca5d27529c4d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76272085"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>Implementar Matriz Virtual StorSimple - Provisão em VMware
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Descrição geral

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Este tutorial descreve como fornecer e ligar a um StorSimple Virtual Array num sistema de anfitriões que executa VMware ESXi 5.0, 5.5, 6.0 ou 6.5. Este artigo aplica-se à implementação de Matrizes Virtuais StorSimple no portal Azure e na Cloud do Governo do Microsoft Azure.

Necessita de privilégios de administrador para aprovisionar e ligar a um dispositivo virtual. O aprovisionamento e a configuração inicial demoram cerca de 10 minutos a concluir.

## <a name="provisioning-prerequisites"></a>Predisposição de pré-requisitos
Os pré-requisitos para fornecer um dispositivo virtual num sistema de anfitriões que executa VMware ESXi 5.0, 5.5, 6.0 ou 6.5, são os seguintes.

### <a name="for-the-storsimple-device-manager-service"></a>Para o serviço Gestor de Dispositivos do StorSimple
Antes de começar, certifique-se de que:

* Completou todos os passos em [Preparar o portal para StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).
* Descarregou a imagem do dispositivo virtual para VMware a partir do portal Azure. Para mais informações, consulte **passo 3: Descarregue a imagem** do dispositivo virtual de Preparar o portal para o guia [StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).

### <a name="for-the-storsimple-virtual-device"></a>Para o dispositivo virtual StorSimple
Antes de implementar um dispositivo virtual, certifique-se de que:

* Tem acesso a um sistema de anfitriões que executa o Hyper-V (2008 R2 ou mais tarde) que pode ser utilizado para fornecer um dispositivo.
* O sistema anfitrião tem capacidade para dedicar os seguintes recursos para aprovisionar o seu dispositivo virtual:

  * Um mínimo de 4 núcleos.
  * Pelos menos 8 GB de RAM. Se planeia configurar a matriz virtual como servidor de ficheiros, 8 GB suporta menos de 2 milhões de ficheiros. Precisa de 16 GB de RAM para suportar 2 a 4 milhões de ficheiros.
  * Uma interface de rede.
  * Um disco virtual de 500 GB para dados do sistema.

### <a name="for-the-network-in-datacenter"></a>Para a rede no datacenter
Antes de começar, certifique-se de que:

* Reviu os requisitos de networking para implementar um dispositivo virtual StorSimple e configurou a rede datacenter de acordo com os requisitos. 

## <a name="step-by-step-provisioning"></a>Provisionamento passo a passo
Para fornecer e ligar a um dispositivo virtual, é necessário efetuar os seguintes passos:

1. Certifique-se de que o sistema de acolhimento dispõe de recursos suficientes para satisfazer os requisitos mínimos do dispositivo virtual.
2. Forneca um dispositivo virtual no seu hipervisor.
3. Inicie o dispositivo virtual e obtenha o endereço IP.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Passo 1: Garantir que o sistema de acolhimento cumpre os requisitos mínimos dos dispositivos virtuais
Para criar um dispositivo virtual, necessitará:

* Acesso a um sistema de anfitriões em execução VMware ESXi Server 5.0, 5.5, 6.0 ou 6.5.
* Ter o cliente VMware vSphere no seu sistema para gerir o anfitrião ESXi.

  * Um mínimo de 4 núcleos.
  * Pelos menos 8 GB de RAM. Se planeia configurar a matriz virtual como servidor de ficheiros, 8 GB suporta menos de 2 milhões de ficheiros. Precisa de 16 GB de RAM para suportar 2 a 4 milhões de ficheiros.
  * Uma interface de rede ligada à rede com capacidade para encaminhar o tráfego para a Internet. A largura de banda mínima da Internet deve ser de 5 Mbps para permitir um ótimo funcionamento do dispositivo.
  * Um disco virtual de 500 GB para dados.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Passo 2: Fornecer um dispositivo virtual em hipervisor
Execute os passos seguintes para aprovisionar um dispositivo virtual no seu hipervisor.

1. Copie a imagem do dispositivo virtual no seu sistema. Descarregou esta imagem virtual através do portal Azure.

   1. Certifique-se de que descarregou o ficheiro de imagem mais recente. Se descarregou a imagem mais cedo, descarregue-a novamente para garantir que tem a imagem mais recente. A imagem mais recente tem dois ficheiros (em vez de um).
   2. Tome nota da localização onde copiou a imagem, uma vez que vai utilizar esta imagem mais à frente no procedimento.

2. Inicie sessão no servidor ESXi com o cliente vSphere. Tem de ter privilégios de administrador para criar uma máquina virtual.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. No cliente vSphere, na secção de inventário no painel esquerdo, selecione o EsXi Server.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Carregue o VMDK para o servidor ESXi. Navegue para o separador **Configuração** no painel direito. Em **hardware,** **selecione Storage**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. No painel da direita, em **Datastores** (Arquivos de dados), selecione o arquivo de dados onde pretende carregar o VMDK. O datastore deve ter espaço livre suficiente para o SISTEMA e os discos de dados.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Clique com o botão direito do rato e selecione **Browse Datastore** (Procurar arquivo de dados).

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. É apresentada a janela **Datastore Browser** (Browser de arquivos de dados).

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Na barra de ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) ferramentas, clique no ícone para criar uma nova pasta. Especifique o nome da pasta e tome nota do mesmo. Vai utilizar este nome de pasta mais tarde quando criar uma máquina virtual (melhor prática recomendada). Clique em **OK**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. A nova pasta aparece no painel esquerdo do **Datastore Browser** (Browser de arquivos de dados).

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Clique no ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png) ícone upload e selecione **Upload File**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Procure e aponte para os ficheiros VMDK que descarregou. Existem dois ficheiros. Selecione um ficheiro para carregar.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Clique em **Open** (Abrir). O carregamento do ficheiro VMDK para o arquivo de dados especificado é iniciado. O carregamento do ficheiro pode demorar alguns minutos.
13. Depois de concluído o carregamento, verá o ficheiro no arquivo de dados na pasta que criou.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Agora, carregue o segundo ficheiro VMDK para o mesmo arquivo de dados.
14. Volte à janela do cliente vSphere. Com o servidor ESXi selecionado, clique à direita e selecione **New Virtual Machine**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. Aparecerá uma janela **Create New Virtual Machine.** Na página **de Configuração,** selecione a opção **Custom.** Clique em **Seguinte**.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. Na página **Nome e Localização,** especifique o nome da sua máquina virtual. Este nome deve coincidir com o nome da pasta (boas práticas recomendadas) especificado anteriormente no Passo 8.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. Na página **de Armazenamento,** selecione uma loja de dados que pretende utilizar para fornecer o seu VM.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. Na página versão da versão virtual da **máquina,** selecione **Versão Virtual Machine: 8**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. Na página do **Sistema Operativo Convidado,** selecione o **Sistema Operativo Convidado** como **Windows**. Para **versão**, a partir da lista de dropdown, selecione **Microsoft Windows Server 2012 (64-bits)**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. Na página **CPUs,** ajuste o **número de tomadas virtuais** e o número de **núcleos por tomada virtual** de modo a que o número total de **núcleos** seja de 4 (ou mais). Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. Na página **Memória,** especifique 8 GB (ou mais) de RAM. Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. Na página **da Rede,** especifique o número das interfaces de rede. O requisito mínimo é uma interface de rede.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Na página do **Controlador SCSI,** aceite o **controlador LSI Logic SAS**predefinido .

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. Na página **Select a Disk,** escolha **Utilizar um disco virtual existente**. Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. Na página **Do Disco Existente,** em seguir no Caminho do **Ficheiro do Disco,** clique em **Navegar**. Isto abre um diálogo **browse Datastores.** Navegue para o local onde fez o upload do VMDK. Agora vê apenas um ficheiro na loja de dados, uma vez que os dois ficheiros que inicialmente carregou foram fundidos. Selecione o ficheiro e clique em **OK**. Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. Na página **Opções Avançadas,** aceite o predefinido e clique **em Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. Na página **Ready to Complete** (Pronto para concluir), reveja todas as definições associadas à nova máquina virtual. Verifique **Editar as definições da máquina virtual antes de terminar**. Clique em **Continue** (Continuar).

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. Na página **Virtual Machines Properties,** no separador **Hardware,** localize o hardware do dispositivo. Selecione **novo disco rígido**. Clique em **Adicionar**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Vê uma janela **de adicionar hardware.** Na página **Tipo de Dispositivo,** em baixo **Escolha o tipo de dispositivo que pretende adicionar,** selecione **Disco Rígido**e clique **em Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. Na página **Select a Disk,** escolha **Criar um novo disco virtual**. Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. Na página **Criar um Disco,** altere o tamanho do **disco** para 500 GB (ou mais). Embora 500 GB seja o requisito mínimo, pode sempre fornecer um disco maior. Note que não pode expandir ou encolher o disco uma vez provisionado. Para obter mais informações sobre o tamanho do disco para fornecer, reveja a secção de dimensionamento no documento de [boas práticas.](storsimple-ova-best-practices.md) No fornecimento **de disco,** selecione **Thin Provision**. Clique em **Seguinte**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. Na página **Opções Avançadas,** aceite o padrão.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. Na página **Ready to Complete,** reveja as opções do disco. Clique em **Concluir**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Volte à página Virtual Machine Properties. Um novo disco rígido é adicionado à sua máquina virtual. Clique em **Concluir**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. Com a sua máquina virtual selecionada no painel certo, navegue para o separador **Resumo.** Reveja as definições para a sua máquina virtual.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

A sua máquina virtual está agora aprovisionada. O passo seguinte é ligar esta máquina e obter o endereço IP.

> [!NOTE]
> Recomendamos que não instale ferramentas VMware na sua matriz virtual (conforme acima previsto). A instalação de ferramentas VMware resultará numa configuração não suportada.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Passo 3: Inicie o dispositivo virtual e obtenha o IP
Execute os passos seguintes para iniciar o dispositivo virtual e ligar ao mesmo.

#### <a name="to-start-the-virtual-device"></a>Para iniciar o dispositivo virtual
1. Inicie o dispositivo virtual. No Gestor de Configuração vSphere, no painel esquerdo, selecione o seu dispositivo e clique à direita para elevar o menu de contexto. Selecione **Power** (Alimentação) e, em seguida, selecione **Power on** (Ligar). Esta ação deve ligar a máquina virtual. Pode ver o estado no painel de **tarefas recentes** do cliente vSphere.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. As tarefas de configuração levarão alguns minutos para serem concluídas. Uma vez que o dispositivo esteja em funcionamento, navegue para o separador **Consola.** Enviar CTRL+Alt+Delete para iniciar sessão no dispositivo. Em alternativa, pode apontar o cursor na janela da consola e premir Ctrl+Alt+Insert. O utilizador predefinido é *o StorSimpleAdmin* e a palavra-passe predefinida é *password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Por motivos de segurança, a palavra-passe de administrador do dispositivo expira após o primeiro início de sessão. É-lhe pedido que altere a palavra-passe.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Introduza uma palavra-passe com, pelo menos, 8 carateres. A palavra-passe deve conter 3 em 4 destes requisitos: maiúsculas, minúsculas, numéricas e caracteres especiais. Reintroduza a palavra-passe para a confirmar. Será notificado de que a palavra-passe foi alterada.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. Depois de a palavra-passe ser alterada com sucesso, o dispositivo virtual pode reiniciar. Aguarde que o reboot termine. A consola Windows PowerShell do dispositivo pode ser exibida juntamente com uma barra de progresso.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. Os passos 6 a 8 aplicam-se apenas se estiver a iniciar num ambiente não DHCP. Se estiver num ambiente DHCP, ignore estes passos e avance para o passo 9. Se tiver iniciado o seu dispositivo em ambiente não DHCP, verá o seguinte ecrã.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Em seguida, configurar a rede.
7. Utilize `Get-HcsIpAddress` o comando para listar as interfaces de rede ativadas no seu dispositivo virtual. Se o seu dispositivo tiver uma única interface de rede ativada, o nome predefinido atribuído a essa interface é `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Utilize o cmdlet `Set-HcsIpAddress` para configurar a rede. Apresentamos um exemplo abaixo:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Depois de a configuração inicial estar concluída e o dispositivo iniciado, verá o texto da faixa do dispositivo. Tome nota do endereço IP e do URL apresentado no texto da faixa para gerir o dispositivo. Utilizará este endereço IP para se ligar à UI web do seu dispositivo virtual e completar a configuração e registo locais.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. (Opcional) Execute este passo apenas se estiver a implementar o seu dispositivo na Nuvem do Governo. Irá agora ativar o modo Padrão de Processamento de Informação (FIPS) dos Estados Unidos no seu dispositivo. O padrão FIPS 140 define algoritmos criptográficos aprovados para uso por sistemas informáticos do governo federal dos EUA para a proteção de dados sensíveis.

    1. Para ativar o modo FIPS, execute o seguinte cmdlet:

        `Enable-HcsFIPSMode`
    2. Reinicie o seu dispositivo depois de ter ativado o modo FIPS para que as validações criptográficas entrem em vigor.

       > [!NOTE]
       > Pode ativar ou desativar o modo FIPS no seu dispositivo. Não é suportado alternar o dispositivo entre o modo FIPS e o modo não-FIPS.
       >
       >

Se o dispositivo não cumprir os requisitos mínimos de configuração, verá um erro no texto da faixa (apresentado abaixo). Terá de modificar a configuração do dispositivo de modo a que tenha os recursos adequados para satisfazer os requisitos mínimos. Em seguida, pode reiniciar e ligar ao dispositivo. Veja os requisitos mínimos de configuração no [Passo 1: Confirmar que o sistema anfitrião cumpre os requisitos mínimos do dispositivo virtual](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Se enfrentar qualquer outro erro durante a configuração inicial utilizando o UI web local, consulte os seguintes fluxos de trabalho:

* Ecorra em testes de diagnóstico para resolver problemas na [configuração da UI](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors)web .
* [Gere o pacote de registo e ver ficheiros de registo](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Passos seguintes
* [Configurar o seu StorSimple Virtual Array como servidor de ficheiros](storsimple-virtual-array-deploy3-fs-setup.md)
* [Configurar o seu StorSimple Virtual Array como um servidor iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
