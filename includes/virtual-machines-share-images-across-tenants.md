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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73903688"
---
Mas, se quiser partilhar imagens fora do seu inquilino Azure, em escala, deverá criar um registo de aplicações para facilitar a partilha.  A utilização de uma aplicação pode permitir cenários de partilha mais complexos, como: 

* Gerir imagens partilhadas quando uma empresa adquire outra, e a infraestrutura Azure é espalhada por inquilinos separados. 
* A Azure Partners gere a infraestrutura Azure em nome dos seus clientes. A personalização das imagens é feita dentro do inquilino parceiro, mas as implementações de infraestruturas vão acontecer no inquilino do cliente. 


## <a name="create-the-app-registration"></a>Criar o registo da aplicação

Crie um registo de candidatura que será utilizado por ambos os inquilinos para partilhar os recursos da galeria de imagens.
1. Abra as inscrições da [App (pré-visualização) no portal Azure.](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/)    
1. Selecione **Nova inscrição** no menu no topo da página.
1. Em **Nome**, *digite myGalleryApp*.
1. Nos tipos de **conta suportados,** selecione **Contas em qualquer diretório organizacional e contas pessoais**da Microsoft .
1. No **Redirecione URI,** escreva *https://www.microsoft.com* e, em seguida, selecione **Registar**. Após a criação do registo da aplicação, a página de visão geral será aberta.
1. Na página de visão geral, copie o **ID da Aplicação (cliente)** e guarde para utilização posteriormente.   
1. Selecione **Certificados & segredos**e, em seguida, selecione **novo segredo do cliente**.
1. Em **Descrição**, escreva *imagem partilhada galeria de imagem cruzada de aplicativos de inquilino.*
1. Em **Expirações,** deixe o padrão de **1 ano** e, em seguida, selecione **Adicionar**.
1. Copie o valor do segredo e guarde-o para um lugar seguro. Não pode recuperá-lo depois de sair da página.


Dê permissão de registo da aplicação para utilizar a galeria de imagens partilhadas.
1. No portal Azure, selecione a Galeria de Imagem Partilhada que pretende partilhar com outro inquilino.
1. **Selecione selecione controlo de acesso (IAM)** e sob a **função adicionar função** *Adicionar*. 
1. Em **Função,** selecione **Reader**.
1. Sob **acesso de atribuição a:**, deixe isto como utilizador, grupo ou diretor de serviço **Azure AD**.
1. Em **Select**, escreva *myGalleryApp* e selecione-o quando aparecer na lista. Quando tiver terminado, selecione **Guardar**.


## <a name="give-tenant-2-access"></a>Dê acesso ao Inquilino 2

Dê ao Inquilino 2 acesso à aplicação solicitando um sessão de sessão utilizando um browser. Substitua o>de * \<Identificação do Tenant2* com a identificação do inquilino para o inquilino com quem gostaria de partilhar a sua galeria de imagens. Substitua o ID da * \<Aplicação (cliente)>com* o ID da aplicação do registo da aplicação que criou. Quando terminar de fazer as substituições, colhe o URL num browser e siga as indicações de entrada para iniciar sessão no Tenant 2.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

No [portal Azure](https://portal.azure.com) inscreva-se como Inquilino 2 e dê à app acesso ao grupo de recursos onde pretende criar o VM.

1. Selecione o grupo de recursos e, em seguida, selecione **o controlo de acesso (IAM)**. Sob **adicionar função** selecionar **Adicionar**. 
1. Em **Função,** digite **Contribuinte.**
1. Sob **acesso de atribuição a:**, deixe isto como utilizador, grupo ou diretor de serviço **Azure AD**.
1. Em **'Seleccionar'** *escreva myGalleryApp* e, em seguida, selecione-o quando aparecer na lista. Quando tiver terminado, selecione **Guardar**.

> [!NOTE]
> É preciso esperar que a versão de imagem termine completamente de ser construída e replicada antes de poder utilizar a mesma imagem gerida para criar outra versão de imagem.

