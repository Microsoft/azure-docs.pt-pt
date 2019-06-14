---
title: Ativar o LDAP seguro (LDAPS) no Azure AD Domain Services | Documentos da Microsoft
description: Ativar o LDAP seguro (LDAPS) para um domínio gerido do Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: ergreenl
ms.openlocfilehash: df189e405dcd5277c1ccbd94e9d5d302660be79b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60359865"
---
# <a name="enable-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Ativar o secure LDAP (LDAPS) para um domínio gerido do Azure AD Domain Services

## <a name="before-you-begin"></a>Antes de começar
Completa [tarefa 2 - exportar o certificado de secure LDAP para um. Ficheiro PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md).


## <a name="task-3-enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>Tarefa 3: Ativar o LDAP seguro para o domínio gerido com o portal do Azure
Para ativar o LDAP seguro, execute os seguintes passos de configuração:

1. Navegue para o  **[portal do Azure](https://portal.azure.com)** .

2. Procure 'Serviços de domínio' na **pesquisar recursos** caixa de pesquisa. Selecione **Azure AD Domain Services** do resultado da pesquisa. O **Azure AD Domain Services** página apresenta uma lista de seu domínio gerido.

    ![Encontrar o domínio gerido, que está a ser aprovisionado](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Clique no nome do domínio gerido (por exemplo, ' contoso100.com') para ver mais detalhes sobre o domínio.

    ![Serviços de domínio - estado de aprovisionamento](./media/getting-started/domain-services-provisioning-state.png)

3. Clique em **Secure LDAP** no painel de navegação.

    ![Serviços de domínio - página de Secure LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. Por predefinição, o acesso de secure LDAP ao seu domínio gerido está desativado. Botão de alternar **LDAP seguro** ao **ativar**.

    ![Ativar o LDAP seguro](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. Por predefinição, o acesso de secure LDAP ao seu domínio gerido através da internet está desativado. Botão de alternar **permitir o acesso de secure LDAP pela internet** para **ativar**, se for necessário.

    > [!WARNING]
    > Quando ativar o acesso de secure LDAP pela internet, seu domínio é suscetível a ataques de força bruta de palavra-passe através da internet. Por conseguinte, recomendamos que configuração de um NSG para bloquear o acesso aos intervalos de endereços IP de origem necessários. Veja as instruções para [bloquear o acesso LDAPS ao seu domínio gerido através da internet](active-directory-ds-ldaps-bind-lockdown.md#task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet).
    >

6. Clique em seguinte de ícone pasta **. Ficheiro PFX com certificado de secure LDAP**. Especifique o caminho para o ficheiro PFX com o certificado para acesso de secure LDAP para o domínio gerido.

7. Especifique o **palavra-passe para desencriptar. Ficheiro PFX**. Fornece a mesma palavra-passe que utilizou ao exportar o certificado para o ficheiro PFX.

8. Quando tiver terminado, clique nas **guardar** botão.

9. Verá uma notificação que informa a que proteger o LDAP está a ser configurada para o domínio gerido. Até que esta operação estiver concluída, não é possível modificar outras definições para o domínio.

    ![Configurar o secure LDAP para o domínio gerido](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> Demora cerca de 10 a 15 minutos para ativar o LDAP seguro para o seu domínio gerido. Se o certificado de secure LDAP fornecido não coincide com os critérios necessários, o LDAP seguro não está ativado para o seu diretório e vir uma falha. Por exemplo, o nome de domínio está incorreto, o certificado já expirou ou expira em breve. Neste caso, tente novamente com um certificado válido.
>
>

## <a name="next-step"></a>Passo seguinte
[Tarefa 4: configurar o DNS para aceder ao domínio gerido a partir da Internet](active-directory-ds-ldaps-configure-dns.md)
