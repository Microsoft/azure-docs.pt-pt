---
title: Upload vHD ficheiro para Azure DevTest Labs usando Storage Explorer
description: Upload vHD ficheiro para a conta de armazenamento do laboratório usando o Microsoft Azure Storage Explorer
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170377"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Upload vHD ficheiro para a conta de armazenamento do laboratório usando o Microsoft Azure Storage Explorer

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Nos Laboratórios Azure DevTest, os ficheiros VHD podem ser usados para criar imagens personalizadas, que são usadas para fornecer máquinas virtuais. Este artigo ilustra como usar o [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) para enviar um ficheiro VHD para a conta de armazenamento de um laboratório. Depois de ter carregado o ficheiro VHD, a [secção Depassos Seguintes](#next-steps) lista alguns artigos que ilustram como criar uma imagem personalizada a partir do ficheiro VHD carregado. Para obter mais informações sobre discos e VHDs em Azure, consulte [Introdução aos discos geridos](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Os seguintes passos passam por você através do upload de um ficheiro VHD para DevTest Labs usando [o Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Descarregue e instale a versão mais recente do Microsoft Azure Storage Explorer](https://www.storageexplorer.com).

1. Obtenha o nome da conta de armazenamento do laboratório usando o portal Azure:

    1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Selecione **Todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.
    
    1. Da lista de laboratórios, selecione o laboratório desejado.  
    
    1. Na lâmina do laboratório, **selecione Configuração**. 
    
    1. Na lâmina de **configuração** do laboratório, selecione **Imagens Personalizadas (VHDs)**.
    
    1. Na lâmina de **imagens Personalizadas,** Selecione **+Adicionar**. 
    
    1. Na lâmina de **imagem Personalizada,** selecione **VHD**.
    
    1. Na lâmina **VHD,** **selecione Carregar um VHD utilizando powerShell**.
    
        ![Upload VHD usando PowerShell][0]
    
    1. O Upload de uma imagem utilizando a lâmina **PowerShell** exibe uma chamada para o cmdlet **Add-AzureVhd.** O primeiro parâmetro *(Destino)* contém o nome da conta de armazenamento para o laboratório no seguinte formato:
    
        `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...`

    1. Tome nota do nome da conta de armazenamento como é utilizado em etapas posteriores.
    
1. Ligue-se a uma conta de subscrição Azure utilizando o Storage Explorer.

    > [!TIP] 
    > 
    > O Storage Explorer suporta várias opções de ligação. Esta secção ilustra a ligação a uma conta de armazenamento associada à sua subscrição Azure. Para ver as outras opções de ligação suportadas pelo Storage Explorer, consulte o artigo, [Começando com o Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).
 
    1. Abra o Explorador de Armazenamento.
    
    1. No Explorer de Armazenamento, selecione **as definições da Conta Azure**. 
    
        ![Definições de conta do Azure][1]
    
    1. O painel esquerdo exibe as contas da Microsoft nas suas contas. Para ligar a outra conta, selecione **Adicionar uma conta** e siga as caixas de diálogo para iniciar sessão com uma conta Microsoft que esteja associada a, pelo menos, uma subscrição Azure ativa.
    
        ![Adicionar uma conta][2]
    
    1. Quando inicia sessão com êxito numa conta Microsoft, o painel esquerdo será preenchido com as subscrições do Azure associadas a essa conta. Selecione as subscrições do Azure com as quais pretende trabalhar e, em seguida, selecione **Aplicar**. (Selecionando **Todas as subscrições** alterna a seleção de todas ou nenhumadas subscrições do Azure listadas.)
    
        ![Selecionar subscrições do Azure][3]
    
    1. O painel esquerdo apresenta as contas de armazenamento associadas às subscrições do Azure selecionadas.
    
        ![Subscrições do Azure selecionadas][4]

1. Localize a conta de armazenamento do laboratório:

    1. No Storage Explorer deixou o painel, localize e expanda o nó para a subscrição azure que é proprietária do laboratório.
    
    1. Sob o nó da subscrição, expandir Contas de **Armazenamento.**

    1. Expanda o nó da conta de armazenamento do laboratório para revelar nós para **recipientes blob,** **partilhas**de **ficheiros, filas**e **tabelas.**
    
    1. Expanda o nó **blob containers.**
    
    1. Selecione o recipiente de bolhas de upload para exibir o seu conteúdo no painel certo.
        
        ![Diretório de upload][5]

1. Faça upload do ficheiro VHD utilizando o Storage Explorer:

    1. No painel direito do Storage Explorer, deve ver uma lista das bolhas no recipiente de **uploads** blob da conta de armazenamento do laboratório. Na barra de ferramentas do editor blob, **selecione Upload** 
        
        ![Botão Carregar][6]
    
    1. A partir do menu de entrega de **upload,** selecione **ficheiros upload...**.
    
    1. No diálogo de **ficheiros Upload,** selecione a elipse.
        
        ![Selecione ficheiro][8]  

    1. Nos **ficheiros Select para carregar** o diálogo, navegue para o ficheiro VHD desejado, selecione-o e, em seguida, selecione **Open**.
    
    1. Quando for devolvido ao diálogo dos **ficheiros Upload,** altere o **tipo Blob** para **'Page Blob**.
    
    1. Selecione **Upload**.

        ![Selecione ficheiro][9]  
    
    1. O painel de registo de **atividades** do Explorador de Armazenamento mostra o estado de descarregamento (juntamente com links para cancelar o upload). O processo de upload de um ficheiro VHD pode ser longo dependendo do tamanho do ficheiro VHD e da sua velocidade de ligação. 

        ![Estado do ficheiro de upload][10]  

## <a name="next-steps"></a>Passos seguintes

- [Crie uma imagem personalizada em Azure DevTest Labs a partir de um ficheiro VHD usando o portal Azure](devtest-lab-create-template.md)
- [Crie uma imagem personalizada em Azure DevTest Labs a partir de um ficheiro VHD usando powerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

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
