---
title: Adicione e verifique nomes de domínio personalizados - Diretório Ativo Azure / Microsoft Docs
description: Conceitos de gestão e como-tos para gerir um nome de domínio no Diretório Ativo Azure
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2395aa5984de2a9fe41e4778d16aba69bfef5192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77559238"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Gerir nomes de domínio personalizados no seu Diretório Ativo Azure

Um nome de domínio é uma parte importante do identificador para muitos recursos de diretório: faz parte de um nome de utilizador ou endereço de e-mail para um utilizador, parte do endereço para um grupo, e às vezes faz parte da aplicação ID URI para uma aplicação. Um recurso em Azure Ative Directory (Azure AD) pode incluir um nome de domínio que é propriedade do diretório que contém o recurso. Apenas um Administrador Global pode gerir domínios em Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Detete o nome principal de domínio para o seu diretório Azure AD

Quando o seu diretório é criado, o nome de domínio inicial, como 'contoso.onmicrosoft.com', é também o nome principal de domínio. O domínio principal é o nome de domínio padrão para um novo utilizador quando cria um novo utilizador. A definição de um nome de domínio primário simplifica o processo para um administrador criar novos utilizadores no portal. Para alterar o nome principal de domínio:

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta que é administradora global para o diretório.
2. Selecione **Azure Active Directory**.
3. Selecione **Nomes de domínio personalizados**.
  
   ![Abertura da página de gestão do utilizador](./media/domains-manage/add-custom-domain.png)
4. Selecione o nome do domínio que pretende ser o domínio primário.
5. Selecione o comando **primário Fazer.** Confirme a sua escolha quando solicitado.
  
   ![Faça um nome de domínio o principal](./media/domains-manage/make-primary-domain.png)

Pode alterar o nome de domínio primário para que o seu diretório seja qualquer domínio personalizado verificado que não seja federado. Alterar o domínio primário para o seu diretório não altera o nome de utilizador para utilizadores existentes.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Adicione nomes de domínio personalizados ao seu inquilino Azure AD

Pode adicionar até 900 nomes de domínio geridos. Se estiver a configurar todos os seus domínios para a federação com diretório ativo no local, pode adicionar até 450 nomes de domínio em cada diretório.

## <a name="add-subdomains-of-a-custom-domain"></a>Adicione subdomínios de um domínio personalizado

Se quiser adicionar um nome de domínio de terceiro nível, como 'europe.contoso.com' ao seu diretório, deve primeiro adicionar e verificar o domínio de segundo nível, como contoso.com. O subdomínio é verificado automaticamente pela AD Azure. Para ver se o subdomínio que adicionou é verificado, refresque a lista de domínios no navegador.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>O que fazer se mudar o registo DNS para o seu nome de domínio personalizado

Se alterar os registores dNS, não existem tarefas adicionais de configuração em Azure AD. Pode continuar a utilizar o nome de domínio com AD Azure sem interrupção. Se utilizar o seu nome de domínio personalizado com o Office 365, Intune ou outros serviços que dependem de nomes de domínio personalizados em Azure AD, consulte a documentação para esses serviços.

## <a name="delete-a-custom-domain-name"></a>Eliminar um nome de domínio personalizado

Pode eliminar um nome de domínio personalizado do seu Anúncio Azure se a sua organização já não utilizar esse nome de domínio, ou se precisar de usar esse nome de domínio com outro Anúncio Azure.

Para eliminar um nome de domínio personalizado, deve primeiro garantir que nenhum recursos no seu diretório dependa do nome de domínio. Não pode excluir um nome de domínio do seu diretório se:

* Qualquer utilizador tem um nome de utilizador, endereço de e-mail ou endereço de procuração que inclua o nome de domínio.
* Qualquer grupo tem um endereço de e-mail ou endereço de procuração que inclui o nome de domínio.
* Qualquer aplicação no seu Anúncio Azure tem uma aplicação ID URI que inclui o nome de domínio.

Tem de alterar ou eliminar qualquer recurso no seu diretório Azure AD antes de poder eliminar o nome de domínio personalizado.

### <a name="forcedelete-option"></a>Opção ForceDelete

