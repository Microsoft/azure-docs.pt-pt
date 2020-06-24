---
title: Criar um laboratório com GPUs nos Serviços de Laboratório Azure Microsoft Docs
description: Saiba como configurar um laboratório com máquinas virtuais de unidade de processamento de gráficos (GPU).
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
ms.openlocfilehash: adac35bd3f59870f0c164b69548375610e9733b1
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84897342"
---
# <a name="set-up-a-lab-with-gpu-virtual-machines"></a>Criar um laboratório com máquinas virtuais gpu

Este artigo mostra-lhe como fazer as seguintes tarefas:

- Escolha entre as unidades de processamento de gráficos *de visualização* e *de cálculo* (GPUs).
- Certifique-se de que os controladores gpu adequados estão instalados.
- Configurar definições de Protocolo remoto de Ambiente de Trabalho (RDP) para ligar a uma máquina virtual GPU (VM).

## <a name="choose-between-visualization-and-compute-gpu-sizes"></a>Escolha entre visualização e calcular tamanhos de GPU
Na primeira página do assistente de criação de laboratório, na lista de que **tamanho virtual da máquina precisa?**  

![Screenshot do painel "New lab" para selecionar um tamanho VM](./media/how-to-setup-gpu/lab-gpu-selection.png)

Neste processo, tem a opção de selecionar GPUs **de Visualização** ou **Compute.**  É importante escolher o tipo de GPU que é baseado no software que os seus alunos vão usar.  

Tal como descrito na tabela seguinte, o tamanho da GPU *do cálculo* destina-se a aplicações intensivas de computação.  Por exemplo, o [tipo de classe de aprendizagem profunda em linguagem natural](./class-type-deep-learning-natural-language-processing.md) utiliza o tamanho da Pequena **GPU (Compute).**  A GPU computacional é adequada para este tipo de aula, porque os alunos usam quadros de aprendizagem profunda e ferramentas que são fornecidas pela [imagem da Máquina Virtual de Ciência de Dados](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) para treinar modelos de aprendizagem profunda com grandes conjuntos de dados.

| Tamanho | Núcleos | RAM | Description | 
| ---- | ----- | --- | ----------- | 
| Pequena GPU (Computação) | -&nbsp;6 &nbsp; núcleos<br>-&nbsp;56 &nbsp; GB &nbsp; DE RAM  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Este tamanho é mais adequado para aplicações intensivas em computador, como inteligência artificial (IA) e aprendizagem profunda. |

Os tamanhos de GPU *de visualização* destinam-se a aplicações com grande intensidade gráfica.  Por exemplo, o [tipo de classe de engenharia SolidWorks](./class-type-solidworks.md) mostra usando o tamanho de **GPU pequeno (Visualização).**  A GPU de visualização é adequada para este tipo de aula, porque os alunos interagem com o ambiente de design 3D assistido por computador SolidWorks (CAD) para modelar e visualizar objetos sólidos.

