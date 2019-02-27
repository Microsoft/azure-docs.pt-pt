---
title: Transferir itens do marketplace do Azure | Documentos da Microsoft
description: O operador da nuvem pode transferir itens do marketplace do Azure para a minha implementação do Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/14/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: c3c577ce2483c5de3aa458c7fe4b6fced418f684
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882948"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Transferir itens do marketplace do Azure para o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Como um operador de cloud, pode transferir itens do Azure Marketplace e disponibilizá-los no Azure Stack. São os itens que pode escolher de uma lista organizada dos itens de Azure Marketplace previamente testada e suportados para trabalhar com o Azure Stack. Itens adicionais com freqüência são adicionados a esta lista, então, continue a verificar para o novo conteúdo. 

Existem dois cenários para ligar ao Azure Marketplace: 

- **Um cenário conectado** -requer que o seu ambiente do Azure Stack para estar ligado à internet. Utilize o portal do Azure Stack para localizar e transferir itens. 
- **Um cenário de desligado ou parcialmente conectado** -que requer a aceder à Internet usando a ferramenta de distribuição de mercado para transferir itens do marketplace. Em seguida, transfere seus downloads para a instalação do Azure Stack desligada. Este cenário utiliza o PowerShell.

Ver [itens de Azure Marketplace para o Azure Stack](azure-stack-marketplace-azure-items.md) para obter uma lista dos itens de marketplace, pode baixar.


## <a name="connected-scenario"></a>Cenário ligado
Se o Azure Stack estabelece ligação à internet, pode utilizar o portal de administração para transferir itens do marketplace.

### <a name="prerequisites"></a>Pré-requisitos
A implementação do Azure Stack tem de ter conectividade à internet e estar [registado com o Azure](azure-stack-register.md).

### <a name="use-the-portal-to-download-marketplace-items"></a>Utilizar o portal para transferir itens do marketplace  
1. Inicie sessão no portal de administrador do Azure Stack.

