---
title: Preparar para a alteração do endereço IP SSL
description: Se o seu endereço IP SSL vai ser alterado, saiba o que fazer para que a sua aplicação continue a funcionar após a mudança.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: dcfe11bcab25f6267a557de5faf7befab467bc29
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535728"
---
# <a name="how-to-prepare-for-an-ssl-ip-address-change"></a>Como preparar uma alteração de endereço IP SSL

Se recebeu uma notificação de que o endereço IP SSL da sua aplicação Azure App Service está a mudar, siga as instruções deste artigo para lançar o endereço IP sSL existente e atribua um novo.

## <a name="release-ssl-ip-addresses-and-assign-new-ones"></a>Lançar endereços IP SSL e atribuir novos

1.  Abra o [portal Azure.](https://portal.azure.com)

2.  No menu de navegação à esquerda, selecione Serviços de **Aplicações**.

3.  Selecione a sua aplicação App Service da lista.

4.  No cabeçalho **Definições,** clique nas **definições SSL** na navegação à esquerda.

1. Na secção de encadernações TLS/SSL, selecione o registo de nome do anfitrião. No editor que abre, escolha **SNI SSL** no menu de entrega do **Tipo SSL** e clique em **Adicionar Encadernação**. Quando vê a mensagem de sucesso da operação, o endereço IP existente foi lançado.

6.  Na secção de **encadernações SSL,** selecione novamente o mesmo registo de nome do anfitrião com o certificado. No editor que abre, desta vez escolha **o Ip Based SSL** no menu de entrega do **Tipo SSL** e clique em **Adicionar Encadernação**. Quando vê a mensagem de sucesso da operação, adquiriu um novo endereço IP.

7.  Se um registo A (registo DNS apontado diretamente para o seu endereço IP) estiver configurado no seu Portal de Registo de Domínio (Fornecedor DNS de terceiros ou DNS Azure), substitua o endereço IP existente pelo recém-gerado. Pode encontrar o novo endereço IP seguindo as instruções na secção seguinte.

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>Encontre o novo endereço IP SSL no Portal Azure

1.  Espere alguns minutos e abra o [portal Azure.](https://portal.azure.com)

2.  No menu de navegação à esquerda, selecione Serviços de **Aplicações**.

3.  Selecione a sua aplicação App Service da lista.

4.  No cabeçalho **Definições,** clique em **Propriedades** na navegação à esquerda e encontre o **endereço IP virtual**da secção rotulado .

5. Copie o endereço IP e reconfigure o seu registo de domínio ou mecanismo IP.

## <a name="next-steps"></a>Passos seguintes

Este artigo explicou como se preparar para uma mudança de endereço IP que foi iniciada pelo Azure. Para obter mais informações sobre endereços IP no Serviço de Aplicações Azure, consulte endereços IP de entrada e saída no Serviço de [Aplicações Azure](overview-inbound-outbound-ips.md).
