---
title: Criar um laboratório com GPUs nos Serviços de Laboratório Azure Microsoft Docs
description: Saiba como configurar um laboratório com máquinas virtuais de unidade de processamento de gráficos (GPU).
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 8293ed1bfb53895b9631d9730fb75a2364457180
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452375"
---
# <a name="set-up-a-lab-with-gpu-virtual-machines"></a>Criar um laboratório com máquinas virtuais gpu

Este artigo mostra-lhe como fazer as seguintes tarefas:

- Escolha entre as unidades de processamento de gráficos *de visualização* e *de cálculo* (GPUs).
- Certifique-se de que os controladores gpu adequados estão instalados.

## <a name="choose-between-visualization-and-compute-gpu-sizes"></a>Escolha entre visualização e calcular tamanhos de GPU
Na primeira página do assistente de criação de laboratório, na lista de que **tamanho virtual da máquina precisa?**  

![Screenshot do painel "New lab" para selecionar um tamanho VM](./media/how-to-setup-gpu/lab-gpu-selection.png)

Neste processo, tem a opção de selecionar GPUs **de Visualização** ou **Compute.**  É importante escolher o tipo de GPU que é baseado no software que os seus alunos vão usar.  

Tal como descrito na tabela seguinte, o tamanho da GPU *do cálculo* destina-se a aplicações intensivas de computação.  Por exemplo, o [tipo de classe de aprendizagem profunda em linguagem natural](./class-type-deep-learning-natural-language-processing.md) utiliza o tamanho da Pequena **GPU (Compute).**  A GPU computacional é adequada para este tipo de aula, porque os alunos usam quadros de aprendizagem profunda e ferramentas que são fornecidas pela [imagem da Máquina Virtual de Ciência de Dados](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) para treinar modelos de aprendizagem profunda com grandes conjuntos de dados.

| Tamanho | Núcleos | RAM | Descrição | 
| ---- | ----- | --- | ----------- | 
| Pequena GPU (Computação) | -&nbsp;6 &nbsp; núcleos<br>-&nbsp;56 &nbsp; GB &nbsp; DE RAM  | [Standard_NC6](../virtual-machines/nc-series.md) |Este tamanho é mais adequado para aplicações computacionalmente intensivas, como inteligência artificial (IA) e aprendizagem profunda. |

Os tamanhos de GPU *de visualização* destinam-se a aplicações com grande intensidade gráfica.  Por exemplo, o tipo de [classe de engenharia SOLIDWORKS](./class-type-solidworks.md) mostra usando o tamanho **de GPU pequeno (Visualização).**  A GPU de visualização é adequada para este tipo de aula, porque os alunos interagem com o ambiente de design 3D assistido por computador SOLIDWORKS (CAD) para modelar e visualizar objetos sólidos.

| Tamanho | Núcleos | RAM | Descrição | 
| ---- | ----- | --- | ----------- | 
| Pequeno GPU (Visualização) | -&nbsp;6 &nbsp; núcleos<br>-&nbsp;56 &nbsp; GB &nbsp; DE RAM  | [Standard_NV6](../virtual-machines/nv-series.md) | Este tamanho é mais adequado para visualização remota, streaming, jogos e codificação que utilizam quadros como OpenGL e DirectX. |
| GPU médio (Visualização) | -&nbsp;12 &nbsp; núcleos<br>-&nbsp;112 &nbsp; GB &nbsp; DE RAM  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Este tamanho é mais adequado para visualização remota, streaming, jogos e codificação que utilizam quadros como OpenGL e DirectX. |

