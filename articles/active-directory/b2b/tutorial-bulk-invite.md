---
title: Tutorial para utilizadores de colaboração B2B convidativos a granel - Azure AD
description: Neste tutorial, saberá como utilizar o PowerShell e um ficheiro CSV para enviar convites em massa a utilizadores externos da colaboração B2B do Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 04/13/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ef9172ca5d0961bb6de1949a61199ce1d6c1bff
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81603381"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>Tutorial: Convidar utilizadores da colaboração B2B do Azure AD em massa

Se utilizar a colaboração B2B do Azure Active Directory (Azure AD) para trabalhar com parceiros externos, pode convidar múltiplos utilizadores convidados para a sua organização ao mesmo tempo. Neste tutorial, aprende-se a utilizar o portal Azure para enviar convites a granel para utilizadores externos. Deve fazer o seguinte:

> [!div class="checklist"]
> * Use **a Granel convidar os utilizadores** a preparar um ficheiro de valor separado de vírem (.csv) com as informações do utilizador e as preferências de convite
> * Faça upload do ficheiro .csv para Azure AD
> * Verificar se os utilizadores foram adicionados ao diretório

Se não tiver o Azure Ative Directory, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Precisa de duas ou mais contas de e-mail de teste para as quais possa enviar os convites. As contas têm de ser externas à sua organização. Pode utilizar qualquer tipo de conta, incluindo contas sociais como os endereços do gmail.com ou outlook.com.

## <a name="invite-guest-users-in-bulk"></a>Convide os utilizadores a granel

1. Inscreva-se no portal Azure com uma conta que é administradora do Utilizador na organização.
2. No painel de navegação, selecione **Azure Ative Directory**.
3. Em **'Gerir',** selecione **Users** > **Bulk invite**.
4. Na página de convidar os utilizadores a **Granel,** selecione **Download** para obter um ficheiro .csv válido com propriedades de convite.

    ![Botão de descarregamento de convite a granel](media/tutorial-bulk-invite/bulk-invite-button.png)

5. Abra o ficheiro .csv e adicione uma linha para cada utilizador convidado. Os valores exigidos são:

   * **Endereço de e-mail para convidar** - o utilizador que receberá um convite

   * Url de **reorientação** - o URL para o qual o utilizador convidado é encaminhado após aceitar o convite

    ![Exemplo de um ficheiro CSV com utilizadores convidados inscritos](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > Não utilize vírgulas na mensagem de **convite personalizada** porque evitarão que a mensagem seja analisada com sucesso.

6. Guarde o ficheiro.
7. Na página **de convidar os utilizadores a Granel,** sob o upload do seu ficheiro **CSV,** navegue para o ficheiro. Quando selecionar o ficheiro, inicia-se a validação do ficheiro .csv. 
8. Quando o conteúdo do ficheiro for validado, verá o **Ficheiro carregado com sucesso**. Se houver erros, tem de os corrigir antes de poder submeter o trabalho.
9. Quando o seu ficheiro passar a validação, selecione **Submeter** para iniciar a operação a granel Azure que adiciona os convites. 
10. Para ver o estado de trabalho, selecione **Clique aqui para ver o estado de cada operação**. Ou, pode selecionar **os resultados** da operação Bulk na secção **Atividade.** Para mais detalhes sobre cada item de linha dentro da operação a granel, selecione os valores sob as colunas **# Sucesso**, **# Falha,** ou **Total Solicitações.** Se ocorrerem falhas, as razões da falha serão listadas.

    ![Exemplo dos resultados da operação a granel](media/tutorial-bulk-invite/bulk-operation-results.png)

11. Quando o trabalho terminar, verá uma notificação de que a operação a granel foi bem sucedida.

## <a name="verify-guest-users-in-the-directory"></a>Verifique os utilizadores convidados no diretório

Verifique se os utilizadores convidados que adicionou existem no diretório, quer no portal Azure, quer através da utilização do PowerShell.

### <a name="view-guest-users-in-the-azure-portal"></a>Ver utilizadores convidados no portal Azure

1. Inscreva-se no portal Azure com uma conta que é administradora do Utilizador na organização.
2. No painel de navegação, selecione **Azure Ative Directory**.
3. Em **Gerir**, selecione **Utilizadores**.
4. No Âmbito do **Show**, selecione **apenas utilizadores convidados** e verifique se os utilizadores adicionados estão listados.

### <a name="view-guest-users-with-powershell"></a>Ver utilizadores convidados com PowerShell

Execute o seguinte comando:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

Deve consultar os utilizadores que convidou listados, com um nome principal do utilizador\@(UPN) no endereço de *e-mail*do formato #EXT#*domínio*. Por exemplo, *\@lstokes_fabrikam.com#EXT# contoso.onmicrosoft.com,* onde contoso.onmicrosoft.com é a organização de onde enviou os convites.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode eliminar as contas de utilizador de teste no diretório do portal Azure na página Utilizadores, selecionando a caixa de verificação ao lado do utilizador convidado e selecionando depois **eliminar**. 

Ou pode executar o seguinte comando PowerShell para eliminar uma conta de utilizador:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Por exemplo: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, enviou convites em massa para utilizadores convidados fora da sua organização. Em seguida, saiba como o processo de resgate do convite funciona.

> [!div class="nextstepaction"]
> [Saiba mais sobre o processo de resgate do convite de colaboração B2B do Azure AD](redemption-experience.md)
