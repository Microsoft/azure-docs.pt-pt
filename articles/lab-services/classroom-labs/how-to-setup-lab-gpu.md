---
title: Criar um laboratório com GPUs nos Serviços de Laboratório Azure Microsoft Docs
description: Saiba como configurar um laboratório com máquinas virtuais GPU (unidade de processamento de gráficos).
services: lab-services
documentationcenter: na
author: nicolela
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/28/2020
ms.author: nicolela
ms.openlocfilehash: 9f4bc77366aaeaef9c66467e540219763228d317
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450774"
---
# <a name="set-up-a-lab-with-gpu-graphics-processing-unit-virtual-machine-size"></a>Configurar um laboratório com GPU (unidade de processamento de gráficos) tamanho da máquina virtual
Este artigo mostra-lhe como fazer as seguintes tarefas:

- Escolha entre virtualização e GPUs computacional
- Certifique-se de que os controladores de GPU adequados são instalados
- Configurar definições de RDP para ligar a uma máquina virtual GPU (VM)

## <a name="choose-between-virtualization-and-compute-gpu-sizes"></a>Escolha entre virtualização e tamanhos de GPU computacional
Na primeira página do assistente de criação de laboratório, seleciona o **tamanho das máquinas virtuais necessárias** para a sua aula.  

![Selecione tamanho VM](../media/how-to-setup-gpu/lab-gpu-selection.png)

Aqui, tem a opção de escolher entre **As GPUs de Visualização** e **Compute.**  É importante que escolha o tipo apropriado de GPU com base no software que os seus alunos irão utilizar.  

Tal como descrito na tabela abaixo, o tamanho da GPU **computacional** destina-se a aplicações intensivas de computação.  Por exemplo, o [tipo de aprendizagem profunda em classe de processamento de linguagem natural](./class-type-deep-learning-natural-language-processing.md) mostra usando o tamanho de **GPU pequeno (Compute).**  O **GPU compute** é adequado para este tipo de aula, uma vez que os alunos usam quadros de aprendizagem profunda e ferramentas fornecidas pela [imagem da Máquina Virtual de Ciência de Dados](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) para treinar modelos de aprendizagem profunda com grandes conjuntos de dados.

| Tamanho | Núcleos | RAM | Descrição | 
| ---- | ----- | --- | ----------- | 
| Pequena GPU (Computação) | <ul><li>6 Núcleos</li><li>56 GB DE RAM</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Este tamanho é mais adequado para aplicações intensivas em computador, como Inteligência Artificial e Deep Learning. |

Os tamanhos **de visualização** destinam-se a aplicações com grande intensidade gráfica.  Por exemplo, o [tipo de classe de engenharia SolidWorks](./class-type-solidworks.md) mostra usando o tamanho de **GPU pequeno (Visualização).**  A GPU **de Visualização** é adequada para este tipo de aula, uma vez que os alunos interagem com o ambiente de design 3D assistido por computador (CAD) da SolidWorks para modelar e visualizar objetos sólidos.

