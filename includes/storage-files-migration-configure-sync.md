---
title: Configurar sincronização de ficheiros Azure
description: Configure O Sincronizado de Ficheiros Azure. Um bloco de texto comum, partilhado entre documentos de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 53d7af6b43ff24ab12501570385162759f4c7633
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209587"
---
Este passo une todos os recursos e pastas que configurano seu Servidor Windows durante os passos anteriores.

* Inicie sessão no [Portal do Azure](https://portal.azure.com).
* Localize o seu recurso de Serviço de Sincronização de Armazenamento.
* Crie um novo *grupo de sincronização* dentro do recurso Storage Sync Service para cada partilha de ficheiros Azure. Na terminologia de Sincronização de Ficheiros Azure, a partilha de ficheiros Azure tornar-se-á um *ponto final* na topologia de sincronização que está a descrever com a criação de um grupo de sincronização. Enquanto está a criar o grupo de sincronização, dê-lhe um nome familiar, de tal forma que reconheça que conjunto de ficheiros sincroniza aqui. Certifique-se de que faz referência à partilha de ficheiros Azure com um nome correspondente.
* Assim que o grupo de sincronização for criado, verá uma linha para que apareça na lista de grupos de sincronização. Clique no nome (um link) para visualizar o conteúdo do grupo de sincronização. Verá a sua quota de ficheiroS Azure em "Cloud endpoints".
* Localize o botão de comando para *+ adicionar ponto final*do servidor . A pasta no servidor local que disponibilizou, tornar-se-á o caminho para este *ponto final*do servidor .
