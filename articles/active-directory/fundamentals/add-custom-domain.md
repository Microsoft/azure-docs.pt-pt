---
title: Adicione o seu domínio personalizado - Diretório Ativo Azure / Microsoft Docs
description: Instruções sobre como adicionar um domínio personalizado usando o Diretório Ativo Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79262155"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Adicione o seu nome de domínio personalizado usando o portal azure ative diretório

Cada novo inquilino da AD Azure vem com um nome inicial de domínio, * \<nome de domínio>.onmicrosoft.com*. Não pode alterar ou apagar o nome de domínio inicial, mas pode adicionar os nomes da sua organização. Adicionar nomes de domínio personalizados ajuda-o a criar nomes de utilizadores que sejam familiares aos seus utilizadores, como *o alain\@contoso.com*.

## <a name="before-you-begin"></a>Antes de começar

Antes de poder adicionar um nome de domínio personalizado, crie o seu nome de domínio com um registo de domínio. Para um registo de domínio acreditado, consulte os [registos acreditados](https://www.icann.org/registrar-reports/accredited-list.html)pela ICANN .

## <a name="create-your-directory-in-azure-ad"></a>Crie o seu diretório em Azure AD

Depois de obter o seu nome de domínio, pode criar o seu primeiro diretório Azure AD. Inscreva-se no portal Azure para o seu diretório, utilizando uma conta com a função **Proprietário** para a subscrição.

Crie o seu novo diretório seguindo os passos em [Criar um novo inquilino para a sua organização.](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization)

>[!IMPORTANT]
>A pessoa que cria o inquilino é automaticamente o administrador global para esse inquilino. O administrador global pode adicionar administradores adicionais ao inquilino.

Para obter mais informações sobre funções de subscrição, consulte [as funções Do Azure RBAC.](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles)

>[!TIP]
> Se planeia federar o seu Anúncio de Servidor estivado no local com a AD Azure, então precisa selecionar **i planeio configurar este domínio para uma única inscrição com o meu Diretório Ativo local** quando executar a ferramenta Azure AD Connect para sincronizar os seus diretórios.
>
> Também tem de registar o mesmo nome de domínio que seleciona para a federação com o seu diretório no local no passo **Domínio do Azure AD** no assistente. Para ver como é a configuração, consulte [Verificar o domínio Azure AD selecionado para a federação](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation). Se não tiver a ferramenta Azure AD Connect, pode [descarregá-la aqui](https://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Adicione o seu nome de domínio personalizado ao Azure AD

Depois de criar o seu diretório, pode adicionar o seu nome de domínio personalizado.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com uma conta de Administrador global do diretório.

1. Procure e selecione *Azure Ative Directory* a partir de qualquer página. Em seguida, selecione Nomes de **domínio personalizados** > **Adicione domínio personalizado**.

    ![Página de nomes de domínio personalizado, com Adicionar domínio personalizado mostrado](media/add-custom-domain/add-custom-domain.png)

1. Em nome de **domínio Personalizado,** introduza o novo nome da sua organização, neste exemplo, *contoso.com*. Selecione **Add domain** (Adicionar domínio).

    ![Página de nomes de domínio personalizado, com página de domínio personalizado Adicionar](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >Deve incluir *.com*, *.net,* ou qualquer outra extensão de nível superior para que isto funcione corretamente.

    O domínio não verificado é adicionado. A página **contoso.com** aparece mostrando as suas informações de DNS. Guarde esta informação. Mais tarde, é necessário criar um disco TXT para configurar o DNS.

    ![Página contoso com informações de entrada do DNS](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>Adicione a sua informação dNS ao registo de domínio

Depois de adicionar o seu nome de domínio personalizado ao Azure AD, deve voltar ao seu registo de domínio e adicionar as informações de DNS AD Azure do seu ficheiro TXT copiado. Criar este registo TXT para o seu domínio verifica a propriedade do seu nome de domínio.

Volte ao seu registo de domínio e crie um novo registo TXT para o seu domínio com base nas suas informações de DNS copiadas. Detete a hora de viver (TTL) a 3600 segundos (60 minutos) e, em seguida, guarde o recorde.

>[!IMPORTANT]
>Pode registar quantos nomes de domínio quiser. No entanto, cada domínio obtém o seu próprio disco TXT a partir de Azure AD. Tenha cuidado ao introduzir as informações de ficheiroTT no registo de domínio. Se introduzir a informação errada ou duplicar por engano, terá de esperar até que o TTL saia (60 minutos) antes de poder tentar novamente.

## <a name="verify-your-custom-domain-name"></a>Verifique o seu nome de domínio personalizado

Depois de registar o seu nome de domínio personalizado, certifique-se de que é válido em Azure AD. A propagação do seu registo de domínio para AD Azure pode ser instantânea ou pode demorar alguns dias, dependendo do seu registo de domínio.

Para verificar o seu nome de domínio personalizado, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com uma conta de Administrador global do diretório.

1. Procure e selecione *O Diretório Ativo Azure* a partir de qualquer página e, em seguida, selecione nomes de **domínio personalizados**.

1. Em nomes de **domínio personalizados,** selecione o nome de domínio personalizado. Neste exemplo, selecione **contoso.com**.

    ![Fabrikam - Página de nomes de domínio personalizado, com contoso em destaque](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. Na página **contoso.com,** selecione **Verifique** se o seu domínio personalizado está devidamente registado e é válido para a AD Azure.

    ![Página Contoso com informações de entrada dNS e o botão Verificar](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

Depois de verificar o seu nome de domínio personalizado, pode eliminar o seu ficheiro TXT ou MX de verificação.

## <a name="common-verification-issues"></a>Questões comuns de verificação

Se a Azure AD não puder verificar um nome de domínio personalizado, experimente as seguintes sugestões:

- **Espere pelo menos uma hora e tente de novo.** Os registos DNS têm de ser propagados antes de o Azure AD poder verificar o domínio. Este processo pode demorar uma hora ou mais.

- **Certifique-se de que o registo DNS está correto.** Volte para o site de registo de nomes de domínio. Certifique-se de que a entrada está lá e que corresponde às informações de entrada do DNS fornecidas pela Azure AD.

  Se não conseguir atualizar o registo no site do registo, partilhe a entrada com alguém que tenha permissões para adicionar a entrada e verificar se está correto.

- **Certifique-se de que o nome de domínio não está já em uso noutro diretório.** Um nome de domínio só pode ser verificado num diretório. Se o seu nome de domínio for verificado em outro diretório, também não pode ser verificado no novo diretório. Para corrigir este problema de duplicação, deve eliminar o nome de domínio do antigo diretório. Para mais informações sobre a abater nomes de domínio, consulte [Gerir nomes de domínio personalizados](../users-groups-roles/domains-manage.md).

- **Certifique-se de que não tem inquilinos não geridos do Power BI.** Se os seus utilizadores ativaram o Power BI através de um autosserviço e criaram um inquilino não gerido para a sua organização, deve assumir a gestão como administrador interno ou externo, utilizando o PowerShell. Para obter mais informações, veja [Take over an unmanaged directory as administrator in Azure Active Directory](../users-groups-roles/domains-admin-takeover.md) (Assumir um diretório não gerido como administrador no Azure Active Directory).

## <a name="next-steps"></a>Passos seguintes

- Adicione outro administrador global ao seu diretório. Para mais informações, consulte [Como atribuir funções e administradores.](active-directory-users-assign-role-azure-portal.md)

- Adicione utilizadores ao seu domínio. Para mais informações, consulte [Como adicionar ou eliminar os utilizadores](add-users-azure-active-directory.md).

- Gerencie a informação do seu nome de domínio em Azure AD. Para mais informações, consulte Gerir nomes de [domínio personalizados](../users-groups-roles/domains-manage.md).

- Se tiver versões no local do Windows Server que pretende utilizar ao lado do Diretório Ativo do Azure, consulte [Integrar os seus diretórios no local com o Diretório Ativo Azure](../connect/active-directory-aadconnect.md).
