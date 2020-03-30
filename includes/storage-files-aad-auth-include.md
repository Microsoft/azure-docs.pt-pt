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
ms.openlocfilehash: b248bbb526baf355faf2564358884fd83422b037
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565095"
---
[O Azure Files](../articles/storage/files/storage-files-introduction.md) suporta a autenticação baseada na identidade através do Bloco de Mensagens do Servidor (SMB) através do [Ative Directory (AD)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (pré-visualização) e dos Serviços de [Domínio do Diretório Ativo azure (Azure AD DS)](../articles/active-directory-domain-services/overview.md) (GA). Este artigo centra-se na forma como o Azure Files pode alavancar os serviços de domínio, quer no local, quer no Azure, para apoiar o acesso baseado na identidade aos Ficheiros Azure sobre sMB. Isto permite-lhe substituir facilmente os servidores de ficheiros existentes por Ficheiros Azure e continuar a utilizar o serviço de diretório existente, mantendo o acesso perfeito dos utilizadores às partilhas. 

O Azure Files impõe a autorização sobre o acesso do utilizador tanto à parte como ao nível de diretório/ficheiro. A atribuição de permissão de nível de partilha pode ser atribuída a utilizadores ou grupos da AD Azure geridos através do modelo típico de controlo de [acesso baseado em funções (RBAC).](../articles/role-based-access-control/overview.md) Com o RBAC, as credenciais que utiliza para acesso a ficheiros devem estar disponíveis ou sincronizadas com o Azure AD. Pode atribuir funções rBAC incorporadas, como Ficheiros de Armazenamento Data SMB Share Reader a utilizadores ou grupos em Azure AD, para garantir o acesso a uma partilha de ficheiros Azure.

Ao nível do diretório/ficheiro, o Azure Files suporta a preservação, herdação e aplicação de [DACLs do Windows,](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) tal como quaisquer servidores de ficheiros windows. Se copiar dados sobre SMB de uma partilha de ficheiros para Ficheiros Azure, ou vice-versa, pode optar por manter os DACLs do Windows. Quer planeie impor ou não a autorização, pode alavancar os Ficheiros Azure para backup de ACLs juntamente com os seus dados. 
