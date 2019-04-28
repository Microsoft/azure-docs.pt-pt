---
title: Regras de firewall ao nível do servidor
description: Regras de firewall ao nível do servidor
keywords: ligação de SQL, cadeia de ligação
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 8d0f9899dbb7599340b8d15ca010a0157011fb9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60202155"
---
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Na lista à esquerda, selecione **todos os serviços**.

3. Desloque-se e selecione **servidores SQL**.

    ![Encontrar o seu servidor de base de dados do Azure SQL no portal do][b21-FindServerInPortal]
5. Na caixa de texto de filtro, comece a escrever o nome do seu servidor. A linha é exibida.

6. Selecione a linha para o seu servidor. É apresentado um painel para o seu servidor.

7. No painel do servidor, selecione **definições**.

8. Selecione **Firewall**.

    ![Selecione definições > Firewall][b31-SettingsFirewallNavig]
9. Selecione **Adicionar cliente IP**. Escreva um nome para a nova regra na primeira caixa de texto.

10. Escreva os valores de endereço IP de baixos e elevados para o intervalo que pretende ativar.

    * Pode ser útil ter final com o valor baixo **.0** e o alto valor terminar com **.255**.

11. Selecione **Guardar**.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
