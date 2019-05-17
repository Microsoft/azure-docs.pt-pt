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
ms.openlocfilehash: 499aeccdf00980eeb66ac6ee06e45267fd515143
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546679"
---
Galerias de imagem partilhado permitem-lhe as imagens de partilha com o RBAC. Pode utilizar o RBAC para partilhar imagens no seu inquilino e até mesmo pessoas fora do seu inquilino. No entanto, se quiser partilhar imagens fora do seu inquilino do Azure, à escala, deve criar um registo de aplicações para facilitar o compartilhamento.  Utilizar um registo de aplicações, pode ativar cenários mais complexos de partilha, como: 

* Gerir partilhado imagens quando uma empresa adquire outro, e a infraestrutura do Azure é distribuir por inquilinos separados. 
* Parceiros do Azure gerir a infraestrutura do Azure em nome dos seus clientes. Personalização de imagens é feita no inquilino de parceiros, mas as implementações de infraestrutura acontecerá no inquilino do cliente. 


## <a name="create-the-app-registration"></a>Criar o registo de aplicações

Crie um registo de aplicação que será utilizado por ambos os inquilinos partilhem os recursos de Galeria de imagens.
1. Abra o [registos de aplicações (pré-visualização) no portal do Azure](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/).    
1. Selecione **novo registo** no menu na parte superior da página.
1. Na **Name**, tipo *myGalleryApp*.
1. Na **tipos de conta suportados**, selecione **contas em qualquer diretório organizacional e contas Microsoft pessoais**.
1. Na **URI de redirecionamento**, tipo *https://www.microsoft.com* e, em seguida, selecione **registar**. Após o registo de aplicações tiver sido criado, será aberta a página de descrição geral.
1. Na página Descrição geral, copie os **ID da aplicação (cliente)** e guarde para utilização posterior.   
1. Selecione **certificados e segredos**e, em seguida, selecione **novo segredo do cliente**.
1. Na **Descrição**, tipo *segredo de aplicação entre inquilinos da Galeria de imagens de partilhado*.
1. Na **Expires**, deixe a predefinição **em 1 ano** e, em seguida, selecione **Add**.
1. Copie o valor do segredo do e guarde-o num local seguro. Não é possível recuperá-la depois de sair da página.


Conceder a permissão de registo de aplicação para utilizar a Galeria de imagem partilhada.
1. No portal do Azure, selecione a Galeria de imagens de partilhado que pretende partilhar com outro inquilino.
1. Selecione **selecione o controlo de acesso (IAM)** e, em **adicionar atribuição de função** selecionar *Add*. 
1. Sob **função**, selecione **leitor**.
1. Sob **atribuir acesso a:**, deixe como **utilizador, grupo ou principal de serviço do Azure AD**.
1. Sob **selecionar**, tipo *myGalleryApp* e selecioná-lo quando esta é apresentada na lista. Quando tiver terminado, selecione **guardar**.


## <a name="give-tenant-2-access"></a>Conceder acesso de 2 de inquilino

Conceder acesso de 2 de inquilino para a aplicação ao pedir um início de sessão usando um navegador. Substitua *<Tenant2 ID>* com o ID de inquilino para o inquilino que gostaria de partilhar a sua galeria de imagens com. Substitua *< ID da aplicação (cliente) >* com o ID de registo de aplicações que criou. Quando terminar de fazer as substituições, cole o URL num browser e siga as instruções de início de sessão para iniciar sessão no inquilino 2.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

Na [portal do Azure](https://portal.azure.com) inicie sessão como 2 de inquilino e dar o acesso de registo de aplicação para o grupo de recursos onde pretende criar a VM.

1. Selecione o grupo de recursos e, em seguida, selecione **controlo de acesso (IAM)**. Sob **adicionar atribuição de função** selecionar **Add**. 
1. Sob **função**, tipo **contribuinte**.
1. Sob **atribuir acesso a:**, deixe como **utilizador, grupo ou principal de serviço do Azure AD**.
1. Sob **selecionar** tipo *myGalleryApp* , em seguida, selecione-o quando esta é apresentada na lista. Quando tiver terminado, selecione **guardar**.

> [!NOTE]
> Terá de aguardar que a versão da imagem concluir completamente a ser criada e replicadas antes de poder utilizar a mesma imagem gerida para criar outra versão da imagem.

