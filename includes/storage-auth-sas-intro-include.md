---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: aec1faa4de1149f08fb6fbc1cc5bf3aa2ab6becd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "75371786"
---
Uma assinatura de acesso partilhado (SAS) permite-lhe conceder acesso limitado a contentores e bolhas na sua conta de armazenamento. Ao criar um SAS, especifica os seus constrangimentos, incluindo os recursos de Armazenamento Azure a que um cliente pode aceder, quais as permissões que têm nesses recursos e quanto tempo o SAS é válido.

Todos os SAS são assinados com uma chave. Pode assinar um SAS de duas maneiras:

- Com uma chave criada utilizando credenciais azure Ative Directory (Azure AD). Um SAS assinado com credenciais Azure AD é uma *delegação de utilizadores* SAS.
- Com a chave da conta de armazenamento. Tanto um *serviço SAS como* uma *conta SAS* são assinados com a chave da conta de armazenamento.

Uma delegação de utilizadores SAS oferece uma segurança superior a um SAS que é assinado com a chave da conta de armazenamento. A Microsoft recomenda a utilização de uma delegação de utilizadores SAS, quando possível. Para obter mais informações, consulte [Grant acesso limitado a dados com assinaturas de acesso partilhado (SAS)](../articles/storage/common/storage-sas-overview.md).
