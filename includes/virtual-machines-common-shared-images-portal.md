---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/29/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 66ec923f12c59d606056d48c09f1ba346e9a4d32
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827063"
---
## <a name="sign-in-to-azure"></a>Inicie sessão no  Azure 

Inicie sessão no portal do Azure em https://portal.azure.com.

> [!NOTE]
> Se tiver registado para utilizar galerias de imagem partilhado durante a pré-visualização, poderá ter de voltar a registar o `Microsoft.Compute` fornecedor. Open [Cloud Shell](https://shell.azure.com/bash) e escreva: `az provider register -n Microsoft.Compute`

## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens

Uma galeria de imagens é o recurso principal utilizado para ativar a partilha de imagens. Carateres permitidos para o nome de galeria são letras em maiúsculas ou minúsculas, dígitos, pontos e períodos. O nome de Galeria não pode conter traços.  Nomes de galeria tem de ser exclusivos na sua subscrição. 

O exemplo seguinte cria uma galeria com o nome *myGallery* no *myGalleryRG* grupo de recursos.

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Utilize o tipo de **Galeria de imagens de partilhado** na caixa de pesquisa e selecione **Galeria de imagens de partilhado** nos resultados.
1. Na **Galeria de imagens de partilhado** página, clique em **criar**.
1. Selecione a subscrição correta.
1. Na **grupo de recursos**, selecione **criar nova** e o tipo *myGalleryRG* para o nome.
1. Na **Name**, tipo *myGallery* para o nome da galeria.
1. Deixe a predefinição para **região**.
1. Pode digitar uma breve descrição da galeria, como *minha Galeria de imagens para fins de teste.* e, em seguida, clique em **rever + criar**.
1. Depois de passar a validação, selecione **criar**.
1. Quando a implementação estiver concluída, selecione **Ir para recurso**.
   
## <a name="create-an-image-definition"></a>Criar uma definição de imagem 

Definições de imagem, criar um agrupamento lógico das imagens. Eles são usados para gerenciar informações sobre as versões de imagem que são criados dentro dos mesmos. Nomes de definição de imagem podem ser constituídos por letras em maiúsculas ou minúsculas, dígitos, pontos, travessões e períodos. Para obter mais informações sobre os valores que pode especificar para obter uma definição de imagem, veja [definições de imagem](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Crie a definição de imagem de galeria dentro da sua galeria. Neste exemplo, a imagem de galeria é denominada *myImageDefinition*.

1. Na página para a nova galeria de imagens, selecione **adicionar uma nova definição de imagem** da parte superior da página. 
1. Para **nome da definição de imagem**, tipo *myImageDefinition*.
1. Para **sistema operativo**, selecione a opção correta com base em sua imagem de origem.
1. Para **publicador**, tipo *myPublisher*. 
1. Para **oferecem**, tipo *myOffer*.
1. Para **SKU**, tipo *mySKU*.
1. Quando terminar, selecione **rever + criar**.
1. Depois da definição de imagem forem validados, selecione **criar**.
1. Quando a implementação estiver concluída, selecione **Ir para recurso**.



## <a name="create-an-image-version"></a>Criar uma versão de imagem

Crie uma versão de imagem a partir de uma imagem gerida. Neste exemplo, é a versão da imagem *1.0.0* e ele é replicado para ambas *e.u.a. Centro-Oeste* e *Centro-Sul* centros de dados. Ao escolher as regiões de destino para replicação, lembre-se de que também tem de incluir o *origem* região como um destino para replicação.

Carateres permitidos para a versão da imagem são números e pontos finais. Tem de ser números dentro do intervalo de um número inteiro de 32 bits. Formato: *MajorVersion*. *MinorVersion*. *Patch*.

1. Na página para a sua definição de imagem, selecione **adicionar versão** da parte superior da página.
1. Na **região**, selecione a região onde está armazenada a imagem gerida. Versões de imagem tem de ser criado na mesma região que a imagem gerida que são criados do.
1. Para **Name**, tipo *1.0.0*. O nome da versão de imagem deve seguir *principais*. *pequenas*. *patch* formatar com números inteiros. 
1. Na **imagem de origem**, selecione a sua imagem gerida de origem da lista pendente.
1. Na **excluir da versão mais recente**, deixe o valor predefinido de *não*.
1. Para **vida data de fim de**, selecione uma data do calendário que é alguns meses no futuro.
1. Na **replicação**, deixe a **réplica número predefinido de** como 1. Terá de replicar para a região de origem, por isso deixe a primeira réplica como a predefinição e, em seguida, escolha uma segunda região de réplica seja *E.U.A. Leste*.
1. Quando tiver terminado, selecione **rever + criar**. Azure validará a configuração.
1. Quando a versão da imagem passa na validação, selecione **criar**.
1. Quando a implementação estiver concluída, selecione **Ir para recurso**.

Pode demorar algum tempo para replicar a imagem para todas as regiões de destino.
