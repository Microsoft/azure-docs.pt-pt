---
title: Tutorial para copiar dados via SMB do Azure Data Box | Microsoft Docs
description: Saiba como copiar dados para o Azure Data Box via SMB
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 204e89bdf8c660a7e96438f8cb3b8a18aeaec306
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91742388"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-smb-preview"></a>Tutorial: Copiar dados do Azure Data Box via SMB (Pré-visualização)

Este tutorial descreve como ligar e copiar dados do Data Box para um servidor no local com a IU da Web local. O dispositivo Data Box contém os dados exportados da sua conta de Armazenamento do Microsoft Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Ligar ao Data Box
> * Copiar dados do Data Box

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Fez o pedido para o Azure Data Box.
    - Para um pedido de importação, veja o [Tutorial: Encomendar o Azure Data Box](data-box-deploy-ordered.md).
    - Para um pedido de exportação, veja o [Tutorial: Encomendar o Azure Data Box](data-box-deploy-export-ordered.md).
2. Recebeu o Data Box e o estado da encomenda no portal é **Entregue**.
3. Tem um computador anfitrião para o qual pretende copiar os dados do Data Box. O computador anfitrião tem de
   * Executar um [sistema operativo suportado](data-box-system-requirements.md).
   * Estar ligado a uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. Se uma ligação de 10 GbE não estiver disponível, utilize uma ligação de dados de 1 GbE, mas as velocidades de cópia serão afetadas.

## <a name="connect-to-data-box"></a>Ligar ao Data Box

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

Se estiver a utilizar um computador anfitrião do Windows Server, execute estes passos para se ligar ao Data Box.

1. O primeiro passo é autenticar e iniciar uma sessão. Aceda a **Ligar e copiar**. Selecione **Obter credenciais** para obter as credenciais de acesso para as partilhas associadas à sua conta de armazenamento. 

    ![Obter credenciais de partilhas](media/data-box-deploy-export-copy-data/get-share-credentials-1.png)

2. Na caixa de diálogo Aceder à partilha e copiar os dados, copie o **Nome de utilizador** e a **Palavra-passe** correspondentes à partilha. Selecione **OK**.
    
    ![Obter credenciais de partilhas 2](media/data-box-deploy-export-copy-data/get-share-credentials-2.png)

3. Para aceder às partilhas associadas à conta de armazenamento (*exportbvtdataset2* no exemplo seguinte) a partir do computador anfitrião, abra uma janela de comandos. Na linha de comandos, escreva:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Consoante o seu formato de dados, os caminhos de partilha são os seguintes:
    - Blob de blocos do Azure – `\\169.254.143.85\exportbvtdataset2_BlockBlob`
    - Blob de páginas do Azure – `\\169.254.143.85\exportbvtdataset2_PageBlob`
    - Ficheiros do Azure – `\\169.254.143.85\exportbvtdataset2_AzFile`

4. Introduza a palavra-passe da partilha quando lhe for pedido. O exemplo seguinte mostra a ligação a uma partilha através do comando anterior.

    ```
    C:\Users\Databoxuser>net use \\169.254.143.85\exportbvtdataset2_BlockBlob /u:exportbvtdataset2
    Enter the password for 'exportbvtdataset2' to connect to '169.254.143.85':
    The command completed successfully.
    ```

4. Prima Windows + R. Na janela **Executar**, especifique o `\\<device IP address>`. Selecione **OK** para abrir o Explorador de Ficheiros.
    
    ![Ligar à partilha através do Explorador de Ficheiros](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-1.png)

    Agora deve conseguir ver as partilhas como pastas.
    
    ![Ligar à partilha através do Explorador de Ficheiros 2](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-2.png)

    
Se estiver a utilizar um cliente Linux, utilize o comando a seguir para montar a partilha SMB. O parâmetro “vers” abaixo é a versão do SMB compatível com o sistema anfitrião Linux. Ligue a versão apropriada no comando a seguir. Para as versões do SMB compatíveis com o Data Box, veja a secção [Sistemas de ficheiros suportados pelos clientes Linux](https://docs.microsoft.com/azure/databox/data-box-system-requirements#supported-file-systems-for-linux-clients) 

```console
sudo mount -t nfs -o vers=2.1 169.254.143.85:/exportbvtdataset2_BlockBlob /home/databoxubuntuhost/databox
```

## <a name="copy-data-from-data-box"></a>Copiar dados do Data Box

Assim que estiver ligado às partilhas do Data Box, o passo seguinte é copiar os dados.

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]


 Depois de ligar à partilha SMB, inicie a cópia dos dados. Pode utilizar qualquer ferramenta de cópia de ficheiros compatível com SMB, como o Robocopy, para copiar os dados. É possível iniciar várias tarefas com o Robocopy. 


Para obter mais informações sobre o comando do Robocopy, aceda a [Robocopy e alguns exemplos](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

Quando a cópia estiver concluída, aceda ao **Dashboard** e verifique o espaço utilizado e o espaço livre no seu dispositivo.

Pode agora proceder ao envio do seu Data Box para a Microsoft.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box, como:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Ligar ao Data Box
> * Copiar dados do Data Box

Avance para o tutorial seguinte para saber como enviar o Data Box de volta para a Microsoft.

> [!div class="nextstepaction"]
> [Enviar o Azure Data Box para a Microsoft](./data-box-deploy-export-picked-up.md)

