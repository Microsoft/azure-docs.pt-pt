---
title: Carregar arquivo VHD para Azure DevTest Labs usando Gerenciador de Armazenamento
description: Carregar o arquivo VHD na conta de armazenamento do laboratório usando Gerenciador de Armazenamento do Microsoft Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: defafdd5809b7e537b3b9abb78f8cb63d0033c16
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170377"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Carregar o arquivo VHD na conta de armazenamento do laboratório usando Gerenciador de Armazenamento do Microsoft Azure

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

No Azure DevTest Labs, os arquivos VHD podem ser usados para criar imagens personalizadas, que são usadas para provisionar máquinas virtuais. Este artigo ilustra como usar [Gerenciador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) para carregar um arquivo VHD na conta de armazenamento de um laboratório. Depois de carregar o arquivo VHD, a [seção próximas etapas](#next-steps) lista alguns artigos que ilustram como criar uma imagem personalizada a partir do arquivo VHD carregado. Para obter mais informações sobre discos e VHDs no Azure, consulte [introdução aos Managed disks](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Instruções passo a passo

As etapas a seguir orientam você pelo carregamento de um arquivo VHD para DevTest Labs usando o [Gerenciador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Baixe e instale a versão mais recente do Gerenciador de armazenamento do Microsoft Azure](https://www.storageexplorer.com).

1. Obtenha o nome da conta de armazenamento do laboratório usando o portal do Azure:

    1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** na lista.
    
    1. Na lista de laboratórios, selecione o laboratório desejado.  
    
    1. Na folha do laboratório, selecione **configuração**. 
    
    1. Na folha **configuração** do laboratório, selecione **imagens personalizadas (VHDs)** .
    
    1. Na folha **imagens personalizadas** , selecione **+ Adicionar**. 
    
    1. Na folha **imagem personalizada** , selecione **VHD**.
    
    1. Na folha **VHD** , selecione **carregar um VHD usando o PowerShell**.
    
        ![Carregar VHD usando o PowerShell][0]
    
    1. A folha **carregar uma imagem usando o PowerShell** exibe uma chamada para o cmdlet **Add-AzureVhd** . O primeiro parâmetro (*destino*) contém o nome da conta de armazenamento para o laboratório no seguinte formato:
    
        `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...`

    1. Anote o nome da conta de armazenamento conforme ele é usado em etapas posteriores.
    
1. Conecte-se a uma conta de assinatura do Azure usando Gerenciador de Armazenamento.

    > [!TIP] 
    > 
    > Gerenciador de Armazenamento dá suporte a várias opções de conexão. Esta seção ilustra a conexão a uma conta de armazenamento associada à sua assinatura do Azure. Para ver as outras opções de conexão com suporte pelo Gerenciador de Armazenamento, consulte o artigo [introdução ao Gerenciador de armazenamento](../vs-azure-tools-storage-manage-with-storage-explorer.md).
 
    1. Abra o Explorador de Armazenamento.
    
    1. Em Gerenciador de Armazenamento, selecione **configurações de conta do Azure**. 
    
        ![Definições de conta do Azure][1]
    
    1. O painel esquerdo exibe as contas da Microsoft nas quais você fez logon. Para ligar a outra conta, selecione **Adicionar uma conta** e siga as caixas de diálogo para iniciar sessão com uma conta Microsoft que esteja associada a, pelo menos, uma subscrição Azure ativa.
    
        ![Adicionar uma conta][2]
    
    1. Quando inicia sessão com êxito numa conta Microsoft, o painel esquerdo será preenchido com as subscrições do Azure associadas a essa conta. Selecione as subscrições do Azure com as quais pretende trabalhar e, em seguida, selecione **Aplicar**. (A seleção de **todas as assinaturas** alterna a seleção de todas ou nenhuma das assinaturas do Azure listadas.)
    
        ![Selecionar subscrições do Azure][3]
    
    1. O painel esquerdo apresenta as contas de armazenamento associadas às subscrições do Azure selecionadas.
    
        ![Subscrições do Azure selecionadas][4]

1. Localize a conta de armazenamento do laboratório:

    1. No painel Gerenciador de Armazenamento esquerdo, localize e expanda o nó da assinatura do Azure que possui o laboratório.
    
    1. No nó da assinatura, expanda **contas de armazenamento**.

    1. Expanda o nó da conta de armazenamento do laboratório para revelar nós para **contêineres de blob**, **compartilhamentos de arquivos**, **filas**e **tabelas**.
    
    1. Expanda o nó **contêineres de blob** .
    
    1. Selecione o contêiner de blob de carregamentos para exibir seu conteúdo no painel direito.
        
        ![Carregar diretório][5]

1. Carregue o arquivo VHD usando Gerenciador de Armazenamento:

    1. No painel Gerenciador de Armazenamento direito, você deve ver uma lista dos BLOBs no contêiner de blob de **carregamentos** da conta de armazenamento do laboratório. Na barra de ferramentas do editor de BLOB, selecione **carregar** 
        
        ![Botão Carregar][6]
    
    1. No menu suspenso **carregar** , selecione **carregar arquivos...** .
    
    1. Na caixa de diálogo **carregar arquivos** , selecione as reticências.
        
        ![Selecionar ficheiro][8]  

    1. Na caixa de diálogo **selecionar arquivos para carregar** , navegue até o arquivo VHD desejado, selecione-o e, em seguida, selecione **abrir**.
    
    1. Quando retornado para a caixa de diálogo **carregar arquivos** , altere o **tipo de blob** para **blob de páginas**.
    
    1. Selecione **Upload**.

        ![Selecionar ficheiro][9]  
    
    1. O painel **log de atividades** Gerenciador de armazenamento mostra o status de download (juntamente com links para cancelar o carregamento). O processo de carregar um arquivo VHD pode ser demorado dependendo do tamanho do arquivo VHD e da velocidade de conexão. 

        ![Status do arquivo de upload][10]  

## <a name="next-steps"></a>Passos seguintes

- [Criar uma imagem personalizada no Azure DevTest Labs de um arquivo VHD usando o portal do Azure](devtest-lab-create-template.md)
- [Criar uma imagem personalizada no Azure DevTest Labs de um arquivo VHD usando o PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

[0]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-image-using-psh.png
[1]: ./media/devtest-lab-upload-vhd-using-storage-explorer/settings-icon.png
[2]: ./media/devtest-lab-upload-vhd-using-storage-explorer/add-account-link.png
[3]: ./media/devtest-lab-upload-vhd-using-storage-explorer/subscriptions-list.png
[4]: ./media/devtest-lab-upload-vhd-using-storage-explorer/storage-accounts-list.png
[5]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-dir.png
[6]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-button.png
[7]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-files.png
[8]: ./media/devtest-lab-upload-vhd-using-storage-explorer/select-file.png
[9]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-file.png
[10]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-status.png
