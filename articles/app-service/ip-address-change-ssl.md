---
title: Preparar para alteração de endereçoS IP SSL
description: Se o seu endereço IP SSL for alterado, saiba o que fazer para que a sua aplicação continue a funcionar após a mudança.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: dcfe11bcab25f6267a557de5faf7befab467bc29
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020964"
---
# <a name="how-to-prepare-for-an-ssl-ip-address-change"></a>Como se preparar para uma alteração de endereço SSL IP

Se recebeu uma notificação de que o endereço IP SSL da sua aplicação Azure App Service está a ser alterado, siga as instruções deste artigo para lançar o endereço IP SSL existente e atribuir um novo.

## <a name="release-ssl-ip-addresses-and-assign-new-ones"></a>Libertar endereços IP SSL e atribuir novos

1.  Abra o [portal do Azure](https://portal.azure.com).

2.  No menu de navegação à esquerda, selecione **Serviços de Aplicações**.

3.  Selecione a sua aplicação De Serviço de Aplicações a partir da lista.

4.  No cabeçalho **Definições,** clique nas **definições SSL** na navegação à esquerda.

1. Na secção de encadernações TLS/SSL, selecione o registo do nome do anfitrião. No editor que abre, escolha **SNI SSL** no menu drop-down **do Tipo SSL** e clique em **Adicionar Ligação**. Quando vir a mensagem de sucesso da operação, o endereço IP existente foi lançado.

6.  Na secção **de encadernações SSL,** selecione novamente o mesmo registo de nome de anfitrião com o certificado. No editor que abre, desta vez escolha **IP Based SSL** no menu drop-down **do Tipo SSL** e clique em **Adicionar Binding**. Quando vê a mensagem de sucesso da operação, adquiriu um novo endereço IP.

7.  Se um registo A (registo DNS que aponta diretamente para o seu endereço IP) estiver configurado no seu Portal de Registo de Domínio (fornecedor de DNS de terceiros ou DNS Azure), substitua o endereço IP existente pelo recém-gerado. Pode encontrar o novo endereço IP seguindo as instruções na secção seguinte.

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>Encontre o novo endereço IP SSL no Portal Azure

1.  Espere alguns minutos e, em seguida, abra o [portal Azure.](https://portal.azure.com)

2.  No menu de navegação à esquerda, selecione **Serviços de Aplicações**.

3.  Selecione a sua aplicação De Serviço de Aplicações a partir da lista.

4.  No cabeçalho **Definições,** clique em **Propriedades** na navegação à esquerda e encontre a secção com a etiqueta **do endereço IP virtual**.

5. Copie o endereço IP e reconfigure o seu registo de domínio ou mecanismo de IP.

## <a name="next-steps"></a>Passos seguintes

Este artigo explicou como se preparar para uma alteração de endereço IP que foi iniciada pela Azure. Para obter mais informações sobre endereços IP no Azure App Service, consulte [endereços IP de entrada e saída no Serviço de Aplicações Azure.](overview-inbound-outbound-ips.md)
