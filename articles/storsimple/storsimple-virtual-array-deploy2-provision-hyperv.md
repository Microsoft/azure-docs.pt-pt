---
title: Provisão StorSimple Matriz Virtual em | Hiper-V Microsoft Docs
description: Este segundo tutorial na implementação de Matriz Virtual StorSimple envolve o fornecimento de uma matriz virtual em Hyper-V.
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
ms.openlocfilehash: d6dfc95820e911781ffa88e2207601703f165839
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87070587"
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>Implementar StorSimple Virtual Array - Provisão em Hiper-V
![Diagrama mostrando os passos necessários para implantar uma matriz virtual. A primeira parte do segundo passo é rotulada Provisão em Hiper-V e é destacada.](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Descrição Geral

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Este tutorial descreve como providenciar um StorSimple Virtual Array num sistema de anfitriões que executa o Hyper-V no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2. Este artigo aplica-se à implementação de StorSimple Virtual Arrays no portal Azure e microsoft Azure Government Cloud.

Precisa de privilégios de administrador para provisão e configurar uma matriz virtual. O aprovisionamento e a configuração inicial demoram cerca de 10 minutos a concluir.

## <a name="provisioning-prerequisites"></a>Aprovisionamento de pré-requisitos
Aqui encontrará os pré-requisitos para a disponibilização de um conjunto virtual num sistema de anfitriões que executa o Hyper-V no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2.

### <a name="for-the-storsimple-device-manager-service"></a>Para o serviço Gestor de Dispositivos do StorSimple
Antes de começar, certifique-se de que:

* Completou todos os passos em [Prepare o portal para o StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).
* Descarregou a imagem de matriz virtual para Hyper-V a partir do portal Azure. Para mais informações, consulte **o Passo 3: Descarregue a imagem de matriz virtual** de Preparar o portal para o guia [StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).

  > [!IMPORTANT]
  > O software em execução no StorSimple Virtual Array só pode ser utilizado com o serviço StorSimple Device Manager.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>Para o StorSimple Virtual Array
Antes de implementar uma matriz virtual, certifique-se de que:

* Tem acesso a um sistema de anfitriões que executa o Hyper-V no Windows Server 2008 R2 ou mais tarde que pode ser utilizado para uma provisão de um dispositivo.
* O sistema de acolhimento é capaz de dedicar os seguintes recursos à oferta da sua matriz virtual:

  * Um mínimo de 4 núcleos.
  * Pelos menos 8 GB de RAM. Se planeia configurar o conjunto virtual como servidor de ficheiros, 8 GB suporta menos de 2 milhões de ficheiros. Precisa de 16 GB de RAM para suportar 2 a 4 milhões de ficheiros.
  * Uma interface de rede.
  * Um disco virtual de 500 GB para dados.

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no datacenter
Antes de começar, reveja os requisitos de networking para implementar um StorSimple Virtual Array e configufique adequadamente a rede do datacenter. Para obter mais informações, consulte [os requisitos de rede StorSimple Virtual Array](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Provisão passo a passo
Para providenciar e ligar a uma matriz virtual, é necessário executar os seguintes passos:

1. Certifique-se de que o sistema de acolhimento dispõe de recursos suficientes para satisfazer os requisitos mínimos de matriz virtual.
2. Adiscam uma matriz virtual no seu hipervisor.
3. Inicie a matriz virtual e obtenha o endereço IP.

Cada um destes passos é explicado nas seguintes secções.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>Passo 1: Certifique-se de que o sistema de anfitrião cumpre os requisitos mínimos de matriz virtual
Para criar uma matriz virtual, é necessário:

* A função Hyper-V instalada no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 SP1.
* Do Microsoft Hyper-V Manager, num cliente Microsoft Windows ligado ao anfitrião.

Certifique-se de que o hardware subjacente (sistema de anfitrião) no qual está a criar a matriz virtual é capaz de dedicar os seguintes recursos à sua matriz virtual:

* Um mínimo de 4 núcleos.
* Pelos menos 8 GB de RAM. Se planeia configurar o conjunto virtual como servidor de ficheiros, 8 GB suporta menos de 2 milhões de ficheiros. Precisa de 16 GB de RAM para suportar 2 a 4 milhões de ficheiros.
* Uma interface de rede.
* Um disco virtual de 500 GB para dados do sistema.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>Passo 2: Provisionamento de uma matriz virtual no hipervisor
Execute os passos seguintes para aprovisionar um dispositivo no seu hipervisor.

#### <a name="to-provision-a-virtual-array"></a>Para providenciar uma matriz virtual
1. No seu anfitrião Do Servidor do Windows, copie a imagem de matriz virtual para uma unidade local. Você descarregou esta imagem (VHD ou VHDX) através do portal Azure. Tome nota da localização onde copiou a imagem, uma vez que vai utilizar esta imagem mais à frente no procedimento.
2. Abra o **Gestor de Servidor**. No canto superior direito, clique em **Tools** (Ferramentas) e selecione **Hyper-V Manager**.

   ![Screenshot do Server Manager mostrando um menu de Ferramentas expandido com o item Hyper-V Manager em destaque.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Se estiver a executar o Windows Server 2008 R2, abra o Hyper-V Manager. No Gestor de Servidor, clique em **Funções > Hiper-V > Hyper-V Manager**.
3. No **Hyper-V Manager**, no painel de âmbito, clique com o botão direito do rato no nó do seu sistema para abrir o menu de contexto e clique em **Novo** > **Máquina Virtual**.

   ![Screenshot do Hyper-V Manager mostrando o menu de atalho de um nó de sistema, com os itens de Máquina Nova e Virtual em destaque.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. Na página **Before you begin** (Antes de começar) do New Virtual Machine Wizard (Assistente de Nova Máquina Virtual), clique em **Next** (Seguinte).
5. Na página **de nome e localização do Especifique,** forneça um **Nome** para a sua matriz virtual. Clique em **Seguinte**.

   ![Screenshot do nome e página de localização do Espec que mostra a caixa Nome preenchida e o botão Seguinte realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. Na página **de geração Desepecar,** escolha o tipo de imagem do dispositivo e, em seguida, clique em **Seguinte**. Esta página não aparece se estiver a utilizar o Windows Server 2008 R2.

   * Escolha **a Geração 2** se descarregou uma imagem .vhdx para o Windows Server 2012 ou mais tarde.
   * Escolha **a Geração 1** se descarregou uma imagem .vhd para Windows Server 2008 R2 ou mais tarde.

   ![Screenshot da página de geração Specify mostrando a opção Geração 2 selecionada e o botão Seguinte realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. Na página **Assign memory** (Atribuir memória), especifique **Startup memory** (Memória de arranque) como, pelo menos, **8192 MB**, não ative a memória dinâmica e clique em **Next** (Seguinte).

   ![Screenshot da página de memória 'Atribuir' mostrando um valor de 8192 na caixa de memória Iniciar. O botão Seguinte é realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. Na página **Configure networking** (Configurar rede), especifique o comutador virtual que está ligado à Internet e clique em **Next** (Seguinte).

   ![Screenshot da página de rede Configure mostrando o item do interruptor virtual Hyper V selecionado na caixa De Ligação e o botão Seguinte realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. Na página **de disco rígido virtual Connect,** escolha Utilize um disco rígido virtual **existente,** especifique a localização da imagem da matriz virtual (.vhdx ou .vhd) e, em seguida, clique em **Seguinte**.

   ![Screenshot da página de disco rígido virtual Connect com Utilize um disco rígido virtual existente selecionado, uma localização inserida e o botão Seguinte realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Reveja **Summary** (Resumo) e clique em **Finish** (Concluir) para criar a máquina virtual.

    ![Screenshot da nova página de assistente de máquina virtual mostrando uma descrição de uma máquina virtual. O botão Terminar é realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. Para cumprir os requisitos mínimos, precisa de quatro núcleos. Para adicionar quatro processadores virtuais, selecione o seu sistema anfitrião, na janela **Hyper-V Manager**. No painel do lado direito, na lista **Virtual Machines** (Máquinas Virtuais), localize a máquina virtual que acabou de criar. Selecione e clique com o botão direito do rato no nome da máquina e selecione **Settings** (Definições).

    ![Screenshot da janela do Gestor Hiper-V. Uma máquina é selecionada na lista e o item Definições é realçado no menu de atalho da máquina.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. Na página **Settings** (Definições), no painel do lado esquerdo, clique em **Processor** (Processador). No painel do lado direito, defina **number of virtual processors** (número de processadores virtuais) como quatro (ou mais). Clique em **Aplicar**.

    ![Screenshot da página de definições com o item do processador em destaque, o número de processadores virtuais definido para quatro, e o botão Aplicar realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. Para satisfazer os requisitos mínimos, também precisa adicionar um disco de dados virtual de 500 GB. Na página **Settings** (Definições):

    1. No painel do lado esquerdo, selecione **SCSI Controller** (Controlador SCSI).
    2. No painel do lado direito, selecione **Hard Drive** (Disco Rígido) e clique em **Add** (Adicionar).

    ![Screenshot da página de definições com o item do controlador SCSI selecionado, o item de unidade rígido selecionado e o botão Adicionar realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. Na página **Hard drive** (Disco Rígido), selecione a opção **Virtual hard disk** (Disco rígido virtual) e clique em **New** (Novo). É iniciado o **New Virtual Hard Disk Wizard** (Assistente de Novo Disco Rígido Virtual).

    ![Screenshot da página de definições com o item de disco rígido selecionado, selecionada a opção de disco rígido Virtual e o novo botão realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. Na página **Before you begin** (Antes de começar) do New Virtual Hard Disk Wizard (Assistente de Novo Disco Rígido Virtual), clique em **Next** (Seguinte).
16. Na página **Choose Disk Format** (Escolher Formato do Disco), aceite a opção predefinida, que é o formato **VHDX**. Clique em **Seguinte**. Este ecrã não é apresentado se executar o Windows Server 2008 R2.

    ![Screenshot da página do formato do disco Escolha com a opção VHDX selecionada e o botão Seguinte realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. Na página **Choose Disk Type page** (Escolher Tipo de Disco), defina o tipo do disco rígido virtual como **Dynamically expanding** (Expansão dinâmica), que é a recomendação. **Fixed size disk** (Disco de tamanho fixo) também funcionaria, mas teria de esperar muito tempo. Não recomendamos a utilização da opção **Differencing** (Diferenciação). Clique em **Seguinte**. No Windows Server 2012 R2 e Windows Server 2012, **a expansão dinâmica** é a opção por defeito, enquanto que no Windows Server 2008 R2, o padrão é **tamanho fixo**.

    ![Screenshot da página do tipo disco Escolha com a opção de expansão dinâmica selecionada e o botão Seguinte realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. Na página **Specify Name and Location** (Especificar Nome e Localização), indique **name** (nome) e **location** (localização, para a qual pode navegar) para o disco de dados. Clique em **Seguinte**.

    ![Screenshot da página de identificação e localização do Especifique os valores nas caixas de Nome e Localização. O botão Seguinte é realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. Na página **Configure Disk,** selecione a opção **Criar um novo disco rígido virtual em branco** e especificar o tamanho como **500 GB** (ou mais). Enquanto 500 GB é o requisito mínimo, pode sempre providenciar um disco maior. Note que não pode expandir ou encolher o disco uma vez previsto. Para obter mais informações sobre o tamanho do disco a provisão, reveja a secção de dimensionamento no documento de [boas práticas.](storsimple-ova-best-practices.md) Clique em **Seguinte**.

    ![Screenshot da página de disco configurar com Criar um novo disco rígido virtual em branco selecionado, o tamanho definido para 500, e o botão Seguinte realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. Na página **Summary** (Resumo), reveja os detalhes do disco de dados virtual e, se estiver satisfeito, clique em **Finish** (Concluir) para criar o disco. O assistente fecha-se e é adicionado um disco rígido virtual à sua máquina.

    ![Screenshot do Completar a nova página virtual de assistente de disco rígido mostrando uma descrição de um disco rígido virtual. O botão Terminar é realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Regresse à página **Settings** (Definições). Clique em **OK** para fechar a página **Settings** (Definições) e regressar à janela do Hyper-V Manager.

    ![Screenshot da página de definições. O botão OK é realçado.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>Passo 3: Inicie a matriz virtual e obtenha o IP
Execute os seguintes passos para iniciar a sua matriz virtual e conectá-la.

#### <a name="to-start-the-virtual-array"></a>Para iniciar a matriz virtual
1. Inicie a matriz virtual.

   ![Screenshot da janela do Gestor Hiper-V. A nova matriz é selecionada na lista, e o item Iniciar é destacado no menu de atalho dessa matriz.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. Quando o dispositivo estiver em execução, selecione-o, clique com o botão direito do rato e selecione **Connect** (Ligar).

   ![Screenshot da janela do Gestor Hiper-V. A nova matriz é selecionada, o seu estado está em execução, e o item Connect é destacado no seu menu de atalho.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Pode ter de esperar 5 a 10 minutos para que o dispositivo esteja pronto. É apresentada uma mensagem de estado na consola, para indicar o progresso. Quando o dispositivo estiver pronto, aceda a **Action** (Ação). Pressione `Ctrl + Alt + Delete` para iniciar sessão na matriz virtual. O utilizador predefinido é *StorSimpleAdmin* e a palavra-passe padrão é *Password1*.

   ![Screenshot do separador consola cliente vSphere mostrando caracteres indeterminados na caixa de entrada.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Por motivos de segurança, a palavra-passe de administrador do dispositivo expira após o primeiro início de sessão. É-lhe pedido que altere a palavra-passe.

   ![Screenshot do separador consola do cliente vSphere. O texto na página indica que a palavra-passe deve ser alterada.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Introduza uma palavra-passe com, pelo menos, 8 carateres. A palavra-passe tem de satisfazer, pelo menos, três dos quatro requisitos seguintes: letras maiúsculas, letras minúsculas, números e carateres especiais. Reintroduza a palavra-passe para a confirmar. É notificado de que a palavra-passe foi alterada.

   ![Screenshot do separador consola do cliente vSphere. O texto na página indica que a palavra-passe foi alterada.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. Depois de a palavra-passe ser alterada com sucesso, a matriz virtual pode reiniciar. Aguarde pelo início do dispositivo.

   ![Screenshot da página principal do StorSimpleAdmin. O texto na página pede ao utilizador que aguarde o serviço de notificação de eventos do sistema.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    É apresentada a consola Windows PowerShell do dispositivo, juntamente com uma barra de progresso.

   ![Screenshot mostrando uma janela da consola com uma barra de progresso. O texto na janela indica que a configuração inicial está em curso e pede ao utilizador que aguarde.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. Os passos 6 a 8 aplicam-se apenas se estiver a iniciar num ambiente não DHCP. Se estiver num ambiente DHCP, ignore estes passos e avance para o passo 9. Se iniciar o seu dispositivo em ambiente não DHCP, verá o seguinte ecrã.

   ![Screenshot mostrando uma janela da consola com texto descrevendo o dispositivo. O pedido de comando diz "Controlador" e aparece pronto para a entrada.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    Em seguida, configurar a rede.
7. Utilize o `Get-HcsIpAddress` comando para listar as interfaces de rede ativadas na sua matriz virtual. Se o seu dispositivo tiver uma única interface de rede ativada, o nome predefinido atribuído a essa interface é `Ethernet`.

   ![Screenshot mostrando uma janela da consola com a saída do comando Get-HcsIpAddress. "Ethernet" está listado como o nome do dispositivo.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. Utilize o cmdlet `Set-HcsIpAddress` para configurar a rede. Veja o seguinte exemplo:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![Screenshot mostrando uma janela da consola com a saída do comando Get-Help Set-HcsIpAddress e a utilização correta do comando Set-HcsIpAddress.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. Depois de a configuração inicial estar concluída e o dispositivo iniciado, verá o texto da faixa do dispositivo. Tome nota do endereço IP e do URL apresentado no texto da faixa para gerir o dispositivo. Utilize este endereço IP para ligar à UI web da sua matriz virtual e completar a configuração e registo local.

   ![Screenshot mostrando uma janela da consola com texto de banner do dispositivo. Este texto inclui o endereço IP do dispositivo e URL.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. (Opcional) Execute este passo apenas se estiver a implementar o seu dispositivo na Cloud do Governo. Irá agora ativar o modo Padrão Federal de Processamento de Informação (FIPS) dos Estados Unidos no seu dispositivo. A norma FIPS 140 define algoritmos criptográficos aprovados para uso pelos sistemas informáticos do governo federal dos EUA para a proteção de dados sensíveis.

    1. Para ativar o modo FIPS, executar o seguinte cmdlet:

        `Enable-HcsFIPSMode`
    2. Reinicie o seu dispositivo depois de ter ativado o modo FIPS para que as validações criptográficas produzam efeitos.

       > [!NOTE]
       > Pode ativar ou desativar o modo FIPS no seu dispositivo. Alternar o dispositivo entre o modo FIPS e o modo não-FIPS não é suportado.
       >
       >

Se o seu dispositivo não cumprir os requisitos mínimos de configuração, vê o seguinte erro no texto do banner (mostrado abaixo). Modifique a configuração do dispositivo de modo a que a máquina tenha os recursos adequados para satisfazer os requisitos mínimos. Em seguida, pode reiniciar e ligar ao dispositivo. Consulte os requisitos mínimos de configuração no Passo 1: Certifique-se de que o sistema de hospedeiro satisfaz os requisitos mínimos de matriz virtual.

![Screenshot mostrando uma janela da consola com texto de banner do dispositivo. Esse texto inclui uma mensagem de erro que fornece um URL para resolver problemas.](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Se enfrentar qualquer outro erro durante a configuração inicial utilizando a UI web local, consulte os seguintes fluxos de trabalho:

* Executar testes de diagnóstico para [resolver problemas na configuração da UI web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Gerei o pacote de registo e visualiza os ficheiros de registo](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Passos seguintes
* [Configurar o seu StorSimple Virtual Array como um servidor de ficheiros](storsimple-virtual-array-deploy3-fs-setup.md)
* [Configurar o seu StorSimple Virtual Array como um servidor iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
