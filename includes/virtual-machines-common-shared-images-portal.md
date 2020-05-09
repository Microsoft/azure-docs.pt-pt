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
ms.openlocfilehash: 0d5947f669b600b544cd7e5265e2cce8de118374
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82789007"
---
## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens

Uma galeria de imagens é o recurso principal usado para permitir a partilha de imagens. Os caracteres permitidos para o nome da Galeria são letras maiúsculas ou minúsculas, dígitos, pontos e períodos. O nome da galeria não pode conter traços.  Os nomes das galerias devem ser únicos dentro da sua subscrição. 

O exemplo seguinte cria uma galeria chamada *myGallery* no grupo de recursos *myGalleryRG.*

1. Inicie sessão no portal do Azure em https://portal.azure.com.
1. Utilize o tipo de galeria de **imagens partilhadas** na caixa de pesquisa e selecione galeria de **imagem partilhada** nos resultados.
1. Na página da galeria de **imagens partilhadas,** clique em **Adicionar**.
1. Na página da **galeria de imagens partilhadas Create,** selecione a subscrição correta.
1. No **grupo Recursos,** selecione **Criar novo** e digitar *myGalleryRG* para o nome.
1. Em **Nome**, escreva *myGallery* para o nome da galeria.
1. Deixe o padrão para **a Região**.
1. Pode escrever uma pequena descrição da galeria, como a minha galeria de *imagens para ser testada.* e, em seguida, clique em **Rever + criar**.
1. Depois dos passes de validação, selecione **Criar**.
1. Quando a implementação estiver terminada, selecione **Ir para o recurso**.


## <a name="create-an-image-definition"></a>Criar uma definição de imagem 

As definições de imagem criam um agrupamento lógico para imagens. São utilizados para gerir informação sobre as versões de imagem que são criadas dentro delas. Os nomes de definição de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e períodos. Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, consulte [definições](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions)de imagem .

Crie a definição de imagem da galeria dentro da sua galeria. Neste exemplo, a imagem da galeria chama-se *myImageDefinition*.

