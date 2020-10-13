---
title: Configure GPU para Windows Virtual Desktop (clássico) - Azure
description: Como permitir a renderização acelerada pela GPU e codificação no Windows Virtual Desktop (clássico).
author: gundarev
ms.topic: how-to
ms.date: 03/30/2020
ms.author: denisgun
ms.openlocfilehash: 32d5c280e80b2f21b30bb34a182070da51e21026
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008496"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop-classic"></a>Configure a aceleração da unidade de processamento de gráficos (GPU) para o Windows Virtual Desktop (clássico)

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows. Se estiver a tentar gerir os objetos de ambiente de trabalho virtuais do Azure Resource Manager Windows, consulte [este artigo](../configure-vm-gpu.md).

O Windows Virtual Desktop suporta renderização acelerada por GPU e codificação para melhorar o desempenho e escalabilidade da aplicação. A aceleração da GPU é particularmente crucial para aplicações com gráficas intensivas.

Siga as instruções deste artigo para criar uma máquina virtual Azure otimizada da GPU, adicione-a à sua piscina de anfitrião e configuure-a para utilizar a aceleração gpu para renderização e codificação. Este artigo pressupõe que já tem um inquilino virtual do Windows configurado.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Selecione um GPU otimizado tamanho da máquina virtual Azure

O Azure oferece uma série de tamanhos de [máquinas virtuais otimizados pela GPU.](/azure/virtual-machines/windows/sizes-gpu) A escolha certa para o seu pool de anfitriões depende de uma série de fatores, incluindo as cargas de trabalho da sua aplicação particular, a qualidade desejada da experiência do utilizador e o custo. Em geral, gpus maiores e mais capazes oferecem uma melhor experiência de utilizador em uma dada densidade de utilizador.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Crie uma piscina de anfitriões, aprovisione a sua máquina virtual e configuure um grupo de aplicações

Crie uma nova piscina de anfitriões utilizando um VM do tamanho selecionado. Para obter instruções, consulte [Tutorial: Crie uma piscina de anfitriões com o Azure Marketplace](/azure/virtual-desktop/create-host-pools-azure-marketplace).

O Windows Virtual Desktop suporta a renderização e codificação aceleradas da GPU nos seguintes sistemas operativos:

* Versão 1511 do Windows 10 ou mais recente
* Windows Server 2016 ou mais recente

Também deve configurar um grupo de aplicações ou utilizar o grupo de aplicações padrão de desktop (chamado "Desktop Application Group") que é automaticamente criado quando cria um novo pool de anfitriões. Para obter instruções, consulte [Tutorial: Gerir grupos de aplicações para o Windows Virtual Desktop](/azure/virtual-desktop/manage-app-groups).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Instale controladores gráficos suportados na sua máquina virtual

