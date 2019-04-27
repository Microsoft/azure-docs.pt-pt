---
title: Configurar o DNS para aceder a um domínio gerido, utilizar o LDAPS através da internet | Documentos da Microsoft
description: Configurar o DNS para aceder a um domínio gerido do Azure AD Domain Services utilizar o LDAPS através da internet
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: a47f0f3e-2578-422a-a421-034f66de38f5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: ergreenl
ms.openlocfilehash: 122282d168246e34aaa4a6369f7433b167355887
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60416743"
---
# <a name="configure-dns-to-access-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Configurar o DNS para aceder a um domínio gerido do Azure AD Domain Services usando o LDAP seguro (LDAPS)

## <a name="before-you-begin"></a>Antes de começar
Completa [tarefa 3: Ativar o LDAP seguro para o domínio gerido com o portal do Azure](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)

## <a name="task-4-configure-dns-to-access-the-managed-domain-from-the-internet"></a>Tarefa 4: configurar o DNS para aceder ao domínio gerido a partir da Internet
> [!TIP]
> **Tarefas opcionais** - se não planear acessar o domínio gerido com o LDAPS através da internet, ignorar esta tarefa de configuração.
>
>

Antes de começar esta tarefa, complete os passos descritos em [tarefa 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).

Depois de ativar o acesso de secure LDAP pela internet, terá de atualizar o DNS para que os computadores cliente podem encontrar este domínio gerido. Verá um endereço IP externo a **propriedades** separador **externo para LDAPS acesso do endereço IP**.

Configure o fornecedor DNS externo para que o nome DNS do domínio gerido (por exemplo, "ldaps.contoso100.com"), aponte para este endereço IP externo. Por exemplo, crie a seguinte entrada DNS:

    ldaps.contoso100.com  -> 52.165.38.113

Já está! Agora, está pronto para ligar ao domínio gerido através de secure LDAP pela internet.

> [!WARNING]
> Lembre-se de que computadores cliente têm de confiar o emissor do certificado LDAPS para conseguir ligar-se com êxito para o domínio gerido com o LDAPS. Se estiver a utilizar uma autoridade de certificação fidedigna publicamente, não é necessário fazer nada, uma vez que os computadores cliente confiar estes emissores de certificados. Se estiver a utilizar um certificado autoassinado, instale a parte pública do certificado autoassinado para o arquivo de certificado fidedigno no computador cliente.
>
>

## <a name="next-step"></a>Passo seguinte
[Tarefa 5: vincular ao domínio gerido e bloquear o acesso LDAP seguro](active-directory-ds-ldaps-bind-lockdown.md)
