---
title: Início rápido para gerir partilhas de ficheiros do Azure com o portal do Azure
description: Utilize este início rápido para aprender a utilizar o portal do Azure para gerir os Ficheiros do Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 69e7dba06fce14c2411a82dfade344ba1b14a6d3
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763879"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-the-azure-portal"></a>Início rápido: Criar e gerir partilhas de ficheiros do Azure com o portal do Azure 
[Ficheiros do Azure](storage-files-introduction.md) é o sistema de ficheiros na cloud fácil de utilizar da Microsoft. As partilhas de ficheiros do Azure podem ser montadas no Windows, Linux e macOS. Este guia orienta-o pelas noções básicas de utilizar partilhas de ficheiros do Azure com o [Portal do Azure](https://portal.azure.com/).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure
Para criar uma partilha de ficheiros do Azure:

1. Selecione a conta de armazenamento a partir do dashboard.
2. Na página da conta de armazenamento, na secção **Serviços**, selecione **Ficheiros**.
    ![Uma captura de ecrã da secção Serviços da conta de armazenamento; selecione o Serviço de Ficheiros](media/storage-how-to-use-files-portal/create-file-share-1.png)

3. No menu na parte superior da página **Serviço de Ficheiros**, clique em **+ Partilha de ficheiros**. É apresentada a página **Nova partilha de ficheiros**.
4. Em **Nome**, escreva *myshare*.
5. Clique em **OK** para criar a partilha de ficheiros do Azure.

Os nomes das partilhas só podem ter letras minúsculas, números e hífenes, mas não podem começar com um hífen. Para obter detalhes completos sobre a nomenclatura de partilhas de ficheiros e ficheiros, veja [Naming and Referencing Shares, Directories, Files, and Metadata (Nomenclatura e Referência de Partilhas, Diretórios, Ficheiros e Metadados)](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Utilizar a partilha de ficheiros do Azure
O serviço Ficheiros do Azure fornece dois métodos para utilizar ficheiros e pastas na sua partilha de ficheiros do Azure : o [protocolo SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) padrão do setor e o [protocolo REST de Ficheiros](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Para montar uma partilha de ficheiros com SMB, veja o documento seguinte com base no seu SO:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-from-the-azure-portal"></a>Utilizar uma partilha de ficheiros do Azure a partir do portal do Azure
Todos os pedidos realizados através do Portal do Azure são feitos com a API REST File, o que lhe permite criar, modificar e eliminar ficheiros e diretórios em clientes que não tenham acesso SMB. É possível trabalho diretamente com o protocolo de REST de ficheiros diretamente (ou seja, handcrafting REST HTTP chama-se), mas a forma mais comum (além com o portal do Azure) para utilizar o RESTANTE do arquivo de protocolo é usar o [módulo Azure PowerShell](storage-how-to-use-files-powershell.md), o [CLI do Azure](storage-how-to-use-files-cli.md), ou um SDK de armazenamento do Azure, os quais fornecem um bom invólucro o protocolo de REST de ficheiros na linguagem de programação/criação de scripts da sua preferência. 

Contamos que a maioria das utilizações dos Ficheiros do Azure irão querer trabalhar com a partilha de ficheiros do Azure através do protocolo SMB, pois permite-lhes utilizar as aplicações e ferramentas existentes, mas existem vários motivos pelos quais é vantajoso utilizar a API REST de Ficheiros em vez do SMB, tais como:

- Se precisar de fazer uma alteração rápida à partilha de ficheiros do Azure enquanto viaja, por exemplo, a partir de um portátil, tablet ou dispositivo móvel sem acesso SMB.
- Se precisar de executar um script ou aplicação a partir de um cliente que não pode montar uma partilha SMB, como clientes no local que não têm a porta 445 desbloqueada.
- Se estiver a tirar partido de recursos sem servidor, como o serviço [Funções do Azure](../../azure-functions/functions-overview.md). 

Os exemplos seguintes mostram como utilizar o portal do Azure para manipular a partilha de ficheiros do Azure com o protocolo REST de Ficheiros. 

Agora que já criou uma partilha de ficheiros do Azure, pode montá-la com SMB em [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) ou [macOS](storage-how-to-use-files-mac.md). Em alternativa, pode trabalhar com a partilha de ficheiros do Azure com o portal do Azure. 

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

## <a name="clean-up-resources"></a>Limpar recursos
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [O que são os Ficheiros do Azure?](storage-files-introduction.md)
