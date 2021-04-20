---
title: Resolver problemas de acesso ao Azure EA Portal
description: Este artigo descreve alguns problemas comuns que podem ocorrer com um Contrato Enterprise (EA) do Azure no Azure EA Portal.
author: bandersmsft
ms.author: banders
ms.date: 03/26/2021
ms.topic: troubleshooting
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 852fb85607318772870a8b6826c934997b84ff6d
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726471"
---
# <a name="troubleshoot-azure-ea-portal-access"></a>Resolver problemas de acesso ao Azure EA Portal

Este artigo descreve alguns problemas comuns que podem ocorrer com um Contrato Enterprise (EA) do Azure. O Azure EA Portal é utilizado para gerir os utilizadores e os custos do Contrato Enterprise. Poderá deparar-se com estes problemas ao configurar ou atualizar o acesso ao Azure EA Portal.

## <a name="issues-adding-an-admin-to-an-enrollment"></a>Problemas ao adicionar um administrador a uma inscrição

Existem diferentes tipos de níveis de autenticação para as inscrições Enterprise. Quando os níveis de autenticação são aplicados incorretamente, poderá ter problemas ao tentar iniciar sessão no Azure EA Portal.

Deverá utilizar o Azure EA Portal para conceder acesso aos utilizadores com diferentes níveis de autenticação. Um administrador do Enterprise pode atualizar o nível de autenticação de forma a cumprir os requisitos de segurança da sua organização.

### <a name="authentication-level-types"></a>Tipos de nível de autenticação

- Apenas Contas Microsoft – para organizações que querem utilizar, criar e gerir utilizadores através das contas Microsoft.
- Conta Escolar ou Profissional – para organizações que tenham configurado o Active Directory Domain Services com Federação na Cloud e que tenham todas as contas num único inquilino.
- Conta Escolar ou Profissional Entre Inquilinos – para organizações que tenham configurado o Active Directory Domain Services com Federação na Cloud e que terão contas em vários inquilinos.
- Conta Mista – permite que adicione utilizadores com a Conta Microsoft e/ou com uma Conta Profissional ou Escolar.

A primeira conta escolar ou profissional adicionada à inscrição determina o domínio _predefinido_. Para adicionar uma conta escolar ou profissional com outro inquilino, terá de alterar o nível de autenticação na inscrição para autenticação entre inquilinos.

Para atualizar o Nível de Autenticação:

1. Inicie sessão no Azure EA Portal como Administrador do Enterprise.
2. Clique em **Gerir** no painel de navegação esquerdo.
3. Clique no separador **Inscrição**.
4. Em **Detalhes da Inscrição**, selecione **Nível de Autenticação**.
5. Clique no símbolo do lápis.
6. Clique em **Guardar**.

![Exemplo que mostra os níveis de autenticação ](./media/ea-portal-troubleshoot/create-ea-authentication-level-types.png)

As contas Microsoft têm de ter um ID associado criado em [https://signup.live.com](https://signup.live.com/).

As contas escolares ou profissionais estão disponíveis para organizações que tenham configurado o Active Directory Domain Services com federação e que tenham todas as contas num único inquilino. Poderão ser adicionados utilizadores com autenticação de utilizador federado escolar ou profissional se o Active Directory Domain Services interno da empresa estiver federado.

Se a sua organização não utilizar a federação do Active Directory Domain Services, não poderá utilizar o endereço de e-mail escolar ou profissional. Em vez disso, deverá registar ou criar um novo endereço de e-mail e, em seguida, registá-lo o como uma conta Microsoft.

## <a name="unable-to-access-the-azure-ea-portal"></a>Não é possível aceder ao Azure EA Portal

Caso receba uma mensagem de erro ao tentar iniciar sessão no Azure EA Portal, siga os passos seguintes de resolução de problemas:

- Verifique se está a utilizar o URL correto do Azure EA Portal, que é https://ea.azure.com.
- Determine se o seu acesso ao Azure EA Portal foi adicionado como uma conta escolar ou profissional ou como uma conta Microsoft.
  - Se estiver a utilizar a conta profissional, introduza o e-mail e a palavra-passe profissionais. A palavra-passe profissional é fornecida pela sua organização. Pode contactar o departamento de TI para saber como repor a palavra-passe, caso tenha problemas com ela.
  - Se estiver a utilizar uma conta Microsoft, introduza o endereço de e-mail e a palavra-passe da conta Microsoft. Caso se tenha esquecido da palavra-passe da conta Microsoft, poderá repô-la em [https://account.live.com/password/reset](https://account.live.com/password/reset).
- Utilize uma sessão privada ou anónima do browser para iniciar sessão, para que não sejam mantidos quaisquer cookies ou informações em cache de sessões anteriores ou existentes. Limpe o cache do browser e utilize uma janela em modo privado ou anónimo para abrir https://ea.azure.com.
- Caso seja apresentado um erro de _Utilizador Inválido_ ao utilizar a conta Microsoft, poderá ser pelo facto de ter várias contas Microsoft. A conta que está a utilizar para tentar iniciar sessão não é o endereço de e-mail principal.
Ou, caso receba um erro de _Utilizador Inválido_, talvez seja porque foi utilizado o tipo de conta errado quando o utilizador foi adicionado à inscrição. Por exemplo, uma conta escolar ou profissional em vez de uma conta Microsoft. Neste exemplo, deverá pedir a outro administrador EA para adicionar a conta correta ou terá de contactar o [suporte](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c).
  - Se precisar de verificar o alias principal, aceda a [https://account.live.com](https://account.live.com). Em seguida, clique em **As suas informações** e em **Gerir como inicia sessão na Microsoft**. Siga as instruções para verificar um endereço de e-mail alternativo e obter um código para aceder às informações confidenciais. Introduza o código de segurança. Selecione **Configurar mais tarde**, caso não queira configurar a autenticação de dois fatores.
  - Verá a página **Gerir como inicia sessão na Microsoft**, onde poderá visualizar os seus aliases de conta. Verifique se o alias principal é aquele que está a utilizar para iniciar sessão no Azure EA Portal. Se não for, poderá torná-lo no alias principal. Em alternativa, poderá utilizar o alias principal do Azure EA Portal.

## <a name="next-steps"></a>Passos seguintes

- Os administradores do Azure EA Portal devem ler o artigo [Azure EA portal administration](ea-portal-administration.md) (Administração do Azure EA Portal) para saber mais sobre as tarefas administrativas comuns.
- Leia a Gestão de [Custos + Faturação FAQ](../cost-management-billing-faq.yml) para perguntas e respostas sobre questões comuns para a Ativação Azure EA.