Pode **eliminar** um nome de domínio no Centro de [Administração AD Azure](https://aad.portal.azure.com) ou utilizar a [Microsoft Graph API](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta). Estas opções usam uma operação assíncrona e atualizam todasuser@contoso.comas referências do nomeuser@contoso.onmicrosoft.comde domínio personalizado como " " ao nome inicial de domínio padrão como " ." 

Para ligar para **forceDelete** no portal Azure, deve certificar-se de que existem menos de 1000 referências ao nome de domínio, e quaisquer referências em que o Exchange é o serviço de provisionamento devem ser atualizadas ou removidas no [Centro de Administração de Intercâmbio.](https://outlook.office365.com/ecp/) Isto inclui grupos de segurança ativados por correio de troca e listas distribuídas; para obter mais informações, consulte [A remoção de grupos de segurança ativados por correio](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups). Além disso, a operação **ForceDelete** não terá sucesso se qualquer uma das seguintes palavras for verdadeira:

* Adquiriu um domínio através dos serviços de subscrição de domínio do Office 365
* Você é um parceiro administrando em nome de outro inquilino cliente

As seguintes ações são realizadas no âmbito da operação **ForceDelete:**

* Rebatiza o NOME UPN, EmailAddress e ProxyAddress dos utilizadores com referências ao nome de domínio personalizado para o nome de domínio padrão inicial.
* Rebatiza o Endereço de E-mail de grupos com referências ao nome de domínio personalizado para o nome de domínio padrão inicial.
* Rebatiza o identificadorUris de aplicações com referências ao nome de domínio personalizado para o nome de domínio padrão inicial.

Um erro é devolvido quando:

* O número de objetos a renomear é superior a 1000
* Uma das aplicações a renomear é uma aplicação multi-inquilino

### <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P: Porque é que a eliminação do domínio está a falhar com um erro que diz que tenho grupos dominados pela Exchange neste nome de domínio?** <br>
**A:** Hoje em dia, certos grupos como os grupos de segurança ativados por correio e as listas distribuídas são provisionados pela Exchange e precisam de ser limpos manualmente no [Exchange Admin Center (EAC)](https://outlook.office365.com/ecp/). Pode haver proxyAddresses persistentes que dependem do nome de domínio personalizado e terão de ser atualizados manualmente para outro nome de domínio. 

**P: Estou registado como administrador\@contoso.com mas não posso apagar o nome de domínio "contoso.com"?**<br>
**A:** Não é possível fazer referência ao nome de domínio personalizado que está a tentar apagar no nome da sua conta de utilizador. Certifique-se de que a conta De Administrador Global está admin@contoso.onmicrosoft.coma utilizar o nome de domínio padrão inicial (.onmicrosoft.com) como . Inscreva-se numa conta de Administrador admin@contoso.onmicrosoft.com Global diferente que, como ou outro admin@fabrikam.comnome de domínio personalizado como "fabrikam.com" onde está a conta.

**P: Cliquei no botão `In Progress` de domínio Eliminar e ver o estado da operação Delete. Quanto tempo demora? O que acontece se falhar?**<br>
**A:** A operação de domínio de exclusão é uma tarefa de fundo assíncrona que renomea todas as referências ao nome de domínio. Deve estar concluído dentro de um minuto ou dois. Se a eliminação do domínio falhar, certifique-se de que não tem:

* Aplicativos configurados no nome de domínio com a appIdentifierURI
* Qualquer grupo via correio que refira o nome de domínio personalizado
* Mais de 1000 referências ao nome de domínio

Se descobrir que alguma das condições não foi satisfeita, limpe manualmente as referências e tente apagar novamente o domínio.

## <a name="use-powershell-or-the-microsoft-graph-api-to-manage-domain-names"></a>Use powerShell ou a Microsoft Graph API para gerir nomes de domínio

A maioria das tarefas de gestão para nomes de domínio no Diretório Ativo do Azure também podem ser completadas usando o Microsoft PowerShell, ou utilizando programáticamente o Microsoft Graph API.

* [Usar o PowerShell para gerir nomes de domínio em Azure AD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#domains)
* [Tipo de recurso de domínio](https://docs.microsoft.com/graph/api/resources/domain?view=graph-rest-1.0)

## <a name="next-steps"></a>Passos seguintes

* [Adicionar nomes de domínio personalizados](/azure/active-directory/fundamentals/add-custom-domain?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Remova grupos de segurança ativados por correio no Exchange Admin Center sobre um nome de domínio personalizado em Azure AD](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)
* [ForceDelete um nome de domínio personalizado com Microsoft Graph API](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta)
