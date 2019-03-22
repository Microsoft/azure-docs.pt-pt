---
title: 'Azure Active Directory Domain Services: Introdução | Documentos da Microsoft'
description: Ativar o Azure Active Directory Domain Services no portal do Azure
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: ergreenl
ms.openlocfilehash: 637ad62744affa37630df9c841f3c7529674e788
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122914"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Ativar o Azure Active Directory Domain Services no portal do Azure
Este artigo mostra como ativar o Azure Active Directory Domain Services (Azure AD DS) no portal do Azure.


## <a name="before-you-begin"></a>Antes de começar
Para concluir as tarefas apresentadas neste artigo, precisa:

* Válido **subscrição do Azure**.
* Uma **diretório do Azure AD** -seja sincronizada com um diretório no local ou um diretório apenas na cloud.
* O **subscrição do Azure tem de ser associada com o diretório do Azure AD**.
* Precisa **administrador global** privilégios no diretório do Azure AD para ativar os serviços de domínio do Azure AD.


## <a name="enable-azure-ad-domain-services"></a>Ativar o Azure AD Domain Services

Para iniciar o **ativar o Azure AD Domain Services** assistente, conclua os seguintes passos:

1. Aceda ao [Portal do Azure](https://portal.azure.com).
2. No painel esquerdo, clique em **Criar um recurso**.
3. Na **New** página, escreva **dos serviços de domínio** na barra de pesquisa.

    ![Procure os serviços de domínio](./media/getting-started/search-domain-services.png)

4. Clique para selecionar **Azure AD Domain Services** na lista de sugestões de pesquisa. Sobre o **serviços de domínio do Azure AD** página, clique no **criar** botão.

    ![Vista de serviços de domínio](./media/getting-started/domain-services-blade.png)

5. O **ativar o Azure AD Domain Services** wizard é iniciado.


## <a name="task-1-configure-basic-settings"></a>Tarefa 1: configurar as definições básicas
Na **Noções básicas** página do assistente, especifique o nome de domínio DNS para o domínio gerido. Também pode escolher o grupo de recursos e a localização do Azure para o qual o domínio gerido deve ser implementado.

![Configurar as noções básicas](./media/getting-started/domain-services-blade-basics.png)

1. Escolha o **nome de domínio DNS** para o seu domínio gerido.

   > [!NOTE]
   > **Diretrizes para selecionar um nome de domínio DNS**
   > * **Nome de domínio internos:** Por predefinição, o assistente Especifica o nome de domínio predefinido/interno-in do diretório (com uma **. onmicrosoft.com** sufixo) para. Se optar por ativar o acesso de secure LDAP para o domínio gerido através da internet, espere problemas de criar um registo DNS público ou obter um certificado de secure LDAP de uma AC pública para este nome de domínio. A Microsoft possui os *. onmicrosoft.com* ACs e de domínio não emitirá certificados pela garantia para este domínio.
   > * **Nomes de domínio personalizado:** Também pode escrever um nome de domínio personalizado. Neste exemplo, o nome de domínio personalizado é *contoso100.com*.
   > * **Sufixos de domínio não encaminháveis:** Em geral, recomendamos que evite um sufixo de nome de domínio não encaminháveis internos. Por exemplo, é melhor evitar a criação de um domínio com o nome de domínio DNS "contoso.com". O sufixo DNS. 'local' não é roteável e pode causar problemas de resolução de DNS.
   > * **Restrições de prefixo de domínio:** O prefixo do seu nome de domínio especificado (por exemplo, contoso100 no nome de domínio *contoso100.com*) tem de conter 15 ou menos carateres. Não é possível criar um domínio gerido com um prefixo mais de 15 carateres.
   > * **Conflitos de nome de rede:** Certifique-se de que o nome de domínio DNS que escolheu para o domínio gerido ainda não existe na rede virtual. Especificamente, verifique se:
   >     * Já tem um domínio do Active Directory com o mesmo nome de domínio DNS na rede virtual.
   >     * A rede virtual em que pretende ativar o domínio gerido tem uma ligação VPN à sua rede no local. Neste cenário, certifique-se que não tiver um domínio com o mesmo nome de domínio DNS na sua rede no local.
   >     * Tem um serviço cloud existente com este nome na rede virtual.

2. Selecione o Azure **subscrição** no qual gostaria de criar o domínio gerido.

3. Selecione o **grupo de recursos** que deve pertencer ao domínio gerido. Escolher entre o **criar novo** ou **utilizar existente** as opções para selecionar o grupo de recursos.

4. Escolha do Azure **localização** na qual o domínio gerido deve ser criado. Sobre o **rede** página do assistente, verá que as redes virtuais apenas que pertencem a localização que selecionou.

5. Clique em **OK** mover para o **rede** página do assistente.


## <a name="next-step"></a>Passo seguinte
[Tarefa 2: configurar as definições de rede](active-directory-ds-getting-started-network.md)
