---
title: Configurar a GPU para visualização de área de trabalho virtuais Windows - Azure
description: Como ativar acelerado de GPU renderização e codificação em pré-visualização de ambiente de Trabalho Virtual do Windows.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: a6a67c89253a1b16f9266d7917655d1b1104022e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65159574"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop-preview"></a>Configurar a aceleração da (GPU unidade) para a pré-visualização de ambiente de Trabalho Virtual do Windows de processamento gráfico

Windows Virtual Desktop Preview suporta acelerado de GPU renderização e codificação para escalabilidade e desempenho de aplicações melhorada. Aceleração por GPU é particularmente crucial para aplicações de grande intensidade de gráficos.

Siga as instruções neste artigo para criar uma máquina virtual do Azure com otimização de GPU, adicioná-lo ao agrupamento de anfitrião e configurá-lo para utilizar a aceleração por GPU de renderização e codificação. Este artigo pressupõe que já tem um inquilino de área de Trabalho Virtual do Windows configurado.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Selecione um tamanho de máquina virtual do Azure com otimização de GPU

O Azure oferece diversas [tamanhos de máquinas virtuais com otimização de GPU](/azure/virtual-machines/windows/sizes-gpu). A escolha certa para o seu conjunto de anfitrião depende de vários fatores, incluindo a cargas de trabalho de aplicação específica, qualidade pretendida de experiência do usuário e os custos. Em geral, maiores e mais capacitadas GPUs oferecem uma melhor experiência de usuário numa densidade de determinado utilizador.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Criar um conjunto de anfitrião, aprovisionar a máquina virtual e configurar um grupo de aplicações

Crie um novo conjunto de anfitrião com uma VM do tamanho que selecionou. Para obter instruções, consulte [Tutorial: Criar um conjunto de anfitriões com o Azure Marketplace](/azure/virtual-desktop/create-host-pools-azure-marketplace).

Windows Virtual Desktop Preview suporta acelerado de GPU renderização e codificação nos seguintes sistemas operativos:

* Windows 10 versão 1511 ou mais recente
* Windows Server 2016 ou mais recente

Também deve configurar um grupo de aplicação ou o grupo de aplicativo de desktop do padrão (com o nome "Grupo de aplicações de ambiente de trabalho") é criado automaticamente quando cria um novo conjunto de anfitrião. Para obter instruções, consulte [Tutorial: Gerir grupos de aplicações para Windows Virtual Desktop Preview](/azure/virtual-desktop/manage-app-groups).

>[!NOTE]
>Windows Virtual Desktop Preview só suporta o tipo de grupo de aplicação "Desktop" para os conjuntos de anfitrião ativadas para GPU. Grupos de aplicações do tipo "RemoteApp" não são suportados para conjuntos de anfitrião ativadas para GPU.

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Instalar controladores de gráfico com suporte na sua máquina virtual

Para tirar partido das capacidades de GPU de VMs de série N do Azure em pré-visualização de ambiente de Trabalho Virtual do Windows, tem de instalar drivers de gráficos da NVIDIA. Siga as instruções em [controladores de GPU de NVIDIA instalar em VMs de série N que executam o Windows](/azure/virtual-machines/windows/n-series-driver-setup) para instalar controladores, seja manualmente ou utilizando o [extensão de controladores de GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows).

Tenha em atenção que apenas [controladores de GRID da NVIDIA](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) distribuídos pelo Azure são suportados para a Windows Virtual Desktop Preview.

Após a instalação de driver, é necessário um reinício VM. Utilize os passos de verificação nas instruções acima para confirmar que os drivers gráficos foram instalados com êxito.

## <a name="configure-gpu-accelerated-app-rendering"></a>Configurar o processamento de aplicação acelerado de GPU

Por predefinição, aplicações e áreas de trabalho em execução em configurações de sessão multi são processadas com a CPU e não aproveitam as GPUs disponíveis para a composição. Configure a política de grupo para o anfitrião de sessões ativar o processamento acelerado de GPU:

