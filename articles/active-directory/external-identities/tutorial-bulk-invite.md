---
title: Tutorial para utilizadores de colaboração B2B convidativos a granel - Azure AD
description: Neste tutorial, saberá como utilizar o PowerShell e um ficheiro CSV para enviar convites em massa a utilizadores externos da colaboração B2B do Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01deae46c442fc95c6aead0f11de929f47163c3c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586562"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>Tutorial: Convidar utilizadores da colaboração B2B do Azure AD em massa

Se utilizar a colaboração B2B do Azure Active Directory (Azure AD) para trabalhar com parceiros externos, pode convidar múltiplos utilizadores convidados para a sua organização ao mesmo tempo. Neste tutorial, aprende-se a usar o portal Azure para enviar convites a granel para utilizadores externos. Deve fazer o seguinte:

> [!div class="checklist"]
> * Use **A granel convide os utilizadores** a preparar um ficheiro de valor separado de vírgula (.csv) com as informações do utilizador e preferências de convite
> * Faça o upload do ficheiro .csv para a Azure AD
> * Verificar se os utilizadores foram adicionados ao diretório

Se não tiver o Azure Ative Directory, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="understand-the-csv-template"></a>Compreenda o modelo CSV

Faça o download e preencha o modelo de CSV de upload a granel para ajudá-lo a convidar com sucesso utilizadores convidados Azure AD a granel. O modelo CSV que descarrega pode parecer este exemplo:

![Folha de cálculo para upload e chamadas explicando a finalidade e valores de cada linha e coluna](media/tutorial-bulk-invite/understand-template.png)

### <a name="csv-template-structure"></a>Estrutura de modelo de CSV

As linhas de um modelo de CSV descarregado são as seguintes:

- **Número da versão**: A primeira linha que contém o número da versão deve ser incluída no upload CSV.
- **Posições de coluna :** O formato das posições da coluna é &lt; *o nome do item* &gt; [Nome de Propriedade] &lt; *Obrigatório ou em branco* &gt; . Por exemplo, `Email address to invite [inviteeEmail] Required`. Algumas versões mais antigas do modelo podem ter ligeiras variações.
- **Linha exemplos**: Incluímos no modelo uma linha de exemplos de valores para cada coluna. Deve remover os exemplos e substituí-lo pelas suas próprias entradas.

### <a name="additional-guidance"></a>Orientações adicionais

- As duas primeiras linhas do modelo de upload não devem ser removidas ou modificadas, ou o upload não pode ser processado.
- As colunas necessárias são listadas primeiro.
- Não recomendamos a adição de novas colunas ao modelo. Quaisquer colunas adicionais que adicionar são ignoradas e não processadas.
- Recomendamos que descarregue a versão mais recente do modelo CSV com a maior frequência possível.

## <a name="prerequisites"></a>Pré-requisitos

Precisa de duas ou mais contas de e-mail de teste para as quais possa enviar os convites. As contas têm de ser externas à sua organização. Pode utilizar qualquer tipo de conta, incluindo contas sociais como os endereços do gmail.com ou outlook.com.

## <a name="invite-guest-users-in-bulk"></a>Convidar utilizadores convidados a granel

1. Inscreva-se no portal Azure com uma conta que é um administrador global na organização.
2. No painel de navegação, selecione **Azure Ative Directory**.
3. Em **Gestão**, **selecione Todos os Utilizadores**.
4. **Selecione a granel**  >  **convite.**

    ![Botão de convite a granel](media/tutorial-bulk-invite/bulk-invite-button.png)

4. Na página de convidar a **granel,** selecione **Descarregue** para obter um modelo de .csv válido com propriedades de convite.

     ![Descarregue o ficheiro CSV](media/tutorial-bulk-invite/download-button.png)

1. Abra o modelo de .csv e adicione uma linha para cada utilizador convidado. Os valores exigidos são:

   * **Endereço de e-mail para convidar** - o utilizador que receberá um convite

   * **Url de redirecionamento** - o URL para o qual o utilizador convidado é reencaminhado após aceitar o convite. Se pretender encaminhar o utilizador para a página My Apps, tem de alterar este valor para https://myapps.microsoft.com ou https://myapplications.microsoft.com .

    ![Exemplo de um ficheiro CSV com utilizadores convidados introduzido](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > Não utilize vírgulas na **mensagem de convite personalizada** porque evitarão que a mensagem seja analisada com sucesso.

6. Guarde o ficheiro.
7. Na página de convidar a **Granel,** no **upload do seu ficheiro CSV,** navegue para o ficheiro. Quando selecionar o ficheiro, inicia-se a validação do ficheiro .csv. 
8. Quando o conteúdo do ficheiro for validado, verá **o Ficheiro carregado com sucesso.** Se houver erros, tem de os corrigir antes de poder submeter o trabalho.
9. Quando o seu ficheiro passar a validação, **selecione Enviar** por terminação da operação a granel Azure que adiciona os convites. 
10. Para ver o estado do trabalho, **selecione Clique aqui para ver o estado de cada operação**. Ou, pode selecionar **os resultados da operação** a granel na secção **Atividade.** Para mais detalhes sobre cada item de linha dentro da operação a granel, selecione os valores sob as colunas **# Sucesso**, **# Falha,** ou **Pedidos Totais.** Se ocorrerem falhas, as razões do fracasso serão listadas.

    ![Exemplo dos resultados da operação a granel](media/tutorial-bulk-invite/bulk-operation-results.png)

11. Quando o trabalho terminar, verá uma notificação de que a operação a granel foi bem sucedida.

## <a name="verify-guest-users-in-the-directory"></a>Verifique os utilizadores convidados no diretório

Verifique se os utilizadores convidados que adicionou existem no diretório, quer no portal Azure, quer utilizando o PowerShell.

### <a name="view-guest-users-in-the-azure-portal"></a>Ver utilizadores convidados no portal Azure

1. Inscreva-se no portal Azure com uma conta que é administrador do Utilizador na organização.
2. No painel de navegação, selecione **Azure Ative Directory**.
3. Em **Gerir**, selecione **Utilizadores**.
4. Em **'Show',** selecione **apenas os utilizadores convidados** e verifique se os utilizadores adicionados estão listados.

### <a name="view-guest-users-with-powershell"></a>Ver utilizadores convidados com PowerShell

Execute o seguinte comando:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

Deverá ver os utilizadores que convidou listados, com um nome principal do utilizador (UPN) no formato *emailaddress*#EXT# \@ *domínio*. Por exemplo, *lstokes_fabrikam.com#EXT# \@ contoso.onmicrosoft.com*, onde contoso.onmicrosoft.com é a organização a partir da qual enviou os convites.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, pode eliminar as contas de utilizador do teste no diretório do portal Azure na página do Utilizador, selecionando a caixa de verificação ao lado do utilizador convidado e, em seguida, selecionando **Delete**. 

Ou pode executar o seguinte comando PowerShell para eliminar uma conta de utilizador:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Por exemplo: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, enviou convites em massa para utilizadores convidados fora da sua organização. Em seguida, saiba como o processo de resgate do convite funciona.

> [!div class="nextstepaction"]
> [Saiba mais sobre o processo de resgate do convite de colaboração B2B do Azure AD](redemption-experience.md)
