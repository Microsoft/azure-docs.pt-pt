---
title: Guarde o seu Pacote AppSource para o armazenamento do Azure e gere um URL com a chave SAS
description: Detalha os passos necessários para fazer o upload e proteger um pacote AppSource.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: dsindona
ms.openlocfilehash: 57bc370fd160b8b3d6d7941ea28cd460c99f3d96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285372"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>Guarde o seu Pacote AppSource para o armazenamento do Azure e gere um URL com a chave SAS
=============================================================================

Para manter a segurança dos seus ficheiros, todos os parceiros devem armazenar o seu ficheiro de pacote AppSource numa conta de armazenamento de blob Azure e usar uma chave SAS para o partilhar. Recuperaremos o ficheiro de pacote do seu local de armazenamento Azure para certificação e utilizá-lo-emos para testes appSource.

Utilize os seguintes passos para fazer o upload da sua embalagem para o armazenamento de bolhas:

1. Vá <https://azure.microsoft.com> e crie um teste gratuito ou conta faturada.

2. Inscreva-se no [Portal Azure.](https://portal.azure.com/)

3. Crie uma nova Conta de Armazenamento clicando **+ Novo** e indo para a conta Data **+ Armazenamento.**

   ![Data + Lâmina de armazenamento no Portal Microsoft Azure](media/CRMScreenShot7.png)

4. Introduza um nome de Grupo **de Nome** e **Recursos** e clique em **Criar** botão.

   ![Criar conta de armazenamento no Portal Microsoft Azure](media/CRMScreenShot8.png)

5. Navegue para o seu grupo de recursos recém-criado e crie um novo recipiente de blob.

   ![Carregue o pacote como blob usando o Portal Microsoft Azure](media/CRMScreenShot9.png)

6. Se ainda não o fez, descarregue e instale o Microsoft [Azure Storage Explorer](https://storageexplorer.com/).

7. Abra o Explorer de Armazenamento e utilize o ícone para se ligar à sua conta de armazenamento Azure.

8. Navegue para o recipiente de blob que criou e clique em **Carregar** para adicionar o seu ficheiro zip pacote.

   ![Pacote de upload usando o Microsoft Storage Explorer](media/CRMScreenShot10.png)

9. Clique no seu ficheiro e selecione **Obter Assinatura de Acesso Partilhado**.

   ![Obtenha assinatura de acesso partilhado de um ficheiro Azure](media/CRMScreenShot11.png)

10. Modifique o **tempo de validade** para tornar o SAS ativo durante um mês e, em seguida, clique em **Criar**.

    ![Modificar a data de validade do SAS de um ficheiro Azure](media/CRMScreenShot12.png)

11. Copie o campo URL e guarde-o para mais tarde. Terá de introduzir este URL quando criar a oferta associada. 

    ![Copiar o URL SAS de um ficheiro Azure](media/CRMScreenShot13.png)

