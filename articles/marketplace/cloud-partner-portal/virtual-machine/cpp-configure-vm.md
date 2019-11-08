---
title: Configurar a VM hospedada em Microsoft Azure para o Azure Marketplace
description: Explica como dimensionar, atualizar e generalizar uma VM hospedada no Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: ce7fe49b07dc250a9f56ff73229e347b997f0cc0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824485"
---
# <a name="configure-the-azure-hosted-vm"></a>Configurar a VM hospedada no Azure

Este artigo explica como dimensionar, atualizar e generalizar uma VM (máquina virtual) hospedada no Azure.  Essas etapas são necessárias para preparar a VM a ser implantada no Azure Marketplace.


## <a name="sizing-the-vhds"></a>Dimensionando os VHDs

<!--TD: Check if the following assertion is true. I didn't understand the original content. -->
Se você tiver selecionado uma das VMs pré-configuradas com um sistema operacional (e, opcionalmente, serviços adicionais), você já selecionou um tamanho de VM do Azure padrão, conforme descrito na [guia SKUs da máquina virtual](./cpp-skus-tab.md).  Iniciar sua solução com um sistema operacional pré-configurado é a abordagem recomendada.  No entanto, se você estiver instalando um so manualmente, deverá dimensionar seu VHD primário em sua imagem de VM:

- Para o Windows, o VHD do sistema operacional deve ser criado como um VHD de formato fixo de 127-128 GB. 
- Para o Linux, esse VHD deve ser criado como um VHD de formato fixo de 30-50 GB.

Se o tamanho físico for menor que 127-128 GB, o VHD deverá ser esparso. As imagens base do Windows e do SQL Server fornecidas já atendem a esses requisitos, portanto, não altere o formato ou o tamanho do VHD obtido. 

Os discos de dados podem ser tão grandes quanto 1 TB. Ao decidir sobre seu tamanho, lembre-se de que os clientes não podem redimensionar VHDs em uma imagem no momento da implantação. Os VHDs de disco de dados devem ser criados como VHDs de formato fixo. Eles também devem ser esparsos. Os discos de dados podem inicialmente estar vazios ou conter dados.


## <a name="install-the-most-current-updates"></a>Instalar as atualizações mais recentes

As imagens base das VMs do sistema operacional contêm as atualizações mais recentes até a data de publicação. Antes de publicar o VHD do sistema operacional que você criou, certifique-se de atualizar o sistema operacional e todos os serviços instalados com todos os patches de segurança e manutenção mais recentes.

Para o Windows Server 2016, execute o comando **verificar atualizações** .  Caso contrário, para versões mais antigas do Windows, consulte [como obter uma atualização por meio de Windows Update](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update).  O Windows Update instalará automaticamente as atualizações de segurança críticas e importantes mais recentes.

Para distribuições do Linux, as atualizações são normalmente baixadas e instaladas por meio de uma ferramenta de linha de comando ou um utilitário gráfico.  Por exemplo, Ubuntu Linux fornece o comando [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) e a ferramenta [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) para atualizar o sistema operacional.


## <a name="perform-additional-security-checks"></a>Executar verificações de segurança adicionais

Você deve manter um alto nível de segurança para suas imagens de solução no Azure Marketplace.  O artigo a seguir fornece uma lista de verificação de configurações e procedimentos de segurança para ajudá-lo neste objetivo: [recomendações de segurança para imagens do Azure Marketplace](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images).  Algumas dessas recomendações são específicas para imagens baseadas em Linux, mas a maioria se aplica a qualquer imagem de VM. 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>Executar configurações personalizadas e tarefas agendadas

Se for necessária configuração adicional, a abordagem recomendada é usar uma tarefa agendada que é executada na inicialização para fazer quaisquer alterações finais na VM após sua implantação.  Considere também as seguintes recomendações:
- Se for uma tarefa de execução única, é recomendável que a própria tarefa seja excluída após a conclusão bem-sucedida.
- As configurações não devem depender de unidades diferentes de C ou D, pois apenas essas duas unidades têm a garantia de que existem sempre. A unidade C é o disco do sistema operacional e a unidade D é o disco local temporário.

Para obter mais informações sobre personalizações do Linux, consulte [recursos e extensões de máquina virtual para Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).


## <a name="generalize-the-image"></a>Generalizar a imagem

Todas as imagens no Azure Marketplace devem ser reutilizáveis de maneira genérica. Para atingir essa reusabilidade, o VHD do sistema operacional deve ser *generalizado*, uma operação que remove todos os identificadores específicos da instância e drivers de software de uma VM.

### <a name="windows"></a>Windows

Os discos do sistema operacional Windows são generalizados com a [ferramenta Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Se, posteriormente, você atualizar ou reconfigurar o sistema operacional, deverá executar novamente o Sysprep. 

> [!WARNING]
>  Como as atualizações podem ser executadas automaticamente, depois de executar o Sysprep, você deve desativar a VM até que ela seja implantada.  Esse desligamento evitará que as atualizações subsequentes façam alterações específicas da instância no sistema operacional VHD ou nos serviços instalados.

Para obter mais informações sobre como executar o Sysprep, consulte [etapas para generalizar um VHD](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)

### <a name="linux"></a>Linux

O processo de duas etapas a seguir generaliza uma VM Linux e a implanta novamente como uma VM separada. Essas duas etapas são apenas os conceitos básicos do processo. Para obter mais informações sobre essas duas etapas e por que elas devem ser feitas, consulte [como criar uma imagem de uma máquina virtual ou VHD](../../../virtual-machines/linux/capture-image.md). Para fins de criação do VHD para sua oferta do Azure Marketplace, você pode parar quando chegar à seção "criar uma VM com base na imagem capturada".

#### <a name="remove-the-azure-linux-agent"></a>Remover o agente Linux do Azure
1.  Conecte-se à sua VM do Linux usando um cliente SSH.
2.  Na janela SSH, digite o seguinte comando: <br/>
    `sudo waagent -deprovision+user`
3.  Digite `y` para continuar. (Você pode adicionar o parâmetro `-force` ao comando anterior evitar esta etapa de confirmação.)
4.  Após a conclusão do comando, digite `exit` para fechar o cliente SSH.

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>Capturar a imagem
1.  Vá para o portal do Azure, selecione seu grupo de recursos (RG) e desaloque a VM.
2.  Seu VHD está generalizado agora e você pode criar uma nova VM usando este VHD.


## <a name="create-one-or-more-copies"></a>Criar uma ou mais cópias

A criação de cópias da VM é geralmente útil para backup, teste, failover personalizado ou balanceamento de carga, para oferecer configurações diferentes de uma solução e assim por diante. Para obter informações sobre como duplicar e baixar um VHD primário, para fazer um clone não gerenciado, consulte:

- VM do Linux: [baixar um VHD do Linux do Azure](../../../virtual-machines/linux/download-vhd.md)
- VM do Windows: [baixar um VHD do Windows do Azure](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>Passos seguintes

Depois que a VM for generalizada, tiver sido desalocada e você tiver criado uma imagem da VM, você estará pronto para [implantar uma máquina virtual de um disco rígido virtual](./cpp-deploy-vm-vhd.md).
