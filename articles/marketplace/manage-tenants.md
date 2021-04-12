---
title: Gerir inquilinos no mercado comercial - Azure Marketplace
description: Saiba como gerir os inquilinos para o programa de marketplace comercial no Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: 446792b26527126a4db99da14a2585c17cf8610c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108455"
---
# <a name="manage-tenants-in-the-commercial-marketplace"></a>Gerir inquilinos no mercado comercial

**Funções adequadas**

- Gestor

Um inquilino do Azure Ative Directory (AD), também referido como sua conta de *trabalho* ao longo desta documentação, é uma representação da sua organização criada no portal Azure e ajuda-o a gerir uma instância específica dos serviços de cloud da Microsoft para os seus utilizadores internos e externos. Se a sua organização subscreveu um serviço de cloud da Microsoft, como a Azure, a Microsoft Intune ou a Microsoft 365, foi criado um inquilino AZure AD para si.

Você pode configurar vários inquilinos para usar com Partner Center. Você pode querer fazê-lo se sua empresa tem vários inquilinos (por exemplo, contoso.com, contoso.uk, e assim por diante) você pode ligar os inquilinos adicionais aqui. Esta associação permitir-lhe-ia adicionar e gerir utilizadores dos inquilinos adicionais na sua conta de mercado comercial.

Qualquer utilizador com a função Manager na conta Partner Center terá a opção de adicionar e remover os inquilinos AD da conta.

## <a name="add-an-existing-tenant"></a>Adicione um inquilino existente

Para associar outro inquilino AZure AD à sua conta Partner Center:

1. No centro de prioridades do Partner Center, selecione  >  **Definições de Conta**.
1. No **perfil da Organização,** selecione **Inquilinos.** As atuais associações de inquilinos são mostradas.
1. No **separador Developer,** **selecione Associate**.
1. Insira as suas credenciais AD AZure para o inquilino que pretende associar.
1. Reveja a organização e o nome de domínio para o seu inquilino AZure AD. Para completar a associação, **selecione Confirmar**.

Se a associação tiver sucesso, estará pronto para adicionar e gerir utilizadores de conta na secção Utilizadores no Partner Center. Para aprender a adicionar utilizadores, consulte [Gerir os utilizadores.](add-manage-users.md)

## <a name="create-a-new-tenant"></a>Criar um novo inquilino

Para criar um novo inquilino AZure AD com a sua conta Partner Center:

1. No centro de prioridades do Partner Center, selecione  >  **Definições de Conta**.
1. No **perfil da Organização,** selecione **Inquilinos.** As atuais associações de inquilinos são mostradas.
1. No separador Programador, **selecione Criar**.
1. Insira as informações do diretório para o seu novo AD Azure:
    - **Nome de domínio**: O nome único que usaremos para o seu domínio AZure AD, juntamente com ".onmicrosoft.com". Por exemplo, se inserisse "exemplo", o seu domínio AD Azure seria "example.onmicrosoft.com".
    - **E-mail de contato:** Um endereço de e-mail onde podemos contactá-lo sobre a sua conta, se necessário.
    - **Informações globais da conta de utilizador do administrador**: O primeiro nome, apelido, nome de utilizador e senha que pretende utilizar para a nova conta de administrador global.
1. Selecione Criar para confirmar o novo domínio e informações de conta.
1. Inicie sôr entrada com o seu novo nome de utilizador e palavra-passe do administrador global Azure AD para começar a [adicionar e gerir utilizadores.](add-manage-users.md)

Para obter mais informações sobre a criação de novos inquilinos dentro do seu portal Azure, em vez do portal Partner Center, consulte o artigo [Criar um novo inquilino no Azure Ative Directory.](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

## <a name="remove-a-tenant"></a>Remova um inquilino

Para remover um inquilino da sua conta Partner Center, encontre o seu nome na página **dos Inquilinos** (nas **definições de Conta),** em seguida, selecione **Remover**. Será solicitado para confirmar que quer retirar o inquilino. Depois de o fazer, nenhum utilizadores nesse inquilino poderá entrar na conta do Centro de Parceiros, e quaisquer permissões que tenha configurado para esses utilizadores serão removidas.

> [!TIP]
> Não pode remover um inquilino se está atualmente assinado no Partner Center usando uma conta no mesmo inquilino. Para remover um inquilino, você deve assinar no Partner Center como **gerente** para outro inquilino que esteja associado à conta. Se houver apenas um inquilino associado à conta, esse inquilino só pode ser removido depois de iniciar sessão com a conta microsoft que abriu a conta.
