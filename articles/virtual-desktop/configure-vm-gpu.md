---
title: Configure GPU para Windows Virtual Desktop - Azure
description: Como permitir a renderização acelerada pela GPU e codificação no Windows Virtual Desktop.
author: gundarev
ms.topic: how-to
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: f95b9c1615cc58d9cc0589bad98c7315e571686e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709468"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>Configurar a aceleração da unidade de processamento gráfico (GPU) para o Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/configure-vm-gpu-2019.md).

O Windows Virtual Desktop suporta renderização acelerada por GPU e codificação para melhorar o desempenho e escalabilidade da aplicação. A aceleração da GPU é particularmente crucial para aplicações com gráficas intensivas.

Siga as instruções deste artigo para criar uma máquina virtual Azure otimizada da GPU, adicione-a à sua piscina de anfitrião e configuure-a para utilizar a aceleração gpu para renderização e codificação. Este artigo pressupõe que já tem um inquilino virtual do Windows configurado.

## <a name="select-an-appropriate-gpu-optimized-azure-virtual-machine-size"></a>Selecione um tamanho de máquina virtual Azure otimizado da GPU

Selecione um dos [tamanhos VM](../virtual-machines/nv-series.md)da série NV da Azure, [série NVv3](../virtual-machines/nvv3-series.md)ou [NVv4.](../virtual-machines/nvv4-series.md) Estes são adaptados para virtualização de aplicativos e desktop e permitem que a maioria das aplicações e a interface de utilizador do Windows sejam aceleradas pela GPU. A escolha certa para o seu pool de anfitriões depende de uma série de fatores, incluindo as cargas de trabalho da sua aplicação particular, a qualidade desejada da experiência do utilizador e o custo. Em geral, as GPUs maiores e mais capazes oferecem uma melhor experiência de utilizador numa dada densidade de utilizador, enquanto tamanhos de GPU mais pequenos e fracionários permitem um controlo mais fino sobre o custo e a qualidade.

>[!NOTE]
>Os VMs da série NC, NCv2, NCv3, ND e NDv2 da Azure não são geralmente apropriados para os anfitriões de sessão virtual do Windows. Estes VMs são adaptados para ferramentas especializadas, de alto desempenho ou de machine learning, como as construídas com a NVIDIA CUDA. Não suportam a aceleração da GPU para a maioria das aplicações ou para a interface de utilizador do Windows.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Crie uma piscina de anfitriões, aprovisione a sua máquina virtual e configuure um grupo de aplicações

Crie uma nova piscina de anfitriões utilizando um VM do tamanho selecionado. Para obter instruções, consulte [Tutorial: Crie uma piscina de anfitriões com o portal Azure](./create-host-pools-azure-marketplace.md).

O Windows Virtual Desktop suporta a renderização e codificação aceleradas da GPU nos seguintes sistemas operativos:

* Versão 1511 do Windows 10 ou mais recente
* Windows Server 2016 ou mais recente

Também deve configurar um grupo de aplicações ou utilizar o grupo de aplicações padrão de desktop (chamado "Desktop Application Group") que é automaticamente criado quando cria um novo pool de anfitriões. Para obter instruções, consulte [Tutorial: Gerir grupos de aplicações para o Windows Virtual Desktop](./manage-app-groups.md).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Instale controladores gráficos suportados na sua máquina virtual

