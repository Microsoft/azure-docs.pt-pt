---
title: Crie os seus ativos técnicos da Máquina Virtual Azure
description: Aprenda a criar e configurar ativos técnicos para uma oferta de máquina virtual (VM) para o Azure Marketplace.
author: dannyevers
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 4d2d33f9d83132147b5b257ffcd6d659f272b8ec
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730723"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>Crie os seus ativos técnicos da Máquina Virtual Azure

> [!IMPORTANT]
> Estamos a mover a gestão das suas ofertas de Máquina Virtual Azure do Portal do Parceiro cloud para o Partner Center. Até que as suas ofertas sejam migradas, siga as instruções em [Criar ativos técnicos para uma oferta de máquina virtual para](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-technical-assets) o Portal parceiro cloud para gerir as suas ofertas.

Este artigo descreve como criar e configurar ativos técnicos para uma oferta de máquina virtual (VM) para o Azure Marketplace. Um VM contém dois componentes: o disco rígido virtual do sistema operativo (VHD) e os VHDs de discos de dados associados opcionais:

* **Sistema operativo VHD** – Contém o sistema operativo e a solução que implementa com a sua oferta. O processo de preparação do VHD difere dependendo se se trata de um VM baseado no Linux, baseado no Windows ou personalizado.
* **VHDs** de discos de dados - Armazenamento dedicado e persistente para um VM. Não utilize o Sistema operativo VHD (por exemplo, o C: unidade) para armazenar informações persistentes.

Uma imagem VM contém um disco de sistema operativo e até 16 discos de dados. Utilize um VHD por disco de dados, mesmo que o disco esteja em branco.

> [!NOTE]
> Independentemente do sistema operativo utilizado, adicione apenas o número mínimo de discos de dados necessários pela solução. Os clientes não podem remover discos que façam parte de uma imagem no momento da implantação, mas podem sempre adicionar discos durante ou após a implantação.

> [!IMPORTANT]
> Cada Imagem VM num plano deve ter o mesmo número de discos de dados.

## <a name="fundamental-technical-knowledge"></a>Conhecimento técnico fundamental

Projetar, construir e testar estes ativos leva tempo e requer conhecimentos técnicos tanto da plataforma Azure como das tecnologias usadas para construir a oferta. Além do seu domínio de solução, a sua equipa de engenharia deve ter conhecimento das seguintes tecnologias da Microsoft:

