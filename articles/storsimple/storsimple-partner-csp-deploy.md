---
title: Descrição geral do programa Microsoft Azure StorSimple e o fornecedor de soluções de Cloud | Documentos da Microsoft
description: Uma visão geral sobre o StorSimple e o CSP para parceiros do StorSimple.
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
ms.openlocfilehash: 0dac86a696599a391cb243ad11e16931e00b8921
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629989"
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>Implementar matriz Virtual StorSimple para o programa de fornecedor de soluções de Cloud

## <a name="overview"></a>Descrição geral

Matriz Virtual StorSimple pode ser implementada, os parceiros do fornecedor de soluções Cloud (CSP) para seus clientes. Um parceiro CSP, pode criar um serviço StorSimple Device Manager. Este serviço, em seguida, pode ser utilizado para implementar e gerir a matriz Virtual StorSimple e o associado partilhas, volumes e cópias de segurança.

Este artigo descreve como um parceiro CSP pode adicionar uma nova subscrição ou do cliente para um cliente existente e, em seguida, criar um serviço para implementar um StorSimple Virtual Array no CSP.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

- Está inscrito no programa CSP.
- Tem válido [Centro de parceiros](https://partnercenter.microsoft.com/) credenciais de início de sessão. As credenciais permitem-lhe iniciar sessão no portal de parceiros para adicionar novos clientes, procurar clientes ou navegar para uma conta de cliente a partir do dashboard de parceiro. O CSP pode funcionar como um administrador do StorSimple em nome do cliente no portal do Azure.
                             
## <a name="add-a-customer"></a>Adicionar um cliente

Se adicionar um cliente, é automaticamente criada uma subscrição. Para adicionar um cliente (e criar automaticamente uma subscrição), execute os seguintes passos no portal de parceiros.

1. Vá para o [Centro de parceiros](https://partnercenter.microsoft.com/) e inicie sessão com as suas credenciais CSP. Clique em **Dashboard**.

     ![Dashboard do Centro de parceiros](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. No painel esquerdo, clique em **clientes**. No painel direito, clique em **adicionar clientes**. Introduza os detalhes do cliente. Clique em **seguinte: Subscrições** para criar uma subscrição de cliente.

    ![Adicionar cliente](./media/storsimple-partner-csp-deploy/image2.png)

3.  Selecione **Microsoft Azure** oferecem. Desloque-se para a parte inferior da página e clique em **revisão**.

    ![Reveja as informações de subscrição](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. Reveja as informações e clique em **submeter**.

    ![Submeter subscrição](./media/storsimple-partner-csp-deploy/image4.png)

5. Guarde as informações de confirmação para referência futura.

    ![Guardar confirmação](./media/storsimple-partner-csp-deploy/image5.png)

6. Encontre ou navegue para o cliente que acabou de adicionar. Clique nas **nome da empresa** para desagregar os detalhes.

    ![Pesquisa do cliente](./media/storsimple-partner-csp-deploy/image6.png)  

7. No painel esquerdo, selecione **gestão de serviço**. No painel direito, sob **administrar serviços**, clique em **Microsoft Azure Management Portal** para iniciar sessão como um administrador do Azure para o seu cliente.

    ![Iniciar sessão no portal do Azure](./media/storsimple-partner-csp-deploy/image9.png)

8. Para criar um StorSimple Device Manager, clique em **+ novo** e procure ou navegue até à **série do dispositivo Virtual StorSimple**. Para obter mais informações, aceda a [implementar um serviço StorSimple Device Manager](storsimple-virtual-array-manage-service.md).

    ![Criar o serviço StorSimple Device Manager](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>Adicionar uma subscrição

Em alguns casos, talvez tenha um cliente existente e tem de adicionar uma subscrição. Para adicionar uma subscrição para um cliente existente, execute os seguintes passos no portal de parceiros.

1. Vá para o [Centro de parceiros](https://partnercenter.microsoft.com/) e inicie sessão com as suas credenciais CSP. Clique em **Dashboard**.

     ![Dashboard do Centro de parceiros](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. No painel esquerdo, clique em **clientes**. Encontre ou navegue para o cliente que pretende adicionar uma subscrição. Clique nas ![ícone de verificação de expansão](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) ícone para expandir a linha para o nome da empresa para o seu cliente. Nos detalhes, clique em **adicionar subscrições**.

    ![Clientes](./media/storsimple-partner-csp-deploy/image10.png)

3. Verifique **Microsoft Azure** para o **principais ofertas** na subscrição e clique em **submeter**. Esta ação cria uma nova subscrição.

    ![Adicionar nova assinatura](./media/storsimple-partner-csp-deploy/image11.png)

6. Depois de criar uma nova subscrição, clique em **< – clientes** no painel esquerdo para voltar para o **clientes** página. Pesquisa do cliente para o qual acabou de criar uma subscrição. Clique nas **nome da empresa** para desagregar os detalhes.

    ![Pesquisa do cliente](./media/storsimple-partner-csp-deploy/image6.png)  

7. No painel esquerdo, selecione **gestão de serviço**. No painel direito, sob **administrar serviços**, clique em **Microsoft Azure Management Portal** para iniciar sessão como um administrador do Azure para o seu cliente.

    ![Iniciar sessão no portal do Azure](./media/storsimple-partner-csp-deploy/image9.png)

8. Para criar um StorSimple Device Manager, clique em **+ novo** e procure ou navegue até à **série do dispositivo Virtual StorSimple**. Para obter mais informações, aceda a [implementar um serviço StorSimple Device Manager](storsimple-virtual-array-manage-service.md).

    ![Criar o serviço StorSimple Device Manager](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>Passos Seguintes

- Se tiver mais perguntas sobre o StorSimple no CSP, aceda a [StorSimple no CSP: Perguntas mais frequentes sobre](storsimple-partner-csp-faq.md).
- Se estiver pronto para implementar o seu StorSimple, aceda a [implementar o seu StorSimple no CSP](storsimple-partner-csp-deploy.md).
