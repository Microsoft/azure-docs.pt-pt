---
title: Store seu pacote de AppSource para o armazenamento do Azure e gerar um URL com a chave SAS | Documentos da Microsoft
description: Fornece detalhes sobre os passos necessários para carregar e proteger um pacote do AppSource.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: ad0e6eaae5c0fad74ea484827e0f8d535cfbf579
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57884664"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>Store seu pacote de AppSource para o armazenamento do Azure e gerar um URL com a chave SAS
=============================================================================

Para manter a segurança dos seus ficheiros, todos os parceiros tem de armazenar seu arquivo de pacote do AppSource numa conta de armazenamento de Blobs do Azure e utilizar uma chave SAS para partilhá-lo. Podemos irá obter o ficheiro de pacote do seu local de armazenamento do Azure para obter uma certificação e utilizá-lo para versões de avaliação do AppSource.

Utilize os seguintes passos para carregar o seu pacote para o armazenamento de BLOBs:

1. Aceda a <https://azure.microsoft.com> e crie uma conta de avaliação ou de faturação gratuita.

2. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

3. Criar uma nova conta de armazenamento ao clicar em **+ novo** e o **dados + armazenamento** conta.

   ![Dados + painel de armazenamento no Portal do Microsoft Azure](media/CRMScreenShot7.png)

4. Introduza um **Name** e **grupo de recursos** nome e clique em **criar** botão.

   ![Criar conta de armazenamento no Portal do Microsoft Azure](media/CRMScreenShot8.png)

5. Navegue para o grupo de recursos recém-criado e criar um novo contentor de Blobs.

   ![Carregar o pacote como blob através do Portal do Microsoft Azure](media/CRMScreenShot9.png)

6. Se ainda não o fez, transfira e instale o Microsoft [Explorador de armazenamento do Azure](https://storageexplorer.com/).

7. Abra o Explorador de armazenamento e utilizar o ícone para ligar à sua conta de armazenamento do Azure.

8. Navegue para o contentor de BLOBs que criou e clique em **carregar** ao adicionar o ficheiro zip do pacote.

   ![Carregar o pacote com o Explorador de armazenamento da Microsoft](media/CRMScreenShot10.png)

9. Clique com o botão direito do rato no seu arquivo e selecione **obter assinatura de acesso partilhado**.

   ![Obter assinatura de acesso partilhado de um arquivo do Azure](media/CRMScreenShot11.png)

10. Modificar a **tempo de expiração** para tornar a SAS ativa durante um mês, em seguida, clique em **criar**.

    ![Modificar a data de expiração de SAS de um arquivo do Azure](media/CRMScreenShot12.png)

11. Copie o campo de URL e guarde-o para utilizar mais tarde. Terá de introduzir este URL quando criar a oferta associada. 

    ![Copie o URL de SAS de um arquivo do Azure](media/CRMScreenShot13.png)

