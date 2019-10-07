---
title: Habilitar a virtualização aninhada em uma VM de modelo no Azure Lab Services | Microsoft Docs
description: Saiba como habilitar a virtualização aninhada em uma VM de modelo no Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 9a52fd958d646af5edd09065e9acf95796c8ce33
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981998"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Habilitar a virtualização aninhada em uma máquina virtual de modelo no Azure Lab Services
Este artigo aborda como configurar a virtualização aninhada em uma máquina virtual de modelo no Azure Lab Services. A virtualização aninhada é usada em Azure Lab Services quando cada aluno em uma classe requer vários computadores.
 
## <a name="considerations"></a>Considerações
Antes de configurar um laboratório com virtualização aninhada, aqui estão algumas coisas a serem levadas em consideração.

- Ao criar um novo laboratório, selecione **médio (virtualização aninhada)** ou tamanhos **grandes** para o tamanho da máquina virtual. Esses tamanhos de máquina virtual dão suporte à virtualização aninhada. 
- Escolha um tamanho que fornecerá um bom desempenho para as máquinas virtuais do host e do cliente.  Lembre-se de que, ao usar a virtualização, o tamanho escolhido deve ser adequado para não apenas um computador, mas o host, bem como quaisquer computadores cliente que devam ser executados simultaneamente.
- As máquinas virtuais do cliente não terão acesso aos recursos do Azure, como servidores DNS na rede virtual do Azure.
- A máquina virtual do host requer a instalação para permitir que o computador cliente tenha conectividade com a Internet. 
- As máquinas virtuais do cliente são licenciadas como máquinas independentes. Consulte [Licenciamento da Microsoft](https://www.microsoft.com/licensing/default) para obter informações sobre licenciamento para produtos e sistemas de operações da Microsoft. Verifique os contratos de licenciamento para qualquer outro software que esteja sendo usado antes de configurar o computador de modelo.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Habilitar a virtualização aninhada em uma VM de modelo
As etapas nesta seção se concentram na configuração da virtualização aninhada para o Windows Server 2016 ou o Windows Server 2019. Você usará um script para configurar a máquina de modelo com o Hyper-V. Para uma solução automatizada, consulte scripts em [scripts do Hyper-V dos serviços de laboratório](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV). As etapas a seguir mostrarão como usar o script.

1. Se você estiver usando o Internet Explorer, talvez seja necessário adicionar `https://github.com` à lista de sites confiáveis. 
    1. Abra o Internet Explorer.
    1. Selecione o ícone de engrenagem e escolha **Opções da Internet**.  
    1. Quando a caixa de diálogo **Opções da Internet** for exibida, selecione **segurança**, selecione **sites confiáveis**, clique no botão **sites** .
    1. Quando a caixa de diálogo **sites confiáveis** for exibida, adicione `https://github.com` à lista de sites confiáveis e selecione **fechar**.

        ![Sites confiáveis](../media/how-to-enable-nested-virtualization-template-vm/trusted-sites-dialog.png)
1.  Baixe os arquivos do repositório git conforme descrito nas etapas a seguir.  Como alternativa, o repositório git pode ser clonado do [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git). 
    1. Vá para [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/).
    1. Clique no botão ***clonar ou baixar** .
    1. Clique em **baixar zip**.
    1. Extrair o arquivo ZIP
1. Inicie o **PowerShell** no modo de **administrador** .
1. Na janela do PowerShell, navegue até a pasta com o script baixado. Se você estiver navegando da pasta superior dos arquivos de repositório, o script estará localizado em `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`.
1. Talvez seja necessário alterar a política de execução para executar o script com êxito. Execute o seguinte comando:
    
    ```powershell
    Set-ExecutionPolicy bypass -force 
    ```
1. Execute o script:
    
    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > O script pode exigir que o computador seja reiniciado. Siga as instruções do script e execute novamente o script até que o **script concluído** seja visto na saída.
1. Não se esqueça de redefinir a política de execução. Execute o seguinte comando: 

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Conclusão
Agora seu computador de modelo está pronto para criar máquinas virtuais do Hyper-V. Consulte [criar uma máquina virtual no Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) para obter instruções sobre como criar máquinas virtuais do Hyper-v. Além disso, consulte [o centro de avaliação da Microsoft](https://www.microsoft.com/evalcenter/) para conferir o software e os sistemas operacionais disponíveis.  

## <a name="next-steps"></a>Passos seguintes 
Consulte os seguintes artigos:

- [Como habilitar a virtualização aninhada em uma VM do Azure](../../virtual-machines/windows/nested-virtualization.md) 
- [Instalar a função Hyper-V no Windows Server](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
- [Scripts do Hyper-V para serviços de laboratório](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)
