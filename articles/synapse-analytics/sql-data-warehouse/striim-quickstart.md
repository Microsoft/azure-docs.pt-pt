---
title: Striim arranque rápido
description: Começa rapidamente com a Striim e a Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d6b5fa1a42201479c5d426a5bbb9188d0cb8e03d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89485979"
---
# <a name="striim-azure-synapse-analytics-marketplace-offering-install-guide"></a>Striim Azure Synapse Analytics Marketplace Guia de instalação

Este quickstart assume que já tem um caso pré-existente de Azure Synapse Analytics.

Procure por Striim no Azure Marketplace e selecione o Striim for Data Integration to Azure Synapse Analytics (Staged) 

![Instalar Striim][install]

Configure o Striim VM com propriedades especificadas, anotendo o nome do cluster Striim, palavra-passe e senha de administração

![Configurar Striim][configure]

Uma vez implementado, clique em \<VM Name> -masternode no portal Azure, clique em Connect e copie o Login usando a conta local VM 

![Ligue Striim a Azure Synapse Analytics][connect]

Descarregue o sqljdbc42.jar <https://www.microsoft.com/en-us/download/details.aspx?id=54671> para a sua máquina local. 

Abra uma janela de linha de comando e mude os diretórios para onde descarregou o frasco JDBC. SCP o ficheiro do frasco para o seu Striim VM, obtendo o endereço e senha do portal Azure

![Copy jar file para o seu VM][copy-jar]

Abra outra janela de linha de comando, ou use um utilitário ssh para ssh no cluster Striim

![SSH no cluster][ssh]

Execute os seguintes comandos para mover o ficheiro do frasco JDBC para o diretório de lib-lib do Striim e iniciar e parar o servidor.

   1. sudo su
   2. cd /tmp
   3. mv sqljdbc42.jar /opt/striim/lib
   4. sistema stop striim-node
   5. paragem sistemacídlia striim-dbms
   6. sistema iniciar striim-dbms
   7. sistema iniciar striim-node

![Inicie o cluster Striim][start-striim]

Agora, abra o seu navegador favorito e navegue até \<DNS Name> :9080

![Navegue para o ecrã de login][navigate]

Faça login com o nome de utilizador e a palavra-passe que criou no portal Azure e selecione o seu assistente preferido para iniciar, ou vá à página apps para começar a usar o UI de arrasto e de lançamento

![Faça login com credenciais de servidor][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
