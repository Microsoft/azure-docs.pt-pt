---
title: Visão geral do instalador do pacote de ferramentas Azure Percept Dev
description: Saiba mais sobre a utilização do Instalador Dev Tools Pack para acelerar o desenvolvimento avançado com o Azure Percept
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: d684311ef959ac13f3be8bac7ffbbb06a741962a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097708"
---
# <a name="dev-tools-pack-installer-overview"></a>Visão geral do instalador do pacote de ferramentas Dev

O Instalador Dev Tools Pack é uma solução de balcão único que instala e configura todas as ferramentas necessárias para desenvolver uma solução Intelligent Edge. Se já instalou algum dos pacotes de software listados abaixo, o Instalador Dev Tools Pack reinstalará esses pacotes de modo a que as suas ferramentas sejam consistentes com as versões de software Do Instalador.

## <a name="mandatory-tools-installed"></a>Ferramentas obrigatórias instaladas

* [Visual Studio Code](https://code.visualstudio.com/)
* [Python 3.6 (Windows) ou 3.5 (Linux)](https://www.python.org/)
* [Estivador 19.03](https://www.docker.com/)
* [PIP3](https://pip.pypa.io/en/stable/user_guide/)
* [TensorFlow 1.13](https://www.tensorflow.org/)
* [Azure Machine Learning SDK 1.1](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py)

## <a name="optional-tools-available-for-installation"></a>Ferramentas opcionais disponíveis para instalação

* [Nvidia DeepStream SDK 5](https://developer.nvidia.com/deepstream-sdk) (Toolkit para desenvolver soluções para aceleradores Nvidia)
* [Intel OpenVino Toolkit 2020.2](https://docs.openvinotoolkit.org/) (Toolkit para desenvolvimento de soluções para aceleradores Intel)
* [Lobe.ai](https://lobe.ai/)  
* [Streamlit](https://www.streamlit.io/)
* [Pytorch 1.4.0 (Windows) ou 1.2.0 (Linux)](https://pytorch.org/)
* [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
* [Corrente 5.2](https://chainer.org/)
* [Caffe](https://caffe.berkeleyvision.org/)
* [Kit de Ferramentas CUDA 10.0.130](https://developer.nvidia.com/cuda-toolkit)
* [Microsoft Cognitive Toolkit 2.5.1](https://www.microsoft.com/research/product/cognitive-toolkit/?lang=fr_ca)

## <a name="known-issues"></a>Problemas Conhecidos

- A instalação opcional do Caffe pode falhar se o Docker não estiver a funcionar corretamente no sistema. Se pretender instalar o Caffe, certifique-se de que o Docker está instalado e em funcionamento antes de tentar a instalação do Caffe através do Instalador Dev Tools Pack. 

- A instalação opcional da CUDA falha em sistemas incompatíveis. Antes de tentar instalar o [Kit de Ferramentas CUDA 10.0.130](https://developer.nvidia.com/cuda-toolkit) através do Instalador Dev Tools Pack, verifique a compatibilidade do sistema.

## <a name="minimum-requirements"></a>Requisitos Mínimos

* Requisitos mínimos do Docker:

    * Windows:
        * https://docs.docker.com/docker-for-windows/install/#system-requirements

        - Windows 10 64-bit: Pro, Enterprise, ou Education (Build 16299 ou posterior).

             Para o Windows 10 Home, consulte o Site Desemestado do Docker no Windows Home.
           - As funcionalidades de Hiper-V e Containers Windows devem ser ativadas.
           - São necessários os seguintes pré-requisitos de hardware para executar com sucesso o Hyper-V do Cliente no Windows 10:

              - Processador de 64 bits com [tradução de endereço de segundo nível (SLAT)](https://en.wikipedia.org/wiki/Second_Level_Address_Translation)
              - RAM sistema de 4 GB
              - O suporte para virtualização de hardware ao nível bios deve ser ativado nas definições bios. Para mais informações, consulte Virtualização.

        > [!NOTE]
        > O Docker suporta o Docker Desktop no Windows com base no ciclo de vida de suporte da Microsoft para o sistema operativo Windows 10. Para obter mais informações, consulte a ficha técnica do ciclo de vida do [Windows.](https://support.microsoft.com/help/13853/windows-lifecycle-fact-sheet)

    * Mac:
        * https://docs.docker.com/docker-for-mac/install/#system-requirements
       
        O seu Mac deve cumprir os seguintes requisitos para instalar com sucesso o Docker Desktop:
         
         - **O hardware Mac deve ser um modelo de 2010 ou um modelo mais recente com um processador Intel**, com suporte de hardware da Intel para a virtualização da unidade de gestão de memória (MMU), incluindo Tabelas de Páginas Estendidas (EPT) e Modo Sem Restrições. Pode verificar se a sua máquina tem este suporte executando o seguinte comando num terminal: ```sysctl kern.hv_support```

        Se o seu Mac suportar a estrutura Hypervisor, o comando imprime ```kern.hv_support: 1``` .

         - **macOS deve ser a versão 10.14 ou mais recente**. Isto é, Mojave, Catalina, ou Big Sur. Recomendamos o upgrade para a versão mais recente do macOS.

        Se tiver algum problema depois de atualizar o seu macOS para a versão 10.15, tem de instalar a versão mais recente do Docker Desktop para ser compatível com esta versão do macOS.

        - De, pelo menos, 4 GB de RAM.
        - A VirtualBox antes da versão 4.3.30 não deve ser instalada, uma vez que não é compatível com o Docker Desktop.

        > [!NOTE]
        > Docker suporta Docker Desktop nas versões mais recentes do macOS. Ou seja, o atual lançamento do macOS e os dois lançamentos anteriores. À medida que as novas grandes versões do macOS são disponibilizadas geralmente, Docker deixa de suportar a versão mais antiga e suporta a versão mais recente do macOS (além das duas versões anteriores). O Docker Desktop suporta atualmente o macOS Mojave, o macOS Catalina e o macOS Big Sur.
        > 
        - O instalador não é suportado no Apple M1.

## <a name="instructions"></a>Instruções

1. Descarregue o Instalador Dev Tools Pack para [Windows,](https://go.microsoft.com/fwlink/?linkid=2132187) [Linux](https://go.microsoft.com/fwlink/?linkid=2132186)e [Mac](https://go.microsoft.com/fwlink/?linkid=2132296).

1. Dependendo da sua Plataforma, haverá algumas diferenças no lançamento do instalador.

    1. Para Windows:
    
        1. Clique no **Dev-Tools-Pack-Installer** para abrir o assistente de instalação.
        
    1. Para Mac:
    
        1. Depois de descarregar, mova o ficheiro Dev-Tools-Pack-Installer.app para a pasta Aplicações.
        
        1. Clique no **Dev-Tools-Pack-Installer.app** para abrir o assistente de instalação.
        
        1. Se tiver um diálogo de segurança "desenvolvedor não identificado":
        
            1. Vá às Preferências do Sistema -> Segurança & Privacidade -> Geral e clique no botão "Abrir de qualquer maneira" ao lado de "Dev-Tools-Pack-Installer.app"
        
            1. Clique no ícone de Eletrão na Doca novamente
        
            1. Clique no botão "Abrir" no diálogo de segurança
    
    1. Para Linux:
    
        1. Quando solicitado pelo navegador clique em "Guardar" para completar o download do instalador
        
        1. Adicione permissões de execução ao método de ficheiro **.appimage** 1 (Linha de Comando):
            
            1. Abra o Terminal Linux
            
            1. Digite o seguinte no Terminal para ir à pasta Downloads
            
                1. cd ~/Downloads/
                
            1. Digite o seguinte no Terminal para tornar a AppImage executável
            
                1. chmod +x **Dev-Tools-Pack-Installer.AppImage**
                
            1. Digite o seguinte no Terminal para executar o instalador
            
                1. ./Dev-Tools-Pack-Installer.AppImage
        
        1. Adicione permissões de execução ao método de ficheiro **.appimage** 2 (UI):
        
            1. Clique no botão direito no ficheiro .appimage e selecione propriedades
            
            1. Separador de Permissões Abertas
            
            1. Verifique a caixa 'Permitir a execução de ficheiros como um programa'
            
            1. Feche as propriedades e abra o ficheiro .appimage

1. Na página **installor de instalação Dev Tools Pack Installer,** clique na **licença 'Ver'** para ver os contratos de licença de cada pacote de software incluído no instalador. Se aceitar os termos dos contratos de licença, verifique a caixa e clique em **Seguinte**.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-license-agreements.png" alt-text="Tela do contrato de licença no instalador.":::

1. Clique em **Declaração de Privacidade** para rever a Declaração de Privacidade da Microsoft. Se concordar com os termos da declaração de privacidade e quiser enviar dados de diagnóstico para a Microsoft, selecione **Sim** e clique em **Seguinte**. Caso contrário, selecione **No** e clique em **Seguinte**.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-privacy-statement.png" alt-text="Ecrã de acordo de declaração de privacidade no instalador.":::

1. Na página **'Configurar componentes',** selecione as ferramentas opcionais que pretende instalar (as ferramentas obrigatórias serão instaladas por predefinição).

    1. Se estiver a trabalhar com o Azure Percept Audio SoM, que faz parte do Azure Percept DK, certifique-se de instalar o Intel OpenVino Toolkit e o Miniconda3.

    1. Clique **em Instalar** para proceder à instalação.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-configure-components.png" alt-text="Ecrã instalador mostrando pacotes de software disponíveis.":::

1. Após a instalação bem sucedida de todos os componentes selecionados, o assistente procede à página 'Completar a página **'Assistente de configuração'.** Clique **em Terminar** para sair do instalador.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-finish.png" alt-text="Ecrã de conclusão do instalador.":::

## <a name="docker-status-check"></a>Verificação do estado do estivador

Se o instalador o notificar para verificar se o Docker Desktop está num bom estado de funcionamento, consulte os seguintes passos:

   1. Windows:
   
      1. Expandir ícones escondidos da bandeja do sistema:
      
         1. Expandir ícones ocultos de bandeja de sistema se estiver escondido:

            :::image type="content" source="./media/dev-tools-installer/system-tray.png" alt-text="Bandeja do Sistema.":::
         
         1. Verifique se o ícone do Ambiente de Trabalho do Docker mostra 'Docker Desktop está a funcionar':

            :::image type="content" source="./media/dev-tools-installer/docker-status-running.png" alt-text="Estado do Docker.":::
         
         1. Se não vir o ícone acima listado na bandeja do sistema, lance o Docker Desktop a partir do menu inicial.
         
         1. Se o Docker te pedir para reiniciares, tudo bem fechar o instalador e relançar depois de um reboot ter terminado e o Docker estar em funcionamento. Quaisquer aplicações de terceiros instaladas com sucesso devem ser detetadas e não serão reinstaladas automaticamente.

## <a name="next-steps"></a>Passos seguintes

Confira o [repositório avançado de desenvolvimento Azure Percept](https://github.com/microsoft/azure-percept-advanced-development) para começar com desenvolvimento avançado para Azure Percept DK.