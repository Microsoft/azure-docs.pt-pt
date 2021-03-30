---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f74d4cbc17e49345534a37e9e6612a36e19be295
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "73903688"
---
Mas, se quiser partilhar imagens fora do seu inquilino Azure, em escala, deverá criar um registo de aplicações para facilitar a partilha.  A utilização de um registo de aplicações pode permitir cenários de partilha mais complexos, como: 

* Gerir imagens partilhadas quando uma empresa adquire outra, e a infraestrutura Azure é distribuída por inquilinos separados. 
* A Azure Partners gere a infraestrutura Azure em nome dos seus clientes. A personalização das imagens é feita dentro do inquilino dos parceiros, mas as infraestruturas vão acontecer no arrendatário do cliente. 


## <a name="create-the-app-registration"></a>Criar o registo da aplicação

Crie um registo de inscrição que será usado por ambos os inquilinos para partilhar os recursos da galeria de imagens.
1. Abra as [inscrições da App (pré-visualização) no portal Azure](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/).    
1. Selecione **Novo registo** no menu no topo da página.
1. Em **Nome,** escreva *myGalleryApp*.
1. Nos **tipos de conta suportada**, selecione Contas em qualquer **diretório organizacional e contas pessoais da Microsoft.**
1. Em **Redirecionar URI,** *https://www.microsoft.com* digite e, em seguida, selecione **Registar**. Após a criação do registo da aplicação, a página geral será aberta.
1. Na página geral, copie o **ID da Aplicação (cliente)** e guarde para utilização posterior.   
1. Selecione **Certificados & segredos** e, em seguida, selecione **Novo segredo de cliente**.
1. Em **Descrição**, tipo *Imagem Partilhada galeria de imagens segredo de aplicação de inquilino.*
1. Em **Expira,** deixe o padrão de **In 1 ano** e, em seguida, selecione **Adicionar**.
1. Copie o valor do segredo e guarde-o para um lugar seguro. Não pode recuperá-lo depois de sair da página.


Dê permissão de registo da aplicação para utilizar a galeria de imagens partilhada.
1. No portal Azure, selecione a Galeria de Imagens Partilhadas que pretende partilhar com outro inquilino.
1. Selecione **selecione Access control (IAM)** e em **Adicionar design de função** select *Add*. 
1. Under **Role**, selecione **Reader**.
1. Informação **de Atribuição de acesso a:**, deixe isto como **utilizador, grupo ou principal de serviço da Azure.**
1. Em **Select**, *digite myGalleryApp* e selecione-o quando aparecer na lista. Quando tiver terminado, selecione **Guardar**.


## <a name="give-tenant-2-access"></a>Dê ao Inquilino 2 acesso

Dê ao Inquilino 2 acesso à aplicação solicitando uma inscrição usando um browser. Substitua *\<Tenant2 ID>* a identificação do inquilino pelo inquilino com quem gostaria de partilhar a sua galeria de imagens. *\<Application (client) ID>* Substitua-se pelo ID da aplicação do registo da aplicação que criou. Quando terminar de fazer as substituições, cole o URL num browser e siga as indicações de inscrição para assinar no Tenant 2.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

No [portal Azure](https://portal.azure.com) inscreva-se como Inquilino 2 e dê acesso ao registo da app ao grupo de recursos onde pretende criar o VM.

1. Selecione o grupo de recursos e, em seguida, selecione **Access control (IAM)**. No **âmbito do add fun assignment** select **Add**. 
1. Em **Função,** **Tipo Contribuinte.**
1. Informação **de Atribuição de acesso a:**, deixe isto como **utilizador, grupo ou principal de serviço da Azure.**
1. Em **Select** *myGalleryApp,* em seguida, selecione-o quando aparecer na lista. Quando tiver terminado, selecione **Guardar**.

> [!NOTE]
> É necessário esperar que a versão de imagem termine completamente de ser construída e replicada antes de poder utilizar a mesma imagem gerida para criar outra versão de imagem.

