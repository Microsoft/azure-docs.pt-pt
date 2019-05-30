---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/22/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 0cfa0fdb51969c92e767adfa86a0065d11da56e2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237764"
---
[Os ficheiros do Azure](../articles/storage/files/storage-files-introduction.md) suporta a autenticação com base na identidade através de SMB (Server Message Block) (pré-visualização) por meio [serviços de domínio do Azure Active Directory (Azure AD)](../articles/active-directory-domain-services/overview.md). Máquinas de virtuais de Windows (VMs) do seu associados a um domínio podem aceder a partilhas de ficheiros do Azure com [do Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md) credenciais. 

O Azure AD autentica uma identidade como um utilizador, grupo ou principal de serviço com [controlo de acesso baseado em funções (RBAC)](../articles/role-based-access-control/overview.md). Pode definir funções RBAC personalizadas que abrangem conjuntos comuns de permissões utilizadas para aceder a ficheiros do Azure. Se atribuir a função RBAC personalizada para uma identidade do Azure AD, que a identidade é concedida acesso a uma partilha de ficheiros do Azure, de acordo com essas permissões.

Como parte da pré-visualização, ficheiros do Azure também suporta preservando, herança e impor [NTFS DACLs](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) em todos os arquivos e diretórios numa partilha de ficheiros. Se copia dados de uma partilha de ficheiros para ficheiros do Azure, ou vice-versa, pode especificar que são mantidas DACLs de NTFS. Desta forma, pode implementar cenários de cópia de segurança através de ficheiros do Azure, preservando a DACLS de NTFS entre a partilha de ficheiros no local e a partilha de ficheiros na cloud. 

> [!NOTE]
> - Autenticação do Azure AD através de SMB não é suportada para VMs do Linux para a versão de pré-visualização. Apenas VMs do Windows Server são suportadas.
> - Autenticação do Azure AD através de SMB não é suportada para máquinas no local a aceder a ficheiros do Azure.
> - Autenticação do Azure AD está disponível apenas para contas de armazenamento criadas após 24 de Setembro de 2018.
> - Autenticação do Azure AD através de SMB e a ACL de NTFS persistente não é suportada em partilhas de ficheiros do Azure geridas pelo serviço de sincronização de ficheiros do Azure. 
