---
title: Resolver problemas de acesso ao Azure EA Portal
description: Este artigo descreve alguns problemas comuns que podem ocorrer com um Contrato Enterprise (EA) do Azure no Azure EA Portal.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: troubleshooting
ms.service: billing
manager: boalcsva
ms.openlocfilehash: a2214ef0dd3b7c4f706ebbb529b6c44e956f9242
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900884"
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

A primeira conta escolar ou profissional adicionada à inscrição determina o domínio _predefinido_ ou _mestre_. Para adicionar uma conta escolar ou profissional com outro inquilino, terá de alterar o nível de autenticação na inscrição para autenticação entre inquilinos.

Para atualizar o Nível de Autenticação:

1. Inicie sessão no Azure EA Portal como Administrador do Enterprise.
2. Clique em **Gerir** no painel de navegação esquerdo.
3. Clique no separador **Inscrição**.
4. Em **Detalhes da Inscrição**, selecione **Nível de Autenticação**.
5. Clique no símbolo do lápis.
6. Clique em **Guardar**.

![Exemplo que mostra os níveis de autenticação ](./media/billing-ea-portal-troubleshoot/create-ea-authentication-level-types.png)

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
- Caso receba um erro de _Utilizador Inválido_ ao utilizar a conta Microsoft, poderá ser pelo facto de ter várias contas Microsoft. A conta que está a utilizar para tentar iniciar sessão não é o endereço de e-mail principal.
Ou, caso receba um erro de _Utilizador Inválido_, talvez seja porque foi utilizado o tipo de conta errado quando o utilizador foi adicionado à inscrição. Por exemplo, uma conta escolar ou profissional em vez de uma conta Microsoft. Neste exemplo, deverá pedir a outro administrador EA para adicionar a conta correta ou terá de contactar o [suporte](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c).

## <a name="no-activation-email-received"></a>Nenhum e-mail de ativação recebido

É enviado um e-mail de ativação do Azure EA Portal a partir do *waep@microsoft.com* . Caso não tenha recebido um e-mail de ativação, verifique se o e-mail está no spam ou na pasta de lixo. O e-mail é enviado com o assunto _Convite para Ver/Gerir o serviço Microsoft Azure_. É enviado a todos os administradores EA recentemente adicionados.

Se tiver a certeza de que foi configurado como administrador EA, não terá de esperar pelo e-mail de ativação para iniciar sessão no Azure EA Portal. Em vez disso, poderá aceder a https://ea.azure.com e iniciar sessão com o seu endereço de e-mail (conta profissional, escolar ou Microsoft) e palavra-passe.

Se precisar de verificar o alias principal, aceda a [https://account.live.com](https://account.live.com). Em seguida, clique em **As suas Informações** e em **Gerir a forma como inicia sessão na Microsoft**. Siga as instruções para verificar um endereço de e-mail alternativo e obter um código para aceder às informações confidenciais. Introduza o código de segurança. Selecione **Configurar mais tarde**, caso não queira configurar a autenticação de dois fatores.

Verá a página **Gerir a forma como inicia sessão na Microsoft**, onde poderá visualizar os seus aliases de conta. Verifique se o alias principal é aquele que está a utilizar para iniciar sessão no Azure EA Portal. Se não for, poderá torná-lo no alias principal. Em alternativa, poderá utilizar o alias principal do Azure EA Portal.

## <a name="next-steps"></a>Passos seguintes

- Os administradores do Azure EA Portal devem ler o artigo [Azure EA portal administration](billing-ea-portal-administration.md) (Administração do Azure EA Portal) para saber mais sobre as tarefas administrativas comuns.
