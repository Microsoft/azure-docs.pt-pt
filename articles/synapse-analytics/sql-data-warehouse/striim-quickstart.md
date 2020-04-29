---
title: Início rápido de Striim
description: Começar rapidamente com o Striim e o Azure SQL Data Warehouse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e963d62df8d2c416726852ee3d46daf1cd22936e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80349948"
---
# <a name="striim-azure-sql-dw-marketplace-offering-install-guide"></a>Guia de instalação de oferta de mercado Striim Azure SQL DW

Este quickstart pressupõe que já tem uma instância pré-existente de SQL Data Warehouse.

Procure striim no Mercado Azure e selecione o Striim para integração de dados para o Armazém de Dados SQL (Encenado) 

![Instalar Striim][install]

Configure o VM Striim com propriedades especificadas, observando o nome do cluster Striim, palavra-passe e senha de administração

![Configure Striim][configure]

Uma vez implementado, \<clique em Nome VM>-masternode no portal Azure, clique em Connect e copie o Login usando a conta local VM 

![Ligar striim ao Armazém de Dados SQL][connect]

Descarregue o sqljdbc42.jar de para <https://www.microsoft.com/en-us/download/details.aspx?id=54671> a sua máquina local. 

Abra uma janela de linha de comando e mude de direção para onde descarregou o frasco JDBC. SCP o ficheiro do frasco para o seu Striim VM, obtendo o endereço e senha do portal Azure

![Arquivo de frasco de cópia para o seu VM][copy-jar]

Abra outra janela de linha de comando, ou use um utilitário ssh para entrar no cluster Striim

![SSH no cluster][ssh]

Execute os seguintes comandos para mover o ficheiro do frasco JDBC para o diretório de lib da Striim, e iniciar e parar o servidor.

   1. sudo su
   2. cd /tmp
   3. mv sqljdbc42.jar /opt/striim/lib
   4. sistemactl parar striim-nó
   5. sistemactl parar striim-dbms
   6. systemctl iniciar striim-dbms
   7. systemctl iniciar striim-nó

![Inicie o aglomerado Striim][start-striim]

Agora, abra o seu \<navegador favorito e navegue para DNS Name>:9080

![Navegue para o ecrã de login][navigate]

Inicie sessão com o nome de utilizador e a palavra-passe que configura no portal Azure e selecione o seu assistente preferido para começar, ou vá à página apps para começar a usar o arrasto e largar UI

![Iniciar sessão com credenciais do servidor][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
