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
ms.openlocfilehash: 5c45dbe29bb86150c76cf5bc136c4a7504270f86
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854558"
---
[Os arquivos do Azure](../articles/storage/files/storage-files-introduction.md) oferecem suporte à autenticação baseada em identidade sobre o protocolo SMB por meio [de Azure Active Directory Domain Services (Azure AD DS)](../articles/active-directory-domain-services/overview.md). Suas VMs (máquinas virtuais) do Windows ingressadas no domínio podem acessar compartilhamentos de arquivos do Azure usando as credenciais [do Azure Active Directory (AD do Azure)](../articles/active-directory/fundamentals/active-directory-whatis.md) .

Você pode gerenciar o acesso em nível de compartilhamento dos arquivos do Azure a uma identidade como um usuário ou grupo no Azure AD usando o [RBAC (controle de acesso baseado em função)](../articles/role-based-access-control/overview.md). Você pode definir funções RBAC personalizadas que incluem conjuntos comuns de permissões usadas para acessar os arquivos do Azure. Quando você atribui sua função RBAC personalizada a uma identidade do Azure AD, essa identidade recebe acesso a um compartilhamento de arquivos do Azure de acordo com essas permissões.

Os arquivos do Azure também dão suporte à preservação, herança e imposição de [DACLs NTFS](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) em todos os arquivos e diretórios em um compartilhamento de arquivos. Se você copiar dados de um compartilhamento de arquivos para arquivos do Azure, ou vice-versa, poderá especificar que as DACLs NTFS sejam mantidas. Dessa forma, você pode implementar cenários de backup usando os arquivos do Azure, preservando suas DACLS NTFS entre o compartilhamento de arquivos local e o compartilhamento de arquivos na nuvem. 

> [!NOTE]
> - Não há suporte para a autenticação de AD DS do Azure para acesso de protocolo SMB para VMs do Linux. Só são suportadas VMs do Windows.
> - Não há suporte para a autenticação de AD DS do Azure para acesso SMB para Active Directory computadores ingressados no domínio. No ínterim, considere o uso de [sincronização de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) para começar a migrar seus dados para os arquivos do Azure e para continuar a impor o controle de acesso usando as credenciais de Active Directory do seu local Active Directory computadores ingressados no domínio. 
> - A autenticação de AD DS do Azure para acesso SMB está disponível somente para contas de armazenamento criadas após 24 de setembro de 2018.
> - Não há suporte para a autenticação de AD DS do Azure para acesso SMB e persistência de DACL NTFS em compartilhamentos de arquivos do Azure gerenciados pelo Sincronização de Arquivos do Azure.
> - A autenticação de AD DS do Azure não oferece suporte à autenticação em contas de computador criadas no Azure AD DS.
