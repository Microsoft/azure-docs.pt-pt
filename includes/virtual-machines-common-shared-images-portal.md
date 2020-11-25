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
ms.openlocfilehash: 1af80f208c72af9434d596f2c1219c08c0e3f719
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016012"
---
## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens

Uma galeria de imagens é o principal recurso utilizado para permitir a partilha de imagens. Os caracteres permitidos para o nome da Galeria são letras maiúsculas ou minúsculas, dígitos, pontos e períodos. O nome da galeria não pode conter traços.  Os nomes das galerias devem ser únicos dentro da sua subscrição. 

O exemplo a seguir cria uma galeria chamada *myGallery* no grupo de recursos *myGalleryRG.*

1. Inicie sessão no portal do Azure em https://portal.azure.com.
1. Utilize o tipo **de galeria de imagens partilhada** na caixa de pesquisa e selecione galeria de **imagens partilhada** nos resultados.
1. Na página da **galeria de imagens Partilhada,** clique em **Adicionar**.
1. Na página da **galeria de imagens partilhada Create,** selecione a subscrição correta.
1. No **grupo de recursos,** selecione **Criar novo** e digitar *myGalleryRG* para o nome.
1. Em **Nome,** *escreva myGallery* para o nome da galeria.
1. Deixe o padrão para **a Região**.
1. Pode escrever uma breve descrição da galeria, como a *Minha galeria de imagens para testes.* e, em seguida, clique em **Rever + criar**.
1. Após os passes de validação, **selecione Criar**.
1. Quando a implementação estiver concluída, selecione **Ir para o recurso**.


## <a name="create-an-image-definition"></a>Criar uma definição de imagem 

