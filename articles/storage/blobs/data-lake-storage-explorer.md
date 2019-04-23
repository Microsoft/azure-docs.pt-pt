---
title: 'Início rápido: Utilize o Explorador de armazenamento do Azure para gerir dados na geração 2 de armazenamento do Azure Data Lake '
description: Neste início rápido, irá aprender a utilizar o Explorador de armazenamento do Azure para criar um sistema de ficheiros numa conta de geração 2 de armazenamento do Azure Data Lake, bem como um diretório e um ficheiro. Em seguida, saiba como transferir o ficheiro para o computador local e como visualizar todos os ficheiros num diretório.
services: storage
author: tamram
ms.subservice: data-lake-storage-gen2
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 12/05/2018
ms.author: tamram
ms.openlocfilehash: b6add3c2402df9c0db2284945269d3fbaa1c65e1
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995477"
---
# <a name="quickstart-use-azure-storage-explorer-to-manage-data-in-an-azure-data-lake-storage-gen2-account"></a>Início rápido: Utilize o Explorador de armazenamento do Azure para gerir dados numa conta de geração 2 de armazenamento do Azure Data Lake

Neste início rápido, irá aprender a utilizar [Explorador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para criar um diretório e um blob. Em seguida, saiba como transferir o blob para o computador local e como visualizar todos os blobs num diretório. Também irá aprender a criar um instantâneo de um blob, gerir políticas de acesso de diretório e criar uma assinatura de acesso partilhado.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Este início rápido requer que instale o Explorador de Armazenamento do Azure. Para instalar o Explorador de Armazenamento do Azure para Windows, Macintosh ou Linux, consulte [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-storage-explorer"></a>Inicie sessão no Explorador de armazenamento

Na primeira execução, é mostrada a janela **Explorador de Armazenamento do Microsoft Azure – Ligar**. Embora o Explorador de armazenamento proporciona várias formas de ligar a contas de armazenamento, apenas uma forma é atualmente suportada para gerenciar ACLs.

|Tarefa|Objetivo|
|---|---|
|Adicionar uma Conta do Azure | Redireciona-o para a página de início de sessão de organizações para o autenticar no Azure. Atualmente, é o método de autenticação suportado apenas se pretender gerir e defino os ACLs. |

Selecione **Adicionar uma Conta do Azure** e clique em **Iniciar sessão...**. Siga as mensagens no ecrã para iniciar sessão na conta do Azure.

![Explorador de Armazenamento do Microsoft Azure – Janela Ligar](media/storage-quickstart-blobs-storage-explorer/connect.png)

Quando a ligação for concluída, o Explorador de Armazenamento do Azure é carregado com o separador **Explorador** mostrado. Esta vista fornece-lhe informações detalhadas de todas as contas de armazenamento do Azure, bem como do armazenamento local configurado através do [Emulador de Armazenamento do Azure](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), contas do [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ou ambientes do [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

![Explorador de Armazenamento do Microsoft Azure – Janela Ligar](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-file-system"></a>Criar um sistema de ficheiros

BLOBs são sempre carregados para um diretório. Isto permite organizar grupos de blobs, como organiza os ficheiros em pastas no seu computador.

Para criar um diretório, expanda a conta de armazenamento que criou no passo seguinte. Selecione **contentor de BLOBs**, clique com botão direito e selecione **contentor de BLOBs criar**. Introduza o nome para o seu sistema de ficheiros. Quando terminar, prima **Enter** para criar o sistema de ficheiros. Assim que o diretório de blob foi criado com êxito, é apresentado no **contentor de BLOBs** pasta para a conta de armazenamento selecionada.

![Explorador de armazenamento do Microsoft Azure – criar um sistema de ficheiros](media/storage-quickstart-blobs-storage-explorer/creating-a-filesystem.png)

## <a name="upload-blobs-to-the-directory"></a>Carregar blobs para o diretório

O armazenamento de blobs suporta blobs de blocos, blobs de acréscimo e blobs de páginas. Os ficheiros VHD utilizados nas cópias de segurança de VMs IaaS são blobs de páginas. Os blobs de acréscimo são utilizados para registo, como quando quer escrever num ficheiro e continuar a adicionar mais informações. A maioria dos ficheiros guardados no armazenamento de Blobs são blobs de blocos.

Da faixa de opções de diretório, selecione **carregar**. Esta operação dá-lhe a opção de carregar uma pasta ou um ficheiro.

Escolha os ficheiros ou pasta a carregar. Selecione o **tipo de blob**. As opções aceitáveis são **Anexar**, **Página** ou **Blob de blocos**.

Se carregar um ficheiro .vhd ou .vhdx, selecione **Carregar ficheiros .vhd/.vhdx como blobs de páginas (recomendado)**.

Na **carregar para pasta (opcional)** campo a um nome de pasta para armazenar os ficheiros ou pastas numa pasta sob o diretório. Se for escolhida nenhuma pasta, os ficheiros são carregados diretamente sob o diretório.

![Explorador de Armazenamento do Microsoft Azure – Carregar um blob](media/storage-quickstart-blobs-storage-explorer/uploadblob.png)

Quando seleciona **OK**, os ficheiros selecionados são colocados em fila para carregamento. Quando o carregamento estiver concluído, os resultados são apresentados na janela **Atividades**.

## <a name="view-blobs-in-a-directory"></a>Ver blobs num diretório

Na **Explorador de armazenamento do Azure** aplicação, selecione um diretório numa conta de armazenamento. O painel principal apresenta uma lista dos blobs no diretório selecionado.

![Explorador de armazenamento do Microsoft Azure – listar os blobs num diretório](media/storage-quickstart-blobs-storage-explorer/listblobs.png)

## <a name="download-blobs"></a>Transferir blobs

Para transferir blobs através do **Explorador de Armazenamento do Azure**, com um blob selecionado, selecione **Transferir** no friso. É aberta uma caixa de diálogo de ficheiro que lhe permite introduzir um nome de ficheiro. Selecione **Guardar** para iniciar a transferência de um blob para a localização local.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu a transferir ficheiros entre um disco local e o armazenamento de Blobs do Azure através do **Explorador de Armazenamento do Azure**. Para saber mais sobre como definir ACLs em seus arquivos e diretórios, avance para o nosso procedimentos sobre o assunto.

> [!div class="nextstepaction"]
> [Como definir ACLs em arquivos e diretórios](data-lake-storage-how-to-set-permissions-storage-explorer.md)
