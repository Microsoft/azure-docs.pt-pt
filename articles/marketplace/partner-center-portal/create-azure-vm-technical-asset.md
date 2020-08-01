---
title: Crie os seus ativos técnicos da Azure Virtual Machine
description: Saiba como criar e configurar ativos técnicos para uma oferta de máquina virtual (VM) para o Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/13/2020
ms.openlocfilehash: afc012329d0d9e337dfca93a88615ba7c28f1754
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460376"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>Crie os seus ativos técnicos da Azure Virtual Machine

Este artigo descreve como criar e configurar ativos técnicos para uma oferta de máquina virtual (VM) para o Azure Marketplace. Um VM contém dois componentes: o disco rígido virtual do sistema operativo (VHD) e os discos de dados associados opcionais VHDs:

* **Sistema operativo VHD** – Contém o sistema operativo e a solução que se implanta com a sua oferta. O processo de preparação do VHD difere consoante se trate de um VM baseado em Linux, baseado no Windows ou personalizado.
* **Discos de dados VHDs** - Armazenamento dedicado e persistente para um VM. Não utilize o VHD do sistema operativo (por exemplo, o C: unidade) para armazenar informações persistentes.

Uma imagem VM contém um disco do sistema operativo e até 16 discos de dados. Utilize um VHD por disco de dados, mesmo que o disco esteja em branco.

> [!NOTE]
> Independentemente do sistema operativo que utilize, adicione apenas o número mínimo de discos de dados necessários pela solução. Os clientes não podem remover discos que fazem parte de uma imagem no momento da implementação, mas podem sempre adicionar discos durante ou após a implementação.

> [!IMPORTANT]
> Cada Imagem VM num plano deve ter o mesmo número de discos de dados.

## <a name="fundamental-technical-knowledge"></a>Conhecimento técnico fundamental

Projetar, construir e testar estes ativos leva tempo e requer conhecimento técnico tanto da plataforma Azure como das tecnologias usadas para construir a oferta. Além do seu domínio de solução, a sua equipa de engenharia deve ter conhecimento das seguintes tecnologias da Microsoft:

