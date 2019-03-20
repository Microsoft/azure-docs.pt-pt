---
title: Configurar a VM de alojado no Azure da Microsoft para o Azure Marketplace | Documentos da Microsoft
description: Explica como dimensionar, atualizar e generalizar uma VM alojada no Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 9cf363bc5f4230306c2fec99eb6287b23e598a4c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57833506"
---
# <a name="configure-the-azure-hosted-vm"></a>Configurar a VM alojado no Azure

Este artigo explica como dimensionar, atualizar e generalizar uma máquina virtual (VM) alojada no Azure.  Estes passos são necessários para preparar a sua VM para ser implementadas a partir do Azure Marketplace.


## <a name="sizing-the-vhds"></a>Os VHDs de dimensionamento

<!--TD: Check if the following assertion is true. I didn't understand the original content. -->
Se tiver selecionado uma das VMs pré-configuradas com um sistema operativo (e, opcionalmente, mais serviços), então já ter escolhido um tamanho de VM do Azure standard, conforme descrito em [separador de SKUs de Máquina Virtual](./cpp-skus-tab.md).  A partir da sua solução de um sistema operacional previamente configurado é a abordagem recomendada.  No entanto, se estiver a instalar um sistema operacional manualmente, tem tamanho do VHD principal na imagem de VM:

- Para Windows, o sistema operativo VHD deverá ser criado como um 127-128 GB VHD de formato fixo. 
- Para o Linux, este VHD deverá ser criado como um 30 a 50 GB VHD de formato fixo.

Se o tamanho físico for inferior a 127 128 GB, o VHD deverá ser disperso. As imagens do Windows e o SQL Server bases, fornecidas já cumprem estes requisitos, portanto, não altere o formato ou o tamanho do VHD obtido. 

Discos de dados podem ter até 1 TB. Quando decidir sobre seu tamanho, lembre-se de que os clientes não podem redimensionar os VHDs de uma imagem no momento da implantação. Os VHDs de discos de dados devem ser criados como VHDs de formato fixo. Também deverá ser dispersos. Discos de dados inicialmente podem estar vazio ou conter dados.


## <a name="install-the-most-current-updates"></a>Instalar as atualizações mais recentes

As imagens base do sistema operativo VMs contêm as atualizações mais recentes até à data da publicação. Antes de publicar o sistema de operativo VHD que criou, certifique-se que Atualize o sistema operacional e todos os serviços instalados com todos os patches mais recentes de segurança e manutenção.

Para o Windows Server 2016, execute o **procurar atualizações** comando.  Caso contrário, para versões mais antigas do Windows, consulte [como obter uma atualização através do Windows Update](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update).  Atualização do Windows irá instalar automaticamente as atualizações de segurança mais recente críticos e importantes.

Para as distribuições de Linux, atualizações normalmente são transferidas e instaladas por meio de uma ferramenta de linha de comando ou um utilitário gráfico.  Por exemplo, Ubuntu Linux fornece os [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) comando e o [Gestor de atualizações de](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) ferramenta para atualizar o SO.


## <a name="perform-additional-security-checks"></a>Executar verificações de segurança adicionais

Deve manter um elevado nível de segurança para as imagens de solução no Azure Marketplace.  O artigo seguinte disponibiliza uma lista de verificação das configurações de segurança e os procedimentos para ajudá-lo neste objetivo: [Recomendações de segurança para imagens do Azure Marketplace](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images).  Algumas destas recomendações são específicas para imagens baseadas em Linux, mas aplicam-se a qualquer imagem de VM. 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>Executar a configuração personalizada e tarefas agendadas

Se é necessária qualquer configuração adicional, a abordagem recomendada é usar uma tarefa agendada que seja executada no arranque para realizar eventuais alterações finais na VM depois de este ter sido implementado.  Além disso, considere as seguintes recomendações:
- Se se trata de uma tarefa de execução única, recomenda-se que a tarefa eliminar automaticamente depois de terminar com êxito.
- Configurações não devem confiar em unidades que não seja C ou D, uma vez que apenas essas duas unidades que são existência garantida. Unidade C é o disco do sistema operativo e a unidade D é o disco local temporário.

Para obter mais informações sobre as personalizações do Linux, consulte [extensões de Máquina Virtual e funcionalidades para Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).


## <a name="generalize-the-image"></a>Generalizar a imagem

Todas as imagens no Azure Marketplace tem de ser reutilizáveis de um modo geral. Para obter esta capacidade de reutilização, o sistema operativo VHD tem de ser *generalizada*, uma operação que remove todos os identificadores de específico da instância e drivers de software a partir de uma VM.

### <a name="windows"></a>Windows

Discos de SO do Windows são generalizados com o [ferramenta sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Se, posteriormente, atualizar ou reconfigurar o sistema operacional, tem de voltar a executar o sysprep. 

> [!WARNING]
>  Uma vez que as atualizações possam ser executadas automaticamente, assim que execute o sysprep, deve para desativar a VM até ser implantado.  Este encerramento evitará atualizações subsequentes de fazer alterações de instância específica para o VHD do SO ou serviços instalados.

Para obter mais informações sobre a execução de sysprep, consulte [passos para generalizar um VHD](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#steps-to-generalize-a-vhd)

### <a name="linux"></a>Linux

Seguir o processo de dois passos generalizar uma VM do Linux e reimplementá-la como uma VM separada.  Para obter mais informações, consulte [como criar uma imagem de uma máquina virtual ou VHD](../../../virtual-machines/linux/capture-image.md). 

#### <a name="remove-the-azure-linux-agent"></a>Remover o agente Linux do Azure
1.  Ligar à sua VM do Linux com um cliente SSH.
2.  Na janela do SSH, escreva o seguinte comando: <br/>
    `sudo waagent -deprovision+user`
3.  Tipo de `y` para continuar. (Pode adicionar o `-force` parâmetro para o comando anterior evitar esta etapa de confirmação.)
4.  Após a conclusão do comando, digite `exit` para fechar o cliente SSH.

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>Capturar a imagem
1.  Aceda ao portal do Azure, selecione o grupo de recursos (RG) e anula a alocação da VM.
2.  O VHD é generalizado agora e pode criar uma nova VM com este VHD.


## <a name="create-one-or-more-copies"></a>Criar cópias de um ou mais

Criação de cópias de VM, muitas vezes, é útil para cópia de segurança, teste, personalizada para a ativação pós-falha ou balanceamento de carga, por oferecer configurações diferentes de uma solução e assim por diante. Para obter informações sobre como duplicar e transferir um VHD principal, para tornar um clone não gerido, consulte:

- VM do Linux: [Transferir um VHD do Linux do Azure](../../../virtual-machines/linux/download-vhd.md)
- VM do Windows: [Transferir um VHD do Windows do Azure](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>Passos Seguintes

Após a configuração da sua VM, está pronto para [implementar uma máquina virtual a partir de um disco rígido virtual](./cpp-deploy-vm-vhd.md).
