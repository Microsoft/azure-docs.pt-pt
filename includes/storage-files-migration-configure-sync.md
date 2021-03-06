---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 7aa3867fdc5de320c47a15737b655b8032f402a6
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075621"
---
Este passo une todos os recursos e pastas que tenha configurado na sua instância do Windows Server durante os passos anteriores.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Localize o seu recurso de Serviço de Sincronização de Armazenamento.
1. Crie um novo *grupo de sincronização* dentro do recurso Storage Sync Service para cada partilha de ficheiros Azure. Na terminologia Azure File Sync, a partilha de ficheiros Azure tornar-se-á um *ponto final* em nuvem na topologia de sincronização que está descrevendo com a criação de um grupo de sincronização. Enquanto estás a criar o grupo de sincronização, dá-lhe um nome familiar para que reconheças que conjunto de ficheiros sincroniza aqui. Certifique-se de que faz referência à partilha de ficheiros Azure com um nome correspondente.
1. Após a criação do grupo de sincronização, aparecerá uma linha para que apareça na lista de grupos de sincronização. Selecione o nome (um link) para visualizar o conteúdo do grupo de sincronização. Verá a sua partilha de ficheiros Azure nos **pontos finais da Cloud.**
1. Localizar o botão **+ adicionar ponto final do servidor.** A pasta no servidor local que a provisionou tornar-se-á o caminho para este *ponto final do servidor*.
