---
title: Configurar GPU para área de trabalho virtual do Windows-Azure
description: Como habilitar o processamento e a codificação com aceleração de GPU na área de trabalho virtual do Windows.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: a0965dc4011b449e617f6dbaeafb68bfa796b620
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953956"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>Configurar a aceleração da GPU (unidade de processamento gráfico) para a área de trabalho virtual do Windows

A área de trabalho virtual do Windows dá suporte à renderização e codificação aceleradas por GPU para melhorar o desempenho e a escalabilidade do aplicativo. A aceleração de GPU é particularmente crucial para aplicativos com uso intensivo de gráficos.

Siga as instruções neste artigo para criar uma máquina virtual do Azure otimizada para GPU, adicioná-la ao seu pool de hosts e configurá-la para usar a aceleração de GPU para renderização e codificação. Este artigo pressupõe que você já tenha um locatário de área de trabalho virtual do Windows configurado.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Selecione um tamanho de máquina virtual do Azure com otimização de GPU

O Azure oferece uma série de [tamanhos de máquina virtual otimizados para GPU](/azure/virtual-machines/windows/sizes-gpu). A escolha certa para seu pool de hosts depende de vários fatores, incluindo suas cargas de trabalho de aplicativo específicas, a qualidade desejada da experiência do usuário e o custo. Em geral, as GPUs maiores e mais compatíveis oferecem uma melhor experiência de usuário em uma determinada densidade de usuário.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Criar um pool de hosts, provisionar sua máquina virtual e configurar um grupo de aplicativos

Crie um novo pool de hosts usando uma VM do tamanho selecionado. Para obter instruções, consulte [tutorial: criar um pool de hosts com o Azure Marketplace](/azure/virtual-desktop/create-host-pools-azure-marketplace).

A área de trabalho virtual do Windows dá suporte à renderização e codificação aceleradas por GPU nos seguintes sistemas operacionais:

* Windows 10 versão 1511 ou mais recente
* Windows Server 2016 ou mais recente

Você também deve configurar um grupo de aplicativos ou usar o grupo de aplicativos de área de trabalho padrão (chamado "grupo de aplicativos de área de trabalho") que é criado automaticamente quando você cria um novo pool de hosts. Para obter instruções, consulte [tutorial: gerenciar grupos de aplicativos para a área de trabalho virtual do Windows](/azure/virtual-desktop/manage-app-groups).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Instalar os drivers gráficos com suporte em sua máquina virtual

Para aproveitar os recursos de GPU das VMs da série N do Azure na área de trabalho virtual do Windows, você deve instalar os drivers gráficos NVIDIA. Siga as instruções em [instalar drivers NVIDIA GPU em VMs da série N executando o Windows](/azure/virtual-machines/windows/n-series-driver-setup) para instalar drivers manualmente ou usando a [extensão de Driver Nvidia GPU](/azure/virtual-machines/extensions/hpccompute-gpu-windows).

Observe que somente os [Drivers NVIDIA Grid](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) distribuídos pelo Azure têm suporte na área de trabalho virtual do Windows.

Após a instalação do driver, é necessária uma reinicialização da VM. Use as etapas de verificação nas instruções acima para confirmar se os drivers gráficos foram instalados com êxito.

## <a name="configure-gpu-accelerated-app-rendering"></a>Configurar renderização de aplicativo acelerada por GPU

Por padrão, aplicativos e áreas de trabalho em execução em configurações de várias sessões são renderizados com a CPU e não aproveitam as GPUs disponíveis para renderização. Configure Política de Grupo para o host de sessão para habilitar a renderização acelerada por GPU:

1. Conecte-se à área de trabalho da VM usando uma conta com privilégios de administrador local.
2. Abra o menu iniciar e digite "gpedit. msc" para abrir o editor de Política de Grupo.
3. Navegue pela árvore para **a configuração do computador** > **modelos administrativos** > **componentes do Windows** > **serviços de área de trabalho remota** > **host da sessão da área de trabalho remota** > **ambiente de sessão remota**.
4. Selecione política **use o adaptador gráfico padrão de hardware para todas as sessões de serviços de área de trabalho remota** e defina essa política como **habilitada** para habilitar a renderização de GPU na sessão remota.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Configurar a codificação de quadro acelerado por GPU

Área de Trabalho Remota codifica todos os gráficos renderizados por aplicativos e áreas de trabalho (sejam renderizados com GPU ou com CPU) para transmissão para clientes Área de Trabalho Remota. Por padrão, Área de Trabalho Remota não aproveita as GPUs disponíveis para essa codificação. Configure Política de Grupo para o host de sessão para habilitar a codificação de quadro acelerado por GPU. Continuando as etapas acima:

1. Selecione política **priorizar o modo gráfico H. 264/AVC 444 para conexões área de trabalho remota** e defina essa política como **habilitada** para forçar o codec H. 264/AVC 444 na sessão remota.
2. Selecione política **Configurar codificação de hardware H. 264/AVC para conexões área de trabalho remota** e defina essa política como **habilitada** para habilitar a codificação de hardware para AVC/H. 264 na sessão remota.

    >[!NOTE]
    >No Windows Server 2016, a opção set **preferir codificação de hardware AVC** para **sempre tentar**.

3. Agora que as políticas de grupo foram editadas, Force uma atualização da política de grupo. Abra o prompt de comando e digite:

    ```batch
    gpupdate.exe /force
    ```

4. Saia da sessão de Área de Trabalho Remota.

## <a name="verify-gpu-accelerated-app-rendering"></a>Verificar renderização de aplicativo acelerada por GPU

Para verificar se os aplicativos estão usando a GPU para renderização, tente qualquer um dos seguintes:

* Use o utilitário `nvidia-smi` conforme descrito em [verificar a instalação do driver](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) para verificar a utilização da GPU ao executar seus aplicativos.
* Em versões de sistema operacional com suporte, você pode usar o Gerenciador de tarefas para verificar a utilização da GPU. Selecione a GPU na guia "desempenho" para ver se os aplicativos estão utilizando a GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Verificar a codificação de quadro acelerada por GPU

Para verificar se Área de Trabalho Remota está usando a codificação acelerada por GPU:

1. Conecte-se à área de trabalho da VM usando o cliente de área de trabalho virtual do Windows.
2. Inicie o Visualizador de Eventos e navegue até o seguinte nó: **logs de aplicativos e serviços** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreTS** > **operacional**
3. Para determinar se a codificação acelerada por GPU é usada, procure a ID de evento 170. Se você vir "codificador de hardware AVC habilitado: 1", a codificação de GPU será usada.
4. Para determinar se o modo AVC 444 é usado, procure a ID do evento 162. Se você vir "AVC disponível: 1 perfil inicial: 2048", será usado o AVC 444.

## <a name="next-steps"></a>Passos seguintes

Essas instruções devem estar em funcionamento com a aceleração de GPU em uma única VM host de sessão. Algumas considerações adicionais para habilitar a aceleração de GPU em um pool de hosts maior:

* Considere usar a [extensão de Driver Nvidia GPU](/azure/virtual-machines/extensions/hpccompute-gpu-windows) para simplificar a instalação e as atualizações de driver em várias VMS.
* Considere o uso de Active Directory Política de Grupo para simplificar a configuração da diretiva de grupo em várias VMs. Para obter informações sobre como implantar Política de Grupo no domínio Active Directory, consulte [trabalhando com objetos política de grupo](https://go.microsoft.com/fwlink/p/?LinkId=620889).
