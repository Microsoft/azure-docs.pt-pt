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
ms.openlocfilehash: d1b10b55481b41f42c6c872522d3dd4dd4be0e77
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570582"
---
[Os arquivos do Azure](../articles/storage/files/storage-files-introduction.md) oferecem suporte à autenticação baseada em identidade sobre o protocolo SMB (visualização) por meio [de Azure Active Directory Domain Services (AD DS)](../articles/active-directory-domain-services/overview.md). Suas VMs (máquinas virtuais) do Windows ingressadas no domínio podem acessar compartilhamentos de arquivos do Azure usando as credenciais [do Azure Active Directory (AD do Azure)](../articles/active-directory/fundamentals/active-directory-whatis.md) .

Você pode gerenciar o acesso em nível de compartilhamento dos arquivos do Azure a uma identidade como um usuário ou grupo no Azure AD usando o [RBAC (controle de acesso baseado em função)](../articles/role-based-access-control/overview.md). Você pode definir funções RBAC personalizadas que incluem conjuntos comuns de permissões usadas para acessar os arquivos do Azure. Quando você atribui sua função RBAC personalizada a uma identidade do Azure AD, essa identidade recebe acesso a um compartilhamento de arquivos do Azure de acordo com essas permissões.

Como parte da visualização, os arquivos do Azure também dão suporte à preservação, herança e imposição de [DACLs NTFS](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) em todos os arquivos e diretórios em um compartilhamento de arquivos. Se você copiar dados de um compartilhamento de arquivos para arquivos do Azure, ou vice-versa, poderá especificar que as DACLs NTFS sejam mantidas. Dessa forma, você pode implementar cenários de backup usando os arquivos do Azure, preservando suas DACLS NTFS entre o compartilhamento de arquivos local e o compartilhamento de arquivos na nuvem. 

> [!NOTE]
> - Não há suporte para a autenticação de AD DS do Azure para acesso de protocolo SMB para VMs do Linux. Só são suportadas VMs do Windows.
> - Não há suporte para a autenticação de AD DS do Azure para acesso SMB para Active Directory computadores ingressados no domínio. No ínterim, considere o uso de [sincronização de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) para começar a migrar seus dados para os arquivos do Azure e para continuar a impor o controle de acesso usando as credenciais de Active Directory do seu local Active Directory computadores ingressados no domínio. 
> - A autenticação de AD DS do Azure para acesso SMB está disponível somente para contas de armazenamento criadas após 24 de setembro de 2018.
> - Não há suporte para a autenticação de AD DS do Azure para acesso SMB e persistência de DACL NTFS em compartilhamentos de arquivos do Azure gerenciados pelo Sincronização de Arquivos do Azure.
