---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ff2ed5abbf2ce67a0b96a5da450b83832403db1f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269350"
---
[Os ficheiros do Azure](../articles/storage/files/storage-files-introduction.md) suporta a autenticação com base na identidade através de SMB (Server Message Block) (pré-visualização) por meio [serviços de domínio do Azure Active Directory (Azure AD)](../articles/active-directory-domain-services/overview.md). Máquinas de virtuais de Windows (VMs) do seu associados a um domínio podem aceder a partilhas de ficheiros do Azure com [do Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md) credenciais. 

Pode gerir o acesso de nível de partilha de ficheiros do Azure para uma identidade como um utilizador, grupo no Azure AD com [controlo de acesso baseado em funções (RBAC)](../articles/role-based-access-control/overview.md). Pode definir funções RBAC personalizadas que abrangem conjuntos comuns de permissões utilizadas para aceder a ficheiros do Azure. Se atribuir a função RBAC personalizada para uma identidade do Azure AD, que a identidade é concedida acesso a uma partilha de ficheiros do Azure, de acordo com essas permissões.

Como parte da pré-visualização, ficheiros do Azure também suporta preservando, herança e impor [NTFS DACLs](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) em todos os arquivos e diretórios numa partilha de ficheiros. Se copia dados de uma partilha de ficheiros para ficheiros do Azure, ou vice-versa, pode especificar que são mantidas DACLs de NTFS. Desta forma, pode implementar cenários de cópia de segurança através de ficheiros do Azure, preservando a DACLS de NTFS entre a partilha de ficheiros no local e a partilha de ficheiros na cloud. 

> [!NOTE]
> - Autenticação de serviço de domínio do AD do Azure para o acesso SMB não é suportada para VMs do Linux. Só são suportadas VMs do Windows.
> - Autenticação de serviço de domínio do AD do Azure para o acesso SMB não é suportada para o computador associado a domínio do Active Directory. Até lá, pode considerar para tirar partido [Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) para começar a migrar os seus dados para ficheiros do Azure e continuar para impor o controlo de acesso através de credenciais do AD do seu local AD associados a um domínio máquinas. 
> - Autenticação de serviço de domínio do AD do Azure para o acesso SMB está disponível apenas para contas de armazenamento criadas após 24 de Setembro de 2018.
> - Autenticação de serviço de domínio do AD do Azure para acesso SMB e NTFS DACL persistente não é suportada em partilhas de ficheiros do Azure geridas pelo serviço de sincronização de ficheiros do Azure. 