* Compreensão básica dos [Serviços Azure](https://azure.microsoft.com/services/)
* Como [projetar e arquiteto aplicações Azure](https://azure.microsoft.com/solutions/architecture/)
* Conhecimentos de trabalho de [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) e [Azure Networking](https://azure.microsoft.com/services/?filter=networking)
* Conhecimento de trabalho do Gestor de [Recursos Azure](https://azure.microsoft.com/features/resource-manager/)
* Conhecimento de Trabalho da [JSON](https://www.json.org/)

## <a name="suggested-tools--optional"></a>Ferramentas sugeridas – opcional

Considere usar um dos seguintes ambientes de script para ajudar a gerir VMs e VHDs:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
* [CLI do Azure](https://code.visualstudio.com/)

Além disso, considere adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento:

* [Explorador do Storage do Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Visual Studio Code](https://code.visualstudio.com/)
  * Extensão: [Ferramentas do gestor de recursos Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * Extensão: [Embelezamento](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Reveja as ferramentas disponíveis na página [Azure Developer Tools](https://azure.microsoft.com/product-categories/developer-tools/) e, se estiver a utilizar o Visual Studio, o [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-a-vm-image-using-an-approved-base"></a>Criar uma imagem VM usando uma base aprovada

> [!NOTE]
> Para criar os seus ativos técnicos de máquina virtual utilizando uma imagem que construiu nas suas próprias instalações, vá para [criar um VM utilizando a sua própria imagem](#create-a-vm-using-your-own-image).

Esta secção descreve vários aspetos da utilização de uma base aprovada, como a utilização do Protocolo de Ambiente de Trabalho Remoto (PDR), a seleção de um tamanho para o VM, a instalação das atualizações mais recentes do Windows e a generalização da imagem VHD.

As seguintes secções focam-se principalmente nos VHDs baseados em janelas. Para obter mais informações sobre a criação de VHDs baseados em Linux, consulte [Linux sobre as distribuições endossadas pela Azure.](../../virtual-machines/linux/endorsed-distros.md)

> [!WARNING]
> Siga as orientações neste tópico para utilizar o Azure para criar um VM que contenha um sistema operativo pré-configurado e endossado. Se isto não for compatível com a sua solução, é possível criar e configurar um VM no local utilizando um sistema operativo aprovado. Em seguida, pode configurar e prepará-lo para o upload, conforme descrito no [Prep a Windows VHD ou VHDX para fazer o upload para Azure](../../virtual-machines/windows/prepare-for-upload-vhd-image.md).

### <a name="select-an-approved-base"></a>Selecione uma base aprovada

Selecione o sistema de funcionamento do Windows ou o Linux como base.

#### <a name="windows"></a>Windows

O sistema operativo VHD para a sua imagem VM baseada no Windows deve basear-se numa imagem base aprovada pelo Azure que contenha servidor do Windows ou SQL Server. Para começar, crie um VM a partir de uma das seguintes imagens do portal Azure:

* Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://www.microsoft.com/cloud-platform/windows-server-pricing), [2012 Datacenter](https://www.microsoft.com/cloud-platform/windows-server-pricing), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [Windows 10 IoT Enterprise](/windows/iot-core/windows-iot-enterprise)
* [SQL Server 2014](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md) (Empresa, Standard, Web)
* [SQL Server 2012 SP2](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md) (Enterprise, Standard, Web)

> [!NOTE]
> Se estiver a utilizar o atual portal Azure ou Azure PowerShell, então as imagens do Windows Server publicadas a 8 de setembro de 2014 e posteriormente são aprovadas.

#### <a name="linux"></a>Linux

A Azure oferece uma gama de distribuições linux aprovadas. Para obter uma lista atual, consulte [o Linux sobre as distribuições endossadas pela Azure](../../virtual-machines/linux/endorsed-distros.md).

### <a name="create-vm-in-the-azure-portal"></a>Criar VM no portal Azure

Siga estes passos para criar a imagem VM base no [portal Azure:](https://ms.portal.azure.com/)

1. Faça o sômis no [portal Azure](https://ms.portal.azure.com/) com a conta Microsoft associada à subscrição Azure que pretende utilizar para publicar a sua oferta VM.
2. Crie um novo grupo de recursos e forneça o **nome do grupo de recursos,** **subscrição**e **localização do grupo de recursos.** Para mais detalhes, consulte [Gerir os recursos.](../../azure-resource-manager/resource-group-portal.md)
3. Selecione **máquinas virtuais** à esquerda para exibir a página de detalhes das máquinas virtuais.
4. **Selecione + Adicione** para abrir a experiência da máquina **virtual.**
5. Selecione a imagem a partir da lista de drop-down ou clique em **navegar em todas as imagens públicas e privadas** para pesquisar ou navegar em todas as imagens de máquinas virtuais disponíveis.
6. Selecione o tamanho do VM para implementar utilizando as seguintes recomendações:
    * Se planeia desenvolver o VHD no local, o tamanho não importa. Considere usar um dos VMs menores.
    * Se planeia desenvolver a imagem em Azure, considere utilizar um dos tamanhos VM recomendados para a imagem selecionada.

7. Na secção **Discos,** expanda a secção **Advanced** e desaver a opção **Utilização de discos geridos** para **Nº**.
8. Forneça os outros detalhes necessários para criar o VM.
9. Selecione **Review + crie** para rever as suas escolhas. Quando vir a mensagem **de validação passada,** selecione **Criar**.

O Azure começa a aturar a máquina virtual especificada. Pode acompanhar o seu progresso selecionando o separador **Máquinas Virtuais** à esquerda. Depois de criado, o estado mudará para **Running**.

Se tiver dificuldades em criar o seu novo VHD baseado em Azure, consulte [questões comuns durante a criação de VHD (FAQs)](common-issues-during-vhd-creation.md).

### <a name="connect-to-your-azure-vm"></a>Ligue-se ao seu Azure VM

Esta secção explica como ligar e assinar no VM que criou no Azure. Depois de ter conectado com sucesso, pode trabalhar com o VM como se tivesse iniciado sessão local no seu servidor anfitrião.

#### <a name="connect-to-a-windows-based-vm"></a>Ligue-se a um VM baseado no Windows

Utilize o cliente de ambiente de trabalho remoto para ligar ao VM baseado no Windows alojado no Azure. A maioria das versões do Windows contêm suporte para o protocolo remoto de ambiente de trabalho (RDP). Para outros sistemas operativos, pode encontrar mais informações sobre clientes em [clientes de Ambiente de Trabalho Remoto.](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)

Este artigo detalha como utilizar o suporte RDP do Windows incorporado para ligar ao seu VM: [Como ligar e iniciar sessão a uma máquina virtual Azure que executa o Windows](../../virtual-machines/windows/connect-logon.md).

> [!TIP]
> Pode receber avisos de segurança durante o processo. Por exemplo, avisos como "O ficheiro .rdp é de um editor desconhecido" ou "As suas credenciais de utilizador não podem ser verificadas.". É seguro ignorar estes avisos.

#### <a name="connect-to-a-linux-based-vm"></a>Ligue-se a um VM baseado em Linux

Para se ligar a um VM baseado em Linux, precisa de um cliente seguro do protocolo de concha (SSH). Os seguintes passos utilizem o terminal [Putty](https://www.ssh.com/ssh/putty/) SHH gratuito.

1. Aceda ao [portal do Azure](https://ms.portal.azure.com/).
2. Procure e selecione **máquinas Virtuais.**
3. Selecione o VM a que pretende ligar.
4. Inicie o VM se ainda não estiver a funcionar.
5. Selecione o nome do VM para abrir a sua página **de visão geral.**
6. Note o endereço IP público e o nome DNS do seu VM (se estes valores não estiverem definidos, tem de [criar uma interface de rede](../../virtual-network/virtual-network-network-interface.md#create-a-network-interface))).
7. Abra a aplicação PuTTY.
8. No diálogo de configuração Putty, insira o endereço IP ou o nome DNS do seu VM.

    :::image type="content" source="media/avm-putty.png" alt-text="Ilustra as definições do terminal PuTTY. destacam-se o nome de anfitrião ou endereço IP e as caixas de porta.":::

9. Selecione **Abrir** para abrir um terminal PuTTY.
10. Quando solicitado, insira o nome da conta e a palavra-passe da sua conta Linux VM.

Se tiver problemas de ligação, consulte a documentação do seu cliente SSH. Por exemplo, [Capítulo 10: Mensagens de erro comuns](https://www.ssh.com/ssh/putty/putty-manuals).

Para obter mais detalhes, incluindo como adicionar um ambiente de trabalho a um Linux VM a provisionado, consulte [instalar e configurar o Ambiente de Trabalho Remoto para ligar a um Linux VM em Azure](../../virtual-machines/linux/use-remote-desktop.md).

## <a name="create-a-vm-using-your-own-image"></a>Crie um VM usando a sua própria imagem

Esta secção descreve como criar e implantar uma imagem de máquina virtual (VM) fornecida pelo utilizador. Pode fazê-lo fornecendo imagens VHD do sistema operativo e do disco de dados a partir de um disco rígido virtual (VHD) implantado no Azure.

> [!NOTE]
> Para utilizar opcionalmente uma imagem base aprovada, siga as instruções na [Criar uma imagem VM utilizando uma base aprovada](#create-a-vm-image-using-an-approved-base).

1. Faça o upload das suas imagens para a conta de Armazenamento Azure.
2. Desdobre a imagem VM.
3. Capture a imagem VM.

### <a name="upload-your-images-to-an-azure-storage-account"></a>Faça o upload das suas imagens para uma conta de armazenamento Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Faça o upload do seu sistema operativo generalizado VHD e VHDs de disco de dados para a sua conta de armazenamento Azure.

### <a name="deploy-your-image"></a>Implemente a sua imagem

Crie a sua imagem utilizando o portal Azure ou o Azure PowerShell.

#### <a name="deploy-using-the-azure-portal"></a>Implementar com o portal do Azure

1. Na página inicial, **selecione Criar um recurso,** procurar por "Implementação do Modelo", e selecione **Criar**.
2. Escolha **Construir o seu próprio modelo no editor.**

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="Ilustra a página de implementação personalizada.":::

3. Cole este [modelo JSON](../partner-center-portal/azure-vm-image-certification.md) no editor e selecione **Save**.
4. Forneça os valores dos parâmetros para as páginas de propriedade **personalizadas** apresentadas.

    | Parâmetro | Descrição |
    | ------------ | ------------- |
    | Nome da conta de armazenamento do utilizador | Conteúdo da célula 2 |
    | Nome do recipiente de armazenamento do utilizador | Nome da conta de armazenamento onde o VHD generalizado está localizado |
    | Nome DNS para IP público | Nome PÚBLICO IP DNS. Defina o nome DNS para o endereço IP público no portal Azure após a implementação da oferta. |
    | Nome de utilizador de administrador | Nome de utilizador da conta do administrador para novo VM |
    | Palavra-passe de Administrador | Senha de conta do administrador para novo VM |
    | Tipo de SO | Sistema operativo VM: Windows ou Linux |
    | ID da subscrição | Identificador da subscrição selecionada |
    | Localização | Localização geográfica da implantação |
    | Tamanho da VM | [Tamanho Azure VM](../../virtual-machines/windows/sizes.md), por exemplo Standard_A2 |
    | Nome do endereço IP público | Nome do seu endereço IP público |
    | Nome da VM | Nome do novo VM |
    | Nome da rede virtual | Nome da rede virtual utilizada pelo VM |
    | Nome NIC | Nome do cartão de interface de rede que executa a rede virtual |
    | VHD URL | URL completo do disco de oss VHD |
    |  |  |

5. Depois de fornecer estes valores, **selecione Comprar**.

O Azure vai começar a implantar-se. Cria um novo VM com o VHD especificado não gerido na trajetória de conta de armazenamento especificado. Pode acompanhar o progresso no portal Azure selecionando **Máquinas Virtuais** no lado esquerdo do portal. Quando o VM for criado, o estado mudará de Iniciar para Executar.

#### <a name="deploy-using-azure-powershell"></a>Implementar com o Azure PowerShell

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>Capture a imagem VM

Utilize as seguintes instruções que correspondem à sua abordagem:

* Azure PowerShell: [Como criar uma imagem VM nãogerida a partir de um VM Azure](../../virtual-machines/windows/capture-image-resource.md)
* Azure CLI: [Como criar uma imagem de uma máquina virtual ou VHD](../../virtual-machines/linux/capture-image.md)
* API: [Máquinas Virtuais - Captura](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>Configure a máquina virtual

Esta secção descreve como dimensionar, atualizar e generalizar um VM Azure. Estes passos são necessários para preparar o seu VM para ser implantado no Azure Marketplace.

### <a name="sizing-the-vhds"></a>Dimensionamento dos VHDs

Se selecionou um dos VM pré-configurados com um sistema operativo (e serviços opcionalmente adicionais), já escolheu um tamanho VM standard. Iniciar a sua solução com um SISTEMA pré-configurado é a abordagem recomendada. No entanto, se estiver a instalar um SO manualmente, deve dimensionar o seu VHD primário na sua imagem VM:

* Para o Windows, o sistema operativo VHD deve ser criado como um VHD de formato fixo de 127-128 GB.
* Para o Linux, este VHD deve ser criado como um VHD de formato fixo de 30-50 GB.

Se o tamanho físico for inferior a 127-128 GB, o VHD deve ser expansível (escasso/dinâmico). As imagens base do Windows e SQL Server que são fornecidas já cumprem estes requisitos, por isso não altere o formato ou o tamanho do VHD.

Os discos de dados podem ser tão grandes como 1 TB. Ao decidir sobre o tamanho, lembre-se que os clientes não podem redimensionar VHDs dentro de uma imagem no momento da implementação. Os VHDs do disco de dados devem ser criados como VHDs de formato fixo. Também devem ser expansíveis (escassos/dinâmicos). Os discos de dados podem inicialmente estar vazios ou conter dados.

### <a name="install-the-most-current-updates"></a>Instale as atualizações mais atuais

As imagens base dos VM do sistema operativo devem conter as atualizações mais recentes até à data publicada. Antes de publicar o sistema operativo VHD que criou, certifique-se de atualizar o SISTEMA e todos os serviços instalados com todos os patches de segurança e manutenção mais recentes.

Para o Windows Server, verifique o comando **Check for Updates.**

Para as distribuições do Linux, as atualizações são normalmente descarregadas e instaladas através de uma ferramenta de linha de comando ou de um utilitário gráfico. Por exemplo, o Ubuntu Linux fornece o comando [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) e a ferramenta [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) para atualizar o SISTEMA.

### <a name="perform-additional-security-checks"></a>Realizar verificações de segurança adicionais

Mantenha um alto nível de segurança para as imagens da sua solução no Mercado Azure. O seguinte artigo fornece uma lista de configurações e procedimentos de segurança para ajudá-lo: [Recomendações de segurança para Azure Marketplace Images](../../security/security-recommendations-azure-marketplace-images.md). Algumas destas recomendações são específicas para imagens baseadas em Linux, mas a maioria aplica-se a qualquer imagem VM.

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Executar configuração personalizada e tarefas agendadas

Se for necessária uma configuração adicional, utilize uma tarefa programada que seja programada no arranque para escoar quaisquer alterações finais no VM depois de ter sido implementada. Considere também as seguintes recomendações:

* Se for uma tarefa de execução, a tarefa deve apagar-se depois de concluída com sucesso.
* As configurações não devem depender de unidades que não sejam C ou D, porque só estas duas unidades estão sempre garantidas a existir (a unidade C é o disco do sistema operativo e a unidade D é o disco local temporário).

Para obter mais informações sobre as personalizações do Linux, consulte [extensões e funcionalidades de máquina virtual para Linux.](../../virtual-machines/extensions/features-linux.md)

## <a name="generalize-the-image"></a>Generalizar a imagem

Todas as imagens no Mercado Azure devem ser reutilizáveis de forma genérica. Para isso, o sistema operativo VHD deve ser generalizado, uma operação que remove todos os identificadores e controladores de software específicos de instância de um VM.

### <a name="windows"></a>Windows

Os discos WINDOWS OS são generalizados com a [ferramenta sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Se posteriormente atualizar ou reconfigurar o SO, terá de refazer o sysprep.

> [!WARNING]
> Como as atualizações podem ser executadas automaticamente, depois de executar o sysprep, desligue o VM até ser implantado. Esta paralisação evitará que atualizações subsequentes esveçam alterações específicas do sistema operativo ou dos serviços instalados. Para obter mais informações sobre a execução do sysprep, consulte [Passos para generalizar um VHD).](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)

### <a name="linux"></a>Linux

O processo seguinte generaliza um Linux VM e reimplanta-o como um VM separado. Para mais detalhes, consulte [Como criar uma imagem de uma máquina virtual ou VHD](../../virtual-machines/linux/capture-image.md). Pode parar quando chegar à secção "Criar um VM a partir da imagem capturada".

1. **Remova o agente Azure Linux**

    1. Ligue-se ao seu VM Linux utilizando um cliente SSH.
    2. Na janela SSH, insira o seguinte comando: `sudo waagent -deprovision+user` .
    3. Tipo **Y** para continuar (pode adicionar o parâmetro **de força ao** comando anterior para evitar o passo de confirmação).
    d. Após o fim do comando, escreva **saída** para fechar o cliente SSH.

2. **Parar a máquina virtual**

    1. No portal Azure, selecione o seu grupo de recursos (RG) e desatribua o VM.
    2. O seu VHD está agora generalizado e pode criar um novo VM utilizando este VHD.

## <a name="next-steps"></a>Passos seguintes

Se tiver dificuldades em criar o seu novo VHD baseado em Azure, consulte [questões comuns durante a criação de VHD.](common-issues-during-vhd-creation.md)

Caso contrário:

* [Test Virtual Machine (VM) implantado a partir de VHD](azure-vm-image-certification.md) explica como testar e enviar uma imagem VM para certificação Azure Marketplace, incluindo onde obter a Ferramenta de Teste de Certificação para a ferramenta *Certificada Azure* e como usá-la para certificar a sua imagem VM.
