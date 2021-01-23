---
title: Autenticação de código de acesso único para utilizadores convidados B2B - Azure AD
description: Como utilizar a senha de acesso de email para autenticar utilizadores convidados B2B sem a necessidade de uma conta Microsoft.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 12/18/2020
ms.author: mimart
author: msmimart
manager: CelesteDG
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9a0668b3ea651d129dc076e5f2247e38f5ab7d0
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725500"
---
# <a name="email-one-time-passcode-authentication"></a>E-mail autenticação de senha única

Este artigo descreve como ativar a autenticação de código de acesso único por e-mail para utilizadores convidados B2B. A funcionalidade de código de acesso de e-mail autentica os utilizadores convidados B2B quando não podem ser autenticados através de outros meios, como o Azure AD, uma conta Microsoft (MSA) ou a federação do Google. Com a autenticação de código de acesso único, não há necessidade de criar uma conta Microsoft. Quando o utilizador convidado resgatar um convite ou aceder a um recurso partilhado, pode solicitar um código temporário, que é enviado para o seu endereço de e-mail. Depois introduzem este código para continuarem a iniciar sessão.

![E-mail código de visão geral de visão única](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
> **A partir de março de 2021,** a funcionalidade de senha de e-mail one-time será ativada para todos os inquilinos existentes e ativada por padrão para novos inquilinos. Se não quiser permitir que esta funcionalidade se ligue automaticamente, pode desativá-la. Consulte [abaixo a senha de acesso de um email desativada.](#disable-email-one-time-passcode)

> [!NOTE]
> Os utilizadores de código de acesso único devem inscrever-se através de um link que inclua o contexto do inquilino (por `https://myapps.microsoft.com/?tenantid=<tenant id>` `https://portal.azure.com/<tenant id>` exemplo, ou, no caso de um domínio verificado, `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com` ). As ligações diretas a aplicações e recursos também funcionam desde que incluam o contexto do inquilino. Os utilizadores convidados não conseguem assinar o uso de pontos finais que não têm contexto de inquilino. Por exemplo, a utilização `https://myapps.microsoft.com` de , `https://portal.azure.com` resultará num erro.

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Experiência do utilizador para utilizadores convidados de código de acesso único

Quando a funcionalidade de código de acesso de e-mail é ativada, os utilizadores recém-convidados [que satisfaçam determinadas condições](#when-does-a-guest-user-get-a-one-time-passcode) utilizarão a autenticação de código de acesso único. Os utilizadores convidados que resgataram um convite antes de o email ter sido ativado código de acesso único continuarão a utilizar o mesmo método de autenticação.

Com a autenticação de código de acesso único, o utilizador convidado pode resgatar o seu convite clicando num link direto ou utilizando o e-mail de convite. Em qualquer dos casos, uma mensagem no navegador indica que um código será enviado para o endereço de e-mail do utilizador convidado. O utilizador convidado seleciona **Enviar código:**

   ![Screenshot mostrando o botão de código Enviar](media/one-time-passcode/otp-send-code.png)

Uma senha é enviada para o endereço de e-mail do utilizador. O utilizador recupera a senha do e-mail e introduz-a na janela do navegador:

   ![Screenshot mostrando a página de código enterr](media/one-time-passcode/otp-enter-code.png)

O utilizador convidado está agora autenticado e pode ver o recurso partilhado ou continuar a iniciar sessão.

> [!NOTE]
> As senhas únicas são válidas por 30 minutos. Após 30 minutos, essa senha única específica já não é válida, e o utilizador deve solicitar uma nova. As sessões do utilizador expiram após 24 horas. Após esse período, o utilizador convidado recebe uma nova senha quando acede ao recurso. A expiração da sessão proporciona segurança adicional, especialmente quando um utilizador convidado sai da sua empresa ou já não precisa de acesso.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Quando é que um utilizador convidado obtém uma senha única?

Quando um utilizador convidado resgatar um convite ou utilizar um link para um recurso que tenha sido partilhado com eles, receberá uma senha única se:

- Eles não têm uma conta AD Azure
- Não têm uma conta microsoft
- O inquilino convidado não criou a federação do Google para @gmail.com e @googlemail.com utilizadores

No momento do convite, não há indicação de que o utilizador que está a convidar utilize a autenticação de código de acesso único. Mas quando o utilizador convidado assinar, a autenticação de código de acesso único será o método de recuo se não forem utilizados outros métodos de autenticação.

Pode ver se um utilizador convidado autentica usando códigos de acesso únicos, visualizando a propriedade **Source** nos dados do utilizador. No portal Azure, aceda aos Utilizadores **do Diretório Ativo Azure**  >  e, em seguida, selecione o utilizador para abrir a página de detalhes.

![Screenshot mostrando um utilizador de código de acesso único com valor de Origem de OTP](media/one-time-passcode/guest-user-properties.png)

> [!NOTE]
> Quando um utilizador resgatar uma senha única e mais tarde obtiver uma conta MSA, AZure AD ou outra conta federada, continuará a ser autenticada através de uma senha única. Se pretender atualizar o seu método de autenticação, pode eliminar a conta de utilizador do utilizador e convidá-la novamente.

### <a name="example"></a>Exemplo

O utilizador convidado teri@gmail.com é convidado para a Fabrikam, que não tem a federação do Google configurada. Teri não tem uma conta microsoft. Receberão uma senha única para autenticação.

## <a name="disable-email-one-time-passcode"></a>Desativar a senha de uma vez por e-mail

A partir de março de 2021, a funcionalidade de senha de e-mail one-time será ativada para todos os inquilinos existentes e ativada por padrão para novos inquilinos. Nessa altura, a Microsoft deixará de apoiar o resgate de convites criando contas de colaboração Azure AD não geridas ("virais" ou "just-in-time") E inquilinos para cenários de colaboração B2B. Estamos a ativar a funcionalidade de código de acesso de e-mail uma vez porque fornece um método de autenticação de recuo sem emenda para os utilizadores convidados. No entanto, tem a opção de desativar esta funcionalidade se optar por não a utilizar.

> [!NOTE]
>
> Se a funcionalidade de código de acesso de e-mail tiver sido ativada no seu inquilino e o desligar, qualquer utilizador convidado que tenha resgatado uma senha única não poderá iniciar sação. Pode eliminar o utilizador convidado e convidá-los novamente utilizando outro método de autenticação.

### <a name="to-disable-the-email-one-time-passcode-feature"></a>Para desativar a funcionalidade de código de senha de e-mail uma vez

1. Inscreva-se no [portal Azure](https://portal.azure.com/) como administrador global da Azure AD.

2. No painel de navegação, selecione **Azure Ative Directory**.

3. Selecione **identidades externas**  >  **Definições de colaboração externa .**

4. Sob **o Email, código de acesso único para os hóspedes,** selecione **Desativar a senha de uma vez para os hóspedes**.

    ![Envie por e-mail definições de código de acesso único](media/one-time-passcode/otp-admin-settings.png)

   > [!NOTE]
   > Se vir o seguinte alternar em vez das opções acima apresentadas, isto significa que já ativou, desativou ou optou pela pré-visualização da funcionalidade. Selecione **Não** para desativar a função.
   >
   >![Ativar a senha de e-mail uma vez optada em](media/delegate-invitations/enable-email-otp-opted-in.png)

5. Selecione **Guardar**.

## <a name="note-for-public-preview-customers"></a>Nota para clientes de pré-visualização pública

Se já optou pela pré-visualização do código de acesso por e-mail, a data de março de 2021 para ativação automática de funcionalidades não se aplica a si, pelo que os seus processos comerciais relacionados não serão afetados. Além disso, no portal Azure, sob a senha de email para propriedades **dos hóspedes,** não verá a opção de ativar automaticamente a **senha de e-mail para os hóspedes em março de 2021.** Em vez disso, verá o seguinte **sim** ou **não:**

![Ativar a senha de e-mail uma vez optada em](media/delegate-invitations/enable-email-otp-opted-in.png)

No entanto, se preferir excluir a funcionalidade e permitir que seja ativada automaticamente em março de 2021, pode reverter para as definições predefinidas utilizando o tipo de recurso de configuração do método de [autenticação de email](/graph/api/resources/emailauthenticationmethodconfiguration)da Microsoft Graph API . Depois de reverter para as definições predefinidas, as seguintes opções estarão disponíveis no **Email one-time passcode para os hóspedes:**

- **Ativar automaticamente a senha de e-mail para os hóspedes em março de 2021**. (Predefinição) Se a funcionalidade de código de acesso de e-mail não estiver já ativada para o seu inquilino, será automaticamente ativada em março de 2021. Não é necessária mais nenhuma ação se pretender que a funcionalidade seja ativada nesse momento. Se já tiver ativado ou desativado a funcionalidade, esta opção não estará disponível.

- **Ativar a senha de e-mail para os hóspedes com efeito agora**. Liga o recurso de senha de e-mail para o seu inquilino.

- **Desativar a senha de e-mail uma vez para os hóspedes.** Desliga a funcionalidade de código de acesso de uma vez por e-mail para o seu inquilino e impede que a funcionalidade se apia em março de 2021.