* Compreensão básica dos [Serviços Azure](https://azure.microsoft.com/services/)
* Como [conceber e arquiteto sintetizar aplicações Azure](https://azure.microsoft.com/solutions/architecture/)
* Conhecimento de trabalho de [Máquinas Virtuais Azure,](https://azure.microsoft.com/services/virtual-machines/) [Armazenamento Azure](https://azure.microsoft.com/services/?filter=storage) e [Networking Azure](https://azure.microsoft.com/services/?filter=networking)
* Conhecimento de trabalho do Gestor de [Recursos Azure](https://azure.microsoft.com/features/resource-manager/)
* Conhecimento de Trabalho da [JSON](https://www.json.org/)

## <a name="suggested-tools--optional"></a>Ferramentas sugeridas – opcionais

Considere utilizar um dos seguintes ambientes de scripts para ajudar a gerir VMs e VHDs:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [CLI do Azure](https://code.visualstudio.com/)

Além disso, considere adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento:

* [Explorador do Storage do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/)
  * Extensão: Ferramentas de [Gestor de Recursos Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * Extensão: [Embelezar](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Reveja as ferramentas disponíveis na página [Azure Developer Tools](https://azure.microsoft.com/product-categories/developer-tools/) e, se estiver a utilizar o Visual Studio, o [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-a-vm-image-using-an-approved-base"></a>Criar uma imagem VM usando uma base aprovada

> [!NOTE]
> Para criar os seus ativos técnicos de máquina virtual utilizando uma imagem que construiu nas suas próprias instalações, vá criar [um VM usando a sua própria imagem](#create-a-vm-using-your-own-image).

Esta secção descreve vários aspetos da utilização de uma base aprovada, como a utilização do Protocolo de Ambiente de Trabalho Remoto (RDP), a seleção de um tamanho para o VM, a instalação das mais recentes atualizações do Windows e a generalização da imagem VHD.

As secções seguintes concentram-se principalmente em VHDs baseados em janelas. Para obter mais informações sobre a criação de VHDs baseados em Linux, consulte [o Linux nas distribuições endossadas pelo Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

> [!WARNING]
> Siga as orientações neste tópico para utilizar o Azure para criar um VM contendo um sistema operativo pré-configurado e endossado. Se isto não for compatível com a sua solução, é possível criar e configurar um VM no local utilizando um sistema operativo aprovado. Em seguida, pode configurá-lo e prepará-lo para upload conforme descrito em [Prepare um Windows VHD ou VHDX para fazer o upload para Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

### <a name="select-an-approved-base"></a>Selecione uma base aprovada

Selecione o sistema de funcionamento windows ou o Linux como base.

#### <a name="windows"></a>Windows

O VHD do sistema operativo para a sua imagem VM baseada no Windows deve basear-se numa imagem base aprovada pelo Azure que contenha o Windows Server ou o SQL Server. Para começar, crie um VM a partir de uma das seguintes imagens do portal Azure:

* Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)
* [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)

> [!NOTE]
> Se estiver a utilizar o portal Atual Azure ou Azure PowerShell, então as imagens do Windows Server publicadas a 8 de setembro de 2014 e posteriormente são aprovadas.

#### <a name="linux"></a>Linux

O Azure oferece uma gama de distribuições de Linux aprovadas. Para obter uma lista atual, consulte [o Linux nas distribuições endossadas pelo Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="create-vm-in-the-azure-portal"></a>Criar VM no portal Azure

Siga estes passos para criar a imagem Base VM no [portal Azure:](https://ms.portal.azure.com/)

1. Inscreva-se no [portal Azure](https://ms.portal.azure.com/) com a conta Microsoft associada à subscrição Azure que pretende utilizar para publicar a sua oferta VM.
2. Crie um novo grupo de recursos e forneça o nome do **grupo Recursos,** **Subscrição,** e localização do **grupo Derecursos.** Para mais detalhes, consulte [Gerir recursos.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)
3. Selecione **máquinas Virtuais** à esquerda para exibir a página de detalhes das máquinas virtuais.
4. Selecione **+ Adicione** para abrir a **experiência Criar uma máquina virtual**.
5. Selecione a imagem a partir da lista de lançamentos ou clique em Navegar em **todas as imagens públicas e privadas** para pesquisar ou navegar em todas as imagens de máquinas virtuais disponíveis.
6. Selecione o tamanho do VM para implementar usando as seguintes recomendações:
    * Se planeiadesenvolver o VHD no local, o tamanho não importa. Considere usar um dos VMs menores.
    * Se planeia desenvolver a imagem em Azure, considere utilizar um dos tamanhos vm recomendados para a imagem selecionada.

7. Na secção **Discos,** expanda a secção **Avançada** e define a opção de utilização de **discos geridos** para **Nº**.
8. Forneça os outros detalhes necessários para criar o VM.
9. Selecione **Review + crie** para rever as suas escolhas. Quando vir a **mensagem de validação passada,** selecione **Criar**.

O Azure começa a fornecer a máquina virtual que especificou. Pode acompanhar o seu progresso selecionando o separador **Máquinas Virtuais** à esquerda. Depois de criado, o estado mudará para **Running**.

Se tiver dificuldade em criar o seu novo VHD baseado em Azure, consulte questões comuns durante a criação de [VHD (FAQs)](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-issues-during-vhd-creation).

### <a name="connect-to-your-azure-vm"></a>Ligue-se ao seu Azure VM

Esta secção explica como ligar e assinar no VM que criou no Azure. Depois de conectado com sucesso, pode trabalhar com o VM como se estivesse localmente ligado ao seu servidor anfitrião.

#### <a name="connect-to-a-windows-based-vm"></a>Ligue-se a um VM baseado no Windows

Utilize o cliente de ambiente de trabalho remoto para se ligar ao VM baseado no Windows hospedado no Azure. A maioria das versões do Windows contêm suporte para o protocolo remoto de desktop (RDP). Para outros sistemas operativos, pode encontrar mais informações sobre clientes em [clientes do Remote Desktop](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

Este artigo detalha como utilizar o suporte rdp do Windows incorporado para se ligar ao seu VM: [Como ligar e iniciar sessão a uma máquina virtual Azure que executa](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)o Windows .

> [!TIP]
> Pode receber avisos de segurança durante o processo. Por exemplo, avisos como "O ficheiro .rdp é de um editor desconhecido" ou "As suas credenciais de utilizador não podem ser verificadas". É seguro ignorar estes avisos.

#### <a name="connect-to-a-linux-based-vm"></a>Ligue-se a um VM baseado em Linux

Para se ligar a um VM baseado em Linux, precisa de um cliente seguro de protocolo de concha (SSH). Os seguintes passos utilizam o terminal [PuTTY](https://www.ssh.com/ssh/putty/) SHH gratuito.

1. Vá ao [portal Azure.](https://ms.portal.azure.com/)
2. Procure e selecione **máquinas Virtuais**.
3. Selecione o VM a que pretende ligar.
4. Inicie o VM se ainda não estiver em funcionamento.
5. Selecione o nome do VM para abrir a sua página **de visão geral.**
6. Note o endereço IP público e o nome DNS do seu VM (se estes valores não estiverem definidos, deve criar uma interface de [rede).](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)
7. Abra a aplicação PuTTY.
8. No diálogo de configuração PuTTY, introduza o endereço IP ou o nome DNS do seu VM.

    :::image type="content" source="media/avm-putty.png" alt-text="Ilustra as definições do terminal PuTTY. destacam-se o nome do anfitrião ou o endereço IP e as caixas de porta.":::

9. **Selecione Abrir** para abrir um terminal PuTTY.
10. Quando solicitado, introduza o nome da conta e a palavra-passe da sua conta VM Linux.

Se tiver problemas de ligação, consulte a documentação para o seu cliente SSH. Por exemplo, [Capítulo 10: Mensagens de erro comuns](https://www.ssh.com/ssh/putty/putty-manuals).

Para mais detalhes, incluindo como adicionar um ambiente de trabalho a um VM Linux provisionado, consulte Instalar e configurar o [Ambiente de Trabalho Remoto para ligar a um Linux VM em Azure](https://docs.microsoft.com/azure/virtual-machines/linux/use-remote-desktop).

## <a name="create-a-vm-using-your-own-image"></a>Crie um VM usando a sua própria imagem

Esta secção descreve como criar e implantar uma imagem de máquina virtual fornecida pelo utilizador (VM). Pode fazê-lo fornecendo imagens VHD do sistema operativo e do disco de dados a partir de um disco rígido virtual (VHD) implantado no Azure.

> [!NOTE]
> Para utilizar opcionalmente uma imagem de base aprovada, siga as instruções em [Criar uma imagem VM utilizando uma base aprovada](#create-a-vm-image-using-an-approved-base).

1. Faça upload das suas imagens para a conta de Armazenamento Azure.
2. Implante a imagem VM.
3. Capture a imagem VM.

### <a name="upload-your-images-to-an-azure-storage-account"></a>Faça upload das suas imagens para uma conta de armazenamento Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Faça upload do seu sistema operativo generalizado VHD e vHDs do disco de dados para a sua conta de armazenamento Azure.

### <a name="deploy-your-image"></a>Implante a sua imagem

Crie a sua imagem usando o portal Azure ou o Azure PowerShell.

#### <a name="deploy-using-the-azure-portal"></a>Implementar com o portal do Azure

1. Na página inicial, selecione **Criar um recurso,** procurar "Implementação do Modelo", e selecione **Criar**.
2. Escolha **Construir o seu próprio modelo no editor.**

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="Ilustra a página de implementação personalizada.":::

3. Colhe este [modelo JSON](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-deploy-json-template) no editor e selecione **Guardar**.
4. Forneça os valores do parâmetro para as páginas de propriedade de **implementação personalizada** saem.

    | Parâmetro | Descrição |
    | ------------ | ------------- |
    | Nome da conta de armazenamento do utilizador | Conteúdo da célula 2 |
    | Nome do recipiente de armazenamento do utilizador | Nome da conta de armazenamento onde está localizado o VHD generalizado |
    | Nome DNS para IP Público | Nome público do DNS IP. Defina o nome DNS para o endereço IP público no portal Azure após a implementação da oferta. |
    | Nome do utilizador do administrador | Nome de utilizador da conta de administrador para novo VM |
    | Palavra-passe de Administrador | Senha da conta de administrador para novo VM |
    | Tipo de SO | Sistema operativo VM: Windows ou Linux |
    | ID da subscrição | Identificador da subscrição selecionada |
    | Localização | Localização geográfica da implantação |
    | Tamanho da VM | [Tamanho VM azure,](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)por exemplo Standard_A2 |
    | Nome do endereço IP público | Nome do seu endereço IP público |
    | Nome da VM | Nome do novo VM |
    | Nome da rede virtual | Nome da rede virtual utilizada pelo VM |
    | Nome NIC | Nome do cartão de interface de rede que executa a rede virtual |
    | VHD URL | URL VHD do disco completo |
    |  |  |

5. Depois de fornecer estes valores, selecione **Comprar**.

O Azure vai começar a ser implantado. Cria um novo VM com o VHD não gerido especificado no caminho especificado da conta de armazenamento. Pode acompanhar o progresso no portal Azure selecionando **Máquinas Virtuais** no lado esquerdo do portal. Quando o VM for criado, o estado passará de Iniciar para Executar.

#### <a name="deploy-using-azure-powershell"></a>Implementar com o Azure PowerShell

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>Capture a imagem VM

Utilize as seguintes instruções que correspondam à sua abordagem:

* Azure PowerShell: [Como criar uma imagem VM não gerida a partir de um Azure VM](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
* Azure CLI: [Como criar uma imagem de uma máquina virtual ou VHD](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
* API: [Máquinas Virtuais - Captura](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>Configure a máquina virtual

Esta secção descreve como dimensionar, atualizar e generalizar um VM Azure. Estes passos são necessários para preparar o seu VM para ser implantado no Azure Marketplace.

### <a name="sizing-the-vhds"></a>Dimensionamento dos VHDs

Se selecionou um dos VMs pré-configurado com um sistema operativo (e serviços opcionalmente adicionais), já escolheu um tamanho De VM Azure padrão. Iniciar a sua solução com um Sistema operativo pré-configurado é a abordagem recomendada. No entanto, se estiver a instalar um SO manualmente, deve dimensionar o seu VHD primário na sua imagem VM:

* Para windows, o vHD do sistema operativo deve ser criado como um VHD de formato fixo de 127-128 GB.
* Para o Linux, este VHD deve ser criado como um VHD de formato fixo de 30 a 50 GB.

Se o tamanho físico for inferior a 127-128 GB, o VHD deve ser expansível (escasso/dinâmico). As imagens base do Windows e do SQL Server que já são fornecidas cumprem estes requisitos, por isso não altere o formato ou o tamanho do VHD.

Os discos de dados podem ser tão grandes como 1 TB. Ao decidir sobre o tamanho, lembre-se que os clientes não podem redimensionar Os VHDs dentro de uma imagem no momento da implementação. Os VHDs do disco de dados devem ser criados como VHDs de formação fixa. Devem também ser expansíveis (escassos/dinâmicos). Os discos de dados podem inicialmente estar vazios ou conter dados.

### <a name="install-the-most-current-updates"></a>Instale as atualizações mais atuais

As imagens base dos VMs do sistema operativo devem conter as últimas atualizações até à data publicada. Antes de publicar o sistema operativo VHD que criou, certifique-se de atualizar o SISTEMA e todos os serviços instalados com todos os patches de segurança e manutenção mais recentes.

Para o Windows Server, execute o comando **'Verificar' Atualizações.**

Para as distribuições do Linux, as atualizações são geralmente descarregadas e instaladas através de uma ferramenta de linha de comando ou de um utilitário gráfico. Por exemplo, o Ubuntu Linux fornece o comando [de get apt e](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) a ferramenta Update [Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) para atualizar o OS.

### <a name="perform-additional-security-checks"></a>Realizar verificações de segurança adicionais

Mantenha um alto nível de segurança para as suas imagens de solução no Mercado Azure. O seguinte artigo fornece uma lista de verificação de configurações e procedimentos de segurança para ajudá-lo: Recomendações de [segurança para Imagens do Mercado Azure](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images). Algumas destas recomendações são específicas das imagens baseadas em Linux, mas a maioria aplica-se a qualquer imagem VM.

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Execute a configuração personalizada e as tarefas programadas

Se for necessária uma configuração adicional, utilize uma tarefa programada que seja lançada no arranque para efazer quaisquer alterações finais no VM depois de ter sido implementada. Considere também as seguintes recomendações:

* Se for uma tarefa executada, a tarefa deve apagar-se depois de concluída com sucesso.
* As configurações não devem basear-se em unidades que não sejam C ou D, porque apenas estas duas unidades estão sempre garantidas (a unidade C é o disco do sistema operativo e a unidade D é o disco local temporário).

Para obter mais informações sobre as personalizações do Linux, consulte [extensões e funcionalidades da máquina virtual para linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).

## <a name="generalize-the-image"></a>Generalizar a imagem

Todas as imagens no Mercado Azure devem ser reutilizáveis de forma genérica. Para tal, o sistema operativo VHD deve ser generalizado, uma operação que remova todos os identificadores específicos de instâncias e controladores de software de um VM.

### <a name="windows"></a>Windows

Os discos Do Windows OS são generalizados com a [ferramenta de sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Se posteriormente atualizar ou reconfigurar o Sistema operativo, tem de reexecutar a sysprep.

> [!WARNING]
> Uma vez que as atualizações podem ser executadas automaticamente, depois de executar sysprep, desligue o VM até ser implantado. Esta paragem evitará atualizações subsequentes de fazer alterações específicas de exemplo no sistema operativo ou nos serviços instalados. Para obter mais informações sobre a execução de sysprep, consulte [Steps para generalizar um VHD](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep).

### <a name="linux"></a>Linux

O processo seguinte generaliza um VM Linux e reimplanta-o como um VM separado. Para mais detalhes, consulte [Como criar uma imagem de uma máquina virtual ou VHD](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image). Pode parar quando chegar à secção "Criar um VM a partir da imagem capturada".

1. **Remova o agente Azure Linux**

    1. Ligue-se ao seu VM Linux utilizando um cliente SSH.
    2. Na janela SSH, introduza `sudo waagent -deprovision+user`o seguinte comando: .
    3. Escreva **Y** para continuar (pode adicionar o parâmetro **de força** ao comando anterior para evitar o passo de confirmação).
    d. Depois de o comando estar concluído, digite **saída** para fechar o cliente SSH.

2. **Parar a máquina virtual**

    1. No portal Azure, selecione o seu grupo de recursos (RG) e desloque o VM.
    2. O seu VHD está agora generalizado e pode criar um novo VM usando este VHD.

## <a name="next-steps"></a>Passos seguintes

Se encontrou dificuldades em criar o seu novo VHD baseado em Azure, consulte [questões comuns durante a criação de VHD](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues).

Caso contrário:

* [Certifica a sua imagem VM](https://docs.microsoft.com/azure/marketplace/partner-center-portal/get-sas-uri) explica como testar e submeter uma imagem VM para certificação Azure Marketplace, incluindo onde obter a ferramenta de teste de certificação para a ferramenta *Azure Certified* e como usá-la para certificar a sua imagem VM.
