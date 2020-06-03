---
title: Resolver problemas de acesso ao Azure EA Portal
description: Este artigo descreve alguns problemas comuns que podem ocorrer com um Contrato Enterprise (EA) do Azure no Azure EA Portal.
author: bandersmsft
ms.author: banders
ms.date: 06/01/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: ed40c6d55f380d979408003a2bd370083cf0e864
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/02/2020
ms.locfileid: "84299150"
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

## <a name="azure-ea-activation-faq"></a>FAQ sobre Ativação do Azure EA

Esta seção do artigo descreve soluções para problemas comuns relacionados com a ativação do Azure EA.

### <a name="i-didnt-receive-an-activation-email"></a>Não recebi um e-mail de ativação

É enviado um e-mail de ativação do Azure EA Portal a partir do *waep@microsoft.com* . Caso não tenha recebido um e-mail de ativação, verifique se o e-mail está no spam ou na pasta de lixo. O e-mail é enviado com o assunto _Convite para Ver/Gerir o serviço Microsoft Azure_. É enviado a todos os administradores EA recentemente adicionados.

Se tiver a certeza de que foi configurado como administrador EA, não terá de esperar pelo e-mail de ativação para iniciar sessão no Azure EA Portal. Em vez disso, poderá aceder a https://ea.azure.com e iniciar sessão com o seu endereço de e-mail (conta profissional, escolar ou Microsoft) e palavra-passe.

### <a name="i-would-like-to-add-a-new-ea-administrator-to-my-enrollment"></a>Gostaria de adicionar um novo administrador do EA à minha inscrição

Um novo administrador do Enterprise pode ser adicionado pelos administradores do Enterprise existentes. Se for o administrador do EA, inicie sessão no EA Portal > clique em **Gerir** > clique em **+ Adicionar Administrador** no canto superior direito para adicionar um novo administrador do EA. Certifique-se de que tem o respetivo endereço de e-mail e um método de início de sessão preferencial, como através de autenticação profissional/escolar ou Microsoft Live ID, para que os utilizadores sejam adicionados.

Se não for o administrador do EA, entre em contacto com os administradores do EA da sua empresa para solicitar que o adicionem à inscrição. Depois de o adicionarem à inscrição, receberá um e-mail de ativação.

No entanto, se os administradores do EA não conseguirem ajudá-lo, podemos adicioná-lo em nome deles se nos facultar:
- o número de inscrição.
- o endereço de e-mail a adicionar e o tipo de autenticação (profissional/escolar/MS).
- uma aprovação de e-mail do administrador do EA.

