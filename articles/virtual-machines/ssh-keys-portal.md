---
title: Criar teclas SSH no portal Azure
description: Aprenda a gerar e armazenar chaves SSH no portal Azure para ligar os VMs Linux.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/31/2020
ms.author: cynthn
ms.openlocfilehash: 39dbf7d9ad933dd47f0a566f02b5e276e4b615a3
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2020
ms.locfileid: "87514447"
---
# <a name="generate-and-store-ssh-keys-in-the-azure-portal"></a>Gerar e armazenar chaves SSH no portal Azure

Se utilizar frequentemente o portal para implantar VMs Linux, pode simplificar a utilização das teclas SSH, criando-as diretamente no portal ou fazendo o upload do computador.

Pode criar uma tecla SSH quando criar um VM e reutilizá-las para outros VMs. Ou pode criar chaves SSH separadamente, para que tenha um conjunto de chaves armazenadas em Azure para se adaptar às necessidades das suas organizações. 

Se tiver as teclas existentes e quiser simplificar a sua utilização no portal, pode carregá-las e armazená-las em Azure para reutilização.

Para obter informações mais detalhadas sobre a criação e utilização de chaves SSH com VMs Linux, consulte (Utilizar as teclas SSH para ligar aos VMs Linux)[./linux/ssh-from-windows.md].

## <a name="generate-new-keys"></a>Gerar novas teclas

1. Abra o portal [Azure]. https://portal.azure.com

1. No topo da página, escreva *SSH* para pesquisar. Em **Mercado,* selecione **teclas SSH**.

1. Na página **chave SSH,** selecione **Criar**.

:::image type="content" source="./media/ssh-keys/portal-sshkey.png" alt-text="Crie um novo grupo de recursos e gere um par de chaves SSH":::

1. No **grupo de recursos** selecione Criar **novos** para criar um novo grupo de recursos para armazenar as suas chaves. Digite um nome para o seu grupo de recursos e selecione **OK**.

1. Na **Região** selecione uma região para guardar as suas chaves. Você pode usar as chaves em qualquer região, esta é apenas a região onde eles serão armazenados.

1. Digite um nome para a sua chave no **nome do par chave.**

1. Na **fonte chave pública SSH,** selecione Gerar fonte de chave **pública**. 

1. Quando terminar, selecione **Rever + criar**.

1. Depois de passar a validação, **selecione Criar**.

1. Em seguida, obterá uma janela pop-up para, selecione **Baixar a tecla privada e criar recurso**. Isto irá descarregar a chave SSH como um ficheiro .pem.

:::image type="content" source="./media/ssh-keys/download-key.png" alt-text="Descarregue a chave privada como um ficheiro .pem":::

1. Uma vez descarregado o ficheiro .pem, é possível movê-lo para algum lugar no seu computador, onde é fácil apontar para o seu cliente SSH.


## <a name="connect-to-the-vm"></a>Ligar à VM

No seu computador local, abra um pedido powerShell e escreva:

```powershell
ssh -i <path to the .pem file> username@<ipaddress of the VM>
```

Por exemplo, escreva:`ssh -i /Downloads/mySSHKey.pem azureuser@123.45.67.890`


## <a name="upload-an-ssh-key"></a>Faça upload de uma chave SSH

Também pode carregar uma chave SSH pública para armazenar em Azure. Para obter informações sobre como criar um par de chaves SSH, consulte [as teclas SSH para ligar aos VMs Linux ](./linux/ssh-from-windows.md).

1. Abra o portal [Azure]. https://portal.azure.com

1. No topo da página, escreva *SSH* para pesquisar. Em **Mercado,* selecione **teclas SSH**.

1. Na página **chave SSH,** selecione **Criar**.

:::image type="content" source="./media/ssh-keys/upload.png" alt-text="Faça o upload de uma chave pública SSH para ser armazenada em Azure":::

1. No **grupo de recursos** selecione Criar **novos** para criar um novo grupo de recursos para armazenar as suas chaves. Digite um nome para o seu grupo de recursos e selecione **OK**.

1. Na **Região** selecione uma região para guardar as suas chaves. Você pode usar as chaves em qualquer região, esta é apenas a região onde eles serão armazenados.

1. Digite um nome para a sua chave no **nome do par chave.**

1. Na **fonte principal do SSH,** selecione Carregar a chave pública **existente**. 

1. Cole todo o conteúdo da chave pública na **tecla upload** e, em seguida, selecione **Review + create**.

1. Após a validação concluída, **selecione Criar**. 

Uma vez que a chave tenha sido carregada, pode optar por usá-la quando criar um VM.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a utilização de teclas SSH com VMs Azure, consulte (Utilizar as teclas SSH para ligar aos VMs Linux[./linux/ssh-from-windows.md].