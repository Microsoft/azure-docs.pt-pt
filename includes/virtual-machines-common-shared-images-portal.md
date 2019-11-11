---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 729e757c69887bbdce324e2d8383c970995dc94a
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903651"
---
## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão no portal do Azure em https://portal.azure.com.

> [!NOTE]
> Se você se registrou para usar galerias de imagens compartilhadas durante a visualização, talvez seja necessário registrar novamente o provedor de `Microsoft.Compute`. Abra [Cloud Shell](https://shell.azure.com/bash) e digite: `az provider register -n Microsoft.Compute`

## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens

Uma galeria de imagens é o principal recurso usado para habilitar o compartilhamento de imagens. Os caracteres permitidos para o nome da galeria são letras maiúsculas ou minúsculas, dígitos, pontos e pontos. O nome da Galeria não pode conter traços.  Os nomes da Galeria devem ser exclusivos em sua assinatura. 

O exemplo a seguir cria uma galeria chamada *myGallery* no grupo de recursos *myGalleryRG* .

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Use a **Galeria de imagens** de tipo compartilhado na caixa de pesquisa e selecione Galeria de **imagens compartilhadas** nos resultados.
1. Na página **Galeria de imagens compartilhadas** , clique em **criar**.
1. Selecione a assinatura correta.
1. Em **grupo de recursos**, selecione **criar novo** e digite *myGalleryRG* para o nome.
1. Em **nome**, digite *myGallery* para o nome da galeria.
1. Deixe o padrão para **região**.
1. Você pode digitar uma breve descrição da galeria, como *minha galeria de imagens para teste.* e, em seguida, clique em **revisar + criar**.
1. Depois que a validação for aprovada, selecione **criar**.
1. Quando a implantação for concluída, selecione **ir para o recurso**.
   
## <a name="create-an-image-definition"></a>Criar uma definição de imagem 

Definições de imagem crie um agrupamento lógico para imagens. Eles são usados para gerenciar informações sobre as versões de imagem que são criadas dentro delas. Os nomes de definição de imagem podem ser compostos de letras maiúsculas ou minúsculas, dígitos, pontos, traços e pontos. Para obter mais informações sobre os valores que você pode especificar para uma definição de imagem, consulte [definições de imagem](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Crie a definição de imagem da Galeria dentro da sua galeria. Neste exemplo, a imagem da galeria é denominada *myImageDefinition*.

1. Na página da nova galeria de imagens, selecione **Adicionar uma nova definição de imagem** na parte superior da página. 
1. Para **nome da definição da imagem**, digite *myImageDefinition*.
1. Para **sistema operacional**, selecione a opção correta com base em sua VM de origem.
1. Para **geração de VM**, selecione a opção com base na VM de origem. Na maioria dos casos, essa será a *Gen 1*. Para obter mais informações, consulte [suporte para VMs de geração 2](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2).
1. Para **estado do sistema operacional**, selecione a opção com base na VM de origem. Para obter mais informações, consulte [generalizado e especializado](../articles/virtual-machines/linux/shared-image-galleries.md#generalized-and-specialized-images).
1. Para o **Publicador**, digite *mypublisher*. 
1. Para **oferta**, digite *myoffer*.
1. Para **SKU**, digite *mySKU*.
1. Quando terminar, selecione **revisar + criar**.
1. Após a definição da imagem passar na validação, selecione **criar**.
1. Quando a implantação for concluída, selecione **ir para o recurso**.


## <a name="create-an-image-version"></a>Criar uma versão de imagem

Crie uma versão de imagem de uma imagem gerenciada. Neste exemplo, a versão da imagem é *1.0.0* e é replicada para os data centers do *Oeste EUA Central* e *do Sul EUA Central* . Ao escolher regiões de destino para replicação, lembre-se de que você também precisa incluir a região de *origem* como um destino para replicação.

Os caracteres permitidos para a versão da imagem são números e pontos. Os números devem estar dentro do intervalo de um inteiro de 32 bits. Formato: *MajorVersion*. *MinorVersion*. *Patch*.

As etapas para criar uma versão de imagem são um pouco diferentes, dependendo se a origem é uma imagem generalizada ou um instantâneo de uma VM especializada. 

### <a name="option-generalized"></a>Opção: generalizada

1. Na página da definição de imagem, selecione **Adicionar versão** na parte superior da página.
1. Em **região**, selecione a região onde a imagem gerenciada está armazenada. As versões de imagem precisam ser criadas na mesma região da imagem gerenciada da qual são criadas.
1. Para **nome**, digite *1.0.0*. O nome da versão da imagem deve seguir a *principal*. *secundária*. formato de *patch* usando inteiros. 
1. Em **imagem de origem**, selecione a imagem gerenciada de origem na lista suspensa.
1. Em **excluir da versão mais recente**, deixe o valor padrão de *não*.
1. Em **data de término da vida útil**, selecione uma data do calendário que esteja a alguns meses no futuro.
1. Em **replicação**, deixe a **contagem de réplicas padrão** como 1. Você precisa replicar para a região de origem, portanto, deixe a primeira réplica como padrão e, em seguida, escolha uma segunda região de réplica como *leste dos EUA*.
1. Quando terminar, selecione **revisar + criar**. O Azure validará a configuração.
1. Quando a versão da imagem passar na validação, selecione **criar**.
1. Quando a implantação for concluída, selecione **ir para o recurso**.

Pode levar algum tempo para replicar a imagem para todas as regiões de destino.

### <a name="option-specialized"></a>Opção: especializada

1. Na página da definição de imagem, selecione **Adicionar versão** na parte superior da página.
1. Em **região**, selecione a região em que o instantâneo está armazenado. As versões de imagem precisam ser criadas na mesma região da origem da qual são criadas.
1. Para **nome**, digite *1.0.0*. O nome da versão da imagem deve seguir a *principal*. *secundária*. formato de *patch* usando inteiros. 
1. Em **instantâneo de disco do so**, selecione o instantâneo da VM de origem na lista suspensa. Se a VM de origem tiver um disco de dados que você gostaria de incluir, selecione o número de **LUN** correto na lista suspensa e, em seguida, selecione o instantâneo do disco de dados para o **instantâneo do disco de dados**. 
1. Em **excluir da versão mais recente**, deixe o valor padrão de *não*.
1. Em **data de término da vida útil**, selecione uma data do calendário que esteja a alguns meses no futuro.
1. Em **replicação**, deixe a **contagem de réplicas padrão** como 1. Você precisa replicar para a região de origem, portanto, deixe a primeira réplica como padrão e, em seguida, escolha uma segunda região de réplica como *leste dos EUA*.
1. Quando terminar, selecione **revisar + criar**. O Azure validará a configuração.
1. Quando a versão da imagem passar na validação, selecione **criar**.
1. Quando a implantação for concluída, selecione **ir para o recurso**.

## <a name="share-the-gallery"></a>Compartilhar a Galeria

Recomendamos que você compartilhe o acesso no nível da Galeria de imagens. O seguinte guia você pelo compartilhamento da galeria que você acabou de criar.

1. Abra o [Portal do Azure](https://portal.azure.com).
1. No menu à esquerda, selecione grupos de **recursos**. 
1. Na lista de grupos de recursos, selecione **myGalleryRG**. A folha do seu grupo de recursos será aberta.
1. No menu à esquerda da página **myGalleryRG** , selecione **controle de acesso (iam)** . 
1. Em **Adicionar uma atribuição de função**, selecione **Adicionar**. O painel **Adicionar uma atribuição de função** será aberto. 
1. Em **função**, selecione **leitor**.
1. Em **atribuir acesso a**, deixe o padrão de **usuário, grupo ou entidade de serviço do Azure ad**.
1. Em **selecionar**, digite o endereço de email da pessoa que você deseja convidar.
1. Se o usuário estiver fora de sua organização, você verá a mensagem **este usuário receberá um email que permite colaborar com a Microsoft.** Selecione o usuário com o endereço de email e clique em **salvar**.

Se o usuário estiver fora de sua organização, ele receberá um convite por email para ingressar na organização. O usuário precisa aceitar o convite, então ele poderá ver a galeria e todas as definições e versões de imagem na lista de recursos.

