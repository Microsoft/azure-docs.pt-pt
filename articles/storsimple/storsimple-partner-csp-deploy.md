---
title: Implementar matriz virtual StorSimple para programa de fornecedor de soluções em nuvem
description: Uma visão geral sobre o StorSimple e cSP para parceiros StorSimple.
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
ms.openlocfilehash: 7f1927a67127766c72be463c283225135b2a2aad
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77466914"
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>Implementar matriz virtual StorSimple para programa de fornecedor de soluções em nuvem

## <a name="overview"></a>Descrição geral

O StorSimple Virtual Array pode ser implementado pelos parceiros cloud Solution Provider (CSP) para os seus clientes. Um parceiro CSP pode criar um serviço StorSimple Device Manager. Este serviço pode então ser utilizado para implementar e gerir o StorSimple Virtual Array e as ações, volumes e backups associados.

Este artigo descreve como um parceiro CSP pode adicionar um cliente ou uma nova subscrição a um cliente existente e, em seguida, criar um serviço para implementar um StorSimple Virtual Array em CSP.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

- Está matriculado no âmbito do programa CSP.
- Tem credenciais de login válidas [do Partner Center.](https://partnercenter.microsoft.com/) As credenciais permitem-lhe iniciar sessão no portal Partner para adicionar novos clientes, procurar clientes ou navegar para uma conta de cliente a partir do dashboard Partner. O CSP pode funcionar como administrador storSimple em nome do cliente no portal Azure.
                             
## <a name="add-a-customer"></a>Adicione um cliente

Se adicionar um cliente, uma subscrição é automaticamente criada. Para adicionar um cliente (e criar automaticamente uma subscrição), execute os seguintes passos no portal Parceiro.

1. Vá ao [Partner Center](https://partnercenter.microsoft.com/) e inscreva-se usando as suas credenciais de CSP. Clique no **Dashboard**.

     ![Painel de instrumentos no Centro de Parceiros](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. No painel esquerdo, clique em **Clientes.** No painel direito, clique em **Adicionar clientes**. Insira os detalhes do cliente. Clique em **Seguinte: Assinaturas** para criar uma subscrição de cliente.

    ![Adicionar cliente](./media/storsimple-partner-csp-deploy/image2.png)

3.  Selecione oferta **do Microsoft Azure.** Percorra para a parte inferior da página e clique em **Rever**.

    ![Rever informações sobre subscrições](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. Reveja as informações e clique em **Submeter**.

    ![Enviar subscrição](./media/storsimple-partner-csp-deploy/image4.png)

5. Guarde as informações de confirmação para referência futura.

    ![Salvar a confirmação](./media/storsimple-partner-csp-deploy/image5.png)

6. Encontre ou navegue para o cliente que acabou de adicionar. Clique no **nome da Empresa** para aprofundar os detalhes.

    ![Procurar o cliente](./media/storsimple-partner-csp-deploy/image6.png)  

7. No painel esquerdo, selecione gestão de **serviço.** No painel direito, sob **os serviços da Administração,** clique no Portal de **Gestão Microsoft Azure** para se inscrever como administrador do Azure para o seu cliente.

    ![Iniciar sessão no portal do Azure](./media/storsimple-partner-csp-deploy/image9.png)

8. Para criar um Gestor de Dispositivos StorSimple, clique em **+ Novo** e procure ou navegue para **a StorSimple Virtual Device Series**. Para mais informações, vá utilizar [um serviço StorSimple Device Manager](storsimple-virtual-array-manage-service.md).

    ![Criar o serviço StorSimple Device Manager](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>Adicionar uma subscrição

Em alguns casos, pode ter um cliente existente, e precisa adicionar uma subscrição. Para adicionar uma subscrição a um cliente existente, execute os seguintes passos no portal Parceiro.

1. Vá ao [Partner Center](https://partnercenter.microsoft.com/) e inscreva-se usando as suas credenciais de CSP. Clique no **Dashboard**.

     ![Painel de instrumentos no Centro de Parceiros](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. No painel esquerdo, clique em **Clientes.** Encontre ou navegue para o cliente a que pretende adicionar uma subscrição. Clique no ícone de ![Expandir](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) ícone para expandir a linha para o nome da empresa para o seu cliente. Nos detalhes, clique em **Adicionar subscrições**.

    ![Clientes](./media/storsimple-partner-csp-deploy/image10.png)

3. Verifique as **ofertas do** **Microsoft Azure** na subscrição e clique em **Submeter**. Isto cria uma nova subscrição.

    ![Adicionar nova subscrição](./media/storsimple-partner-csp-deploy/image11.png)

6. Depois de criada uma nova subscrição, clique **em <-- Clientes** no painel esquerdo para voltar à página **clientes.** Procure o cliente por quem acabou de criar uma subscrição. Clique no **nome da Empresa** para aprofundar os detalhes.

    ![Procurar o cliente](./media/storsimple-partner-csp-deploy/image6.png)  

7. No painel esquerdo, selecione gestão de **serviço.** No painel direito, sob **os serviços da Administração,** clique no Portal de **Gestão Microsoft Azure** para se inscrever como administrador do Azure para o seu cliente.

    ![Iniciar sessão no portal do Azure](./media/storsimple-partner-csp-deploy/image9.png)

8. Para criar um Gestor de Dispositivos StorSimple, clique em **+ Novo** e procure ou navegue para **a StorSimple Virtual Device Series**. Para mais informações, vá utilizar [um serviço StorSimple Device Manager](storsimple-virtual-array-manage-service.md).

    ![Criar o serviço StorSimple Device Manager](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>Passos seguintes

- Se tiver mais perguntas sobre o StorSimple em CSP, vá ao [StorSimple em CSP: Perguntas frequentes](storsimple-partner-csp-faq.md).
- Se estiver pronto para implementar o seu StorSimple, vá implementar [o seu StorSimple em CSP](storsimple-partner-csp-deploy.md).
