---
title: Provisão StorSimple Matriz Virtual em VMware
description: Este segundo tutorial na série de implementação StorSimple Virtual Array envolve o fornecimento de um dispositivo virtual em VMware.
author: alkohli
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9810a34021aa039354aad24f84aff373229c0190
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87021482"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>Implementar StorSimple Virtual Array - Provision in VMware
![Diagrama mostrando os passos necessários para implantar uma matriz virtual. A segunda parte do segundo escalão é rotulada Provisão em VMware e é destacada.](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Descrição Geral

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Este tutorial descreve como providenciar e ligar a um StorSimple Virtual Array num sistema de anfitrião que executa VMware ESXi 5.0, 5.5, 6.0 ou 6.5. Este artigo aplica-se à implementação de Arrays Virtuais StorSimple no portal Azure e na Cloud do Governo microsoft Azure.

Necessita de privilégios de administrador para aprovisionar e ligar a um dispositivo virtual. O aprovisionamento e a configuração inicial demoram cerca de 10 minutos a concluir.

## <a name="provisioning-prerequisites"></a>Aprovisionamento de pré-requisitos
Os pré-requisitos para o fornecimento de um dispositivo virtual num sistema de anfitriões que executa vMware ESXi 5.0, 5.5, 6.0 ou 6.5, são os seguintes.

### <a name="for-the-storsimple-device-manager-service"></a>Para o serviço Gestor de Dispositivos do StorSimple
Antes de começar, certifique-se de que:

* Completou todos os passos em [Prepare o portal para o StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).
* Descarregou a imagem do dispositivo virtual para VMware a partir do portal Azure. Para mais informações, consulte **o Passo 3: Descarregue a imagem do dispositivo virtual** de Preparar o portal para o guia [StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).

### <a name="for-the-storsimple-virtual-device"></a>Para o dispositivo virtual StorSimple
Antes de implementar um dispositivo virtual, certifique-se de que:

* Tem acesso a um sistema de anfitriões em funcionamento de Hyper-V (2008 R2 ou posterior) que pode ser utilizado para uma provisão de um dispositivo.
* O sistema anfitrião tem capacidade para dedicar os seguintes recursos para aprovisionar o seu dispositivo virtual:

  * Um mínimo de 4 núcleos.
  * Pelos menos 8 GB de RAM. Se planeia configurar o conjunto virtual como servidor de ficheiros, 8 GB suporta menos de 2 milhões de ficheiros. Precisa de 16 GB de RAM para suportar 2 a 4 milhões de ficheiros.
  * Uma interface de rede.
  * Um disco virtual de 500 GB para dados do sistema.

### <a name="for-the-network-in-datacenter"></a>Para a rede no datacenter
Antes de começar, certifique-se de que:

* Reviu os requisitos de networking para implementar um dispositivo virtual StorSimple e configurar a rede do datacenter de acordo com os requisitos. 

## <a name="step-by-step-provisioning"></a>Provisão passo a passo
Para providenciar e ligar a um dispositivo virtual, é necessário executar os seguintes passos:

1. Certifique-se de que o sistema de acolhimento dispõe de recursos suficientes para satisfazer os requisitos mínimos do dispositivo virtual.
2. A provisiona um dispositivo virtual no seu hipervisor.
3. Inicie o dispositivo virtual e obtenha o endereço IP.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Passo 1: Garantir que o sistema de anfitriões cumpre os requisitos mínimos do dispositivo virtual
Para criar um dispositivo virtual, necessitará:

* Acesso a um sistema de anfitriões que executa vMware ESXi Server 5.0, 5.5, 6.0 ou 6.5.
* Ter o cliente VMware vSphere no seu sistema para gerir o anfitrião ESXi.

  * Um mínimo de 4 núcleos.
  * Pelos menos 8 GB de RAM. Se planeia configurar o conjunto virtual como servidor de ficheiros, 8 GB suporta menos de 2 milhões de ficheiros. Precisa de 16 GB de RAM para suportar 2 a 4 milhões de ficheiros.
  * Uma interface de rede ligada à rede com capacidade para encaminhar o tráfego para a Internet. A largura de banda mínima da Internet deve ser de 5 Mbps para permitir um funcionamento ótimo do dispositivo.
  * Um disco virtual de 500 GB para dados.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Passo 2: Provisionamento de um dispositivo virtual no hipervisor
Execute os passos seguintes para aprovisionar um dispositivo virtual no seu hipervisor.

1. Copie a imagem do dispositivo virtual no seu sistema. Descarregou esta imagem virtual através do portal Azure.

   1. Certifique-se de que descarregou o último ficheiro de imagem. Se descarregou a imagem mais cedo, descarregue-a novamente para garantir que tem a imagem mais recente. A imagem mais recente tem dois ficheiros (em vez de um).
   2. Tome nota da localização onde copiou a imagem, uma vez que vai utilizar esta imagem mais à frente no procedimento.

2. Inicie sessão no servidor ESXi com o cliente vSphere. Tem de ter privilégios de administrador para criar uma máquina virtual.

   ![Screenshot da página de login do cliente vSphere. O endereço IP, o nome de utilizador e as caixas de palavra-passe contêm valores, e o botão de Início de Sessão é realçado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. No cliente vSphere, na secção de inventário no painel esquerdo, selecione o Servidor ESXi.

   ![Screenshot da página principal do cliente vSphere. Na secção de inventário, o servidor ESXi é destacado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Carregue o VMDK para o servidor ESXi. Navegue para o **separador Configuração** no painel direito. Em **Hardware,** selecione **Storage**.

   ![Screenshot mostrando o separador configuração do cliente vSphere. Na secção de Hardware, destaca-se o armazenamento.](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. No painel da direita, em **Datastores** (Arquivos de dados), selecione o arquivo de dados onde pretende carregar o VMDK. A datastore deve ter espaço livre suficiente para o SO e discos de dados.

   ![Screenshot mostrando a página de armazenamento do cliente vSphere. O separador Datastores está aberto e contém uma lista de datastores. Uma datastore é selecionada.](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Clique com o botão direito do rato e selecione **Browse Datastore** (Procurar arquivo de dados).

   ![Screenshot mostrando o menu de atalho selecionado da datastore. O item da loja de dados Browse está selecionado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. É apresentada a janela **Datastore Browser** (Browser de arquivos de dados).

   ![Screenshot de um navegador de datastore. As pastas na loja de dados são visíveis.](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Na barra de ferramentas, clique no :::image type="icon" source="./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png"::: ícone para criar uma nova pasta. Especifique o nome da pasta e tome nota do mesmo. Vai utilizar este nome de pasta mais tarde quando criar uma máquina virtual (melhor prática recomendada). Clique em **OK**.

   ![Screenshot de um navegador de loja de dados com o novo ícone de pasta em destaque. Uma caixa de diálogo tem um nome de pasta preenchido e o botão OK realçado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. A nova pasta aparece no painel esquerdo do **Datastore Browser** (Browser de arquivos de dados).

   ![Screenshot de um navegador de datastore com a nova pasta visível na hierarquia da pasta.](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Clique no ícone upload :::image type="icon" source="./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png"::: e selecione **Upload File**.

    ![Screenshot mostrando o menu de atalho do ícone Upload. O item 'Ficheiro upload' está selecionado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Procure e aponte para os ficheiros VMDK que descarregou. Existem dois ficheiros. Selecione um ficheiro para carregar.

    ![Screenshot de uma caixa de diálogo mostrando pastas e dois ficheiros V M D K. Um dos ficheiros está em destaque.](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Clique em **Abrir**. O carregamento do ficheiro VMDK para o arquivo de dados especificado é iniciado. O carregamento do ficheiro pode demorar alguns minutos.
13. Depois de concluído o carregamento, verá o ficheiro no arquivo de dados na pasta que criou.

    ![Screenshot de um navegador de datastore. A nova pasta é realçada na hierarquia da pasta e o ficheiro carregado é visível nessa pasta.](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Agora, carregue o segundo ficheiro VMDK para o mesmo arquivo de dados.
14. Volte à janela do cliente vSphere. Com o servidor ESXi selecionado, clique à direita e selecione **Nova Máquina Virtual**.

    ![Screenshot do menu de atalho de um servidor ESXi. O novo item da máquina virtual está selecionado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. Aparecerá uma janela **da Máquina Virtual Create.** Na página **Configuração,** selecione a opção **'Personalizar'.** Clique em **Seguinte**.
    ![Screenshot da página de Configuração da nova janela da máquina virtual. A opção Custom é selecionada e o botão Seguinte é realçado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. Na página **Nome e Localização,** especifique o nome da sua máquina virtual. Este nome deve corresponder ao nome da pasta (boas práticas recomendadas) especificado anteriormente no passo 8.

    ![Screenshot do Nome e página de localização da nova janela da máquina virtual. A caixa Nome está preenchida e o botão Seguinte é realçado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. Na página **'Armazenamento',** selecione uma loja de dados que pretende utilizar para forre o seu VM.

    ![Screenshot da página de armazenamento da nova janela da máquina virtual Create. É selecionada uma datastore e o botão Seguinte é realçado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. Na página **versão virtual da máquina,** selecione **Virtual Machine Version: 8**.

    ![Screenshot da página versão virtual da máquina. A opção da versão 8 da máquina Virtual é selecionada e o botão Seguinte é realçado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. Na página **do Sistema Operativo convidado,** selecione o **Sistema Operativo convidado** como **Windows**. Para **a versão**, a partir da lista de dropdown, selecione Microsoft Windows Server **2012 (64-bit)**.

    ![Screenshot da página do Sistema Operativo de Hóspedes com o Windows selecionado, a versão definida para o Microsoft Windows Server 2012 (64-bit) e Next em destaque.](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. Na página do **CPUs,** ajuste o **número de tomadas virtuais** e o número de **núcleos por tomada virtual** de modo a que o número total de **núcleos** seja de 4 (ou mais). Clique em **Seguinte**.

    ![Screenshot da página do CPUs mostrando uma tomada virtual, quatro núcleos por tomada virtual, e quatro núcleos totais. O botão Seguinte é realçado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. Na página **Memória,** especifique 8 GB (ou mais) de RAM. Clique em **Seguinte**.

    ![Screenshot da página Memória. Um valor de 8 GB é preenchido para o tamanho da memória.](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. Na página **'Rede',** especifique o número das interfaces de rede. O requisito mínimo é uma interface de rede.

    ![Screenshot da página da Rede. O número de interfaces de rede está definido para um e o botão Seguinte é realçado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Na página **do Controlador SCSI,** aceite o **controlador LSI Logic SAS** padrão.

    ![Screenshot da página do Controlador SCSI. A opção L S I logic S A S é selecionada e o botão Seguinte é realçado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. Na página **Select a Disk,** escolha **Utilize um disco virtual existente.** Clique em **Seguinte**.

    ![Screenshot da página de disco Select, com a utilização de uma opção de disco virtual existente selecionada e o botão Seguinte realçado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. Na página **Select Existente do Disco,** no **Caminho do Ficheiro do Disco,** clique **em procurar**. Isto abre um diálogo **Browse Datastores.** Navegue até ao local onde carregou o VMDK. Agora vê apenas um ficheiro na loja de dados, uma vez que os dois ficheiros que inicialmente carregou foram fundidos. Selecione o ficheiro e clique **em OK**. Clique em **Seguinte**.

    ![Screenshot da página de disco existente Select. O botão Browse é realçado e uma caixa de diálogo contém um ficheiro e um botão OK realçado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. Na página **Opções Avançadas,** aceite o padrão e clique em **Seguinte**.

    ![Screenshot da página Opções Avançadas. O botão Seguinte é realçado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. Na página **Ready to Complete** (Pronto para concluir), reveja todas as definições associadas à nova máquina virtual. Verifique **editar as definições da máquina virtual antes de terminar**. Clique em **Continue** (Continuar).

    ![Screenshot da página Ready to Complete com um botão Continue realçado. Editar as definições da máquina virtual antes de ser verificada a opção de conclusão.](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. Na página **Virtual Machines Properties,** no separador **Hardware,** localize o hardware do dispositivo. Selecione **novo disco rígido**. Clique em **Adicionar**.

    ![Screenshot do separador hardware da página Virtual Machines Properties. O novo Disco Rígido está selecionado na lista de hardware. O botão Adicionar é realçado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Vê uma janela **de Hardware Add.** Na página **'Tipo dispositivo',** em **Escolha o tipo de dispositivo que pretende adicionar**, selecione Hard **Disk**, e clique em **Seguinte**.

    ![Screenshot da página do tipo dispositivo da janela de hardware Adicionar. O dispositivo de disco rígido é selecionado e o botão Seguinte é realçado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. Na página **Select a Disk,** escolha **Criar um novo disco virtual.** Clique em **Seguinte**.

    ![Screenshot da página Select a Disk. A nova opção de disco virtual é selecionada e o botão Seguinte é realçado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. Na página **Criar um Disco,** altere o Tamanho do **Disco** para 500 GB (ou mais). Enquanto 500 GB é o requisito mínimo, pode sempre providenciar um disco maior. Note que não pode expandir ou encolher o disco uma vez previsto. Para obter mais informações sobre o tamanho do disco a provisão, reveja a secção de dimensionamento no documento de [boas práticas.](storsimple-ova-best-practices.md) Em **termos de Provisão de Discos**, selecione Thin **Provision**. Clique em **Seguinte**.

    ![Screenshot da página Criar um disco. O tamanho do disco está definido para 500 GB, a opção de provisão Thin é selecionada e o botão Seguinte é realçado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. Na página **Opções Avançadas,** aceite o padrão.

    ![Screenshot da página Opções Avançadas. O nó do dispositivo virtual está definido para SCSI (0:0), e o botão Seguinte é realçado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. Na página **Pronto para Concluir,** reveja as opções do disco. Clique em **Concluir**.

    ![Screenshot da página Pronto para Completar. Um resumo das opções do disco é visível e o botão Terminar é realçado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Volte à página Virtual Machine Properties. Um novo disco rígido é adicionado à sua máquina virtual. Clique em **Concluir**.

    ![Screenshot da página Virtual Machine Properties. A lista de hardware contém o novo disco rígido e o botão Terminar é realçado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. Com a sua máquina virtual selecionada no painel direito, navegue para o **separador Resumo.** Reveja as definições da sua máquina virtual.

    ![Screenshot do separador resumo do cliente vSphere. A nova máquina virtual está em destaque, e os seus recursos e propriedades gerais são visíveis.](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

A sua máquina virtual está agora aprovisionada. O passo seguinte é ligar esta máquina e obter o endereço IP.

> [!NOTE]
> Recomendamos que não instale ferramentas VMware na sua matriz virtual (conforme acima referido). A instalação de ferramentas VMware resultará numa configuração não suportada.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Passo 3: Inicie o dispositivo virtual e obtenha o IP
Execute os passos seguintes para iniciar o dispositivo virtual e ligar ao mesmo.

#### <a name="to-start-the-virtual-device"></a>Para iniciar o dispositivo virtual
1. Inicie o dispositivo virtual. No vSphere Configuration Manager, no painel esquerdo, selecione o seu dispositivo e clique à direita para trazer o menu de contexto. Selecione **Power** (Alimentação) e, em seguida, selecione **Power on** (Ligar). Esta ação deve ligar a máquina virtual. Pode ver o estado no painel de **tarefas recentes** do cliente vSphere.

   ![Screenshot do menu de atalho do dispositivo. O item Power é selecionado. Um menu adjacente é visível, com o artigo Power On selecionado.](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. As tarefas de configuração levarão alguns minutos a ser concluídas. Uma vez que o dispositivo esteja em funcionamento, navegue para o separador **Consola.** Enviar Ctrl+Alt+Delete para iniciar sessão no dispositivo. Em alternativa, pode apontar o cursor para a janela da consola e premir Ctrl+Alt+Insert. O utilizador predefinido é *StorSimpleAdmin* e a palavra-passe padrão é *Password1*.

   ![Screenshot do separador consola do cliente vSphere. A caixa de senha está vazia.](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Por motivos de segurança, a palavra-passe de administrador do dispositivo expira após o primeiro início de sessão. É-lhe pedido que altere a palavra-passe.

   ![Screenshot do separador consola do cliente vSphere. O texto na página indica que a palavra-passe deve ser alterada.](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Introduza uma palavra-passe com, pelo menos, 8 carateres. A palavra-passe deve conter 3 de 4 destes requisitos: maiúsculas, minúsculas, numéricas e caracteres especiais. Reintroduza a palavra-passe para a confirmar. Será notificado de que a palavra-passe mudou.

   ![Screenshot do separador consola do cliente vSphere. O texto na página indica que a palavra-passe foi alterada.](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. Depois de a palavra-passe ser alterada com sucesso, o dispositivo virtual pode reiniciar. Aguarde que o reboot esteja completo. A consola Windows PowerShell do dispositivo pode ser exibida juntamente com uma barra de progresso.

   ![Screenshot mostrando uma janela da consola com uma barra de progresso. O texto na janela indica que a configuração inicial está em curso e pede ao utilizador que aguarde.](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. Os passos 6 a 8 aplicam-se apenas se estiver a iniciar num ambiente não DHCP. Se estiver num ambiente DHCP, ignore estes passos e avance para o passo 9. Se iniciar o seu dispositivo em ambiente não DHCP, verá o seguinte ecrã.

   ![Screenshot mostrando uma janela da consola com texto descrevendo o dispositivo. O pedido de comando diz "Controlador" e aparece pronto para a entrada.](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Em seguida, configurar a rede.
7. Utilize o `Get-HcsIpAddress` comando para listar as interfaces de rede ativadas no seu dispositivo virtual. Se o seu dispositivo tiver uma única interface de rede ativada, o nome predefinido atribuído a essa interface é `Ethernet`.

   ![Screenshot mostrando uma janela da consola com a saída do comando Get-HcsIpAddress. "Ethernet" está listado como o nome do dispositivo.](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Utilize o cmdlet `Set-HcsIpAddress` para configurar a rede. Apresentamos um exemplo abaixo:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![Screenshot mostrando uma janela da consola com a saída do comando Get-Help Set-HcsIpAddress e a utilização correta do comando Set-HcsIpAddress.](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Depois de a configuração inicial estar concluída e o dispositivo iniciado, verá o texto da faixa do dispositivo. Tome nota do endereço IP e do URL apresentado no texto da faixa para gerir o dispositivo. Utilizará este endereço IP para ligar à UI web do seu dispositivo virtual e completar a configuração e registo locais.

   ![Screenshot mostrando uma janela da consola com texto de banner do dispositivo. Este texto inclui o endereço IP do dispositivo e URL.](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. (Opcional) Execute este passo apenas se estiver a implementar o seu dispositivo na Cloud do Governo. Irá agora ativar o modo Padrão Federal de Processamento de Informação (FIPS) dos Estados Unidos no seu dispositivo. A norma FIPS 140 define algoritmos criptográficos aprovados para uso pelos sistemas informáticos do governo federal dos EUA para a proteção de dados sensíveis.

    1. Para ativar o modo FIPS, executar o seguinte cmdlet:

        `Enable-HcsFIPSMode`
    2. Reinicie o seu dispositivo depois de ter ativado o modo FIPS para que as validações criptográficas produzam efeitos.

       > [!NOTE]
       > Pode ativar ou desativar o modo FIPS no seu dispositivo. Alternar o dispositivo entre o modo FIPS e o modo não-FIPS não é suportado.
       >
       >

Se o dispositivo não cumprir os requisitos mínimos de configuração, verá um erro no texto da faixa (apresentado abaixo). Terá de modificar a configuração do dispositivo de modo a que tenha os recursos adequados para satisfazer os requisitos mínimos. Em seguida, pode reiniciar e ligar ao dispositivo. Veja os requisitos mínimos de configuração no [Passo 1: Confirmar que o sistema anfitrião cumpre os requisitos mínimos do dispositivo virtual](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![Screenshot mostrando uma janela da consola com texto de banner do dispositivo. Esse texto inclui uma mensagem de erro que fornece um URL para resolver problemas.](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Se enfrentar qualquer outro erro durante a configuração inicial utilizando a UI web local, consulte os seguintes fluxos de trabalho:

* Executar testes de diagnóstico para [resolver problemas na configuração da UI web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Gerei o pacote de registo e visualiza os ficheiros de registo](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Passos seguintes
* [Configurar o seu StorSimple Virtual Array como um servidor de ficheiros](storsimple-virtual-array-deploy3-fs-setup.md)
* [Configurar o seu StorSimple Virtual Array como um servidor iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
