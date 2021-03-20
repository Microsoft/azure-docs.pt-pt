---
title: Início rápido para gerir partilhas de ficheiros do Azure com o portal do Azure
description: Veja como criar e gerir ações de ficheiros Azure no portal Azure. Crie uma conta de armazenamento, crie uma partilha de ficheiros Azure e use a sua partilha de ficheiros Azure.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6a88124397812f7599ce54b46b23d22e626cf520
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94629823"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-the-azure-portal"></a>Início Rápido: Criar e gerir partilhas de ficheiros do Azure com o portal do Azure 
[Ficheiros do Azure](storage-files-introduction.md) é o sistema de ficheiros na cloud fácil de utilizar da Microsoft. As partilhas de ficheiros do Azure podem ser montadas no Windows, Linux e macOS. Este guia orienta-o pelas noções básicas de utilizar partilhas de ficheiros do Azure com o [Portal do Azure](https://portal.azure.com/).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure
Para criar uma partilha de ficheiros do Azure:

1. Selecione a conta de armazenamento a partir do dashboard.
2. Na página da conta de armazenamento, na secção **Serviços**, selecione **Ficheiros**.
    ![Uma captura de ecrã da secção Serviços da conta de armazenamento; selecione o Serviço de Ficheiros](media/storage-how-to-use-files-portal/create-file-share-1.png)

3. No menu no topo da página de **serviço do Ficheiro,** clique em **Partilhar Ficheiros**. É apresentada a página **Nova partilha de ficheiros**.
4. Em **Nome**, escreva *myshare*.
5. Clique em **OK** para criar a partilha de ficheiros do Azure.

Os nomes de partilha só podem ter letras minúsculas, números e hífenes, mas não podem começar com um hífen. Para obter detalhes completos sobre a nomenclatura de partilhas de ficheiros e ficheiros, veja [Naming and Referencing Shares, Directories, Files, and Metadata (Nomenclatura e Referência de Partilhas, Diretórios, Ficheiros e Metadados)](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Utilizar a partilha de ficheiros do Azure
A Azure Files fornece três métodos de trabalho com ficheiros e pastas dentro da sua partilha de ficheiros Azure: o protocolo padrão [do Bloco de Mensagens do Servidor (SMB)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)da indústria, o protocolo sistema de ficheiros de rede (NFS) (pré-visualização) e o [protocolo Rest](/rest/api/storageservices/file-service-rest-api)do Ficheiro . 

Para montar uma partilha de ficheiros com SMB, veja o documento seguinte com base no seu SO:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-from-the-azure-portal"></a>Utilizar uma partilha de ficheiros do Azure a partir do portal do Azure
Todos os pedidos através do Portal do Azure são efetuados com a API REST de Ficheiros, o que lhe permite criar, modificar e eliminar ficheiros e diretórios nos clientes sem acesso SMB. É possível trabalhar diretamente com o protocolo File REST (isto é, o REST HTTP artesanal chama-se), mas a forma mais comum (para além de utilizar o portal Azure) para utilizar o protocolo File REST é utilizar o [módulo Azure PowerShell,](storage-how-to-use-files-powershell.md)o [Azure CLI,](storage-how-to-use-files-cli.md)ou um SDK de armazenamento Azure, que fornece um invólucro agradável em torno do protocolo File REST no idioma de script/programação da sua escolha. 

Esperamos que a maioria dos utilizadores de Ficheiros Azure queira trabalhar com a sua partilha de ficheiros Azure sobre o protocolo SMB, pois isso permite-lhes utilizar as aplicações e ferramentas existentes que esperam poder usar, mas existem várias razões pelas quais é vantajoso utilizar a API de rest de ficheiros em vez de SMB, tais como:

- Se precisar de fazer uma alteração rápida à partilha de ficheiros do Azure enquanto viaja, por exemplo, a partir de um portátil, tablet ou dispositivo móvel sem acesso SMB.
- Se precisar de executar um script ou aplicação a partir de um cliente que não pode montar uma partilha SMB, como clientes no local que não têm a porta 445 desbloqueada.
- Se estiver a tirar partido de recursos sem servidor, como o serviço [Funções do Azure](../../azure-functions/functions-overview.md). 

Os exemplos seguintes mostram como utilizar o portal do Azure para manipular a partilha de ficheiros do Azure com o protocolo REST de Ficheiros. 

Agora que criou uma partilha de ficheiros do Azure, pode montá-la com SMB no [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) ou [macOS](storage-how-to-use-files-mac.md). Em alternativa, pode trabalhar com a partilha de ficheiros do Azure com o portal do Azure. 

#### <a name="create-a-directory"></a>Criar um diretório
Para criar um novo diretório com o nome *myDirectory* na raiz da partilha de ficheiros do Azure:

1. Na página **Serviço de Ficheiros**, selecione a partilha de ficheiros **myshare**. É aberta a página da partilha de ficheiros.
2. No menu na parte superior da página, selecione **+ Adicionar diretório**. É apresentada a página **Novo diretório**.
3. Escreva *myDirectory* e, em seguida, clique em **OK**.

#### <a name="upload-a-file"></a>Carregar um ficheiro 
Para demonstrar o carregamento de um ficheiro, primeiro tem de criar ou selecionar um ficheiro a ser carregado. Pode fazê-lo da forma que considerar adequada. Depois de selecionar o ficheiro que quer carregar:

1. Clique no diretório **myDirectory**. É aberto o painel **myDirectory**.
2. No menu na parte superior, clique em **Carregar**. É aberto o painel **Carregar ficheiros**.  
    ![Uma captura de ecrã do painel Carregar ficheiros](media/storage-how-to-use-files-portal/upload-file-1.png)

3. Clique no ícone de pasta para abrir uma janela para procurar os ficheiros locais. 
4. Selecione um ficheiro e, em seguida, clique em **Abrir**. 
5. Na página **Carregar ficheiros**, verifique o nome de ficheiro e, em seguida, clique em **Carregar**.
6. Quando terminar, o ficheiro deve aparecer na lista na página **myDirectory**.

#### <a name="download-a-file"></a>Transferir um ficheiro
Pode transferir uma cópia do ficheiro que carregou ao clicar com o botão direito do rato no ficheiro. Depois de clicar no botão de transferência, a experiência exata dependerá do sistema operativo e do browser que estiver a utilizar.

## <a name="clean-up-resources"></a>Limpar os recursos
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [O que são os Ficheiros do Azure?](storage-files-introduction.md)