---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: ce949caa2b80c08f1015ee21c00197d6a95103c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531090"
---
Se pretender que um domínio, pode comprar domínios no [Portal de gestão do Azure](https://portal.azure.com) diretamente. Utilize os seguintes passos para comprar os nomes de domínio e a atribuir à sua aplicação web.

1. No browser, abra a [Portal de gestão do Azure](https://portal.azure.com).
2. Na **aplicações Web** separador, clique no nome da sua aplicação web, selecione **definições**e, em seguida, selecione **domínios personalizados e SSL**
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. Na **domínios personalizados e SSL** painel, clique em **comprar domínios**.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-1.png)
4. Na **comprar domínios** painel, utilize a caixa de texto para introduzir o nome de domínio que pretende comprar. Serão mostrados os domínios disponíveis sugeridos apenas mensagens de caixa de texto. Selecione o domínio em que pretende comprar.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-2.png)
5. Clique nas **informações de contacto** e preencha o formulário de informações de contacto do domínio.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-3.png)
6. Clique nas **selecionar** no **comprar domínios** painel, em seguida, verá que as informações de compra nos **confirmação de compra** painel. Se aceitar os termos legais e clique em **comprar**, será possível submeter a sua encomenda e pode monitorizar o processo de compra no **notificação**.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-4.png)
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-5.png)
7. Se pediu com êxito um domínio, pode gerir o domínio e atribuir a sua aplicação web. Clique nas **"..."** no lado direito do seu domínio. Em seguida, pode **Cancelar Compra** ou **gerir domínio**. Clique em **gerir domínio**, em seguida, podemos fazer ligação **subdomínio** à nossa aplicação web no **gerir domínio** painel.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-6.png)
   
    Depois de concluída a configuração, o nome de domínio personalizado será listado na **enlaces de nome de anfitrião** secção da sua aplicação web.

Neste momento, deve ser capaz de introduzir o nome de domínio personalizado no seu browser e ver que isso com êxito leva à sua aplicação web.