As definições de imagem criam um agrupamento lógico para imagens. São utilizados para gerir informações sobre as versões de imagem que são criadas dentro delas. Os nomes da definição de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e períodos. Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, consulte [definições de imagem](../articles/virtual-machines/windows/shared-image-galleries.md#image-definitions).

Crie a definição de imagem da galeria dentro da sua galeria. Neste exemplo, a imagem da galeria é nomeada *myImageDefinition*.

1. Na página para a sua nova galeria de imagens, **selecione Adicione uma nova definição** de imagem no topo da página. 
1. Na **nova definição de imagem add para galeria de imagens partilhada,** para **região,** selecione *East US*.
1. Para **o nome de definição de** imagem, *escreva myImageDefinition*.
1. Para **o sistema operativo,** selecione a opção correta com base na sua fonte VM.  
1. Para **a geração VM,** selecione a opção com base na sua fonte VM. Na maioria dos casos, esta será *a Gen 1.* Para obter mais informações, consulte [Suporte para a geração 2 VMs](../articles/virtual-machines/generation-2.md).
1. Para **o estado do sistema operativo,** selecione a opção com base na sua fonte VM. Para mais informações, consulte [Generalizado e especializado.](../articles/virtual-machines/linux/shared-image-galleries.md#generalized-and-specialized-images)
1. Para **Editor,** escreva *myPublisher*. 
1. Para **Oferta**, *Digite myOffer*.
1. Para **SKU,** escreva *mySKU*.
1. Quando terminar, selecione **Review + create**.
1. Depois de a definição de imagem passar a validação, **selecione Criar**.
1. Quando a implementação estiver concluída, selecione **Ir para o recurso**.


## <a name="create-an-image-version"></a>Criar uma versão de imagem

Crie uma versão de imagem a partir de uma imagem gerida. Neste exemplo, a versão de imagem é *1.0.0* e é replicada tanto para os centros de dados do *Centro Central Oeste dos EUA* como para os centros de dados do Centro Central Sul dos *EUA.* Ao escolher regiões-alvo para replicação, lembre-se que também tem de incluir a região *de origem* como alvo de replicação.

Os caracteres permitidos para a versão de imagem são números e períodos. Os números devem estar dentro do alcance de um inteiro de 32 bits. Formato: *MajorVersion*. *Menorversão.* *Patch*.

Os passos para criar uma versão de imagem são ligeiramente diferentes, dependendo se a fonte é uma imagem generalizada ou um instantâneo de um VM especializado. 

### <a name="option-generalized"></a>Opção: Generalizada

1. Na página para a definição de imagem, **selecione Adicionar versão** a partir do topo da página.
1. Na **Região,** selecione a região onde a sua imagem gerida está armazenada. As versões de imagem precisam de ser criadas na mesma região que a imagem gerida a partir da mesma.
1. Para **nome**, tipo *1.0.0*. O nome da versão de imagem deve *seguir-se principal*. *menor*. formato *de patch* usando inteiros. 
1. Na **imagem 'Source',** selecione a sua imagem gerida pela fonte a partir do drop-down.
1. Em **Excluir a partir do mais recente,** deixe o valor padrão de *Nº*.
1. Para **data de fim de vida**, selecione uma data do calendário que é de alguns meses no futuro.
1. Na **Replicação,** deixe a **contagem de réplica padrão** como 1. Você precisa replicar para a região de origem, então deixe a primeira réplica como padrão e, em seguida, escolha uma segunda região réplica para ser *o Leste dos EUA*.
1. Quando terminar, selecione **Rever + criar**. O Azure validará a configuração.
1. Quando a versão de imagem passar na validação, **selecione Criar**.
1. Quando a implementação estiver concluída, selecione **Ir para o recurso**.

Pode demorar um pouco a replicar a imagem em todas as regiões-alvo.

### <a name="option-specialized"></a>Opção: Especializado

1. Na página para a definição de imagem, **selecione Adicionar versão** a partir do topo da página.
1. Na **Região,** selecione a região onde o seu instantâneo está armazenado. As versões de imagem precisam de ser criadas na mesma região que a fonte a partir da mesma.
1. Para **nome**, tipo *1.0.0*. O nome da versão de imagem deve *seguir-se principal*. *menor*. formato *de patch* usando inteiros. 
1. No **instantâneo do disco OS,** selecione a imagem instantânea do VM da sua fonte a partir do drop-down. Se a sua fonte VM tiver um disco de dados que gostaria de incluir, selecione o número **LUN** correto a partir da entrega e, em seguida, selecione a imagem do disco de dados para a **snapshot do disco de dados**. 
1. Em **Excluir a partir do mais recente,** deixe o valor padrão de *Nº*.
1. Para **data de fim de vida**, selecione uma data do calendário que é de alguns meses no futuro.
1. Na **Replicação,** deixe a **contagem de réplica padrão** como 1. Você precisa replicar para a região de origem, então deixe a primeira réplica como padrão e, em seguida, escolha uma segunda região réplica para ser *o Leste dos EUA*.
1. Quando terminar, selecione **Rever + criar**. O Azure validará a configuração.
1. Quando a versão de imagem passar na validação, **selecione Criar**.
1. Quando a implementação estiver concluída, selecione **Ir para o recurso**.

## <a name="share-the-gallery"></a>Partilhe a galeria

Recomendamos que partilhe o acesso ao nível da galeria de imagens. O seguinte acompanha-o através da partilha da galeria que acabou de criar.

1. Abra o [portal do Azure](https://portal.azure.com).
1. No menu à esquerda, selecione **Grupos de Recursos.** 
1. Na lista de grupos de recursos, selecione **myGalleryRG**. A lâmina do seu grupo de recursos abrir-se-á.
1. No menu à esquerda da página **myGalleryRG,** selecione **Access control (IAM)**. 
1. Under **Add a role assignment**, selecione **Add**. O **painel de atribuição de funções Add a role** será aberto. 
1. Under **Role**, selecione **Reader**.
1. No acesso **a atribuir,** deixe o padrão do utilizador, grupo ou principal de serviço da **Azure.**
1. Em **Select**, digite o endereço de e-mail da pessoa que pretende convidar.
1. Se o utilizador estiver fora da sua organização, verá a mensagem **Este utilizador será enviado um e-mail que lhes permite colaborar com a Microsoft.** Selecione o utilizador com o endereço de e-mail e, em seguida, clique em **Guardar**.

Se o utilizador estiver fora da sua organização, receberá um convite por e-mail para se juntar à organização. O utilizador precisa de aceitar o convite, então poderá ver a galeria e todas as definições de imagem e versões na sua lista de recursos.