| Tamanho | Núcleos | RAM | Descrição | 
| ---- | ----- | --- | ----------- | 
| Pequeno GPU (Visualização) | <ul><li>6 Núcleos</li><li>56 GB DE RAM</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Este tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando quadros como OpenGL e DirectX. |
| GPU médio (Visualização) | <ul><li>12 Núcleos</li><li>112 GB DE RAM</li></ul>  | [Standard_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Este tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando quadros como OpenGL e DirectX. |

## <a name="ensure-the-appropriate-gpu-drivers-are-installed"></a>Certifique-se de que os controladores de GPU adequados são instalados
Para tirar partido das capacidades de GPU dos seus VMs de laboratório, deve certificar-se de que os controladores de GPU apropriados estão instalados.  No assistente de criação de laboratório quando selecionar um tamanho VM GPU, existe a opção de **instalar controladores GPU**.  

![Instalar condutores de GPU](../media/how-to-setup-gpu/lab-gpu-drivers.png)

Esta opção é **ativada** por padrão e garante que os controladores *mais recentes* são instalados para o tipo de GPU e imagem que selecionou:
- Ao selecionar o tamanho do **GPU compute,** os seus VMs de laboratório são alimentados pelo [GPU NVIDIA Tesla K80.](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf)  Neste caso, os controladores [cuda](https://www.nvidia.com/object/io_69526.html) mais recentes são instalados para permitir a computação de alto desempenho.
- Ao selecionar um tamanho GPU **de visualização,** os seus VMs de laboratório são alimentados pela tecnologia [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU e [GRID](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf).  Neste caso, os controladores GRID mais recentes são instalados para permitir aplicações com grande intensidade gráfica.

> [!IMPORTANT]
> Para ter a melhor experiência do utilizador com GPUs **de Visualização,** deve certificar-se de que *ambos os* controladores *estão* instalados e a GPU está ativada sobre as ligações RDP.  Consulte os passos na secção chamada [Enable GPU sobre a ligação RDP aos VMs do Windows](how-to-setup-lab-gpu.md#enable-gpu-over-rdp-connection-to-windows-vms).

### <a name="manually-install-drivers"></a>Instalar manualmente controladores
Pode ter cenários em que precisa de instalar uma versão diferente dos controladores do que a versão mais recente.  Os passos desta secção mostram como instalar manualmente os controladores apropriados, dependendo se estiver a utilizar uma GPU **compute** ou **visualização.**

#### <a name="compute-gpu-drivers"></a>Condutores da GPU computacional
Siga estes passos para instalar manualmente os controladores para o tamanho da GPU **compute:**
1. Ao [criar o seu laboratório,](./how-to-manage-classroom-labs.md) **desative** a definição **de controladores de GPU de instalação** no assistente de criação de laboratório.
1. Após a criação do seu laboratório, ligue-se ao modelo VM para instalar os controladores apropriados.
   ![Download de motoristas NVIDIA](../media/how-to-setup-gpu/nvidia-driver-download.png) 
   1. No seu navegador, abra a [página de Downloads de Motoristas da NVIDIA.](https://www.nvidia.com/Download/index.aspx)
   2. Desaprote o **tipo de produto** para **Tesla**.
   3. Coloque a **Série de Produtos** em Série **K**.
   4. Coloque o **produto** no **Tesla K80**.
   5. Desaperte o **Sistema Operativo** de acordo com o tipo de imagem base selecionada ao criar o seu laboratório.
   6. Desa estade o **kit de ferramentas CUDA** à versão dos controladores CUDA de que necessita.
   7. Clique **em Procurar** para encontrar os seus motoristas.
   8. Clique **em Baixar** para descarregar o instalador.
   9. Executar o instalador de modo a que os controladores sejam instalados no modelo VM.  
2. Validar os controladores instalados corretamente seguindo os passos na secção denominado [Validate controladores instalados](how-to-setup-lab-gpu.md#validate-installed-drivers). 
3. Depois de ter instalado os controladores e outros softwares necessários para a sua aula, pode clicar em **Publicar** para criar os VM dos seus alunos.

> [!NOTE]
> Se estiver a utilizar uma imagem Linux, consulte também os seguintes passos para instalar os controladores depois de ter descarregado o instalador: [Instale os controladores CUDA no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#install-cuda-drivers-on-n-series-vms).

#### <a name="visualization-gpu-drivers"></a>Condutores de GPU de visualização
Siga estes passos para instalar manualmente os controladores para **os** tamanhos gpu visualização:
1. Ao [criar o seu laboratório,](./how-to-manage-classroom-labs.md) **desative** a definição **de controladores de GPU de instalação** no assistente de criação de laboratório.
1. Após a criação do seu laboratório, ligue-se ao modelo VM para instalar os controladores apropriados.
1. Instale os controladores GRID fornecidos pela Microsoft no modelo VM seguindo estes passos:
   -  [Controladores da GRELHA do Windows NVIDIA](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers)
   -  [Condutores da GRELHA Linux NVIDIA](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#nvidia-grid-drivers)
  
1. Reinicie o modelo VM.
1. Validar os controladores instalados corretamente seguindo os passos na secção denominado [Validate controladores instalados](how-to-setup-lab-gpu.md#validate-installed-drivers).
1. Configure as definições de RDP para permitir a GPU seguindo os passos na secção chamada [Enable GPU sobre a ligação RDP aos VMs do Windows](how-to-setup-lab-gpu.md#enable-gpu-over-rdp-connection-to-windows-vms).
1. Depois de ter instalado os controladores e outros softwares necessários para a sua aula, pode clicar em **Publicar** para criar os VM dos seus alunos.

### <a name="validate-installed-drivers"></a>Validar controladores instalados
Os passos desta secção descrevem como validar que os seus controladores de GPU estão devidamente instalados.

#### <a name="windows-images"></a>Imagens do Windows
1.  Siga os passos do artigo que mostra como verificar a [instalação do controlador no Windows](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation).
1.  Se estiver a utilizar uma GPU **de Visualização,** também pode:
    - Aceda ao **Painel de Controlo da NVIDIA** para visualizar e ajustar as definições de GPU.  Para aceder a estas definições, abra **o Painel de Controlo > Hardware** e selecione o Painel de Controlo da **NVIDIA**.
      ![Painel de controlo aberto da NVIDIA](../media/how-to-setup-gpu/control-panel-nvidia-settings.png) 
     - Veja o desempenho da sua GPU utilizando **o Gestor de Tarefas**.  Para ver isto, abra o **Gestor de Tarefas** e selecione o **separador Performance.** ![ Desempenho gpu do gestor de tarefas](../media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > As definições **do Painel de Controlo NVIDIA** só podem ser acedidas para GPUs de **Visualização.**  Se tentar abrir o **Painel de Controlo NVIDIA** para uma GPU **compute,** verá o seguinte erro: **as definições do Visor NVIDIA não estão disponíveis.  Não está atualmente a utilizar um ecrã ligado a um GPU NVIDIA.**  Da mesma forma, a informação de desempenho da GPU em **Task Manager** é fornecida apenas para GPUs de **visualização.**

#### <a name="linux-images"></a>Imagens linux
Siga os passos do artigo que mostra como verificar a [instalação do condutor no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#verify-driver-installation).

## <a name="enable-gpu-over-rdp-connection-to-windows-vms"></a>Ativar a GPU sobre a ligação rdp aos VMs do Windows
Ao utilizar o RDP (protocolo de ambiente de trabalho remoto) para ligar a um VM do Windows que é alimentado por um GPU **de Visualização,** é necessário fazer alguma configuração extra para que a GPU seja utilizada para renderizar gráficos (caso contrário, o CPU será utilizado).

Os passos abaixo precisam de ser preenchidos no modelo VM.

1. Em primeiro lugar, configurar as definições de PDR para a utilização da GPU:

   1. Siga os passos deste artigo que mostra como configurar a [renderização de aplicações aceleradas pela GPU.](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#configure-gpu-accelerated-app-rendering)

   2. Siga os passos deste artigo que mostram como [configurar a codificação de quadros acelerados pela GPU](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#configure-gpu-accelerated-frame-encoding).

2. Em seguida, verifique a configuração: 

   1. Siga os passos deste artigo que mostra como verificar a [renderização de aplicações aceleradas pela GPU.](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#verify-gpu-accelerated-app-rendering)

   2.  Siga os passos deste artigo que mostram como verificar a [codificação de quadros acelerados pela GPU](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#verify-gpu-accelerated-frame-encoding).

3. Finalmente, tem agora os controladores instalados e tem configurações RDP configuradas para utilizar a sua GPU.  Depois de ter instalado outro software necessário para a sua aula, pode clicar em **Publicar** para criar os VM dos seus alunos.  Quando os seus alunos se ligarem aos seus VMs utilizando RDP, o seu ambiente de trabalho será renderizado utilizando a GPU dos seus VM.

## <a name="next-steps"></a>Próximos passos
Consulte os seguintes artigos:

- [Criar e gerir laboratórios de sala de aula](how-to-manage-classroom-labs.md)
- [Tipo de classe de design assistido por computador SolidWorks (CAD)](class-type-solidworks.md)
- [Tipo de classe MATLAB](class-type-matlab.md)



