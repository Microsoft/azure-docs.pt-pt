---
title: Regras de firewall ao nível do servidor
description: Regras de firewall ao nível do servidor
keywords: conexão sql,cadeia de conexão
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 8d0f9899dbb7599340b8d15ca010a0157011fb9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "67184338"
---
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Na lista à esquerda, selecione **Todos os serviços**.

3. Desloque e selecione **servidores SQL**.

    ![Encontre o seu servidor de base de dados Azure SQL no portal][b21-FindServerInPortal]
5. Na caixa de texto do filtro, comece a escrever o nome do seu servidor. A sua linha está exposta.

6. Selecione a linha para o seu servidor. É apresentada uma lâmina para o seu servidor.

7. Na lâmina do servidor, selecione **Definições**.

8. Selecione **Firewall**.

    ![Selecione Definições > Firewall][b31-SettingsFirewallNavig]
9. Selecione **Adicionar Computador IP**. Digite um nome para a sua nova regra na primeira caixa de texto.

10. Digite os valores de endereço IP baixos e altos para o intervalo que deseja ativar.

    * Pode ser útil ter a extremidade de baixo valor com **.0** e o fim de alto valor com **.255**.

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
