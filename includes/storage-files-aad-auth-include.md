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
ms.openlocfilehash: 84fa97ec964d490eb9571c7e030704562a4a81d0
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95561808"
---
[A Azure Files](../articles/storage/files/storage-files-introduction.md) suporta a autenticação baseada na identidade sobre o Bloco de Mensagens do Servidor (SMB) através [dos serviços de domínio do diretório ativo (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) e [do Azure Ative Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md). Este artigo centra-se na forma como as ações de ficheiros Azure podem usar serviços de domínio, quer no local, quer no Azure, para apoiar o acesso baseado na identidade às ações de ficheiros Azure sobre a SMB. Permitir o acesso baseado na identidade das suas ações de ficheiros Azure permite-lhe substituir os servidores de ficheiros existentes por ações de ficheiros Azure sem substituir o seu serviço de diretório existente, mantendo o acesso sem emenda do utilizador às ações. 

A Azure Files impõe a autorização de acesso do utilizador tanto aos níveis de partilha como dos níveis de diretório/ficheiro. A atribuição de permissão ao nível de partilha pode ser realizada em utilizadores ou grupos do Azure Ative Directory (Azure AD) geridos através do modelo [Azure role-based access control (Azure RBAC).](../articles/role-based-access-control/overview.md) Com o RBAC, as credenciais que utiliza para acesso a ficheiros devem estar disponíveis ou sincronizadas com a Azure AD. Pode atribuir funções incorporadas ao Azure, como o Storage File Data SMB Share Reader, aos utilizadores ou grupos em Azure AD para conceder acesso à leitura de uma partilha de ficheiros Azure.

Ao nível do diretório/ficheiro, o Azure Files suporta a preservação, herdade e a aplicação [de DACLs do Windows](/windows/win32/secauthz/access-control-lists) tal como qualquer servidor de ficheiros Windows. Pode optar por manter os DACLs do Windows ao copiar dados sobre o SMB entre a sua parte de ficheiro existente e as suas ações de ficheiroS Azure. Quer planeie impor ou não a autorização, pode utilizar ações de ficheiros Azure para fazer o back up ACLs juntamente com os seus dados.