1. Ligue-se na área de trabalho da VM com uma conta com privilégios de administrador local.
2. Abra o início menu e escreva "gpedit. msc" para abrir o Editor de diretiva de grupo.
3. Navegue pela árvore para **configuração do computador** > **modelos administrativos** > **componentes do Windows**  >   **Os serviços de ambiente de trabalho remoto** > **anfitrião de sessões de ambiente de trabalho remoto** > **sessão remota ambiente**.
4. Selecione a política **utilizar o adaptador de gráficos do hardware padrão para todas as sessões de serviços de ambiente de trabalho remoto** e defina esta política como **ativado** para ativar a renderização de GPU na sessão remota.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Configurar a codificação de quadro acelerado de GPU

Ambiente de trabalho remoto codifica todos os elementos gráficos processados pelas aplicações e áreas de trabalho (seja processado com GPU ou com CPU) para transmissão aos clientes de ambiente de trabalho remoto. Por predefinição, o ambiente de trabalho remoto não aproveita GPUs disponíveis para essa codificação. Configure a política de grupo para o anfitrião de sessões ativar a codificação de quadro acelerado de GPU. Continuar os passos acima:

1. Selecione a política **modo de Priorizar o H.264/AVC 444 gráficos para ligações de ambiente de trabalho remoto** e defina esta política como **ativado** para forçar o codec de H.264/AVC 444 na sessão remota.
2. Selecione a política **H.264/AVC configurar hardware codificação para ligações de ambiente de trabalho remoto** e defina esta política como **ativado** para ativar a codificação de hardware para AVC/H.264 na sessão remota.

    >[!NOTE]
    >No Windows Server 2016, defina a opção **preferir a codificação de Hardware de AVC** ao **sempre tentativa**.

3. Agora que as políticas de grupo tem sido editadas, força uma atualização de política de grupo. Abra a linha de comandos e escreva:

    ```batch
    gpupdate.exe /force
    ```

4. Termine a sessão de ambiente de trabalho remoto.

## <a name="verify-gpu-accelerated-app-rendering"></a>Verificar a composição de aplicação acelerado de GPU

Para verificar que aplicações estão a utilizar a GPU para processamento, experimente uma das seguintes:

* Utilize o `nvidia-smi` utilitário conforme descrito na [verificar a instalação de driver](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) para verificar a utilização de GPU ao executar as suas aplicações.
* Em versões de sistema operativo suportado, pode utilizar o Gestor de tarefas para verificar a existência de utilização da GPU. Selecione a GPU no separador "Desempenho" para ver se aplicações são a utilização de GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Certifique-se de que a codificação de quadro acelerado de GPU

Para verificar que o ambiente de trabalho remoto está usando a codificação acelerada por GPU:

1. Ligue-se na área de trabalho da VM com o cliente de área de Trabalho Virtual do Windows.
2. Inicie o Visualizador de eventos e navegue até o nó seguinte: **Registos de serviços e aplicativos** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreTS**  >  **Operacional**
3. Para determinar se acelerado de GPU de codificação, procure o evento ID 170. Se vir "codificador de hardware AVC ativada: 1", em seguida, codificação de GPU é utilizada.
4. Para determinar se o modo de AVC 444 é usado, procure o evento ID 162. Se vir "AVC disponível: 1 perfil inicial: 2048", em seguida, AVC 444 é utilizado.

## <a name="next-steps"></a>Passos Seguintes

Estas instruções devem ter em execução com aceleração por GPU num host de sessão única VM. Algumas considerações adicionais para ativar a aceleração por GPU num conjunto maior de anfitrião:

* Considere utilizar o [extensão de controladores de GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) para simplificar a instalação de controladores e atualizações num número de VMs.
* Considere utilizar a política de grupo do Active Directory para simplificar a configuração de política de grupo num número de VMs. Para obter informações sobre como implementar a política de grupo no domínio do Active Directory, consulte [trabalhando com objetos de política de grupo](https://go.microsoft.com/fwlink/p/?LinkId=620889).
