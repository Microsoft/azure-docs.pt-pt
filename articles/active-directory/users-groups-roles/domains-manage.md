---
title: Adicione e verifique os nomes de domínio personalizado - Azure Ative Directory / Microsoft Docs
description: Conceitos de gestão e como-tos para gerir um nome de domínio no Azure Ative Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48f924cef12db974faae8fb8ed73f01ff8c9a3f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90056256"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Gerir nomes de domínio personalizados no seu Diretório Ativo Azure

Um nome de domínio é uma parte importante do identificador para muitos recursos de diretório: faz parte de um nome de utilizador ou endereço de e-mail para um utilizador, parte do endereço para um grupo, e às vezes faz parte da aplicação ID URI para uma aplicação. Um recurso no Azure Ative Directory (Azure AD) pode incluir um nome de domínio que é propriedade do diretório que contém o recurso. Apenas um Administrador Global pode gerir domínios em Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Desconfie o nome de domínio primário para o seu diretório AD Azure

Quando o seu diretório é criado, o nome de domínio inicial, como "contoso.onmicrosoft.com", é também o nome de domínio primário. O domínio primário é o nome de domínio predefinido para um novo utilizador quando cria um novo utilizador. A definição de um nome de domínio primário simplifica o processo para um administrador criar novos utilizadores no portal. Para alterar o nome de domínio primário:

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta que é um Administrador Global para o diretório.
2. Selecione **Azure Active Directory**.
3. Selecione **Nomes de domínio personalizados**.
  
   ![Abertura da página de gestão de utilizadores](./media/domains-manage/add-custom-domain.png)
4. Selecione o nome do domínio que pretende ser o domínio primário.
5. Selecione o comando **principal Fazer.** Confirme a sua escolha quando solicitado.
  
   ![Faça um nome de domínio o principal](./media/domains-manage/make-primary-domain.png)

Pode alterar o nome de domínio primário para o seu diretório ser qualquer domínio personalizado verificado que não seja federado. Alterar o domínio primário do seu diretório não alterará o nome de utilizador de quaisquer utilizadores existentes.

## <a name="add-custom-domain-names-to-your-azure-ad-organization"></a>Adicione nomes de domínio personalizados à sua organização AZure AD

Pode adicionar até 900 nomes de domínio geridos. Se estiver a configurar todos os seus domínios para federação com diretório ativo no local, pode adicionar até 450 nomes de domínio em cada diretório.

## <a name="add-subdomains-of-a-custom-domain"></a>Adicione subdomínios de um domínio personalizado

Se pretender adicionar um nome de domínio de terceiro nível, como "europe.contoso.com" ao seu diretório, deve primeiro adicionar e verificar o domínio do segundo nível, como contoso.com. O subdomínio é verificado automaticamente pela Azure AD. Para ver se o subdomínio adicionado é verificado, refresque a lista de domínios no navegador.

Nota

Se já adicionou um domínio contoso.com a um inquilino da AD Azure, também pode adicionar o subdomínio europe.contoso.com a um segundo inquilino AD AZure. Ao adicionar o subdomínio, será solicitado que adicione um registo TXT no fornecedor de hospedagem DNS.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>O que fazer se alterar o registo DNS para o seu nome de domínio personalizado

Se alterar os registos DNS, não existem tarefas de configuração adicionais no Azure AD. Pode continuar a utilizar o nome de domínio com Azure AD sem interrupção. Se utilizar o seu nome de domínio personalizado com o Microsoft 365, Intune ou outros serviços que se baseiem em nomes de domínio personalizados em AZure AD, consulte a documentação para esses serviços.

## <a name="delete-a-custom-domain-name"></a>Excluir um nome de domínio personalizado

Pode eliminar um nome de domínio personalizado do seu AD Azure se a sua organização já não utilizar esse nome de domínio, ou se precisar de usar esse nome de domínio com outro AD Azure.

Para eliminar um nome de domínio personalizado, deve primeiro certificar-se de que nenhum dos recursos do seu diretório depende do nome de domínio. Não é possível apagar um nome de domínio do seu diretório se:

* Qualquer utilizador tem um nome de utilizador, endereço de e-mail ou endereço de procuração que inclua o nome de domínio.
* Qualquer grupo tem um endereço de e-mail ou endereço de procuração que inclui o nome de domínio.
* Qualquer aplicação no seu AD Azure tem uma aplicação ID URI que inclui o nome de domínio.

Tem de alterar ou eliminar qualquer recurso no seu diretório AD Azure antes de poder eliminar o nome de domínio personalizado.

### <a name="forcedelete-option"></a>Opção ForceDelete

