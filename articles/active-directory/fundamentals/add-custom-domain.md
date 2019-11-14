---
title: Adicionar o seu domínio personalizado - Azure Active Directory | Documentos da Microsoft
description: Instruções sobre como adicionar um domínio personalizado com o Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: elkuzmen
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: de91bd7e1e4c5f9909213f663dd3ede0f979d4de
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073538"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Adicionar o seu nome de domínio personalizado com o portal do Azure Active Directory

Todos os novos locatários do Azure AD vêm com um nome de domínio inicial, *\<domainname >. onmicrosoft. com*. Você não pode alterar ou excluir o nome de domínio inicial, mas pode adicionar os nomes de sua organização. Adicionar nomes de domínio personalizados ajuda a criar nomes de usuário que são familiares para seus usuários, como *alain\@contoso.com*.

## <a name="before-you-begin"></a>Antes de começar

Para poder adicionar um nome de domínio personalizado, crie seu nome de domínio com um registrador de domínio. Para uma entidade de registo do domínio acreditada, consulte [ICANN-Accredited entidades de registo](https://www.icann.org/registrar-reports/accredited-list.html).

## <a name="create-your-directory-in-azure-ad"></a>Criar o seu diretório no Azure AD

Depois de obter o seu nome de domínio, é possível criar o primeiro diretório do Azure AD. Entre no portal do Azure do seu diretório, usando uma conta com a função de **proprietário** para a assinatura.

Criar novo diretório ao seguir os passos em [criar um novo inquilino para a sua organização](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization).

>[!IMPORTANT]
>A pessoa que cria o inquilino é automaticamente o Administrador Global desse inquilino. O Administrador Global pode adicionar outros administradores de inquilinos.

Para obter mais informações sobre as funções de assinatura, consulte [funções de RBAC do Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles).

>[!TIP]
> Se você planeja federar seu AD do Windows Server local com o Azure AD, você precisa selecionar o **plano para configurar esse domínio para logon único com o meu Active Directory local** ao executar a ferramenta de Azure ad Connect para sincronizar seus diretórios.
>
> Também tem de registar o mesmo nome de domínio que seleciona para a federação com o seu diretório no local no passo **Domínio do Azure AD** no assistente. Para ver como essa configuração se parece, consulte [verificar o domínio do Azure ad selecionado para Federação](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation). Se você não tiver a ferramenta Azure AD Connect, poderá [baixá-la aqui](https://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Adicionar o seu nome de domínio personalizado para o Azure AD

Depois de criar o seu diretório, pode adicionar o seu nome de domínio personalizado.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com uma conta de Administrador global do diretório.

1. Pesquise e selecione *Azure Active Directory* em qualquer página. Em seguida, selecione **nomes de domínio personalizados** > **Adicionar domínio personalizado**.

    ![Página nomes de domínio personalizados, com Adicionar domínio personalizado mostrado](media/add-custom-domain/add-custom-domain.png)

1. Em **nome de domínio personalizado**, insira o novo nome da sua organização, neste exemplo, *contoso.com*. Selecione **Add domain** (Adicionar domínio).

    ![Página nomes de domínio personalizados, com a página Adicionar domínio personalizado](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >Você deve incluir *. com*, *.net*ou qualquer outra extensão de nível superior para que isso funcione corretamente.

    O domínio não verificado é adicionado. A página **contoso.com** aparece mostrando as informações de DNS. Salve essas informações. Você precisará dela mais tarde para criar um registro TXT para configurar o DNS.

    ![Página de contoso com informações de entrada DNS](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>Adicione as suas informações de DNS para a entidade de registo do domínio

Depois de adicionar o seu nome de domínio personalizado para o Azure AD, tem de voltar à sua entidade de registo do domínio e adicione as informações de DNS do Azure AD a partir do ficheiro TXT copiado. A criação desse registro TXT para seu domínio verifica a propriedade do seu nome de domínio.

Volte para seu registrador de domínio e crie um novo registro TXT para seu domínio com base nas informações de DNS copiadas. Defina o tempo de vida (TTL) como 3600 segundos (60 minutos) e salve o registro.

>[!IMPORTANT]
>Pode registrar os nomes de domínio como quiser. No entanto, cada domínio obtém seu próprio registo TXT do Azure AD. Tenha cuidado ao inserir as informações do arquivo TXT no registrador de domínios. Se você inserir as informações erradas ou duplicadas por engano, terá que aguardar até que o TTL expire (60 minutos) antes de tentar novamente.

## <a name="verify-your-custom-domain-name"></a>Verificar o seu nome de domínio personalizado

Depois de registrar seu nome de domínio personalizado, verifique se ele é válido no Azure AD. A propagação do seu registrador de domínio para o Azure AD pode ser instantânea ou pode levar alguns dias, dependendo do seu registrador de domínio.

Para verificar seu nome de domínio personalizado, siga estas etapas:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com uma conta de Administrador global do diretório.

1. Procure e selecione *Azure Active Directory* em qualquer página e, em seguida, selecione **nomes de domínio personalizados**.

1. Em **nomes de domínio personalizados**, selecione o nome de domínio personalizado. Neste exemplo, selecione **contoso.com**.

    ![Fabrikam - página de nomes de domínio personalizado, com contoso realçado](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. Na página **contoso.com** , selecione **verificar** para certificar-se de que seu domínio personalizado está registrado corretamente e é válido para o Azure AD.

    ![Página de contoso com informações de entrada DNS e no botão verificar](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

Depois de verificar seu nome de domínio personalizado, você pode excluir seu arquivo TXT ou MX de verificação.

## <a name="common-verification-issues"></a>Problemas comuns de verificação

Se o Azure AD não é possível verificar um nome de domínio personalizado, experimente as sugestões seguintes:

- **Aguarde, pelo menos, uma hora e tente novamente**. Os registos DNS têm de ser propagados antes de o Azure AD poder verificar o domínio. Este processo pode demorar uma hora ou mais.

- **Certifique-se de que o registo DNS está correto.** Volte para o site de registrador de nomes de domínio. Verifique se a entrada está lá e se ela corresponde às informações de entrada de DNS fornecidas pelo Azure AD.

  Se você não puder atualizar o registro no site do registrador, compartilhe a entrada com alguém que tenha permissões para adicionar a entrada e verifique se ela está correta.

- **Certifique-se de que o nome de domínio não está já em utilização de outro diretório.** Um nome de domínio só pode ser verificado em um diretório. Se o seu nome de domínio estiver atualmente verificado em outro diretório, ele também não poderá ser verificado no novo diretório. Para corrigir esse problema de duplicação, tem de eliminar o nome de domínio do diretório antigo. Para obter mais informações sobre como eliminar nomes de domínio, consulte [gerir nomes de domínio personalizado](../users-groups-roles/domains-manage.md).

- **Certifique-se de que não tem quaisquer inquilinos do Power BI não geridos.** Se os utilizadores ativaram o Power BI através de inscrição Self-Service e criado um inquilino não gerido para a sua organização, deve assumir a gestão como um administrador interno ou externo, com o PowerShell. Para obter mais informações, veja [Take over an unmanaged directory as administrator in Azure Active Directory](../users-groups-roles/domains-admin-takeover.md) (Assumir um diretório não gerido como administrador no Azure Active Directory).

## <a name="next-steps"></a>Passos seguintes

- Adicione outro administrador Global para o seu diretório. Para obter mais informações, consulte [como atribuir funções e administradores](active-directory-users-assign-role-azure-portal.md).

- Adicione usuários ao seu domínio. Para obter mais informações, consulte [como adicionar ou excluir usuários](add-users-azure-active-directory.md).

- Gerir as suas informações de nome de domínio no Azure AD. Para obter mais informações, consulte [Gerenciando nomes de domínio personalizados](../users-groups-roles/domains-manage.md).

- Se tiver versões no local do Windows Server que pretende utilizar em conjunto com o Azure Active Directory, consulte [integrar seus diretórios no local com o Azure Active Directory](../connect/active-directory-aadconnect.md).