Para tirar partido das capacidades da GPU dos VMs da série Azure N no Windows Virtual Desktop, tem de instalar os controladores gráficos apropriados. Siga as instruções nos [sistemas operativos suportados e nos controladores](../virtual-machines/sizes-gpu.md#supported-operating-systems-and-drivers) para instalar os controladores. Apenas os condutores distribuídos pela Azure são apoiados.

* Para VMs da série Azure NV ou NVv3, apenas controladores NVIDIA GRID, e não os controladores NVIDIA CUDA, suportam a aceleração da GPU para a maioria das aplicações e a interface de utilizador do Windows. Se optar por instalar os controladores manualmente, certifique-se de que instala os controladores GRID. Se optar por instalar controladores utilizando a extensão Azure VM, os controladores GRID serão automaticamente instalados para estes tamanhos VM.
* Para VMs da série Azure NVv4, instale os controladores AMD fornecidos pela Azure. Pode instalá-los automaticamente utilizando a extensão Azure VM, ou pode instalá-los manualmente.

Após a instalação do condutor, é necessário reiniciar um VM. Utilize as etapas de verificação nas instruções acima para confirmar que os controladores gráficos foram instalados com sucesso.

## <a name="configure-gpu-accelerated-app-rendering"></a>Configurar a renderização de aplicativos acelerados pela GPU

Por predefinição, as aplicações e os desktops em execução em configurações multi-sessão são renderizados com o CPU e não alavancam as GPUs disponíveis para renderização. Configure a política de grupo para o anfitrião da sessão para permitir a renderização acelerada pela GPU:

1. Ligue-se ao ambiente de trabalho do VM utilizando uma conta com privilégios de administrador local.
2. Abra o menu Iniciar e escreva "gpedit.msc" para abrir o Editor de Política do Grupo.
3. Navegue na árvore para **configurar o computador**  >  **Modelos administrativos**  >  **Windows Componentes**  >  **Remotos desktop Services** Remote Desktop Session  >  **Host** Remote  >  **Session Environment**.
4. Selecione os **adaptadores gráficos de hardware para todas as sessões de Serviços de Ambiente de Trabalho Remoto** e desementa esta política para **ativar** a renderização de GPU na sessão remota.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Codificação de quadros acelerados pela GPU

O Ambiente de Trabalho Remoto codifica todos os gráficos renderizados por apps e ambientes de trabalho (seja renderizado com GPU ou com CPU) para transmissão a clientes de Ambiente de Trabalho Remoto. Quando uma parte do ecrã é frequentemente atualizada, esta parte do ecrã é codificada com um código de vídeo (H.264/AVC). Por predefinição, o Remote Desktop não aproveita as GPUs disponíveis para esta codificação. Configure a política de grupo para o anfitrião da sessão para permitir a codificação de quadros acelerados pela GPU. Continuando os passos acima:

>[!NOTE]
>A codificação de quadros acelerados pela GPU não está disponível em VMs da série NVv4.

1. Selecione a codificação de **hardware Configure H.264/AVC para ligações remotas de ambiente de trabalho** e descongessione esta política para **Ativar** a codificação de hardware para AVC/H.264 na sessão remota.

    >[!NOTE]
    >No Windows Server 2016, opção de definição **Prefere hardware AVC Encoding** to **Always try**.

2. Agora que as políticas de grupo foram editadas, forcem uma atualização da política de grupo. Abra a solicitação de comando e escreva:

    ```cmd
    gpupdate.exe /force
    ```

3. Inscreva-se na sessão de desktop remoto.

## <a name="configure-fullscreen-video-encoding"></a>Configurar a codificação de vídeo completo

Se utilizar frequentemente aplicações que produzam um conteúdo de alta taxa de fotogramas, como modelação 3D, cad/CAM e aplicações de vídeo, pode optar por ativar uma codificação de vídeo completa para uma sessão remota. O perfil de vídeo fullscreen proporciona uma maior taxa de fotogramas e uma melhor experiência do utilizador para tais aplicações em detrimento da largura de banda da rede e tanto dos recursos do anfitrião da sessão como dos recursos dos clientes. Recomenda-se a utilização de molduras aceleradas pela GPU para codificação de vídeo de ecrã inteiro. Configure a Política de Grupo para o anfitrião da sessão para permitir a codificação de vídeo sonoro. Continuando os passos acima:

1. Selecione a política **Priorização H.264/AVC 444 O modo gráfico para ligações de ambiente de trabalho remoto** e define esta política para **Ativar** o código H.264/AVC 444 na sessão remota.
2. Agora que as políticas de grupo foram editadas, forcem uma atualização da política de grupo. Abra a solicitação de comando e escreva:

    ```cmd
    gpupdate.exe /force
    ```

3. Inscreva-se na sessão de desktop remoto.
## <a name="verify-gpu-accelerated-app-rendering"></a>Verifique a renderização de aplicativos acelerados pela GPU

Para verificar se as aplicações estão a utilizar a GPU para renderização, experimente qualquer uma das seguintes:

* Para VMs Azure com um GPU NVIDIA, utilize o `nvidia-smi` utilitário como descrito na [instalação do controlador verificar](../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation) para verificar se há utilização de GPU ao executar as suas aplicações.
* Nas versões suportadas do sistema operativo, pode utilizar o Gestor de Tarefas para verificar a utilização da GPU. Selecione a GPU no separador "Performance" para ver se as aplicações estão a utilizar a GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Verifique a codificação de quadros acelerados pela GPU

Para verificar se o Ambiente de Trabalho Remoto está a utilizar codificação acelerada pela GPU:

1. Ligue-se ao ambiente de trabalho do VM utilizando o cliente de ambiente de trabalho virtual do Windows.
2. Lançar o Observador de Eventos e navegar para o seguinte nó: **Aplicações e Serviços Regista**  >  **Microsoft**  >  **Windows**  >  **RemoteDesktopServices-RdpCoreCDV**  >  **Operacional**
3. Para determinar se a codificação acelerada da GPU é usada, procure o ID 170 do evento. Se vir "AVC hardware codificador: 1" então a codificação da GPU é utilizada.

## <a name="verify-fullscreen-video-encoding"></a>Verifique a codificação de vídeo sonoro

Para verificar se o Remote Desktop está a utilizar a codificação de vídeo sonoro:

1. Ligue-se ao ambiente de trabalho do VM utilizando o cliente de ambiente de trabalho virtual do Windows.
2. Lançar o Observador de Eventos e navegar para o seguinte nó: **Aplicações e Serviços Regista**  >  **Microsoft**  >  **Windows**  >  **RemoteDesktopServices-RdpCoreCDV**  >  **Operacional**
3. Para determinar se a codificação de vídeo de ecrã completo é utilizada, procure o ID 162 do evento. Se vir "AVC Disponível: 1 Perfil Inicial: 2048" então é utilizado AVC 444.

## <a name="next-steps"></a>Passos seguintes

Estas instruções devem tê-lo a funcionar com a aceleração da GPU num anfitrião de uma sessão (um VM). Algumas considerações adicionais para permitir a aceleração da GPU através de um pool de hospedeiro maior:

* Considere usar uma [extensão VM](../virtual-machines/extensions/overview.md) para simplificar a instalação do controlador e atualizações em vários VMs. Utilize a extensão do [condutor do GPU NVIDIA](../virtual-machines/extensions/hpccompute-gpu-windows.md) para VMs com GPUs NVIDIA e utilize a extensão do [condutor gpu amd](../virtual-machines/extensions/hpccompute-amd-gpu-windows.md) para VMs com GPUs AMD.
* Considere usar a Política de Grupo de Diretório Ativo para simplificar a configuração da política de grupo em vários VMs. Para obter informações sobre a implementação da Política de Grupo no domínio do Diretório Ativo, consulte [Trabalhar com objetos de política de grupo](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731212(v=ws.11)).