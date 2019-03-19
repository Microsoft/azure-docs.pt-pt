---
title: Carregar o ficheiro VHD para o Azure DevTest Labs com o Explorador de armazenamento do Microsoft Azure | Documentos da Microsoft
description: Carregar o ficheiro VHD para a conta de armazenamento do laboratório com o Explorador de armazenamento do Microsoft Azure
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 12a21f4fdfb279df154b4995b6191aa0ed54ab43
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57894483"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Carregar o ficheiro VHD para a conta de armazenamento do laboratório com o Explorador de armazenamento do Microsoft Azure

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

No Azure DevTest Labs, ficheiros VHD podem ser utilizados para criar imagens personalizadas, que são utilizadas para aprovisionar máquinas virtuais. Este artigo ilustra como usar [Explorador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) para carregar um ficheiro VHD para a conta de armazenamento do laboratório. Depois de carregar o ficheiro VHD, o [próximos passos secção](#next-steps) apresenta uma lista de alguns artigos que mostram como criar uma imagem personalizada a partir do ficheiro VHD carregado. Para obter mais informações acerca dos discos e VHDs no Azure, consulte [introdução aos discos geridos](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Os seguintes passos guiá-lo por meio de carregar um ficheiro VHD para utilizar o DevTest Labs [Explorador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Transfira e instale a versão mais recente do Microsoft Azure Storage Explorer](https://www.storageexplorer.com).

1. Obter o nome da conta de armazenamento do laboratório com o portal do Azure:

    1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.
    
    1. Na lista de laboratórios, selecione o laboratório pretendido.  
    
    1. No painel do laboratório, selecione **configuração**. 
    
    1. O laboratório **Configuration** painel, selecione **imagens personalizadas (VHDs)**.
    
    1. Sobre o **imagens personalizadas** painel, selecione **+ adicionar**. 
    
    1. Sobre o **Custom image** painel, selecione **VHD**.
    
    1. Sobre o **VHD** painel, selecione **carregar um VHD com o PowerShell**.
    
        ![Carregar o VHD com o PowerShell][0]
    
    1. O **carregue uma imagem com o PowerShell** painel apresenta uma chamada para o **Add-AzureVhd** cmdlet. O primeiro parâmetro (*destino*) contém o nome da conta de armazenamento para o laboratório no seguinte formato:
    
        https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/... 

    1. Tome nota do nome de conta de armazenamento à medida que é utilizada em passos posteriores.
    
1. Ligar a uma conta de subscrição do Azure utilizando o Explorador de armazenamento.

    > [!TIP] 
    > 
    > Explorador de armazenamento suporta várias opções de ligação. Esta secção ilustra a ligar a uma conta de armazenamento associada à subscrição do Azure. Para ver as outras opções de ligação suportadas pelo Explorador de armazenamento, consulte o artigo [introdução ao Explorador de armazenamento](../vs-azure-tools-storage-manage-with-storage-explorer.md).
 
    1. Abra o Explorador de Armazenamento.
    
    1. No Explorador de armazenamento, selecione **definições de conta do Azure**. 
    
        ![Definições de conta do Azure][1]
    
    1. O painel esquerdo apresenta as contas da Microsoft que tenha sessão iniciada. Para ligar a outra conta, selecione **Adicionar uma conta** e siga as caixas de diálogo para iniciar sessão com uma conta Microsoft que esteja associada a, pelo menos, uma subscrição Azure ativa.
    
        ![Adicionar uma conta][2]
    
    1. Quando inicia sessão com êxito numa conta Microsoft, o painel esquerdo será preenchido com as subscrições do Azure associadas a essa conta. Selecione as subscrições do Azure com as quais pretende trabalhar e, em seguida, selecione **Aplicar**. (Selecionar **todas as subscrições** alterna a seleção de todas ou nenhuma das subscrições Azure listadas.)
    
        ![Selecionar subscrições do Azure][3]
    
    1. O painel esquerdo apresenta as contas de armazenamento associadas às subscrições do Azure selecionadas.
    
        ![Subscrições do Azure selecionadas][4]

1. Localize a conta de armazenamento do laboratório:

    1. No painel esquerdo do Explorador de armazenamento, localize e expanda o nó para a subscrição do Azure que possui o laboratório.
    
    1. No nó da subscrição, expanda **contas de armazenamento**.

    1. Expanda o nó de conta de armazenamento do laboratório para revelar nós para **contentores de BLOBs**, **partilhas de ficheiros**, **filas**, e **tabelas**.
    
    1. Expanda a **contentores de BLOBs** nó.
    
    1. Selecione o contentor de blob de carregamentos para exibir seu conteúdo no painel da direita.
        
        ![Carregar o diretório][5]

1. Carregar o ficheiro VHD com o Explorador de armazenamento:

    1. No painel da direita Explorador de armazenamento, deverá ver uma lista dos blobs no **carrega** contentor de BLOBs da conta de armazenamento do laboratório. Na barra de ferramentas do editor para BLOBs, selecione **carregar** 
        
        ![Botão Carregar][6]
    
    1. Do **carregue** menu pendente, selecione **carregar ficheiros...** .
    
    1. Sobre o **carregar ficheiros** caixa de diálogo, selecione as reticências.
        
        ![Selecionar ficheiro][8]  

    1. Sobre o **selecionar ficheiros para carregar** caixa de diálogo, procure o ficheiro VHD pretendido, selecione-o e, em seguida, selecione **aberto**.
    
    1. Quando devolvidos para o **carregar ficheiros** caixa de diálogo, alteração **tipo de Blob** para **BLOBs de páginas**.
    
    1. Selecione **Upload**.

        ![Selecionar ficheiro][9]  
    
    1. O Explorador de armazenamento **registo de atividades** painel mostra o status do download (juntamente com ligações para cancelar o carregamento). O processo de carregamento de um ficheiro VHD pode ser demorado, dependendo do tamanho do ficheiro VHD e sua velocidade da ligação. 

        ![Estado do ficheiro de carregamento][10]  

## <a name="next-steps"></a>Passos Seguintes

- [Criar uma imagem personalizada no Azure DevTest Labs, de um ficheiro VHD com o portal do Azure](devtest-lab-create-template.md)
- [Criar uma imagem personalizada no Azure DevTest Labs, de um ficheiro VHD com o PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

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
