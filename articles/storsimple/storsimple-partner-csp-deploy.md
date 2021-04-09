---
title: Implementar StorSimple Matriz Virtual para Programa de Provedor de Soluções em Nuvem
description: Saiba como um parceiro CSP pode adicionar um cliente ou uma nova subscrição a um cliente existente e, em seguida, criar um serviço para implementar um StorSimple Virtual Array em CSP.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/08/2017
ms.author: alkohli
ms.openlocfilehash: dc0cf718ee51e23fb749bdf57d5344977de009d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021865"
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>Implementar StorSimple Matriz Virtual para Programa de Provedor de Soluções em Nuvem

## <a name="overview"></a>Descrição Geral

O StorSimple Virtual Array pode ser implementado pelos parceiros Cloud Solution Provider (CSP) para os seus clientes. Um parceiro CSP pode criar um serviço StorSimple Device Manager. Este serviço pode então ser utilizado para implantar e gerir o StorSimple Virtual Array e as ações, volumes e backups associados.

Este artigo descreve como um parceiro CSP pode adicionar um cliente ou uma nova subscrição a um cliente existente e, em seguida, criar um serviço para implementar um StorSimple Virtual Array em CSP.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

- Está matriculado no âmbito do programa CSP.
- Tem credenciais de login [válidas do Partner Center.](https://partnercenter.microsoft.com/) As credenciais permitem-lhe iniciar scontabilidade no portal Partner para adicionar novos clientes, procurar clientes ou navegar para uma conta de cliente a partir do painel partner. O CSP pode funcionar como administrador StorSimple em nome do cliente no portal Azure.
                             
## <a name="add-a-customer"></a>Adicionar um cliente

Se adicionar um cliente, uma subscrição é criada automaticamente. Para adicionar um cliente (e criar automaticamente uma subscrição), execute os seguintes passos no portal Partner.

1. Vá ao [Centro de Parceiros](https://partnercenter.microsoft.com/) e inscreva-se usando as suas credenciais de CSP. Clique **no Painel de Instrumentos**.

     ![Painel de instrumentos no Centro de Parceiros](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. No painel esquerdo, clique em **Clientes.** No painel direito, clique **em Adicionar clientes.** Insira os detalhes do cliente. Clique **em Seguida: Subscrições** para criar uma subscrição do cliente.

    ![Adicionar cliente](./media/storsimple-partner-csp-deploy/image2.png)

3.  Selecione a oferta **do Microsoft Azure.** Percorra a parte inferior da página e clique em **'Rever'.**

    ![Rever informações de subscrição](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. Reveja as informações e clique **em Enviar por isso.**

    ![Submeter subscrição](./media/storsimple-partner-csp-deploy/image4.png)

5. Guarde as informações de confirmação para referência futura.

    ![Salvar confirmação](./media/storsimple-partner-csp-deploy/image5.png)

6. Encontre ou navegue para o cliente que acabou de adicionar. Clique no **nome da Empresa** para aprofundar os detalhes.

    ![Pesquisar o cliente](./media/storsimple-partner-csp-deploy/image6.png)  

7. No painel de esquerda, selecione **Gestão de Serviços**. No painel direito, nos **serviços de Administração,** clique no **Portal de Gestão microsoft Azure** para iniciar sação como administrador da Azure para o seu cliente.

    ![Iniciar sessão no portal do Azure](./media/storsimple-partner-csp-deploy/image9.png)

8. Para criar um Gestor de Dispositivos StorSimple, clique **em + Novo** e procure ou navegue para **storSimple Virtual Device Series**. Para mais informações, aceda a [implementar um serviço de Gestor de Dispositivos StorSimple](storsimple-virtual-array-manage-service.md).

    ![Criar serviço StorSimple Device Manager](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>Adicionar uma subscrição

Em alguns casos, pode ter um cliente existente, e precisa adicionar uma subscrição. Para adicionar uma subscrição a um cliente existente, execute os seguintes passos no portal Partner.

1. Vá ao [Centro de Parceiros](https://partnercenter.microsoft.com/) e inscreva-se usando as suas credenciais de CSP. Clique **no Painel de Instrumentos**.

     ![Painel de instrumentos no Centro de Parceiros](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. No painel esquerdo, clique em **Clientes.** Encontre ou navegue para o cliente a que pretende adicionar uma subscrição. Clique no ![ ícone do ícone de verificação expandir para expandir a linha para o nome da empresa para o seu ](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) cliente. Nos detalhes, clique **em Adicionar subscrições**.

    ![Clientes](./media/storsimple-partner-csp-deploy/image10.png)

3. Verifique **o Microsoft Azure** para obter as **ofertas principais** na subscrição e clique em Enviar por **isso**. Isto cria uma nova subscrição.

    ![Adicionar nova subscrição](./media/storsimple-partner-csp-deploy/image11.png)

6. Depois de ser criada uma nova subscrição, clique **<-- Clientes** no painel esquerdo para voltar à página **clientes.** Procure o cliente para o qual acabou de criar uma subscrição. Clique no **nome da Empresa** para aprofundar os detalhes.

    ![Pesquisar o cliente](./media/storsimple-partner-csp-deploy/image6.png)  

7. No painel de esquerda, selecione **Gestão de Serviços**. No painel direito, nos **serviços de Administração,** clique no **Portal de Gestão microsoft Azure** para iniciar sação como administrador da Azure para o seu cliente.

    ![Iniciar sessão no portal do Azure](./media/storsimple-partner-csp-deploy/image9.png)

8. Para criar um Gestor de Dispositivos StorSimple, clique **em + Novo** e procure ou navegue para **storSimple Virtual Device Series**. Para mais informações, aceda a [implementar um serviço de Gestor de Dispositivos StorSimple](storsimple-virtual-array-manage-service.md).

    ![Criar serviço StorSimple Device Manager](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>Passos seguintes

- Se tiver mais perguntas sobre o StorSimple em CSP, vá ao [StorSimple em CSP: Perguntas frequentes](storsimple-partner-csp-faq.md).
- Se estiver pronto para implantar o seu StorSimple, vá ao [Implementar o seu StorSimple em CSP](storsimple-partner-csp-deploy.md).
