---
title: Faça upload do ficheiro VHD para Azure DevTest Labs usando o Storage Explorer
description: Faça upload do ficheiro VHD para a conta de armazenamento do laboratório usando o Microsoft Azure Storage Explorer
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d4e421932ebba5d4f389000c12bcf44ac2f37599
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91282532"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Faça upload do ficheiro VHD para a conta de armazenamento do laboratório usando o Microsoft Azure Storage Explorer

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Nos Laboratórios Azure DevTest, os ficheiros VHD podem ser usados para criar imagens personalizadas, que são usadas para alojamento de máquinas virtuais. Este artigo ilustra como usar o [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) para enviar um ficheiro VHD para a conta de armazenamento de um laboratório. Uma vez carregado o seu ficheiro VHD, a [secção passos seguintes](#next-steps) lista alguns artigos que ilustram como criar uma imagem personalizada a partir do ficheiro VHD carregado. Para obter mais informações sobre discos e VHDs em Azure, consulte [Introdução a discos geridos](../virtual-machines/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Os passos seguintes acompanham-no através do upload de um ficheiro VHD para a DevTest Labs utilizando [o Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Descarregue e instale a versão mais recente do Microsoft Azure Storage Explorer](https://www.storageexplorer.com).

1. Obtenha o nome da conta de armazenamento do laboratório usando o portal Azure:

    1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Selecione **Todos os serviços** e, em seguida, selecione **DevTest Labs** da lista.
    
    1. Da lista de laboratórios, selecione o laboratório desejado.  
    
    1. Na lâmina do laboratório, selecione **Configuração**. 
    
    1. Na lâmina de **configuração** do laboratório, selecione **Imagens Personalizadas (VHDs)**.
    
    1. Na lâmina de **imagens personalizadas,** Selecione **+Add**. 
    
    1. Na lâmina **de imagem personalizada,** selecione **VHD**.
    
    1. Na lâmina **VHD,** **selecione Carreque um VHD utilizando o PowerShell**.
    
        ![Upload VHD usando PowerShell][0]
    
    1. O Upload de uma imagem utilizando a lâmina **PowerShell** exibe uma chamada para o **cmdlet Add-AzureVhd.** O primeiro parâmetro *(Destino)* contém o nome da conta de armazenamento do laboratório no seguinte formato:
    
        `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...`

    1. Tome nota do nome da conta de armazenamento, uma vez que é utilizado em etapas posteriores.
    
1. Ligue-se a uma conta de subscrição Azure utilizando o Storage Explorer.

    > [!TIP] 
    > 
    > O Storage Explorer suporta várias opções de ligação. Esta secção ilustra a ligação a uma conta de armazenamento associada à sua subscrição Azure. Para ver as outras opções de ligação suportadas pelo Storage Explorer, consulte o artigo, [Começar com o Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).
 
    1. Abra o Explorador de Armazenamento.
    
    1. No Explorador de Armazenamento, selecione **as definições de Conta Azure**. 
    
        ![Definições de conta do Azure][1]
    
    1. O painel esquerdo exibe as contas da Microsoft a que iniciou sessão. Para ligar a outra conta, selecione **Adicionar uma conta** e siga as caixas de diálogo para iniciar sessão com uma conta Microsoft que esteja associada a, pelo menos, uma subscrição Azure ativa.
    
        ![Adicionar uma conta][2]
    
    1. Quando inicia sessão com êxito numa conta Microsoft, o painel esquerdo será preenchido com as subscrições do Azure associadas a essa conta. Selecione as subscrições do Azure com as quais pretende trabalhar e, em seguida, selecione **Aplicar**. (Selecionar **todas as subscrições** alterna a seleção de todas ou nenhuma das subscrições Azure listadas.)
    
        ![Selecionar subscrições do Azure][3]
    
    1. O painel esquerdo apresenta as contas de armazenamento associadas às subscrições do Azure selecionadas.
    
        ![Subscrições do Azure selecionadas][4]

1. Localize a conta de armazenamento do laboratório:

    1. No painel esquerdo do Storage Explorer, localize e expanda o nó para a subscrição do Azure que é proprietária do laboratório.
    
    1. Sob o nó da subscrição, expanda **as Contas de Armazenamento.**

    1. Expanda o nó de conta de armazenamento do laboratório para revelar nós para **recipientes Blob, Partilhas** de **Ficheiros, Filas** e  **Tabelas**.
    
    1. Expanda o nó **de recipientes Blob.**
    
    1. Selecione o recipiente blob uploads para mostrar o seu conteúdo no painel direito.
        
        ![Diretório de upload][5]

1. Faça o upload do ficheiro VHD utilizando o Storage Explorer:

    1. No painel direito do Storage Explorer, deverá ver uma lista das bolhas no recipiente de bolhas de **uploads** da conta de armazenamento do laboratório. Na barra de ferramentas do editor blob, selecione **Upload** 
        
        ![Screenshot que mostra o painel direito do Explorador de Armazenamento com o botão "Upload" selecionado.][6]
    
    1. A partir do menu de entrega do **Upload,** selecione **Ficheiros upload...**.
    
    1. No diálogo de **ficheiros upload,** selecione a elipse.
        
        ![Screenshot que mostra o diálogo "Carregar ficheiros" com a elipse selecionada.][8]  

    1. Nos **ficheiros Select para carregar** o diálogo, navegue para o ficheiro VHD pretendido, selecione-o e, em seguida, selecione **Abrir**.
    
    1. Quando voltar ao diálogo de **ficheiros upload,** altere **o tipo Blob** para **Page Blob**.
    
    1. Selecione **Carregar**.

        ![Selecione ficheiro][9]  
    
    1. O painel de **registo de atividade do** explorador de armazenamento mostra o estado de descarregamento (juntamente com links para cancelar o upload). O processo de upload de um ficheiro VHD pode ser longo, dependendo do tamanho do ficheiro VHD e da sua velocidade de ligação. 

        ![Estado do ficheiro de upload][10]  

## <a name="next-steps"></a>Passos seguintes

- [Crie uma imagem personalizada em Azure DevTest Labs a partir de um ficheiro VHD usando o portal Azure](devtest-lab-create-template.md)
- [Crie uma imagem personalizada em Azure DevTest Labs a partir de um ficheiro VHD usando PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

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
