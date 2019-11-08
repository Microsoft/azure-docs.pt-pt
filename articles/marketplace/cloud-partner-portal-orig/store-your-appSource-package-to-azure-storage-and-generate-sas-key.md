---
title: Armazenar o pacote AppSource no armazenamento do Azure e gerar uma URL com a chave SAS
description: Detalha as etapas necessárias para carregar e proteger um pacote AppSource.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pabutler
ms.openlocfilehash: 5f1a09244697a6771ad1b499f3d7c36eb7297067
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827655"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>Armazenar o pacote AppSource no armazenamento do Azure e gerar uma URL com a chave SAS
=============================================================================

Para manter a segurança de seus arquivos, todos os parceiros devem armazenar o arquivo de pacote AppSource em uma conta de armazenamento de BLOBs do Azure e usar uma chave SAS para compartilhá-lo. Recuperaremos o arquivo de pacote do seu local de armazenamento do Azure para certificação e o usarei para avaliações do AppSource.

Use as seguintes etapas para carregar seu pacote no armazenamento de BLOBs:

1. Vá para <https://azure.microsoft.com> e crie uma conta de avaliação gratuita ou cobrada.

2. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

3. Crie uma nova conta de armazenamento clicando em **+ novo** e indo para a conta **dados + armazenamento** .

   ![Folha de dados + armazenamento no portal Microsoft Azure](media/CRMScreenShot7.png)

4. Insira um **nome** e um nome de **grupo de recursos** e clique no botão **criar** .

   ![Criar conta de armazenamento no portal Microsoft Azure](media/CRMScreenShot8.png)

5. Navegue até o grupo de recursos criado recentemente e crie um novo contêiner de BLOBs.

   ![Carregar pacote como BLOB usando o portal Microsoft Azure](media/CRMScreenShot9.png)

6. Se você ainda não tiver feito isso, baixe e instale o Microsoft [Gerenciador de armazenamento do Azure](https://storageexplorer.com/).

7. Abra Gerenciador de Armazenamento e use o ícone para se conectar à sua conta de armazenamento do Azure.

8. Navegue até o contêiner de BLOB que você criou e clique em **carregar** para adicionar o arquivo zip do pacote.

   ![Carregar o pacote usando o Microsoft Gerenciador de Armazenamento](media/CRMScreenShot10.png)

9. Clique com o botão direito do mouse no arquivo e selecione **obter assinatura de acesso compartilhado**.

   ![Obter assinatura de acesso compartilhado de um arquivo do Azure](media/CRMScreenShot11.png)

10. Modifique o **tempo de expiração** para tornar a SAS ativa por um mês e clique em **criar**.

    ![Modificar a data de validade da SAS de um arquivo do Azure](media/CRMScreenShot12.png)

11. Copie o campo URL e salve-o para mais tarde. Você precisará inserir essa URL ao criar a oferta associada. 

    ![Copiar a URL SAS de um arquivo do Azure](media/CRMScreenShot13.png)