Para tirar partido das capacidades da GPU dos VMs da série Azure N no Windows Virtual Desktop, tem de instalar os controladores gráficos apropriados. Siga as instruções nos [sistemas operativos suportados e nos controladores](/azure/virtual-machines/windows/sizes-gpu#supported-operating-systems-and-drivers) para instalar os controladores do fornecedor gráfico apropriado, manualmente ou utilizando uma extensão VM Azure.

Apenas os controladores distribuídos pelo Azure são suportados para o Windows Virtual Desktop. Além disso, para VMs Azure com GPUs NVIDIA, apenas [os controladores da GRELHA NVIDIA](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) são suportados para o Windows Virtual Desktop.

Após a instalação do condutor, é necessário reiniciar um VM. Utilize as etapas de verificação nas instruções acima para confirmar que os controladores gráficos foram instalados com sucesso.

## <a name="configure-gpu-accelerated-app-rendering"></a>Configurar a renderização de aplicativos acelerados pela GPU

Por predefinição, as aplicações e os desktops em execução em configurações multi-sessão são renderizados com o CPU e não alavancam as GPUs disponíveis para renderização. Configure a política de grupo para o anfitrião da sessão para permitir a renderização acelerada pela GPU:

1. Ligue-se ao ambiente de trabalho do VM utilizando uma conta com privilégios de administrador local.
2. Abra o menu Iniciar e escreva "gpedit.msc" para abrir o Editor de Política do Grupo.
3. Navegue na árvore para **configurar o computador**  >  **Modelos administrativos**  >  **Windows Componentes**  >  **Remotos desktop Services**Remote Desktop Session  >  **Host**Remote  >  **Session Environment**.
4. Selecione a política **Utilize o adaptador gráfico padrão de hardware para todas as sessões de Serviços de Ambiente de Trabalho Remoto** e desementa esta política para **ativar** a renderização de GPU na sessão remota.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Codificação de quadros acelerados pela GPU

O Ambiente de Trabalho Remoto codifica todos os gráficos renderizados por apps e ambientes de trabalho (seja renderizado com GPU ou com CPU) para transmissão a clientes de Ambiente de Trabalho Remoto. Por predefinição, o Remote Desktop não aproveita as GPUs disponíveis para esta codificação. Configure a política de grupo para o anfitrião da sessão para permitir a codificação de quadros acelerados pela GPU. Continuando os passos acima:

1. Selecione a política **Priorização H.264/AVC 444 O modo gráfico para ligações de ambiente de trabalho remoto** e define esta política para **Ativar** o código H.264/AVC 444 na sessão remota.
2. Selecione a codificação de **hardware Configure H.264/AVC para ligações remotas de ambiente de trabalho** e descongessione esta política para **Ativar** a codificação de hardware para AVC/H.264 na sessão remota.

    >[!NOTE]
    >No Windows Server 2016, opção de definição **Prefere hardware AVC Encoding** to **Always try**.

3. Agora que as políticas de grupo foram editadas, forcem uma atualização da política de grupo. Abra a solicitação de comando e escreva:

    ```batch
    gpupdate.exe /force
    ```

4. Inscreva-se na sessão de desktop remoto.

## <a name="verify-gpu-accelerated-app-rendering"></a>Verifique a renderização de aplicativos acelerados pela GPU

Para verificar se as aplicações estão a utilizar a GPU para renderização, experimente qualquer uma das seguintes:

* Para VMs Azure com um GPU NVIDIA, utilize o `nvidia-smi` utilitário como descrito na [instalação do controlador verificar](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) para verificar se há utilização de GPU ao executar as suas aplicações.
* Nas versões suportadas do sistema operativo, pode utilizar o Gestor de Tarefas para verificar a utilização da GPU. Selecione a GPU no separador "Performance" para ver se as aplicações estão a utilizar a GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Verifique a codificação de quadros acelerados pela GPU

Para verificar se o Ambiente de Trabalho Remoto está a utilizar codificação acelerada pela GPU:

1. Ligue-se ao ambiente de trabalho do VM utilizando o cliente de ambiente de trabalho virtual do Windows.
2. Lançar o Observador de Eventos e navegar para o seguinte nó: **Aplicações e Serviços Regista**  >  **Microsoft**  >  **Windows**  >  **RemoteDesktopServices-RdpCoreCDV**  >  **Operacional**
3. Para determinar se a codificação acelerada da GPU é usada, procure o ID 170 do evento. Se vir "AVC hardware codificador: 1" então a codificação da GPU é utilizada.
4. Para determinar se o modo AVC 444 é utilizado, procure o ID 162 do evento. Se vir "AVC Disponível: 1 Perfil Inicial: 2048" então é utilizado AVC 444.

## <a name="next-steps"></a>Passos seguintes

Estas instruções devem tê-lo a funcionar com a aceleração da GPU num anfitrião de uma sessão (um VM). Algumas considerações adicionais para permitir a aceleração da GPU através de um pool de hospedeiro maior:

* Considere usar uma [extensão VM](/azure/virtual-machines/extensions/overview) para simplificar a instalação do controlador e atualizações em vários VMs. Utilize a extensão do [condutor do GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) para VMs com GPUs NVIDIA e utilize a extensão do condutor gpu amd para VMs com GPUs AMD.
* Considere usar a Política de Grupo de Diretório Ativo para simplificar a configuração da política de grupo em vários VMs. Para obter informações sobre a implementação da Política de Grupo no domínio do Diretório Ativo, consulte [Trabalhar com objetos de política de grupo](https://go.microsoft.com/fwlink/p/?LinkId=620889).