Pode **forçar o Nome de** Domínio no [Azure AD Admin Center](https://aad.portal.azure.com) ou utilizar a Microsoft [Graph API](/graph/api/domain-forcedelete?view=graph-rest-beta). Estas opções usam uma operação assíncronea e atualizam todas as referências do nome de domínio personalizado como " user@contoso.com " ao nome de domínio padrão inicial, como " user@contoso.onmicrosoft.com ." 

Para ligar para o **ForceDelete** no portal Azure, deve certificar-se de que existem menos de 1000 referências ao nome de domínio, e quaisquer referências em que o Exchange é o serviço de fornecimento devem ser atualizadas ou removidas no [Centro de Administração de Intercâmbio.](https://outlook.office365.com/ecp/) Isto inclui Grupos de Segurança exchange Mail-Enabled e listas distribuídas; para obter mais informações, consulte [remover grupos de segurança via correio](/Exchange/recipients/mail-enabled-security-groups?view=exchserver-2019#Remove%20mail-enabled%20security%20groups). Além disso, a operação **ForceDelete** não terá sucesso se um dos seguintes for verdadeiro:

* Adquiriu um domínio através dos serviços de subscrição de domínio microsoft 365
* Você é um parceiro que administra em nome de outra organização de clientes

As seguintes ações são realizadas no âmbito da operação **ForceDelete:**

* Renomea o UPN, EmailAddress e ProxyAddress dos utilizadores com referências ao nome de domínio personalizado ao nome de domínio inicial padrão.
* Rebatiza o EmailAddress de grupos com referências ao nome de domínio personalizado ao nome de domínio inicial padrão.
* Rebatiza o identificadorUris das aplicações com referências ao nome de domínio personalizado ao nome de domínio inicial padrão.

Um erro é devolvido quando:

* O número de objetos a renomear é superior a 1000
* Uma das aplicações a renomear é uma app multi-inquilino

### <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P: Porque é que a supressão de domínio está a falhar com um erro que diz que tenho grupos dominados por Exchange neste nome de domínio?** <br>
**A:** Hoje em dia, certos grupos como Mail-Enabled Grupos de Segurança e listas distribuídas são a provisionados pela Exchange e precisam de ser limpos manualmente no [Exchange Admin Center (EAC)](https://outlook.office365.com/ecp/). Pode haver ProxyAddresses persistentes que dependem do nome de domínio personalizado e terão de ser atualizados manualmente para outro nome de domínio. 

**P: Estou registado como administrador \@ contoso.com mas não posso apagar o nome de domínio "contoso.com"?**<br>
**A:** Não é possível fazer referência ao nome de domínio personalizado que está a tentar eliminar no nome da sua conta de utilizador. Certifique-se de que a conta Dedução Global está a utilizar o nome de domínio inicial (.onmicrosoft.com) tais como admin@contoso.onmicrosoft.com . Inscreva-se com uma conta de Administrador Global diferente que, como admin@contoso.onmicrosoft.com ou outro nome de domínio personalizado, como "fabrikam.com" onde a conta está admin@fabrikam.com .

**Q: Cliquei no botão de domínio Eliminar e ver `In Progress` o estado da operação Eliminar. Quanto tempo demora? O que acontece se falhar?**<br>
**A:** A operação de domínio de eliminação é uma tarefa de fundo assíncrol que rebatiza todas as referências ao nome de domínio. Deve estar completo dentro de um minuto ou dois. Se a eliminação de domínio falhar, certifique-se de que não tem:

* Aplicativos configurados no nome de domínio com a appIdentifierURI
* Qualquer grupo habilitado por correio que se refere ao nome de domínio personalizado
* Mais de 1000 referências ao nome de domínio

Se descobrir que alguma das condições não foi cumprida, limpe manualmente as referências e tente apagar novamente o domínio.

## <a name="use-powershell-or-the-microsoft-graph-api-to-manage-domain-names"></a>Use PowerShell ou a Microsoft Graph API para gerir nomes de domínio

A maioria das tarefas de gestão para nomes de domínio no Azure Ative Directory também podem ser concluídas usando o Microsoft PowerShell, ou utilizando programáticamente a API do Gráfico microsoft.

* [Utilizar o PowerShell para gerir nomes de domínio em Azure AD](/powershell/module/azuread/?view=azureadps-2.0#domains)
* [Tipo de recurso de domínio](/graph/api/resources/domain?view=graph-rest-1.0)

## <a name="next-steps"></a>Passos seguintes

* [Adicionar nomes de domínio personalizados](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
* [Remova grupos de segurança habilitados por correio de Exchange no Exchange Admin Center em um nome de domínio personalizado em Azure AD](/Exchange/recipients/mail-enabled-security-groups?view=exchserver-2019#Remove%20mail-enabled%20security%20groups)
* [ForceDelete um nome de domínio personalizado com Microsoft Graph API](/graph/api/domain-forcedelete?view=graph-rest-beta)