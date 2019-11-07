---
title: Início rápido do Striim
description: Comece rapidamente com o Striim e o Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 65dc0b8fcece4364f3828fee09221612b999df4e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685345"
---
# <a name="striim-azure-sql-dw-marketplace-offering-install-guide"></a>Guia de instalação de oferta do Azure SQL DW Marketplace Striim

Este guia de início rápido pressupõe que você já tem uma instância pré-existente do SQL Data Warehouse.

Pesquise Striim no Azure Marketplace e selecione a opção Striim for Data Integration to SQL Data Warehouse (preparado) 

![Instalar o Striim][install]

Configure a VM Striim com as propriedades especificadas, observando o nome do cluster Striim, a senha e a senha do administrador

![Configurar o Striim][configure]

Depois de implantado, clique em \<nome da VM >-masternode no portal do Azure, clique em conectar e copie o logon usando a conta local da VM 

![Conectar o Striim ao SQL Data Warehouse][connect]

Baixe o sqljdbc42. jar de <https://www.microsoft.com/en-us/download/details.aspx?id=54671> para seu computador local. 

Abra uma janela de linha de comando e altere os diretórios para onde você baixou o jar JDBC. SCP o arquivo JAR para sua VM Striim, obtendo o endereço e a senha do portal do Azure

![Copiar o arquivo JAR para sua VM][copy-jar]

Abra outra janela de linha de comando ou use um utilitário SSH para ssh no cluster Striim

![SSH no cluster][ssh]

Execute os comandos a seguir para mover o arquivo JAR do JDBC para o diretório lib do Striim e iniciar e parar o servidor.

   1. o sudo su
   2. CD/tmp
   3. MV sqljdbc42. jar/opt/striim/lib
   4. systemctl parar striim-nó
   5. systemctl parar striim-DBMS
   6. systemctl iniciar striim-DBMS
   7. systemctl iniciar striim-nó

![Iniciar o cluster Striim][start-striim]

Agora, abra seu navegador favorito e navegue até \<nome DNS >: 9080

![Navegue até a tela de logon][navigate]

Faça logon com o nome de usuário e a senha que você configurou na portal do Azure e selecione seu assistente preferido para começar ou acesse a página de aplicativos para começar a usar a interface do usuário de arrastar e soltar

![Fazer logon com as credenciais do servidor][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
