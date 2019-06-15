---
title: Início rápido do Striim com o Azure SQL Data Warehouse | Documentos da Microsoft
description: Comece rapidamente com Striim e Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 8ed9936884a648d736942caecade2ac3c2980e67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873401"
---
# <a name="striim-azure-sql-dw-marketplace-offering-install-guide"></a>Striim oferta do Azure SQL DW Marketplace guia de instalação

Este guia de introdução pressupõe que já tem uma instância já existente do SQL Data Warehouse.

Procure Striim no Azure Marketplace e selecione o Striim para integração de dados para opção do SQL Data Warehouse (teste) 

![Instalar Striim][install]

Configurar a VM de Striim com propriedades especificadas, anota o nome do cluster Striim, a palavra-passe e a palavra-passe de administrador

![Configurar Striim][configure]

Uma vez implantado, clique em \<VM Name >-masternode no portal do Azure, clique em ligar e copiar o início de sessão com conta local de VM 

![Ligar Striim ao SQL Data Warehouse][connect]

Transferir o sqljdbc42.jar de <https://www.microsoft.com/en-us/download/details.aspx?id=54671> no seu computador local. 

Abra uma janela da linha de comandos e mude de diretório para onde transferiu o jar do JDBC. SCP o ficheiro jar para a VM Striim, obter o endereço e a palavra-passe do portal do Azure

![Copie o ficheiro jar para a VM][copy-jar]

Abrir outra janela da linha de comandos ou utilize um ssh utilitário para ssh para o Striim do cluster

![SSH para o cluster][ssh]

Execute os seguintes comandos para mover o ficheiro jar do JDBC no diretório do Striim lib e iniciar e parar o servidor.

   1. sudo su
   2. CD /tmp
   3. mv sqljdbc42.jar /opt/striim/lib
   4. parar de systemctl striim-nó
   5. parar de systemctl striim-dbms
   6. início de systemctl striim-dbms
   7. striim-nó systemctl inicial

![Inicie o cluster do Striim][start-striim]

Agora, abra o seu browser favorito e navegue para \<nome DNS >: 9080

![Navegue para o ecrã de início de sessão][navigate]

Iniciar sessão com o nome de utilizador e a palavra-passe que configurar no portal do Azure e selecione o seu assistente preferido para começar a utilizar ou vá para a página de aplicações para começar a utilizar o arrastar e soltar da interface do Usuário

![Inicie sessão com as credenciais do servidor][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