Depois de ter todas as informações necessárias, envie um pedido em [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

### <a name="i-would-like-to-update-the-first-ea-admin-on-the-enrollment"></a>Gostaria de atualizar o primeiro administrador do EA na inscrição

O primeiro administrador do EA pode ser atualizado no Volume Licensing Service Center ao atualizar o contacto para notificação e o administrador online no portal. Demorará cerca de 24 horas até o EA Portal atualizar. Depois de atualizado, o novo administrador do EA receberá um e-mail de ativação.

Se não tiver acesso ao portal VLSC ou se o seu administrador do EA inicial já não puder gerir a inscrição e não tiver acesso ao EA Portal, submeta um pedido em [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) para pedir uma atualização e faculte as seguintes informações:
- Número de inscrição
- Endereço de e-mail a adicionar e tipo de autenticação (profissional/escolar/MS)
- Motivo para alterar o administrador do EA inicial
- Aprovação de e-mail do administrador do EA inicial

### <a name="my-current-ea-admin-is-no-longer-with-the-company"></a>O administrador do meu EA atual já não está na empresa

Uma inscrição de EA pode ter vários administradores do EA. Pode entrar em contacto com outro administrador do EA para que adicione um novo administrador do EA/proprietários de conta/administrador de departamento. No entanto, se não tiver a certeza de quem é o administrador do EA na sua empresa ou se não existir outro administrador do EA disponível na inscrição, entre em contacto connosco com as seguintes informações:
- Número de inscrição
- Endereço de e-mail a adicionar e tipo de autenticação (profissional/escolar/MS)
- Facultar a informação de que o atual administrador do EA já não está na empresa

Note que se existirem outros administradores do EA na inscrição, entraremos em contacto com os administradores do EA para solicitar a aprovação das alterações administrativas na inscrição.

### <a name="my-enrollment-is-showing-in-pending-status-how-do-i-activate-my-enrollment"></a>A minha inscrição é apresentada no estado pendente. Como posso ativar a minha inscrição?

As inscrições estarão no estado pendente se o administrador do EA inicial não se tiver registado antes na inscrição. Se for o administrador do EA, inicie sessão no Azure EA Portal. Na página de destino com todos os seus números de inscrição, poderá não ver a sua inscrição pendente. Desmarque a caixa "ativo" no canto superior direito do EA Portal. Esta ação apresentará a inscrição pendente. Clique na inscrição para aceder às informações e quando chegar à página Gerir da inscrição, o estado será atualizado de Pendente para Ativo.

### <a name="why-is-my-account-stuck-in-pending-status"></a>Por que é que a minha conta está no estado pendente?

Quando são adicionados novos Proprietários de Conta (AO) à inscrição pela primeira vez, serão sempre apresentados com o estado "pendente". Quando receber o e-mail de boas-vindas de ativação, o AO pode iniciar sessão para ativar a respetiva conta. Ao iniciar sessão, o estado da conta será atualizado de "pendente" para "ativo".

### <a name="i-received-an-error-when-signing-in-to-azure-ea-portal"></a>Recebi um erro ao iniciar sessão no Azure EA Portal

Existem alguns motivos possíveis para uma mensagem de erro no Azure EA Portal durante o início de sessão. Siga estes passos de resolução de problemas:

 1. Certifique-se de que está a utilizar o URL do EA Portal correto em [https://ea.azure.com](https://ea.azure.com).
 1. Determine se o seu acesso ao Azure EA Portal está adicionado como uma conta escolar ou profissional, ou como um Microsoft Live ID. Se estiver a utilizar a sua conta profissional, introduza o e-mail e a palavra-passe profissionais. Se estiver a utilizar o Microsoft Live ID, introduza o e-mail do Live ID e a palavra-passe do Microsoft Live ID. Caso se tenha esquecido da palavra-passe do Microsoft Live ID, redefina-a em [https://account.live.com/password/reset](https://account.live.com/password/reset).
 1. É recomendado que utilize um browser privado para iniciar sessão, para que não sejam mantidos cookies ou a cache de sessões anteriores/existentes. Limpe a cache e utilize o modo de navegação privado/incógnito para abrir [https://ea.azure.com](https://ea.azure.com).
 1. Se receber o erro Utilizador Inválido quando utilizar uma conta Microsoft, pode dever-se ao facto de ter várias contas Microsoft e aquela com que está a tentar iniciar sessão não é o alias principal. Para verificar o alias principal, aceda a account.live.com:
    - Aceda a "As suas informações" > "Gerir o e-mail de início de sessão ou o número de telefone".
    - Siga as instruções no ecrã para verificar o endereço de e-mail alternativo e obter um código para aceder às informações confidenciais.
    - Introduza o código de segurança.
    - Se preferir configurar a autenticação de dois passos mais tarde, selecione "Configurar mais tarde".
    - Irá para a página "Gerir os aliases da conta", na qual pode ver os aliases da conta que tem. Verifique se o alias principal é aquele que está a utilizar para iniciar sessão no Azure EA Portal. Se não for, poderá torná-lo no alias principal ou, em alternativa, utilizar o alias principal no EA Portal.

Se os passos de resolução de problemas indicados acima falharem, submeta um pedido em [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) com informações como:
- Os browsers e a versão utilizados.
- Captura de ecrã da mensagem de erro.
- URL da página que apresenta o erro.  
- Data, hora e fuso horário de quando ocorre o erro.
- Além disso, irá ajudá-lo a obter um ficheiro de registo. Eis os passos para capturar um rastreio de rede com as informações abaixo:
  1. Abra o Internet Explorer.
  1. Prima F12, que abrirá uma caixa na parte inferior do IE.
  1. Selecione o separador **Rede**.
  1. Clique em **Iniciar captura**.
  1. Execute a ação que está a causar o erro.
  1. Quando o erro ocorrer, clique em **Parar captura**.
  1. Guarde o ficheiro e inclua as informações no pedido de suporte.
  1. Certifique-se de que fornece o seu número de inscrição e o endereço de e-mail no pedido de suporte.

### <a name="what-is-the-difference-between-a-workschool-account-and-microsoft-account"></a>Qual é a diferença entre uma conta escolar/profissional e uma conta Microsoft?

**Conta Microsoft:** As contas que foram associadas ao Live ID em [https://signup.live.com](https://signup.live.com).

**Conta escolar/profissional:** Disponível apenas para empresas que tenham configurado o Active Directory com Federação na Cloud e que tenham todas as contas num único inquilino. Os utilizadores podem ser adicionados com o tipo de autorização escolar/profissional se o Active Directory interno da empresa estiver federado na cloud.

  A partir de setembro de 2016, a Microsoft deixou de permitir que endereços de e-mail escolares ou profissionais fossem registados como contas Microsoft. Para obter mais detalhes, consulte os seguintes materiais: [https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/](https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/).

  Se sua organização não estiver federada na cloud, não poderá utilizar o seu endereço de e-mail escolar ou profissional. Em vez disso, deverá registar ou criar um novo endereço de e-mail e, em seguida, registá-lo como uma conta Microsoft.

### <a name="i-forgot-my-password-to-azure-ea-portal"></a>Esqueci-me da minha palavra-passe do Azure EA Portal

Caso se tenha esquecido da palavra-passe do Microsoft Live ID, redefina-a em [https://account.live.com/password/reset](https://account.live.com/password/reset).

Caso se tenha esquecido da sua palavra-passe profissional, contacte o administrador de TI da sua empresa.

### <a name="i-have-a-valid-work-or-school-account-but-i-cant-add-it-to-the-ea-portal"></a>Tenho uma conta escolar ou profissional válida, mas não consigo adicioná-la ao EA Portal

Se tiver uma conta escolar ou profissional num inquilino diferente, altere o nível de autorização na página de detalhes de inscrição para "Conta Escolar ou Profissional em Vários Inquilinos" e poderá adicionar a conta.

## <a name="next-steps"></a>Passos seguintes

- Os administradores do Azure EA Portal devem ler o artigo [Azure EA portal administration](ea-portal-administration.md) (Administração do Azure EA Portal) para saber mais sobre as tarefas administrativas comuns.