> [!NOTE]
> Pode não ver alguns destes tamanhos de VM na lista ao criar um laboratório de sala de aula. A lista é povoada com base na capacidade atual da localização do laboratório. Se o criador da conta de laboratório permitir que os criadores de [laboratório escolham um local para o laboratório,](allow-lab-creator-pick-lab-location.md)pode tentar escolher um local diferente para o laboratório e ver se o tamanho do VM está disponível. Para disponibilidade de VMs, consulte [produtos disponíveis por região.](https://azure.microsoft.com/regions/services/?products=virtual-machines)

## <a name="ensure-that-the-appropriate-gpu-drivers-are-installed"></a>Certifique-se de que os controladores de GPU adequados são instalados
Para tirar partido das capacidades de GPU dos seus VMs de laboratório, certifique-se de que os controladores de GPU apropriados estão instalados.  No assistente de criação de laboratório, quando selecionar um tamanho GPU VM, pode selecionar a opção **de controladores GPU instalar.**  

![Screenshot do "Novo laboratório" mostrando a opção "Instalar controladores GPU"](./media/how-to-setup-gpu/lab-gpu-drivers.png)

Como mostrado na imagem anterior, esta opção é ativada por padrão, o que garante que os controladores *mais recentes* são instalados para o tipo de GPU e imagem que selecionou.
- Quando seleciona um tamanho GPU *compute,* os seus VMs de laboratório são alimentados pelo [GPU NVIDIA Tesla K80.](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf)  Neste caso, são instalados os mais recentes controladores [compute Unified Device Architecture (CUDA),](http://developer.download.nvidia.com/compute/cuda/2_0/docs/CudaReferenceManual_2.0.pdf) o que permite a computação de alto desempenho.
- Ao selecionar um tamanho GPU *de visualização,* os seus VMs de laboratório são alimentados pela tecnologia [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU e [GRID](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf).  Neste caso, são instalados os mais recentes controladores GRID, o que permite a utilização de aplicações com grande intensidade gráfica.

### <a name="install-the-drivers-manually"></a>Instale os controladores manualmente
Poderá ser necessário instalar uma versão do controlador que não seja a versão mais recente.  Esta secção mostra como instalar manualmente os controladores apropriados, dependendo se está a usar uma GPU *computacional* ou uma GPU *de visualização.*

#### <a name="install-the-compute-gpu-drivers"></a>Instale os controladores de GPU compute

Para instalar manualmente os controladores para o tamanho da GPU do cálculo, faça o seguinte:

1. No assistente de criação de laboratório, quando estiver a [criar o seu laboratório,](./how-to-manage-classroom-labs.md)desative a definição **de controladores de GPU de instalação.**

1. Após a criação do seu laboratório, ligue-se ao modelo VM para instalar os controladores apropriados.

   ![Screenshot da página de downloads do condutor da NVIDIA](./media/how-to-setup-gpu/nvidia-driver-download.png) 

   a. Num browser, aceda à [página de Downloads do Condutor NVIDIA.](https://www.nvidia.com/Download/index.aspx)  
   b. Desaprote o **tipo de produto** para **Tesla**.  
   c. Coloque a **Série de Produtos** em Série **K**.  
   d. Desaperte o **Sistema Operativo** de acordo com o tipo de imagem base selecionada quando criou o seu laboratório.  
   e. Desa estade o **kit de ferramentas CUDA** à versão do controlador CUDA de que necessita.  
   f. Selecione **Procurar** os seus motoristas.  
   exemplo, Selecione **Baixar** para descarregar o instalador.  
   h. Executar o instalador de modo a que os controladores sejam instalados no modelo VM.  
1. Valide que os controladores são instalados corretamente seguindo as instruções na secção [Validate the installed drivers.](how-to-setup-lab-gpu.md#validate-the-installed-drivers) 
1. Depois de instalar os controladores e outros softwares necessários para a sua aula, **selecione Publicar** para criar os VM dos seus alunos.

> [!NOTE]
> Se estiver a utilizar uma imagem Linux, depois de ter descarregado o instalador, instale os controladores seguindo as instruções no [Instalar os controladores CUDA no Linux](../virtual-machines/linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#install-cuda-drivers-on-n-series-vms).

#### <a name="install-the-visualization-gpu-drivers"></a>Instale os controladores GPU de visualização

Para instalar manualmente os controladores para o tamanho de GPU de visualização, faça o seguinte:

1. No assistente de criação de laboratório, quando estiver a [criar o seu laboratório,](./how-to-manage-classroom-labs.md)desative a definição **de controladores de GPU de instalação.**
1. Após a criação do seu laboratório, ligue-se ao modelo VM para instalar os controladores apropriados.
1. Instale os controladores GRID fornecidos pela Microsoft no modelo VM seguindo as instruções para o seu sistema operativo:
   -  [Controladores da GRELHA do Windows NVIDIA](../virtual-machines/windows/n-series-driver-setup.md#nvidia-grid-drivers)
   -  [Condutores da GRELHA Linux NVIDIA](../virtual-machines/linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#nvidia-grid-drivers)
  
1. Reinicie o modelo VM.
1. Valide que os controladores são instalados corretamente seguindo as instruções na secção [Validate the installed drivers.](how-to-setup-lab-gpu.md#validate-the-installed-drivers)
1. Depois de instalar os controladores e outros softwares necessários para a sua aula, **selecione Publicar** para criar os VM dos seus alunos.

### <a name="validate-the-installed-drivers"></a>Validar os controladores instalados
Esta secção descreve como validar que os seus controladores de GPU estão corretamente instalados.

#### <a name="windows-images"></a>Imagens do Windows
1.  Siga as instruções na secção "Verificar a instalação do controlador" dos [controladores de GPU da NVIDIA em VMs da série N que executam o Windows](../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation).
1.  Se estiver a utilizar uma GPU *de visualização,* também pode:
    - Ver e ajustar as definições de GPU no Painel de Controlo da NVIDIA. Para tal, no **Painel de Controlo do Windows,** selecione **Hardware** e, em seguida, selecione **O Painel de Controlo NVIDIA**.

      ![Screenshot do Painel de Controlo do Windows mostrando a ligação do Painel de Controlo NVIDIA](./media/how-to-setup-gpu/control-panel-nvidia-settings.png) 

     - Veja o desempenho da sua GPU utilizando **o Gestor de Tarefas**.  Para tal, selecione o **separador Performance** e, em seguida, selecione a opção **GPU.**

       ![Screenshot mostrando o separador de desempenho do Gestor de Tarefas GPU](./media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > As definições do Painel de Controlo NVIDIA só podem ser acedidas para *GPUs de visualização.*  Se tentar abrir o Painel de Controlo NVIDIA para uma GPU computacional, terá o seguinte erro: "As definições do NVIDIA Display não estão disponíveis.  Não está atualmente a utilizar um ecrã ligado a um GPU da NVIDIA."  Da mesma forma, a informação de desempenho da GPU no Task Manager é fornecida apenas para GPUs de visualização.

#### <a name="linux-images"></a>Imagens linux
Siga as instruções na secção "Verificar a instalação do controlador" dos [controladores da Instalação NVIDIA GPU em VMs da série N que executam o Linux](../virtual-machines/linux/n-series-driver-setup.md#verify-driver-installation).

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Criar e gerir laboratórios](how-to-manage-classroom-labs.md)
- [Tipo de classe assistido por computador SOLIDWORKS (CAD)](class-type-solidworks.md)
- [Tipo de classe MATLAB (laboratório de matriz)](class-type-matlab.md)