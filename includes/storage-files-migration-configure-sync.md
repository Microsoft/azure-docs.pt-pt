---
title: Sincronização de ficheiros Azure configurando
description: Configurar a sincronização de ficheiros Azure. Um bloco de texto comum, partilhado através de documentos de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 562099c65273cad50596268b89055ec78d92d18a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82143565"
---
Este passo une todos os recursos e pastas que tenha configurado na sua instância do Windows Server durante os passos anteriores.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Localize o seu recurso de Serviço de Sincronização de Armazenamento.
1. Crie um novo *grupo de sincronização* dentro do recurso Storage Sync Service para cada partilha de ficheiros Azure. Na terminologia Azure File Sync, a partilha de ficheiros Azure tornar-se-á um *ponto final* em nuvem na topologia de sincronização que está descrevendo com a criação de um grupo de sincronização. Enquanto estás a criar o grupo de sincronização, dá-lhe um nome familiar para que reconheças que conjunto de ficheiros sincroniza aqui. Certifique-se de que faz referência à partilha de ficheiros Azure com um nome correspondente.
1. Após a criação do grupo de sincronização, aparecerá uma linha para que apareça na lista de grupos de sincronização. Selecione o nome (um link) para visualizar o conteúdo do grupo de sincronização. Verá a sua partilha de ficheiros Azure nos **pontos finais da Cloud.**
1. Localizar o botão **+ adicionar ponto final do servidor.** A pasta no servidor local que a provisionou tornar-se-á o caminho para este *ponto final do servidor*.