1. Na página para a sua nova galeria de imagens, selecione **Adicionar uma nova definição** de imagem a partir do topo da página. 
1. No **Add nova definição de imagem para galeria**de imagem partilhada , para **Região**, selecione *East US*.
1. Para o nome da **definição de imagem,** escreva *myImageDefinition*.
1. Para **o sistema operativo,** selecione a opção correta com base no VM de origem.  
1. Para **a geração VM,** selecione a opção com base na sua fonte VM. Na maioria dos casos, esta será a *Gen 1.* Para mais informações, consulte [Suporte para Geração 2 VMs](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2).
1. Para **o estado do sistema operativo,** selecione a opção com base no vM de origem. Para mais informações, consulte [Generalizado e especializado.](../articles/virtual-machines/linux/shared-image-galleries.md#generalized-and-specialized-images)
1. Para **Editor,** escreva *myPublisher*. 
1. Para **oferta**, escreva *myOffer*.
1. Para **SKU,** escreva *mySKU*.
1. Quando terminar, selecione **Review + criar**.
1. Depois da definição de imagem passar a validação, selecione **Criar**.
1. Quando a implementação estiver terminada, selecione **Ir para o recurso**.


## <a name="create-an-image-version"></a>Criar uma versão de imagem

Criar uma versão de imagem a partir de uma imagem gerida. Neste exemplo, a versão de imagem é *de 1.0.0* e é replicada tanto para centros de dados do *Centro Central Oeste* dos EUA como para os centros de dados do Centro Central Sul *dos EUA.* Ao escolher as regiões-alvo para replicação, lembre-se que também tem de incluir a região *de origem* como alvo de replicação.

Os caracteres permitidos para a versão de imagem são números e períodos. Os números devem estar dentro do alcance de um inteiro de 32 bits. Formato: *MajorVersion*. *Versão menor.* *Patch*.

Os passos para criar uma versão de imagem são ligeiramente diferentes, dependendo se a fonte é uma imagem generalizada ou um instantâneo de um VM especializado. 

### <a name="option-generalized"></a>Opção: Generalizado

1. Na página para a sua definição de imagem, selecione **Adicionar versão** a partir do topo da página.
1. Na **Região,** selecione a região onde a sua imagem gerida está armazenada. As versões de imagem precisam de ser criadas na mesma região que a imagem gerida de onde são criadas.
1. Para **Nome**, tipo *1.0.0*. O nome da versão de imagem deve *seguir-se principalmente*. *menor.* formato *de patch* usando inteiros. 
1. Na **imagem Fonte,** selecione a imagem gerida de origem a partir da queda.
1. Em **Excluir do mais recente,** deixe o valor padrão de *Nº*.
1. Para **data de fim de vida,** selecione uma data do calendário que é de alguns meses no futuro.
1. Na **Replicação,** deixe a contagem de **réplicas Padrão** como 1. É preciso replicar-se para a região de origem, por isso deixe a primeira réplica como padrão e escolha uma segunda região de réplica para ser *o Leste dos EUA.*
1. Quando terminar, selecione **Rever + criar**. O Azure validará a configuração.
1. Quando a versão da imagem passar a validação, selecione **Criar**.
1. Quando a implementação estiver terminada, selecione **Ir para o recurso**.

Pode levar algum tempo a replicar a imagem a todas as regiões-alvo.

### <a name="option-specialized"></a>Opção: Especializado

1. Na página para a sua definição de imagem, selecione **Adicionar versão** a partir do topo da página.
1. Na **Região,** selecione a região onde o seu instantâneo está armazenado. As versões de imagem precisam de ser criadas na mesma região que a fonte de que são criadas.
1. Para **Nome**, tipo *1.0.0*. O nome da versão de imagem deve *seguir-se principalmente*. *menor.* formato *de patch* usando inteiros. 
1. No **instantâneo do disco OS,** selecione o instantâneo do vM de origem a partir da queda. Se o VM de origem tiver um disco de dados que gostaria de incluir, selecione o número **LUN** correto a partir da queda e, em seguida, selecione o instantâneo do disco de dados para instantâneo de disco de **dados**. 
1. Em **Excluir do mais recente,** deixe o valor padrão de *Nº*.
1. Para **data de fim de vida,** selecione uma data do calendário que é de alguns meses no futuro.
1. Na **Replicação,** deixe a contagem de **réplicas Padrão** como 1. É preciso replicar-se para a região de origem, por isso deixe a primeira réplica como padrão e escolha uma segunda região de réplica para ser *o Leste dos EUA.*
1. Quando terminar, selecione **Rever + criar**. O Azure validará a configuração.
1. Quando a versão da imagem passar a validação, selecione **Criar**.
1. Quando a implementação estiver terminada, selecione **Ir para o recurso**.

## <a name="share-the-gallery"></a>Partilhar a galeria

Recomendamos que partilhe o acesso ao nível da galeria de imagens. O que se segue leva-o através da partilha da galeria que acabou de criar.

1. Abra o [portal Azure.](https://portal.azure.com)
1. No menu à esquerda, selecione **Grupos de Recursos**. 
1. Na lista de grupos de recursos, selecione **myGalleryRG**. A lâmina do seu grupo de recursos abrirá.
1. No menu à esquerda da página **myGalleryRG,** selecione Controlo de **Acesso (IAM)**. 
1. Em **adicionar uma atribuição de funções,** selecione **Adicionar**. O painel **de atribuição de um papel adicionar** será aberto. 
1. Em **Função,** selecione **Reader**.
1. Sob **atribuír acesso a**, deixe o padrão do utilizador, grupo ou diretor de serviço Da **Azure AD**.
1. Em **Select**, escreva o endereço de e-mail da pessoa que gostaria de convidar.
1. Se o utilizador estiver fora da sua organização, verá a mensagem **Este utilizador será enviado um e-mail que lhes permite colaborar com a Microsoft.** Selecione o utilizador com o endereço de e-mail e, em seguida, clique em **Guardar**.

Se o utilizador estiver fora da sua organização, receberá um convite por e-mail para se juntar à organização. O utilizador precisa de aceitar o convite, para depois poder ver a galeria e todas as definições e versões de imagem na sua lista de recursos.

