---
title: Configure GPU para Windows Virtual Desktop - Azure
description: Como permitir a renderização e codificação aceleradas pela GPU no Windows Virtual Desktop.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: 99393ed518df590140f79933623a9f7ec96edc85
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402280"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>Configure a aceleração da unidade de processamento de gráficos (GPU) para o Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização da primavera de 2020 com os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows. Se estiver a utilizar o lançamento do Windows Virtual Desktop Fall 2019 sem objetos do Gestor de Recursos Azure, consulte [este artigo](./virtual-desktop-fall-2019/configure-vm-gpu-2019.md).
>
> A atualização Do Windows Virtual Desktop Spring 2020 encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-la para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

O Windows Virtual Desktop suporta a renderização e codificação aceleradas por GPU para melhorar o desempenho e a escalabilidade das aplicações. A aceleração da GPU é particularmente crucial para aplicações com grande intensidade gráfica.

Siga as instruções deste artigo para criar uma máquina virtual Azure otimizada por GPU, adicione-a à sua piscina hospedeira e configure-a para utilizar a aceleração de GPU para renderização e codificação. Este artigo assume que já tem um inquilino do Windows Virtual Desktop configurado.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Selecione uma GPU otimizada tamanho da máquina virtual Azure

O Azure oferece uma série de tamanhos de [máquinavirtual otimizada para a GPU.](/azure/virtual-machines/windows/sizes-gpu) A escolha certa para o seu pool de anfitriões depende de uma série de fatores, incluindo as suas cargas de trabalho específicas, a qualidade desejada da experiência do utilizador e o custo. Em geral, gpus maiores e mais capazes oferecem uma melhor experiência de utilizador numa determinada densidade de utilizador.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Crie uma piscina de anfitriões, forre a sua máquina virtual e configure um grupo de aplicações

Crie uma nova piscina de anfitriões utilizando um VM do tamanho que selecionou. Para obter instruções, consulte [Tutorial: Crie uma piscina de acolhimento com o portal Azure](/azure/virtual-desktop/create-host-pools-azure-marketplace).

O Windows Virtual Desktop suporta a renderização e codificação aceleradas por GPU nos seguintes sistemas operativos:

* Windows 10 versão 1511 ou mais recente
* Windows Server 2016 ou mais recente

Também deve configurar um grupo de aplicações ou utilizar o grupo de aplicações de ambiente de trabalho padrão (chamado "Desktop Application Group") que é criado automaticamente quando cria um novo pool de anfitriões. Para obter instruções, consulte [Tutorial: Gerencie os grupos de aplicações para o Windows Virtual Desktop](/azure/virtual-desktop/manage-app-groups).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Instale controladores gráficos suportados na sua máquina virtual

