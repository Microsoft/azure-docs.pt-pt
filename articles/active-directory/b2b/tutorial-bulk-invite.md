---
title: Tutorial para convidar em massa usuários de colaboração B2B – Azure Active Directory | Microsoft Docs
description: Neste tutorial, saberá como utilizar o PowerShell e um ficheiro CSV para enviar convites em massa a utilizadores externos da colaboração B2B do Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 9/19/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec1a6ea8f363f2ddd4a9568700d5bff3330443c0
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71128730"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users-preview"></a>Tutorial: Convidar em massa usuários de colaboração B2B do Azure AD (versão prévia)

|     |
| --- |
| Este artigo descreve um recurso de visualização pública do Azure Active Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |


Se utilizar a colaboração B2B do Azure Active Directory (Azure AD) para trabalhar com parceiros externos, pode convidar múltiplos utilizadores convidados para a sua organização ao mesmo tempo. Neste tutorial, você aprenderá a usar o portal do Azure para enviar convites em massa a usuários externos. Deve fazer o seguinte:

> [!div class="checklist"]
> * Usar **usuários de convite em massa (visualização)** para preparar um arquivo de valores separados por vírgulas (. csv) com as informações do usuário e as preferências de convite
> * Carregar o arquivo. csv no Azure AD
> * Verificar se os utilizadores foram adicionados ao diretório

Se você não tiver Azure Active Directory, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

Precisa de duas ou mais contas de e-mail de teste para as quais possa enviar os convites. As contas têm de ser externas à sua organização. Pode utilizar qualquer tipo de conta, incluindo contas sociais como os endereços do gmail.com ou outlook.com.

## <a name="invite-guest-users-in-bulk"></a>Convidar usuários convidados em massa

1. Entre no portal do Azure com uma conta que seja um administrador de usuário na organização.
2. No painel de navegação, selecione **Azure Active Directory**.
3. Em **gerenciar**, selecione **usuários** > **convite em massa**.
4. Na página **usuários do convite em massa (versão prévia)** , selecione **baixar** para obter um arquivo. csv válido com propriedades do convite.

    ![Botão de download de convite em massa](media/tutorial-bulk-invite/bulk-invite-button.png)

5. Abra o arquivo. csv e adicione uma linha para cada usuário convidado. Os valores necessários são:

   * **Endereço de email para convidar** -o usuário que receberá um convite

   * **URL de redirecionamento** -a URL para a qual o usuário convidado é encaminhado após aceitar o convite

    ![Exemplo de um arquivo CSV com usuários convidados inseridos](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > Não use vírgulas na **mensagem de convite personalizada** porque elas impedirão que a mensagem seja analisada com êxito.

6. Guarde o ficheiro.
7. Na página **usuários do convite em massa (versão prévia)** , em **carregar o arquivo CSV**, navegue até o arquivo. Quando você seleciona o arquivo, a validação do arquivo. csv é iniciada. 
8. Quando o conteúdo do arquivo for validado, você verá o **arquivo carregado com êxito**. Se houver erros, você deverá corrigi-los antes de poder enviar o trabalho.
9. Quando o arquivo passar na validação, selecione **Enviar** para iniciar a operação em massa do Azure que adiciona os convites. 
10. Para exibir o status do trabalho, selecione **clique aqui para exibir o status de cada operação**. Ou, você pode selecionar **resultados de operação em massa (versão prévia)** na seção **atividade** . Para obter detalhes sobre cada item de linha na operação em massa, selecione os valores nas colunas **# êxito**, **# falha**ou **total de solicitações** . Se ocorrerem falhas, os motivos da falha serão listados.

    ![Exemplo de resultados de operação em massa](media/tutorial-bulk-invite/bulk-operation-results.png)

11. Quando o trabalho for concluído, você verá uma notificação de que a operação em massa foi bem-sucedida.

## <a name="verify-guest-users-in-the-directory"></a>Verificar usuários convidados no diretório

Verifique se os usuários convidados que você adicionou existem no diretório no portal do Azure ou usando o PowerShell.

### <a name="view-guest-users-in-the-azure-portal"></a>Exibir usuários convidados no portal do Azure

1. Entre no portal do Azure com uma conta que seja um administrador de usuário na organização.
2. No painel de navegação, selecione **Azure Active Directory**.
3. Em **Gerir**, selecione **Utilizadores**.
4. Em **Mostrar**, selecione **somente usuários convidados** e verifique se os usuários adicionados estão listados.

### <a name="view-guest-users-with-powershell"></a>Exibir usuários convidados com o PowerShell

Execute o seguinte comando:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

Você deve ver os usuários que convidou listados, com um nome UPN no formato *EmailAddress*#EXT #\@*Domain*. Por exemplo, *lstokes_fabrikam. com # ext #\@contoso.onmicrosoft.com*, em que contoso.onmicrosoft.com é a organização da qual você enviou os convites.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você poderá excluir as contas de usuário de teste no diretório no portal do Azure na página usuários, marcando a caixa de seleção ao lado do usuário convidado e, em seguida, selecionando **excluir**. 

Ou você pode executar o seguinte comando do PowerShell para excluir uma conta de usuário:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Por exemplo: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, enviou convites em massa para utilizadores convidados fora da sua organização. Em seguida, saiba como o processo de resgate do convite funciona.

> [!div class="nextstepaction"]
> [Saiba mais sobre o processo de resgate do convite de colaboração B2B do Azure AD](redemption-experience.md)
