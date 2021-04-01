---
title: Resolver erro de login para ferramentas de azure Dev para ensinar
description: Isto descreve as ações que um aluno deve tomar se receber uma mensagem de erro ao iniciar sessão no Azure Dev Tools for Teaching.
author: rymend
ms.author: rymend
ms.topic: troubleshooting
ms.service: azure-education
ms.subservice: education-hub
ms.date: 06/30/2020
ms.openlocfilehash: 276ab2cc1d79235c00ce1bb50ff9b648bb1de6ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87097681"
---
# <a name="troubleshooting-student-login-issues"></a>Problemas de resolução de problemas de login de estudante
Aceder a Azure Dev Tools for Teaching requer que um utilizador tenha uma Conta Microsoft (MSA). Os alunos serão automaticamente direcionados para a criação de um MSA se a sua conta não for já um MSA ou ligado a um MSA. Se o seu domínio estiver associado ao Ative Directory, todas as contas sob esse domínio já são consideradas MSA.

Se um aluno tentar fazer login e receber a seguinte mensagem de erro, utilize uma das soluções descritas abaixo.

:::image type="content" source="media/troubleshoot-login/error-login-message.png" alt-text="Assine a mensagem de erro." border="false":::

Existem duas soluções: criar uma nova conta Microsoft ou utilizar uma conta Microsoft existente.

## <a name="create-a-new-microsoft-account"></a>Criar uma nova conta microsoft
### <a name="use-a-university-email-address"></a>Use um endereço de e-mail da universidade
Se estiver a iniciar sessão com um endereço de e-mail universitário (por `John.Smith@university.edu` exemplo, ), terá de criar uma conta Microsoft com esse endereço de e-mail. Criar uma conta é gratuito e leva apenas alguns minutos. Ter uma conta Microsoft permitir-lhe-á iniciar automaticamente o seu login em todos os seus produtos Microsoft com um único nome de utilizador e senha.

### <a name="use-a-personal-email-address"></a>Use um endereço de e-mail pessoal
Se você estiver fazendo login com um endereço de e-mail pessoal (por exemplo, `John.Smith@email.com` ), mas também tem um endereço de e-mail da universidade, tente usar o endereço de e-mail da universidade. Se iniciar sessão na webstore da sua instituição com um endereço de e-mail pessoal ou não tiver um endereço de e-mail universitário, terá de criar ou ligar uma conta da Microsoft com o seu endereço de e-mail pessoal.

## <a name="use-an-existing-microsoft-account"></a>Utilize uma conta Microsoft existente
Se um estudante tiver uma conta Microsoft existente (por exemplo, Xbox), pode ligar essa conta a uma conta Azure Dev Tools.

1. Aceda a https://account.microsoft.com.
1. Faça login com as suas credenciais de conta Microsoft.
1. Selecione **a sua informação** no menu de fita superior.

1. Clique **em Gerir como se inscreve na Microsoft.** Pedir-lhe-ão que verifique a sua identidade. Será enviado um código de segurança por e-mail.

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in.png" alt-text="Faça o sinal." border="false":::

1. Introduza o código de segurança enviado por e-mail.

    :::image type="content" source="media/troubleshoot-accounts/sign-in-enter-code.png" alt-text="Introduza o código de entrada!" border="false":::

1. Clique **em Adicionar e-mail** à sua conta e insira o seu endereço de e-mail da universidade.
Da próxima vez que iniciar seduca, pode usar o seu endereço de e-mail da universidade para aceder às suas Ferramentas de Ensino Azure Dev.

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in-add-email.png" alt-text="Gerencie a forma como se inscreve na Microsoft." border="false":::

## <a name="next-steps"></a>Passos seguintes
- [FAQ](program-faq.md)

- [Opções de suporte](program-support.md)
