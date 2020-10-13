---
title: Crie ativos técnicos para uma oferta de máquina virtual Azure Marketplace
description: Saiba como criar e configurar ativos técnicos para uma oferta de máquina virtual (VM) para o Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: a83532e2dd6fc8e83206a3b4a055170b40d131fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91803522"
---
# <a name="create-technical-assets-for-an-azure-marketplace-virtual-machine-offer"></a>Crie ativos técnicos para uma oferta de máquina virtual Azure Marketplace

Ao publicar as imagens da sua máquina virtual (VM) no Azure Marketplace, a equipa da Azure valida a imagem VM para garantir a sua capacidade de arranque, segurança e compatibilidade Azure. Se algum dos testes de alta qualidade falhar, a publicação falhará com uma mensagem que contenha o erro e possíveis [etapas de retificação](https://docs.microsoft.com/azure/marketplace/partner-center-portal/vm-certification-issues-solutions).

Este artigo descreve como criar e configurar ativos técnicos para uma oferta de máquina virtual (VM) para o Azure Marketplace. Um VM contém dois componentes: o disco rígido virtual do sistema operativo (VHD) e os discos de dados associados opcionais VHDs:

- **Sistema operativo VHD**: Contém o sistema operativo e a solução que se implanta com a sua oferta. O processo de preparação do VHD difere consoante se trate de um VM baseado em Linux, baseado no Windows ou um VM personalizado.

- **VHDs de disco de dados**: Armazenamento dedicado e persistente para um VM. Não utilize o VHD do sistema operativo (por exemplo, o C: unidade) para armazenar informações persistentes.

Uma imagem VM contém um disco do sistema operativo e até 16 discos de dados. Utilize um VHD por disco de dados, mesmo que o disco esteja em branco.

> [!NOTE]
> Independentemente do sistema operativo que utilize, adicione apenas o número mínimo de discos de dados necessários pela solução. Os clientes não podem remover discos que fazem parte de uma imagem no momento da implementação, mas podem sempre adicionar discos durante ou após a implementação.

> [!IMPORTANT]
> Cada Imagem VM num plano deve ter o mesmo número de discos de dados.

## <a name="fundamental-technical-knowledge"></a>Conhecimento técnico fundamental

Projetar, construir e testar estes ativos leva tempo e requer conhecimento técnico tanto da plataforma Azure como das tecnologias usadas para construir a oferta. Além do seu domínio de solução, a sua equipa de engenharia deve ter conhecimento das seguintes tecnologias da Microsoft:

- Compreensão básica dos [Serviços Azure](https://azure.microsoft.com/services/)
- Como [projetar e arquiteto aplicações Azure](https://azure.microsoft.com/solutions/architecture/)
- Conhecimentos de trabalho das [Máquinas Virtuais Azure,](https://azure.microsoft.com/services/virtual-machines/) [Azure Storage](https://azure.microsoft.com/services/?filter=storage)e [Azure Networking](https://azure.microsoft.com/services/?filter=networking)
- Conhecimento de trabalho do Gestor de [Recursos Azure](https://azure.microsoft.com/features/resource-manager/)
- Conhecimento de Trabalho da [JSON](https://www.json.org/)

### <a name="optional-suggested-tools"></a>Ferramentas sugeridas opcionalmente

Considere usar um dos seguintes ambientes de script para ajudar a gerir VMs e VHDs:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [CLI do Azure](https://code.visualstudio.com/)

Além disso, considere adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento:

- [Explorador de Armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-vm-image-using-an-approved-base"></a>Criar uma imagem VM usando uma base aprovada

Para criar os seus ativos técnicos de máquina virtual utilizando uma imagem que construiu nas suas próprias instalações, consulte [Criar uma imagem VM utilizando uma base aprovada](#create-a-vm-image-using-an-approved-base) abaixo.

Esta secção descreve vários aspetos da utilização de uma base aprovada, como a utilização do Protocolo de Ambiente de Trabalho Remoto (PDR), a seleção de um tamanho para o VM, a instalação das atualizações mais recentes do Windows e a generalização da imagem VHD.

Siga as orientações deste artigo para utilizar o Azure para criar um VM que contenha um sistema operativo pré-configurado e endossado. Se isto não for compatível com a sua solução, é possível criar e configurar um VM no local utilizando um sistema operativo aprovado. Em seguida, pode configurar e prepará-lo para o upload, conforme descrito no [Prep a Windows VHD ou VHDX para fazer o upload para Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

### <a name="select-an-approved-base-image"></a>Selecione uma imagem base aprovada

Selecione o sistema de funcionamento do Windows ou o Linux como base.

O sistema operativo VHD para a sua imagem VM deve basear-se numa imagem base aprovada pelo Azure que contenha servidor do Windows ou sql Server.

Quando submete um pedido para republicar a sua imagem com atualizações, o caso de verificação de número parcial pode falhar. Nesse caso, a sua imagem não será aprovada.

Esta falha ocorrerá quando usou uma imagem base que pertence a outra editora e atualizou a imagem. Neste caso, não poderá publicar a sua imagem.

Para corrigir este problema, recupere a sua última imagem do Azure Marketplace e faça alterações nessa imagem. Veja o seguinte para ver as imagens base aprovadas onde pode pesquisar a sua imagem:

#### <a name="windows"></a>Windows

- Windows Server ([2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
- SQL Server 2019[(Enterprise,](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview) [Standard,](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview) [Web)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview)
- SQL Server 2014[(Enterprise,](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) [Standard,](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) [Web)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)
- SQL Server 2012 SP2[(Enterprise,](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) [Standard,](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) [Web)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)

#### <a name="linux"></a>Linux

A Azure oferece uma gama de distribuições linux aprovadas. Para obter uma lista atual, consulte [o Linux sobre as distribuições endossadas pela Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="create-vm-on-the-azure-portal"></a>Criar VM no portal Azure

Siga estes passos para criar a imagem VM base no [portal Azure:](https://ms.portal.azure.com/)

1. Faça o sômis no [portal Azure](https://ms.portal.azure.com/) com a conta Microsoft associada à subscrição Azure que pretende utilizar para publicar a sua oferta VM.
2. Crie um novo grupo de recursos e forneça o **nome do grupo de recursos,** **subscrição**e **localização do grupo de recursos.** Para mais detalhes, consulte [Gerir os recursos.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)

    :::image type="content" source="media/vm/create-resource-group.png" alt-text="Mostra o início da criação de um grupo de recursos.":::

3. Selecione **máquinas virtuais** na navegação à esquerda para exibir a página de detalhes das máquinas Virtuais.
4. **Selecione + Adicione** para abrir a experiência da máquina **virtual.**
5. Selecione a imagem da lista de drop-down ou **selecione Navegue em todas as imagens públicas e privadas** para pesquisar ou navegar em todas as imagens de máquinas virtuais disponíveis. Exemplo:

    :::image type="content" source="media/vm/create-resource-group-example.png" alt-text="Mostra o início da criação de um grupo de recursos.":::

6. Selecione o tamanho do VM para implementar utilizando as seguintes recomendações:
    1. Se planeia desenvolver o VHD no local, o tamanho não importa. Considere usar um dos VMs menores.
    2. Se planeia desenvolver a imagem em Azure, considere utilizar um dos tamanhos VM recomendados para a imagem selecionada.

    :::image type="content" source="media/vm/create-virtual-machine.png" alt-text="Mostra o início da criação de um grupo de recursos.":::

7. Na secção **Discos,** expanda a secção **Advanced** e desaver a opção **Utilização de discos geridos** para **Nº**.

    :::image type="content" source="media/vm/use-managed-disks.png" alt-text="Mostra o início da criação de um grupo de recursos.":::

8. Forneça os outros detalhes necessários para criar o VM.
9. Selecione **Review + crie** para rever as suas escolhas. Quando vir a mensagem **A validação passou**, selecione **Criar**.

O Azure começa a aturar a máquina virtual especificada. Pode acompanhar o seu progresso selecionando o separador **Máquinas Virtuais** à esquerda. Depois de criado, o estado mudará para **Running**.

### <a name="create-a-generation-2-vm-on-the-azure-portal"></a>Criar uma Geração 2 VM no portal Azure

Criar uma geração 2 (Gen2) VM no portal Azure.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).
2. Selecione **Criar um recurso**.
3. **Selecione Veja tudo** no Azure Marketplace à esquerda.
4. Selecione uma imagem que suporte a Gen2.
5. Selecione **Criar**.
6. No separador **Avançado,** na secção **de geração VM,** selecione a opção **Gen 2.**
7. No **separador Basics,** detalhes de Under **Instance,** vá ao **Tamanho** e abra a lâmina **de tamanho VM** Select.
8. Selecione um tamanho recomendado de [Gen 2 VM](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2#generation-2-vm-sizes) suportado e tamanho.
9. Passeie pelo fluxo de [criação do portal Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) para terminar a criação do VM.

    :::image type="content" source="media/vm/vm-generation.png" alt-text="Mostra o início da criação de um grupo de recursos.":::

## <a name="connect-to-your-azure-vm"></a>Ligue-se ao seu Azure VM

Esta secção explica como ligar e assinar no VM que criou no Azure. Depois de ter conectado com sucesso, pode trabalhar com o VM como se tivesse iniciado sessão local no seu servidor anfitrião.

### <a name="connect-to-a-windows-based-vm"></a>Ligue-se a um VM baseado no Windows

Utilize o cliente de ambiente de trabalho remoto para ligar ao VM baseado no Windows alojado no Azure. A maioria das versões do Windows contêm suporte para o protocolo remoto de ambiente de trabalho (RDP). Para outros sistemas operativos, pode encontrar mais informações sobre clientes em [clientes de Ambiente de Trabalho Remoto.](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)

Este artigo detalha como utilizar o suporte RDP do Windows incorporado para ligar ao seu VM: [Como ligar e iniciar sação a uma máquina virtual Azure que executa o Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

> [!TIP]
> Pode receber avisos de segurança durante o processo. Por exemplo, avisos como "O ficheiro .rdp é de um editor desconhecido" ou "As suas credenciais de utilizador não podem ser verificadas.". É seguro ignorar estes avisos.

### <a name="connect-to-a-linux-based-vm"></a>Ligue-se a um VM baseado em Linux

Para se ligar a um VM baseado em Linux, precisa de um cliente seguro do protocolo de concha (SSH). Os seguintes passos utilizem o terminal [Putty](https://www.ssh.com/ssh/putty/) SHH gratuito.

1. Aceda ao [portal do Azure](https://ms.portal.azure.com/).
2. Procure e selecione máquinas Virtuais.
3. Selecione o VM a que pretende ligar.
4. Inicie o VM se ainda não estiver a funcionar.
5. Selecione o nome do VM para abrir a sua página de visão geral.
6. Note o endereço IP público e o nome DNS do seu VM (se estes valores não estiverem definidos, deve [criar uma interface de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface).
7. Abra a aplicação PuTTY.
8. No diálogo de configuração Putty, insira o endereço IP ou o nome DNS do seu VM.

    :::image type="content" source="media/vm/putty-configuration.png" alt-text="Mostra o início da criação de um grupo de recursos.":::

9. Selecione **Abrir** para abrir um terminal PuTTY.
10. Quando solicitado, insira o nome da conta e a palavra-passe da sua conta Linux VM.

## <a name="configure-the-virtual-machine"></a>Configure a máquina virtual

Esta secção descreve como dimensionar, atualizar e generalizar um VM Azure. Estes passos são necessários para preparar o seu VM para ser implantado no Azure Marketplace.

### <a name="sizing-the-vhds"></a>Dimensionamento dos VHDs

As seguintes regras são para limitações no tamanho do disco de SO. Quando submeter qualquer pedido, certifique-se de que o tamanho do disco OS está dentro da limitação para Linux ou Windows.

| SO | Tamanho VHD recomendado |
| --- | --- |
| Linux | 30 a 1023 GB |
| Windows | 30 a 250 GB |

Como os VMs permitem o acesso ao sistema operativo subjacente, certifique-se de que o tamanho do VHD é grande o suficiente para o VHD. Como os discos não são expansíveis sem tempo de inatividade, use um tamanho de disco entre 30 e 50 &nbsp; GB.

| Tamanho VHD | Tamanho real ocupado | Solução |
| --- | --- | --- |
| >500 TB | n/a | Contacte a equipa de apoio para obter uma aprovação de exceção. |
| 250-500 TB | >200 GB diferente do tamanho da bolha | Contacte a equipa de apoio para obter uma aprovação de exceção. |

### <a name="install-the-most-current-updates"></a>Instale as atualizações mais atuais

As imagens base dos VM do sistema operativo devem conter as atualizações mais recentes até à data publicada. Antes de publicar o sistema operativo VHD que criou, certifique-se de atualizar o SISTEMA e todos os serviços instalados com todos os patches de segurança e manutenção mais recentes.

- Para o Windows Server, verifique o comando Check for Updates.
- Para as distribuições do Linux, as atualizações são normalmente descarregadas e instaladas através de uma ferramenta de linha de comando ou de um utilitário gráfico. Por exemplo, o Ubuntu Linux fornece o comando [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) e a ferramenta [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) para atualizar o SISTEMA.

#### <a name="perform-additional-security-checks"></a>Realizar verificações de segurança adicionais

Mantenha um alto nível de segurança para as imagens da sua solução no Mercado Azure. Para obter uma lista de configurações e procedimentos de segurança, consulte [recomendações de segurança para imagens do mercado Azure](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images). Algumas destas recomendações são específicas para imagens baseadas em Linux, mas a maioria aplica-se a qualquer imagem VM.

#### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Executar configuração personalizada e tarefas agendadas

Se precisar de configuração adicional, utilize uma tarefa programada que seja operada no arranque para escoar as alterações finais no VM depois de ter sido implementada. Além disso, considere o seguinte:

- Se for uma tarefa de execução, a tarefa deve apagar-se depois de concluída com sucesso.
- As configurações não devem depender de unidades que não sejam C ou D, porque só estas duas unidades estão sempre garantidas a existir (a unidade C é o disco do sistema operativo e a unidade D é o disco local temporário).

Para obter mais informações sobre as personalizações do Linux, consulte [extensões e funcionalidades de máquina virtual para Linux.](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)

## <a name="generalize-the-image"></a>Generalizar a imagem

Todas as imagens no Mercado Azure devem ser reutilizáveis de forma genérica. Para isso, o sistema operativo VHD deve ser generalizado, uma operação que remove todos os identificadores e controladores de software específicos de instância de um VM.

### <a name="for-windows"></a>Para Windows

Os discos WINDOWS OS são generalizados com a ferramenta [sysprep.](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) Se atualizar ou reconfigurar o SO, terá de voltar a executar sysprep.

> [!WARNING]
> Depois de executar o sysprep, desligue o VM até ser implantado porque as atualizações podem ser executadas automaticamente. Esta paralisação evitará que atualizações subsequentes esveçam alterações específicas do sistema operativo ou dos serviços instalados. Para obter mais informações sobre a execução do sysprep, consulte [passos para generalizar um VHD](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>Para Linux

O processo seguinte generaliza um Linux VM e reimplanta-o como um VM separado. Para mais detalhes, consulte [Como criar uma imagem de uma máquina virtual ou VHD](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image). Pode parar quando chegar à secção "Criar um VM a partir da imagem capturada".

1. Remova o agente Azure Linux

    1. Ligue-se ao seu VM Linux usando um cliente SSH
    2. Na janela SSH, insira o seguinte comando: `sudo waagent –deprovision+user` .
    3. Tipo Y para continuar (pode adicionar o parâmetro de força ao comando anterior para evitar o passo de confirmação).
    4. Após o fim do comando, escreva saída para fechar o cliente SSH.

2. Parar a máquina virtual

    1. No portal Azure, selecione o seu grupo de recursos (RG) e desatribua o VM.
    2. O seu VHD está agora generalizado e pode criar um novo VM utilizando este VHD.

## <a name="next-steps"></a>Passos seguintes

- Se tiver dificuldades em criar o seu novo VHD baseado em Azure, consulte [questões comuns durante a criação de VHD.](common-issues-during-vhd-creation.md)
- Caso contrário, [testar a Máquina Virtual (VM) implantada a partir da VHD](azure-vm-image-certification.md) explica como testar e enviar uma imagem VM para certificação Azure Marketplace, incluindo onde obter a Ferramenta de Teste de Certificação para a ferramenta Certificada Azure e como usá-la para certificar a sua imagem VM.
