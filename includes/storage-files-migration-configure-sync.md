---
title: Configurar sincronização de ficheiros Azure
description: Configure O Sincronizado de Ficheiros Azure. Um bloco de texto comum, partilhado em todos os documentos migratórios.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 562099c65273cad50596268b89055ec78d92d18a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143565"
---
Este passo une todos os recursos e pastas que configuraste na instância do Windows Server durante os passos anteriores.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Localize o seu recurso de Serviço de Sincronização de Armazenamento.
1. Crie um novo *grupo de sincronização* dentro do recurso Storage Sync Service para cada partilha de ficheiros Azure. Na terminologia de Sincronização de Ficheiros Azure, a partilha de ficheiros Azure tornar-se-á um *ponto final* na topologia de sincronização que está a descrever com a criação de um grupo de sincronização. Enquanto está a criar o grupo de sincronização, dê-lhe um nome familiar para que reconheça que conjunto de ficheiros sincroniza aqui. Certifique-se de que faz referência à partilha de ficheiros Azure com um nome correspondente.
1. Após a criação do grupo de sincronização, uma linha para ele aparecerá na lista de grupos de sincronização. Selecione o nome (um link) para visualizar o conteúdo do grupo de sincronização. Verá a sua quota de ficheiroS Azure em **cloud endpoints**.
1. Localize o botão + Adicionar ponto final do **servidor.** A pasta no servidor local que disponibilizou tornar-se-á o caminho para este *ponto final*do servidor .