2.  Reveja o espaço de armazenamento disponível antes de transferir itens do marketplace. Mais tarde, ao selecionar itens para download, pode comparar o tamanho do download para a capacidade de armazenamento disponível. Se a capacidade é limitada, considere as opções para [gerir o espaço disponível](azure-stack-manage-storage-shares.md#manage-available-space). 

    Para consultar o espaço disponível, na **gestão da região** selecione a região que pretende explorar e, em seguida, aceda à **fornecedores de recursos** > **armazenamento**.

    [![Reveja o espaço de armazenamento](media/azure-stack-download-azure-marketplace-item/storagesm.png "reveja o espaço de armazenamento")](media/azure-stack-download-azure-marketplace-item/storage.png#lightbox)

    
3. Abra a pilha do Azure Marketplace e ligar ao Azure. Para tal, selecione **gestão de Marketplace**e, em seguida, selecione **adicionar a partir do Azure**.

    [![Adicionar a partir do Azure](media/azure-stack-download-azure-marketplace-item/marketplacesm.png "adicionar a partir do Azure")](media/azure-stack-download-azure-marketplace-item/marketplace.png#lightbox)

    O portal apresenta a lista de itens disponíveis para download no Azure Marketplace. Pode clicar em cada item para ver a respetiva descrição e informações adicionais sobre ele, inclusive seu tamanho de download. 

    [![Marketplace list](media/azure-stack-download-azure-marketplace-item/image03sm.png "Marketplace list")](media/azure-stack-download-azure-marketplace-item/image03.png#lightbox)

4. Selecione o item que pretende e, em seguida, selecione **transferir**. Tempo de transferência variam.

    [![Transferir a mensagem](media/azure-stack-download-azure-marketplace-item/image04.png "mensagem de Download")](media/azure-stack-download-azure-marketplace-item/image04.png#lightbox)

    Depois de concluída a transferência, pode implementar o novo item do marketplace como um operador do Azure Stack ou o usuário.

5. Para implementar o item de transferido, selecione **+ criar um recurso**e, em seguida, pesquise entre as categorias para o novo item do marketplace. Em seguida, selecione o item para iniciar o processo de implantação. O processo varia para itens do marketplace diferentes. 

## <a name="disconnected-or-a-partially-connected-scenario"></a>Desligado ou um cenário parcialmente conectado

Se o Azure Stack está no modo desligado e sem ligação à internet, vai utilizar o PowerShell e o *ferramenta de distribuição de mercado* para transferir os itens do marketplace para um computador com ligação à internet. Em seguida, transferir os itens ao seu ambiente do Azure Stack. Num ambiente desligado, não é possível transferir itens do marketplace com o portal do Azure Stack. 

Também pode ser utilizada a ferramenta de distribuição de mercado num cenário conectado. 

Existem duas partes para este cenário:
- **Parte 1:** Transfira a partir do Azure Marketplace. No computador com acesso à internet configurar o PowerShell, transfira a ferramenta de distribuição e, em seguida, transfira o formulário de itens no Azure Marketplace.  
- **Parte 2:** Carregar e publicar no Marketplace do Azure Stack. Mover os ficheiros que transferiu para o ambiente do Azure Stack, importá-los para o Azure Stack e, em seguida, publique-os no Azure Stack Marketplace.  


### <a name="prerequisites"></a>Pré-requisitos
- A implementação do Azure Stack tem de ser [registado com o Azure](azure-stack-register.md).  

- O computador que tem conectividade à internet tem de ter **módulo do PowerShell do Azure Stack versão 1.2.11** ou superior. Se ainda não existir, [instalar módulos PowerShell específicos do Azure Stack](azure-stack-powershell-install.md).  

- Para ativar a importação de um item do mercado transferido, o [ambiente do PowerShell para o operador do Azure Stack](azure-stack-powershell-configure-admin.md) tem de ser configurado.  

- Tem de ter uma [conta de armazenamento](azure-stack-manage-storage-accounts.md) no Azure Stack que tem um contentor publicamente acessível (que é um blob de armazenamento). Utilize o contentor como armazenamento temporário para os ficheiros de Galeria de itens do marketplace. Se não estiver familiarizado com as contas de armazenamento e contentores, veja [trabalhar com blobs - portal do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) na documentação do Azure.

- A ferramenta de distribuição de mercado é transferida durante o primeiro procedimento. 

- Pode instalar [AzCopy](../storage/common/storage-use-azcopy.md) para download ideal desempenho, mas isso não é necessária.

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Utilize a ferramenta de distribuição de mercado para transferir itens do marketplace

1. Num computador com uma ligação à Internet, abra uma consola do PowerShell como administrador.

2. Adicione a conta do Azure que utilizou para registar o Azure Stack. Para adicionar a conta, na execução do PowerShell `Add-AzureRmAccount` sem quaisquer parâmetros. Lhe for pedido que introduza as credenciais de conta do Azure e poderá ter de utilizar a autenticação de 2 fatores com base na configuração da sua conta.

3. Se tiver várias subscrições, execute o seguinte comando para selecionar a que utilizou para o registo:  

   ```PowerShell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Baixe a versão mais recente da ferramenta de distribuição de mercado, utilizando o seguinte script:  

   ```PowerShell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master

   ```

5. Importe o módulo de distribuição e, em seguida, inicie a ferramenta, executando os comandos seguintes. Substitua `Destination folder path` com uma localização para armazenar os ficheiros que transfere do Azure Marketplace.   

   ```PowerShell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes" 
   ```

6. Quando a ferramenta é executada, verá uma tela semelhante à imagem seguinte, com a lista de itens do marketplace disponível:

   [![Pop-up itens do Azure Marketplace](media/azure-stack-download-azure-marketplace-item/image05.png "itens do Azure Marketplace")](media/azure-stack-download-azure-marketplace-item/image05.png#lightbox)

7. Selecione o item que pretende transferir e anote o *versão*. Pode manter os *Ctrl* tecla para selecionar várias imagens. Fazer referência a *versão* ao importar o item no próximo procedimento. 
   
   Também pode filtrar a lista de imagens, utilizando o **adicionar critérios** opção.

8. Selecione **OK**e, em seguida, reveja e aceite os termos legais. 

9. O tempo que demora a transferência depende do tamanho do item. Depois de concluída a transferência, o item está disponível na pasta que especificou no script. O download inclui um arquivo VHD (para máquinas virtuais) ou um ficheiro. zip (para extensões de máquina virtual). Pode também incluir um pacote de galeria no *.azpkg* formato, que é simplesmente um ficheiro. zip.

10. Se o download falhar, pode tentar novamente executando novamente o cmdlet PowerShell seguinte:

    ```powershell
    Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes”
    ```

    Antes de tentar novamente, remova a pasta de produto em que a transferência falhou. Por exemplo, se o script de download falhar durante o download para `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1`, remova o `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1` pasta, em seguida, volte a executar o cmdlet.
 
### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1811-and-higher"></a>O download de importar e publicar no Azure Marketplace de pilha (1811 e superior)

1. Tem de mover os ficheiros que tiver [transferido anteriormente](#use-the-marketplace-syndication-tool-to-download-marketplace-items) localmente para que fiquem disponíveis para o seu ambiente do Azure Stack. A ferramenta de distribuição de mercado também deve estar disponível para o ambiente do Azure Stack, uma vez que precisa usar a ferramenta para executar a operação de importação.

   A imagem seguinte mostra um exemplo de estrutura de pasta. `D:\downloadfolder` contém todos os itens do marketplace transferido. Cada subpasta é um item do mercado (por exemplo, `microsoft.custom-script-linux-arm-2.0.3`), chamado pelo ID de produto. Dentro de cada sub-pasta é conteúdo baixado do item marketplace.

   [![Estrutura de diretório de download do Marketplace](media/azure-stack-download-azure-marketplace-item/mp1sm.png "estrutura de diretório de download do Marketplace")](media/azure-stack-download-azure-marketplace-item/mp1.png#lightbox)

2. Siga as instruções em [este artigo](azure-stack-powershell-configure-admin.md) para configurar a sessão do PowerShell de operador do Azure Stack. 

3. Importe o módulo de distribuição e, em seguida, inicie a ferramenta de distribuição de mercado ao executar o seguinte script:

   ```PowerShell
   $credential = Get-Credential -Message "Enter the azure stack operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -AzsCredential $credential
   ```

   O `-origin` parâmetro especifica a pasta de nível superior que contém todos os produtos transferidos; por exemplo, `"D:\downloadfolder"`.

   O `-AzsCredential` parâmetro é opcional. É utilizado para renovar o token de acesso, se tiver expirado. Se o `-AzsCredential` parâmetro não for especificado e o token expirar, recebe um pedido para introduzir as credenciais de operador.

    > [!Note]  
    > AD FS só suporta a autenticação interativa com identidades de utilizador. Se um objeto de credencial é necessário tem de utilizar um principal de serviço (SPN). Para obter mais informações sobre como configurar um principal de serviço com o Azure Stack e o AD FS que o seu serviço de gestão de identidade, consulte [gerir principal de serviço para o AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

4. Após o script ser concluído com êxito, o item deve estar disponível no mercado de pilha do Azure.

### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1809-and-lower"></a>O download de importar e publicar no Azure Marketplace de pilha (1809 e inferior)

1. Os ficheiros de imagens de máquinas virtuais ou modelos de soluções que tenha [transferido anteriormente](#use-the-marketplace-syndication-tool-to-download-marketplace-items) devem ser disponibilizados localmente para o seu ambiente do Azure Stack.  

2. Utilize o portal de administração para carregar o pacote de item do marketplace (o arquivo .azpkg) e a imagem de disco rígido virtual (o ficheiro. vhd) para o armazenamento de Blobs do Azure Stack. Carregamento do pacote e ficheiros de disco torna-os disponíveis para o Azure Stack para poder publicar o item mais tarde para a pilha do Azure Marketplace.

   Carregamento requer que tenha uma conta de armazenamento com um contentor publicamente acessível (consulte os pré-requisitos para este cenário).  
   1. No portal de administração do Azure Stack, aceda a **todos os serviços** e, em seguida, sob o **dados + armazenamento** categoria, selecione **contas de armazenamento**.  
   
   2. Selecione uma conta de armazenamento da sua subscrição e, em seguida, em **serviço BLOB**, selecione **contentores**.  
      [![Serviço blob](media/azure-stack-download-azure-marketplace-item/blob-service.png "serviço Blob")](media/azure-stack-download-azure-marketplace-item/blob-service.png#lightbox)  
   
   3. Selecione o contentor que pretende utilizar e, em seguida, selecione **carregue** para abrir o **carregar blob** painel.  
      [![Container](media/azure-stack-download-azure-marketplace-item/container.png "Container")](media/azure-stack-download-azure-marketplace-item/container.png#lightbox)  
   
   4. No painel de blob de carregamento, navegue para os ficheiros de pacote e um disco para carregar para o armazenamento e, em seguida, selecione **carregar**: [![Carregue](media/azure-stack-download-azure-marketplace-item/uploadsm.png "carregar")](media/azure-stack-download-azure-marketplace-item/upload.png#lightbox)  

   5. Ficheiros que carrega são apresentados no painel de contentor. Selecione um ficheiro e, em seguida, copie o URL a partir da **propriedades do Blob** painel. Irá utilizar este URL no passo seguinte quando importar o item do marketplace para o Azure Stack.  Na imagem seguinte, o contentor está *test-armazenamento de BLOBs* e o ficheiro é *Microsoft.WindowsServer2016DatacenterServerCore ARM.1.0.801.azpkg*.  O ficheiro de URL é *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*.  
      [![Propriedades do blob](media/azure-stack-download-azure-marketplace-item/blob-storagesm.png "propriedades do Blob")](media/azure-stack-download-azure-marketplace-item/blob-storage.png#lightbox)  

3. Importe a imagem VHD para o Azure Stack, utilizando o **Add-AzsPlatformimage** cmdlet. Quando utilizar este cmdlet, substitua a *publicador*, *oferecer*e outros valores de parâmetros com os valores da imagem que está a importar. 

   Pode obter o *publicador*, *oferecer*, e *sku* valores da imagem a partir do arquivo de texto que transfere com o arquivo AZPKG. O arquivo de texto é armazenado na localização de destino. O *versão* valor é a versão observada ao transferir o item a partir do Azure no procedimento anterior. 
 
   No script de exemplo seguinte, são utilizados valores para o Windows Server 2016 Datacenter - máquina de virtual de Server Core. O valor para *- Osuri* é um caminho de exemplo para a localização de armazenamento de BLOBs para o item.

   Como alternativa para esse script, pode utilizar o [procedimento descrito neste artigo](azure-stack-add-vm-image.md#add-a-vm-image-through-the-portal) para importar o. Imagem VHD com o portal do Azure.

   ```PowerShell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.vhd"  
   ```

   **Sobre os modelos de solução:** Alguns modelos podem incluir um pequeno de 3 MB. Ficheiro VHD com o nome **fixed3.vhd**. Não tem de importar esse ficheiro para o Azure Stack. Fixed3.vhd.  Este ficheiro é fornecido com alguns modelos de solução para satisfazer os requisitos de publicação para o Azure Marketplace.

   Reveja a descrição de modelos e transferir e, em seguida, importar os requisitos adicionais, como VHDs, que são necessárias para trabalhar com o modelo de solução.  
   
   **Sobre as extensões:** Quando trabalha com extensões de imagem de máquina virtual, utilize os seguintes parâmetros:
   - *Publicador*
   - *Tipo*
   - *Versão*  

   Não usar *oferecem* para extensões.   


4.  Utilizar o PowerShell para publicar o item do marketplace no Azure Stack, utilizando o **Add-AzsGalleryItem** cmdlet. Por exemplo:  
    ```PowerShell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     –Verbose
    ```
5. Depois de publicar um item da galeria, agora está disponível para utilização. Para confirmar o item da galeria é publicado, aceda a **todos os serviços**e, em seguida, sob o **gerais** categoria, selecione **Marketplace**.  Se seu download é um modelo de solução, certifique-se de que adicione qualquer imagem VHD dependente para esse modelo de solução.  
  [![Marketplace de exibição](media/azure-stack-download-azure-marketplace-item/view-marketplacesm.png "marketplace do Vista")](media/azure-stack-download-azure-marketplace-item/view-marketplace.png#lightbox)  

Com o lançamento do Azure Stack do PowerShell 1.3.0 pode agora adicionar extensões de Máquina Virtual. Por exemplo:

```PowerShell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
```

## <a name="next-steps"></a>Passos Seguintes

[Criar e publicar um item do mercado](azure-stack-create-and-publish-marketplace-item.md)