Para tirar partido das capacidades de GPU dos VMs da série N Azure no Windows Virtual Desktop, tem de instalar os controladores gráficos apropriados. Siga as instruções nos [sistemas operativos suportados e nos controladores](/azure/virtual-machines/windows/sizes-gpu#supported-operating-systems-and-drivers) para instalar os controladores do fornecedor gráfico apropriado, manualmente ou utilizando uma extensão Azure VM.

Apenas os condutores distribuídos pelo Azure são suportados para o Windows Virtual Desktop. Além disso, para VMs Azure com GPUs NVIDIA, apenas [os controladores NVIDIA GRID](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) são suportados para Windows Virtual Desktop.

Após a instalação do condutor, é necessário um reinício vm. Utilize as etapas de verificação nas instruções acima para confirmar que os condutores gráficos foram instalados com sucesso.

## <a name="configure-gpu-accelerated-app-rendering"></a>Configurar a renderização acelerada da aplicação GPU

Por padrão, aplicações e desktops em configurações multi-sessões são renderizados com o CPU e não alavancam GPUs disponíveis para renderização. Configure a Política de Grupo para o anfitrião da sessão para permitir a renderização acelerada pela GPU:

1. Ligue-se ao ambiente de trabalho do VM utilizando uma conta com privilégios de administrador local.
2. Abra o menu Iniciar e escreva "gpedit.msc" para abrir o Editor de Política do Grupo.
3. Navigate the tree to **Computer Configuration**  >  **Administrative Templates**  >  **Windows Components**  >  **Remote Desktop Services**  >  **Remote Desktop Session Host**  >  **Remote Session Environment**.
4. Selecione política **Utilize o adaptador de gráficos padrão de hardware para todas as sessões** de Serviços de Ambiente de Trabalho Remoto e detetete tea política para **ativar** a renderização de GPU na sessão remota.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Configure codificação de quadros acelerados por GPU

O Remote Desktop codifica todos os gráficos renderizados por apps e desktops (sejam renderizados com GPU ou com CPU) para transmissão para clientes do Remote Desktop. Por predefinição, o Remote Desktop não aproveita as GPUs disponíveis para esta codificação. Configure a Política de Grupo para o anfitrião da sessão para permitir a codificação de quadros acelerados por GPU. Continuando os passos acima:

1. Selecione política **Priorize o modo gráfico H.264/AVC 444 para ligações remotas** de ambiente de trabalho e defina esta política para **ativar** o código H.264/AVC 444 na sessão remota.
2. Selecione a definição de **hardware Configure H.264/AVC para ligações remotas** de ambiente de trabalho e defina esta política para **Ativar** para permitir a codificação de hardware para AVC/H.264 na sessão remota.

    >[!NOTE]
    >No Windows Server 2016, a opção definida Prefere a codificação de **hardware AVC** para **tentar sempre**.

3. Agora que as políticas do grupo foram editadas, force mato uma atualização da política do grupo. Abra o Pedido de Comando e escreva:

    ```batch
    gpupdate.exe /force
    ```

4. Inscreva-se na sessão de Ambiente de Trabalho Remoto.

## <a name="verify-gpu-accelerated-app-rendering"></a>Verifique a renderização de aplicações aceleradas pela GPU

Para verificar se as aplicações estão a utilizar a GPU para renderização, experimente qualquer uma das seguintes aplicações:

* Para Os VMs Azure com um GPU NVIDIA, utilize o `nvidia-smi` utilitário conforme descrito na [instalação do controlador Verifique](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) se há utilização de GPU ao executar as suas apps.
* Nas versões do sistema operativo suportado, pode utilizar o Gestor de Tarefas para verificar se há utilização de GPU. Selecione a GPU no separador "Performance" para ver se as aplicações estão a utilizar a GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Verifique a codificação de quadros acelerados pela GPU

Para verificar se o Ambiente de Trabalho Remoto está a utilizar codificação acelerada por GPU:

1. Ligue-se ao ambiente de trabalho do VM utilizando o cliente Windows Virtual Desktop.
2. Lance o Espectador de Eventos e navegue para o seguinte nó: **Aplicações e serviços Logs**  >  **Microsoft**  >  **Windows**  >  **RemoteDesktopServices-RdpCoreCDV**  >  **Operacional**
3. Para determinar se é utilizada codificação acelerada por GPU, procure o ID 170 do evento. Se vir "Codificador de hardware AVC ativado: 1" então a codificação de GPU é utilizada.
4. Para determinar se o modo AVC 444 é utilizado, procure o ID 162 do evento. Se vir "AVC Disponível: 1 Perfil Inicial: 2048" então é utilizado O Vc 444.

## <a name="next-steps"></a>Passos seguintes

Estas instruções devem fazê-lo funcionar com aceleração de GPU em um anfitrião de sessão (um VM). Algumas considerações adicionais para permitir a aceleração da GPU através de uma piscina de hospedeiro maior:

* Considere utilizar uma [extensão VM](/azure/virtual-machines/extensions/overview) para simplificar a instalação do condutor e atualizações em vários VMs. Utilize a extensão do [condutor GPU da NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) para VMs com GPUs NVIDIA e utilize a extensão do [condutor GPU da AMD](/azure/virtual-machines/extensions/hpccompute-amd-gpu-windows) para VMs com GPUs AMD.
* Considere usar a Política do Grupo De Diretório Ativo para simplificar a configuração da política de grupo em vários VMs. Para obter informações sobre a implementação da Política de Grupo no domínio do Diretório Ativo, consulte [Trabalhar com Objetos de Política de Grupo](https://go.microsoft.com/fwlink/p/?LinkId=620889).
