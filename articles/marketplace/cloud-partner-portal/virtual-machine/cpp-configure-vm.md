---
title: Configure o VM hospedado no Microsoft Azure para o Mercado Azure
description: Explica como dimensionar, atualizar e generalizar um VM hospedado no Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 4cb247a3e64f8d44cc64010dde40963f4e9a1993
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146102"
---
# <a name="configure-the-azure-hosted-vm"></a>Configure o VM hospedado em Azure

> [!IMPORTANT]
> A partir de 13 de abril de 2020, iniciaremos a gestão móvel das ofertas da sua Máquina Virtual Azure ao Partner Center. Após a migração, você vai criar e gerir suas ofertas no Partner Center. Siga as instruções em [Create your Azure Virtual Machine ativos técnicos](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) para gerir as suas ofertas migradas.

Este artigo explica como dimensionar, atualizar e generalizar uma máquina virtual (VM) hospedada no Azure.  Estes passos são necessários para preparar o seu VM para ser implantado a partir do Mercado Azure.

## <a name="sizing-the-vhds"></a>Dimensionamento dos VHDs

<!--TD: Check if the following assertion is true. I didn't understand the original content. -->
Se selecionou um dos VMs pré-configurado com um sistema operativo (e serviços opcionalmente adicionais), então já escolheu um tamanho De VM Azul padrão, como descrito no [separador SKUs da máquina virtual](./cpp-skus-tab.md).  Iniciar a sua solução com um Sistema operativo pré-configurado é a abordagem recomendada.  No entanto, se estiver a instalar um SO manualmente, então deve dimensionar o seu VHD primário na sua imagem VM:

- Para windows, o vHD do sistema operativo deve ser criado como um VHD de formato fixo de 127-128 GB. 
- Para o Linux, este VHD deve ser criado como um VHD de formato fixo de 30-50 GB.

Se o tamanho físico for inferior a 127-128 GB, o VHD deve ser escasso. As imagens base do Windows e do SQL Server fornecidas já satisfazem estes requisitos, pelo que não altera o formato ou o tamanho do VHD obtido. 

Os discos de dados podem ser tão grandes como 1 TB. Ao decidir sobre o seu tamanho, lembre-se que os clientes não podem redimensionar Os VHDs dentro de uma imagem no momento da implementação. Os VHDs do disco de dados devem ser criados como VHDs de formação fixa. Também devem ser escassos. Os discos de dados podem inicialmente estar vazios ou conter dados.


## <a name="install-the-most-current-updates"></a>Instale as atualizações mais atuais

As imagens base dos VMs do sistema operativo contêm as últimas atualizações até à data publicada. Antes de publicar o sistema operativo VHD que criou, certifique-se de que atualiza o SISTEMA e todos os serviços instalados com todos os patches de segurança e manutenção mais recentes.

Para o Windows Server 2016, execute o comando **'Verificar' Atualizações.**  Caso contrário, para versões mais antigas do Windows, veja [como obter uma atualização através do Windows Update](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update).  A atualização do Windows irá instalar automaticamente as mais recentes atualizações de segurança críticas e importantes.

Para as distribuições do Linux, as atualizações são geralmente descarregadas e instaladas através de uma ferramenta de linha de comando ou de um utilitário gráfico.  Por exemplo, o Ubuntu Linux fornece o comando [de get apt e](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) a ferramenta Update [Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) para atualizar o OS.


## <a name="perform-additional-security-checks"></a>Realizar verificações de segurança adicionais

Deverá manter um alto nível de segurança para as suas imagens de solução no Mercado Azure.  O seguinte artigo fornece uma lista de configurações e procedimentos de segurança para ajudá-lo neste objetivo: Recomendações de [segurança para imagens do Mercado Azure](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images).  Algumas destas recomendações são específicas das imagens baseadas em Linux, mas a maioria aplica-se a qualquer imagem VM. 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>Execute a configuração personalizada e as tarefas programadas

Se for necessária uma configuração adicional, a abordagem recomendada é utilizar uma tarefa programada que funciona no arranque para fazer quaisquer alterações finais no VM depois de implementado.  Considere também as seguintes recomendações:
- Se for uma tarefa executada, recomenda-se que a tarefa se elimine depois de concluída com sucesso.
- As configurações não devem depender de unidades que não sejam C ou D, porque apenas estas duas unidades que estão sempre garantidas para existir. A unidade C é o disco do sistema operativo, e a unidade D é o disco local temporário.

Para obter mais informações sobre as personalizações do Linux, consulte [extensões e funcionalidades da máquina virtual para linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).


## <a name="generalize-the-image"></a>Generalizar a imagem

Todas as imagens no Mercado Azure devem ser reutilizáveis de forma genérica. Para alcançar esta reutilização, o sistema operativo VHD deve ser *generalizado,* uma operação que remova todos os identificadores específicos de instâncias e controladores de software de um VM.

### <a name="windows"></a>Windows

Os discos Do Windows OS são generalizados com a [ferramenta de sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Se posteriormente atualizar ou reconfigurar o Sistema operativo, tem de reexecutar a sysprep. 

> [!WARNING]
>  Uma vez que as atualizações podem ser executadas automaticamente, uma vez executada sysprep, deve desligar o VM até que seja implantado.  Esta paragem evitará atualizações subsequentes de fazer alterações específicas de exemplo sao o VHD OS ou serviços instalados.

Para mais informações sobre a execução de sysprep, consulte [Steps para generalizar um VHD](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)

### <a name="linux"></a>Linux

O processo de duas etapas seguintegeneraliza um VM Linux e reimplanta-o como um VM separado. Estes dois passos são apenas o essencial do processo. Para obter mais informações sobre estes dois passos e por que devem ser feitos, consulte [como criar uma imagem de uma máquina virtual ou VHD](../../../virtual-machines/linux/capture-image.md). Para efeitos de criação do VHD para a sua oferta azure Marketplace, pode parar quando chegar à secção "Criar um VM a partir da imagem capturada".

#### <a name="remove-the-azure-linux-agent"></a>Remova o agente Azure Linux
1.  Ligue-se ao seu VM Linux utilizando um cliente SSH.
2.  Na janela SSH, digite o seguinte comando: <br/>
    `sudo waagent -deprovision+user`
3.  Escreva `y` para continuar. (Pode adicionar `-force` o parâmetro ao comando anterior evitar este passo de confirmação.)
4.  Depois do comando terminar, escreva `exit` para fechar o cliente SSH.

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>Capturar a Imagem
1.  Vá ao portal Azure, selecione o seu grupo de recursos (RG) e desloque o VM.
2.  O seu VHD está generalizado agora e pode criar um novo VM utilizando este VHD.


## <a name="create-one-or-more-copies"></a>Criar uma ou mais cópias

A criação de cópias de VM é muitas vezes útil para backup, teste, falha personalizada ou equilíbrio de carga, para oferecer diferentes configurações de uma solução, e assim por diante. Para obter informações sobre como duplicar e descarregar um VHD primário, para fazer um clone não gerido, consulte:

- Linux VM: [Descarregue um Linux VHD do Azure](../../../virtual-machines/linux/download-vhd.md)
- Windows VM: [Descarregue um VHD windows a partir do Azure](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>Passos seguintes

Depois do seu VM ser generalizado, foi deallocalizado, e você criou uma imagem do VM, você está pronto para [implantar uma máquina virtual a partir de um disco rígido virtual](./cpp-deploy-vm-vhd.md).