| Tamanho | Núcleos | RAM | Description | 
| ---- | ----- | --- | ----------- | 
| Pequeno GPU (Visualização) | -&nbsp;6 &nbsp; núcleos<br>-&nbsp;56 &nbsp; GB &nbsp; DE RAM  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Este tamanho é mais adequado para visualização remota, streaming, jogos e codificação que utilizam quadros como OpenGL e DirectX. |
| GPU médio (Visualização) | -&nbsp;12 &nbsp; núcleos<br>-&nbsp;112 &nbsp; GB &nbsp; DE RAM  | [Standard_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Este tamanho é mais adequado para visualização remota, streaming, jogos e codificação que utilizam quadros como OpenGL e DirectX. |

## <a name="ensure-that-the-appropriate-gpu-drivers-are-installed"></a>Certifique-se de que os controladores de GPU adequados são instalados
Para tirar partido das capacidades de GPU dos seus VMs de laboratório, certifique-se de que os controladores de GPU apropriados estão instalados.  No assistente de criação de laboratório, quando selecionar um tamanho GPU VM, pode selecionar a opção **de controladores GPU instalar.**  

![Screenshot do "Novo laboratório" mostrando a opção "Instalar controladores GPU"](./media/how-to-setup-gpu/lab-gpu-drivers.png)

Como mostrado na imagem anterior, esta opção é ativada por padrão, o que garante que os controladores *mais recentes* são instalados para o tipo de GPU e imagem que selecionou.
- Quando seleciona um tamanho GPU *compute,* os seus VMs de laboratório são alimentados pelo [GPU NVIDIA Tesla K80.](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf)  Neste caso, são instalados os mais recentes controladores [compute Unified Device Architecture (CUDA),](https://www.nvidia.com/object/io_69526.html) o que permite a computação de alto desempenho.
- Ao selecionar um tamanho GPU *de visualização,* os seus VMs de laboratório são alimentados pela tecnologia [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU e [GRID](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf).  Neste caso, são instalados os mais recentes controladores GRID, o que permite a utilização de aplicações com grande intensidade gráfica.

> [!IMPORTANT]
> Para ter a melhor experiência do utilizador com GPUs *de visualização,* certifique-se de que *ambos os* controladores estão instalados *e* a GPU está ativada sobre ligações RDP. Para obter mais informações, consulte a [GPU enable over RDP connection to Windows VMs](how-to-setup-lab-gpu.md#enable-gpu-over-rdp-connection-to-windows-vms) section this article.

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
> Se estiver a utilizar uma imagem Linux, depois de ter descarregado o instalador, instale os controladores seguindo as instruções no [Instalar os controladores CUDA no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#install-cuda-drivers-on-n-series-vms).

#### <a name="install-the-visualization-gpu-drivers"></a>Instale os controladores GPU de visualização

Para instalar manualmente os controladores para o tamanho de GPU de visualização, faça o seguinte:

1. No assistente de criação de laboratório, quando estiver a [criar o seu laboratório,](./how-to-manage-classroom-labs.md)desative a definição **de controladores de GPU de instalação.**
1. Após a criação do seu laboratório, ligue-se ao modelo VM para instalar os controladores apropriados.
1. Instale os controladores GRID fornecidos pela Microsoft no modelo VM seguindo as instruções para o seu sistema operativo:
   -  [Controladores da GRELHA do Windows NVIDIA](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers)
   -  [Condutores da GRELHA Linux NVIDIA](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#nvidia-grid-drivers)
  
1. Reinicie o modelo VM.
1. Valide que os controladores são instalados corretamente seguindo as instruções na secção [Validate the installed drivers.](how-to-setup-lab-gpu.md#validate-the-installed-drivers)
1. Configure as definições de RDP para ativar a ligação gpu seguindo as instruções da [GPU ativar a ligação RDP à secção VMs do Windows.](how-to-setup-lab-gpu.md#enable-gpu-over-rdp-connection-to-windows-vms)
1. Depois de instalar os controladores e outros softwares necessários para a sua aula, **selecione Publicar** para criar os VM dos seus alunos.

### <a name="validate-the-installed-drivers"></a>Validar os controladores instalados
Esta secção descreve como validar que os seus controladores de GPU estão corretamente instalados.

#### <a name="windows-images"></a>Imagens do Windows
1.  Siga as instruções na secção "Verificar a instalação do controlador" dos [controladores de GPU da NVIDIA em VMs da série N que executam o Windows](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation).
1.  Se estiver a utilizar uma GPU *de visualização,* também pode:
    - Ver e ajustar as definições de GPU no Painel de Controlo da NVIDIA. Para tal, no **Painel de Controlo do Windows,** selecione **Hardware**e, em seguida, selecione **O Painel de Controlo NVIDIA**.

      ![Screenshot do Painel de Controlo do Windows mostrando a ligação do Painel de Controlo NVIDIA](./media/how-to-setup-gpu/control-panel-nvidia-settings.png) 

     - Veja o desempenho da sua GPU utilizando **o Gestor de Tarefas**.  Para tal, selecione o **separador Performance** e, em seguida, selecione a opção **GPU.**

       ![Screenshot mostrando o separador de desempenho do Gestor de Tarefas GPU](./media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > As definições do Painel de Controlo NVIDIA só podem ser acedidas para *GPUs de visualização.*  Se tentar abrir o Painel de Controlo NVIDIA para uma GPU computacional, terá o seguinte erro: "As definições do NVIDIA Display não estão disponíveis.  Não está atualmente a utilizar um ecrã ligado a um GPU da NVIDIA."  Da mesma forma, a informação de desempenho da GPU no Task Manager é fornecida apenas para GPUs de visualização.

#### <a name="linux-images"></a>Imagens linux
Siga as instruções na secção "Verificar a instalação do controlador" dos [controladores da Instalação NVIDIA GPU em VMs da série N que executam o Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#verify-driver-installation).

## <a name="enable-gpu-over-rdp-connection-to-windows-vms"></a>Ativar a GPU sobre a ligação rdp aos VMs do Windows
Quando estiver a utilizar o RDP para ligar a um VM do Windows que é alimentado por uma GPU *de visualização,* precisa de fazer alguma configuração extra para que a GPU seja usada para renderizar gráficos. Caso contrário, o CPU será usado para renderizar gráficos.

No modelo VM, faça o seguinte:

1. Configure as definições rdp para a utilização da GPU.

   a. Siga as instruções na [renderização de aplicações aceleradas pela GPU.](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#configure-gpu-accelerated-app-rendering)  
   b. Siga as instruções na [codificação de quadros acelerados pela GPU.](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#configure-gpu-accelerated-frame-encoding)

1. Verifique a configuração. 

   a. Siga as instruções na [verificação da renderização de aplicações aceleradas pela GPU](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#verify-gpu-accelerated-app-rendering).  
   b. Siga as instruções na [verificação da codificação de quadros acelerados pela GPU](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#verify-gpu-accelerated-frame-encoding).

1. Tem agora os controladores instalados e as definições DE PDR configuradas para utilizar a sua GPU.  Depois de ter instalado o outro software necessário para a sua aula, pode selecionar **a Publish** para criar os VM dos seus alunos.  

Quando os seus alunos se ligarem aos seus VM utilizando RDP, os seus ambientes de trabalho serão renderizados de acordo com a GPU dos seus VM.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Criar e gerir laboratórios de sala de aula](how-to-manage-classroom-labs.md)
- [Tipo de classe de design assistido por computador SolidWorks (CAD)](class-type-solidworks.md)
- [Tipo de classe MATLAB (laboratório de matriz)](class-type-matlab.md)



