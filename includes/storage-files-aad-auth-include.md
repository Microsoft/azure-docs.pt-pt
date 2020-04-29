---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 167d50e5c7f3049f46fd8540630e47044240809f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536591"
---
[O Azure Files](../articles/storage/files/storage-files-introduction.md) suporta a autenticação baseada na identidade através do Bloco de Mensagens do Servidor (SMB) através dos Serviços de Domínio ativo do [Diretório Ativo (AD DS) (pré-visualização)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) e dos Serviços de [Domínio do Diretório Ativo azure (Azure AD DS)](../articles/active-directory-domain-services/overview.md). Este artigo centra-se na forma como as ações de ficheiros azure podem utilizar serviços de domínio, quer no local, quer no Azure, para apoiar o acesso baseado na identidade a ações de ficheiros Azure sobre a SMB. Permitir o acesso baseado na identidade das suas ações de ficheiros Azure permite-lhe substituir os servidores de ficheiros existentes por ações de ficheiros Azure sem substituir o serviço de diretório existente, mantendo o acesso perfeito dos utilizadores às ações. 

O Azure Files impõe a autorização de acesso ao utilizador tanto para os níveis de diretório/ficheiro. A atribuição de permissão de nível de partilha pode ser realizada em utilizadores ou grupos de utilizadores ou grupos de diretório seletiva (Azure AD) do Azure Ative Directory (Azure AD) geridos através do modelo de controlo de [acesso baseado em funções (RBAC).](../articles/role-based-access-control/overview.md) Com o RBAC, as credenciais que utiliza para acesso a ficheiros devem estar disponíveis ou sincronizadas com o Azure AD. Pode atribuir funções rBAC incorporadas, como Ficheiros de Armazenamento Data SMB Share Reader a utilizadores ou grupos em Azure AD, para garantir o acesso a uma partilha de ficheiros Azure.

Ao nível do diretório/ficheiro, o Azure Files suporta a preservação, herdação e aplicação de [DACLs do Windows,](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) tal como quaisquer servidores de ficheiros windows. Pode optar por manter os DACLs do Windows ao copiar dados sobre sMB entre a sua quota de ficheiros existente e as suas partilhas de ficheiros Azure. Quer pretenda impor ou não a autorização, pode utilizar ações de ficheiros Azure para fazer o backback acLs juntamente com os seus dados. 
